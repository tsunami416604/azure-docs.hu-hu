Folyamatos fejlesztését, mert az Android SDK-verzió az Android Studio telepített nem egyeznek meg a kódot a verzióját. Az Android SDK ebben az oktatóanyagban hivatkozott verziója 23, a legújabb írásának időpontjában. A verziószám megnövelheti az SDK-t új kiadásaiban jelenik meg, és azt javasoljuk, az elérhető legújabb verzióra.

Verzióütközés két tünetei a következők:

- Build, illetve a projekt újraépítéséhez kaphat például a gradle-lel hibaüzenetek "**nem található a cél Google Inc.:Google APIs:n**".
- Oldja fel az kódban szabványos Android objektumok alapján `import` előfordulhat, hogy utasítások generálása hibaüzenetek.

Mindkét jelenik meg, ha a előfordulhat, hogy az Android SDK az Android Studio telepített verziója nem egyezik a letöltött projektből SDK célját. A verzió ellenőrzéséhez a következő módosításokat:

1. Az Android Studióban kattintson **eszközök** > **Android** > **SDK Manager**. Ha nem telepítette az SDK Platform legújabb verzióját, majd kattintson a telepítésre. Jegyezze fel a verziószám.
2. Az a **Project Explorer** lap **Gradle parancsfájlok**, nyissa meg a fájlt **build.gradle (modeule: alkalmazás)**. Győződjön meg arról, hogy a **compileSdkVersion** és **buildToolsVersion** vannak beállítva, hogy a legfrissebb SDK-verzió van telepítve. A címkék nézhet ki:

             compileSdkVersion 'Google Inc.:Google APIs:23'
            buildToolsVersion "23.0.2"
3. Az Android Studio Project Explorer kattintson a jobb gombbal a projektcsomópontra, válassza a **tulajdonságok**, és válassza a bal oldali oszlopban **Android**. Győződjön meg arról, hogy a **Project Build Target** verziójával megegyező SDK verzióra van beállítva a **targetSdkVersion**.

Az Android Studióban az Alkalmazásjegyzék-fájl már nem szolgál adja meg a cél SDK és a minimális SDK-verzió, eltérően az esetet, amelyben az eclipse-ben.
