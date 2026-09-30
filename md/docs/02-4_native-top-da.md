# 02-4. Native-TOP DA

> [!NOTE]
> **TOP DA**는 Native 지면에서 제공하는 이미지형 배너 광고 타입입니다. 360x210 또는 1200x700 규격의 이미지 소재를 단독 배너로 노출합니다.<br>이 문서를 따라 하면 TOP DA 소재를 판별해 표시하는 것까지 완료할 수 있습니다.

## 개요

본 가이드는 Native 지면에서 제공하는 TOP DA 형태의 지면을 설명합니다. TOP DA도 [02-1. Native-기본설정](02-1_native-basic.md)과 동일하게 Native 지면 위에서 연동하는 커스텀 배너 타입이며, 이미지 소재로 전달됩니다.

TOP DA 소재는 **360x210** 또는 **1200x700** 규격을 지원합니다.

<img src="resource/02-4/02-4_01_top-da-creative-example.png" alt="TOP DA 소재 예시" height="250">

> [!IMPORTANT]
> TOP DA는 P.AD SDK **v1.14.0부터** 지원됩니다. `v1.14.0+`

## 사전 준비

- [ ] [01. 시작하기](01_getting-started.md) 적용 완료 — SDK 설치 및 초기화
- [ ] [02-1. Native-기본설정](02-1_native-basic.md) 연동 흐름 숙지
- [ ] Native 지면용 **Unit ID** 발급 — 이 문서에서는 YOUR_NATIVE_UNIT_ID 로 표기합니다

## 연동 방법

### STEP 1. 광고 레이아웃 구성

Native 지면은 광고 레이아웃을 자유롭게 구성하여 노출하는 지면입니다. Activity 또는 Fragment 레이아웃 내에 아래 구조에 맞게 Native 광고 레이아웃을 구성합니다.

TOP DA는 광고 소재(이미지)만 필수이며, 나머지 항목은 선택입니다.

| 항목 | 설명 | 필수 여부 | 비고 |
|---|---|---|---|
| 광고 소재 | TOP DA를 위한 Image 광고 소재 | Mandatory | com.skplanet.skpad.benefit.presentation.media.MediaView 사용 필수<br>종횡비 유지 필수<br>사이즈: 360 x 210 또는 1200x700 (px) |
| 광고 제목 | 광고의 제목 | Optional | 최대 10자<br>생략 부호로 일정 길이 이상은 생략 가능 |
| 광고 설명 | 광고에 대한 상세 설명 | Optional | 생략 부호로 일정 길이 이상은 생략 가능<br>최대 40자 |
| 광고주 아이콘 | 광고주 아이콘 이미지 | Optional | 종횡비 유지 필수<br>이미지 사이즈 80x80 \[px\] |
| CTA 버튼 | 광고의 참여를 유도하는 버튼 | Optional | com.skplanet.skpad.benefit.presentation.media.CtaView 사용 필수<br>최대 7자<br>생략 부호로 일정 길이 이상은 생략 가능 |
| 광고 알림 문구 | Sponsored image/text view | Optional | 광고임을 나타내는 텍스트 또는 이미지<br>APP 정책에 맞게 필요하다면 추가<br>예시) "광고", "ad", "스폰서", "Sponsored" |

광고 레이아웃의 최상위 컴포넌트는 NativeAdView이며, 위 컴포넌트는 NativeAdView의 하위 컴포넌트로 구현해야 합니다.

다음은 NativeAdView의 레이아웃 예시입니다.

**res/layout/your_native_topda_view.xml**

```xml
<com.skplanet.skpad.benefit.presentation.nativead.NativeAdView
    android:id="@+id/native_ad_view"
    ...생략... >

    // MediaView는 NativeAdView의 하위 컴포넌트로 구현해야합니다.

    <com.skplanet.skpad.benefit.presentation.media.MediaView
        android:id="@+id/mediaView"
        ...생략... />
    ...생략...
</com.skplanet.skpad.benefit.presentation.nativead.NativeAdView>
```

### STEP 2. 광고 할당 요청

광고를 표시하기 위해 광고 할당을 요청합니다. 할당된 소재의 Creative Type이 TOPDA인지 확인해, 맞으면 TOP DA용 처리(populateTOPDA())로 분기합니다.

