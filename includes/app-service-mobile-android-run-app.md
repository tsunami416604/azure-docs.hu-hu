
1. Látogasson el az [Azure portálra]. Kattintson az **Összes tallózása** > **Mobilalkalmazások** lehetőségre > az imént létrehozott háttérrendszerre. A mobilalkalmazás beállításaiban kattintson az **Első lépések** > **Android** elemre. Az **Ügyfélalkalmazás konfigurálása** menüben kattintson a **Letöltés** elemre. Ez letölti egy olyan alkalmazás teljes Android-projektjét, amely előre konfigurálva van a háttérkészlethez való csatlakozáshoz. 

2. Nyissa meg a projektet az **Android Studio** **Import project (Eclipse ADT, Gradle, etc.)** (Projekt importálása (Eclipse ADT, Gradle stb.)) lehetőségével. A JDK-hibák elkerülése érdekében győződjön meg arról, hogy ez az importálási lehetőség van kiválasztva.

3. Nyomja le a **Run 'app'** („Alkalmazás” futtatása) gombot a projekt felépítéséhez és az alkalmazás Android-szimulátorban történő elindításához.

4. Az alkalmazásban írjon be egy értelmes szöveget, például _Az oktatóanyag befejezése_, majd kattintson a Hozzáadás gombra. Ez egy POST kérést küld a korábban telepített Azure-háttérrendszernek. A háttérrendszer beilleszti az adatokat a kérésből a TodoItem SQL táblázatba, majd visszaküldi az újonnan eltárolt adatokra vonatkozó információkat a mobilalkalmazásnak. A mobilalkalmazás megjeleníti ezeket az adatokat a listában. 

    ![](./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png)

[Azure portálra]: https://portal.azure.com/



<!--HONumber=Jun16_HO2-->


