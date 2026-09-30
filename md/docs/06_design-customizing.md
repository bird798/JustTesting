# 06. 디자인 커스터마이징

> [!NOTE]
> **Planet AD Benefit SDK가 제공하는 광고 지면의 색상·아이콘·CTA를 앱 디자인에 맞게 바꾸는 방법**을 안내합니다. 이 문서를 따라 하면 application theme에 attribute를 정의해 리워드 아이콘, PrimaryColor, CTA 버튼 등을 커스터마이징할 수 있습니다.

## 개요

Planet AD Benefit SDK는 앱에서 사용 중인 **application theme**에 SDK가 제공하는 attribute를 정의하는 방식으로 UI를 커스터마이징합니다. 별도 코드 없이 테마 값만 지정하면 CTA, 리워드 아이콘, 색상 등이 앱 디자인에 맞게 바뀝니다.

이 문서에서는 아래 항목을 다룹니다.

- **테마 적용** — 커스터마이징 가능한 전체 attribute 목록
- **PrimaryColor 변경** — Dialog·Bottom sheet 등의 주요 색상
- **리워드 아이콘** 변경
- **CTA 변경** — 광고 CTA 버튼의 색상·아이콘·텍스트
- **Display Point** — 실제 지급 포인트와 표시 포인트가 다른 경우의 처리

## 테마 적용

Planet AD Benefit에서 제공하는 광고 지면의 CTA 테마를 변경할 수 있습니다. 이 단계는 **선택사항**이지만, 원하는 색상·아이콘을 적용하는 단계이므로 적용하는 것을 권장합니다.

앱에서 사용 중인 application theme에 Planet AD에서 제공하는 attribute를 정의하면 간편하게 Planet AD Benefit SDK의 CTA UI를 커스터마이징할 수 있습니다. 사용 가능한 attribute는 아래와 같습니다.

| attribute (type) | child attribute (type) | 커스터마이징 되는 UI |
|---|---|---|
| skpadRewardIcon (reference) | N/A | \[All\] cta view 리워드 아이콘<br>\[Feed\] profile banner 리워드 아이콘<br>\[Feed\] pop opt-in 버튼 아이콘 |
| skpadCtaViewStyle (reference) | skpadCtaBackground - (reference) Cta 배경 설정<br>skpadCtaParticipatedIcon - (reference) 광고 참여 후 아이콘 설정<br>skpadCtaTextColor - (color\|reference) Cta 텍스트 색상<br>skpadCtaTextSize - (dimension) Cta 텍스트 크기 | \[All\] cta view |
| skpadColorPrimaryDark (reference\|color)<br>skpadColorPrimary (reference\|color)<br>skpadColorPrimaryLight (reference\|color)<br>skpadColorPrimaryLighter (reference\|color)<br>skpadColorPrimaryLightest (reference\|color) | N/A | \[Feed\] Tab UI, Filter UI, Pop FAB 배경색 등<br>\[Interstitial\] Feed 진입 경로 텍스트 색상<br>\[Pop\] Pop 아이콘 배경색, Toolbar UI, 다른 앱 위에 그리기 권한 다이얼로그 UI 등 |

## PrimaryColor 변경

Planet AD SDK에서 제공하는 UI 중 Dialog의 버튼 색상, Bottom sheet UI의 확인 버튼을 포함한 일부 UI의 색상을 아래와 같이 Theme에서 설정할 수 있습니다.

**styles.xml (application theme)**

```xml
<!-- Base application theme. -->
<style name="YourAppTheme" parent="Theme.AppCompat.DayNight.DarkActionBar">
    <item name="skpadColorPrimaryDark">@color/samplePrimaryDark</item>
    <item name="skpadColorPrimary">@color/samplePrimary</item>
    <item name="skpadColorPrimaryLight">@color/samplePrimaryLight</item>
    <item name="skpadColorPrimaryLighter">@color/samplePrimaryLighter</item>
    <item name="skpadColorPrimaryLightest">@color/samplePrimaryLightest</item>
</style>
```

## 리워드 아이콘