**광고 할당 요청 및 타입 분기**

```java
final NativeAdLoader loader = new NativeAdLoader("YOUR_NATIVE_UNIT_ID");
loader.loadAd(new NativeAdLoader.OnAdLoadedListener() {
    @Override
    public void onAdLoaded(@NonNull NativeAd nativeAd) {

        if (Creative.Type.TOPDA.equals(creativeType)) { // 해당 소재는 이미지 소재입니다.
            populateTOPDA(nativeAd); // TOPDA 타입
        }

    @Override
    public void onLoadError(@NonNull AdError adError) {
        // 할당된 광고가 없으면 호출됩니다.
        Log.e(TAG, "Failed to load a native ad.", adError);
    }
});
```

### STEP 3. 광고 표시

할당받은 광고 데이터를 직접 구현한 광고 레이아웃(your_native_topda_view)에 채워 넣습니다. TOP DA는 이미지 소재이므로 MediaView에 소재를 설정하고, 클릭 처리를 위해 MediaView를 clickableViews에 추가합니다.

**populateTOPDA() — TOP DA 표시**

```java
public void populateTOPDA(final NativeAd nativeAd) {

    final Ad ad = nativeAd.getAd();
    CreativeHtml creative = ((CreativeHtml) ad.getCreative());

    int layoutId = R.layout.your_native_topda_view;

    final NativeAdView view = findViewById(layoutId);
    final MediaView mediaView = view.findViewById(R.id.mediaView);

    if (mediaView != null) {
        mediaView.setCreative(ad.getCreative());
    }

    // clickableViews에 추가된 UI 컴포넌트를 클릭하면 광고 페이지로 이동합니다.
    final List<View> clickableViews = new ArrayList<>();
    clickableViews.add(mediaView);

    // 광고 콜백 이벤트를 수신할 수 있습니다.
    // view.setNativeAd 이전에 호출해야 합니다.
    // 중복하여 호출하면 2개 이상의 리스너가 등록됩니다.
    NativeAdView.OnNativeAdEventListener nativeAdEventListener = new NativeAdView.OnNativeAdEventListener() {
        @Override
        public void onImpressed(@NonNull NativeAdView view, @NonNull NativeAd nativeAd) {

        }

        @Override
        public void onClicked(@NonNull NativeAdView view, @NonNull NativeAd nativeAd) {
            // 기획에 따른 추가적인 UI 처리
        }

        @Override
        public void onRewardRequested(@NonNull NativeAdView view, @NonNull NativeAd nativeAd) {
            // 기획에 따라 리워드 로딩 이미지를 보여주는 등의 처리
        }

        @Override
        public void onRewarded(@NonNull NativeAdView view, @NonNull NativeAd nativeAd, @Nullable RewardResult rewardResult) {
            // 리워드 적립의 결과 (RewardResult) SUCCESS, ALREADY_PARTICIPATED, MISSING_REWARD 등에 따라서 적절한 유저 커뮤니케이션 처리
        }

        @Override
        public void onParticipated(@NonNull NativeAdView view, @NonNull NativeAd nativeAd) {
            ctaPresenter.bind(nativeAd);
            // 기획에 따른 추가적인 UI 처리
        }
    };
    // 중복하여 addOnNativeAdEventListener를 호출하면 2개 이상의 리스너가 등록됩니다.
    // 하나의 리스너만 등록하기 위해서는 아래와 같이 리스너를 해제하거나, addOnNativeAdEventListener를 한번만 호출하기 위한 로직을 추가해야 합니다.
    view.removeOnNativeAdEventListener(nativeAdEventListener);

    view.addOnNativeAdEventListener(nativeAdEventListener);

    view.setClickableViews(clickableViews);
    view.setMediaView(mediaView);
    view.setNativeAd(nativeAd);
}
```

> **다음 단계**
>
> - [02-1. Native-기본설정](02-1_native-basic.md) — Native 지면 기본 연동
> - [02-2. Native-고급설정](02-2_native-advanced.md) — CTA 커스터마이징, 비디오·체류형 광고 등
> - [02-3. Native-HTML Banner](02-3_native-html-banner.md) — HTML 배너 타입 연동
> - [02-5. Native-CarouselView](02-5_native-carousel-view.md) — 좌우 스와이프 캐러셀 타입 연동
