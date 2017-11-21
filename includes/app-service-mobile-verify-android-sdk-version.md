Folyamatos fejlesztését, mert az Android SDK-verzió az Android Studio telepített nem egyeznek meg a kódot a verzióját. Az Android SDK ebben az oktatóanyagban hivatkozott verziója 26, a legújabb írásának időpontjában. A verziószám megnövelheti az SDK-t új kiadásaiban jelenik meg, és azt javasoljuk, az elérhető legújabb verzióra.

Verzióütközés két tünetei a következők:

- Build, illetve a projekt újraépítéséhez kaphat például a gradle-lel hibaüzenetek `Gradle sync failed: Failed to find target with hash string 'android-XX'`.
- Oldja fel az kódban szabványos Android objektumok alapján `import` előfordulhat, hogy utasítások generálása hibaüzenetek.

Mindkét jelenik meg, ha a előfordulhat, hogy az Android SDK az Android Studio telepített verziója nem egyezik a letöltött projektből SDK célját. A verzió ellenőrzéséhez a következő módosításokat:

1. Az Android Studióban kattintson **eszközök** > **Android** > **SDK Manager**. Ha nem telepítette az SDK Platform legújabb verzióját, majd kattintson a telepítésre. Jegyezze fel a verziószám.

2. Az a **Project Explorer** lap **Gradle parancsfájlok**, nyissa meg a fájlt **build.gradle (modul: alkalmazás)**. Győződjön meg arról, hogy a **compileSdkVersion** és **targetSdkVersion** vannak beállítva, hogy a legfrissebb SDK-verzió van telepítve. A `build.gradle` nézhet ki:

    ```gradle
    android {
        compileSdkVersion 26
        defaultConfig {
            targetSdkVersion 26
        }
    }
    ```
