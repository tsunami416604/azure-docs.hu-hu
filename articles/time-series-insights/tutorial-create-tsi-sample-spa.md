---
title: 'Oktatóanyag: Azure Time Series Insights egyoldalas Webalkalmazás létrehozása | Microsoft Docs'
description: Megtudhatja, hogyan hozhat létre egy egyoldalas webalkalmazást, amely Azure Time Series Insights-környezetből származó adatok lekérdezését és megjelenítését teszi lehetővé.
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: dpalled
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: 031e8074585426584d7ef63a103c9c2b4d90e6c3
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70194215"
---
# <a name="tutorial-create-an-azure-time-series-insights-single-page-web-app"></a>Oktatóanyag: Azure Time Series Insights egyoldalas webalkalmazás létrehozása

Ez az oktatóanyag végigvezeti Önt a saját egyoldalas webalkalmazások (SPA) létrehozásának folyamatán, amely a Azure Time Series Insights adataihoz való hozzáférést teszi elérhetővé.

Ezen oktatóanyag segítségével elsajátíthatja a következőket:

> [!div class="checklist"]
> * Az alkalmazás kialakítása
> * Az alkalmazás regisztrálása a Azure Active Directory (Azure AD) használatával
> * A webalkalmazás létrehozása, közzététele és tesztelése

