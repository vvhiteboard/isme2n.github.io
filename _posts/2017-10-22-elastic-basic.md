---
layout: post
title:  "Elastic Search 기본"
categories: general
tags: elasticsearch
---



-  용어 정리
-  한글 형태소 분석기 플러그인
-  인덱스 정의
-  타입 매핑





### 용어 정리

------

Elastic Search에서 사용하는 용어를 설명한다.

-  인덱스(index)

   RDBMS에 비유하면 Database에 해당한다.

-  타입(type)

   RDBMS에 비유하면 Table에 해당한다.

-  문서(document)

   RDBMS에 비유하면 Row에 해당한다.

-  필드(field)

   RDBMS에 비유하면 Column에 해당한다.

-  매핑(mapping)

   인덱스/타입/문서를 정의하는 것을 말한다. RDBMS에 비유하면 스키마 정의에 해당한다.

-  색인(index)

   엘라스틱 서치가 문서를 검색할 수 있도록 색인 데이터를 만드는 과정을 말한다. 처음에 쓴 인덱스(index)와 용어는 같지만 의미가 조금 다르므로 주의해야한다.

-  샤드/리플리카(shard/replica)

   엘라스틱 서치가 색인 데이터를 여러 물리적 공간에 분산 저장하는 것을 말한다. 샤드(shard)는 성능 향상을 위해 데이터를  분산 저장하는 것을 말하며 리플리카(replica)은 한 노드가 검색에 실패 했을 때에도 검색 서비스를 제공하기 위해 같은 데이터를 여러 곳에 복사하여 분산 저장하는 것을 한다.

-  QueryDSL

   JSON으로 표현되는 엘라스틱 서치 검색 문법이다.

<br>

### 한글 형태소 분석기 플러그인

------

엘라스틱 서치에 한글 검색을 지원하기 위해 한글 형태소 분석기 플러그인을 설치 한다.

가장 많이 사용하는 한글 형태소 분석기 중에 **'은전한닢'**이라는 분석기가 있다. 해당 분석기는 엘라스틱 서치에 플러그인 형식으로 쉽게 설치 할 수 있다.

>  공식 사이트 : http://eunjeon.blogspot.kr

<br>

**[설치 방법]**

설치 방법은 공식 사이트에 접속하면 관련 깃 사이트(bitbucket)에 공개되어 있다. 설치 방법이 간단하므로 쉽게 설치할 수 있을 것이다.

-  플러그인과 설치 스크립트를 다운로드 받는다.

   ```sh
   $ bash <(curl -s https://bitbucket.org/eunjeon/seunjeon/raw/master/elasticsearch/scripts/downloader.sh) -e 5.6.3 -p 5.4.1.1
   # -e 옵션은 엘라스틱 서치의 버전을 명시, -p 옵션은 플러그인 버전 명시
   # 나의 경우 5.6.3 버전이므로 해당 버전을 명시하여 다운로드 받았다.
   ```

-  설치 스크립트를 실행한다.

   ```sh
   $ ./bin/elasticsearch-plugin install file://`pwd`/elasticsearch-analysis-seunjeon-5.4.1.1.zip
   # 현재 경로를 엘라스틱 서치가 설치된 디렉토리(일반적으로 elasticsearch-5.6.3)로 지정하여 해당 명령어를 실행한다.
   # elasticsearch-analysis-seunjeon-5.4.1.1.zip 파일을 ~/elasticsearch-x.x.x/ 위치에 놓고 실행한다.
   ```


<br>

**[설치 확인]**

```sh
ES='http://localhost:9200'
ESIDX='seunjeon-idx'

curl -XDELETE "${ES}/${ESIDX}?pretty"
sleep 1
curl -XPUT "${ES}/${ESIDX}/?pretty" -d '{
  "settings" : {
    "index":{
      "analysis":{
        "analyzer":{
          "korean":{
            "type":"custom",
            "tokenizer":"seunjeon_default_tokenizer"
          }
        },
        "tokenizer": {
          "seunjeon_default_tokenizer": {
            "type": "seunjeon_tokenizer",
            "user_words": ["낄끼빠빠,-100", "버카충", "abc마트"]
          }
        }
      }
    }
  }
}'

sleep 1

echo "========================================================================"
curl -XGET "${ES}/${ESIDX}/_analyze?analyzer=korean&pretty" -d '삼성전자'
echo "========================================================================"
curl -XGET "${ES}/${ESIDX}/_analyze?analyzer=korean&pretty" -d '빨라짐'
echo "========================================================================"
curl -XGET "${ES}/${ESIDX}/_analyze?analyzer=korean&pretty" -d '낄끼빠빠 어그로'
```

