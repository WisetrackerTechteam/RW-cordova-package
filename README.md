### 1. 코르도바 플러그인 설치 (AOS/IOS 공통 설정)

#### 1.1 Plugman 설치 (Plugman 미설치 경우)

#### a) Plugman 설치

```node
$ npm install -g plugman@1.5
```

#### b) Plugman 설치 버전 확인 
-> plugman version 2.0 이상인 경우에 원격지의 라이브러리가 정상 설치되지 못하는 현상이 있어, **plugman 1.5 버전 사용을 권장**합니다.


```node
// plugman 버전 정보 확인
$ plugman –v
```

#### 1.2 플러그인 다운로드
-> 터미널에서 해당 프로젝트 경로로 이동한 후 아래의 명령어를 통해 코르도바 플러그인을 설치합니다.

#### a) Android

```node
$ plugman install -d --platform android --project ./platforms/android --plugin https://github.com/WisetrackerTechteam/RW-cordova-package.git
```

#### b) IOS
```node
$ plugman install -d --platform ios --project ./platforms/ios --plugin https://github.com/WisetrackerTechteam/RW-cordova-package.git
```

#### 1.3 플러그인 설치 확인

#### a) Android
-> 코르도바 프로젝트에 플러그인이 정상 다운로드되면 다음과 같이 플러그인 파일이 추가된 것을 확인할 수 있습니다.

