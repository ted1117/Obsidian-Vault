
## 프로젝트 생성
1. 안드로이드 스튜디오에서 프로젝트 생성
2. No Activity 선택 후 이름 설정
3. Groovy

## Gradle 설정
### settings.gradle
   ```java
   dependencyResolutionManagement {  
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)  
    repositories {  
        google()  
        mavenCentral()  
        jcenter()  
    }  
}
```

### build.gradle(app)
```java
dependencies {  
    // Xposed Framework API dependencies  
    compileOnly 'de.robv.android.xposed:api:82'  
  
    implementation libs.appcompat  
    implementation libs.material  
    testImplementation libs.junit  
    androidTestImplementation libs.ext.junit  
    androidTestImplementation libs.espresso.core  
}
```

## Manifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>  
<manifest xmlns:android="http://schemas.android.com/apk/res/android"  
    xmlns:tools="http://schemas.android.com/tools">  
  
    <application        android:allowBackup="true"  
        android:dataExtractionRules="@xml/data_extraction_rules"  
        android:fullBackupContent="@xml/backup_rules"  
        android:icon="@mipmap/ic_launcher"  
        android:label="@string/app_name"  
        android:roundIcon="@mipmap/ic_launcher_round"  
        android:supportsRtl="true"  
        android:theme="@style/Theme.MyFirstXposedModule"  
        tools:targetApi="31">  
  
        <meta-data            android:name="xposedmodule"  
            android:value="true" />  
        <meta-data            android:name="xposeddescription"  
            android:value="An example which changes the color of clock text in status bar" />  
        <meta-data            android:name="xposedminversion"  
            android:value="53" />  
    </application>  
</manifest>
```
**application 태그 안에**  메타 데이터 추가  

## xposed_init
app > main 안에 assets 폴더 생성 후 xposed_init 파일 생성  
```
(패키지 이름).(클래스 이름)
ex) com.myfirstxposedmodule.MyModule
```

## Java 파일 생성
```java
package com.myfirstxposedmodule;  
  
import de.robv.android.xposed.IXposedHookLoadPackage;  
import de.robv.android.xposed.callbacks.XC_LoadPackage;  
import de.robv.android.xposed.XposedBridge;  
public class MyModule implements IXposedHookLoadPackage {  
  
    @Override  
    public void handleLoadPackage(XC_LoadPackage.LoadPackageParam lpparam) throws Throwable {  
          
    }  
}
```

## Edit Configure
Run > Edit Configure 탭에서 Launch Options 아래에 Default Activity 말고 Nothing 선택  

## 참고링크
https://medium.com/@meetvora1994/develop-your-first-xposed-module-386c7b0335a2