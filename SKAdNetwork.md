## 모바일 어트리뷰션 방법론

1. 결정론적 방법: 유저에 대한 **고유 식별인자**를 이용해서 "유저"와 "설치"가 매칭하는 건을 "설치 어트리뷰션"으로 측정하는 방법    
&nbsp;1) **앱 설치 리퍼러**(referrer)를 활용한 매칭  
&nbsp;&nbsp;&nbsp;&nbsp;- 현재 구글은 구글플레이를 활용하며, 페이스북은 앱 자체의 구매 전환 측정 등에 활용 중([관련문서](https://blog.hunchads.com/ios-14-facebook-tracking-update))  
&nbsp;2) **광고 식별인자**(ADID)를 활용한 매칭  
&nbsp;&nbsp;&nbsp;&nbsp;- 이것에 대해 구글은 "GAID(Google Advertising ID)", 애플은 "IDFA(Apple ID For Advertisers)"라고 부름  
&nbsp;&nbsp;&nbsp;&nbsp;- **iOS 14.5 이후, IDFA 수집을 위해서는 앱 내 ATT(App Tracking Transparency), 즉, "광고추적 허용 동의팝업"을 반드시 구현해야 함**  
&nbsp;&nbsp;&nbsp;&nbsp;- 또한, EU의 개인정보 보호에 관한 규정(GDPR)이 강화되는 추세이므로 구글도 머지않아 애플과 비슷해질 것이라는 것이 중론
2. **확률론적 방법**: 여러가지 유저 정보를 조합하여, "앱을 설치했을 것"이라고 추정하고 "설치 어트리뷰션"으로 측정하는 방법  
&nbsp;1) **핑거프린팅**을 활용한 추정  
&nbsp;&nbsp;&nbsp;&nbsp;- 앱 다운한 유저의 IP, 단말기, OS, 통신사, 국가, 언어 등의 정보를 조합하여 나름의 고유 식별정보를 재생산하여 설치를 추정  
&nbsp;&nbsp;&nbsp;&nbsp;- **iOS 14.5 뿐만 아니라 위의 EU GDPR 및 각국 개인정보 보호 강화로 인해 현실적으로 사용 불가  
&nbsp;2) **확률적 모델링**을 활용한 추정  
&nbsp;&nbsp;&nbsp;&nbsp;- 유저의 행동패턴, ATT 옵트-인 유저의 IDFA 등 수집 가능 데이터를 토대로, **고유 식별정보를 재생산하지 않고** 유저 설치경로를 추정  
&nbsp;&nbsp;&nbsp;&nbsp;- 당연히 각 MMP마다 자신들이 사용하는 확률적 모델링 알고리즘은 다를 수 밖에 없음  
&nbsp;&nbsp;&nbsp;&nbsp;- 현재 구글은 GA의 UTM을 활용한 통계 가공을 통해 확률적 모델링 중([관련문서](https://support.google.com/google-ads/answer/10625151?hl=en&ref_topic=3122882#zippy=%2Cwhats-the-difference-between-the-reporting-i-see-for-app-campaigns-and-the-skadnetwork-report-also-what-will-be-available-in-app-attribution-partner-reporting%2Cwhat-reporting-details-should-i-expect-for-conversion-data-captured-through-the-new-url-parameters%2Cis-it-possible-to-observe-longer-delays-for-some-of-my-app-campaigns-on-ios-as-a-result-of-conversion-modeling%2Chow-will-the-new-url-parameters-work-when-the-ad-interaction-occurs-on-a-non-google-site-will-advertisers-need-to-send-google-the-parameter-themselves%2Cin-what-cases-would-these-new-url-parameters-be-used%2Chow-will-skadnetwork-conversion-values-work-with-app-campaigns%2Chow-does-google-model-conversions-when-there-is-no-shared-identifier-between-ad-interactions-and-conversions-or-when-conversions-cant-be-measured))  
&nbsp;&nbsp;&nbsp;&nbsp;- 현재 앱스플라이어는 64비트 전환값에 대한 전략적인 활용과, 기존까지 전환별 가중치 관리를 해왔었던 데이터들을 토대로 머신러닝을 함으로써 확률적 모델링 중([관련문서](https://www.appsflyer.com/kr/blog/skadnetworks-conversion-values-power/))  

## SKAN(SKAdNetwork)이란

1. **SKAN**(StoreKit Ad Network): **앱 설치와 재설치** 어트리뷰션을 할 수 있게 하는 애플의 어트리뷰션 프레임워크
2. **SKAN의 목적**: 위의 "결정론적 모바일 어트리뷰션 측정"을 더이상 불가능하게 하고, 유저 정보를 정확하게 측정하지 못하게 하기 위함
3. **MMP**: 트래킹툴 또는 광고주 앱

## 앱 설치 어트리뷰션 작동구조

1. **기존 체제**: 해당 앱 설치 유저의 정보가 담긴 포스트백을 MMP가 수집하여 매체에게 제공해왔음  
2. **SKAN 체제**: 해당 앱 설치 유저의 정보가 담긴 포스트백은 오직 **앱스토어**가 수집하여 매체에게만 제공함  
&nbsp; ➡️ &nbsp; 따라서 MMP는 매체와 별도로 엔드포인트 연동을 진행하여 SKAN 포스트백의 사본을 받을 수 밖에 없게 됨

## SKAN의 특징

1. 유저 식별인자(IDFA)가 결여되어 있고, 이를 수집하기 위해선 앱 내 ATT 팝업 구현이 필요(현실적으로 추적허용 동의하는 유저는 없음)
2. 오직 **클릭**만 어트리뷰션 기준으로 인정
3. 오직 **설치**에 관한 포스트백만 전달하므로 리타겟팅은 실질적으로 불가능해짐  
 ➡️ &nbsp; 만약 유저가 앱 설치한 시점부터 24시간 이내에 다른 전환을 일으키더라도, 최우선순위인 1건만 설치 포스트백에 포함시킴  
 ➡️ &nbsp; 전환값은 **6비트**, 즉, 총 64가지로 제한됨
4. 룩백 윈도우는 24시간이므로, 유저가 광고 클릭한지 24시간을 지나서 앱을 다운 및 설치하는 경우에는 organic으로 측정  
 ➡️ &nbsp; 이를 보완하고자 **뷰-쓰루** 어트리뷰션 개념을 신설
5. **딜레이 타이머**를 운영하며, 실시간 설치 포스트백 전송을 불허함  
 ➡️ &nbsp; 유저가 앱 설치한 시점부터 24시간이 지난 후, 0~24시간 중 **랜덤**하게 더 딜레이 한 시점에야 비로소 설치 포스트백 전송  
 👀 &nbsp; 이는 유저의 앱 설치 시점을 정확하게 알 수 없게 함으로써, 이벤트와 유저를 매칭하지 못하게 하려는 목적임
6. 오직 **모바일앱**만 SKAN을 통한 측정이 이뤄지므로, 모바일웹으로 유입된 유저의 설치 포스트백은 측정불가
7. **디퍼드 딥링크**(Deferred Deeplink)를 불허함  
 👀 &nbsp; 디퍼드 딥링크: 앱 첫 실행 시 특정 웹페이지 등으로 랜딩 처리하는 기술(re-engagement)
8. 캠페인은 최대 100가지로 제한됨

## SKAN 체제에서의 최종 데이터(앱스토어가 전달하는 설치 포스트백)

위에서 정리한 SKAN의 특징들로 인해, 결과적으로 설치 포스트백에는 유저 식별인자가 포함되지 않는다. 더불어, 광고 소재나 광고그룹과 같은 세부 데이터도 결여된다. 설치 포스트백에는 오직 캠페인 단위로만 데이터가 존재하게 된다. 아래는 앱스토어가 매체에 제공하는 설치 포스트백의 최종적인 모습이다.  

```java
{
  "version": "3.0",                             // SKAN 버전(현재는 3.0)
  "ad-network-id": "wisetracker.skadnetwork",   // 매체의 고유ID(앱스토어에서 부여)
  "campaign-id": 42,                            // 캠페인ID
  "transaction-id": "6afd212-124...(이하생략)",   // 설치 포스트백의 중복제거 및 유효성검증을 위한 고유ID
  "app-id": 12736128,                           // 광고주 앱의 고유ID(앱스토어에서 부여)
  "attribution-signature": "MADS...(이하생략)",   // 설치 포스트백의 암호화 확인을 위한 애플의 서명
  "redownload": true,                           // 신규 설치 유저라면 false, 재설치 유저라면 true
  "source-app-id": 3468434,                     // 광고를 표시한 앱의 고유ID(앱스토어에서 부여)
  "fidelity-type": 1,                           // 뷰-쓰루 어트리뷰션이면 0, 클릭이면 1
  "conversion-value": 20,                       // 앱 설치 유저가 다른 전환을 일으킨 경우 해당 전환값(0~63)
  "did-win": true                               // SKAN이 어트리뷰션을 얻었다면 true, 얻지 못했다면 false
}
```
