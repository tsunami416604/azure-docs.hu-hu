---
title: 'Oktatóanyag: Hozzon létre egy Azure Time Series Insights egyoldalas webalkalmazást |} A Microsoft Docs'
description: Megismerheti, hogyan hozhat létre egyoldalas webalkalmazást, amely adatokat kér le és renderel egy TSI-környezetből.
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 04/25/2019
ms.author: anshan
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: 18f5c14a9427f4d7e34a802b2bcc0612a51a804a
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64573118"
---
# <a name="tutorial-create-an-azure-time-series-insights-single-page-web-app"></a>Oktatóanyag: Azure Time Series Insights egyoldalas webalkalmazás létrehozása

Ez az oktatóanyag végigvezeti a Time Series Insights-adatok eléréséhez a saját egyoldalas webalkalmazás létrehozásának folyamatán. Pontosabban a következőkről szerezhet ismereteket:

> [!div class="checklist"]
> * Az alkalmazás kialakítása
> * Az alkalmazás regisztrálása az Azure Active Directory (AD) szolgáltatásban
> * A webalkalmazás létrehozása, közzététele és tesztelése

> [!NOTE]
> * Ebben az oktatóanyagban a forráskódot a megadott [GitHub](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).
> * A Time Series Insights [ügyfélalkalmazás minta](https://insights.timeseries.azure.com/clientsample) üzemel, a jelen oktatóanyagban használt az elkészült alkalmazás megjelenítéséhez.

## <a name="prerequisites"></a>Előfeltételek

* Regisztráljon egy [ingyenes Azure-előfizetést](https://azure.microsoft.com/free/), ha még nem rendelkezik ilyennel.

* A Visual Studio ingyenes példánya is szüksége lesz. Töltse le a [2017-es vagy 2019 Community verziójának](https://www.visualstudio.com/downloads/) a kezdéshez.

* Biztosítani kell a **IIS Express**, **Web Deploy**, és **Azure Cloud Services alapvető eszközök** components Pro Visual Studio. Adja hozzá ezeket a Visual Studio telepítésének módosításával.

## <a name="application-design-overview"></a>Az alkalmazás kialakításának áttekintése

A Time Series Insights egyoldalas mintaalkalmazás alapjául szolgáló a tervezési és a jelen oktatóanyagban használt kódot. A kód a TSI-ügyfél JavaScript-kódtárát használja. A TSI-ügyfélkódtár két fő API-kategória absztrakcióját nyújtja:

- **A TSI lekérdezési API-k meghívására szolgáló burkoló módszerek**: REST API-k, amelyek lehetővé teszik, hogy a lekérdezés TSI adatok JSON-alapú kifejezések használatával. A módszerek a kódtár `TsiClient.server` névterében vannak rendezve.

- **Módszerek a létrehozása és feltöltése számos különböző típusú vezérlők diagramkészítési**: A TSI vizualizációja egy weblap által használt módszerek. A módszerek a kódtár `TsiClient.ux` névterében vannak rendezve.

Ez az oktatóanyag a mintaalkalmazás TSI-környezetéből származó adatokat is használja. További részletek a TSI-mintaalkalmazás szerkezetéről és a TSI-ügyfélkódtár használatáról: [Az Azure Time Series Insights JavaScript ügyfélkódtár felderítése](tutorial-explore-js-client-lib.md).

## <a name="register-the-application-with-azure-ad"></a>Az alkalmazás regisztrálása az Azure AD-ben

Mielőtt létrehozna egy alkalmazást, regisztrálnia kell azt az Azure AD-ben. A regisztráció megadja egy alkalmazás identitáskonfigurációját, ezzel lehetővé téve az OAuth-támogatás kihasználását az egyszeri bejelentkezéshez. Az OAuth-nak SPA-kra van szüksége az „implicit” engedélyezés használatához, amit az alkalmazásjegyzékben kell frissíteni. Az alkalmazásjegyzék az alkalmazás identitáskonfigurációjának JSON-ábrázolása.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-előfizetése fiókjával.  
1. Válassza ki a **Azure Active Directory** erőforrás a bal oldali panelen, majd **alkalmazásregisztrációk**, majd **+ új alkalmazásregisztráció**.

   [![Az Azure portal Azure AD alkalmazás regisztrálása](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration.png#lightbox)

1. Az a **létrehozás** lap, adja meg a szükséges paramétereket.

   Paraméter|Leírás
   ---|---
   **Name (Név)** | Adjon egy kifejező regisztrációs nevet a profilnak.  
   **Alkalmazás típusa** | Mivel éppen egy SPA-webalkalmazást hoz létre, hagyja meg a „Webalkalmazás/API” értéket.
   **Bejelentkezési URL** | Adja meg az alkalmazás kezdőlapjának/bejelentkezési oldalának URL-címét. Az alkalmazás üzemel az Azure App Service (később), mert egy URL-t kell használnia a "https:\//azurewebsites.net" tartomány. Ebben a példában a név a regisztrációs néven alapul.

   Ha elkészült, az új alkalmazásregisztráció létrehozásához kattintson a **Létrehozás** gombra.

   [![Az Azure portal Azure AD alkalmazás regisztrálása - létrehozás](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-create.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-create.png#lightbox)

1. Az erőforrás-alkalmazások más alkalmazások által használt REST API-kat biztosítanak, és szintén regisztrálva vannak az Azure AD-ben. Az API-k részletes/biztonságos hozzáférést nyújtanak az ügyfélalkalmazásokhoz, „hatókörök” használatával. Mivel az alkalmazás az „Azure Time Series Insights” API-t hívja, meg kell adni az API-t és a hatókört, amelyre a futásidőben kért/kapott engedély vonatkozik. Válassza ki **beállítások**, majd **szükséges engedélyek**, majd **+ Hozzáadás**.

   [![Az Azure portal Azure AD-engedélyek hozzáadása](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms.png#lightbox)

1. Az **API-hozzáférés hozzáadása** oldalon kattintson az **1 API kiválasztása** lehetőségre a TSI API megadásához. Az **API kiválasztása** oldalon a keresési mezőbe írja be: „azure time”. Ezután az eredmények listájában válassza ki az "Azure Time Series Insights" API-t, és kattintson a **kiválasztása**.

   [![Az Azure portal Azure AD-engedélyek - API hozzáadása](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api.png#lightbox)

1. Most meg kell adnia az API hatókörét. Az **API-hozzáférés hozzáadása** oldalon kattintson a **2 Engedélyek kiválasztása** lehetőségre. A **Hozzáférés engedélyezése** oldalon válassza az „Azure Time Series Insights szolgáltatás hozzáférés” hatókört. Kattintson a **kiválasztása**, visszaadja, hogy a **API-hozzáférés hozzáadása** oldal, ahol **kész**.

   [![Az Azure portal Azure AD-engedélyek - hatókör hozzáadása](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api-scopes.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api-scopes.png#lightbox)

1. Amikor visszalép a **Szükséges engedélyek** oldalra, láthatja, hogy a felsorolásban most már szerepel az „Azure Time Series Insights” API. Emellett minden felhasználó esetében előzetes hozzájárulást kell adnia ahhoz, hogy az alkalmazás hozzáférjen az API-hoz és a hatókörhöz. Kattintson a **engedélyeket** gombra az oldal tetején, és válassza **Igen**.

   [![Az Azure portal Azure AD - engedélyek és jóváhagyás szükséges](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-required-permissions-consent.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-required-permissions-consent.png#lightbox)

1. Ahogy már említettük, frissíteni kell az alkalmazásjegyzéket. Kattintson az alkalmazás nevére a navigációs menüben, hogy ezzel visszalépjen a **Regisztrált alkalmazás** oldalra. Válassza ki **Manifest**, módosítsa a `oauth2AllowImplicitFlow` tulajdonságot `true`, majd kattintson az **mentése**.

   [![Az Azure portal Azure ad-ben frissítés manifest](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-update-manifest.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-update-manifest.png#lightbox)

1. Végül a navigációs menüben lépjen vissza a **Regisztrált alkalmazás** oldalra, és másolja az alkalmazás **Kezdőlap** URL-címét és **alkalmazásazonosítóját**. Ezek a tulajdonságok egy későbbi lépésben fogja használni.

   [![Az Azure portal Azure AD tulajdonságok](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-application.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-application.png#lightbox)

## <a name="build-and-publish-the-web-application"></a>A webalkalmazás létrehozása és közzététele

1. Hozzon létre egy könyvtárat az alkalmazás projektfájljainak tárolásához. Keresse meg a következő URL-címek mindegyike, kattintson a jobb gombbal a megfelelő lapra, és a "Mentés másként" be a projektkönyvtárba jobb felső területén "Raw" hivatkozásra.

   > [!NOTE]
   > A használt böngészőtől függően előfordulhat, hogy a mentés előtt javítania kell a fájlok kiterjesztését (HTML-re vagy CSS-re).

   - [**index.HTML**](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html): HTML és JavaScript a laphoz.
   - [**sampleStyles.css**]( https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/sampleStyles.css): Stíluslap.

1. Ellenőrizze, hogy a Visual Studio rendelkezik-e a szükséges összetevők telepítése.

    [![A VS - módosíthatja a telepített összetevők](media/tutorial-create-tsi-sample-spa/vs-installation.png)](media/tutorial-create-tsi-sample-spa/vs-installation.png#lightbox)

    * Szüksége lesz a **IIS Express**, **Web Deploy**, és **Azure Cloud Services alapvető eszközök** components Pro Visual Studio.

    > [!NOTE]
    > A Visual Studio felület kissé eltérhet a leírt példák verziója és a konfigurációs beállításoktól függően.

1. Indítsa el a Visual Studiót, jelentkezzen be, és hozzon létre egy projektet a webalkalmazáshoz. A **Fájl** menüben válassza ki a **Megnyitás**, majd a **Webhely** lehetőséget.

    [![A VS - új megoldás létrehozása](media/tutorial-create-tsi-sample-spa/vs-solution-create.png)](media/tutorial-create-tsi-sample-spa/vs-solution-create.png#lightbox)

1. Az a **webhely megnyitása** párbeszédpanelen jelölje ki a munkakönyvtárban a HTML és CSS-fájlok tárolására, majd kattintson a **nyílt**.

   [![A VS - fájlt a webhely megnyitása](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png)](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png#lightbox)

1. Nyissa meg a **Solution Explorer** (Megoldáskezelő) ablakot a Visual Studio **View** (Nézet) menüjéből. Megjelenik az új megoldásokat tartalmazó egy webhely-projektet (földgömb ikon), a HTML és CSS-fájlokat tartalmazó.

   [![A VS - megoldás explorer új megoldás](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png#lightbox)

1. Az alkalmazás közzététele előtt szüksége lesz a konfigurációs beállításokat az megváltoztatására **index.html**.

   a. A függőségeket a FEJLESZTÉSTŐL az éles környezetbe váltás által a hozzászólás alatt a három sor uncommenting `"PROD RESOURCE LINKS"`. Tegye megjegyzésbe a hozzászólás alatt a három sor `"DEV RESOURCE LINKS"`.

      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=2-20&highlight=10-13,15-18)]

      A függőségek ennek megfelelően kell ellátva:

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

   b. Ezután konfigurálja az alkalmazásnak, hogy az Azure Active Directory-alkalmazás regisztrációs azonosítót. Módosítsa a `clientID` és `postLogoutRedirectUri` használandó mezők a **Alkalmazásazonosító** és **kezdőlap URL-címe** a másolt **9. lépés** , [regisztrálni a az Azure AD-alkalmazás](#register-the-application-with-azure-ad).

      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-153&highlight=4-5)]

      Példa:

      ```javascript
      clientId: '8884d4ca-b9e7-403a-bd8a-366d0ce0d460',
      postLogoutRedirectUri: 'https://tsispaapp.azurewebsites.net',
      ```

   c. Mentés **index.html** amikor befejezte, így ezeket a módosításokat.

1. Most tegye közzé a webalkalmazást az Azure-előfizetéshez az Azure App Service-be:  

   > [!NOTE]
   > A következő párbeszédablakokban számos mező az Azure-előfizetéséből származó adatokkal van feltöltve. Emiatt eltarthat néhány másodpercig, mire a párbeszédablakok teljesen betöltenek, és tovább haladhat.  

   a. Kattintson jobb gombbal a a webhely projektcsomópontra **Megoldáskezelőben**, és válassza ki **webalkalmazás közzététele**.  

      [![A VS - megoldáskezelőben a webalkalmazás közzététele](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png#lightbox)

   b. Válassza ki **Start** az alkalmazás közzététele a kezdéshez.

      [![A VS - közzétételi profil](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png#lightbox)

   c. Közzétette az alkalmazást használni kívánt előfizetés kiválasztásához. Válassza ki a **TsiSpaApp** projekt. Ezt követően **OK**:

      [![A VS - közzétételi profil - app Service-ben](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png#lightbox)

   d. Kattintson a **közzététel** webes alkalmazás üzembe helyezéséhez.

      [![VS – Webalkalmazás közzététele – A közzétételi napló kimenete](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png#lightbox)

   e. A Visual Studio **Output** (Kimenet) ablakában látnia kell egy sikeres közzétételi naplót. A telepítés befejeztével a Visual Studio meg is nyitja a webalkalmazást egy böngészőlapon, és a bejelentkezési adatokat kéri. A sikeres bejelentkezést követően látni fogja a adatokkal feltöltve a TSI szabályozza.

## <a name="troubleshooting"></a>Hibaelhárítás  

Hibakód/állapot | Leírás
---------------------| -----------
*AADSTS50011: A válaszcím nem az alkalmazás regisztrálva van.* | Az Azure AD-regisztrációs hiányzik a **válasz URL-cím** tulajdonság. Lépjen az Azure AD-alkalmazásregisztráció **Beállítások** / **Válasz URL-címek** oldalára. Ellenőrizze, hogy a **bejelentkezési** megadott URL-cím **3. lépés** , [regisztrálnia kell az alkalmazást az Azure ad-vel](#register-the-application-with-azure-ad) megtalálható.
*AADSTS50011: A kérésben megadott URL-címet a válasz nem felel meg a válasz URL-címek az alkalmazáshoz konfigurált: '\<Application ID GUID>'.* | A [Webalkalmazás létrehozása és közzététele](#build-and-publish-the-web-application) 4.b lépésében megadott `postLogoutRedirectUri` értéknek ugyanannak kell lennie, mint az Azure AD-alkalmazásregisztráció **Beállítások** / **Válasz URL-címek** tulajdonságánál megadott érték. Ügyeljen arra is módosíthatja, hogy **cél URL-címe** használandó `https`, kiszolgálónként **5. lépés** , [hozhat létre, és tegye közzé a webalkalmazást](#build-and-publish-the-web-application).
A webalkalmazás betöltődik, de egy stílus nélküli, csak szöveges bejelentkezési oldalt jelenít meg fehér háttérrel. | Győződjön meg arról, hogy az elérési utak tárgyalt **4. lépés** , [hozhat létre, és tegye közzé a webalkalmazást](#build-and-publish-the-web-application) helyes-e. Ha a webalkalmazás nem találja a .css fájlokat, akkor a lapok nem a megfelelő stílussal jelennek meg.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez az oktatóanyag több futó Azure-szolgáltatást hoz létre. Ha nem tervezi teljesíteni ezt az oktatóanyag-sorozatot, javasoljuk, hogy a felesleges költségek elkerülése érdekében törölje az összes erőforrást.

Az Azure Portal bal oldali menüjében:

1. Válassza ki **erőforráscsoportok**, majd válassza ki a TSI-környezet számára létrehozott erőforráscsoportot. Az oldal tetején kattintson az **Erőforráscsoport törlése** elemre, írja be az erőforráscsoport nevét, majd kattintson a **Törlés** gombra.
1. Válassza ki **erőforráscsoportok**, majd válassza ki az eszköz szimulálása megoldásgyorsító által létrehozott erőforráscsoportot. Az oldal tetején kattintson az **Erőforráscsoport törlése** elemre, írja be az erőforráscsoport nevét, majd kattintson a **Törlés** gombra.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az alkalmazás kialakítása
> * Az alkalmazás regisztrálása az Azure Active Directory (AD) szolgáltatásban
> * A webalkalmazás létrehozása, közzététele és tesztelése

Ez az oktatóanyag együttműködik az Azure AD-vel, és a bejelentkezett felhasználó identitását használja egy hozzáférési token lekéréséhez. A TSI API egy szolgáltatás/démon alkalmazás identitásával való elérését a következő cikk ismerteti:

> [!div class="nextstepaction"]
> [Hitelesítés és engedélyezés az Azure Time Series Insights API-hoz](time-series-insights-authentication-and-authorization.md)