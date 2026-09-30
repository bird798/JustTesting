# 04-1. Interstitial 기본설정

> [!NOTE]
> **Interstitial**은 SDK가 제공하는 UI로 앱 화면을 완전히 덮으면서 노출되는 전면 광고 지면입니다.<br>이 문서를 따라 하면 다이얼로그 형태의 Interstitial 지면을 띄우는 것까지 완료할 수 있습니다.

## 개요

Interstitial 지면은 Planet AD SDK에서 제공하는 UI를 사용해 앱을 완전히 덮으면서 노출됩니다.<br>제공하는 UI로 쉽게 연동할 수 있으며, 광고 지면이 앱을 덮고 있어서 앱 UI와의 조합을 고려하지 않고도 노출하기 용이합니다.

Interstitial 지면은 **다이얼로그 / 바텀시트 / 풀스크린** 세 가지 UI를 제공합니다. 아래는 각 UI의 예시 화면입니다.

<kbd><img src="resource/04-1/04-1_01_interstitial-ui-types.png" alt="다이얼로그·바텀시트·풀스크린 UI 비교" width="500"></kbd>

## 사전 준비

시작하기 전에 아래 항목이 준비되어 있어야 합니다.

- [ ] [01. 시작하기](01_getting-started.md) 적용 완료 — SDK 설치 및 초기화
- [ ] Interstitial 지면용 **Unit ID** 발급 — 이 문서에서는 YOUR_INTERSTITIAL_UNIT_ID 로 표기합니다

## 연동 방법

### STEP 1. UI 타입 선택

Planet AD SDK의 Interstitial 지면은 다이얼로그, 바텀시트, 풀스크린(`v1.12.0+`)의 UI를 제공합니다.<br>각각 아래 타입으로 설정할 수 있습니다.

| UI 종류 | Type 값 |
|---|---|
| 다이얼로그 | InterstitialAdHandler.Type.Dialog |
| 바텀시트 | InterstitialAdHandler.Type.BottomSheet |
| 풀스크린 | InterstitialAdHandler.Type.FullScreen |

### STEP 2. Interstitial 지면 표시

InterstitialAdHandlerFactory로 핸들러를 만들고 show()를 호출하면 지면이 표시됩니다.<br>다음은 **다이얼로그 형태**의 Interstitial 지면을 표시하는 예시입니다.

**Interstitial 지면 표시**

```java
final InterstitialAdHandler interstitialAdHandler = new InterstitialAdHandlerFactory().create("YOUR_INTERSTITIAL_UNIT_ID", InterstitialAdHandler.Type.Dialog);
interstitialAdHandler.show(context);
```

> [!TIP]
> create()의 두 번째 인자를 Type.BottomSheet 또는 Type.FullScreen으로 바꾸면 다른 UI로 표시됩니다. 각 UI의 세부 기능과 커스터마이징은 아래 고급설정 문서를 참고하세요.

> **다음 단계**
>
> - [04-2. Interstitial 고급설정 - Dialog, BottomSheet](04-2_interstitial-advanced-dialog-bottomsheet.md) — 광고 개수·콜백·지면 UI 설정
> - [04-3. Interstitial 고급설정 - Full Screen Default 타입](04-3_interstitial-advanced-fullscreen-default.md) — 풀스크린 기본 타입
> - [04-4. Interstitial 고급설정 - Fullscreen No Edge 타입](04-4_interstitial-advanced-fullscreen-no-edge.md) — 풀스크린 No Edge 타입
> - [04-5. Interstitial 디자인 커스터마이징](04-5_interstitial-customizing.md) — 타이틀·색상·아이콘 변경
