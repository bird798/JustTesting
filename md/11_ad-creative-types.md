# 11. 광고소재종류

> [!NOTE]
> **Planet AD가 제공하는 광고 소재(Creative)의 종류와, 각 소재가 어떤 지면(Unit Type)에서 지원되는지**를 정리한 문서입니다. 이 표를 확인하면 내 지면(Native/Feed/Interstitial/POP/LockScreen)에 어떤 소재가 노출될 수 있는지, 플랫폼(AOS/IOS/WEB)별 지원 여부를 파악할 수 있습니다.

## 개요

광고 소재(Creative)는 사용자에게 실제로 보여지는 광고의 형태입니다. 소재 종류마다 이미지/동영상/HTML 등 형식과 지원 규격(사이즈·비율)이 다르며, 소재별로 노출 가능한 지면(연관 Unit Type)도 플랫폼(AOS/IOS/WEB)에 따라 다릅니다.

아래 표에서 각 소재의 형식·규격, 그리고 플랫폼별로 연관되는 Unit Type을 확인하세요. Unit Type의 미지원은 해당 플랫폼에서 그 소재가 노출되지 않음을 의미합니다.

## Creative Type

| 광고 소재<br>(Creative) Type | Description | 연관 Unit Type | 기타 |
|---|---|---|---|
| **Native** | 가로형 이미지 타입 소재<br>1200x627 | AOS - Native, Feed, Interstitial(BottomSheet, Dialog, FullScreen No Edge), POP<br>IOS - Native, Feed, Interstitial(BottomSheet, Dialog, FullScreen No Edge)<br>WEB - Native |  |
| **Image** | 세로형 이미지 소재<br>1080x2340<br>720x1230<br>320x480 | AOS - LockScreen, Interstitial(FullScreen, FullScreen No Edge)<br>IOS - Interstitial(FullScreen, FullScreen No Edge)<br>WEB - 미지원 |  |
| **TOPDA** | 최상단 DA를 위한 이미지 소재<br>1200x700<br>360x210 | AOS - Native, Feed, Interstitial(BottomSheet, Dialog), POP<br>IOS - Native, Feed<br>WEB - Native | v1.14.0부터 지원 |
| **VAST** | 동영상 소재<br>가로형인 경우 16:9, 4:3 비율<br>세로형인 경우 9:16, 3:4 비율 | AOS - Native, Feed, Interstitial, POP<br>IOS - Native, Feed, Interstitial<br>WEB - Native |  |
| **VIDEO** | 동영상 소재<br>가로형인 경우 16:9, 4:3 비율<br>세로형인 경우 9:16, 3:4 비율 | AOS - 미지원<br>IOS - Native, Feed, Interstitial<br>WEB - Native | WEB SDK v1.16.0x부터 웹에서도 미지원<br>일괄 VAST만 제공 |
| **HTML** | HTML 형태의 소재<br>320x100<br>300x250<br>320x480 | AOS - Native(320x100, 300x250), Interstitial-FullScreen, FullScreen No Edge(320x480)<br>IOS - Native(320x100, 300x250), Interstitial-FullScreen, FullScreen No Edge(320x480)<br>WEB - 미지원 | 배경색상 추출 기능 제공 |
| **WEB BANNER** | P.AD에서 사전 정의된 HTML 형태의 소재<br>1200x627<br>Coupang, Naver, 모비위드, 등등 | AOS - Native, Feed, Interstitial(BottomSheet, Dialog)<br>IOS - Native, Feed, Interstitial(BottomSheet, Dialog)<br>WEB - 미지원 |  |
