### 1. **CTR과 CPC 계산**

CTR (Click Through Rate)와 CPC (Cost Per Click)를 계산하는 문제입니다.

**문제:** 각 광고(`ad_id`)에 대해 다음 값을 계산한 뒤 기존 데이터에 추가해서 보여주세요. 계산값이 안 나오면 NULL로 처리해주세요

- **CTR**: `(Clicks / Impressions) * 100`
- **CPC**: `Spent / Clicks`

**정답**
```sql
SELECT 
  *,
  (Clicks / NULLIF(Impressions, 0)) * 100 AS CTR,
  Spent / NULLIF(Clicks, 0) AS CPC
FROM Facebook_AD.Facebook_AD
```
### 2. **연령대별 평균 CTR 계산**

**문제:** 연령대(`age`)별 평균 CTR을 계산하고 오름차순으로 정렬하세요.

CTR 공식은 `(Clicks / Impressions) * 100`이며, 같은 연령대에 속하는 모든 광고의 CTR 평균을 구합니다.

결과 컬럼: `age`, `average_CTR`

**정답**
```sql
SELECT 
  age,
  AVG((Clicks / NULLIF(Impressions, 0)) * 100) AS average_CTR
FROM 
  Facebook_AD.Facebook_AD
GROUP BY 
  age
ORDER BY 
  average_CTR ASC;
```

### 3. **최소 10번 이상** `Approved_Conversion`**을 기록한 광고 찾기**

**문제:** `Approved_Conversion` 수가 10번 이상인 광고의 정보를 조회하고 내림차순으로 정렬하세요.

결과 컬럼: `ad_id`, `age`, `gender`, `Approved_Conversion`

**정답**
```sql
SELECT 
  ad_id,
  age,
  gender,
  Approved_Conversion
FROM Facebook_AD.Facebook_AD
WHERE 
  Approved_Conversion >= 10
ORDER BY
  Approved_Conversion DESC;
```

### 4. **캠페인별 광고의 개수 찾기**

**문제:** 각 Facebook 캠페인(`fbcampaignid`)에서 사용된 광고(`ad_id`)의 개수를 계산하고 내림차순으로 정렬해주세요.

결과 컬럼: `fbcampaignid`, `Unique_Ads`

**정답**
```sql
SELECT 
  fb_campaign_id,
  COUNT(DISTINCT ad_id) as Unique_Ads
FROM Facebook_AD.Facebook_AD
GROUP BY
  fb_campaign_id
ORDER BY
  Unique_Ads DESC;
```

### 4-1. **캠페인별 광고의 개수 찾기**

문제: 서브쿼리를 이용해서 각 ‘Unique_Ads’의 수를 세어주는 쿼리를 작성해봅시다~

**정답**
```sql
SELECT 
  Unique_Ads,
  COUNT(Unique_Ads) AS Unique_Ads_Count
FROM (
  SELECT 
    fb_campaign_id,
    COUNT(DISTINCT ad_id) AS Unique_Ads
  FROM 
    Facebook_AD.Facebook_AD
  GROUP BY 
    fb_campaign_id
)
GROUP BY 
  Unique_Ads
ORDER BY 
  Unique_Ads;
```
