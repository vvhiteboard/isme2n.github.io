---
layout: post
title:  "ElasticSearch 실습1(logstash 활용)"
categories: general
tags: elasticsearch
---



-  logstash config 설정
-  데이터 넣어보기 실습
-  logstash와 MySQL 연동하기
-  DB 데이터를 Elasticsearch에 색인하기


<br>


### Logstash를 이용한 색인 데이터 추가

------

Logstash를 이용하여 Elasticsearch에 색인 데이터를 추가하는 방법에 대해 실습한다.
Logstash는 다양한 방법으로 데이터를 입력받아 정제하고 출력하는 기능을 제공하고 있다. Logstash를 이용해서 Elasticsearch에 색인 데이터를 추가하는 방법에 대해 알아보려고 한다.

내가 진행할 실습은 네이버 영화에 평점에 대한 내용을 Elasticsearch에 넣어보려고 한다. (무슨 의미가 있을진 모르겠지만 아무튼...) 먼저 평점 데이터를 MySQL에 쌓아놓고 그 내용을 Logstash를 이용해서 Elasticsearch에 넣는 실습을 하려고 한다.

먼저 Elasticsearch에 넣을 색인 데이터에 대한 매핑 정보를 먼저 추가한다.

<br>

**[naver_movie.json]**

```json
{
	"settings" : {
		"number_of_shards" : 2,
		"number_of_replicas": 1,
		"index": {
			"analysis": {
				"analyzer": {
					"korean": {
						"type":"custom",
						"tokenizer":"loopin_test_tokenizer"
					}
				},
				"tokenizer": {
					"loopin_test_tokenizer": {
						"type" : "seunjeon_tokenizer"
					}
				}
			}
		}
	},
	"mappings": {
		"review": {
			"_source": {
				"enabled": true
			},
			"properties" : {
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

Elasticsearh에서 위 매핑 정보를 갖는 naver_movie라는 색인을 생성한다.
아래와 같은 명령을 입력한다.

```sh
$ curl -XPUT http://localhost:9200/naver_movie?pretty -d @naver_movie.json
# curl -XGET http://localhost:9200/naver_movie?pretty 를 이용하여 매핑이 되었는지 확인
```

<br>

#### logstash config 설정

logstash는 config 파일 설정을 통해 input과 output을 설정할 수 있다. 다음은 logstash의 간단한 설정파일 예제이다.

**[logstash.test.conf]**

```
input {
  stdin {
    codec => json
  }
}

output {
  elasticsearch {
    hosts => ["localhost:9200"]
    index => "naver_movie"
    document_type => "review"
  }
  stdout {
    codec => rubydebug
  }
}
```

-  input

   -  stdin

      입력을 표준 입력으로 받겠다는 설정이다. 입력의 포맷은 json이다. codec은 보통 어떤 데이터 스트림을 변환(인코딩, 디코딩)하여 다른 데이터 스트림으로 바꾸는 역할을 하는 것을 말하는데 여기선 표준 입력으로 들어온 json데이터를 해석하겠다는 뜻으로 보면 되겠다.

-  output

   -  elasticsearch

      출력을 elasticsearch로 보내겠다는 뜻이다. elasticsearch를 설정하기 위한 몇가지 옵션이 있다. logstash 버전별로 설정법이 조금 다른것같다. 버전별 공식 reference를 꼭 확인하고 설정하자. (위 예제 설정은 logstash 5.6.4버전 기준으로 작성되었다.)

      -  hosts : 보낼 elasticsearch 호스트설정이다.

      -  index : elasticsearch로 보낼 index를 지정한다.

      -  document_type : elasticsearch로 보낼 type을 지정.

      -  document_id : elasticsearch로 보낼 document id를 지정.

      -  그 외 많은 옵션값들이 있음 (공식 reference 참고 바람)

         (참고 : https://www.elastic.co/guide/en/logstash/5.6/plugins-outputs-elasticsearch.html)

   -  stdout

      표준출력으로 보내겠다는 뜻이다. 코덱은 rubydebug로 지정한다. rubydebug로 설정하면 ruby의 awesome_print 라이브러리를 이용하여 json을 보기좋게 출력해준다.


<br>


#### 데이터 넣어보기 실습

logstash config 설정에서 알아본 예제 파일을 적용하여 logstash 실행해보자

```sh
./logstash -f ../config/logstash.test.conf # 해당 설정파일로 로그스태시 실행
# 설정파일이 제대로 적용되는지 테스트를 해보고 싶으면 -t 옵션을 추가한다.
```

<br>

```
[2017-11-19T17:36:42,085][INFO ][logstash.pipeline        ] Pipeline main started
The stdin plugin is now waiting for input:
[2017-11-19T17:36:42,130][INFO ][logstash.agent           ] Successfully started Logstash API endpoint {:port=>9600}

