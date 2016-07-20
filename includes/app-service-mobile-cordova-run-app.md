
1. Látogasson el az [Azure portálra]. Kattintson az **Összes tallózása** > **Mobilalkalmazások** lehetőségre > az imént létrehozott háttérrendszerre. A mobilalkalmazás beállításaiban kattintson az **Első lépések** > **Cordova** elemre. Az **Ügyfélalkalmazás konfigurálása** menüben válassza az **Új alkalmazás létrehozása** lehetőséget, majd kattintson a **Letöltés** gombra. Ez letölti egy olyan alkalmazás teljes Cordova-projektjét, amely előre konfigurálva van a háttérrendszerhez való csatlakozáshoz.

2. Csomagolja ki a letöltött ZIP-fájlt a merevlemez egy könyvtárába.

3. Nyissa meg a projektet a **Visual Studio** alkalmazással.  Kattintson az **Open** (Megnyitás) > **Project/Solution...** (Projekt/Megoldás...) lehetőségre.

4. Keresse meg a _sitename_.sln nevű fájlt, majd kattintson az **Open** (Megnyitás) gombra.

5. Az alapértelmezett emulátor a **Ripple - Nexus (Galaxy)**.  Kattintson az emulátor melletti legördülő nyílra, majd válassza ki a **Google Android Emulator** elemet.

6. Kattintson a **Google Android Emulator** elemre.  A rendszer felépíti és futtatja a projektet.  Előfordulhat, hogy megjelenik egy hálózatbiztonsági figyelmeztetés, amelyben a Google Android Emulator hozzáférést kér a hálózathoz.  Végül a Google Android Emulator megjelenik, és lefuttatja az alkalmazást.

7. Az alkalmazásban írjon be egy értelmes szöveget, például _Az oktatóanyag befejezése_, majd kattintson a Hozzáadás gombra. Ez egy POST kérést küld a korábban telepített Azure-háttérrendszernek. A háttérrendszer beilleszti az adatokat a kérésből a TodoItem SQL táblázatba, majd visszaküldi az újonnan eltárolt adatokra vonatkozó információkat a mobilalkalmazásnak. A mobilalkalmazás megjeleníti ezeket az adatokat a listában.

    ![](./media/app-service-mobile-cordova-quickstart/quickstart-startup.png)

[Azure portálra]: https://portal.azure.com/



<!--HONumber=Jun16_HO2-->


