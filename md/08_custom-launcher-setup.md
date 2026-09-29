# 08. 커스텀런처설정

> [!NOTE]
> **Custom Launcher를 사용해 광고 클릭 시 랜딩되는 In-App Browser를 매체사가 직접 구현·커스터마이징하는 방법**을 안내합니다. 이 문서를 따라 하면 SKPAdBrowser가 제공하는 Fragment로 In-App Browser를 구성하고, 매체사가 지정한 Class에서 랜딩 페이지 로드 및 이벤트를 처리할 수 있습니다.

## 개요

Custom Launcher를 사용하면 광고를 클릭했을 때 랜딩되는 In-App Browser를 매체사가 직접 Customize할 수 있습니다. 예를 들어 광고 랜딩 페이지 로드 등을 매체사가 지정하는 Class에서 구현할 수 있습니다.

> [!IMPORTANT]
> **적용 대상**
>
> - 현재 **Custom Launcher를 사용하지 않을 경우**, 이 가이드는 아무 영향이 없습니다.
> - Custom Launcher를 **사용할 예정이거나, 이미 사용하고 있을 경우**, 아래 항목을 **필수**로 적용해야 합니다.

## 구현 시 주의사항

> [!WARNING]
> - **SKPAdBrowser에서 제공하는 Fragment를 사용**하여 In-App Browser를 구현해야 합니다. 사용하지 않을 경우, 일부 광고(액션형 광고, 체류 리워드 광고)가 제대로 동작하지 않을 수 있습니다.
> - Launcher에서 제공하는 <strong>LandingInfo의 URL을 임의로 변경해서 사용하면 안 됩니다.</strong> 이 경우 웹 페이지가 제대로 로드되지 않을 수 있습니다.

## CustomBrowserActivity 구현

In-App Browser 화면 역할을 하는 `CustomBrowserActivity`를 구현합니다. SKPAdBrowser로부터 WebView를 가진 Fragment를 받아와 컨테이너에 붙이고, Browser 이벤트

를 처리합니다.

**CustomBrowserActivity.java**

```java
public class CustomBrowserActivity extends AppCompatActivity {
    public static final String KEY_URL = "com.sample.KEY_URL";
    private SKPAdBrowserFragment fragment;

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_custom_browser);

        // URL을 KEY로 하여 WebView를 가지고있는 Fragment를 받아와 사용합니다.
        Intent intent = getIntent();
        fragment = SKPAdBrowser.getInstance(this).getFragment(intent.getStringExtra(KEY_URL));
        getSupportFragmentManager().beginTransaction().replace(R.id.browserContainer, fragment).commit();
        final SKPAdWebView webView = fragment.getWebView();

        // Browser의 이벤트를 받을 수 있습니다. DeepLink가 열렸을 경우, Browser를 닫아주어야 빈 페이지가 보여지는 현상을 방지할 수 있습니다.
        SKPAdBrowser.getInstance(this).setOnBrowserEventListener(new SKPAdBrowser.OnBrowserEventListener() {

        });
    }

    // Optional - BackButton을 눌렀을때 뒤로가기 기능
    @Override
    public void onBackPressed() {
        final SKPAdWebView webView = fragment.getWebView();
        if (webView != null && webView.canGoBack()) {
            webView.goBack();
        } else {
            super.onBackPressed();
        }
    }
}
```

Fragment를 붙일 컨테이너 레이아웃은 아래와 같이 정의합니다.

**activity_custom_browser.xml**

```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <FrameLayout
        android:id="@+id/browserContainer"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
</FrameLayout>
```

## Launcher 구현

광고 클릭 시 SDK가 호출하는 `Launcher`를 구현하여, 위에서 만든 `CustomBrowserActivity`를 실행합니다.

**MyLauncher.java**

```java
public class MyLauncher implements Launcher {

    @Override
    public void launch(@NonNull Context context, @NonNull LaunchInfo launchInfo) {
        launch(context, launchInfo, null);
    }

    @Override
    public void launch(@NonNull final Context context, @NonNull final LaunchInfo launchInfo, @Nullable final LauncherEventListener listener) {
        launch(context, launchInfo, listener, null);
    }

    @Override
    public void launch(@NonNull final Context context, @NonNull final LaunchInfo launchInfo, @Nullable final LauncherEventListener listener, @Nullable List<Class<? extends SKPAdJavascriptInterface>> javascriptInterfaces) {

        // Custom Browser 실행
        final Intent intent = new Intent(context, CustomBrowserActivity.class);
        intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
        intent.putExtra(CustomBrowserActivity.KEY_URL, launchInfo.getUri().toString()); // URI는 변경 하면 안 됨
        context.startActivity(intent);
    }
}
```

## Launcher 등록

`SKPAdBenefit.init` 호출 이후에 생성한 Launcher를 세팅합니다.

**Launcher 등록**

```java
SKPAdBenefit.setLauncher(new MyLauncher());
```

## Article의 sourceUrl 사용법

컨텐츠의 경우 URL scheme에 따라 랜딩 방식을 다르게 처리하고 싶다면(예: 앱 안에서 브라우저 오픈 없이 다른 화면으로 이동되는 컨텐츠), 아래와 같이 `NativeArticle` 객체의 `sourceUrl`을 가져와 분기 처리를 할 수 있습니다.

**Article sourceUrl 분기 처리**

```java
public class MyLauncher implements Launcher {
    @Override
    public void launch(@NonNull final Context context, @NonNull final LaunchInfo launchInfo, @Nullable final LauncherEventListener listener, @Nullable List<Class<? extends SKPAdJavascriptInterface>> javascriptInterfaces) {
        if (launchInfo.getArticle() != null) {
            String sourceUrl = launchInfo.getArticle().getSourceUrl();
        }
    }
}
```

## 광고 / 컨텐츠 사전 판단

Custom Launcher 사용 시 랜딩 대상이 광고인지 컨텐츠인지 미리 판단하고 싶을 경우, 아래와 같이 `LaunchInfo`의 `getAd()`·`getArticle()` 반환값으로 확인할 수 있습니다.

**광고 / 컨텐츠 사전 판단**

```java
public class MyLauncher implements Launcher {
    ...
    @Override
    public void launch(@NonNull final Context context, @NonNull final LaunchInfo launchInfo, @Nullable final LauncherEventListener listener, @Nullable List<Class<? extends SKPAdJavascriptInterface>> javascriptInterfaces) {

        // 광고 또는 컨텐츠인지 미리 판단하고 싶을 경우, 다음을 이용하여 확인
        if (launchInfo.getAd() != null) {
            // 광고
        } else if (launchInfo.getArticle() != null) {
            // 컨텐츠
        }

        ...// Custom Browser 실행
    }
}
```
