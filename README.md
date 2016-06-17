# CacheAll

本来来自：https://github.com/yangfuhai/ASimpleCache.git

ASimpleCache 是一个为android制定的 轻量级的 开源缓存框架。轻量到只有一个java文件（由十几个类精简而来）。

1、可以缓存String、JsonObject、JsonArray、Bitmap、Drawable、序列化的java对象，和 byte数据。


2、它有什么特色？

特色主要是：
1：轻，轻到只有一个JAVA文件。
2：可配置，可以配置缓存路径，缓存大小，缓存数量等。
3：可以设置缓存超时时间，缓存超时自动失效，并被删除。
4：支持多进程。
3、它在android中可以用在哪些场景？

1、替换SharePreference当做配置文件
2、可以缓存网络请求数据，比如oschina的android客户端可以缓存http请求的新闻内容，缓存时间假设为1个小时，超时后自动失效，让客户端重新请求新的数据，减少客户端流量，同时减少服务器并发量。
3、您来说...
4、如何使用 ASimpleCache？

以下有个小的demo，希望您能喜欢：

ACache mCache = ACache.get(this);
mCache.put("test_key1", "test value");
mCache.put("test_key2", "test value", 10);//保存10秒，如果超过10秒去获取这个key，将为null
mCache.put("test_key3", "test value", 2 * ACache.TIME_DAY);//保存两天，如果超过两天去获取这个key，将为null
获取数据

ACache mCache = ACache.get(this);
String value = mCache.getAsString("test_key1");


具体详细用法如下：

//获取缓存对象
ACache mCache = ACache.get(this);

mCache.remove(CACHE_KEY);//清除缓存
put(String key, Obeject value, int saveTime);//设置缓存时间


1.存取普通字符串

mCache.put("testString", mEt_string_input.getText().toString());

String testString = mCache.getAsString("testString");
	
	
2.存取Json对象

JSONObject jsonObject = new JSONObject();

jsonObject.put("name", "Yoson");
jsonObject.put("age", 18);
		

mCache.put("testJsonObject", jsonObject);

JSONObject testJsonObject = mCache.getAsJSONObject("testJsonObject");

3.存取Json数组
JSONArray jsonArray = new JSONArray();

JSONObject yosonJsonObject = new JSONObject();
jsonObject.put("name", "Yoson");
jsonObject.put("age", 18);

JSONObject michaelJsonObject = new JSONObject();
michaelJsonObject.put("name", "Michael");
michaelJsonObject.put("age", 25);

jsonArray.put(yosonJsonObject);
jsonArray.put(michaelJsonObject);

mCache.put("testJsonArray", jsonArray);//存储json数组
JSONArray testJsonArray = mCache.getAsJSONArray("testJsonArray");//读取json数组


4.存取bitmao对象

Bitmap bitmap = BitmapFactory.decodeResource(getResources(), R.drawable.img_test);

mCache.put("testBitmap", bitmap);
Bitmap testBitmap = mCache.getAsBitmap("testBitmap");

5.存取drawable对象

Drawable drawable = getResources().getDrawable(R.drawable.img_test);

mCache.put("testDrawable", drawable);
Drawable testDrawable = mCache.getAsDrawable("testDrawable");

6.存取一般序列化的java对象

UserBean userBean = new UserBean();
userBean.setAge("18");
userBean.setName("HaoYoucai");

mCache.put("testObject", userBean);
UserBean testObject = (UserBean) mCache.getAsObject("testObject");



7.存取媒体信息 需要异步处理

//保存
@Override
    public void run() {
        OutputStream ostream = null;
        try {
            ostream = mCache.put(CACHE_KEY);
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        }
        if (ostream == null){
            Toast.makeText(this, "Open stream error!", Toast.LENGTH_SHORT)
                    .show();
            return;
        }
        try {
            URL u = new URL(mUrl);
            HttpURLConnection conn = (HttpURLConnection) u.openConnection();
            conn.connect();
            InputStream stream = conn.getInputStream();

            byte[] buff = new byte[1024];
            int counter;

            while ((counter = stream.read(buff)) > 0){
                ostream.write(buff, 0, counter);
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                // cache update
                ostream.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
            runOnUiThread(new Runnable() {
                @Override
                public void run() {
                    text = (TextView) findViewById(R.id.text);
                    text.setText("done...");
                }
            });
        }
    }


//读取
public void read(View v) {
        InputStream stream = null;
        try {
            stream = mCache.get(CACHE_KEY);
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        }
        if (stream == null) {
			Toast.makeText(this, "Bitmap cache is null ...", Toast.LENGTH_SHORT)
					.show();
            text.setText("file not found");
			return;
		}
        try {
            text.setText("file size: " + stream.available());
        } catch (IOException e) {
            text.setText("error " + e.getMessage());
        }
    }