> [!NOTE]
> * Az oktatóanyag forráskódját a [githubon](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial)kell megadnia.
> * Az Time Series Insights [ügyfél-minta alkalmazás](https://insights.timeseries.azure.com/clientsample) az oktatóanyagban használt befejezett alkalmazás megjelenítésére szolgál.

Ha még nem rendelkezik ilyennel, regisztráljon egy [ingyenes Azure](https://azure.microsoft.com/free/) -előfizetésre.

## <a name="prerequisites"></a>Előfeltételek

* A Visual Studio ingyenes példánya. A kezdéshez töltse le a [2017-es vagy a 2019-es közösségi verziót](https://www.visualstudio.com/downloads/) .

* A Visual studióhoz készült IIS Express, web Deploy és Azure Cloud Services Core Tools összetevő. Adja hozzá az összetevőket a Visual Studio telepítésének módosításával.

## <a name="understand-application-design"></a>Az alkalmazás kialakításának megismerése

A Time Series Insights minta SPA az oktatóanyagban használt terv és kód alapja. A kód a Time Series Insights JavaScript ügyféloldali kódtárat használja. A Time Series Insights ügyféloldali kódtár két fő API-kategóriához biztosít absztrakciót:

- **A Time Series Insights lekérdezési API**-k meghívására szolgáló burkoló metódusok: REST API-k JSON-alapú kifejezések használatával Time Series Insights adatok lekérdezésére használható. A metódusok a könyvtár TsiClient. Server névterében vannak rendszerezve.

- **Különféle típusú diagramok létrehozásának és feltöltésének módszerei**: A weblapon lévő Time Series Insights-adatmegjelenítéshez használható metódusok. A metódusok a könyvtár TsiClient. UX névterében vannak rendszerezve.

Ez az oktatóanyag a minta alkalmazás Time Series Insights környezetének adatait is használja. A Time Series Insights minta alkalmazás struktúrájával és a Time Series Insights ügyféloldali függvénytárával kapcsolatos részletekért tekintse meg az oktatóanyag [a Azure Time Series Insights JavaScript-ügyfél függvénytárában](tutorial-explore-js-client-lib.md)című témakört.

## <a name="register-with-azure-ad"></a>Regisztrálás az Azure AD-vel

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

## <a name="build-and-publish"></a>Létrehozás és közzététel

1. Hozzon létre egy könyvtárat az alkalmazás projektfájljainak tárolásához. Ezután nyissa meg az alábbi URL-címeket. Kattintson a jobb gombbal a **nyers** hivatkozásra a lap jobb felső sarkában, majd válassza a **Mentés másként** lehetőséget a fájlok a projekt könyvtárába való mentéséhez.

   - [*index. html*](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html): az oldal HTML-és JavaScript-kódja
   - [*sampleStyles. css*](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/sampleStyles.css): a CSS-stíluslap

   > [!NOTE]
   > A böngészőtől függően előfordulhat, hogy a fájl mentése előtt módosítania kell a kiterjesztéseket. html vagy. CSS fájlra.

1. Ellenőrizze, hogy a szükséges összetevők telepítve vannak-e a Visual Studióban. Telepíteni kell a IIS Express, a web Deploy és az Azure Cloud Services Core Tools összetevőt a Visual studióhoz.

    [![Visual Studio – a telepített összetevők módosítása](media/tutorial-create-tsi-sample-spa/vs-installation.png)](media/tutorial-create-tsi-sample-spa/vs-installation.png#lightbox)

    > [!NOTE]
    > A Visual Studio-élmény a verziótól és a konfigurációs beállításoktól függően némileg eltérő lehet a látható példáktól.

1. Nyissa meg a Visual studiót, és jelentkezzen be. A webalkalmazáshoz tartozó projekt létrehozásához a **fájl** menüben válassza a webhely **megnyitása** > lehetőséget.

    [![Visual Studio – új megoldás létrehozása](media/tutorial-create-tsi-sample-spa/vs-solution-create.png)](media/tutorial-create-tsi-sample-spa/vs-solution-create.png#lightbox)

1. A webhely **megnyitása** ablaktáblán válassza ki azt a munkakönyvtárat, AHOVÁ a HTML-és CSS-fájlokat tárolta, majd kattintson a **Megnyitás**gombra.

   [![Visual Studio – a Fájl menü a Megnyitás és a webhely beállításaival](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png)](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png#lightbox)

1. A Visual Studio **nézet** menüben válassza a **megoldáskezelő**lehetőséget. Megnyílik az új megoldás. Tartalmaz egy webhely-projektet (Globe ikon), amely tartalmazza a HTML-és CSS-fájlokat.

   [![Visual Studio – az új megoldás a Megoldáskezelő](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png#lightbox)

1. Az alkalmazás közzététele előtt meg kell változtatnia az *index. html*konfigurációs beállításait.

   1. A Megjegyzés `"PROD RESOURCE LINKS"` alá tartozó három sor megjegyzésének visszahelyezése a függőségek fejlesztésből éles környezetbe való váltásához. Tegye megjegyzésbe a megjegyzésben `"DEV RESOURCE LINKS"`szereplő három sort.

      [!code-html[head-sample](~/samples-javascript/pages/tutorial/index.html?range=2-20&highlight=10-13,15-18)]

      A függőségeket a következő példához hasonló módon kell megadnia:

      ```HTML
      <!-- PROD RESOURCE LINKS -->
      <link rel="stylesheet" type="text/css" href="./sampleStyles.css">
      <script src="https://unpkg.com/tsiclient@1.1.4/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="https://unpkg.com/tsiclient@1.1.4/tsiclient.css">

      <!-- DEV RESOURCE LINKS -->
      <!-- <link rel="stylesheet" type="text/css" href="./sampleStyles.css">
      <script src="../../dist/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="../../dist/tsiclient.css"> -->
      ```

   1. Ha úgy szeretné konfigurálni az alkalmazást, hogy az Azure ad-alkalmazás regisztrációs azonosítóját használja, `clientID` módosítsa az értéket a 3. **lépésben** átmásolt alkalmazás- **azonosító** használatára, ha [az alkalmazást az Azure ad használatára regisztrálta](#register-with-azure-ad). Ha létrehozott egy kijelentkezési **URL-címet** az Azure ad-ben, állítsa `postLogoutRedirectUri` az értéket értékként.

      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-153&highlight=4-5)]

      Példa:

      ```javascript
      clientId: '8884d4ca-b9e7-403a-bd8a-366d0ce0d460',
      postLogoutRedirectUri: 'https://tsispaapp.azurewebsites.net',
      ```

   1. A módosítások végrehajtása után mentse az *index. html*fájlt.

1. Tegye közzé a webalkalmazást az Azure-előfizetésében Azure App Serviceként.  

   > [!NOTE]
   > Az alábbi lépésekben látható képernyőképek számos lehetőségét automatikusan feltölti az Azure-előfizetésből származó adatokkal. Az egyes ablaktáblák teljes betöltése eltarthat néhány másodpercig.  

   1. Megoldáskezelő kattintson a jobb gombbal a webhely-projekt csomópontra, majd válassza a **webalkalmazás közzététele**lehetőséget.  

      [![Visual Studio – válassza a webalkalmazás-közzététel Megoldáskezelő lehetőséget](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png#lightbox)

   1. Válassza az **Indítás** lehetőséget az alkalmazás közzétételének megkezdéséhez.

      [![Visual Studio – a profil közzététele panel](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png#lightbox)

   1. Válassza ki az új Azure App Service példány közzétételét, vagy használjon egy meglévőt.

      [![Azure App Service példány kiválasztása](media/tutorial-create-tsi-sample-spa/vs-publish-select-target.png)](media/tutorial-create-tsi-sample-spa/vs-publish-select-target.png#lightbox)

   1. Válassza ki az alkalmazás közzétételéhez használni kívánt előfizetést. Válassza ki a **TsiSpaApp** projektet. Ezután válassza az **OK** lehetőséget.

      [![Visual Studio – a profil közzététele App Service panel](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png#lightbox)

   1. A webalkalmazás telepítéséhez válassza a **Közzététel** lehetőséget.

      [![Visual Studio – a közzétételi lehetőség és a közzétételi napló kimenete](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png#lightbox)

   1. A Visual Studio **kimenet** paneljén megjelenik egy sikeres közzétételi napló. Az üzembe helyezés befejezésekor a Visual Studio megnyitja a webalkalmazást egy böngésző lapon, és bekéri a bejelentkezést. A sikeres bejelentkezést követően a Time Series Insights vezérlőelemek adatokkal vannak feltöltve.

   1. Navigáljon a webalkalmazáshoz, és jelentkezzen be a megjelenített Time Series Insights vizuális adatok megtekintéséhez.

      [![Az üzemeltetett webalkalmazás áttekintése](media/tutorial-create-tsi-sample-spa/vs-publish-hosted-app.png)](media/tutorial-create-tsi-sample-spa/vs-publish-hosted-app.png#lightbox)

## <a name="troubleshoot"></a>Hibaelhárítás  

Hibakód/állapot | Leírás
---------------------| -----------
*AADSTS50011: Az alkalmazáshoz nincs regisztrálva Válaszcím.* | Az Azure AD-regisztrációból hiányzik egy **átirányítási URI** -tulajdonság. Nyissa meg az Azure ad-alkalmazás regisztrációjának **hitelesítés** > -átirányítási**URI** -azonosítóit. Győződjön meg arról, hogy az **átirányítási URI** -t a **2. lépésben** vagy a **4** . lépésben adta meg, amikor [regisztrálta az alkalmazást az Azure ad használatára](#register-with-azure-ad) .
*AADSTS50011: A kérelemben megadott válasz URL-cím nem egyezik az alkalmazáshoz konfigurált válasz URL-címekkel: "\<Application ID GUID >".* |  > Awebalkalmazás [felépítése és közzététele](#build-and-publish) című **6. b lépésben** megadottértéknekmegkellegyeznieazAzuread-alkalmazásregisztrációjábanahitelesítésiátirányításiURIazonosítóbanmegadottértékkel`postLogoutRedirectUri` . |
A webalkalmazás betöltődik, de egy stílus nélküli, csak szöveges bejelentkezési oldala van, fehér háttérrel. | Győződjön meg arról, hogy a [Webalkalmazás létrehozása és közzététele](#build-and-publish) című **6. lépésben** tárgyalt elérési utak helyesek. Ha a webalkalmazás nem találja a .css fájlokat, akkor a lapok nem a megfelelő stílussal jelennek meg.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez az oktatóanyag több futó Azure-szolgáltatást hoz létre. Ha nem tervezi befejezni ezt az oktatóanyag-sorozatot, ajánlott törölni az összes erőforrást, hogy elkerülje a szükségtelen költségek felmerülését.

A Azure Portal bal oldali menüben:

1. Válassza ki az **erőforráscsoportok**elemet, majd válassza ki a Time Series Insights-környezethez létrehozott erőforráscsoportot. Az oldal tetején válassza az **erőforráscsoport törlése**elemet, adja meg az erőforráscsoport nevét, majd válassza a **Törlés**lehetőséget.
1. Válassza az **erőforráscsoportok**lehetőséget, majd válassza ki azt az erőforráscsoportot, amelyet az eszköz-szimulációs megoldás-gyorsító hozott létre. Az oldal tetején válassza az **erőforráscsoport törlése**elemet, adja meg az erőforráscsoport nevét, majd válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megismerte a következőket:

> [!div class="checklist"]
> * Az alkalmazás kialakítása
> * Az alkalmazás regisztrálása az Azure AD-ben
> * A webalkalmazás létrehozása, közzététele és tesztelése

Ez az oktatóanyag integrálódik az Azure AD-vel, és a bejelentkezett felhasználó identitását használja a hozzáférési token beszerzéséhez. A Time Series Insights API szolgáltatás vagy démon alkalmazás identitásával való elérésének megismeréséhez tekintse meg ezt a cikket:

> [!div class="nextstepaction"]
> [Hitelesítés és engedélyezés az Azure Time Series Insights API-hoz](time-series-insights-authentication-and-authorization.md)
