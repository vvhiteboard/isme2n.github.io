---
layout: post
title:  "Elasticsearch 설치하기"
categories: general
tags: elasticsearch
---


-  Elasticsearch 설치하기
-  샘플 검색




### Elasticsearch 설치하기

---

설치 방법은 매우 간단하다.

1. Elasticsearch 공식 사이트 접속

   >  https://www.elastic.co/kr/downloads/elasticsearch


2. OS 버전에 맞는 파일을 다운로드한 후 압축 해제
   (나의 경우 Mac OS로 실습을 진행할 예정임)
3. 압축 해제한 Elasticsearch 디렉토리에 bin/elasticsearch 바이너리를 실행



**[설치 환경]**

-  Mac OS
-  Elasticsearch 5.6.3



Elasticsearch가 버전 별로 사용법이 조금 상이하다고 한다. 실습용 교재인 Elasticsearch In Action은 1.x 버전을 사용하고 있지만 나는 5.x 버전을 사용할 예정이므로 책에서 나오는 예제 코드가 동작하지 않을 수도 있기 때문에 그 부분을 참고하면서 실습을 진행하고자 한다.

<br>

### 샘플 검색

---

Elasticsearch In Action에서 제공하는 테스트 파일을 사용해서 설치가 제대로 되었는지 확인한다.



**[테스트]**

1. Elasticsearch In Action github에 접속

   >  https://github.com/dakrone/elasticsearch-in-action/tree/5.x (5.x 버전용 브랜치)

2. mapping.json과 populate.sh 을 다운로드 받는다.

3. populate.sh을 실행하여 샘플 데이터를 넣는다.

4. 테스트 쿼리를 날려 확인한다.

<br>

[샘플 url]

`http://localhost:9200/get-together/group/_search?q=elasticsearch&_source=name,location_group&size=2&pretty`

>  책에서 제공해준 테스트 url과 조금 다르다. Elasticsearch 버전이 변경되면서 fields가 아닌 store_fields 또는 _source를 사용하도록 변경되었다. 따라서 위 쿼리는 책에서 제공해준 쿼리에서 fields를 _source로 변경한 내용이다.



[결과]

```json
{
  "took" : 2,
  "timed_out" : false,
  "_shards" : {
    "total" : 2,
    "successful" : 2,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 2,
    "max_score" : 1.2422469,
    "hits" : [
      {
        "_index" : "get-together",
        "_type" : "group",
        "_id" : "2",
        "_score" : 1.2422469,
        "_source" : {
          "location_group" : "Denver, Colorado, USA",
          "name" : "Elasticsearch Denver"
        }
      },
      {
        "_index" : "get-together",
        "_type" : "group",
        "_id" : "3",
        "_score" : 0.5792202,
        "_source" : {
          "location_group" : "San Francisco, California, USA",
          "name" : "Elasticsearch San Francisco"
        }
      }
    ]
  }
}
```



`populate.sh`을 실행하여 넣은 샘플 데이터를 조회한 것이다. 쿼리에서 `q`는 조회할 키워드를 넣는 것이고 `_source`는 해당 키워드로 조회 된 데이터에서 어떤 필드를 조회할 것인지 명시하는 것이다. `_source`를 명시하지 않으면 모든 필드가 조회된다. 또 `size`는 조회할 개수를 의미한다. 위 결과를 보면 `size`를 2로 지정하여 2개의 데이터가 검색되었다. `pretty`는 검색 결과를 보다 보기 좋게 출력해주는 역할을 한다.