리워드를 표시하는 아이콘 추가를 위해 application theme에 skpadRewardIcon을 추가합니다.

**styles.xml (application theme)**

```xml
<!-- Base application theme. -->
<style name="YourAppTheme" parent="Theme.AppCompat.DayNight.DarkActionBar">
    <!-- 생략 -->
    <item name="skpadRewardIcon">@drawable/your_reward_icon</item>
</style>
```

## CTA 변경

광고의 CTA의 색상·아이콘 등을 변경하기 위해 application theme에 skpadCtaViewStyle을 추가합니다.

**styles.xml (application theme)**

```xml
<!-- Base application theme. -->
<style name="YourAppTheme" parent="Theme.AppCompat.DayNight.DarkActionBar">
    <!-- 생략 -->
    <item name="skpadCtaViewStyle">@style/YourCtaViewStyle</item>
</style>

<style name="YourCtaViewStyle">
    <item name="skpadCtaBackground">@drawable/your_background</item>
    <item name="skpadCtaParticipatedIcon">@drawable/your_participated_icon</item>
    <item name="skpadCtaRewardIcon">@drawable/your_reward_icon</item>
    <item name="skpadCtaTextColor">@color/your_text_color</item>
    <item name="skpadCtaTextSize">14sp</item>
</style>
```

CTA 배경(skpadCtaBackground)에 지정할 drawable은 아래와 같이 상태별로 정의할 수 있습니다.

<details>
<summary>Cta 배경 예시 (your_background.xml)</summary>

**your_background.xml**

```xml
<!-- your_background.xml -->
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:state_pressed="true">
        <shape>
            <solid android:color="@color/colorPrimaryDark"/>
            <corners android:radius="4dp"/>
        </shape>
    </item>
    <item android:state_enabled="false">
        <shape>
            <solid android:color="@color/gray"/>
            <corners android:radius="4dp"/>
        </shape>
    </item>
    <item>
        <shape>
            <solid android:color="@color/colorPrimary"/>
            <corners android:radius="4dp"/>
        </shape>
    </item>
</selector>
```

</details>

## Display Point

실제 지급되는 리워드 포인트(Reward Point)와 사용자에게 **표시**하는 포인트(Display Reward)가 다른 경우가 있습니다. 예를 들어 실제로는 1포인트를 지급하지만, 사용자에게는 "금 8"처럼 다른 값과 이름으로 표시합니다. 이럴 때는 아래 API로 표시용 값을 가져와 노출합니다.

| 구분 | 동일한 경우 | 다른 경우 | 예시 |
|---|---|---|---|
| **Reward Point** | NativeAd.getAd().getReward() | NativeAd.getAd().getDisplayReward() | Reward : 1<br>Display Reward : 8 |
| **Reward Point Name** |  | NativeAd.getAd().getDisplayRewardName() | Display Reward Name : "금" |

CTA 버튼 혹은 적립 불릿을 통해 Point를 표시할 때는 아래와 같이 getDisplayReward()를 사용합니다.

**CTA 버튼 혹은 적립 불릿을 통해 Point 표시 시**

```java
int displayReward = nativeAd.getAd().getDisplayReward();

ctaView.showRewardImage(CtaView.ImageType.Default);
ctaView.setRewardText(String.format(Locale.US, "+%,d", displayReward));
ctaView.setCallToActionText(callToAction);
```

사용자에게 적립 성공을 고지할 때도 표시용 포인트·이름을 사용합니다.

**사용자에게 적립 성공 고지 시**

```java
@Override
public void onRewarded(@NonNull NativeAdView view, @NonNull NativeAd nativeAd, @Nullable RewardResult nativeAdRewardResult) {
    if (nativeAdRewardResult == RewardResult.SUCCESS) {
        int rewardPoint = nativeAd.getAd().getDisplayReward();
        String rewardPointName = nativeAd.getAd().getDisplayRewardName();

        Toast.makeText(view.getContext(), rewardPoint + rewardPointName + "가 적립되었습니다.", Toast.LENGTH_SHORT).show();
    }
}
```
