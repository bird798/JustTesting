# 07. Web Android SDK 연동 가이드

> [!NOTE]
> **Web Android SDK**는 Android 앱 안의 WebView에서 광고를 표시하기 위한 연동 방식입니다.<br>이 문서를 1단계부터 8단계까지 순서대로 따라 하면, WebView에 Planet AD Benefit Web SDK를 연동하고 앱을 빌드하는 것까지 완료할 수 있습니다.

## 개요

이 가이드는 Android 앱 내의 WebView에서 광고를 표시하기 위한 Planet AD Benefit Web Android용 SDK 연동 방법을 안내합니다.

## 사전 준비

시작하기 전에 아래 항목이 준비되어 있어야 합니다. ID는 STEP 1에서 발급받습니다.

- [ ] 앱의 고유 식별자 **App ID** 발급 — 이 문서에서는 YOUR_APP_ID 로 표기합니다
- [ ] 광고 지면의 고유 식별자 **Unit ID** 발급

## 연동 방법

### STEP 1. 연동용 ID 발급받기

Planet AD SDK를 연동하려면 반드시 앱의 고유 식별자인 **App ID**와 광고 지면의 고유 식별자 **Unit ID**가 필요합니다. 연동용 ID를 발급받으려면 SKP 광고 담당자에게 연락하세요.

| ID 유형 | 설명 |
|---|---|
| App ID | Planet AD SDK를 연동하는 **앱 별로 부여하는 고유 식별자**입니다. |
| Unit ID | 앱 내에 **광고 지면별로 부여하는 고유 식별자**입니다. |

### STEP 2. SDK 설치하기

Planet AD SDK를 설치하려면 다음 절차를 따르세요.

<strong>1. 프로젝트 레벨의 build.gradle 파일에 Planet AD SDK 저장소를 추가하세요.</strong>

**프로젝트 레벨의 build.gradle**

```groovy
allprojects {
    repositories {
        maven { url "https://asia-northeast3-maven.pkg.dev/planetad-379102/planetad" } // Planet AD 저장소
    }
}
```

<strong>2. 모듈 레벨의 build.gradle 파일에 Planet AD SDK 라이브러리를 추가하세요.</strong>

**모듈 레벨의 build.gradle**

```groovy
dependencies {
    implementation ("com.skplanet.sdk.ad:skpad-benefit:1.17.0") { changing = true } // SKP AD Benefit SDK 라이브러리
}
```

<strong>3. 모듈 레벨의 build.gradle 파일에 compileSdkVersion과 targetSdkVersion을 31로 업데이트하세요.</strong>

**모듈 레벨의 build.gradle**

```groovy
android {
    compileSdkVersion 31
    defaultConfig {
        targetSdkVersion 31
    }
}
```

### STEP 3. App ID 설정하기

AndroidManifest.xml 파일에 아래와 같이 &lt;meta-data&gt; 요소를 추가하고, app-pub-{YOUR_APP_ID}의 {YOUR_APP_ID}를 SKP 광고 담당자로부터 발급받은 App ID로 교체하세요.

> [!IMPORTANT]
> 발급받은 App ID가 123456789123 이라면 app-pub-123456789123 가 되어야 합니다.

**AndroidManifest.xml**

```xml
<manifest>
    <application>
        <!-- Planet AD SDK App id -->
        <meta-data
            android:name="com.skplanet.APP_KEY"
            android:value="app-pub-000000000000" />
    </application>
</manifest>
```

### STEP 4. SDK 초기화하기

Application의 onCreate()에서 다음 코드를 추가하여 Planet AD SDK를 초기화하세요.

**Application.onCreate()**

```java
public class App extends Application {
    @Override
    public void onCreate() {
        super.onCreate();

        // SKPAdBenefit 초기화
        final SKPAdBenefitConfig skpAdBenefitConfig = new SKPAdBenefitConfig.Builder(context)
            .build();
        SKPAdBenefit.init(this, skpAdBenefitConfig);
    }
}
```

### STEP 5. 사용자 프로필 등록하기

광고 할당을 요청하려면 사용자 프로필을 등록해야 합니다. 사용자 프로필을 구성하는 항목은 아래 표를 참고하세요.

