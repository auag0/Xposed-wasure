# Xposed コピー&ペースト

### AndroidManifest.xml
- #### ホーム画面に表示せずに設定ActivityをLSPosedから開けるようにする
```xml
<activity
    android:name="SettingsActivity"
    android:exported="true">
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="de.robv.android.xposed.category.MODULE_SETTINGS" />
    </intent-filter>
</activity> 
```

### XposedHelpers
- #### クラスの取得
```kotlin
val clazz = XposedHelpers.findClass(
    "com.android.systemui.statusbar.policy.Clock",
    classLoader
)
```
- #### 関数の呼び出し
```kotlin
val result = XposedHelpers.callMethod(instance, "sendMessage", "hero")
```
- #### static関数の呼び出し
```kotlin
val result = XposedHelpers.callStaticMethod(clazz, "sendMessage", "hero")
```
- #### 変数の値を取得
```kotlin
val value = XposedHelpers.getObjectField(instance, "isPremium") as Boolean
```
- #### static変数の値を取得
```kotlin
val value = XposedHelpers.getStaticObjectField(instance, "isPremium") as Boolean
```
- #### enumの値を取得
```kotlin
val value = XposedHelpers.getStaticObjectField(clazz, "Premium")
```
### XposedBridge
- #### 指定した関数名の関数を全てhook
```kotlin
XposedBridge.hookAllMethods(clazz, "updateClock", object: XC_MethodHook() {
    override fun beforeHookedMethod(param: MethodHookParam) {

    }

    override fun afterHookedMethod(param: MethodHookParam) {

    }
})
```
- #### 指定したクラスのコンストラクタを全てhook
```kotlin
XposedBridge.hookAllConstructors(clazz, object: XC_MethodHook() {
    override fun beforeHookedMethod(param: MethodHookParam) {

    }

    override fun afterHookedMethod(param: MethodHookParam) {

    }
})
```

### Reflection
- #### 引数を指定してコンストラクタを取得してインスタンスを作成
```kotlin
val constructor = clazz.getConstructor(Drawable::class.java, Integer::class.java)
val instance = constructor.newInstance(drawable, intColor)
```

### XModuleResources
- #### 初期化
```kotlin
class MainHook : IXposedHookZygoteInit, IXposedHookInitPackageResources {
    companion object {
        private lateinit var MODULE_PATH: String
        lateinit var modResource: XModuleResources
    }

    override fun initZygote(startupParam: IXposedHookZygoteInit.StartupParam) {
        MODULE_PATH = startupParam.modulePath
    }

    override fun handleInitPackageResources(resparam: XC_InitPackageResources.InitPackageResourcesParam) {
        modResource = XModuleResources.createInstance(MODULE_PATH, resparam.res)
    }
}
```

### 使えるかなコード
- #### Layoutのリソース名からリソースIDを取得
```kotlin
fun getLayoutId(context: Context, name: String, packageName: String): Int {
    return context.resources.getIdentifier(name, "layout", context.packageName)
}
```
- #### Viewのリソース名からリソースIDを取得
```kotlin
fun getViewId(context: Context, name: String, packageName: String): Int {
    return context.resources.getIdentifier(name, "id", context.packageName)
}
```
