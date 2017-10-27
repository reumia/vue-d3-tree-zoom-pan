# Vue + d3 : 줌과 패닝이 가능한 Tree 그래프 그리기

> - Vue : 2.2.6
> - D3 : 4.11.0

`Vue`를 대부분은 '정말 쉽다'라고 소개하곤 하지만 나는 거기에 동의하지는 않는다. `Vue`는 쉽다기보단 간편한 도구이고, 간편한 만큼 그 간편함 뒤에 숨어 있는 구석이 많다. 때문에 익히기는 쉬울 수 있어도 마음껏 사용하기에는 어려움이 많다.

`Vue`는 충분히 자유로이 JavaScript를 쓸 수 있도록 만들어졌지만, 일반적인 JavaScript의 사용 방법과는 사뭇 다르기 때문에 다른 라이브러리를 함께 사용하는 순간이 오면 쉽게 당황해 버리고 만다. 전혀 다른 맥락으로 쓰여진 다른 JavaScript 라이브러리의 문서들은 Vue를 사용하는 순간 무용지물이 되는 것 처럼 느껴진다.

이 포스트에서는 `d3`의 예제와 `Vue`로 작성한 `d3` 예제를 비교해 보면서, Vue에서 DOM과 JavaScript를 작성하는 방법을 소개하려고 한다.

Vue와 d3를 사용해 Tree 구조의 그래프를 그리고, 간단히 Zoom과 Pan을 구현해보자.

## Tree 그리기