| 사용자 프로필 | 설명 |
|---|---|
| userId | 매체사 앱에서 사용하는 사용자 식별자입니다. 서비스 도중 변하지 않는 고정 값이어야 하며, 광고 할당을 위해 **필수로 전달**해야 합니다.<br>앱을 삭제 후 재설치하여 사용자의 ID 값이 변경되거나 다른 사유로 고정 ID를 사용하지 못하는 경우 SKP 광고 담당자에게 문의하세요. |
| gender | 사용자의 성별입니다. 사용자 맞춤형 광고를 제공하는 데 활용됩니다.<br>남성: UserProfile.Gender.MALE<br>여성: UserProfile.Gender.FEMALE |
| birthYear | 사용자의 출생연도입니다. 사용자 맞춤형 광고를 제공하는 데 활용됩니다. |

사용자가 로그인하는 시점에 다음 코드를 추가하여 SDK에 사용자 프로필을 등록하세요.

> [!WARNING]
> 원활한 서비스 운영을 위해 사용자 프로필 등록은 반드시 호출해야 합니다. SKPAdBenefit.setUserProfile() 메소드를 호출하지 않으면 광고가 제공되지 않습니다.<br>사용자의 로그인 정보를 웹 페이지에서는 알 수 있지만 앱에서는 알 수 없는 경우, 웹 페이지에서 사용자 프로필을 설정하는 기능도 지원합니다.

**로그인 시 — 사용자 프로필 등록**

```java
// 사용자 정보를 등록하는 코드입니다.
final UserProfile.Builder builder = new UserProfile.Builder(SKPAdBenefit.getUserProfile());
final UserProfile userProfile = builder
    .userId("USER_ID") // 사용자 식별자값
    .gender(UserProfile.Gender.MALE) // 사용자의 성별
    .birthYear(2000) // 출생연도
    .build();
SKPAdBenefit.setUserProfile(userProfile);
```

사용자가 앱에서 로그아웃하는 시점에는 다음과 같이 사용자 프로필 정보를 삭제하세요.

**로그아웃 시 — 사용자 프로필 삭제**

```java
// SDK에 등록한 사용자 프로필을 삭제하는 코드입니다.
SKPAdBenefit.setUserProfile(null);
```

### STEP 6. 광고를 표시할 WebView 설정하기

웹 SDK와 소통할 수 있도록, 광고를 표시하려는 WebView에 다음 코드를 추가하세요.

**광고 표시용 WebView 설정**

```java
WebView webview = (WebView) findViewById(R.id.webView);

final SKPAdBenefitJavascriptInterface javascriptInterface = new SKPAdBenefitJavascriptInterface(webView);
webView.getSettings().setJavaScriptEnabled(true); // JS를 사용하여 광고를 로드하기 때문에 필수임

// 동영상 광고를 위한 추가
webView.getSettings().setDomStorageEnabled(true); // VAST Video 광고 로드를 위해 필요
webView.getSettings().setMediaPlaybackRequiresUserGesture(false); // 동영상 광고 자동 재생을 위해 필요
webView.setWebChromeClient(new WebChromeClient()); // WebView에서 HTML5 video의 인라인 재생 및 fullscreen 처리를 위해 WebChromeClient 설정이 필요.
    // 앱에서 이미 커스텀 WebChromeClient를 사용 중인 경우, 별도로 교체할 필요는 없으나 video 관련 처리를 막고 있지 않은지 확인이 필요.
    // (onShowCustomView() / onHideCustomView()에서 video fullscreen 처리를 차단하지 않도록 구현 필요. 기본 동작 유지를 위해 super 호출을 권장합니다.)

if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
    // 롤리팝부터 Mixed Content 에러 막기 위함
    webView.getSettings().setMixedContentMode(WebSettings.MIXED_CONTENT_ALWAYS_ALLOW);
}
webView.addJavascriptInterface(javascriptInterface, SKPAdBenefitJavascriptInterface.INTERFACE_NAME);

javascriptInterface.init(webView); // v1.15.3부터 추가
```

### STEP 7. Benefit JS SDK가 삽입된 웹 페이지 로드하기

Benefit JS SDK가 삽입된 웹 페이지를 열면, Android 코드에서 설정한 UserProfile 정보 등을 Web SDK가 자동으로 받아 광고를 로드합니다.

**웹 페이지 로드**

```java
webView.loadUrl(MY_WEB_PAGE);
```

### STEP 8. 앱 빌드하기

Planet AD SDK를 사용하기 위한 모든 설정이 완료되었습니다. 앱을 빌드하고 정상적으로 실행되는지 확인하세요.
