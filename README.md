# **SuezX SDK**

## SuezX SDK 통합

### 1. SuezX SDK 다운로드
SuezX SDK를 JitPack을 통해 다운로드할 수 있습니다. 프로젝트의 build.gradle에 다음을 추가하세요. 
~~~groovy
allprojects {
    repositories {
        maven { url 'https://jitpack.io' }
    }
}
~~~

앱 모듈의 build.gradle에 다음을 추가하세요.
~~~groovy
dependencies {
    implementation 'com.github.tpmn:suezx-android-sdk:2.1.0'
}
~~~

Google의 정책에 따라 Google Advertising ID를 사용해야 합니다. 앱 모듈의 build.gradle에 다음을 추가하세요.
~~~groovy
dependencies {
    implementation ‘com.google.android.gms:play-services-ads-identifier:17.0.0’
    implementation ‘com.google.android.gms:play-services-base:17.5.0’
}
~~~

Java 8을 지원하려면 앱 모듈의 build.gradle에 다음을 추가하세요.
~~~groovy
android {
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
      }
}
~~~

### 2. AndroidManifest.xml 업데이트
SuezX SDK가 정상적으로 동작하도록 AndroidManifest.xml에 다음 권한을 추가하세요.
~~~
<manifest>
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.INTERNET" />
</manifest>
~~~

## SuezX 배너 광고 구현

### 1. XML 레이아웃에 배너 인벤토리 정의
~~~
<io.tpmn.suezx.SuezxBannerAd
    android:id="@+id/banner"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"/>
~~~

### 2. 배너 인벤토리에 광고 로드
`Activity` 또는 `Fragment`에 `SuezXBannerAd` 객체를 선언하세요.
~~~java
private SuezXBannerAd suezXBannerAd;
~~~

`Activity`의 `onCreate()` 또는 `Fragment`의 `onCreateView()`에 다음을 추가하세요. 담당자로부터 발급 받은 퍼블리셔 ID와 인벤토리 ID를 사용해야 합니다.
~~~java
suezXBannerAd = findViewById(R.id.banner);

suezXBannerAd.setPublisherId(YOUR_BANNER_PUBLISHER_ID_HERE); // FIXME: 배너 퍼블리셔 ID를 넣으세요.
suezXBannerAd.setInventoryId(YOUR_BANNER_INVENTORY_ID_HERE); // FIXME: 배너 인벤토리 ID를 넣으세요.
suezXBannerAd.setAdHeight(250); // 300 x 250 배너의 경우에만 추가하세요.
suezXBannerAd.setUseCache(true); // 선택. 기본값: true. 
suezXBannerAd.setRefreshInterval(30); // 선택. 기본값: 60(초), 최솟값: 20(초), 최댓값: 120(초).

suezXBannerAd.loadAd();
~~~

또는 테스트 ID를 사용해서 테스트 광고를 요청할 수도 있습니다.
- 320 x 50 배너 테스트 광고 ID  
퍼블리셔 ID: TPMN  
인벤토리 ID: 14061
- 300 x 250 배너 테스트 광고 ID   
퍼블리셔 ID: TPMN  
인벤토리 ID: 14062

`Activity`의 `onDestroy()` 또는 `Fragment`의 `onDestroyView()`에 다음을 추가하세요.
~~~java
if (suezXBannerAd != null) {
    suezXBannerAd.destroy();
    suezXBannerAd = null;
}
~~~

### 3. 리스너 구현 및 설정
~~~java
suezXBannerAd.setBannerListener(new SuezXBannerListener() {
        @Override
        public void onBannerLoaded() {
            Log.i(TAG, "SuezX banner ad loaded.");
        }

        @Override    
        public void onBannerFailed(SuezXError, suezXError) {
            Log.i(TAG, "SuezX banner ad failed. " + sueXError);
        }

        @Override
        public void onBannerClicked() {
            Log.i(TAG, "SuezX banner ad clicked.");
        }

        @Override
        public void onBannerClosed() {
            Log.i(TAG, "SuezX banner ad closed.");
        }
});
~~~

## SuezX 인터스티셜 광고 구현

### 1. 광고 로드
`Activity`에 `SuezXInterstitialAd` 객체를 선언하세요.
~~~java
private SuezXInterstitialAd suezXInterstitialAd;
~~~

`Activity`의 `onCreate()`에 다음을 추가하세요. 담당자로부터 발급 받은 퍼블리셔 ID와 인벤토리 ID를 사용해야 합니다.
~~~java
suezXInterstitialAd = new SuezXInterstitialAd(this);

suezXInterstitialAd.setPublisherId(YOUR_INTERSTILTIAL_PUBLISHER_ID_HERE); // FIXME: 인터스티셜 퍼블리셔 ID를 넣으세요.
suezXInterstitialAd.setInventoryId(YOUR_INTERSTILTIAL_INVENTORY_ID_HERE); // FIXME: 인터스티셜 인벤토리 ID를 넣으세요.
suezXInterstitialAd.setUseCache(true); // 선택. 기본값: true.

suezXInterstitialAd.loadAd();
~~~

또는 테스트 ID를 사용해서 테스트 광고를 요청할 수도 있습니다.
- 320 x 480 인터스티셜 테스트 광고 ID  
퍼블리셔 ID: TPMN  
인벤토리 ID: 14063

`Activity`의 `onDestroy()`에 다음을 추가하세요.
~~~java
if (suezXInterstitialAd != null) {
    suezXInterstitialAd = null;
}
~~~

### 2. 리스너 구현 및 설정, 광고 게재
~~~java
suezXInterstitialAd.setInterstitialListener(new SuezXInterstitialListener() {
        @Override
        public void onInterstitialLoaded() {
            Log.i(TAG, "SuezX interstitial ad loaded.");
            
            // 로드 완료 후 게재
            if (suezXInterstitialAd != null) {
                suezXInterstitialAd.showAd();
            }
        }

        @Override
        public void onInterstitialFailed(SuezXError suezXError) {
            Log.i(TAG, "SuezX interstitial ad failed. " + suezXError);
        
        }

        @Override
        public void onInterstitialShown() {
            Log.i(TAG, "SuezX interstitial ad shown.");
        }

        @Override
        public void onInterstitialClicked() {
            Log.i(TAG, "SuezX interstitial ad clicked.");
        }

        @Override
        public void onInterstitialDismissed() {
            Log.i(TAG, "SuezX interstitial ad dismissed.");
        }
});
~~~
