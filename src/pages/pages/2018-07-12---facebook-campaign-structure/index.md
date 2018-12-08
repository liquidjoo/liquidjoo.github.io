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

```ㅍ
- 노출수, 클릭수, 지출 금액 등의 높은 수준의 인사이트도 표시됩니다.
```java
APINodeList<AdsInsights> adsInsightss = new Campaign(<CAMPAIGN_ID>, context).getInsights()
  .setParam("end_time", end_time)
  .requestField("impressions")
  .requestField("inline_link_clicks")
  .requestField("spend")
  .execute();
```

### 광고 세트
- 광고 세트는 여러 광고가 포함된 그룹으로, 이러한 광고를 게재할 예산과 기간을 설정합니다. 하나의 광고 세트에 포함된 모든 광고는 동일한 타게팅을 가집니다.

```java
AdSet adSet = new AdAccount(act_<AD_ACCOUNT_ID>, context).createAdSet()
  .setName("My First AdSet")
  .setLifetimeBudget(20000L)
  .setStartTime(<START_TIME>)
  .setEndTime(<END_TIME>)
  .setCampaignId(<CAMPAIGN_ID>)
  .setBidAmount(500L)
  .setBillingEvent(AdSet.EnumBillingEvent.VALUE_IMPRESSIONS)
  .setOptimizationGoal(AdSet.EnumOptimizationGoal.VALUE_POST_ENGAGEMENT)
  .setTargeting(
    new Targeting()
      .setFieldAgeMax(24L)
      .setFieldAgeMin(20L)
      .setFieldBehaviors(Arrays.asList(
        new IDName()
          .setFieldId("6002714895372")
          .setFieldName("All travelers")
      ))
      .setFieldGenders(Arrays.asList(1L))
      .setFieldGeoLocations(
        new TargetingGeoLocation()
          .setFieldCities(Arrays.asList(
            new TargetingGeoLocationCity()
              .setFieldDistanceUnit("mile")
              .setFieldKey("2420605")
              .setFieldRadius(10L)
          ))
          .setFieldCountries(Arrays.asList("JP"))
          .setFieldRegions(Arrays.asList(
            new TargetingGeoLocationRegion()
              .setFieldKey("3886")
          ))
      )
      .setFieldHomeOwnership(Arrays.asList(
        new IDName()
          .setFieldId("6006371327132")
          .setFieldName("Renters")
      ))
      .setFieldInterests(Arrays.asList(
        new IDName()
          .setFieldId("6003107902433")
          .setFieldName("Association football (Soccer)")
      ))
      .setFieldLifeEvents(Arrays.asList(
        new IDName()
          .setFieldId("6002714398172")
          .setFieldName("Newlywed (1 year)")
      ))
      .setFieldPublisherPlatforms(Arrays.asList("facebook", "audience_network"))
  )
  .setStatus(AdSet.EnumStatus.VALUE_PAUSED)
  .execute();
String ad_set_id = adSet.getId();
```

- UI에서 캠페인 내 모든 광고 세트를 나열하고 싶은 경우 원하는 필드와 상태를 지정하여 다음 경로에 대한 요청을 작성하세요.
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

    new Campaign(id, context).getAdSets()
      .requestNameField()
      .requestStartTimeField()
      .requestEndTimeField()
      .requestDailyBudgetField()
      .requestLifetimeBudgetField()
      .execute();

  }
}
```

### 광고
- 광고 개체에는 크리에이티브를 포함하여 Facebook에 광고를 표시하는 데 필요한 모든 정보가 포함됩니다.
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

    new AdAccount(id, context).createAd()
      .setName(\"My Ad\")
      .setAdsetId(<adSetID>L)
      .setCreative(
          new AdCreative()
            .setFieldId(\"<adCreativeID>\")
        )
      .setStatus(Ad.EnumStatus.VALUE_PAUSED)
      .execute();

  }
}
```

- 이제 UI에 광고 세트의 모든 광고를 나열할 차례입니다. 이렇게 하려면 검색할 필드를 지정하여 다음 경로에 대한 요청을 작성하세요.
```java
APINodeList<Ad> ads = new AdSet(<AD_SET_ID>, context).getAds()
  .setEffectiveStatus("[\"ACTIVE\",\"PAUSED\",\"PENDING_REVIEW\",\"PREAPPROVED\"]")
  .requestNameField()
  .requestConfiguredStatusField()
  .requestEffectiveStatusField()
  .requestCreativeField()
  .execute();
```
- 전체 광고 수가 하나의 페이지에 표시할 수보다 큰 경우 가져올 항목 수로 limit 필드를 지정하고 남은 항목에는 페이지를 매겨야 합니다.

- 페이지 매기기는 응답의 일부로 반환된 커서를 사용하여 수행됩니다.
```json
"paging": {
  "previous" : "https://graph.facebook.com/...",
  "next": "https://graph.facebook.com/...", 
  "cursors": {
    "before": "NjAxNjc3NTU1ODMyNw==", 
    "after": "NjAxMTc0NTU2MjcyNw=="
  }

```

### 목표
- 목표는 광고를 통해 사람들에게 유도할 액션이며, 개체는 사람들이 액션을 수행하는 기반을 말합니다. 

#### 목표 설정
새 캠페인, 광고 세트, 광고, 광고 크리에이티브 개체를 만든 후 각 개체를 만들기 위한 필수 필드를 제공하세요. 캠페인을 만드는 데 필요한 최소 API 호출은 다음과 같습니다

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

    new AdAccount(id, context).createCampaign()
      .setName(\"My First Campaign\")
      .setObjective(Campaign.EnumObjective.VALUE_PAGE_LIKES)
      .setStatus(Campaign.EnumStatus.VALUE_PAUSED)
      .execute();

  }
}
```


- 목표를 명시적으로 설정할 경우 광고에 대해 적절한 추적, 최적화, 입찰 옵션을 얻을 수 있습니다. 각 목표에 대한 고유 UI 및 분석 대시보드를 볼 수 있습니다.


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

    new AdAccount(id, context).createCampaign()
      .setName(\"My First Campaign\")
      .setObjective(Campaign.EnumObjective.VALUE_POST_ENGAGEMENT)
      .setStatus(Campaign.EnumStatus.VALUE_PAUSED)
      .execute();

  }
}
```


####전체 목표 리스트는 
```https://developers.facebook.com/docs/marketing-api/reference/ad-campaign-group``` 참고


```
참조
https://developers.facebook.com/docs/marketing-api/buying-api
```