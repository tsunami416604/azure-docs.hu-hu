---
title: 'Oktatóanyag: Hozzon létre egy Azure Time Series Insights egyoldalas webalkalmazást |} A Microsoft Docs'
description: Ismerje meg, hogyan hozhat létre egy egyoldalas webalkalmazást, amely lekérdezi, és ez a beállítás le adatokat Azure Time Series Insights-környezetből.
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 04/25/2019
ms.author: dpalled
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: 2f25267b95e9ed5f7d5f6e6373fb9e3807927a7f
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735348"
---
# <a name="tutorial-create-an-azure-time-series-insights-single-page-web-app"></a>Oktatóanyag: Azure Time Series Insights egyoldalas webalkalmazás létrehozása

Ez az oktatóanyag végigvezeti a felhasználót az Azure Time Series Insights-adatok eléréséhez a saját egyoldalas webes alkalmazás (SPA) létrehozásának folyamatán. 

Ezen oktatóanyag segítségével elsajátíthatja a következőket:

> [!div class="checklist"]
> * Az alkalmazás kialakítása
> * Az alkalmazás regisztrálása az Azure Active Directoryval (Azure AD)
> * A webalkalmazás létrehozása, közzététele és tesztelése

> [!NOTE]
> * Ebben az oktatóanyagban a forráskódot a megadott [GitHub](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).
> * A Time Series Insights [ügyfélalkalmazás minta](https://insights.timeseries.azure.com/clientsample) üzemel, a jelen oktatóanyagban használt az elkészült alkalmazás megjelenítéséhez.

## <a name="prerequisites"></a>Előfeltételek

* Regisztráljon egy [ingyenes Azure-előfizetés](https://azure.microsoft.com/free/) Ha még nem rendelkezik.

* A Visual Studio ingyenes példánya. Töltse le a [2017-es vagy 2019 közösségi verzió](https://www.visualstudio.com/downloads/) a kezdéshez.

* Az IIS Express, Web Deploy és az Azure Cloud Services eszközök alapösszetevők a Visual Studióhoz. Az összetevők hozzáadása a Visual Studio telepítésének módosításával.

## <a name="application-design"></a>Az alkalmazás kialakítása

A Time Series Insights minta SPA alapja a tervezési és a jelen oktatóanyagban használt kódot. A kód a Time Series Insights JavaScript ügyféloldali kódtárat használja. A Time Series Insights-klienskódtára biztosítja egy absztrakciós két fő API-kategóriák:

- **A Time Series Insights meghívására szolgáló burkoló módszerek lekérdezési API-k**: REST API-k használatával adatokat kérdezhet le a Time Series Insights JSON-alapú kifejezések használatával. A metódusok a könyvtár a TsiClient.server névtérben vannak rendszerezve.

- **Módszerek a létrehozása és feltöltése számos különböző típusú vezérlők diagramkészítési**: A módszerek segítségével megjelenítheti a Time Series Insights adatait egy weblap. A metódusok a könyvtár a TsiClient.ux névtérben vannak rendszerezve.

Ez az oktatóanyag a mintaalkalmazás a Time Series Insights-környezet adatait is használja. A Time Series Insights-mintaalkalmazást, és hogyan használja a Time Series Insights ügyféloldali kódtár szerkezete, lásd az oktatóanyag [Fedezze fel az Azure Time Series Insights JavaScript ügyféloldali kódtár](tutorial-explore-js-client-lib.md).

## <a name="register-the-application-with-azure-ad"></a>Az alkalmazás regisztrálása az Azure AD-ben

Mielőtt az alkalmazás létrehozásához, az Azure ad-vel kell regisztrálni. Regisztráció az identitás konfigurációs kínálja fel, hogy az alkalmazás használhatja OAuth-támogatás az egyszeri bejelentkezés. OAuth gyógyfürdők Implicit engedélyezés engedélyezési típusa használatára van szükség. Frissítse az engedély az alkalmazásjegyzékben. Az alkalmazásjegyzék az alkalmazás identitáskonfigurációjának JSON-ábrázolása.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) az Azure-előfizetés fiók használatával.  
1. Válassza az **Azure Active Directory** > **Alkalmazásregisztrációk** > **Új alkalmazás regisztrálása** lehetőséget.

   [![Az Azure portal – kezdje az Azure AD alkalmazás regisztrálása](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration.png#lightbox)

1. Az a **létrehozás** panelen adja meg a szükséges paramétereket.

   Paraméter|Leírás
   ---|---
   **Name (Név)** | Adjon meg egy kifejező regisztrációs nevet.  
   **Alkalmazás típusa** | Hagyja meg **Web app és az API**.
   **Bejelentkezési URL** | Adja meg a bejelentkezési (kezdőlap) lapján az alkalmazás URL-CÍMÉT. Az alkalmazás újabb üzemel az Azure App Service-ben, mert egy URL-címet kell használnia a a https:\//azurewebsites.net tartományhoz. Ebben a példában a név a regisztrációs néven alapul.

   Válassza ki **létrehozás** hozhat létre az új alkalmazás regisztrálása.

   [![Azure portal – lehetőség a létrehozása az Azure AD alkalmazás regisztrációs panelen](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-create.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-create.png#lightbox)

1. Erőforrás alkalmazások biztosítanak a REST API-k, amelyek más alkalmazások is használják. Az API-k is az Azure ad-vel van regisztrálva. API-k az ügyfélalkalmazások számára részletes, biztonságos hozzáférést biztosítanak a megjelenítésével befejeztük *hatókörök*. Mivel az alkalmazás az Azure Time Series Insights API-hívások, meg kell adnia az API-t és a hatókör. Az engedélyt az API-t és a hatókör futásidőben. Válassza ki **beállítások** > **szükséges engedélyek** > **Hozzáadás**.

   [![Az Azure portal – hozzáadásához az Azure AD-engedélyekről az hozzáadása lehetőség](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms.png#lightbox)

1. Az a **API-hozzáférés hozzáadása** ablaktáblán válassza előbb **1 API kiválasztása** , adja meg az Azure Time Series Insights API. Az a **API kiválasztása** ablaktáblában a Keresés mezőbe írja be **az azure time**. Ezután válassza ki **Azure Time Series Insights** az eredménylistában. Válassza a **Kiválasztás** lehetőséget

   [![Az Azure portal – a keresési lehetőség az Azure AD-engedélyekről hozzáadása](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api.png#lightbox)

1. Jelölje ki az API-hoz, a hatókör az **API-hozzáférés hozzáadása** ablaktáblán válassza előbb **2 Select engedélyek**. Az a **hozzáférés engedélyezése** panelen válassza a **hozzáférés az Azure Time Series Insights szolgáltatás** hatókör. Válassza a **Kiválasztás** lehetőséget Visszatér a **API-hozzáférés hozzáadása** ablaktáblán. Válassza a **Done** (Kész) lehetőséget.

   [![Az Azure portal - hozzáadása az Azure AD-engedélyekről hatókör beállítása](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api-scopes.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api-scopes.png#lightbox)

1. Az a **szükséges engedélyek** ablaktáblán, az Azure Time Series Insights API mostantól látható. Emellett meg kell adnia a előtti hozzájárulási engedélyt az alkalmazáshoz hozzáférni az API-hoz és a hatókör az összes felhasználó számára. Válassza ki **engedélyeket**, majd válassza ki **Igen**.

   [![Az Azure portal – a Grant engedélyek beállítás hozzáadásához az Azure AD szükséges engedélyek](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-required-permissions-consent.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-required-permissions-consent.png#lightbox)

1. Hogy taglaltak szerint is frissítenie kell az alkalmazásjegyzékben. A vízszintes menü felső részén a panelen (a "navigációs"), válassza ki az alkalmazás nevét, térjen vissza a **regisztrált alkalmazás** ablaktáblán. Válassza ki **Manifest**, módosítsa a `oauth2AllowImplicitFlow` tulajdonságot `true`, majd válassza ki **mentése**.

   [![Azure portal – a jegyzékfájl frissítése az Azure ad-ben](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-update-manifest.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-update-manifest.png#lightbox)

1. A webhely-navigációs válassza ki az alkalmazás nevét, térjen vissza a **regisztrált alkalmazás** ablaktáblán. Másolja le az értékeket a **kezdőlap** és **Alkalmazásazonosító** az alkalmazáshoz. Az oktatóanyag későbbi részében használhatja ezeket a tulajdonságokat.

   [![Azure portal – Másolás a kezdőlap URL-címe és az Alkalmazásazonosító az alkalmazás értékei](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-application.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-application.png#lightbox)

## <a name="build-and-publish-the-web-application"></a>A webalkalmazás létrehozása és közzététele

1. Hozzon létre egy könyvtárat az alkalmazás projektfájljainak tárolásához. Ezt követően folytassa a következő URL-címek mindegyike. Kattintson a jobb gombbal a **Raw** hivatkozásra az oldal jobb felső sarkában, és válassza ki **Mentés másként** , mentse a fájlokat a projekt könyvtárában.

   - [*index.HTML*](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html): HTML és JavaScript a laphoz
   - [*sampleStyles.css*]( https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/sampleStyles.css): Stíluslap

   > [!NOTE]
   > A böngésző függően előfordulhat, hogy módosítani szeretné a kiterjesztések .html vagy .css a fájl mentése előtt.

1. Győződjön meg arról, hogy a szükséges összetevők telepítve vannak-e a Visual Studióban. Az IIS Express, Web Deploy és az Azure Cloud Services alapvető eszközök összetevői a Visual Studio kell telepíteni.

    [![A Visual Studio - módosíthatja a telepített összetevők](media/tutorial-create-tsi-sample-spa/vs-installation.png)](media/tutorial-create-tsi-sample-spa/vs-installation.png#lightbox)

    > [!NOTE]
    > A Visual Studio felhasználói élmény némileg eltérhet a leírt példák a verzió és a konfigurációs beállításoktól függően.

1. Nyissa meg a Visual Studiót, és jelentkezzen be. Egy projektet a webes alkalmazás létrehozása a a **fájl** menüjében válassza **nyílt** > **webhely**.

    [![Visual Studio – új megoldás létrehozása](media/tutorial-create-tsi-sample-spa/vs-solution-create.png)](media/tutorial-create-tsi-sample-spa/vs-solution-create.png#lightbox)

1. Az a **megnyitott webhely** ablaktáblán válassza ki a HTML és CSS-fájlok tárolására a munkakönyvtárat, és válassza **nyílt**.

   [![Visual Studio – a Fájl menü megnyitása és a webhely beállításokkal](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png)](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png#lightbox)

1. A Visual Studio **nézet** menüjében válassza **Megoldáskezelőben**. Megnyílik az új megoldásokat. Egy webhely-projektet (földgömb ikon), a HTML és CSS-fájlokat tartalmazó tartalmazza.

   [![Visual Studio – új megoldásra a Megoldáskezelőben](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png#lightbox)

1. Mielőtt közzéteszi az alkalmazást, meg kell változtatnia a konfigurációs beállításai a *index.html*.

   1. Állítsa vissza a hozzászólás alatt a három sor `"PROD RESOURCE LINKS"` a függőségeket a FEJLESZTÉSTŐL az éles környezetbe váltás. Tegye megjegyzésbe a hozzászólás alatt a három sor `"DEV RESOURCE LINKS"`.

      [!code-html[head-sample](~/samples-javascript/pages/tutorial/index.html?range=2-20&highlight=10-13,15-18)]

      A függőségek az alábbi példához hasonlóan kell ellátva:

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

   1. Konfigurálásához az alkalmazásnak, hogy az Azure AD alkalmazás regisztrációs Azonosítót használja, módosítsa a `clientID` és `postLogoutRedirectUri` értékeket kell használnia a tartozó értékeket **Alkalmazásazonosító** és **kezdőlap** a9.lépésbenkimásolt[ Az alkalmazás regisztrálása az Azure ad-vel](#register-the-application-with-azure-ad).

      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-153&highlight=4-5)]

      Példa:

      ```javascript
      clientId: '8884d4ca-b9e7-403a-bd8a-366d0ce0d460',
      postLogoutRedirectUri: 'https://tsispaapp.azurewebsites.net',
      ```

   1. Ha elkészült, így a módosításokat, Mentés *index.html*.

1. Tegye közzé a webalkalmazást az Azure-előfizetésében, egy Azure App Service-ben.  

   > [!NOTE]
   > Több lehetőség képernyőképeken látható a következő lépések automatikusan fel van töltve az Azure-előfizetés adatait. Eltarthat néhány másodpercre egyes betöltése befejeződik.  

   1. A Megoldáskezelőben kattintson a jobb gombbal a projektcsomópontra webhelyet, és válassza **webalkalmazás közzététele**.  

      [![Visual Studio – a Solution Explorer közzététele webalkalmazás lehetőséget választja](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png#lightbox)

   1. Válassza ki **Start** az alkalmazás közzététele a kezdéshez.

      [![Visual Studio – a közzétételi profil ablaktábla](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png#lightbox)

   1. Jelölje ki az alkalmazás közzététele használni kívánt előfizetést. Válassza ki a **TsiSpaApp** projekt. Ezután válassza az **OK** lehetőséget.

      [![Visual Studio – a közzétételi profilt az App Service panel](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png#lightbox)

   1. Válassza ki **közzététel** webes alkalmazás üzembe helyezéséhez.

      [![Visual Studio – a közzététel lehetőségről és a közzététel kimenet](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png#lightbox)

   1. A Visual studióban megjelenik a napló sikeres közzététel **kimeneti** ablaktáblán. Az üzembe helyezés befejezése a Visual Studio egy böngészőlapon nyílik meg a webalkalmazás, és kéri a bejelentkezéshez. A sikeres bejelentkezést követően a Time Series Insights vezérlőelemek adatokkal van feltöltve.

## <a name="troubleshoot"></a>Hibaelhárítás  

Hibakód/állapot | Leírás
---------------------| -----------
*AADSTS50011: A válaszcím nem az alkalmazás regisztrálva van.* | Az Azure AD-regisztrációs hiányzik a **válasz URL-cím** tulajdonság. Lépjen a **beállítások** > **válasz URL-címek** számára az Azure AD-alkalmazás regisztrációja. Ellenőrizze, hogy a **bejelentkezési** a 3. lépésben megadott URL-cím [regisztrálnia kell az alkalmazást az Azure ad-vel](#register-the-application-with-azure-ad) megtalálható.
*AADSTS50011: A kérésben megadott URL-címet a válasz nem felel meg a válasz URL-címek az alkalmazáshoz konfigurált: '\<Application ID GUID>'.* | A `postLogoutRedirectUri` 6. lépésben megadott [hozhat létre, és tegye közzé a webalkalmazást](#build-and-publish-the-web-application) meg kell egyeznie a megadott érték **beállítások** > **válasz URL-címek** a az Azure AD-alkalmazás regisztrációja. Ügyeljen arra, hogy tartozó értéknek a módosításához is **cél URL-címe** használandó *https* száma 5. lépés [hozhat létre, és tegye közzé a webalkalmazást](#build-and-publish-the-web-application).
A webalkalmazás betöltődik, de az még egy stílus nélküli, csak szöveges bejelentkezési oldala, amelyen egy fehér háttér. | Győződjön meg arról, hogy ha az elérési utak tárgyalt 4. lépés [hozhat létre, és tegye közzé a webalkalmazást](#build-and-publish-the-web-application) helyes-e. Ha a webalkalmazás nem találja a .css fájlokat, akkor a lapok nem a megfelelő stílussal jelennek meg.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez az oktatóanyag több futó Azure-szolgáltatást hoz létre. Ha nem szeretné befejezni az oktatóanyag-sorozat, javasoljuk, hogy biztosan törölje a felesleges költségek elkerülése érdekében minden erőforrás.

Az Azure portal bal oldali menüben lévő:

1. Válassza ki **erőforráscsoportok**, majd válassza ki a Time Series Insights-környezethez létrehozott erőforráscsoportot. A lap tetején válassza **erőforráscsoport törlése**, és adja meg az erőforráscsoport nevét, majd válassza ki **törlése**.
1. Válassza ki **erőforráscsoportok**, majd válassza ki az erőforráscsoportot, amely az eszköz szimulálása megoldásgyorsító hozta létre. A lap tetején válassza **erőforráscsoport törlése**, és adja meg az erőforráscsoport nevét, majd válassza ki **törlése**.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megismerkedett:

> [!div class="checklist"]
> * Az alkalmazás kialakítása
> * Az alkalmazás regisztrálása az Azure ad-vel
> * A webalkalmazás létrehozása, közzététele és tesztelése

Ebben az oktatóanyagban integrálható az Azure ad-ben, és a hozzáférési jogkivonat beszerzése van bejelentkezett felhasználó az identitást használja. A Time Series Insights API eléréséhez egy szolgáltatás- vagy démonalkalmazásban alkalmazáshoz használatával kapcsolatban lásd: Ez a cikk:

> [!div class="nextstepaction"]
> [Hitelesítés és engedélyezés az Azure Time Series Insights API-hoz](time-series-insights-authentication-and-authorization.md)
