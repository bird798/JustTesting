# 10. 광고 노출/클릭/참여와 관련한 콜백 변화

> [!NOTE]
> **광고의 노출 → 클릭 → 리워드 → 참여로 이어지는 5종 콜백**이 각각 언제 호출되고, 어떻게 대응해야 하는지 정리한 문서입니다. 이 문서를 읽으면 각 콜백의 호출 시점·중복 호출 여부·권장 대응을 이해하고, 특히 onRewarded의 상태값(result)별 처리 방법을 파악할 수 있습니다.

## 개요

광고에 대한 콜백은 아래 순서로 호출됩니다. AOS와 iOS는 콜백 **이름만 다를 뿐 동작은 동일**하므로, 아래에서는 AOS 이름을 기준으로 설명합니다. (onRewardRequested는 조건에 따라 생략될 수 있어 괄호로 표기했습니다.)

- **AOS**: onImpressed → onClicked → (onRewardRequested) → onRewarded → onParticipated
- **iOS**: didImpressAd → didClickAd → willRequestRewardForAd → didRewardForAd → didParticipateAd

아래 표들은 각 콜백의 정의, 중복 호출 여부, 주의사항, 권장 대응을 순서대로 설명합니다.

## onImpressed

| 항목 | 설명 |
|---|---|
| **정의** | 광고가 유저에게 노출되었음 (= NativeAdView의 광고 레이아웃의 50%가 유저에게 보여짐) |
| **중복 호출 여부** | 한 광고당 한 번만 호출됨 |
| **추천 대응** | X |

## onClicked

| 항목 | 설명 |
|---|---|
| **정의** | 광고가 유저에 의해 클릭됨 (= clickableViews로 지정한 component들이 클릭됨)<br>일반/비디오 광고 모두 onClickEvent / shouldClickAd (AOS/iOS)를 통한 click 이벤트 intercept가 가능함<br>이때 일반 광고는 랜딩 페이지로 이동을 시도하며, 비디오 광고는 서버에서 비디오의 Asset을 받아와 재생을 시작함 |
| **중복 호출 여부** | 한 광고당 중복해서 호출될 수 있음 (유저가 광고를 클릭하여 랜딩한 후 돌아와서 다시 클릭하거나, 비디오 Pause/Resume 등이 불리거나 inline으로 재생 중에 fullscreen 재생하기를 누르는 경우 등) |
| **주의** | 비디오 광고의 경우 mediaView 내에 재생/전체화면재생/리플레이/랜딩 버튼이 유저에게 노출되어 있고, 이 버튼을 클릭했을 때 아무런 반응이 없는 것은 유저 경험상 이상하기 때문에, mediaView를 clickableViews로 지정하지 않았더라도 해당 4개의 버튼은 클릭이 가능하며 클릭 시 onClicked 콜백이 불림.<br>또한 비디오 재생 중에 Pause/Resume 등을 위해 위에 있는 "재생" 버튼이 반복적으로 눌릴 수 있는데, 이 경우에도 매번 onClicked 콜백이 불림 |
| **추천 대응** | X |

## onRewardRequested

| 항목 | 설명 |
|---|---|
| **정의** | 광고에 대한 리워드 지급 조건을 만족해서 리워드가 지급 요청되었음<br>일반 광고의 경우 click 후 너무 빠르게(1초) back-button을 눌러 돌아오지 않았고, 비디오 광고의 경우 최소 재생 시간이 지남 |
| **중복 호출 여부** | 보통 한 번 호출되나, 아래 onRewarded의 Failure 조건이 만족하는 경우 호출이 되지 않거나(리워드 지급 대상이 아닌 경우), 중복하여 호출될 수 있음(서버에서 에러가 나서 적립이 이루어지지 않아 유저가 재시도 하는 경우) |
| **주의** | 해당 콜백은 생략이 가능함 (리워드 지급 조건을 만족시키지 못 하면 리워드가 지급되지 않는데, 이때는 onRewarded 콜백에 Failure로 전달됨) |
| **추천 대응** | 리워드 지급 완료에 대한 Response를 받기까지 시간이 걸릴 수 있으므로 로딩 이미지 등을 보여줌.<br>비디오의 경우 최소 재생 시간을 Play 한 후 리워드가 지급되고 onClicked 콜백이 여러 번 호출되기 때문에, onClicked 콜백이 아닌 onRewardRequested 콜백에서 로딩 이미지를 보여주는 것을 추천함 |

