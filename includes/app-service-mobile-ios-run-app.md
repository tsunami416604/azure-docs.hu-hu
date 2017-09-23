
1. A Mac gépen látogasson el az [Azure Portal]. Kattintson az **Összes tallózása** > **Mobilalkalmazások** lehetőségre > az imént létrehozott háttérrendszerre. A mobilalkalmazás beállításaiban kattintson az **Első lépések** > **iOS (Objective-C)** elemre. Ha a Swift nyelvet részesíti előnyben, kattintson az **Első lépések** > **iOS (Swift)** elemre. Az **iOS-projekt letöltése és futtatása** elemnél kattintson a **Letöltés** gombra. Ez letölti egy olyan alkalmazás teljes Xcode-projektjét, amely előre konfigurálva van a háttérrendszerhez való csatlakozáshoz. Nyissa meg a projektet az Xcode használatával.
2. Nyomja le a **Run** (Futtatás) gombot a projekt felépítéséhez és az alkalmazás iOS-szimulátorban történő elindításához.
3. Az alkalmazásban írjon be egy jelentéssel bíró szöveget, például *Az oktatóanyag befejezése*, majd kattintson a plusz (**+**) ikonra. Ez egy POST kérést küld a korábban telepített Azure-háttérrendszernek. A háttérrendszer beilleszti az adatokat a kérésből a TodoItem SQL táblázatba, majd visszaküldi az újonnan eltárolt adatokra vonatkozó információkat a mobilalkalmazásnak. A mobilalkalmazás megjeleníti ezeket az adatokat a listában. 

   ![iOS rendszeren futó gyorsindítási alkalmazás](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure Portal]: https://portal.azure.com/
