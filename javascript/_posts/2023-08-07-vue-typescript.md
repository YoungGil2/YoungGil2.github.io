---
layout: post
title: "Vue + Typescript"
date: 2023-08-07 13:42:00 +0900
description: >
  Vue.js에서 Typescript 사용 방법
lastmod: 2023-08-08 13:42:00 +0900
sitemap:
  changefreq: weekly
---

0. 
{:toc}

## Vue + Typescript

`Vue`에서 Typescript를 사용하는 방법은 두가지가 있다. 첫번째 `Vue.extend` 객체형, 두번째 `class` 클래스형이 있다.

## Vue.extend

### 사용법

SFC(Single File Components)에서는 반드시 `<script lang="ts"></script>` 속성을 추가하여야한다.

~~~vue
<template>
  <input v-model="job" />
  <button @click="sayName" />
</template>
<script lang="ts">
import Vue from 'vue'

export default Vue.extend({
  name: 'parson',
  props: {
    name: String,
    modelValue: String
  },
  data () {
    return {
      age: 25
    }
  },
  methods: {
    sayName () {
      console.log(this.name)
    }
  },
  computed: {
    job: {
      get () {
        return 'develop'
      },
      set (value: string) {
        this.job = value
      }
    }
  },
  created() {
    // ...
  },
  mounted () {
    // ...
  }
})
</script>
~~~


## Class Component

class형 작업을 위해 라이브러리 몇가지를 같이 설명할 것이다.

* vue-class-component: vue 컴포넌트를 클래스 형으로 만들 수 있게 해주는 라이브러리
* vue-property-decorator: vue-class-component 기반으로 만들어진 라이브러리 class형 개발을 도와주는 라이브러리


### vue-property-decorator

[vue-property-decorator](https://github.com/kaorun343/vue-property-decorator){:target="_blank"}
{:.note title="github"}

라이브러리를 구성을 확인해보면 `vue-class-component`에서 제공되는 것의 대부분을 데이코레이터 함수로 만들어둔 라이브러리 정도로 보인다. 

### Decorator 란?

[Decorator](https://www.typescriptlang.org/docs/handbook/decorators.html){:targe="_blank"}
{:.note title="공식 문서"}

위에서 말하는 vue-property-decorator에서 decorator가 무엇인지 몰라 알아보았다.

데코레이터는 Typescript 및 ES6에 클래스가 도입되면서 추가되었다고 한다. 데코레이터란 클래스, 메소드, 접근자, 프로퍼티, 파라미터에 적용이 가능하다.
적용 형식은 `@expression`이고 여기서 `expression`은 런타임에 호출되는 함수여야함.

우선 데코레이터에 대해서는 이정도로 알아보고 추후 데코레이터에 대해서도 자세히 알아보겠다. 일단 우선 앞으로 나올 `@~~`가 데코레이팅되어 있다고 보면 되겠다. 

### 사용법

SFC(Single File Components)에서는 반드시 `<script lang="ts"></script>` 속성을 추가하여야한다.

~~~vue
<template>
  <input v-model="job" />
  <button @click="sayName" />
</template>
<script lang="ts">
import { Vue, Component, Model, Prop, Watch } from 'vue-property-decorator'

@Component({
  // options
  components: {
    // ...
  },
  name: 'parson'
})
export default class Parson extends Vue {
  @Model() modelValue: string
  @Prop({ required: true }) name: string

  @Watch('name')
  onChangeName (value: string) {
    console.log(value)
  }

  // data
  age: number = 25

  // methods
  sayName () {
    console.log(this.name)
  }

  //Computed
  get job () {
    return 'develop'
  }
  set job (value: string) {
    this.job = value
  }

  // vue lifeCycle hooks
  created () {
    // ...
  }
  mounted () {
    // ...
  }
}
</script>
~~~

이 기록은 Vue2.* 버전입니다. Vue3에서는 클래스 기반 컴포넌트 사용을 지양하고 Composition API 사용을 권장합니다.
추후에는 Vue3 Composition API를 사용해 보고, 기존 vue-class-component와는 어떤게 다른지도 기록해 보겠습니다.

