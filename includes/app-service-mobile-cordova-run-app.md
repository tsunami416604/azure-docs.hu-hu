
1. Látogasson el az [Azure Portal]. Kattintson az **Összes tallózása** > **Mobilalkalmazások** lehetőségre > az imént létrehozott háttérrendszerre. A mobilalkalmazás beállításaiban kattintson az **Első lépések** > **Cordova** elemre. Az **Ügyfélalkalmazás konfigurálása** menüben válassza az **Új alkalmazás létrehozása** lehetőséget, majd kattintson a **Letöltés** gombra. Ez letölti egy olyan alkalmazás teljes Cordova-projektjét, amely előre konfigurálva van a háttérrendszerhez való csatlakozáshoz.

2. Csomagolja ki a letöltött ZIP-fájlt a merevlemez egy könyvtárába, lépjen a megoldásfájlhoz (.sln), és nyissa meg a Visual Studióval.

5. A Visual Studióban válassza ki a megoldás platformját (Android, iOS vagy Windows) a start nyíl melletti legördülő menüből, majd válassza ki a konkrét üzemelő eszközt vagy emulátort a zöld nyílon található legördülő menüre kattintva. Használhatja az alapértelmezett Android platformot és Ripple emulátort. Az összetettebb oktatóanyagok esetén egy támogatott eszközt és emulátort kell majd választania. 

6. Nyomja le az F5 billentyűt vagy kattintson a zöld nyílra a Cordova-alkalmazás létrehozásához és futtatásához. Ha az emulátor egy biztonsági párbeszédablakot jelenít meg, amelyben hozzáférést kér a hálózathoz, fogadja el.   

7. Miután az alkalmazás elindult az eszközön vagy az emulátoron, írjon be egy értelmes szöveget az **Új szöveg megadása** mezőbe, például _Az oktatóanyag befejezése_, majd kattintson a **Hozzáadás** gombra.  
Ez egy POST kérést küld a korábban telepített Azure-háttérrendszernek. A háttérrendszer beilleszti az adatokat a kérésből az SQL Database-ben található TodoItem táblába, majd visszaküldi az újonnan eltárolt elemekre vonatkozó információkat a mobilalkalmazásnak. A mobilalkalmazás megjeleníti ezeket az adatokat a listában.

    ![](./media/app-service-mobile-cordova-quickstart/quickstart-startup.png)
    
8. Ismételje meg az előző három lépést minden támogatni kívánt eszközplatformra vonatkozóan.

[Azure Portal]: https://portal.azure.com/



<!--HONumber=sep16_HO1-->


