---
layout: post
title:  "Elasticsearch 색인데이터 추가"
categories: general
tags: elasticsearch
---



-  타입정보 (Type) 관련


<br>

### 타입 정보 (Type) 관련

---

색인 데이터를 추가, 삭제, 수정하는 방법에 대해 설명한다.

-  _mapping

   -  현재 매핑 정보를 조회한다.

      ```sh
      curl -XGET 'http://localhost:9200/naver_movie/review/_mapping?pretty'
      # 'naver_movie' 색인에 'review' 타입의 매핑 정보를 조회

      curl -XGET 'http://localhost:9200/naver_movie/_mapping?pretty'
      # 'naver_movie' 색인의 모든 매핑 정보를 조회
      ```

   ​

   -  기존 매핑 정보를 확장 또는 추가한다.

      ```sh
      curl -XPUT 'http://localhost:9200/naver_movie/review/_mapping?pretty' -d'{
        "review": {
          "properties": {
            "title": {"type": "text"}
          }
        }
      }'
      # naver_movie 색인에 review 타입에 매핑 정보 생성 또는 기존 review 타입에 새로운 필드 추가
      ```


<br>

-  document 추가

   ```sh
   # 새로운 document를 추가한다.
   curl -XPOST 'http://localhost:9200/naver_movie/review?pretty' -d'{
     "title" : "sample Title"
     , "contents" : "sample contents"
   }'

   curl -XPOST 'http://localhost:9200/naver_movie/review/1?pretty' -d'{
     "title" : "sample Title"
     , "contents" : "sample contents"
   }'
   # id까지 명시를 하면 해당 id로 document를 생성한다.
   ```


<br>


-  document 수정

   ```sh
   curl -XPOST 'http://localhost:9200/naver_movie/review/1/_update' -d '{
     "doc" : {"user_id" : "guest"}
   }'
   # 1번 id를 가진 document에 "user_id"라는 필드와 그 값을 넣는다.
   # 기존에 해당 필드가 존재했으면 update하고 필드가 없었다면 필드도 함께 생성해준다.
   ```


<br>

-  document 여러개 추가 (_bulk)

   -  넣을 데이터를 정의한 json 파일을 생성한다.

   **[review.json]**

   ```json
   { "index" : {"_index" : "naver_movie", "_type" : "review", "_id" : "1"}}
   {"title" : "Bulk Title 1", "contents" : "Build Contents 1", "user_id" : "Buik User1"}
   { "index" : {"_index" : "naver_movie", "_type" : "review", "_id" : "2"}}
   {"title" : "Bulk Title 2", "contents" : "Build Contents 2", "user_id" : "Buik User2"}
   { "index" : {"_index" : "naver_movie", "_type" : "review", "_id" : "3"}}
   {"title" : "Bulk Title 3", "contents" : "Build Contents 2", "user_id" : "Buik User3"}
   ```

   <br>

   -  해당 데이터를 엘라스틱서치에 추가한다.

   ```sh
   curl -XPOST http://localhost:9200/_bulk?pretty --data-binary @review.json
   ```

   <br>

-  document 삭제

   ```sh
   curl -XDELETE http://localhost:9200/naver_movie/review/1?pretty # document 삭제
   curl -XDELETE http://localhost:9200/naver_movie/review?pretty # type 삭제
   curl -XDELETE http://localhost:9200/naver_movie?pretty # index 삭제
   ```

   삭제 하고자 하는 document의 id를 입력하고 HTTP의 DELETE 메소드를 사용하면 해당 document가 삭제된다. 뿐만 아니라 해당 index 또는 type을 지정하면 해당 자원이 삭제된다.
