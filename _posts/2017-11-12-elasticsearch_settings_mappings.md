---
layout: post
title:  "Elasticsearch 색인 정의하기"
categories: general
tags: elasticsearch
---



-  RESTful API
-  색인(index)과 타입(type) 정의




### RESTful API

---

엘라스틱 서치는 모든 기능을 RESTful API로 지원한다.

>  **REST**(Representational State Transfer)는 [월드 와이드 웹](https://ko.wikipedia.org/wiki/%EC%9B%94%EB%93%9C_%EC%99%80%EC%9D%B4%EB%93%9C_%EC%9B%B9)과 같은 분산 [하이퍼미디어](https://ko.wikipedia.org/wiki/%ED%95%98%EC%9D%B4%ED%8D%BC%EB%AF%B8%EB%94%94%EC%96%B4) 시스템을 위한 [소프트웨어 아키텍처](https://ko.wikipedia.org/wiki/%EC%86%8C%ED%94%84%ED%8A%B8%EC%9B%A8%EC%96%B4_%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98)의 한 형식이다. (출처 : 위키백과)

<br>

RESTful API에 대해 간단하게만 설명하면 웹 상의 특정 자원(URI)을 조작하는데 있어서 HTTP의 Method를 활용하여 조작할 수 있도록 지원하는 것이다. 즉 엘라스틱 서치는 데이터를 조작하기 위해 GET, POST, PUT, DELETE 등의 HTTP Method로 지원하고 있다. 엘라스틱 서치는 RESTful API로 간단하게 데이터를 추가, 삭제, 수정, 조회할 수 있게 지원하고 있다.

<br>

### 색인과 타입 정의

---

엘라스틱 서치는 데이터는 미리 매핑되어 있는 색인이 존재하지 않아도 데이터를 추가하려고 할 때 입력된 데이터에 해당하는 색인(index),  타입(type), 문서(document)를 자동으로 생성한다. 엘라스틱 서치가 알아서 색인과 타입을 생성하기 때문에 굉장히 간편하다.

하지만 주의해야 할 점이 있다. 엘라스틱 서치는 입력된 데이터에 적당한 데이터 타입을 매핑하여 저장하는데 이 데이터 타입은 엘라스틱 서치가 임의로 결정한다. 예를 들어 "1234"라는 문자열 데이터를 문서에 추가하려고 할 때 엘라스틱 서치는 이를 숫자로 인식하여 integer로 저장할 수 있다. 그렇게 생성된 타입에 해당 필드(field)에 다음 데이터가 "abcd"인 경우 에러가 발생할 것이다. 이러한 문제를 방지하기 위해선 타입 매핑을 미리 생성하는 것이 좋다. 자동으로 생성해주는 기능이 편하긴 이런 문제가 발생할 수 있기 때문에 색인과 타입을 미리 정의하는 것이 좋다.

<br>

색인(index) 정의는 샤드 개수, 레플리카 개수, 사용할 분석기, 필터 등을 설정할 수 있다. 한글 데이터를 사용하는 경우 한글 분석기 등을 설치하여 연동할 수 있다. (한글 분석기 참고 : https://vvhiteboard.github.io/general/2017/10/22/elastic-basic/)

타입 정의에서는 문서의 필드 이름, 필드 데이터 타입등을 설정할 수 있고, 어떤 분석기를 사용할 것인지 등을 설정할 수 있다.

다음은 내가 실습에서 사용할 네이버 영화 평점 정보에 대한 색인 정의와 타입 매핑이다.

**[naver_movie.json]**

```json
{
	"settings" : { # 색인(index) 정의
		"number_of_shards" : 2, # 샤드 개수
		"number_of_replicas": 1, # 레플리카 개수
		"index": { # 색인 전체 설정
			"analysis": {
				"analyzer": {
					"korean": { # 사용자 정의 분석기
						"type":"custom",
						"tokenizer":"loopin_test_tokenizer" # 토크나이저 설정
					}
				},
				"tokenizer": {
					"loopin_test_tokenizer": { # 토크나이저 정의
						"type" : "seunjeon_tokenizer" # 한글 분석기 (은전한닢)
					}
				}
			}
		}
	},
	"mappings": { # 타입(type) 정의
		"review": { # 타입 이름
			"_source": {
				"enabled": true
			},
			"properties" : { # 문서(document) 정의
				"movie_id": {"type": "integer", "index": "not_analyzed"}
				,"premiere_year": {"type": "integer"}
				,"movie_title": {"type": "text", "analyzer": "korean"}
				,"user_name": {"type": "text", "index":"not_analyzed"}
				,"review_date": {"type": "date", "format": "yyyy-MM-dd HH:mm:ss"}
				,"score": {"type": "integer"}
				,"recommend": {"type": "integer"}
				,"notrecommend": {"type": "integer"}
				,"review_contents": {"type": "text", "analyzer": "korean"}
			}
		}
	}
}
```

<br>

위와 같이 색인과 타입을 정의하는 json 파일을 생성한다. 엘라스틱 서치가 실행되어 있는 상태에서 해당 색인과 타입을 엘라스틱 서치에 정의할 수 있다.

색인 정의는 `settings`아래에 정의된다. 색인 전체적으로 적용될 설정은 `index`아래에 입력된다.

타입 정의는 `mappings`아래에 정의된다. 타입 이름은 사용자 정의에 따라 지정할 수 있고 아래 `properties`로 해당 타입의 각 필드를 정의할 수 있다. 각 필드는 `type`으로 데이터 타입을 설정할 수 있다. `"index": "not_analyzed"`는 데이터가 입력될 때 해당 데이터를 분석하지 않겠다는 뜻이다. `"analyzer" : "korean"`은 분석기를 `korean`으로 설정하겠다는 뜻이고 해당 분석기는 `settings`에 미리 사용할 분석기를 정의해야 한다.

`settings`와 `mappings`에 설정할 수 있는 옵션들은 추후 한꺼번에 정리하려고 한다. 그럼 위에 설정한 색인을 엘라스틱 서치에 적용하면 된다.

**[적용]**

```sh
$ curl -XPUT http://localhost:9200/naver_movie?pretty -d @naver_movie.json
# 색인과 타입을 정의한다. 
# 요청 URI : http://[Elasticsearch IP]:[PORT]/[index name] -d @[mapping file]
$ curl -XGET http://localhost:9200/naver_movie?pretty
# 정의한 내용을 확인한다.
```

엘라스틱 서치는 RESTful API를 제공한다. curl 명령어는 해당 HTTP 요청을 보내주는 명령어이다. 첫번째 명령어는 `http://localhost:9200/naver_movie`에 PUT 메소드 요청을 보내는데 `naver_movie.json`파일 내용을 HTTP body에 담아서 전송해주는 명령어이다. 즉, 엘라스틱 서치에 PUT 메소드로 요청을 보내면 어떤 데이터를 추가할 수 있다.

두번째 명령어는 엘라스틱 서치에서 특정 색인의 정보를 조회할 때 사용하는 명령어이다. 특정 타입의 정보까지 조회하고자 한다면 `http://localhost:9200/naver_movie/review`와 같이 타입까지 경로를 명시해주면 된다.

URI 끝에 있는 `pretty`옵션은 엘라스틱 서치 서버의 응답인 json 데이터를 보기 좋게 바꿔주는 옵션이다.

위와 같은 명령어를 수행하면 엘라스틱 서치에는 해당 색인과 타입이 정의되고 데이터를 추가할 준비가 되었다.
