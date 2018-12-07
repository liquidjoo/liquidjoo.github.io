---
title: facebook 캠페인 구조
date: "2018-12-07T19:00:00.121Z"
layout: post
draft: false
path: "/posts/facebook-campaign-structure"
category: "facebook", "marketing"
description: "페이스북 광고를 위한 캠페인 구조"
---

## 캠페인 구조
- Facebook의 캠페인 구조는 캠페인, 광고 세트, 광고 세가지 수준으로 구성됩니다. API에서는 개발자를 위해 "creative"라는 네 번째 수준이 제공됩니다.

### 캠페인
- 광고 계정 구조의 최상위 수준으로, 페이지 게시물 참여 유도 등 광고주가 원하는 하나의 목표를 나타내야 합니다. 캠페인 목표를 설정하면 캠페인에 추가되는 광고의 목표가 올바르게 설정되었는지 확인됩니다.

- 광고 관리자에는 광고 계정에 포함된 활성 캠페인 및 일시 중단된 캠페인이 모두 표시됩니다.

- 특정 계정에 연결된 캠페인을 읽어들이려면 사용 중인 광고 계정의 연결에 대한 요청을 작성. 기본적으로 상태가 ACTIVE or PAUSED인 캠페이만 반환되므로 필요한 경우 다음과 같이 추가 상태를 지정해야 합니다.

```java

import com.facebook.ads.sdk.*;
import java.io.File;
import java.util.Arrays;

public class SAMPLE_CODE_EXAMPLE {
  public static void main (String args[]) throws APIException {

    String access_token = \"<ACCESS_TOKEN>\";
    String app_secret = \"<APP_SECRET>\";
    String app_id = \"<APP_ID>\";
    String id = \"<ID>\";
    APIContext context = new APIContext(access_token).enableDebug(true);

    new AdAccount(id, context).getCampaigns()
      .setEffectiveStatus(Arrays.asList(Campaign.EnumEffectiveStatus.VALUE_ACTIVE,Campaign.EnumEffectiveStatus.VALUE_PAUSED))
      .requestNameField()
      .requestObjectiveField()
      .execute();

  }
}

```
- 노출수, 클릭수, 지출 금액 등의 높은 수준의 인사이트도 표시됩니다.
```java
APINodeList<AdsInsights> adsInsightss = new Campaign(<CAMPAIGN_ID>, context).getInsights()
  .setParam("end_time", end_time)
  .requestField("impressions")
  .requestField("inline_link_clicks")
  .requestField("spend")
  .execute();
```


- 전체 목표 리스트는 ```https://developers.facebook.com/docs/marketing-api/reference/ad-campaign-group``` 참고

```

```
https://developers.facebook.com/docs/marketing-api/buying-api
```