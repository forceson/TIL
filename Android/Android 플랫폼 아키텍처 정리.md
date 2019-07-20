# Android 플랫폼 아키텍처 정리

Created: Jul 20, 2019 5:52 PM

Android는 다양한 기기와 폼 팩터에 사용할 수 있도록 제작된 Linux 기반의 오픈소스 소프트웨어 스택이다.

![](https://developer.android.com/guide/platform/images/android-stack_2x.png)

## Linux 커널

- ART는 스레딩 및 하위 수준의 메모리 관리와 같은 기본 기능에 Linux 커널 사용.
- 주요 기능을 활용하고 기기 제조업차가 널리 알려진 커널용 하드웨어 드라이버를 개발할 수 있음.

## HAL

- 상위수준의 Java API Framework에 기기 하드웨어 기능을 노출하는 표준 인터페이스 제공.
- Framework API가 기기 하드웨어에 액세스하기 위해 호출을 수행하면 Android 시스템이 해당 하드웨어 구성요소에 대한 라이브러리 모듈을 로드.

## Android Runtime

- 안드로이드 버전 5.0(API Level 21) 이상을 실행하는 기기의 경우, 각 앱이 자체 프로세스 내에서 자체 ART 인스턴스로 실행된다. ART는 DEX파일을 실행하여 저용량 메모리 기기에서 여러 가상 머신을 실행하도록 작성되었따.
- DEX 파일은 Android 용으로 특별히 설계된 바이트코드 형식으로, 최소 메모리 공간에 맞게 최적화됨.
- Jack과 같은 ToolChain을 빌드하고, Java 소스를 Android 플랫폼에서 실행될 수 있는 DEX 바이트코드로 컴파일.
- ART - AOT (Ahead of time) 및 JIT(Just In Time) 컴파일, 최적화된 GC, 전용 샘플링 프로파일러, 상세진단 예외 및 크래시 보고, Watch Pointer를 설정하여 특정 필드를 모니터링 할 수 있는 기능을 비롯한 향상된 디버깅 기능 지원 가능.
- 안드로이드 버전 5.0 이전 버전에서는 Dalvik이 Android 런타임.
- Java API Framework이 사용하는 몇가지 Java 8 기능 포함하여 대부분의 Java 기능을 제공하는 핵심 런타임 라이브러리도 포함됨.

## Native C/C++ Libraries

- ART 및 HAL 등의 핵심 Android 시스템 구성 요소와 서비스가 C 및 C++로 작성된 네이티브 라이브러리를 필요로하는 네이티브 코드를 기반으로 빌드됨.
- Android는 Java API Framework를 제공하여 이러한 일부 네이티브 라이브러리 기능을 앱에 노출. ex) Java OpenGL API → OpenGL ES, 2D 3D 그래픽, 조작
- C/C++ 코드가 필요한 앱을 개발하는 경우, Android NDK를 사용하여 네이티브 코드에서 직접 몇몇 네이티브 플랫폼 라이브러리에 액세스 할 수 있음.

## Java API Framework

- Android OS 전체 기능 세트는 Java 언어로 작성된 API를 통해 액세스 할 수 있다.
- 이러한 API는 핵심 모듈식 시스템 구성요소 및 서비스 재활용을 단순화하여 Android 앱을 제작하는데 필요한 빌딩블록을 구성하며 빌딩블록에는 다음이 포함됨.
    - 기능이 풍부하며 확장가능한 뷰 시스템 - 목록, 그리드, 텍스트박스, 버튼 및 삽입가능한 웹브라우저 포함. UI 빌드에 사용
    - Resource Manager - 현지화된 문자열, 그래픽 및 레이아웃 파일과 같은 코드가 아닌 리소스에 대한 액세스 제공
    - Notificaion Manager - 모든 앱이 상태 표지술에 사용자 지정 알람을 표시할 수 있도록 지원
    - Activity Manager - 앱의 수명주기 관리 + 공통 탐색 백스택 제공
    - Content Provider - 앱이 주소록 앱과 같은 다른 앱의 데이터에 액세스하거나 자신의 데이터 공유할 수 있도록 지원

## 시스템 앱

- Android는 이메일, SMS, 캘린더, 인터넷, 주소록 등의 주요 앱세트와 함께 제공됨
- 사용자를 위한 앱으로도 작동하고 개발자가 자신의 앱에서 액세스 할 수 있는 주요기능을 제공하기 위한 용도로도 작동