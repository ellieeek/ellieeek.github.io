---
layout: post
categories: Android
tags: [Android, Kotlin, Navi, Navigation]
title: [Android] 안드로이드 코틀린 Bottom Navigation 만들기
date: 2024-10-06
---

## 0. 미리보기

앱을 구현할 때 Bottom Navigation은 빠질 수 없는 요소이다.
코틀린을 활용하여 하단 내비게이션을 통한 화면 이동 기능을 구현해보자.

아래는 완성할 Bottom Navigation 화면이다. 

assets/android_bottom_navi_fast.gif

## 1. Bottom Navigation이란?
앱 화면 하단에 3-5개의 아이템을 표시하여 사용자가 앱의 주요 기능을 탐색할 수 있게 해주는 UI 컴포넌트를 말한다.

## 2. 구현 준비
### 프로젝트 설정

코드 작성에 들어가기 전,
build.grade 파일에 종속성을 추가해줘야 한다.

```gradle
dependencies {
    implementation("com.google.android.material:material:1.9.0")
}
```

## 3. Fragment 파일 구성
### Fragment별 Kotlin 파일과 XML 파일 생성

하단 탭별로 보여질 Fragment를 구성해야 한다.
필요한 주요 기능에 따라 개수는 달라질 수 있다.<br>
우리는 홈, 채용공고, 채팅, 알림, 마이페이지와 같이 5개의 주요 기능으로 구성하였다.<br>
따라서 5개의 프래그먼트 파일쌍을 구성해주었다.

Fragment (Blank) 로 생성하였고, 지정한 Name에 따라 XML 파일도 같이 생성된다.
![alt text](image-1.png)


## 4. 네비게이션 레이아웃 파일 구성

### 1) res/menu/menu_bottom.xml

우선 res 디렉토리 밑에 menu 디렉토리를 생성한다.<br>
Bottom Navigation의 UI 구성을 위해 res/menu_menu_bottom.xml 파일을 만들어준다.

![image](https://github.com/user-attachments/assets/e7a87759-08c0-41c5-8315-064681cb57fe)


```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item
        android:id="@+id/homeFragment"
        android:title="@string/title_home"
        android:icon="@drawable/ic_home_black_24dp"/>
    <item
        android:id="@+id/recruitFragment"
        android:title="@string/title_recruit"
        android:icon="@drawable/baseline_home_work_24"/>
    <item
        android:id="@+id/chatFragment"
        android:title="@string/title_chat"
        android:icon="@drawable/baseline_chat_bubble_outline_24"/>
    <item
        android:id="@+id/notificationFragment"
        android:title="@string/title_notification"
        android:icon="@drawable/ic_notifications_black_24dp"/>
    <item
        android:id="@+id/myPageFragment"
        android:title="@string/title_mypage"
        android:icon="@drawable/baseline_person_outline_24"/>
</menu>
```

**app:startDestination="@id/기본으로 보여줄 프래그먼트 id"**를 작성하여 기본 프래그먼트를 지정해줄 수 있다.

여기서 title에 활용한 @String/title_home은 res>values 폴더의 string.xml에 지정해주었다.
```xml
    <string name="title_chat">채팅</string>
    <string name="title_home">홈</string>
    <string name="title_recruit">채용공고</string>
    <string name="title_notification">알림</string>
    <string name="title_mypage">마이페이지</string>
```
<br>

Tip)
기본 제공하는 아이콘 사용하기

1) res/drawable/New/Vector Asset을 눌러준다.
![image](https://github.com/user-attachments/assets/8413e130-9ce4-4576-9d0d-8f95b98011b6)

<br>

2) Clip art의 아이콘을 눌러 원하는 아이콘 모양을 선택하여 저장한다.
![image](https://github.com/user-attachments/assets/5c98092e-5ebd-42a5-ada6-f265fd5cf3a3)


<br>

3) android:icon="@drawable/ic_home_black_24dp"과 같이 저장한 icon명을 불러와 사용하면 된다.

