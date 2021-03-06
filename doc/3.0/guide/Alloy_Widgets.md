# Alloy Widgets

## 개요
위젯은 Alloy 프로젝트에 쉽게 붙여넣을 수 있는 독립적인 구성요소로써 자신만의 뷰와 컨트롤러 그리고 스타일, 리소스(assets)등을 가지고 있다. 그리고 다양한 응용 프로그램에서 쉽게 코드를 재사용할 수도 있고 같은 프로그램에서도 여러번 재사용될수 있도록 고안되었다. 

## 위젯 사용하기
자세한 사용법은 [위젯 삽입하기][1] 문서를 참고한다.
[1]: http://docs.appcelerator.com/titanium/latest/#!/guide/Alloy_XML_Markup-section-35621528_AlloyXMLMarkup-ImportingWidgets

## 위젯 만들기
위젯은 Alloy 프로젝트의 app/widgets/ 폴더 하위에 자신만의 폴더 구조(뷰,컨트롤러,모델,스타일 등등)를 갖는다. 자세한 구조는 [설정 규칙][2]을 참고한다. 추가로 위젯은 자신만의 구조를 갖기 때문에 i18n 폴더에 있는 국제화 언어 설정 외에는 다른 경로에 있는 코드나 리소스를 참조할 수 없다. 그밖에 주요 차이점은 다음과 같다.  

[2]: http://docs.appcelerator.com/titanium/latest/#!/guide/Alloy_Concepts-section-34636240_AlloyConcepts-ConventionoverConfiguration

### 리소스(assets)와 라이브러리(libs)
위젯에 있는 assets 이나 libs 폴더에 있는 모든 파일들은 WPATH() 매크로를 파일의 상대 위치를 자동으로 결정하는데, 이때 위젯은 위젯의 루트경로를 기준으로 하지만, Alloy 프로젝트는 app 폴더를 기준으로 한다. 예를 들어, *app/widgets/foo/lib/helper.js* 라는 위치에 라이브러리가 있다면 require(WPATH('helper')); 호출로 불어올수있고, *app/widgets/foo/assets/images/foo.png* 라는 위치에 있는 이미지도 require(WPATH('images/foo.png'))로 불러올수있다. 

### 설정
위젯은 위젯마다 자신만의 설정을 widget.json 파일에 기록하고, 이 설정파일은 위젯의 루트 폴더에 위치한다. 자세한 내용은 [위젯 설정 파일][3]을 참고한다. 

[3]: http://docs.appcelerator.com/titanium/latest/#!/guide/Widget_Configuration_File_(widget.json)

### 컨트롤러 
메인 컨트롤러는 index.js 파일 대신에 widget.js 파일을 호출한다.  

메인 컨트롤러인 widget.js/widget.xml 외에 또다른 뷰-컨트롤러를 사용하려면, *Widget.createController(controller_name, [params])* 메소드를 이용해 새로운 컨트롤러 인스턴스를 만들고, 타이타늄 프록시 객체에 접근 하려면 컨트롤러의 getView() 메소드를 호출한다. 예를 들어, foo라는 위젯 안에 button 컨트롤을 삽입하려면 다음과 같이한다. 

**app/widgets/foo/controllers/widget.js**  
   
```
var button = Widget.createController('button').getView();$.widget.add(button);```

위젯 컨트롤러 안에 있는 모든 메소드들은 비공개 함수이기 때문에 Alloy 프로젝트에서 위젯의 내부 메소드를 사용하고 싶다면, $ 변수를 통해 외부로 공개해야한다. 예를 들어 다음과 같이 init 함수를 정의했다면, 

```
$.init = function (args) {    // Button object with id=button    $.button.title = args.title || 'Si';     $.button.color = args.color || 'black';     // global variable    message = args.message || 'Hola mundo';}```
Alloy 프로젝트에서 init을 함수를 접근하기 위해서는 $ 다음에 위젯의 ID를 먼저 참조해야한다. 따라서 다음과 같이 호출해야한다. 
```$.foo.init({title:'Yes', color:'gray', message:'I pity the foo.'});```

### 모델 
위젯에서 모델을 사용하는 방법은 일반적인 Alloy 프로젝트와 같다. 다만 모델이나 콜렉션을 생성하려면 다음과 같이 Widget.createModel(model_name, [params]) 이나 Widget.createCollection(model_name, [params]) 메소드를 호출해야한다. 위젯 뷰에서도 Model과 Collection 태그를 이용해 생성할수도 있다. 

### 스타일
메인 tss 파일은 index.tss 파일 대신에 widget.tss 파일을 호출한다. 

### 뷰 
메인 뷰 파일은 index.xml 대신에 widget.xml 파일을 호출한다.

XML 마크업 요소에 id 속성을 지정하면, 타이타늄 객체에서 쉽게 접근 할수있다. 예를 들어, 위젯의 id가 foo 이고 이 위젯에 button 이라는 id를 가진 버튼 객체가 있다면 Alloy 프로젝트에서 버튼을 접근하기 위해서 다음과 같이 호출한다. 

```
Ti.API.info("button state: " + $.foo.button.enabled);
```

위젯 안에 또 다른 위젯을 포함하는 위젯은 Widget 태그에 name 속성을 부여한다. 이때 name 값은 뷰-컨트롤러에서 파일 확장자를 제외한 이름이다. 예를 들어 다음과 같은 마크업은 컨트롤러 섹션에 있는 예제와 유사하다. 
**app/widgets/foo/views/widget.xml**

```
<Alloy>	<View>		<Widget src="foo" name="button"/>	</View></Alloy>```

### 위젯
[위젯 삽입하기][1]에 있는 방법에 따라 위젯은 또 다른 위젯을 포함할 수도 있다. 이때 위젯 안에 추가되는 위젯은 widget.json 파일에 의존성을 기술해야된다. 이렇게 widget.json 설정 파일에 의존성이 기술된 위젯을 생성하려면 Widget.createWidget(widget_name, [controller_name], [params]) 메소드를 사용한다. 참고로 Alloy 프로젝트에서 위젯을 사용할때는 config.json 설정 파일에 의존성을 기술했지만, 위젯 안에 또다른 위젯을 정의하는 경우에는 위젯 설정파일(widget.json)에 의존성을 기술한다.   