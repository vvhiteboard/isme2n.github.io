---
layout: post
title:  " Vue 시작하기"
categories: web
tags: vue
---


-  Vue 시작하기





## Vue 시작하기

Vue는 자바 스크립트 프레임 워크중 하나로 비슷한 프레임 워크로 React, Angular 등이 있다. 관련된 자세한 내용은 https://kr.vuejs.org에 자세하게 나와있으며 이 블로그 내용도 해당 페이지에 기반하고 있다. 다른 프레임 워크 간의 성능 비교를 해당 페이지에서 볼 수 있으며 나중에 나도 정리해볼 생각이다.



#### 설치하기

Javascript 프레임 워크이기 때문에 관련 js파일을 페이지에 추가하면 된다.

```html
<script src="https://unpkg.com/vue"></script>
```



해당 경로는 npm에 올라간 최신 버전의 vue를 받을 수 있는 경로이다. 만약 특정 버전의 vue를 사용하고 싶다면 해당 vue.js를 다운로드받아 자신의 서버에 배포해서 사용하도록 한다. 개발을 진행할 때는 vue.js를 사용하고 배포시에는 vue.min.js를 사용하도록 한다.

-  개발용 버전

   개발용 버전에는 개발시 발생할 수 있는 에러나 실수 등에 대한 모든 경고 및 디버그 모드를 포함하고 있다. 따라서 개발 시에는 개발용 버전으로 개발을 하는게 도움이 된다.

-  배포용 버전

   서비스에 배포할 땐 경고 및 디버그 모드가 제거된 버전으로 배포하는 것이 좋다.

   ​



#### 개발환경

vue는 개발용 툴을 chrome 브라우저 앱으로 지원한다. chrome 앱스토어에서 `vue.js devtools`을 검색하면 나오는 앱을 chrome 브라우저에 설치하면 된다.



## Vue 인스턴스

Vue 어플리케이션을 사용하기 위해선 먼저 Vue 인스턴스를 생성해야 한다.

**[Vue 인스턴스 생성]**

```javascript
var app = new Vue({
  el: "#app",
  data: {
      text: "Hello World~!"
  }
  // 그 외 옵션들
});
```

<br>

각 Vue 인스턴스는 `data`객체에 있는 모든 속성을 프록시로 처리한다.

```javascript
var sampleData = {key: "value"};

var app = new Vue({
   data: sampleData
});

// app.key === sampleData.key
```

위 코드에서 app.key와 sampleData.key는 같은 인스턴스를 참조한다는 점에 유의해야한다.

또한 생성한 vue 인스턴스의 데이터가 변경되면 화면은 다시 렌더링 된다. `app.key = "new value"`또는 `sampleData.key = "new value"`와 같이 app에 있는 데이터를 변경하면 화면을 다시 렌더링하여 변경된 데이터를 적용한다. 즉 인스턴스가 생성될 때 있었던 속성(위 코드에서 `app.data`)은 **반응형**으로 동작한다.

<br>

하지만 추가된 데이터에 대해서는 반응형으로 동작하지 않는다. 

```javascript
var app = new Vue({
  data: {key1: "value1"}
});

app.key1 = "new value1" // 반응형으로 동작

app.key2 = "new value2" // 반응형으로 동작하지 않음
```

위 예제 코드처럼 인스턴스 생성시 존재하는 `key1`에 대해서는 **반응형**으로 동작하지만 동적으로 추가된 `key2`에 대해서는 반응형으로 동작하지 않는다는 점을 유의해야한다. 만약 `key2`도 반응형으로 동작하도록 하고 싶다면 vue 인스턴스를 생성할 때 `key2` 속성을 생성하면 된다.

<br>

#### 라이프 사이클

vue 인스턴스는 라이프 사이클을 가지고 있다. vue 인스턴스는 먼저 데이터를 설정하고 템플릿을 컴파일하고 인스턴스를 DOM에 마운트하고 데이터가 변경될 때 DOM을 업데이터한다. 이 라이프 사이클 과정에서 사용자 정의 메소드를 호출 할 수 있는데 이 메소드를 **라이프 사이클 훅**이라고 한다.

<br>

#### 라이프 사이클 훅

라이프 사이클 훅은 인스턴스의 각 라이프 사이클 사이에 발생하는 이벤트에 사용자 정의 메소드를 실행할 수 있도록 해준다.

예를 들어, `created`는 인스턴스가 생성된 후에 호출된다.

```javascript
new Vue({
    data: {
        x: 1
    },
  	created: function() {
        console.log("data.x is " + this.x)
    }
})
```

 `created`외에도 `mounted`, `updated`, `destoryed`가 있다. 모든 라이프 사이클 훅에서 `this` 컨텍스트는 Vue 인스턴스를 가리키고 있다. 따라서 위의 `created`에 바인딩 된 함수에서 this.x는 초기화한 vue 인스턴스의 x값을 참조하기 때문에 콘솔에 "data.x is 1"이 출력 될 것이다.

>  주의) 화살표 함수(arrow function)에서는 this를 사용하지 않는 것이 좋다. 화살표 함수는 부모 컨텍스트에 바인딩 되기 때문에 this.x가 바인딩 되지 않을 것이기 때문이다.



#### 라이프 사이클 다이어그램

![라이프사이클]({{ site.url }}/assets/img/posts/2017-10-3-vue-start.png)

