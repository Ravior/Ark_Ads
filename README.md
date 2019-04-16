# Ark_Ads
聚合广告sdk
## 使用方法
### Gradle
```groovy
maven { url 'http://nexus.xiaoc.cn/repository/maven-releases/'}

implementation 'com.ark.ads:core:1.1.0'(必须)
implementation 'com.ark.ads:iflytek:1.1.0'(科大讯飞广告)
implementation 'com.ark.ads:longyun:1.1.0'(龙云聚合广告，包含GDTUnionSDK.4.19.574.min.jar)
implementation 'com.ark.ads:zhaocai:1.1.0'(无双科技广告,已去除广点通依赖)
implementation 'com.ark.ads:gdt:1.1.0'(广点通单独依赖时需要拷贝GDTUnionSDK.4.19.574.min.jar)
implementation 'com.ark.ads:ttad:1.1.0'(今日头条穿山甲广告)
```
### 初始化SDK，非必须
```java
ADTool.initialize(new ADTool.Builder()
        .setStrategy(Strategy.cycle)//按sort轮流排序策略
        .setLoadOtherWhenVideoDisable(true)//视频广告失败后尝试其他原生横图广告填充
        .setLocalConfig(JsonUtils.getJson(this,"localconfig.json"))//使用本地json字符串配置
        .setDebugMode(BuildConfig.DEBUG)//调试模式（日志打印，平台标识）
        .build());
```
#### 广告使用
##### 1.开屏
```java
ADTool.getADTool().getManager()
        .getSplashWrapper()
        .needPermissions(true)//是否由SDK申请必要权限
        .setPermissions(list)//自定义权限列表
        .loadSplash(activity, adContainer, rootView, new OnSplashImpl() {

                @Override
                public void onAdTimeTick(long tick) {

                }

                @Override
                public void onAdShouldLaunch() {

                }
        });
```
##### 2.原生信息流
```java
ADTool.getADTool().getManager()
        .getWrapper()
        .loadBannerView(context, adContainer);
        
ADTool.getADTool().getManager()
        .getNativeWrapper()
        .loadNativeView(context, adContainer);
        
ADTool.getADTool().getManager()
        .getNativeWrapper()
        .loadSmallNativeView(context, adContainer);
        
ADTool.getADTool().getManager()
        .getNativeWrapper()
        .loadVideoView(context, adContainer);
```
##### 3.配置
```java
Map<String,String> mConfig= ADTool.getADTool()
        .getManager()
        .getConfigWrapper()
        .getConfig();
        
boolean hasAd=ADTool.getADTool()
        .getManager()
        .getConfigWrapper()
        .hasAd();
```
##### 4.交叉推广
```java
new SelfNativeAD(this, SelfADStyle.INTER_RECOMMEND)
        .setListener(new SelfNativeAD.ADListener() {
                @Override
                public void onAdLoad(List<SelfDataRef> dataRefs) {
                        if (dataRefs != null && dataRefs.size() > 0) {
                            SelfDataRef selfDataRef = dataRefs.get(0);
                            RecommendDialog recommendDialog = new RecommendDialog(
                                    context);
                            recommendDialog.setBundle(selfDataRef);
                            recommendDialog.show();
                        }
                    }

                @Override
                public void onAdFailed(int errorCode, @NonNull String errorMsg) {

                }
        }).loadAllADList();
```
##### 5.可能出现的冲突解决
```xml
<activity
            android:name="com.baidu.mobads.AppActivity"
            tools:replace="android:configChanges"
            android:configChanges="keyboard|keyboardHidden|orientation"/>
```
