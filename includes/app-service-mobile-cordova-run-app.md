
1. Látogasson el az [Azure Portalra].
2. Kattintson az **Összes tallózása** > **Mobilalkalmazások** elemre, majd a létrehozott háttérrendszerre.
3. A mobilalkalmazás beállításaiban kattintson az **Első lépések** > **Cordova** elemre.
4. Az **Ügyfélalkalmazás konfigurálása** menüben válassza az **Új alkalmazás létrehozása** lehetőséget, majd kattintson a **Letöltés** gombra.
2. Csomagolja ki a letöltött ZIP-fájlt a merevlemez egy könyvtárába, lépjen a megoldásfájlhoz (.sln), és nyissa meg a Visual Studióval.
3. A Visual Studióban válassza ki a megoldás platformját (Android, iOS vagy Windows) a start nyíl melletti legördülő menüből.. Válassza ki a konkrét eszközt vagy emulátort a zöld nyílon található legördülő menüre kattintva. Használhatja az alapértelmezett Android platformot és a Ripple emulátort. Az összetettebb oktatóanyagok (pl. leküldéses értesítések) esetén egy támogatott eszközt és emulátort kell majd választania.
4. A Cordova alkalmazás létrehozásához és futtatásához nyomja le az F5 billentyűt, vagy kattintson a zöld nyílra. Ha az emulátor egy biztonsági párbeszédablakot jelenít meg, amelyben hozzáférést kér a hálózathoz, fogadja el.
5. Miután az alkalmazás elindult az eszközön vagy az emulátoron, írjon be egy értelmes szöveget az **Új szöveg megadása** mezőbe, például *„Az oktatóanyag befejezése”*, majd kattintson a **Hozzáadás** gombra.

A háttérrendszer beszúrja az adatokat a kérésből az SQL Database-ben található TodoItem táblába, majd visszaküldi az újonnan tárolt elemekre vonatkozó információkat a mobilalkalmazásnak. A mobilalkalmazás megjeleníti ezeket az adatokat a listában.

![](./media/app-service-mobile-cordova-quickstart/quickstart-startup.png)

Más platformok esetén megismételheti a 3–5. lépést.

[Azure Portalra]: https://portal.azure.com/


<!--HONumber=Dec16_HO1-->


