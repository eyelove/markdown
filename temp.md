
Table of Contents
=================

* [시작하기](#시작하기)
* [SDK 적용하기](#sdk-적용하기)
* [어플리케이션 설정](#어플리케이션-설정)
  * [라이브러리 추가](#라이브러리-추가)
  * [프로가드 설정](#프로가드-설정)
  * [매니페스트 설정](#매니페스트-설정)
  * [Permission 설정](#permission-설정)
* [광고 적용하기](#광고-적용하기)
  * [인스턴스 공통 메소드](#인스턴스-공통-메소드)
  * [배너광고](#배너광고)
  * [전면 광고](#전면-광고)
  * [네이티브](#네이티브)
  * [다이얼로그 공통 메소드](#다이얼로그-공통-메소드)
  * [다이얼로그 광고 (전면)](#다이얼로그-광고-전면)
  * [다이얼로그 광고 (네이티브)](#다이얼로그-광고-네이티브)


## 시작하기

## SDK 적용하기

1. 계정을 생성합니다
2. Inventory -> New App을 선택합니다.
3. 앱정보를 등록한후, unit을 생성 합니다.

## 어플리케이션 설정


### 라이브러리 추가

ExelBid Android SDK가 제대로 작동하려면 Google Play Service 4.0 이상의 라이브러리가 필요합니다. 광고 식별자 수집에 대한 Google Play 콘텐츠 가이드라인을 준수하기 위한 것입니다.

##### 1. AndroidManifest.xml파일에 <application> 태그 안에 아래 코드를 추가합니다.

```xml
<meta-data  
android:name="com.google.android.gms.version"  
android:value="@integer/google_play_services_version" />  
```

##### 2. Google Play Service jar를 dependencies에 추가합니다.

poject structure -> dependencies -> add -> library dependency 에서 com.google.android.gms:play-services or com.google.android.gms:play-services-ads를 추가합니다

_* eclipse를 사용하는 경우에는 Google Play Service 라이브러리 프로젝트를 추가합니다._

### 프로가드 설정

```
-keep class com.google.android.gms.ads.identifier.AdvertisingIdClient{public *;}  
-keep class com.google.android.gms.ads.identifier.AdvertisingIdClient$Info{public *;}  
-keep class com.google.android.gms.common.api.GoogleApiClient { public *; }  
-keep class com.google.android.gms.common.api.GoogleApiClient$* {public *;}  
-keep class com.google.android.gms.location.LocationServices {public *;}  
-keep class com.google.android.gms.location.FusedLocationProviderApi {public *;}  
  
-keepattributes SourceFile,LineNumberTable,InnerClasses  
-keep class com.onnuridmc.exelbid.** { *; }  
-dontwarn com.onnuridmc.exelbid.**  
-dontoptimize
```

### 매니페스트 설정

com.onnuridmc.exelbid.common.ExelBidActivity를 AndroidManafest.xml의 <applicatrion> 태그 안에 추가합니다. 이 Acitivity는 전면광고를 표시하는데 사용됩니다.

```xml
<activity android:name="com.onnuridmc.exelbid.common.ExelBidActivity"  
          android:configChanges="keyboardHidden|orientation|screenSize" >  
</activity>  
```

### Permission 설정

* 필수권한

```xml
<uses-permission android:name="android.permission.INTERNET" />  
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />  
```

* 권장권한

```xml
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />  
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />  
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />  
<uses-permission android:name="android.permission.CHANGE_WIFI_STATE" />  
```

## 광고 적용하기

### 인스턴스 공통 메소드

광고의 효율을 높이기 위해 나이, 성별을 설정하는 것이 좋습니다.

*	setAge(int) : 나이
*	setGender(boolean) : 성별 (true : 남자, false : 여자)
*	addKeyword(String, String) : Custom 메타 데이터 (Key, Value)
*	setTestMode(Boolean) : 광고의 테스트를 위해 설정하는 값입니다. 통계에 적용 되지 않으며 항상 광고가 노출되게 됩니다.
*	setAdUnitId(String) : 광고 아이디를 셋팅 합니다.


### 배너광고

띠 배너 형태의 광고를 사용합니다.

1.  배너 광고 인스턴스를 원하는 layout위치에다가 생성합니다.
```xml
<com.onnuridmc.exelbid.ExelBidAdView  
     android:id="@+id/adview"  
     android:layout_width="match_parent"  
     android:layout_height="wrap_content">  
</com.onnuridmc.exelbid.ExelBidAdView>  
```

2. Activity에서 해당 인스턴스를 바인딩 합니다.
```java
ExelBidAdView mAdView = (ExelBidAdView) findViewById(R.id.adview); 
```

3. 사이트로부터 발급받은 유닛 아이디를 확인합니다
4. 유닛아이디를 배너 인스턴스에 셋팅합니다.
```java
setAdUnitId(String)
```

5. 광고를 요청합니다.
```java
loadAd()
```

6. 광고 이벤트 등록
* ``setAdListener(OnBannerAdListener)``
  - ``onAdLoaded()`` : 광고가 로딩된 시점에 호출 됩니다.
  -	``onAdFailed(ExelBidError)`` : 서버로부터 광고를 가져오지 못한 경우에 호출 됩니다.

7. 기본적으로 띠배너의 광고의 경우 유닛에 설정한 리플래쉬 시간에 따라 자동으로 갱신 됩니다. 해당 기능을 사용하지 않게 하기 위해서는 리플래쉬 기능을 해제해 주어야 합니다.
```java
setAutorelashDisable()
```

8. 엑티비티 종료시 destroy를 호출합니다.
```java
destroy()
```

### 전면 광고

### 네이티브

### 다이얼로그 공통 메소드

### 다이얼로그 광고 (전면)

### 다이얼로그 광고 (네이티브)
