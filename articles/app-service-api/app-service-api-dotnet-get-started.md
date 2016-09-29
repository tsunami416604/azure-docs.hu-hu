<properties
    pageTitle="Az App Service szolgáltatásban elérhető API Apps és az ASP.NET megismerése | Microsoft Azure"
    description="Cikkünkből megtanulhatja, hogyan hozhat létre, telepíthet és használhat fel ASP.NET API-alkalmazásokat az Azure App Service szolgáltatásban a Visual Studio 2015 segítségével."
    services="app-service\api"
    documentationCenter=".net"
    authors="tdykstra"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-api"
    ms.workload="na"
    ms.tgt_pltfrm="dotnet"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/20/2016"
    ms.author="rachelap"/>


# Az Azure App Service szolgáltatásban elérhető API Apps, az ASP.NET és a Swagger használatának megismerése

[AZURE.INCLUDE [selector](../../includes/app-service-api-get-started-selector.md)]

Ez az oktatóanyag egy sorozat része, amelyből megtanulhatja, hogy hogyan segítenek az Azure App Service szolgáltatás funkciói a RESTful API-k fejlesztésében és üzemeltetésében.  Ebben az oktatóanyagban Swagger formátumú API-metaadatok használatát vesszük alapul.

A következőket fogja megtanulni:

* [API Apps](app-service-api-apps-why-best-platform.md) létrehozása és telepítése az Azure App Service szolgáltatásban a Visual Studio 2015 beépített eszközei segítségével.
* Az API-felfedezés automatizálása a Swashbuckle NuGet csomag segítségével és a Swagger API-metaadatok dinamikus generálásával.
* A Swagger API-metaadatok felhasználása az API-alkalmazások ügyfélkódjának automatikus generálására.

## Mintaalkalmazás áttekintése

Ebben az oktatóanyagban egy egyszerű tennivalólista típusú mintaalkalmazáson keresztül mutatjuk be a szolgáltatás funkcióit. Az alkalmazás egyoldalas előtérrel (SPA), ASP.NET Web API középső réteggel, és ASP.NET Web API adatréteggel rendelkezik.

![Az API Apps mintaalkalmazás-diagramja](./media/app-service-api-dotnet-get-started/noauthdiagram.png)

