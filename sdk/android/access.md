# 1、SMSSDK中用到的权限

- 普通组

```java
	android.permission.ACCESS_NETWORK_STATE
	android.permission.ACCESS_WIFI_STATE
	android.permission.INTERNET
	android.permission.GET_TASKS
```
- 谨慎组

```
    android.permission.READ_PHONE_STATE
	android.permission.READ_SMS
	android.permission.READ_CONTACTS
	android.permission.WRITE_EXTERNAL_STORAGE
```

这些权限一般在Manifest文件中按需加入

# 2、6.0权限

因为6.0权限授权的改变，即使你在Manifest中加入，有些权限依然需要获得用户的手动授权。由于手动授权在SDK中不好做，
它会中断SDK的初始化，因此需要开发者自己实现权限授权。
上面**三个普通组中的只需在manifest文件中加入即可**。谨慎组中的需要**额外授权**。

- 如果你需要智能验证，请授权READ_PHONE_STATE，READ_SMS。
- 如果你需要通讯录功能，请授权READ_CONTACT。
- 如果你需要读取SD卡，请授权WRITE_EXTERNAL_STORAGE

具体授权过程请参考官方教材:
[http://developer.android.com/training/permissions/index.html](http://developer.android.com/training/permissions/index.html)

**如果你实在不想弄这个授权。又需要这些权限。请用22或者以下的SDK编译吧**
