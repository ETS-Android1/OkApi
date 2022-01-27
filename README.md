# OkApi
Android接口联调工具
1.内含Gson、okhttp、okio
2.支持Get、Post、Put、Delete、Patch
# Maven
1.build.grade
```
allprojects {
    repositories {
		...
		maven { url 'https://jitpack.io' }
	}
}
```
2./app/build.grade
```
dependencies {
	implementation 'com.github.RelinRan:OkApi:2022.1.25.1'
}
```
# 初始化
注意在Application里初始化
```
Configure configure = Api.initialize(this);
configure.debug(true);//调试模式，可以查看日志
configure.url(Api.BETA, "http://47.108.248.71:8109");//设置测试环境地址
configure.url(Api.ONLINE, "https://www.yiketianqi.com");//设置线上环境地址
configure.address(Api.BETA);//设置当前环境地址
configure.contentType(Api.JSON);//全局请求方式JSON
```
# 生命周期
在对应页面onDestroy()里调用api.cancel(context);会自动取消当前页面的请求。
```
Api api = new OkApi();
//to do get or post...

@Override
protected void onDestroy() {
    super.onDestroy();
    api.cancel(context);
}
```
# 类型
## 初始化
```
Api.initialize(this).contentType(Api.FORM_DATA);
```
## 全局
```
Configure.Config().contentType(Api.JSON);
```
## 单个
```
Api api = new OkApi();
RequestParams params = new RequestParams();
params.addHeader(Header.CONTENT_TYPE,Api.FORM_DATA);
api.get(context, "/business/editShelf", params, new OnRequestListener() {
    @Override
    public void onRequestSucceed(Request request, Response response) {
           ApiLog.i("Api", response.body());
    }

    @Override
    public void onRequestFailed(Request request, Exception exception) {

    }
});
```

# 权限
1.网络权限
```
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```
2.非https请求，AndroidManifest.xml中application标签设置属性：
```
android:usesCleartextTraffic="true"
```
# Api

## GET
```
Api api = new OkApi();
RequestParams params = new RequestParams();
params.add("key","value");
api.get(context, "/business/editShelf", params, new OnRequestListener() {
    @Override
    public void onRequestSucceed(Request request, Response response) {
           ApiLog.i("Api", response.body());
    }

    @Override
    public void onRequestFailed(Request request, Exception exception) {

    }
});
```
## POST
```
Api api = new OkApi();
RequestParams params = new RequestParams();
params.add("key","value");
api.post(context, "/business/editShelf", params, new OnRequestListener() {
    @Override
    public void onRequestSucceed(Request request, Response response) {
           ApiLog.i("Api", response.body());
    }

    @Override
    public void onRequestFailed(Request request, Exception exception) {

    }
});
```
## 实体转换
User user = response.convert(User.class);
```
Api api = new OkApi();
RequestParams params = new RequestParams();
params.add("key","value");
api.post(this, "/business/editShelf", params, new OnRequestListener() {

    @Override
    public void onRequestSucceed(Request request, Response response) {
         User user = response.convert(User.class);
    }

    @Override
    public void onRequestFailed(Request request, Exception exception) {

    }
});
```
## 实体上传
```
Api api = new OkApi();
RequestParams params = new RequestParams();
User user = new User();
user.setName("OkApi");
String json = new Gson().toJson(user);
params.add(json);
api.get(context, "/business/editShelf", params, new OnRequestListener() {
    @Override
    public void onRequestSucceed(Request request, Response response) {
           ApiLog.i("Api", response.body());
    }

    @Override
    public void onRequestFailed(Request request, Exception exception) {

    }
});
```
## 切换域名
```
Api api = new OkApi();
RequestParams params = new RequestParams();
params.addHeader(Api.DOMAIN,"http://47.206.248.71:8006");
api.get(context, "/business/editShelf", params, new OnRequestListener() {
    @Override
    public void onRequestSucceed(Request request, Response response) {
           ApiLog.i("Api", response.body());
    }

    @Override
    public void onRequestFailed(Request request, Exception exception) {

    }
});
```
## 文件上传
```
Api api = new OkApi();
RequestParams params = new RequestParams();
File file = new File("/storeage/xxxx.png")
params.add("file",file);
api.get(context, "/business/editShelf", params, new OnRequestListener() {
    @Override
    public void onRequestSucceed(Request request, Response response) {
           ApiLog.i("Api", response.body());
    }

    @Override
    public void onRequestFailed(Request request, Exception exception) {

    }
});
```
## 文件下载
1.在res/新建xml文件夹，在xml文件夹新建path.xml
```
<?xml version="1.0" encoding="utf-8"?>
<paths>
    <root-path
        name="root"
        path="/storage/emulated/0" />
    <files-path
        name="files"
        path="/storage/emulated/0/Android/data/${applicationId}/files" />
    <cache-path
        name="cache"
        path="/storage/emulated/0/Android/data/${applicationId}/cache" />
    <external-path
        name="Download"
        path="/storage/emulated/0/Android/data/${applicationId}/cache/Download" />
</paths>
```
2.权限，注意6.0以上还需要动态申请权限
```
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />

<application>
    <provider
        android:name="androidx.core.content.FileProvider"
        android:authorities="${applicationId}.fileprovider"
        android:exported="false"
        android:grantUriPermissions="true">
        <meta-data
            android:name="android.support.FILE_PROVIDER_PATHS"
            android:resource="@xml/paths" />
    </provider>
</application>

```
3.下载操作
```
Downloader.Builder builder = new Downloader.Builder(this);
builder.url("http://download.tianditu.com/download/mobile/Tiandituapi3.0.2%2820190103-02%29.zip");
builder.listener(new OnDownloadListener() {
    @Override
    public void onDownloading(long total, long progress) {

    }

    @Override
    public void onDownloadCompleted(File file) {

    }

    @Override
    public void onDownloadFailed(Exception e) {

    }
});
builder.breakpoint(true);
builder.build();
```