<br>

### 2) res/navigation/navi_main.xml

이번에는 res 디렉토리 밑에 navigation 디렉토리를 생성한다.<br>
프래그먼트 간의 화면 전환을 위해 res/navigation/navi_main.xml 파일을 만들어준다.


```xml
<?xml version="1.0" encoding="utf-8"?>
<navigation xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/navi_main"
    app:startDestination="@id/homeFragment">

    <fragment
        android:id="@+id/homeFragment"
        android:name="com.example.yesable.HomeFragment"
        android:label="@string/title_home"
        tools:layout="@layout/fragment_home" />

    <fragment
        android:id="@+id/recruitFragment"
        android:name="com.example.yesable.RecruitFragment"
        android:label="@string/title_recruit"
        tools:layout="@layout/fragment_recruit" />

    <fragment
        android:id="@id/chatFragment"
        android:name="com.example.yesable.ChatFragment"
        android:label="@string/title_chat"
        tools:layout="@layout/fragment_chat" />

    <fragment
        android:id="@+id/notificationFragment"
        android:name="com.example.yesable.NotificationFragment"
        android:label="@string/title_notification"
        tools:layout="@layout/fragment_notification" />

    <fragment
        android:id="@+id/myPageFragment"
        android:name="com.example.yesable.MyPageFragment"
        android:label="@string/title_mypage"
        tools:layout="@layout/fragment_my_page" />
</navigation>
```

## 5. 화면간 전환을 위한 Main_Activiy 구성
### 1) activity_main.xml
Bottom Navigation을 포함한 기본 레이아웃을 구성한다.<br>
각 프래그먼트마다 타이틀바가 동일하게 보이고자 한다면 이 파일에서 toolbar를 통해 구성할 수 있다.
<br> 우리는 Home 탭에서만 로고를 보이고 나머지는 메뉴명을 띄울 거라 각 프래그먼트 XML 파일에서 작성해주었다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".Main_Activity">

    <androidx.fragment.app.FragmentContainerView
        android:id="@+id/fragment_container"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        app:layout_constraintBottom_toTopOf="@id/bottom_navigation_tab"
        app:layout_constraintTop_toTopOf="parent"
        android:name="androidx.navigation.fragment.NavHostFragment"
        app:navGraph="@navigation/navi_main"
        app:defaultNavHost="true" />

    <com.google.android.material.bottomnavigation.BottomNavigationView
        android:id="@+id/bottom_navigation_tab"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginBottom="2dp"
        app:labelVisibilityMode="labeled"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:menu="@menu/menu_bottom" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

### 2) Main_Activity.kt

Bottom Navigation과 Fragment 간의 연동을 구현하기 위해 Kotlin 파일을 작성한다.
setBottomNav 함수를 통해 BottomNavigationView와 NavController를 연결하여, 사용자가 Bottom Navigation에서 탭을 선택할 때 적절한 프래그먼트를 전환해주는 기능을 구현한다.

```kotlin
class Main_Activity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContentView(R.layout.activity_main)
        setBottomNav()
    }

    private fun setBottomNav() {
        val navHostFragment = supportFragmentManager.findFragmentById(R.id.fragment_container) as NavHostFragment
        val navController = navHostFragment.findNavController()
        val bottomNavTab = findViewById<BottomNavigationView>(R.id.bottom_navigation_tab)
        bottomNavTab.setupWithNavController(navController)
    }
}
```

## 6. 결론
이번 포스트에서는 Kotlin을 활용해 Bottom Navigation을 구현하는 방법을 알아보았다.

지금까지의 작업은 뼈대를 구성한 것이므로,
Fragment XML에서 각 메뉴별 UI를 구성하고,
Fragment Kotlin에서 기능 동작을 처리해주면 된다.


### |  Reference
https://it-of-fortune.tistory.com/20