![](http://www.wisetracker.co.kr/wp-content/uploads/2019/04/aos-cordova-addfile.jpg)

#### b) IOS
-> 코르도바 프로젝트에 플러그인이 정상 다운로드되면 X-code를 통해 다음과 같이 플러그인 파일이 추가된 것을 확인할 수 있습니다.

![](http://www.wisetracker.co.kr/wp-content/uploads/2020/03/RW-cordova-project.png)

### 2. Android 코르도바 플러그인 설정

#### 2.1 AndroidManifest.xml 설정

#### a) Http 통신 허용 설정
-> 프로젝트의 **Target API 28 이상**일 경우 Http 통신 허용을 설정해 주세요.

```xml
<!-- AndroidManifest.xml -->
<application
	android:icon="@mipmap/ic_launcher"
	android:label="@string/app_name"
	android:networkSecurityConfig="@xml/network_security_config"
	android:theme="@style/AppTheme">
```

```xml
<!-- app/res/xml/network_security_config.xml -->
<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
    <domain-config cleartextTrafficPermitted="true">
        <domain includeSubdomains="true">collector.naver.wisetracker.co.kr</domain>
    </domain-config>
</network-security-config>
```

#### b) 딥링크 설정
-> 딥링크로 진입할 android:scheme="YOUR_SCHEME" 스키마와 android:host="YOUR_HOST" 호스트를 설정해 주세요.

```xml
<!-- 예시는 wisetracker://wisetracker.co.kr 링크로 진입시 딥링크 분석이 가능하며, 사용될 값을 직접 수정해 주세요. -->
<activity android:name="kr.co.wisetracker.tracker.DeepLinkActivity" 
          android:launchMode="singleTop" >
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
      	<!-- 딥링크로 진입될 스키마와 호스트 정보 입력 -->
        <data android:host="wisetracker.co.kr"
              android:scheme="wisetracker" />
    </intent-filter>
</activity>
```

#### c) 인스톨 레퍼러 활성화 여부 (필요시 설정)

```xml
<!-- true 변경시 Wisetracker 통한 인스톨 레퍼러 미수신 -->
<meta-data 
	android:name="disableDotReceiver" 
	android:value="false" />
```

#### 2.2 strings.xml 설정

#### a) dotAuthorizationKey 설정
-> 안드로이드 코르도바 프로젝트의 app/res/values/strings.xml 파일에 제공받은 App Analytics Key 정보를 추가

```xml
<!-- 예시는 샘플 코드이며, 관리자 페이지에서 직접 발급 받은 값을 적용해 주세요  -->
<string-array name="dotAuthorizationKey">
    <item name="usdMode">1</item> // 1. DOT.DOX 2. DOT
    <item name="domain">http://collector.naver.wisetracker.co.kr</item> // DOT END POINT
    <item name="domain_x">http://collector.naver.wisetracker.co.kr</item> // DOX END POINT
    <item name="serviceNumber">103</item>
    <item name="expireDate">14</item>
    <item name="isDebug">false</item>
    <item name="isInstallRetention">true</item>
    <item name="isFingerPrint">true</item>
    <item name="accessToken">access_token_string</item>
</string-array>
```

#### b) customKeyList 설정 (필요시 설정)
**'#'** 구분자 기준으로 **왼쪽**은 기본 사용되고 있는 키 값 **오른쪽**은 변경하고자 하는 키 값을 적용해주세요.

```xml
<!-- 예시는 디폴트 advtId 키 값을 advt_id 값으로 변경하는 설정입니다. -->
<string-array name="customKeyList">
  <item name="custom_key_value1">advtId#advt_id</item>
</string-array>
```

### 3. IOS 코르도바 플러그인 설정

XCode 프로젝트의 info.plist 파일에 제공받은 App Analytics Key 정보를 추가합니다
info.plist 파일을 open할때 **list로 보기** 가 아니라 **source로 보기**를 선탁하신뒤, 제공받으신 Key를 **Ctrl+V** 하시면 됩니다
제공받은 Key값은 아래의 예시와 같이 xml 형태를 가지고 있는 데이터 입니다

```xml
    <key>dotAuthorizationKey</key>
    <dict>
        <key>accessToken</key>
        <string></string>
        <key>domain</key>
        <string>http://rw-collector-env.ap-northeast-2.elasticbeanstalk.com</string>
        <key>expireDate</key>
        <string>14</string>
        <key>isDebug</key>
        <string>true</string>
        <key>isFingerPrint</key>
        <string>true</string>
        <key>isInstallRetention</key>
        <string>true</string>
        <key>serviceNumber</key>
        <string>102</string>
        <key>useMode</key>
        <integer>1</integer>
    </dict>
```

위의 Key 중에 isDebug는 collector 로 전송되는 분석 데이터를 Xcode 에서 확인할 수 있게 해주는 boolean 변수로 사용되고 있습니다
SDK를 적용해야 하는 앱 개발자는 이 값을 true로 설정하고, SDK의 기본 적용 및 추가 분석 코드 적용 과정에서 사용할 수 있습니다
( 예를 들면 이 값을 true로 적용하여, 분석 대상이 되는 앱에 적용하여 build를 하면 Android 의 경우 logcat에서, iOS 의 경우 debug console에서 collector로 전송되는 plain text 형태의 로그 확인이 가능합니다. )
분석 코드 적용이 완료되고, 스토어 배포시에는 해당 필드를 false로 변경하여 배포하는 것을 권장합니다.


http통신을 허용하기 위해 NSAppTransportSecurity 를 아래와 같이 추가합니다

```xml
	<key>NSAppTransportSecurity</key>
	<dict>
		<key>NSAllowsArbitraryLoads</key>
		<true/>
	</dict>
```
### 4. 플러그인 초기화
코르도바 프로젝트에서 앱 시작시 가장 먼저 로드되는 .js 파일에 플러그인 초기화와 Library inject 하는 코드를 추가합니다.

**샘플 코드는 index.js 기준으로 작성하였습니다. 적용 프로젝트에 맞게 적용해 주세요.**

```javascript

var app = {

    // Application Constructor
    initialize: function() {
        document.addEventListener('deviceready', this.onDeviceReady.bind(this), false);
    },

    // deviceready Event Handler
    //
    // Bind any cordova events here. Common events are:
    // 'pause', 'resume', etc.
    onDeviceReady: function() {
        this.receivedEvent('deviceready');
    },

    // Update DOM on a Received Event
    receivedEvent: function(id) {
        var parentElement = document.getElementById(id);
        var listeningElement = parentElement.querySelector('.listening');
        var receivedElement = parentElement.querySelector('.received');

        listeningElement.setAttribute('style', 'display:none;');
        receivedElement.setAttribute('style', 'display:block;');

        /*
        ** Wisetracker SDK 초기 설정 코드 적용 예시 
        */
	// SDK 초기화를 위해 최초 1회만 호출
        cordova.plugins.DotCordovaBridge.init();
	// 이후 로드되는 모든 페이지에 injecting 함수 호출
        cordova.plugins.DotCordovaBridge.inject();
            
        }
        
};

app.initialize();
```