Íme egy képernyőkép az [AngularJS](https://angularjs.org/) előtérrel.

![API Apps mintaalkalmazás, tennivalólista](./media/app-service-api-dotnet-get-started/todospa.png)

A Visual Studio megoldás három projektet tartalmaz:

![](./media/app-service-api-dotnet-get-started/projectsinse.png)

* **ToDoListAngular** – Ez az előtér: az AngularJS SPA, amely behívja a középső réteget.

* **ToDoListAPI** – Ez a középső réteg: egy ASP.NET Web API-projekt, amely behívja az adatréteget CRUD-műveleteknek a tennivalólista elemein való elvégzésére.

* **ToDoListDataAPI** – Ez az adatréteg: egy ASP.NET Web API-projekt, amely elvégzi a CRUD-műveleteket a tennivalólista elemein.

Ez a háromrétegű architektúra csupán egy azok közül, amelyeket az API Apps segítségével megvalósíthat, itt csupán a szolgáltatás bemutatását szolgálja. Az egyes rétegekben a lehető legegyszerűbb kódot használtuk az API Apps funkcióinak demonstrálására. Az adatréteg például adatbázis helyett a kiszolgáló memóriáját használja adatmegőrzési mechanizmusként.

Az oktatóanyag elvégzését követően két Web API-projektje fog futni a felhőben, az App Service API Apps szolgáltatásában.

A sorozat következő oktatóanyagában azt tanulhatja meg, hogyan lehet az SPA-előteret telepíteni a felhőben.

## Előfeltételek

* ASP.NET Web API – Az oktatóanyagban szereplő utasítások feltételezik, hogy Ön legalább alapvető gyakorlattal rendelkezik a Visual Studio program ASP.NET [Web API 2](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) funkciójával való munkavégzés terén.

* Azure-fiók – [Nyisson ingyenes Azure-fiókot](/pricing/free-trial/?WT.mc_id=A261C142F) vagy [használja ki a Visual Studio-előfizetők számára elérhető előnyöket](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

    Ha nem szeretne regisztrálni Azure-fiókot az Azure App Service megismerése előtt, menjen [Az App Service kipróbálása](http://go.microsoft.com/fwlink/?LinkId=523751) oldalra. Itt azonnal létrehozhat egy ideiglenes, kezdő szintű alkalmazást az App Service szolgáltatásban – kötelezettségek vállalása, és **bankkártyaadatok megadása nélkül**.

* Visual Studio 2015 a következővel: [Azure SDK for .NET](https://azure.microsoft.com/downloads/archive-net-downloads/) – Az SDK automatikusan telepíti a Visual Studio 2015-öt, ha nincs telepítve a számítógépen.

    * A Visual Studióban kattintson a Súgó -> A Microsoft Visual Studio névjegye elemre, és győződjön meg arról, hogy az Azure App Service Tools 2.9.1-es vagy újabb verziója van telepítve.

    ![Azure App Tools-verzió](./media/app-service-api-dotnet-get-started/apiversion.png)

    >[AZURE.NOTE] Attól függően, hogy mennyi SDK-függőség van telepítve a számítógépen, az SDK telepítése hosszú időt is igénybe vehet – néhány perctől akár több mint fél óráig is tarthat.

## A mintaalkalmazás letöltése

1. Töltse le a következő tárházat: [Azure-Samples/app-service-api-dotnet-to-do-list](https://github.com/Azure-Samples/app-service-api-dotnet-todo-list).

    Kattintson a **Download ZIP** (ZIP-fájl letöltése) gombra, vagy klónozza a tárházat a helyi számítógépre.

2. Nyissa meg a ToDoList megoldást a Visual Studio 2015-ben vagy 2013-ban.
   1. Mindegyik megoldást megbízhatóként kell megjelölnie.
        ![Biztonsági figyelmeztetés](./media/app-service-api-dotnet-get-started/securitywarning.png)

3. Hozza létre a NuGet-csomagok visszaállítására szolgáló megoldást (CTRL + SHIFT + B).

    Ha telepítés előtt szeretné működés közben megtekinteni az alkalmazást, a helyi számítógépen is futtathatja. Győződjön meg arról, hogy a kezdőprojekt a ToDoListDataAPI, és futtassa a megoldást. Számíthat arra, hogy a böngészőben a HTTP 403-as hiba jelenik meg.

## A Swagger API-metaadatok és felhasználói felület használata

Az Azure App Service beépített támogatást tartalmaz a [Swagger](http://swagger.io/) 2.0 API-metaadatokhoz. Minden API-alkalmazás megadhat egy URL-végpontot, amely Swagger JSON formátumban adja vissza a metaadatokat az API-nak. A végpontról visszaadott metaadatok ezt követően felhasználhatók az ügyfélkód generálásához.

Az ASP.NET Web API-projektek a [Swashbuckle](https://www.nuget.org/packages/Swashbuckle) NuGet-csomag segítségével képesek a Swagger-metaadatok dinamikus generálására. A letöltött ToDoListDataAPI és ToDoListAPI projektek már tartalmazzák a Swashbuckle NuGet csomagot.

Az oktatóanyag ezen részében a generált Swagger 2.0-metaadatokat tekintjük át, és kipróbálunk egy felhasználói felületet, amely a Swagger-metaadatokon alapul.

1. Állítsa be indítási projektként a ToDoListDataAPI projektet (**nem** a ToDoListAPI projektet).

    ![A ToDoDataAPI beállítása kezdőprojektként](./media/app-service-api-dotnet-get-started/startupproject.png)

2. A projekt hibakeresési módban való futtatásához nyomja meg az F5 billentyűt, vagy kattintson a **Debug > Start Debugging** (Hibakeresés > Hibakeresés indítása) elemre.

    Megnyílik a böngésző, és megjelenik a HTTP 403 hibaoldal.

3. A böngésző címsorában adja hozzá a cím végéhez a `swagger/docs/v1` szöveget, majd nyomja le az Enter billentyűt. (Az URL-cím így a következő: `http://localhost:45914/swagger/docs/v1`.)

    Ez az alapértelmezett URL-cím, amelyet a Swashbuckle a Swagger 2.0 JSON-metaadatoknak az API számára való visszaküldésére használ.

    Ha az Internet Explorert használja, a böngésző felszólítja egy *v1.json* nevű fájl letöltésére.

    ![JSON-metaadatok letöltése az Internet Explorerben](./media/app-service-api-dotnet-get-started/iev1json.png)

    Ha Chrome, Firefox vagy Microsoft Edge böngészőt használ, a JSON jelenik meg a böngészőablakban. A különböző böngészők eltérően kezelik a JSON-t, így előfordulhat, hogy az Ön böngészőjében nem pontosan az látható, mint a képen.

    ![JSON-metaadatok a Chrome-ban](./media/app-service-api-dotnet-get-started/chromev1json.png)

    A következő mintában az API számára küldött Swagger-metaadatok első részét láthatja, amely tartalmazza a Get metódus definícióját is. A következő lépéseknél használt Swagger felhasználói felület ezen metaadatok alapján működik. A metaadatokat az oktatóanyag következő részeiben is fel fogjuk használni az ügyfélkód automatikus generálására.

        {
          "swagger": "2.0",
          "info": {
            "version": "v1",
            "title": "ToDoListDataAPI"
          },
          "host": "localhost:45914",
          "schemes": [ "http" ],
          "paths": {
            "/api/ToDoList": {
              "get": {
                "tags": [ "ToDoList" ],
                "operationId": "ToDoList_GetByOwner",
                "consumes": [ ],
                "produces": [ "application/json", "text/json", "application/xml", "text/xml" ],
                "parameters": [
                  {
                    "name": "owner",
                    "in": "query",
                    "required": true,
                    "type": "string"
                  }
                ],
                "responses": {
                  "200": {
                    "description": "OK",
                    "schema": {
                      "type": "array",
                      "items": { "$ref": "#/definitions/ToDoItem" }
                    }
                  }
                },
                "deprecated": false
              },

4. Zárja be a böngészőt, és állítsa le a Visual Studio hibakeresési módját.

5. A **Solution Explorer** (Megoldáskezelő) területén, a ToDoListDataAPI projektben nyissa meg az *App_Start\SwaggerConfig.cs* nevű fájlt, majd görgessen le a következő kódrészig (174. sor), és vonja vissza annak megjegyzéssé tételét.

        /*
            })
        .EnableSwaggerUi(c =>
            {
        */

    Amikor a projekthez telepíti a Swashbuckle csomagot, a rendszer létrehozza a *SwaggerConfig.cs* fájlt. Ez a fájl számos lehetőséget kínál a Swashbuckle konfigurálására.

    A kódrész, amelynek megjegyzéssé tételét visszavonta, lehetővé teszi, hogy a következő lépések során használja a Swagger felhasználói felületet. Amikor az API-alkalmazás projektsablon használatával hoz létre Web API-projektet, a kód biztonsági intézkedésként megjegyzéssé van téve.

6. Futtassa ismét a projektet.

7. A böngésző címsorában adja hozzá a cím végéhez a `swagger` szöveget, majd nyomja le az Enter billentyűt. (Az URL-cím így a következő: `http://localhost:45914/swagger`.)

8. A megjelenő Swagger felhasználói felületben kattintson a **ToDoList** elemre az elérhető metódusok megjelenítéséhez.

    ![Swagger felhasználói felület, elérhető metódusok](./media/app-service-api-dotnet-get-started/methods.png)

9. Kattintson a lista első **Get** (Lekérés) gombjára.

10. A **Parameters** (Paraméterek) mezőnél írjon be egy csillagot az `owner` paraméter értékeként, majd kattintson a **Try it out** (Kipróbálás) elemre.

    Amikor a további oktatóanyagokban hitelesítést állít majd be, a középső réteg biztosítani fogja a tényleges felhasználói azonosítót az adatrétegnek. Az alkalmazás egyelőre hitelesítés nélkül fut, ezért minden feladatnál csillag helyettesítő a tulajdonosazonosítót.

    ![Swagger felhasználói felület, kipróbálás funkció](./media/app-service-api-dotnet-get-started/gettryitout1.png)

    A Swagger felhasználói felület behívja a ToDoList Get metódust, és megjeleníti a válaszkódot és a JSON-eredményeket.

    ![Swagger felhasználói felület, kipróbálás, eredmények](./media/app-service-api-dotnet-get-started/gettryitout.png)

11. Kattintson a **Post** (Közzététel) elemre, majd a **Model Schema** (Modellséma) rész alatti mezőre.

    Ha a modellsémára kattint, a rendszer kitölti a beviteli mezőt, ahol megadhatja a Post metódusra vonatkozó paraméterértékeket. (Ha a funkció nem működik az Internet Explorerben, próbálkozzon másik böngészővel, vagy adja meg manuálisan a paraméterértékeket a következő lépésben.)  

    ![Swagger felhasználói felület, kipróbálás funkció, Post metódus](./media/app-service-api-dotnet-get-started/post.png)

12. Módosítsa a JSON-t a `todo` paraméter beviteli mezőjében úgy, hogy az alábbi példához hasonlítson, vagy adja meg saját tetszőleges leíró szövegét:

        {
          "ID": 2,
          "Description": "buy the dog a toy",
          "Owner": "*"
        }

13. Kattintson a **Try it out** (Kipróbálás) elemre.

    A ToDoList API HTTP 204-es válaszkódot ad vissza, amely a művelet sikerességét jelzi.

14. Kattintson az első **Get** (Lekérés) gombra, majd az oldal ezen részében kattintson a **Try it out** (Kipróbálás) gombra.

    A Get metódusra adott válasz mostantól tartalmazza az új tennivalót is.

15. Nem kötelező: próbálja ki a Put, a Delete és a Get by ID metódust is.

16. Zárja be a böngészőt, és állítsa le a Visual Studio hibakeresési módját.

A Swashbuckle bármelyik ASP.NET Web API-projekttel működik. Ha egy meglévő projektjébe szeretné felvenni a Swagger-metaadatok létrehozását, egyszerűen telepítse a Swashbuckle csomagot.

>[AZURE.NOTE] A Swagger-metaadatokban minden API-művelet saját egyedi azonosítót kap. Alapértelmezés szerint előfordulhat, hogy a Swashbuckle duplikált Swagger-műveleti azonosítókat hoz létre a Web API-vezérlő metódusokhoz. Ez akkor fordul elő, ha a vezérlőben túlterhelt HTTP-metódusok találhatók, például: `Get()` vagy `Get(id)`. A túlterhelések kezeléséről további információt talál a következő cikkben: [Customize Swashbuckle-generated API definitions](app-service-api-dotnet-swashbuckle-customize.md) (A Swashbuckle által generált API-definíciók testreszabása). Ha az Azure API App sablon segítségével hoz lére Web API-projekteket a Visual Studióban, az egyedi műveleti azonosítókat generáló kódot a rendszer automatikusan hozzáadja a *SwaggerConfig.cs* fájlhoz.  

## <a id="createapiapp"></a> API-alkalmazás létrehozása az Azure-ban, majd kód telepítése az alkalmazáshoz

Ebben a részben a Visual Studio **Publish Web** (Weboldal közzététele) varázslójában elérhető integrált Azure-eszközök segítségével hozunk létre egy új API-alkalmazást az Azure-ban. Ezt követően telepítjük a ToDoListDataAPI projektet az új API-alkalmazásra, majd a Swagger felhasználói felület futtatásával meghívjuk az API-t.

1. A **Solution Explorer** (Megoldáskezelő) területén kattintson jobb gombbal a ToDoListDataAPI projektre, majd kattintson a **Publish** (Közzététel) elemre.

    ![A Publish (Közzététel) elem a Visual Studióban](./media/app-service-api-dotnet-get-started/pubinmenu.png)

2.  A **Publish Web** (Weboldal közzététele) varázsló **Profile** (Profil) lépésénél kattintson a **Microsoft Azure App Service** lehetőségre.

    ![Az Azure App Service elem kiválasztása a Publish Web (Weboldal közzététele) varázslóban](./media/app-service-api-dotnet-get-started/selectappservice.png)

3. Ha még nem tette meg, jelentkezzen be Azure-fiókjába, ha pedig a rendszer kijelentkeztette volna, adja meg ismét hitelesítő adatait.

4. Az App Service párbeszédpanelen válassza ki a használni kívánt **Azure-előfizetést**, majd kattintson a **New** (Új) gombra.

    ![A New (Új) elem kiválasztása az App Service párbeszédpanelen](./media/app-service-api-dotnet-get-started/clicknew.png)

    Megjelenik a **Create App Service** (App Service létrehozása) párbeszédpanel **Hosting** (Üzemeltetés) lapja.

    Mivel olyan Web API-projektet telepít, amelyhez telepített Swashbuckle tartozik, a Visual Studio feltételezi, hogy API-alkalmazást szeretne létrehozni. Ezt jelzi az **API App Name** (API-alkalmazás neve) cím, és az, hogy a **Change Type** (Típus módosítása) legördülő lista aktuális értéke **API App** (API-alkalmazás).

    ![Alkalmazás típusának megadása az App Service párbeszédpanelen](./media/app-service-api-dotnet-get-started/apptype.png)

5. Adjon olyan nevet az alkalmazásnak az **API App Name** (API-alkalmazás neve) mezőben, amely még nem létezik az *azurewebsites.net* tartományban. Elfogadhatja a Visual Studio által ajánlott alapértelmezett nevet.

    Ha olyan nevet ad meg, amelyet valaki más már használ, piros felkiáltójel jelenik meg a jobb oldalon.

    Az API-alkalmazás URL-címe a következő lesz: `{API app name}.azurewebsites.net`.

6. A **Resource Group** (Erőforráscsoport) legördülő menüben kattintson a **New** (Új) elemre, majd adja meg a „ToDoListGroup”, vagy más tetszőleges nevet.

    Az erőforráscsoportok Azure-erőforrások (például API Apps, adatbázisok, virtuális gépek stb.) gyűjteményei. Ebben az oktatóanyagban érdemes új erőforráscsoportot létrehozni, mivel így később egyetlen művelettel törölheti az oktatóanyaghoz létrehozott összes Azure-erőforrást.

    Ebben a mezőben kiválaszthat egy meglévő [erőforráscsoportot](../resource-group-overview.md), vagy újat is létrehozhat. Új csoport létrehozásához írjon be egy olyan nevet, amely eltér az előfizetéshez tartozó többi erőforráscsoport nevétől.

7. Kattintson az **App Service Plan** (App Service-csomag) legördülő menü mellett látható **New** (Új) gombra.

    A képernyőképen az **API App Name** (API-alkalmazás neve), a **Subscription** (Előfizetés) és a **Resource Group** (Erőforráscsoport) mezőben látható értékek csupán példaként szolgálnak, a tényleges értékek ezektől eltérőek lesznek.

    ![A Create App Service (App Service létrehozása) párbeszédpanel](./media/app-service-api-dotnet-get-started/createas.png)

    A következő lépésekben létrehozzuk az új erőforráscsoporthoz tartozó App Service-csomagot. Az App Service-csomag határozza meg azokat a számítási erőforrásokat, amelyek segítségével az API-alkalmazás fut. Ha például az ingyenes csomagot választja, az API-alkalmazás megosztott virtuális gépeken fog futni, míg egyes fizetős csomagokban az alkalmazás dedikált virtuális gépeken fut. Az App Service-csomagokkal kapcsolatban további információkért lásd a következő cikket: [App Service plans overview](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) (App Service-csomagok áttekintése).

8. A **Configure App Service Plan** (App Service-csomag konfigurálása) párbeszédpanelen írja be a „ToDoListPlan”, vagy bármilyen más tetszőleges nevet.

9. A **Location** (Hely) legördülő listában válassza ki az Önhöz legközelebb eső helyet.

    Ez a beállítás azt határozza, meg hogy az alkalmazás az Azure melyik adatközpontjában fog futni. Válassza az Önhöz legközelebb eső helyet a [késés](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090) minimalizálása érdekében.

10. A **Méret** legördülő menüben kattintson az **Ingyenes** lehetőségre.

    Ehhez az oktatóanyaghoz az ingyenes szint is megfelelő teljesítményt nyújt.

11. A **Configure App Service Plan** (App Service-csomag konfigurálása) párbeszédpanelen kattintson az **OK** gombra.

    ![Az OK gombra kattintás a Configure App Service Plan (App Service-csomag konfigurálása) párbeszédpanelen](./media/app-service-api-dotnet-get-started/configasp.png)

12. A **Create App Service** (App Service létrehozása) párbeszédpanelen kattintson a **Create** (Létrehozás) gombra.

    ![A Create (Létrehozás) gombra kattintás a Create App Service (App Service létrehozása) párbeszédpanelen](./media/app-service-api-dotnet-get-started/clickcreate.png)

    A Visual Studio létrehozza az API-alkalmazást és egy közzétételi profilt, amely tartalmazza az API-alkalmazáshoz szükséges összes beállítást. Ezt követően megnyitja a **Publish Web** (Weboldal közzététele) varázslót, amely a projekt telepítésére használatos.

    A **Publish Web** (Weboldal közzététele) varázsló a **Connection** (Kapcsolat) lapon nyílik meg (lásd alább).

    A **Connection** (Kapcsolat) lap **Server** (Kiszolgáló) és **Site name** (Hely neve) beállításainak értéke mutat az API-alkalmazásra. A **User name** (Felhasználónév) és a **Password** (Jelszó) mezőben az Azure által az Ön számára létrehozott telepítési hitelesítő adatokat láthatja. A telepítést követően a Visual Studio elindít egy böngészőt, és megnyitja a **Destination URL** (Cél URL-címe) mezőben megadott weblapot (a **Destination URL** (Cél URL-címe) mezőnek ez az egyetlen funkciója).  

13. Kattintson a **Tovább** gombra.

    ![Kattintás a Next (Tovább) gombra a Publish Web (Weboldal közzététele) varázsló Connection (Kapcsolat) lapján](./media/app-service-api-dotnet-get-started/connnext.png)

    A következő lap a **Settings** (Beállítások) (lásd alább). Itt módosíthatja a build konfigurációját, ha hibakeresési buildet szeretne telepíteni [távoli hibakeresés](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug) céljából. Ezen a lapon található a **File Publish Options** (Fájlközzétételi lehetőségek) rész is:

    * Remove additional files at destination (További fájlok eltávolítása a célhelyen)
    * Precompile during publishing (Előfordítás a közzététel során)
    * Exclude files from the App_Data folder (Az App_Data mappában található fájlok kihagyása)

    Ehhez az oktatóanyagokhoz ezek egyikét sem kell használnia. Ezek használatáról a következő cikkben talál részletes leírást: [How to: Deploy a Web Project Using One-Click Publish in Visual Studio](https://msdn.microsoft.com/library/dd465337.aspx) (Útmutató: Webes projekt telepítése a Visual Studio Közzététel egyetlen kattintással funkciójával).

14. Kattintson a **Next** (Tovább) gombra.

    ![Kattintás a Next (Tovább) gombra a Publish Web (Weboldal közzététele) varázsló Settings (Beállítások) lapján](./media/app-service-api-dotnet-get-started/settingsnext.png)

    A **Preview** (Előnézet) lap következik (lásd alább). Itt megtekintheti, hogy a rendszer mely fájlokat fogja másolni a projektből az API-alkalmazásba. Ha olyan API-alkalmazáshoz tartozó projektet telepít, amelyet korábban már telepített, a rendszer kizárólag a módosult fájlokat másolja. A másolt fájlok listájának megtekintéséhez kattintson a **Start Preview** (Előnézet indítása) gombra.

15. Kattintson a **Publish** (Közzététel) gombra.

    ![Kattintás a Publish (Közzététel) gombra a Publish Web (Webes közzététel) varázsló Preview (Előnézet) lapján](./media/app-service-api-dotnet-get-started/clickpublish.png)

    A Visual Studio telepíti a ToDoListDataAPI projektet az új API-alkalmazásba. Az **Output** (Kimenet) ablakban láthatja a sikeres telepítés naplóját. A böngészőben megnyílik a „sikeres létrehozásról” tájékoztató oldal az API-alkalmazásnál megadott URL-címen.

    ![Output (Kimenet) ablak, sikeres telepítés](./media/app-service-api-dotnet-get-started/deploymentoutput.png)

    ![Az új API-alkalmazás sikeres létrehozásáról tájékoztató oldal](./media/app-service-api-dotnet-get-started/appcreated.png)

16. Adja hozzá a „swagger” szót a böngésző címsorában látható URL-címhez, majd nyomja le az Enter billentyűt. (Az URL-cím így a következő: `http://{apiappname}.azurewebsites.net/swagger`.)

    A böngészőben megjelenik a korábban látott Swagger felhasználói felület, azzal a különbséggel, hogy ez most a felhőben fut. Próbálja ki a Get metódust, és tapasztalni fogja, hogy ismét a két alapértelmezett tennivaló jelenik meg. Az elvégzett módosításokat a rendszer a helyi gép memóriájába mentette.

17. Nyissa meg az [Azure portált](https://portal.azure.com/).

    Az Azure portál egy webes felhasználói felület, amely az Azure-erőforrások (például API Apps) kezelésére szolgál.

18. Kattintson a **További szolgáltatások > App Services** elemre.

    ![Tallózás az Alkalmazásszolgáltatások között](./media/app-service-api-dotnet-get-started/browseas.png)

19. Az **App Services** (Alkalmazásszolgáltatások) panelen keresse meg az új API-alkalmazást, majd kattintson rá. (Az Azure-portálon jobb oldalt megnyíló ablakokat *paneleknek* nevezzük.)

    ![Az App Services (Alkalmazásszolgáltatások) panel](./media/app-service-api-dotnet-get-started/choosenewapiappinportal.png)

    Két panel nyílik meg. Az egyik panelen az API-alkalmazások áttekintése látható, míg a másikon számos beállítás, amelyeket megtekinthet, vagy módosíthat.

20. A **Settings** (Beállítások) panelen keresse meg az **API** szakaszt, és kattintson az **API Definition** (API-definíció) elemre.

    ![Az API Definition (API-definíció) elem a Settings (Beállítások) panelen](./media/app-service-api-dotnet-get-started/apidefinsettings.png)

    Az **API Definition** (API-definíció) panelen adhatja meg az URL-címet, amely Swagger 2.0-metaadatokat ad vissza JSON formátumban. Amikor a Visual Studio létrehozza az API-alkalmazást, a korábban látott Swashbuckle által generált metaadatokban található alapértelmezésre állítja az API-definíció URL-címét. Ez az API-alkalmazás alap URL-címe, plusz a következő: `/swagger/docs/v1`.

    ![Az API-definíció URL-címe](./media/app-service-api-dotnet-get-started/apidefurl.png)

    Amikor kiválaszt egy API-alkalmazást, amelyhez ügyfélkódot szeretne generálni, a Visual Studio erről az URL-címről kéri le a metaadatokat.

## <a id="codegen"></a> Ügyfélkód generálása az adatréteg számára

A Swaggernek az Azure API Appsbe való integrálásának egyik legnagyobb előnye az automatikus kódgenerálás. A generált ügyfélosztályok leegyszerűsítik az API-alkalmazásokat behívó kód megírását.

A ToDoListAPI projekt már tartalmazza a generált ügyfélkódot, de a következő lépésekben törölni fogjuk azt, majd ismét generáljuk, hogy megismerkedjen a kódgenerálás módjával.

1. A Visual Studio **Solution Explorer** (Megoldáskezelő) területén, a ToDoListAPI projektben törölje a *ToDoListDataAPI* mappát. **Figyelem: csak a mappát törölje, a ToDoListDataAPI projektet ne!**

    ![Generált ügyfélkód törlése](./media/app-service-api-dotnet-get-started/deletecodegen.png)

    Ezt a mappát a rendszer a kódgenerálási folyamat segítségével hozta létre, amelyen hamarosan Önt is végigvezetjük.

2. Kattintson jobb gombbal a ToDoListAPI projektre, majd kattintson a **Add > REST API Client** (Hozzáadás > REST API-ügyfél) elemre.

    ![REST API-ügyfél hozzáadása a Visual Studióban](./media/app-service-api-dotnet-get-started/codegenmenu.png)

3. Az **Add REST API Client** (REST API-ügyfél hozzáadása) párbeszédpanelen kattintson a **Swagger URL** elemre, majd a **Select Azure Asset** (Azure-objektum kiválasztása) lehetőségre.

    ![Azure-objektum kiválasztása](./media/app-service-api-dotnet-get-started/codegenbrowse.png)

4. Az **App Service** párbeszédpanelen bontsa ki az oktatóanyagban használt erőforráscsoportot, válassza ki az API-alkalmazást, majd kattintson az **OK** gombra.

    ![API-alkalmazás kiválasztása a kódgeneráláshoz](./media/app-service-api-dotnet-get-started/codegenselect.png)

    Az **Add REST API Client** (REST API-ügyfél hozzáadása) párbeszédpanelre visszatérve figyelje meg, hogy a szöveges mezőt a rendszer kitöltötte a portálon korábban látott API-definíció URL-címével.

    ![API-definíció URL-címe](./media/app-service-api-dotnet-get-started/codegenurlplugged.png)

    >[AZURE.TIP] A metaadatok kódgenerálás céljából történő megadásának alternatív módja, ha a tallózási párbeszédpanelen tett lépéseket megkerülve közvetlenül megadja az URL-címet. Ha pedig még az Azure-ba való telepítés előtt szeretné generálni az ügyfélkódot, futtassa helyileg a Web API-projektet, nyissa meg a Swagger JSON-fájlt biztosító URL-címet, mentse a fájlt, majd használja a **Select an existing Swagger metadata file** (Meglévő Swagger-metaadatfájl kiválasztása) lehetőséget.

5. Az **Add REST API Client** (REST API-ügyfél hozzáadása) párbeszédpanelen kattintson az **OK** gombra.

    A Visual Studio létrehoz egy, az API-alkalmazással megegyező nevű mappát, majd elvégzi az ügyfélosztályok generálását.

    ![A generált ügyfélhez tartozó kódfájlok](./media/app-service-api-dotnet-get-started/codegenfiles.png)

6. A ToDoListAPI projektben nyissa meg a *Controllers\ToDoListController.cs* fájlt az API-t a generált ügyfél segítségével behívó kód megtekintéséhez (40. sor).

    Az alábbi kódrészletben láthatja, hogy a kód hogyan példányosítja az ügyfélobjektumot, és hívja be a Get metódust.

        private static ToDoListDataAPI NewDataAPIClient()
        {
            var client = new ToDoListDataAPI(new Uri(ConfigurationManager.AppSettings["toDoListDataAPIURL"]));
            return client;
        }

        public async Task<IEnumerable<ToDoItem>> Get()
        {
            using (var client = NewDataAPIClient())
            {
                var results = await client.ToDoList.GetByOwnerAsync(owner);
                return results.Select(m => new ToDoItem
                {
                    Description = m.Description,
                    ID = (int)m.ID,
                    Owner = m.Owner
                });
            }
        }

    A konstruktorparaméter a `toDoListDataAPIURL` alkalmazásbeállításból kéri le a végpont URL-címét. A Web.config fájlban az API-projekthez tartozó helyi IIS Express URL-cím van beállítva, hogy az alkalmazás a helyi számítógépen is futtatható legyen. Ha kihagyja a konstruktorparamétert, az alapértelmezett végpont az az URL-cím lesz, amelyből a kódot generálta.

7. Az Ön ügyfélosztályát a rendszer az adott API-alkalmazás nevének megfelelő névvel generálja. Módosítsa a *Controllers\ToDoListController.cs* fájlban lévő kódot úgy, hogy a típusnév megfeleljen a projektben generáltnak. Ha például az API-alkalmazás neve ToDoListDataAPI071316, a következő kódot:

        private static ToDoListDataAPI NewDataAPIClient()
        {
            var client = new ToDoListDataAPI(new Uri(ConfigurationManager.AppSettings["toDoListDataAPIURL"]));

módosítsa a következőre:

        private static ToDoListDataAPI071316 NewDataAPIClient()
        {
            var client = new ToDoListDataAPI071316(new Uri(ConfigurationManager.AppSettings["toDoListDataAPIURL"]));


## A középső réteget futtató API-alkalmazás létrehozása

Korábban [létrehozta az API-alkalmazás adatrétegét, és telepítette rá a kódot](#createapiapp).  Most ugyanezt az eljárást kell követni a középső réteg API-alkalmazásánál.

1. A **Solution Explorer** (Megoldáskezelő) területén kattintson a jobb gombbal a középső réteghez tartozó ToDoListAPI projektre (nem az adatréteghez tartozó ToDoListDataAPI-ra), majd kattintson a **Publish** (Közzététel) elemre.

    ![A Publish (Közzététel) elem a Visual Studióban](./media/app-service-api-dotnet-get-started/pubinmenu2.png)

2.  A **Publish Web** (Weboldal közzététele) varázsló **Profile** (Profile) lapján kattintson a **Microsoft Azure App Service** lehetőségre.

3. Az **App Service** párbeszédpanelen kattintson a **New** (Új) gombra.

4. A **Create App Service** (App Service létrehozása) párbeszédpanel **Hosting** (Üzemeltetés) lapján írjon be egy olyan nevet az **API App Name** (API-alkalmazás neve) mezőbe, amely egyedi az *azurewebsites.net* tartományban.

5. Válassza ki a használt Azure-**előfizetést**.

6. A **Resource Group** (Erőforráscsoport) legördülő menüben válassza a korábban létrehozott erőforráscsoportot.

7. Az **App Service Plan** (App Service-csomag) legördülő menüben válassza a korábban létrehozott csomagot. A rendszer alapértelmezésként beírja ezt az értéket.

8. Kattintson a **Create** (Létrehozás) elemre.

    A Visual Studio létrehozza az API-alkalmazást és a hozzá tartozó közzétételi profilt, majd megjeleníti a **Publish Web** (Weboldal közzététele) varázsló  **Connection** (Kapcsolat) lépését.

9.  A **Publish Web** (Weboldal közzététele) varázsló **Connection** (Kapcsolat) lépésénél kattintson a **Publish** (Közzététel) elemre.

    A Visual Studio telepíti a ToDoListAPI projektet az új API-alkalmazásba, és egy böngészőablakban megnyitja az API-alkalmazás URL-címét. Megjelenik a sikeres létrehozásról tájékoztató oldal.

## A középső réteg konfigurálása az adatréteg behívására

Ha most próbálná meg behívni a középső réteg API-alkalmazását, az a Web.config fájlban továbbra is szereplő localhost URL-cím használatával hívná be az adatréteget. Ebben a részben meg kell adnia az adatréteg API-alkalmazásának URL-címét a középső réteg API-alkalmazásának egyik környezeti beállításánál. Ha a középső réteg API-alkalmazásának kódja lekéri az adatréteg URL-beállításait, ez a környezeti beállítás felülírja a Web.config fájlban található értéket.

1. Nyissa meg az [Azure portált](https://portal.azure.com/), majd keresse meg a ToDoListAPI projekt (középső réteg) üzemeltetésére létrehozott API-alkalmazás **API-alkalmazás** paneljét.

2. Az API-alkalmazás **Beállítások** paneljén kattintson az **Alkalmazásbeállítások** elemre.

3. Az API-alkalmazások **Alkalmazásbeállítások** paneljén görgessen le az **Alkalmazásbeállítások** részhez, és vegye fel a következő paramétert és értéket: Az érték az oktatóanyagban elsőként közzétett API-alkalmazás URL-címe lesz.

  	| **Kulcs** | toDoListDataAPIURL |
  	|---|---|
  	| **Érték** | https://{az adatréteghez tartozó API-alkalmazás neve}.azurewebsites.net |
  	| **Példa** | https://todolistdataapi.azurewebsites.net |

4. Kattintson a **Save** (Mentés) gombra.

    ![Kattintás a Mentés gombra az Alkalmazásbeállítások menüben](./media/app-service-api-dotnet-get-started/asinportal.png)

    Amikor a kód lefut az Azure-ban, a rendszer mostantól ezzel az értékkel írja felül a Web.config fájlban található localhost URL-címet.

## Tesztelés

1. Egy böngészőablakban nyissa meg a középső réteg API-alkalmazásának URL-címét, amelyet az előző lépéseknél hozott létre a ToDoListAPI számára. Ehhez úgy juthat el, ha a portálon, az API-alkalmazás főpaneljén rákattint az URL-címre.

2. Adja hozzá a „swagger” szót a böngésző címsorában látható URL-címhez, majd nyomja le az Enter billentyűt. (Az URL-cím így a következő: `http://{apiappname}.azurewebsites.net/swagger`.)

    A böngésző ugyanazt a Swagger felhasználói felületet jeleníti meg, amit korábban a ToDoListDataAPI esetében már láttunk, azzal a különbséggel, hogy most az `owner` mező kitöltése nem kötelező a Get művelethez, mivel a középső réteg API-alkalmazása elküldi Ön helyett ezt az értéket az adatréteg API-alkalmazásának. (A hitelesítési oktatóanyagok elvégzése során a középső réteg valós felhasználói azonosítókat fog küldeni az `owner` paraméternél, ezek helyett most egy fix kódolású csillag szerepel a mezőben.)

3. Próbálja ki a Get metódust és a többi metódust, ezzel ellenőrizve, hogy a középső réteghez tartozó API-alkalmazás sikeresen behívja-e az adatréteg API-alkalmazását.

    ![Swagger felhasználói felület, Get metódus](./media/app-service-api-dotnet-get-started/midtierget.png)

## Hibakeresés

Ha az oktatóanyag lépéseinek elvégzése közben hibákba ütközne, olvassa el az alábbi hibakeresési tippeket:

* Ellenőrizze, hogy az [Azure SDK for .NET](http://go.microsoft.com/fwlink/?linkid=518003) legfrissebb verzióját használja-e.

* Két projekt neve hasonlít egymáshoz (ToDoListAPI, ToDoListDataAPI). Ha valami nem az utasításokban leírtak szerint működik, ellenőrizze, hogy a megfelelő projektet nyitotta-e meg.

* Ha vállalati hálózaton dolgozik, és hálózaton keresztül próbálja meg telepíteni az Azure App Service szolgáltatást, ügyeljen rá, hogy a 443-as és 8172-es portok nyitva álljanak a webes telepítés számára. Ha nem tudja megnyitni ezeket a portokat, használjon más telepítési módszert.  További információk: [Deploy your app to Azure App Service](../app-service-web/web-sites-deploy.md) (Alkalmazás telepítése az Azure App Service szolgáltatásban).

* „Az útvonalneveknek egyedieknek kell lenniük” típusú hibák: ezek akkor léphetnek fel, ha először véletlenül a nem megfelelő, majd a megfelelő projektet telepíti az adott API-alkalmazásba. Ennek javításához telepítse ismételten a megfelelő projektet az API-alkalmazásba, majd a **Publish Web** (Weboldal közzététele) varázsló **Settings** (Beállítások) lapján jelölje be a **Remove additional files at destination** (További fájlok eltávolítása a célhelyen) jelölőnégyzetet.

Ha elérte, hogy az ASP.NET API-alkalmazás fusson az Azure App Service szolgáltatásban, érdemes lehet jobban megismerni a Visual Studio funkcióit, mivel ezek segíthetnek a hibakeresésben. A naplózással, a távoli hibakereséssel és a többi funkcióval kapcsolatban további információkért lásd: [Troubleshooting Azure App Service apps in Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md) (Azure App Service alkalmazások hibakeresése a Visual Studióban).

## Következő lépések

Az előzőekben megtanulta, hogy telepíthet meglévő Web API-projektet az API Appsbe, hogyan generálhat ügyfélkódot az API Appshez, illetve hogyan használhatja fel az API Appset .NET-ügyfelekből. A sorozat következő oktatóanyaga azt mutatja be, hogyan [lehet a CORS segítségével API-alkalmazásokat felhasználni JavaScript-ügyfelekből](app-service-api-cors-consume-javascript.md).

Az ügyfélkód generálásával kapcsolatban lásd a GitHub.com [Azure/AutoRest](https://github.com/azure/autorest) tárházát. A generált ügyféllel kapcsolatos hibákkal kapcsolatban nyisson [problémát az AutoRest tárházban](https://github.com/azure/autorest/issues).

Ha a nulláról szeretne API-alkalmazásprojektet létrehozni, használja az **Azure API App** sablont.

![API App sablon a Visual Studióban](./media/app-service-api-dotnet-get-started/apiapptemplate.png)

Az **Azure API App** projektsablon használatával azt az eredményt éri el, mintha az **Üres** ASP.NET 4.5.2 sablont választaná, majd a jelölőnégyzet bejelölésével hozzáadná a Web API támogatását, és telepítené a Swashbuckle NuGet csomagot. A sablon ezenfelül tartalmaz némi Swashbuckle konfigurációs kódot is, amely megakadályozza a duplikált Swagger műveleti azonosítók létrehozását. Ha létrehozta az API App projektet, azt szintén az oktatóanyagban leírt lépésekkel telepítheti az API-alkalmazásra.



<!--HONumber=Sep16_HO4-->