```

logstash 실행 후 출력이 위와 같은 상태로 완료되면 표준입력을 받을 준비가 되었다는 뜻이다. 이제 elasticsearch에 입력하고자 하는 데이터를 json형태로 입력하면 된다.

<br>

#### logstash와 MySQL 연동하기

앞에서 말한 방법을 이용하면 다량의 데이터를 넣을때 곤란하다. 이미 DB에 저장된 데이터를 처리하기 어렵다. logstash는 다양한 방법의 input과 output을 플러그인 형태로 지원하고 있다. DB에서 데이터를 읽어 해당 값을 input으로 사용할 수 있다. logstash와 mysql을 연동하는 방법을 알아보겠다.

먼저 DB연동을 위해서 logstash가 지원하는 jdbc 플러그인을 설치해야한다. 설치방법은 매우 간단하다. logstash 설치 디렉토리에 bin에 있는 logstash-plugin을 이용하여 손쉽게 설치 할 수 있다.

<br>

**[logstash-input-jdbc 플러그인 설치]**

```sh
./bin/logstash-plugin list # logstash에서 지원하는 플러그인 목록을 보여준다.
./bin/logstash-plugin list jdbc # 플러그인 중에 jdbc와 관련된 목록을 보여준다.
./bin/logstash-plugin install logstash-input-jdbc # logstash-input-jdbc 플러그인 설치
```

<br>

jdbc 플러그인를 설치하였다. 하지만 DBMS와 연동하기 위해선 connector 라이브러리를 추가해야한다. MySQL과 연동하기 위해 MySQL-connector를 다운로드 받아야 한다.

>  libraray 다운로드 경로 : http://xbib.org/repository/org/xbib/elasticsearch/importer/elasticsearch-jdbc
>
>  해당 경로에서 원하는 버전의 library를 설치하면 된다. 끝에 dist가 붙은 파일로 다운로드 받으면 된다.
>  나는 http://xbib.org/repository/org/xbib/elasticsearch/importer/elasticsearch-jdbc/2.3.4.1/elasticsearch-jdbc-2.3.4.1-dist.zip 을 다운로드 받아 실습하였다.

<br>

라이브러리를 다운로드 받아 압축을 해제하고 lib 디렉토리에 있는 mysql-connector-java 라이브러리를 logstash에 lib 디렉토리로 복사한다.

<br>

#### DB 데이터 elasticsearch 색인하기

이제 jdbc 플러그인을 이용하여 MySQL 데이터를 조회해 elasticsearch에 색인 데이터를 넣어보자. logstash 설정에 DB 설정과 elasticsearch 설정을 통해 데이터를 색인해보도록 하겠다.

<br>

먼저 logstash.conf 파일 설정이다.

**[logstash.conf]**

```
input {
  jdbc {
    jdbc_driver_library => "../lib/mysql-connector-java-5.1.38.jar"
    jdbc_driver_class => "com.mysql.jdbc.Driver"
    jdbc_connection_string => "jdbc:mysql://localhost:3306/sampledb?useUnicode=true&characterEncoding=utf8"
    jdbc_user => "user_id"
    jdbc_password => "user_password"
    
    parameters => {"param1" : "abc"}
    statement => "select * from ... where :param1"
    
    jdbc_pool_timeout => 10
    jdbc_paging_enabled => true
    jdbc_page_size => 10000
  }
}

output {
  elasticsearch {
    hosts => ["localhost:9200"]
    index => "naver_movie"
    document_type => "review"
  }
  stdout {
    codec => rubydebug
  }
}
```

<br>

설정파일의 옵션은 다음과 같다. (output 설정은 앞에서 알아봤으므로 jdbc 설정만 알아보도록 한다)

-  jdbc

   -  jdbc_driver_library : connecter 라이브러리 설정이다. 앞에서 다운로드 받은 mysql-connector 경로를 설정하면 된다.

   -  jdbc_driver_class : 드라이버 클래스를 설정한다. Mysql이므로 `com.mysql.jdbc.Driver`로 설정

   -  jdbc_connection_string : DBMS connection string 설정

   -  jdbc_user : DBMS 접속 계정 설정

   -  jdbc_password : DBMS 접속 계정 비밀번호

   -  parameters : statement에서 사용할 파라미터를 설정한다.

   -  statement : 조회하고자하는 데이터의 쿼리를 설정한다.

   -  jdbc_pool_timeout : connection pool timeout 시간을 설정한다. (단위 초, 기본값 5)

   -  jdbc_paging_enabled : 데이터 조회시 페이징 처리를 할 것인지를 설정한다. (쿼리 결과의 ordering은 보장하지 않는다.)

   -  jdbc_page_size : 페이징의 사이즈를 결정한다.

   -  그 외 많은 설정 (공식 문서 참고)

      >  참고 : https://www.elastic.co/guide/en/logstash/5.6/plugins-inputs-jdbc.html

<br>

statement에서 조회 쿼리를 작성할 때 조회할 칼럼 이름을 elasticsearch의 필드명과 동일하게 하면 조회한 데이터의 칼럼명과 동일한 필드에 색인한다. 또한 statement를 위와같이 config 파일이 아닌 따로 파일로 작성하여 추가할 수도 있는데 해당 방법은 나중에 추후 필요할 때 다시 알아보려고 한다.

위와 같이 설정한 후 logstash를 위의 config 파일로 실행하면 DB에서 데이터를 읽어 elasticsearch에 저장한다. 대량의 데이터를 elasticsearch에 색인하려는 경우 logstash를 이용하면 간단하게 데이터를 색인 할 수 있다.
