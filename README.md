# android-reactnative
###原生android项目中增加React native页面。
####
1.先建一个空目录 eg：android-reactnative，在目录下新建一个android 文件夹，将自己原有的Android工程（假如没有就创建一个HelloWorld ）。扔里面。
####
2.创建package.json。方法一：npm init  一路狂点enter，就生成了一个package.json。
                                方法二：自己创建，
```                                
{
  "name": "MyDemo",
  "version": "0.0.1",
  "private": true,
  "scripts": {
    "start": "node node_modules/react-native/local-cli/cli.js start"
  },
  "dependencies": {
    "react": "15.3.1",
    "react-native": "0.32.0",
    "react-native-wechat": "^1.5.5"
  }
}
```
这些参数看一下就明白意思了。
方法三：可以把已有的RN项目中的package.json复制过来。可以自行修改。
注意： 记住这个name的值。下面有用到
####
3.npm  install
####
4.curl -o .flowconfig   https://raw.githubusercontent.com/facebook/react-native/master/.flowconfig
 或者
 复制其他项目中的 .flowconfig 文件
####
5.创建 index.android.js
```
/**
 * Sample React Native App
 * https://github.com/facebook/react-native
 * @flow
 */
 
import React, { Component } from 'react';
import {
  AppRegistry,
  StyleSheet,
  Text,
  View
} from 'react-native';
 
class MyDemo extends Component {
  render() {
    return (
      <View style="{styles.container}">
        <text style="{styles.welcome}">
          Welcome to React Native!
        </text>
        <text style="{styles.instructions}">
          我是原生项目嵌入的 ReactNative
        </text>
        <text style="{styles.instructions}">
          Press Cmd+R to reload,{'\n'}
          Cmd+D or shake for dev menu
        </text>
      </View>
    );
  }
}
 
const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#F5FCFF',
  },
  welcome: {
    fontSize: 20,
    textAlign: 'center',
    margin: 10,
  },
  instructions: {
    textAlign: 'center',
    color: '#333333',
    marginBottom: 5,
  },
});
 
AppRegistry.registerComponent('MyDemo', () => MyDemo );
```
####
6.开始配置上的修改。
 ①android app 下的build.gradle  添加ndk支持.以及依赖包
 ```
defaultConfig {
  。。。

  ndk {
  abiFilters "armeabi-v7a", "x86"
  }
}

dependencies {
 。。。
  compile "com.facebook.react:react-native:+" // From node_modules
}


②android build.gradle下
allprojects {  
    repositories {  
        jcenter()  
        maven {  
            // All of React Native (JS, Obj-C sources, Android binaries) is installed from npm  
            url "$rootDir/../node_modules/react-native/android"  
        }  
    }  
} 
```
③
在gradle.properties中, 添加ndk选项.
```
android.useDeprecatedNdk=true
```
④
AndroidManifest.xml中添加权限，（这个必须有，测试时候一直提示连不上服务器。原来是这个问题，但是纯RN项目里面没看到啊。。。）
<uses-permission android:name="android.permission.INTERNET"></uses-permission>
添加网络设置Activity
<activity android:name="com.facebook.react.devsupport.DevSettingsActivity"/>
⑤添加 MainApplication，直接复制就行，加入原项目中有，那就修改下，实现ReactApplication 。
```
public class MainApplication extends Application implements ReactApplication {

    private final ReactNativeHost mReactNativeHost = new ReactNativeHost(this){

        @Override
        protected boolean getUseDeveloperSupport() {
            return BuildConfig.DEBUG;
        }

        @Override
        protected List<ReactPackage> getPackages() {
            return Arrays.<ReactPackage>asList(
                    new MainReactPackage()
            );
        }
    };

    @Override
    public ReactNativeHost getReactNativeHost() {
        return mReactNativeHost;
    }
}
```
⑥
开始测试了。在主界面跳转到第二个页面。SecondActivity ，在AndroidManifest.xml 添加
<activity android:name=".SecondActivity"></activity>


```
public class SecondActivity extends ReactActivity {


    @Override
    protected String getMainComponentName() {
        return "MyDemo";
    }
}
```
OVER 大功告成
直接react-native run
或者在studio里面运行。
源码

注意

出现:android.app.Application cannot be cast to com.facebook.react.ReactApplication

解决方案:需要将创建的MainApplication在AndroidManifest.xml配置好.