엘라스틱 서치를 실행한 후에 해당 코드를 sh파일로 생성하여 실행하고 형태소 분석이 제대로 이루어지면 플러그인이 정상적으로 설치된 것이다.

<br>

**[결과 확인]**

```json
{
  "acknowledged" : true
}
{
  "acknowledged" : true,
  "shards_acknowledged" : true,
  "index" : "seunjeon-idx"
}
========================================================================
{
  "tokens" : [
    {
      "token" : "삼성/N",
      "start_offset" : 0,
      "end_offset" : 2,
      "type" : "N",
      "position" : 0
    },
    {
      "token" : "삼성전자/EOJ",
      "start_offset" : 0,
      "end_offset" : 4,
      "type" : "EOJ",
      "position" : 0,
      "positionLength" : 2
    },
    {
      "token" : "전자/N",
      "start_offset" : 2,
      "end_offset" : 4,
      "type" : "N",
      "position" : 1
    }
  ]
}
========================================================================
{
  "tokens" : [
    {
      "token" : "빠르/V",
      "start_offset" : 0,
      "end_offset" : 2,
      "type" : "V",
      "position" : 0
    },
    {
      "token" : "빨라짐/EOJ",
      "start_offset" : 0,
      "end_offset" : 3,
      "type" : "EOJ",
      "position" : 0,
      "positionLength" : 2
    },
    {
      "token" : "지/V",
      "start_offset" : 2,
      "end_offset" : 3,
      "type" : "V",
      "position" : 1
    }
  ]
}
========================================================================
{
  "tokens" : [
    {
      "token" : "낄끼빠빠/N",
      "start_offset" : 0,
      "end_offset" : 4,
      "type" : "N",
      "position" : 0
    },
    {
      "token" : "어그/N",
      "start_offset" : 5,
      "end_offset" : 7,
      "type" : "N",
      "position" : 1
    },
    {
      "token" : "어그로/EOJ",
      "start_offset" : 5,
      "end_offset" : 8,
      "type" : "EOJ",
      "position" : 1
    }
  ]
}
```

<br>

### 인덱스 정의

---

RDBMS에서 스키마를 정의하듯이 엘라스틱 서치에서 인덱스/타입/문서를 정의해야한다. 그 중에 인덱스 정의하는 방법을 설명한다. RDBMS로 비유하면 Database 설정이다. (RDBMS의 DB 엔진등의 설정이라고 보면 된다.)

<br>

**[인덱스 예제]**

```json
{
  "number_of_shards": 1, // 샤드 개수
  "number_of_replicas": 0, // 복사본 개수
  "index": { // 인덱스
    "analysis": {
      "tokenizer": {
        "seunjeon_default_tokenizer": { // 분석기 이름
          "type": "seunjeon_tokenizer", // 은전한닢 분석기 
          "user_words": ["낄끼빠빠,-100", "버카충", "abc마트"] // 옵션(사용자 지정 사전)
        }
      },
      "analyzer": { // 분석기 정의
        "korean": { // 한글 형태소 분석기
          "type": "custom",
          "tokenizer": "seunjeon_default_tokenizer"
        }
      }
    }
  }
}
```

위의 예제 파일 처럼 스키마를 정의 한 후에 스키마를 정의해주는 엘라스틱 서치 쿼리를 호출하여 적용한다. 



### 타입 매핑

---

타입을 매핑한다. RDBMS로 비유하면 테이블 스키마 설정이다.

<br>

**[타입 매핑 예제]**

```json
{
  "properties": { // 각 타입 정보 매핑
    "urn":          {"type": "text", "index": "not_analyzed"},
    "title":        {"type": "text", "analyzer": "korean"}, // 위에서 지정한 분석기 설정
    "content":      {"type": "text", "analyzer": "korean"},
    "writer":       {
      "type": "nested",
      "properties": { // 하위 타입 설정도 가능함
        "name": {"type": "string", "index": "not_analyzed"},
        "email": {"type": "string", "index": "not_analyzed"}
      }
    }
  }
}
// _id, _source, _timestamp와 같은 메타 데이터는 자동으로 생성된다.
```