## onRewarded

| 항목 | 설명 |
|---|---|
| **정의** | 리워드 관련한 처리가 완료됨 |
| **중복 호출 여부** | SUCCESS는 한 번만 호출되고, Failure의 다양한 경우는 중복 호출될 수 있음 |

result 인자에 담겨 전달되는 상태값은 아래와 같습니다. (AOS / iOS 전달값)

| AOS | iOS | Description | 비고 |
|---|---|---|---|
| SUCCESS | SABRewardResultSuccess | 리워드가 성공적으로 지급 완료됨 |  |
| ALREADY_PARTICIPATED | SABRewardResultAlreadyParticipated | 이미 참여한 광고(현참여)임 |  |
| MISSING_REWARD | SABRewardResultMissingReward | 리워드가 없는 광고임 | 단, 기참여 광고(rewardStatus가 received) 역시 이 콜백이 불리기 때문에 별도 처리가 필요함 |
| TOO_SHORT_TO_PARTICIPATE | SABRewardResultTooShortToParticipate | 일반 광고의 경우 유저가 광고 click 후 너무 빨리(1초 이내) back-button을 눌러서 돌아옴 |  |
| NETWORK_TIMEOUT | SABRewardResultTimeout | 리워드 지급을 요청했지만 Response를 기다리는 데에 Timeout(5초)이 발생함 |  |
| BROWSER_NOT_LAUNCHED | SABRewardResultBrowserNotLaunched | 광고를 클릭했지만 어떤 이유로든 브라우저가 열리지 않음 |  |
| UNKNOWN_NETWORK_ERROR | SABRewardResultNetworkError | 알 수 없는 이유로 네트워크 에러가 발생함 |  |
| UNKNOWN_SERVER_ERROR | SABRewardResultServerError | 알 수 없는 이유로 서버 쪽에서 에러가 발생함 |  |
| UNKNOWN_CLIENT_ERROR | SABRewardResultClientError | 알 수 없는 이유로 런타임 에러가 발생함 |  |
| UNKNOWN_ERROR | SABRewardResultUnknownError | 그 외의 모든 에러 |  |

**추천 대응**

- 모든 경우, onRewardRequested에서 만든 "로딩 이미지"가 있으면 없앰
- SUCCESS의 경우 리워드 지급 완료 애니메이션을 보여줌
- ALREADY_PARTICIPATED 또는 MISSING_REWARD에서 rewardStatus가 received인 경우에 대해서 "이미 적립한 광고" 메시지를 보여줌
- TOO_SHORT_TO_PARTICIPATE에서 광고를 더 보시면 리워드가 지급된다는 메시지를 보여줌
- 그 외의 경우 일반 에러 메시지를 보여줌

## onParticipated

| 항목 | 설명 |
|---|---|
| **정의** | 광고 참여가 완료됨 |
| **중복 호출 여부** | 한 번만 호출됨 |
| **주의** | 유저가 한 광고(A)를 참여 완료했을 때, 그 광고뿐만 아니라 SDK가 현재 로드하고 있는 모든 똑같은 광고(Planet AD가 부여한 id 값으로 구분)에 대해서도 해당 콜백이 호출되고 광고의 isParticipated가 true로 변경됨 |
| **추천 대응** | isParticipated가 true인 광고에 대해 적용하는 UI(리워드 부분을 없애고 지급 처리 되었음을 나타내는 체크 표시 등)를 적용함 |
