---
layout: post
title: fabric 캔버스 유용한 기능
date: 2023-08-18 08:47:00 +0900
description: >
  HTML Canvas Library
lastmod: 2023-08-18 08:47:00 +0900
sitemap:
  changefreq: weekly
---

0. 
{:toc}

fabric.js 자주 쓰고 유용한 메소드, 프로퍼티를 정리합니다.

## Canvas

[Canvas 문서](http://fabricjs.com/docs/fabric.Canvas.html){:target=""_blank}
{:.note title="링크"}

### 프로퍼티(property)

#### preserveObjectStacking
`preserveObjectStacking: boolean` 기본값 false

선택한 오브젝트를 현재 순서 위치를 유지할지 여부를 정함, false일 경우 선택한 오브젝트가 가장 위로 가져옴
~~~ts
const canvas = new fabric.Canvas('canvas', {
  preserveObjectStacking: true
})
~~~

#### selection
`selection: boolean` 기본값 true

다중 선택 가능 여부를 정함, false일 경우 드래그 다중 선택이 불가능함
~~~ts
const canvas = new fabric.Canvas('canvas', {
  selection: false
})
~~~

#### moveCursor
`moveCursor: string` 기본값 default

캔버스에 기본 커서 값 `ew-resize`, `ns-resize`... 등등 적용이 가능
~~~ts
const canvas = new fabric.Canvas('canvas', {
  moveCursor: 'ew-resize'
})
~~~


### 메소드(method)

#### getObjects
`getObjects(type?: string): fabric.Object[]`

캔버스 안에 속한 오브젝트들을 배열로 리턴(복사본 배열), 매개변수로 오브젝트 타입을 전달하면 해당 타입의 오브젝트들만 리턴
~~~ts
canvas.getObjects()
// or
canvas.getObjects('image')
~~~

#### getActiveObject
`getActiveObject(): fabric.Object`

캔버스에 현재 선택중인 오브젝트를 리턴
~~~ts
canvas.getActiveObject()
~~~

#### setActiveObject
`setActiveObject(object: fabric.Object)`

오브젝트를 캔버스 선택 오브젝트로 설정

~~~ts
canvas.setActiveObject(object)
~~~

#### discardActiveObject
`discardActive(e: Event)`

캔버스에 현재 선택중인 오브젝트를 선택 해제

~~~ts
canvas.discardActiveObject()
~~~


#### toObject
`toObject(propertiesToInclude: string[])`

캔버스 데이터를 추출한다. 매개변수로 캔버스 안에 추가 속성을 배열에 넣어 전달하면 추출 시 오브젝트에 추가 속성을 같이 추출
~~~ts
canvas.toObject()
// or
canvas.toObject(['selectable', 'evented'])

// {version: '4.6.0', objects: [], background: 'rgba(0, 0, 0, 0)'}
~~~

#### toDataURL
`toDataURL(format?: string, quality? number...): string`

캔버스를 URL 이미지(base64)로 리턴. 매개변수로 옵션 설정시 이미지 형식이나, 퀄리티를 변경 가능
~~~ts
canvas.toDataURL()
// or
canvas.toDataURL({
  format: 'png',
  quality: 1
})
~~~

#### moveTo
`moveTo(object: fabric.Object, index: number)`

오브젝트의 노출 순서를 변경
~~~ts
canvas.moveTo(object, 1)
~~~

#### sendToBack, bringToFront
`sendToBack(object: fabric.Object), bringToFront(object: fabric.Object)`

`sendToBack`는 오브젝트를 맨 아래로 순서 이동, `bringToFront`는 오브젝트를 맨 위로 순서 이동

~~~ts
canvas.sendToBack(object)
canvas.bringToFront(object)
~~~