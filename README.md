# flutter_clean_architecture Package


![CI](https://github.com/shadyboukhary/flutter_clean_architecture/workflows/Continuous%20Integration/badge.svg)


## Overview
[Uncle Bob의 Clean Architecture](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)를 Flutter에서 쉽고 직관적으로 구현할 수 있게 해주는 Flutter 패키지입니다. 이 패키지는 기본 클래스를 제공하는데, 이는 Flutter와 함께 작동하도록 조정되었고 Clean Architecture에 따라 설계되었습니다. 

## Installation

### 1. Depend on It
Add this to your package's pubspec.yaml file:

```yaml

dependencies:
  flutter_clean_architecture: ^5.0.4

```

### 2. Install it
You can install packages from the command line:

with Flutter:

```shell
$ flutter packages get
```

Alternatively, your editor might support `flutter packages get`. Check the docs for your editor to learn more.

### 3. Import it
Now in your Dart code, you can use:

```dart
import 'package:flutter_clean_architecture/flutter_clean_architecture.dart';
```

## Flutter Clean Architecture Primer
### Introduction
Uncle Bob의 책과 블로그를 기반으로 한 아키텍처입니다. Onion 아키텍처와 다른 아키텍처에서 가져온 개념의 조합입니다. 아키텍처의 주요 초점은 관심사의 분리와 확장성입니다. `App`, `Domain`, `Data` 및 `Device`의 네 가지 주요 모듈로 구성됩니다.

### The Dependency Rule
**소스 코드 종속성은 안쪽만 가리킵니다**. 
즉, 내부 모듈은 외부 모듈을 인식하지도 종속되지도 않습니다. 
그러나 외부 모듈은 내부 모듈을 인식하고 의존합니다. 
외부 모듈은 비즈니스 규칙 및 정책(내부 모듈)이 작동하는 메커니즘을 나타냅니다. 
더 많이 안으로 들어갈수록 더 많은 추상화가 나타납니다. 
바깥쪽으로 이동할수록 더 구체적인 구현이 나타납니다. 
내부 모듈은 외부 모듈에 있는 클래스, 함수, 이름, 라이브러리 등을 인식하지 못합니다. 
그들은 단순히 **규칙**을 나타내며 구현과 완전히 독립적입니다.

### Layers

#### Domain
`Domain` 모듈은 애플리케이션의 비즈니스 로직을 정의합니다. 
개발 플랫폼과 독립적인 모듈입니다. 
즉, 순전히 프로그래밍 언어로 작성되고 플랫폼의 요소를 포함하지 않습니다. 
`Flutter`의 경우 `Domain`은 `Flutter` 요소 없이 순전히 `Dart`로 작성됩니다. 
`Domain`은 구현 세부 사항이 아닌 애플리케이션의 비즈니스 로직에만 관련되어야 하기 때문입니다. 
또한 문제가 발생할 경우 플랫폼 간에 쉽게 마이그레이션할 수 있습니다.

##### Contents of Domain
`도메인`은 여러 가지로 구성되어 있습니다.
* **엔터티(Entities)**
   * 전사적(Enterprise-wide) 비즈니스 규칙
   * 메서드를 포함할 수 있는 클래스로 구성
   * 애플리케이션의 비즈니스 객체
   * 응용 프로그램 전체에 사용됨
   * 응용 프로그램의 내용이 변경될 때 변경 가능성이 가장 낮음
* **유스케이스(Usecases)**
   * 애플리케이션별 비즈니스 규칙
   * 애플리케이션의 모든 사용 사례를 캡슐화
   * 앱 전체의 데이터 흐름 조정
   * 어떤 UI 변경에도 영향을 받지 않아야 합니다.
   * 애플리케이션의 기능 및 흐름이 변경되면 변경될 수 있습니다.
* **저장소(Repositories)**
   * 외부 레이어의 예상 기능을 정의하는 추상 클래스
   * 외부 레이어를 인식하지 않고 예상되는 기능만 정의
     * 예. `Login` usecase 에는 `login` 기능이 있는 `Repository`가 필요합니다.
   * 외부 레이어에서 `Usecases`로 전달됨

'도메인'은 가장 안쪽 레이어를 나타냅니다. 따라서 아키텍처에서 가장 추상적인 계층입니다.

#### App
`앱`은 `도메인` 외부의 레이어입니다. 
`앱`은 레이어의 경계를 넘어 `도메인`과 통신합니다. 
그러나 **종속성 규칙**은 위반되지 않습니다.
`다형성`을 사용하여 `App`은 상속된 클래스를 사용하여 `Domain`과 통신합니다. 클래스는 `Domain` 계층에 있는 `Repositories`를 구현하거나 확장합니다. 
`다형성`이 사용되기 때문에 `도메인`에 전달된 `저장소`는 `도메인`에 관한 한 추상적이기 때문에 여전히 **종속성 규칙**을 준수합니다. 
구현은 `다형성` 뒤에 숨겨져 있습니다.

##### Contents of App
`App`은 애플리케이션의 프리젠테이션 계층이므로 UI 및 UI의 이벤트 핸들러를 포함하므로 프레임워크에 가장 의존적인 계층입니다. 
애플리케이션의 모든 페이지에 대해 `App`은 `Controller`, `Presenter` 및 `View`의 3개 이상의 클래스를 정의합니다.

* 페이지의 UI 만을 나타냅니다.
`View`는 페이지의 UI를 만들고 스타일을 지정하며 이벤트를 처리하기 위해 `Controller`에 의존합니다. 
The `View` **Has a ** `Controller`.
   * 플러터의 경우
     * `View`는 2개의 클래스로 구성됩니다.
       * 하나는 `View`를 확장한 클래스로, 이는 `View`를 나타내는 루트 `Widget`입니다.
       * 다른 하나는 다른 클래스와 그의 `Controller`의 템플릿 특수화를 가진 `ViewState`를 확장한 클래스입니다.
     * `ViewState`는 기술적으로 UI 구현인 `view` getter가 포함되어 있습니다.
     * `StatefulWidget`에는 `Flutter`에 따른 `State`가 포함됩니다.
     * `StatefulWidget`은 제목 등과 같은 다른 페이지에서 `State`로 인수를 전달하는 역할만 합니다. `State` 개체(`ViewState`)를 인스턴스화하고 이를 통해 필요한 `Controller`를 제공합니다. consumer 입니다.
     * `StatefulWidget` **에는 `Controller`가 **있는** `State` 개체(`ViewState`)가 있습니다.
     * 요약하면 `StatefulWidget`과 `State`는 모두 페이지의 `View`와 `ViewState`로 표현됩니다.
     * `ViewState` 클래스는 스캐폴드에서 키로 사용할 수 있는 `GlobalKey`를 관리합니다. 
     사용하는 경우 `컨트롤러`는 스낵바 및 기타 대화 상자를 표시하기 위해 `getState()`를 통해 쉽게 액세스할 수 있습니다. 
     이는 선택 사항입니다.


* **Controller**
  * Every `ViewState` **has-a** `Controller`. The `Controller` provides the needed member data of the `ViewState` i.e. dynamic data. The `Controller` also implements the event-handlers of the `ViewState` widgets, but has no access to the `Widgets` themselves. The `ViewState` uses the `Controller`, not the other way around. When the `ViewState` calls a handler from the `Controller`, `refreshUI()` can be called to update the view.
  * Every `Controller` extends the `Controller` abstract class, which implements `WidgetsBindingObserver`. Every `Controller` class is responsible for handling lifecycle events for the `View` and can override:
    * **void onInActive()**
    * **void onPaused()** 
    * **void onResumed()** 
    * **void onDetached()**
    * **void onDisposed()**
    * **void onReassembled()**
    * **void onDidChangeDependencies()**
    * **void onInitState()**
    * etc..
  * Also, every `Controller` **has** to implement **initListeners()** that initializes the listeners for the `Presenter` for consistency.
  * The `Controller` **has-a** `Presenter`. The `Controller` will pass the `Repository` to the `Presenter`, which it communicate later with the `Usecase`. The `Controller` will specify what listeners the `Presenter` should call for all success and error events as mentioned previously. Only the `Controller` is allowed to obtain instances of a `Repository` from the `Data` or `Device` module in the outermost layer.
  * The `Controller` has access to the `ViewState` and can refresh the `ControlledWidgets` via `refreshUI()`.

* 모든 `ViewState`에는 **컨트롤러**가 있습니다. `컨트롤러`는 `ViewState`의 필수 멤버 데이터, 즉 동적 데이터를 제공합니다. `Controller`는 `ViewState` 위젯의 이벤트 핸들러도 구현하지만 `Widgets` 자체에는 액세스할 수 없습니다. `ViewState`는 `Controller`를 사용하며 그 반대는 아닙니다. `ViewState`가 `Controller`에서 핸들러를 호출하면 `refreshUI()`를 호출하여 보기를 업데이트할 수 있습니다.
   * 모든 `Controller`는 `WidgetsBindingObserver`를 구현하는 `Controller` 추상 클래스를 확장합니다. 모든 `Controller` 클래스는 `View`에 대한 수명 주기 이벤트를 처리할 책임이 있으며 다음을 재정의할 수 있습니다.
     * **void onInActive()**
     * **void onPaused()**
     * **void onResumed()**
     * **void onDetached()**
     * **void onDisposed()**
     * **void onReassembled()**
     * **void onDidChangeDependencies()**
     * **void onInitState()**
     * 등..
   * 또한 모든 `Controller`는 일관성을 위해 `Presenter`에 대한 리스너를 초기화하는 **initListeners()**를 구현해야 **해야 합니다.
   * `컨트롤러` **에는 ** `presenter`가 있습니다. `Controller`는 `Repository`를 `Presenter`로 전달하고 나중에 `Usecase`와 통신합니다. `Controller`는 앞에서 언급한 모든 성공 및 오류 이벤트에 대해 `Presenter`가 호출해야 하는 리스너를 지정합니다. '컨트롤러'만 가장 바깥쪽 레이어의 '데이터' 또는 '기기' 모듈에서 '리포지토리' 인스턴스를 가져올 수 있습니다.
   * `Controller`는 `ViewState`에 액세스할 수 있으며 `refreshUI()`를 통해 `ControlledWidgets`를 새로 고칠 수 있습니다.


* **Presenter**
  * Every `Controller` **has-a** `Presenter`. The `Presenter` communicates with the `Usecase` as mentioned at the beginning of the `App` layer. The `Presenter` will have members that are functions, which are optionally set by the `Controller` and will be called if set upon the `Usecase` sending back data, completing, or erroring.
  * The `Presenter` is comprised of two classes
    * `Presenter` e.g. `LoginPresenter`
      * Contains the event-handlers set by the `Controller`
      * Contains the `Usecase` to be used
      * Initializes and executes the usecase with the `Observer<T>` class and the appropriate arguments. E.g. with `username` and `password` in the case of a `LoginPresenter`
    * A class that implements `Observer<T>`
      * Has reference to the `Presenter` class. Ideally, this should be an inner class but `Dart` does not yet support them.
      * Implements 3 functions
        * **onNext(T)**
        * **onComplete()**
        * **onError()**
      * These 3 methods represent all possible outputs of the `Usecase`
        * If the `Usecase` returns an object, it will be passed to `onNext(T)`. 
        * If it errors, it will call `onError(e)`. 
        * Once it completes, it will call `onComplete()`. 
       * These methods will then call the corresponding methods of the `Presenter` that are set by the `Controller`. This way, the event is passed to the `Controller`, which can then manipulate data and update the `ViewState`
* Extra
  * `Utility` classes (any commonly used functions like timestamp getters etc..)
  * `Constants` classes (`const` strings for convenience)
  * `Navigator` (if needed)

* 모든 `Controller`에는 **`Presenter`가 있습니다**. `Presenter`는 `App` 계층의 시작 부분에서 언급한 `Usecase`와 통신합니다. `Presenter`에는 `Controller`에 의해 선택적으로 설정되는 기능인 멤버가 있으며 `Usecase`에서 데이터를 다시 보내거나 완료하거나 오류가 발생하면 호출됩니다.
   * `Presenter`는 두 개의 클래스로 구성됩니다.
     * `presenter` 예: `로그인 프리젠터`
       * `Controller`에 의해 설정된 이벤트 핸들러를 포함합니다.
       * 사용할 `Usecase`를 포함합니다.
       * `Observer<T>` 클래스와 적절한 인수를 사용하여 사용 사례를 초기화하고 실행합니다. 예를 들어 `LoginPresenter`의 경우 `username` 및 `password` 사용
     * `Observer<T>`를 구현하는 클래스
       * `Presenter` 클래스에 대한 참조가 있습니다. 이상적으로 이것은 내부 클래스여야 하지만 `Dart`는 아직 이를 지원하지 않습니다.
       * 3가지 기능 구현
         * **온넥스트(T)**
         * **onComplete()**
         * **onError()**
       * 이 3가지 방법은 `Usecase`의 가능한 모든 출력을 나타냅니다.
         * `Usecase`가 객체를 반환하면 `onNext(T)`로 전달됩니다.
         * 오류가 발생하면 `onError(e)`를 호출합니다.
         * 완료되면 `onComplete()`를 호출합니다.
        * 이 메서드는 `Controller`에 의해 설정된 `Presenter`의 해당 메서드를 호출합니다. 이렇게 하면 이벤트가 `컨트롤러`로 전달되어 데이터를 조작하고 `ViewState`를 업데이트할 수 있습니다.
* 추가로
   * `Utility` 클래스(타임스탬프 게터 등과 같이 일반적으로 사용되는 함수)
   * `Constants` 클래스(편의상 `const` 문자열)
   * `네비게이터`(필요한 경우)
  
#### Data
Represents the data-layer of the application. The `Data` module, which is a part of the outermost layer, is responsible for data retrieval. This can be in the form of API calls to a server, a local database, or even both. 

애플리케이션의 데이터 계층을 나타냅니다. 가장 바깥쪽 레이어의 일부인 '데이터' 모듈은 데이터 검색을 담당합니다. 이것은 서버, 로컬 데이터베이스 또는 둘 다에 대한 API 호출의 형태일 수 있습니다.
##### Contents of Data
* **Repositories**
  * Every `Repository` **should** implement `Repository` from the **Domain** layer.
  * Using `polymorphism`, these repositories from the data layer can be passed across the boundaries of layers, starting from the `View` down to the `Usecases` through the `Controller` and `Presenter`.
  * Retrieve data from databases or other methods. 
  * Responsible for any API calls and high-level data manipulation such as
    * Registering a user with a database
    * Uploading data
    * Downloading data
    * Handling local storage
    * Calling an API
* **Models** (not a must depending on the application)
  * Extensions of `Entities` with the addition of extra members that might be platform-dependent. For example, in the case of local databases, this can be manifested as an `isDeleted` or an `isDirty` entry in the local database. Such entries cannot be present in the `Entities` as that would violate the **Dependency Rule** since **Domain** should not be aware of the implementation.
  * In the case of our application, models in the `Data` layer will not be necessary as we do not have a local database. Therefore, it is unlikely that we will need extra entries in the `Entities` that are platform-dependent.
* **Mappers**
  * Map `Entity` objects to `Models` and vice-versa.
  * Static classes with static methods that receive either an `Entity` or a `Model` and return the other.
  * Only necessary in the presence of `Models`
* Extra
  * `Utility` classes if needed
  * `Constants` classes if needed

* **저장소**
   * 모든 `저장소`는 **도메인** 계층의 `저장소`를 구현해야 합니다**.
   * `다형성`을 사용하면 데이터 레이어의 이러한 저장소를 `View`에서 시작하여 `Controller` 및 `Presenter`를 통해 `Usecases`까지 레이어 경계를 넘어 전달할 수 있습니다.
   * 데이터베이스 또는 기타 방법에서 데이터를 검색합니다.
   * 모든 API 호출 및 다음과 같은 높은 수준의 데이터 조작을 담당합니다.
     * 데이터베이스에 사용자 등록
     * 데이터 업로드
     * 데이터 다운로드 중
     * 로컬 스토리지 처리
     * API 호출
* **모델**(애플리케이션에 따라 필수는 아님)
   * 플랫폼에 따라 다를 수 있는 추가 멤버를 추가하여 '엔티티' 확장. 예를 들어 로컬 데이터베이스의 경우 로컬 데이터베이스에서 `isDeleted` 또는 `isDirty` 항목으로 나타날 수 있습니다. 이러한 항목은 **도메인**이 구현을 인식하지 않아야 하므로 **종속성 규칙**을 위반하므로 '엔티티'에 존재할 수 없습니다.
   * 우리 애플리케이션의 경우 로컬 데이터베이스가 없기 때문에 '데이터' 레이어의 모델이 필요하지 않습니다. 따라서 플랫폼에 따라 달라지는 'Entities'에 추가 항목이 필요하지 않을 것입니다.
* **매퍼**
   * `Entity` 개체를 `Models`에 매핑하고 그 반대도 마찬가지입니다.
   * `엔티티` 또는 `모델`을 수신하고 다른 것을 반환하는 정적 메서드가 있는 정적 클래스.
   * `모델`이 있는 경우에만 필요합니다.
* 추가의
   * 필요한 경우 `유틸리티` 클래스
   * 필요한 경우 `상수` 클래스


#### Device
Part of the outermost layer, `Device` communicates directly with the platform i.e. Android and iOS. `Device` is responsible for Native functionality such as `GPS` and other functionality present within the platform itself like the filesystem. `Device` calls all Native APIs. 

가장 바깥쪽 레이어의 일부인 'Device'는 Android 및 iOS와 같은 플랫폼과 직접 통신합니다. '장치'는 'GPS'와 같은 기본 기능과 파일 시스템과 같은 플랫폼 자체 내에 존재하는 기타 기능을 담당합니다. `Device`는 모든 기본 API를 호출합니다.

##### Contents of Data
* **Devices**
  * Similar to `Repositories` in `Data`, `Devices` are classes that communicate with a specific functionality in the platform.
  * Passed through the layers the same way `Repositories` are pass across the boundaries of the layer: using polymorphism between the `App` and `Domain` layer. That means the `Controller` passes it to the `Presenter` then the `Presenter` passes it polymorphically to the `Usecase`, which receives it as an abstract class.
* Extra
  * `Utility` classes if needed
  * `Constants` classes if needed

* **Devices**
   * `Data`의 `Repositories`와 마찬가지로 `Devices`는 플랫폼의 특정 기능과 통신하는 클래스입니다.
   * 'Repositories'가 레이어의 경계를 통과하는 것과 같은 방식으로 레이어를 통과합니다. 즉, 'App'과 'Domain' 레이어 사이의 다형성을 사용합니다. 즉, `Controller`가 이를 `Presenter`로 전달한 다음 `Presenter`가 이를 추상 클래스로 수신하는 `Usecase`로 다형적으로 전달합니다.
* Extra
   * 필요한 경우 `Utility` 클래스
   * 필요한 경우 `Constants` 클래스


## Usage

### Folder structure

```
lib/
    app/                          <--- application layer
        pages/                        <-- pages or screens
          login/                        <-- some page in the app
            login_controller.dart         <-- login controller extends `Controller`
            login_presenter.dart          <-- login presenter extends `Presenter`
            login_view.dart               <-- login view, 2 classes extend `View` and `ViewState` resp.
        widgets/                      <-- custom widgets
        utils/                        <-- utility functions/classes/constants
        navigator.dart                <-- optional application navigator
    data/                         <--- data layer
        repositories/                 <-- repositories (retrieve data, heavy processing etc..)
          data_auth_repo.dart           <-- example repo: handles all authentication
        helpers/                      <-- any helpers e.g. http helper
        constants.dart                <-- constants such as API keys, routes, urls, etc..
    device/                       <--- device layer
        repositories/                 <--- repositories that communicate with the platform e.g. GPS
        utils/                        <--- any utility classes/functions
    domain/                       <--- domain layer (business and enterprise) PURE DART
        entities/                   <--- enterprise entities (core classes of the app)
          user.dart                   <-- example entity
          manager.dart                <-- example entity
        usecases/                   <--- business processes e.g. Login, Logout, GetUser, etc..
          login_usecase.dart          <-- example usecase extends `UseCase` or `CompletableUseCase`
        repositories/               <--- abstract classes that define functionality for data and device layers
    main.dart                     <--- entry point

```

```
lib/
     app/                         <--- 애플리케이션 계층
         pages/                       <-- 페이지 또는 화면
           login/                       <-- 앱의 일부 페이지
             login_controller.dart        <-- 로그인 컨트롤러는 `컨트롤러`를 확장합니다.
             login_presenter.dart         <-- 로그인 presenter는 `Presenter`를 확장합니다.
             login_view.dart              <-- 로그인 보기, 2개의 클래스는 `View` 및 `ViewState` resp를 확장합니다.
         widgets/                       <-- 맞춤 위젯
         utils/                         <-- 유틸리티 함수/클래스/상수
         navigator.dart                 <-- 선택적 애플리케이션 네비게이터
     data/ <--- 데이터 레이어
         repositories/ <-- 저장소(데이터 검색, 과도한 처리 등..)
           data_auth_repo.dart <-- 예제 저장소: 모든 인증 처리
         helpers/ <-- 모든 도우미 e.g. HTTP 도우미
         constants.dart <-- API 키, 경로, URL 등과 같은 상수.
     device/ <--- 장치 계층
         repositories/ <--- 플랫폼과 통신하는 저장소 e.g. GPS
         utils/ <--- 모든 유틸리티 클래스/함수
     domain/ <--- 도메인 계층(비즈니스 및 엔터프라이즈) PURE DART
         entities/ <--- 엔터프라이즈 엔티티(앱의 핵심 클래스)
           user.dart <-- 예제 엔터티
           manager.dart <-- 예제 엔터티
         usecases/ <--- 비즈니스 프로세스 e.g. 로그인, 로그아웃, GetUser 등
           login_usecase.dart <-- `UseCase` 또는 `CompletableUseCase`를 확장하는 사용 사례 예
         repositories/ <--- 데이터 및 장치 계층에 대한 기능을 정의하는 추상 클래스
     main.dart <--- 진입점

```
### Example Code
Checkout a small example [here](./example/) and a full application built [here](https://github.com/ShadyBoukhary/Axion-Technologies-HnH).

[여기](./example/)에서 작은 예제를 확인하고 [여기](https://github.com/ShadyBoukhary/Axion-Technologies-HnH)에서 빌드한 전체 애플리케이션을 확인하세요.

#### View and ControlledWidgetBuilder

```dart
import 'package:flutter_clean_architecture/flutter_clean_architecture.dart';
class CounterPage extends View {
    @override
     // Dependencies can be injected here
     State<StatefulWidget> createState() => CounterState();
}

class CounterState extends ViewState<CounterPage, CounterController> {
     CounterState() : super(CounterController());

     @override
     Widget get view => MaterialApp(
        title: 'Flutter Demo',
        home: Scaffold(
        key: globalKey, // using the built-in global key of the `View` for the scaffold or any other
                        // widget provides the controller with a way to access them via getContext(), getState(), getStateKey()
        body: Column(
          children: <Widget>[
            Center(
              // show the number of times the button has been clicked
              child: ControlledWidgetBuilder<CounterController>(
                builder: (context, controller) {
                  return Text(controller.counter.toString());
                }
              ),
            ),
            // you can refresh manually inside the controller
            // using refreshUI()
            ControlledWidgetBuilder<CounterController>(
                builder: (context, controller) {
                  return MaterialButton(onPressed: controller.increment);
                }
              ),
          ],
        ),
      ),
    );
}
```
##### Responsive view state

To deal with screens on flutter web, you can take advantage of the responsive view state,
that abstracts the main web apps breakpoints (desktop, tablet and mobile) to ease development for web with `flutter_clean_architecture`

Flutter 웹에서 화면을 처리하려면 반응형 보기 상태를 활용할 수 있습니다.
`flutter_clean_architecture`로 웹 개발을 쉽게 하기 위해 주요 웹 앱 중단점(데스크톱, 태블릿 및 모바일)을 추상화합니다.

For example:

```dart
import 'package:flutter_clean_architecture/flutter_clean_architecture.dart';
class CounterPage extends View {
    @override
     // Dependencies can be injected here
     State<StatefulWidget> createState() => CounterState();
}

class CounterState extends ResponsiveViewState<CounterPage, CounterController> {
     CounterState() : super(CounterController());

     Widget AppScaffold({Widget child}) {
         return MaterialApp(
              title: 'Flutter Demo',
           home: Scaffold(
             key: globalKey, // using the built-in global key of the `View` for the scaffold or any other
                             // widget provides the controller with a way to access them via getContext(), getState(), getStateKey()
             body: child
           ),
         );
     }
 
     @override
     ViewBuilder get mobileView => AppScaffold(
         child: Column(
             children: <Widget>[
               // you can refresh manually inside the controller
               // using refreshUI()
               ControlledWidgetBuilder<CounterController>(
                 builder: (context, controller) {
                   return Text('Counter on mobile view ${controller.counter.toString()}');
                 }
               ),
             ],
           )
      );

     @override
     ViewBuilder get tabletBuilder => AppScaffold(
       child: Column(
           children: <Widget>[
             // you can refresh manually inside the controller
             // using refreshUI()
             ControlledWidgetBuilder<CounterController>(
               builder: (context, controller) {
                 return Text('Counter on tablet view ${controller.counter.toString()}');
               }
             ),
           ],
         )
     );

     @override
     ViewBuilder get desktopBuilder => AppScaffold(
        child: Row(
            children: <Widget>[
              // you can refresh manually inside the controller
              // using refreshUI()
              ControlledWidgetBuilder<CounterController>(
                builder: (context, controller) {
                  return Text('Counter on desktop view ${controller.counter.toString()}');
                }
              ),
            ],
          )
      );
}
```

##### Widgets with Common Controller
In the event that multiple widgets need to use the same `Controller` of a certain `Page`,
the `Controller` can be retrieved inside the children widgets of that page via 
`FlutterCleanArchitecture.getController<HomeController>(context)`.

여러 개의 위젯이 특정 `Page`의 동일한 `Controller`를 사용해야 하는 경우,
`컨트롤러`는 `FlutterCleanArchitecture.getController<HomeController>(context)`를 통해 해당 페이지의 하위 위젯 내에서 검색할 수 있습니다.

For example:

```dart

import '../pages/home/home_controller.dart';
import 'package:flutter/material.dart';
import 'package:flutter_clean_architecture/flutter_clean_architecture.dart';

class HomePageButton extends StatelessWidget {
  final String text;
  HomePageButton({@required this.text});

  @override
  Widget build(BuildContext context) {
    // use a common controller assuming HomePageButton is always a child of Home
    HomeController controller =
        FlutterCleanArchitecture.getController<HomeController>(context);
    return GestureDetector(
      onTap: controller.buttonPressed,
      child: Container(
        height: 50.0,
        alignment: FractionalOffset.center,
        decoration: BoxDecoration(
          color: Color.fromRGBO(230, 38, 39, 1.0),
          borderRadius: BorderRadius.circular(25.0),
        ),
        child: Text(
          text,
          style: const TextStyle(
              color: Colors.white,
              fontSize: 20.0,
              fontWeight: FontWeight.w300,
              letterSpacing: 0.4),
        ),
      ),
    );
  }
}

```


#### Controller 

```dart
import 'package:flutter_clean_architecture/flutter_clean_architecture.dart';

class CounterController extends Controller {
  int counter;
  final LoginPresenter presenter;
  CounterController() : counter = 0, presenter = LoginPresenter(), super();

  void increment() {
    counter++;
  }

  /// Shows a snackbar
  void showSnackBar() {
    ScaffoldState scaffoldState = getState(); // get the state, in this case, the scaffold
    scaffoldState.showSnackBar(SnackBar(content: Text('Hi')));
  }

  @override
  void initListeners() {
    // Initialize presenter listeners here
    // These will be called upon success, failure, or data retrieval after usecase execution
     presenter.loginOnComplete = () => print('Login Successful');
     presenter.loginOnError = (e) => print(e);
     presenter.loginOnNext = () => print("onNext");
  }

  void login() {
      // pass appropriate credentials here
      // assuming you have text fields to retrieve them and whatnot
      presenter.login();
  }
}

```

#### Presenter
```dart
import 'package:flutter_clean_architecture/flutter_clean_architecture.dart';

class LoginPresenter() {

  Function loginOnComplete; // alternatively `void loginOnComplete();`
  Function loginOnError;
  Function loginOnNext; // not needed in the case of a login presenter

  final LoginUseCase loginUseCase;
  // dependency injection from controller
  LoginPresenter(authenticationRepo): loginUseCase = LoginUseCase(authenticationRepo);

  /// login function called by the controller
  void login(String email, String password) {
    loginUseCase.execute(_LoginUseCaseObserver(this), LoginUseCaseParams(email, password));
  }

   /// Disposes of the [LoginUseCase] and unsubscribes
   @override
   void dispose() {
     _loginUseCase.dispose();
   }
}

/// The [Observer] used to observe the `Stream` of the [LoginUseCase]
class _LoginUseCaseObserver implements Observer<void> {

  // The above presenter
  // This is not optimal, but it is a workaround due to dart limitations. Dart does
  // not support inner classes or anonymous classes.
  final LoginPresenter loginPresenter;

  _LoginUseCaseObserver(this.loginPresenter);

  /// implement if the `Stream` emits a value
  // in this case, unnecessary
  void onNext(_) {}

  /// Login is successful, trigger event in [LoginController]
  void onComplete() {
    // any cleaning or preparation goes here
    assert(loginPresenter.loginOnComplete != null);
    loginPresenter.loginOnComplete();

  }

  /// Login was unsuccessful, trigger event in [LoginController]
  void onError(e) {
    // any cleaning or preparation goes here
    assert(loginPresenter.loginOnError != null);
    loginPresenter.loginOnError(e);
  }
}

```
#### UseCase
```dart
import 'package:flutter_clean_architecture/flutter_clean_architecture.dart';

// In this case, no parameters were needed. Hence, void. Otherwise, change to appropriate.
class LoginUseCase extends CompletableUseCase<LoginUseCaseParams> {
  final AuthenticationRepository _authenticationRepository; // some dependency to be injected
                                          // the functionality is hidden behind this
                                          // abstract class defined in the Domain module
                                          // It should be implemented inside the Data or Device
                                          // module and passed polymorphically.

  LoginUseCase(this._authenticationRepository);

  @override
  // Since the parameter type is void, `_` ignores the parameter. Change according to the type
  // used in the template.
  Future<Stream<void>> buildUseCaseStream(params) async {
    final StreamController controller = StreamController();
    try {
        // assuming you pass credentials here
      await _authenticationRepository.authenticate(email: params.email, password: params.password);
      logger.finest('LoginUseCase successful.');
      // triggers onComplete
      controller.close();
    } catch (e) {
      print(e);
      logger.severe('LoginUseCase unsuccessful.');
      // Trigger .onError
      controller.addError(e);
    }
    return controller.stream;
  }
}

class LoginUseCaseParams {
    final String email;
    final String password;
    LoginUseCaseParams(this.email, this.password);
}
```
##### Background UseCase
A usecase can be made to run on a separate isolate using the `BackgroundUseCase` class. 
Implementing this kind of usecase is a little different than a regular usecase due to the constraints of an isolate.
In order to create a `BackgroundUseCase`, simply extend the class and override the `buildUseCaseTask` method.
This method should return a `UseCaseTask`, which is just a function that has a void return type and takes a
`BackgroundUseCaseParameters` parameter. This method should be static and will contain all the code you wish to run
on a separate isolate. This method should communicate with the main isolate using the `port` provided in the `BackgroundUseCaseParameters`
as follows. This example is of a `BackgroundUseCase` that performs matrix multiplication.

Usecase는 `BackgroundUseCase` 클래스를 사용하여 별도의 분리에서 실행되도록 만들 수 있습니다.
이러한 종류의 사용 사례를 구현하는 것은 격리의 제약으로 인해 일반 사용 사례와 약간 다릅니다.
`BackgroundUseCase`를 생성하려면 클래스를 확장하고 `buildUseCaseTask` 메서드를 재정의하기만 하면 됩니다.
이 메소드는 `UseCaseTask`를 반환해야 합니다. 이 함수는 반환 유형이 무효이고 `BackgroundUseCaseParameters` 매개변수를 사용하는 함수입니다. 이 메서드는 정적이어야 하며 실행하려는 모든 코드를 포함합니다.
별도의 격리에. 이 메서드는 `BackgroundUseCaseParameters`에 제공된 `port`를 사용하여 기본 격리와 통신해야 합니다.
다음과 같이. 이 예제는 행렬 곱셈을 수행하는 `BackgroundUseCase`입니다.

```dart

class MatMulUseCase extends BackgroundUseCase<List<List<double>>, MatMulUseCaseParams> {

  // must be overridden
  @override
  buildUseCaseTask() {
    return matmul;  // returns the static method that contains the code to be run on an isolate
  }

  /// This method will be executed on a separate isolate. The [params] contain all the data and the sendPort 
  /// needed
  static void matmul(BackgroundUseCaseParams params) async {
    MatMulUseCaseParams matMulParams = params.params as MatMulUseCaseParams;
    List<List<double>> result = List<List<double>>.generate(
        10, (i) => List<double>.generate(10, (j) => 0));

    for (int i = 0; i < matMulParams.mat1.length; i++) {
      for (int j = 0; j < matMulParams.mat1.length; j++) {
        for (int k = 0; k < matMulParams.mat1.length; k++) {
          result[i][j] += matMulParams.mat1[i][k] * matMulParams.mat2[k][j];
        }
      }
    }
    // send the result back to the main isolate
    // this will be forwarded to the observer listneres
    params.port.send(BackgroundUseCaseMessage(data: result));

  }
}
```
Just like a regular [UseCase], a parameter class is recommended for any [BackgroundUseCase].
An example corresponding to the above example would be

 ```dart
class MatMulUseCaseParams {
  List<List<double>> mat1;
  List<List<double>> mat2;
  MatMulUseCaseParams(this.mat1, this.mat2);
  MatMulUseCaseParams.random() {
    var size = 10;
    mat1 = List<List<double>>.generate(size,
        (i) => List<double>.generate(size, (j) => i.toDouble() * size + j));

    mat2 = List<List<double>>.generate(size,
        (i) => List<double>.generate(size, (j) => i.toDouble() * size + j));
  }
}
```

#### Repository in Domain

```dart

abstract class AuthenticationRepository {
  Future<void> register(
      {@required String firstName,
      @required String lastName,
      @required String email,
      @required String password});

  /// Authenticates a user using his [username] and [password]
  Future<void> authenticate(
      {@required String email, @required String password});

  /// Returns whether the [User] is authenticated.
  Future<bool> isAuthenticated();

  /// Returns the current authenticated [User].
  Future<User> getCurrentUser();

  /// Resets the password of a [User]
  Future<void> forgotPassword(String email);

  /// Logs out the [User]
  Future<void> logout();
}

```
This repository should be implemented in **Data** layer

```dart

class DataAuthenticationRepository extends AuthenticationRepository {
  // singleton
  static DataAuthenticationRepository _instance = DataAuthenticationRepository._internal();
  DataAuthenticationRepository._internal();
  factory DataAuthenticationRepository() => _instance;

    @override
  Future<void> register(
      {@required String firstName,
      @required String lastName,
      @required String email,
      @required String password}) {
          // TODO: implement
      }

  /// Authenticates a user using his [username] and [password]
  @override
  Future<void> authenticate(
      {@required String email, @required String password}) {
          // TODO: implement
      }

  /// Returns whether the [User] is authenticated.
  @override
  Future<bool> isAuthenticated() {
      // TODO: implement
  }

  /// Returns the current authenticated [User].
  @override
  Future<User> getCurrentUser() {
      // TODO: implement
  }

  /// Resets the password of a [User]
  @override
  Future<void> forgotPassword(String email) {
      // TODO: implement
  }

  /// Logs out the [User]
  @override
  Future<void> logout() {
      // TODO: implement
  }
}
```
If the repository is platform-related, implement it in the **Device** layer.

#### Entity 
Defined in **Domain** layer.
```dart

class User {
    final String name;
    final String email;
    final String uid;
    User(this.name, this.email, this.uid);
}

```

Checkout a small example [here](./example/) and a full application built [here](https://github.com/ShadyBoukhary/Axion-Technologies-HnH).

## Authors
**[Shady Boukhary](https://github.com/ShadyBoukhary)** 
**[Rafael Monteiro](https://github.com/rafaelcmm)**