> [__d3 공식 튜토리얼__ : Tidy Tree vs Dendogram](https://bl.ocks.org/mbostock/e9ba78a2c1070980d1b530800ce7fa2b)  
> [__Vue 적용 예제__ : Github](https://github.com/reumia/vue-d3-tree-zoom-pan/tree/f5eece5ddf354f4a9c4af093c945a074c6fe5e63)

### data 준비

JavaScript 소스를 읽을 때에는 함수가 정의되는 부분과 실제로 함수가 실행되는 부분을 구분하여 확인하는 것이 중요하다. 튜토리얼 소스는 `d3.csv()`가 실행될 때에 callback으로 실행되는 익명함수가 나머지 모든 함수를 실행하는 구조이다.

`d3.csv`는 `d3.tree`가 사용할 수 있는 JSON 포멧의 데이터를 CSV 포멧의 소스를 기반으로 생성하는 [d3-dsv API](https://github.com/d3/d3-dsv)이며, 본 포스팅에서는 준비된 JSON 포멧의 소스를 기반으로 하기에 간소화할 수 있다.

__튜토리얼 소스__

```javascript
// ...

// d3-tree API가 각 node의 위치를 포함한 tree 객체를 만들어낼 수 있도록 캔버스의 크기를 지정하는 내용이다.
var tree = d3.tree()
    .size([height - 400, width - 160]);

// d3-hierarchy API 옵션을 정의한다.
var stratify = d3.stratify()
    .parentId(function(d) { return d.id.substring(0, d.id.lastIndexOf(".")); });
    
// CSV 포멧으로 전달된 파일을 JSON 객체형태로 Parsing 한다.    
d3.csv("flare.csv", function(error, data) {
  	if (error) throw error;
  
	// d3-hierarchy API로 d3-tree API에 필요한 구조로 data를 JSON 구조로 parsing한다.
	var root = stratify(data).sort(function(a, b) { 
		return (a.height - b.height) || a.id.localeCompare(b.id); 
	});
		
	// JSON으로 parsing된 data를 기반으로 화면을 그린다.
   	// ...
```

__Vue에서의 적용__

```javascript

```


### 그래프 그리기 : DOM Element 핸들링

튜토리얼은 d3만으로 작성되어 있기 때문에, `d3.selection API`를 활용해 DOM Element를 가져오고, `.attr()`로 속성을 추가하거나 `.append()`로 DOM 내에 삽입하는 구조로 작성되어 있지만, `Vue`에서 이와 같은 방식으로 DOM Element를 다루는 것은 어색하다.

크롬 개발자도구를 통해 튜토리얼 소스가 만들어내는 `html`의 형태를 확인하고, `Vue`를 이용해 같은 모양으로 만들어내는 방식으로 작성해 나가면 편리하다.

__튜토리얼 소스__

```html
<svg width="600" height="600"></svg>
```
```javascript
var svg = d3.select("svg"),
    width = +svg.attr("width"),
    height = +svg.attr("height"),
    g = svg.append("g").attr("transform", "translate(40,0)");
    
// ...
    
d3.csv("flare.csv", function(error, data) {
  	// ...
	var link = g.selectAll(".link")
		.data(root.descendants().slice(1))
	   	.enter().append("path")
	   	.attr("class", "link")
	   	.attr("d", diagonal);	
	   	
   	// ...
```

위 코드는 결과적으로 아래와 같은 구조의 `html`을 만들어낸다. 

```html
<svg width="600" height="600">
	<g transform="translate(40,0)">
		<path class="link" d="M200,25.9067...">
		<path class="link" d="M200,25.1761...">
		<path class="link" d="M200,25.1709...">
		<!-- ... -->
```

`.data(root.descendants().slice(1))`로 전달된 `JSON`을 바탕으로 `<path>`를 반복적으로 `<g>`에 `.append()`하는 내용임을 확인한다.

__Vue에서의 적용__

```html
<template></template>
```
```javascript

```

나머지 내용도 위와 동일하게 적용할 수 있으므로 생략한다.  
본 단락의 최상단에 링크한 [__Vue 적용 예제__ : Github](https://github.com/reumia/vue-d3-tree-zoom-pan/tree/f5eece5ddf354f4a9c4af093c945a074c6fe5e63)를 통해 현재 단계에서 작성된 전체 소스를 확인할 수 있다.

## Zoom & Panning

> [__d3 공식 튜토리얼__ :Drag + Zoom](https://bl.ocks.org/mbostock/6123708)  
> [__Vue 적용 예제__ : Github](https://github.com/reumia/vue-d3-tree-zoom-pan/tree/6e66ea95fce60f88db06054be7631890460d21aa)

위 링크한 공식 튜토리얼은 v3를 기준으로 작성하였기 때문에 v4와는 코드가 조금 다르다.
v4를 사용하기 위해서 링크한 튜토리얼로 구조를 참고하고, [d3 공식문서의 API Reference](https://github.com/d3/d3/blob/master/API.md)를 확인하여 변경된 API를 적용하도록 한다.

### d3-zoom API

[d3-zoom API](https://github.com/d3/d3-zoom)의 적용을 위해서는 [d3-selection API](https://github.com/d3/d3-selection)의 사용이 필수적이다. d3-selection API는 DOM Element에 이벤트를 발생시키거나 발생된 이벤트를 `d3.event`를 통해 조회할 수 있도록 도와준다.

`d3-selection API`를 사용하기위해 DOM Element를 `document.querySelector()`등의 방법으로 직접 가져와야 하는데, 이는 Vue에서 권장하는 방법은 아니다. 그러나 이를 Vue의 Lifecycle 내에서 무리없이 지원하기 위해 `$refs`와 `$el`을 제공한다.

`$refs`를 사용해 DOM Element를 가져와 `selection`을 생성하고, d3-zoom API를 적용하여 `d3.event`에 전달된 결과 확인해 보자.

```html
<template>
	<div class="tree">
		<!-- ... -->
		<svg ref="svg" class="container" :width="viewer.w" :height="viewer.h">
    		<!-- ... -->
```
```javascript
export deafault {
	data () { 
		// ...
	},
	created () {
		this.setData()
	},
	mounted () {
		// $refs는 created hook에서는 할당되지 않기 때문에 mounted hook을 사용한다.
		this.setZoom()
	},
	methods: {
		// ...
		setZoom () {
			// zoom의 scale 범위, zoom 이벤트가 실행할 callback 등의 옵션을 정의한다.
			const zoom = d3.zoom().scaleExtent([1, 10]).on('zoom', this.onZoom)
			// selection을 $refs로 생성한다.
	      	const selection = d3.select(this.$refs.svg)
	      	
	      	selection.call(zoom)
		},
		onZoom () {
			// Console을 통해 d3.event에 할당되는 값을 확인한다.
			console.log(d3.event)
		}
	}
```

### Translate, Scale 바인딩

`onZoom()` callback에서 발생한 `d3.event` 값에는 `zoom` 이벤트가 생성한 `d3.event.transform` 객체가 존재한다.

[d3-zoom API](https://github.com/d3/d3-zoom)는 발생한 사용자의 이벤트에 따라 계산된 `transform` 값을 자동으로 생성하고 전달한다. 모든 SVG 요소를 감싸고있는 가장 상위의 SVG 요소 `<g>`에서 변경된 `translate`값과 `scale`을 적용할 수 있도록 Vue Component의 `data`로 설정하고 템플릿에 바인딩한다.

```javascript
export default {
	data () {
		return {
			// D3-zoom 이벤트가 만들어내는 zoom 객체와 동일한 모양으로 구성
			// https://github.com/d3/d3-zoom/blob/master/src/transform.js
			zoom: {
				x: 40,		// Translate를 위한 X좌표 초기값
				y: 0,		// Translate를 위한 Y좌표 초기값
				k: 1		// Scale 초기값
			}
			// ...
		}
	},
	methods: {
		// ...
		onZoom () {
			// 변경된 값을 data에 전달하면, 변경된 내용이 template에 반영된다.
			this.zoom.x = d3.event.transform.x
			this.zoom.y = d3.event.transform.y
			this.zoom.k = d3.event.transform.k
		}
	}
}
```
```html
<template>
	<div class="tree">
		<!-- ... -->
		<svg ref="svg" class="container" :width="viewer.w" :height="viewer.h">
      		<g :transform="`translate(${zoom.x},${zoom.y})scale(${zoom.k})`">
        		<!-- ... -->
```

## d3.zoomIdentity

> [__d3.zoomIdentity 관련 Issue__ : d3 Github repository](https://github.com/d3/d3/issues/2521)  
> [__Vue 적용 예제__ : Github](https://github.com/reumia/vue-d3-tree-zoom-pan/tree/74c850e45d4617aabf991b1812664663a50c4565)

여기까지 작업하고나면 Zoom은 무리없이 동작하지만, 최초에 설정해 두었던 `data.zoom.x`값 때문에 `panning`이나 `zoom`을 시작하기위해 캔버스를 클릭하면 화면이 조금 어색하게 틀어지는 현상이 발생한다.

`data`에서만 정의한 초기 `data.zoom.x`값을 `d3-zoom API`가 인식하고 있지 못하기 때문에 생기는 현상으로, 이를 수정하기 위해 `d3.zoomIdentity`를 적용하고, 그래프의 빈 공간을 클릭했을 때에 `zoom` 상태를 초기하는 기능을 추가해 보자.

```javascript
export default {
	// ...
	methods: {
		// ...
		setZoom () {
			const zoom = d3.zoom().scaleExtent([1, 10]).on('zoom', this.onZoom)
      		const selection = d3.select(this.$refs.svg)

        	selection
          	.call(zoom)
          	// 초기화된 zoom 값을 적용한다.
          	.call(zoom.transform, this.initZoom())
		},
		initZoom () {
			// zoom 값을 초기화한다.
			this.zoom.x = 40
			this.zoom.y = 0
			this.zoom.k = 1
			// 초기화된 zoom 값을 d3-zoom API에 전달한다.
			return d3.zoomIdentity
				.translate(this.zoom.x, this.zoom.y)
				.scale(this.zoom.k)
		},
		// ...
```
```html
<template>
	<div class="tree">
		<!-- ... -->
		<!-- 클릭시 다시 Zoom을 초기화할 수 있도록 @click 속성으로 이벤트를 할당한다. -->
		<svg @click="setZoom" ref="svg" class="container" :width="viewer.w" :height="viewer.h">
    		<!-- ... -->
```


## 마치며.

> [__전체 소스__ : Github](https://github.com/reumia/vue-d3-tree-zoom-pan)

포스팅에서 사용한 전체 소스는 위 링크를 통해 확인할 수 있다. 소스를 클론받고 사용하기 위해서는 아래의 절차를 거치면 된다.

``` bash
# 저장소 클론
git clone https://github.com/reumia/vue-d3-tree-zoom-pan.git

# 의존성 모듈 설치
npm install

# localhost:8080 서버 실행
npm run dev
```

이 포스팅의 예제는 사실 무서운 비밀이 숨어있다. 여기서 그린 Tree 그래프는 속씨식물군의 구조를 선별적으로 나열하고 있는데, 트리의 마지막을 따라가면 놀라운 사실을 알 수 있다.

__여러분..__ 토마토, 감자, 고추가 전부다 가지과라는 사실, 알고 계셨습니까? 내게는 너무나 충격적이었던 [감자 열매 사진](https://www.google.co.kr/search?q=%EA%B0%90%EC%9E%90%EC%97%B4%EB%A7%A4&rlz=1C5CHFA_enKR722KR722&source=lnms&tbm=isch&sa=X&ved=0ahUKEwjrnJ7S_43XAhUKgLwKHTQ5BxgQ_AUICigB&biw=1573&bih=880#imgrc=ZjmbJaCywgOerM:)을 공유하면서 이 포스팅을 마친다.

#### 참고문서

- [Tidy Tree vs. Dendrogram](https://bl.ocks.org/mbostock/e9ba78a2c1070980d1b530800ce7fa2b)
- [Drag & Zoom simple example](https://bl.ocks.org/mbostock/6123708)
- [D3 Zoom initial transition state](https://github.com/d3/d3/issues/2521)
- [Zoom to bound box](https://bl.ocks.org/mbostock/9656675)
