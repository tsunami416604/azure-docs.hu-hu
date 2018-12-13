---
title: Hozzon létre egy Azure Time Series Insights egyoldalas webalkalmazást |} A Microsoft Docs
description: Megismerheti, hogyan hozhat létre egyoldalas webalkalmazást, amely adatokat kér le és renderel egy TSI-környezetből.
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 06/14/2018
ms.author: anshan
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: fccd509d4f16cee86d30feb0e838f1493cae4e0b
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53275839"
---
# <a name="tutorial-create-an-azure-time-series-insights-single-page-web-app"></a>Oktatóanyag: Azure Time Series Insights egyoldalas webalkalmazás létrehozása

Az oktatóanyag végigvezeti egy saját egyoldalas webalkalmazás (SPA) létrehozásának folyamatán a TSI-adatok elérése érdekében, amely a [Time Series Insights- (TSI-) mintaalkalmazás](https://insights.timeseries.azure.com/clientsample) után lett modellezve. Ezen oktatóanyag segítségével elsajátíthatja a következőket:

> [!div class="checklist"]
> * Az alkalmazás kialakítása
> * Az alkalmazás regisztrálása az Azure Active Directory (AD) szolgáltatásban
> * A webalkalmazás létrehozása, közzététele és tesztelése 

## <a name="prerequisites"></a>Előfeltételek

Regisztráljon egy [ingyenes Azure-előfizetést](https://azure.microsoft.com/free/), ha még nem rendelkezik ilyennel. 

Emellett telepítenie kell a Visual Studiót, ha még nem tette meg. Ehhez az oktatóanyaghoz [letöltheti/telepítheti az ingyenes Community verziót, vagy egy ingyenes próbaverziót](https://www.visualstudio.com/downloads/).

## <a name="application-design-overview"></a>Az alkalmazás kialakításának áttekintése

Ahogy említettük, a jelen oktatóanyagban használt kialakítás és kód alapja a TSI-mintaalkalmazás. A kód a TSI-ügyfél JavaScript-kódtárát használja. A TSI-ügyfélkódtár két fő API-kategória absztrakcióját nyújtja:

- **A TSI lekérdezési API-k meghívására szolgáló burkoló módszerek**: REST API-k, amelyek lehetővé teszik, hogy a lekérdezés TSI adatok JSON-alapú kifejezések használatával. A módszerek a kódtár `TsiClient.server` névterében vannak rendezve.
- **Módszerek a létrehozása és feltöltése számos különböző típusú vezérlők diagramkészítési**: A TSI vizualizációja egy weblap által használt módszerek. A módszerek a kódtár `TsiClient.ux` névterében vannak rendezve.

Ez az oktatóanyag a mintaalkalmazás TSI-környezetéből származó adatokat is használja. További részletek a TSI-mintaalkalmazás szerkezetéről és a TSI-ügyfélkódtár használatáról: [Az Azure Time Series Insights JavaScript ügyfélkódtár felderítése](tutorial-explore-js-client-lib.md).

## <a name="register-the-application-with-azure-ad"></a>Az alkalmazás regisztrálása az Azure AD-ben 

Mielőtt létrehozna egy alkalmazást, regisztrálnia kell azt az Azure AD-ben. A regisztráció megadja egy alkalmazás identitáskonfigurációját, ezzel lehetővé téve az OAuth-támogatás kihasználását az egyszeri bejelentkezéshez. Az OAuth-nak SPA-kra van szüksége az „implicit” engedélyezés használatához, amit az alkalmazásjegyzékben kell frissíteni. Az alkalmazásjegyzék az alkalmazás identitáskonfigurációjának JSON-ábrázolása. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-előfizetése fiókjával.  
1. Válassza az **Azure Active Directory** erőforrást a bal oldali panelen, majd az **Alkalmazásregisztrációk**, végül az **+ Új alkalmazás regisztrálása** lehetőséget:  
   
   ![Azure AD-alkalmazásregisztráció az Azure Portalon](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration.png)

1. A **Létrehozás** oldalon adja meg a szükséges paramétereket:
   
   Paraméter|Leírás
   ---|---
   **Name (Név)** | Adjon egy kifejező regisztrációs nevet a profilnak.  
   **Alkalmazás típusa** | Mivel éppen egy SPA-webalkalmazást hoz létre, hagyja meg a „Webalkalmazás/API” értéket.
   **Bejelentkezési URL** | Adja meg az alkalmazás kezdőlapjának/bejelentkezési oldalának URL-címét. Mivel az alkalmazást az Azure App Service futtatja (lásd később), a használt URL-nek a „https://azurewebsites.net” tartományon belül kell lennie. Ebben a példában a név a regisztrációs néven alapul.

   Ha elkészült, az új alkalmazásregisztráció létrehozásához kattintson a **Létrehozás** gombra.

   ![Azure AD-alkalmazásregisztráció az Azure Portalon – Létrehozás](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-create.png)

1. Az erőforrás-alkalmazások más alkalmazások által használt REST API-kat biztosítanak, és szintén regisztrálva vannak az Azure AD-ben. Az API-k részletes/biztonságos hozzáférést nyújtanak az ügyfélalkalmazásokhoz, „hatókörök” használatával. Mivel az alkalmazás az „Azure Time Series Insights” API-t hívja, meg kell adni az API-t és a hatókört, amelyre a futásidőben kért/kapott engedély vonatkozik. Válassza a **Beállítások**, a **Szükséges engedélyek**, majd **+ Hozzáadás** lehetőséget:

   ![Azure AD-engedélyek hozzáadása az Azure Portalon](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms.png)

1. Az **API-hozzáférés hozzáadása** oldalon kattintson az **1 API kiválasztása** lehetőségre a TSI API megadásához. Az **API kiválasztása** oldalon a keresési mezőbe írja be: „azure time”. Ezután válassza ki az „Azure Time Series Insights” API-t az eredménylistából, és kattintson a **Kiválasztás** lehetőségre: 

   ![Azure AD-engedélyek hozzáadása az Azure Portalon – API](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api.png)

1. Most meg kell adnia az API hatókörét. Az **API-hozzáférés hozzáadása** oldalon kattintson a **2 Engedélyek kiválasztása** lehetőségre. A **Hozzáférés engedélyezése** oldalon válassza az „Azure Time Series Insights szolgáltatás hozzáférés” hatókört. Kattintson a **Kiválasztás** gombra, ezzel visszatér az **API-hozzáférés hozzáadása** oldalra, ahol kattintson a **Kész** gombra:

   ![Azure AD-engedélyek hozzáadása az Azure Portalon – Hatókör](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api-scopes.png)

1. Amikor visszalép a **Szükséges engedélyek** oldalra, láthatja, hogy a felsorolásban most már szerepel az „Azure Time Series Insights” API. Emellett minden felhasználó esetében előzetes hozzájárulást kell adnia ahhoz, hogy az alkalmazás hozzáférjen az API-hoz és a hatókörhöz. Kattintson a felül lévő **Engedélyek megadása** gombra, majd az **Igen** lehetőségre:

   ![Azure AD-engedélyek hozzáadása az Azure Portalon – Hozzájárulás](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-required-permissions-consent.png)

1. Ahogy már említettük, frissíteni kell az alkalmazásjegyzéket. Kattintson az alkalmazás nevére a navigációs menüben, hogy ezzel visszalépjen a **Regisztrált alkalmazás** oldalra. Válassza a **Jegyzék** lehetőséget, módosítsa az `oauth2AllowImplicitFlow` tulajdonság beállítását `true` értékre, majd kattintson a **Mentés** gombra:

   ![Azure AD-jegyzék frissítése az Azure Portalon](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-update-manifest.png)

1. Végül a navigációs menüben lépjen vissza a **Regisztrált alkalmazás** oldalra, és másolja az alkalmazás **Kezdőlap** URL-címét és **alkalmazásazonosítóját**. Ezekre a tulajdonságokra egy későbbi lépésben lesz szükség:

   ![Azure AD-tulajdonságok az Azure Portalon](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-application.png)

## <a name="build-and-publish-the-web-application"></a>A webalkalmazás létrehozása és közzététele

1. Hozzon létre egy könyvtárat az alkalmazás projektfájljainak tárolásához. Ezután nyissa meg a következő URL-címeket, kattintson a jobb gombbal az oldal jobb felső részén található „Nyers” hivatkozásokra, majd a „Mentés másként” lehetőséggel mentse őket a projekt könyvtárába:

   > [!NOTE]
   > A használt böngészőtől függően előfordulhat, hogy a mentés előtt javítania kell a fájlok kiterjesztését (HTML-re vagy CSS-re).

   - **index.html**: a https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html oldal HTML- és JavaScript-kódja
   - **sampleStyles.css:** Stíluslap: https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/sampleStyles.css
    
1. Indítsa el a Visual Studiót, jelentkezzen be, és hozzon létre egy projektet a webalkalmazáshoz. A **Fájl** menüben válassza ki a **Megnyitás**, majd a **Webhely** lehetőséget. A **Webhely megnyitása** párbeszédpanelen válassza ki a munkakönyvtárat, ahol a HTML- és CSS-fájlok találhatók, majd kattintson a **Megnyitás** lehetőségre:

   ![VS – Webhely fájljainak megnyitása](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png)

1. Nyissa meg a **Solution Explorer** (Megoldáskezelő) ablakot a Visual Studio **View** (Nézet) menüjéből. Ekkor meg kell jelennie az új megoldásnak, amely egy webhelyprojektet tartalmaz (földgömb ikon), amely pedig tartalmazza a HTML- és CSS-fájlokat:

   ![VS – Új megoldás a megoldáskezelőben](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png)

1. Mielőtt közzétenné az alkalmazást, frissítenie kell a JavaScript-kód egyes szakaszait az **index.html** fájlban: 

   a. Először módosítsa a JavaScript- és stíluslap-fájlok elérési útjait a `<head>` elemben. Nyissa meg az **index.html** fájlt a Visual Studio-megoldásban, és adja hozzá a következő sorokat a JavaScript-kódhoz. Távolítsa el a megjegyzés jelölést a „PROD RESOURCE LINKS” részen található három sor mellől, és lássa el megjegyzés jelöléssel a „DEV RESOURCE LINKS” részen található három sort:
   
      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=2-20&highlight=10-13,15-18)]

      A módosított kódnak a következőképpen kell kinéznie:

      ```javascript
      <!-- PROD RESOURCE LINKS -->
      <link rel="stylesheet" type="text/css" href="sampleStyles.css"></link>
      <script src="https://unpkg.com/tsiclient@1.1.4/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="https://unpkg.com/tsiclient@1.1.4/tsiclient.css"></link>

      <!-- DEV RESOURCE LINKS -->
      <!-- <link rel="stylesheet" type="text/css" href="pages/samples/sampleStyles.css"></link>
      <script src="dist/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="dist/tsiclient.css"></link> -->
      ```

   b. Ezután módosítsa a hozzáférési token logikáját az új Azure AD-alkalmazásregisztráció használatára. Módosítsa a `clientID` és `postLogoutRedirectUri` változókat, és illessze be az alkalmazásazonosítót és kezdőlap URL-t, amelyeket [Az alkalmazás regisztrálása az Azure AD-ben](#register-the-application-with-azure-ad) szakasz 9. lépésében másolt le:

      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-153&highlight=4-5)]

      A módosított kódnak a következőképpen kell kinéznie:

      ```javascript
      clientId: '8884d4ca-b9e7-403a-bd8a-366d0ce0d460',
      postLogoutRedirectUri: 'https://tsispaapp.azurewebsites.net',
      ``` 

   c. Mentse az **index.html** fájlt, ha végzett a szerkesztéssel.

1. Ezután tegye közzé a webalkalmazást az Azure-előfizetésében Azure App Service szolgáltatásként:  

   > [!NOTE]
   > A következő párbeszédablakokban számos mező az Azure-előfizetéséből származó adatokkal van feltöltve. Emiatt eltarthat néhány másodpercig, mire a párbeszédablakok teljesen betöltenek, és tovább haladhat.  

   a. A **Solution Explorer** (Megoldáskezelő) felületén kattintson a jobb gombbal a projektcsomópontra, majd válassza a **Publish Web App** (Webalkalmazás közzététele) lehetőséget:  

      ![VS – Webalkalmazás közzététele a megoldáskezelőben](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png)

   b. Válassza ki a **Microsoft Azure App Service** lehetőséget egy közzétételi cél létrehozásához:  

      ![VS – Közzétételi profil](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png)  

   c. Válassza ki az alkalmazás közzétételéhez használni kívánt előfizetést, majd kattintson az „Új” lehetőségre: 

      ![VS – Közzétételi profil – App Service](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png)  

   d. Várjon néhány másodpercet, míg az **App Service létrehozása** párbeszédpanel betölti az összes mezőt, majd módosítsa a következő mezőket:
   
      Mező | Leírás
      ---|---
      **Alkalmazás neve** | Módosítsa arra az Azure AD-alkalmazásregisztrációs névre, amelyet [Az alkalmazás regisztrálása az Azure AD-ben](#register-the-application-with-azure-ad) szakasz 3. lépésében megadott. 
      **Erőforráscsoport** | Az **Új...**  gomb segítségével módosítsa az **Alkalmazásnév** mezőnek megfelelően.
      **App Service-csomag** | Az **Új...**  gomb segítségével módosítsa az **Alkalmazásnév** mezőnek megfelelően.

      Ha végzett, kattintson a **Létrehozás** gombra. A bal alsó részen az **Exportálás** gomb helyett néhány másodpercig a „Telepítés:” felirat jelenik meg, amíg az Azure-erőforrások létrejönnek. Az erőforrások létrejöttét követően a rendszer visszalépteti az előző párbeszédablakba. 

      ![VS – Közzétételi profil – Új App Service szolgáltatás hozzáadása](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service-create.png)  

   e. Miután visszalépett a **Közzététel** párbeszédpanelre, győződjön meg arról, hogy a **Közzététel módja** beállítása „Webes telepítés”. Továbbá az Azure AD-alkalmazásregisztrációnak megfelelően módosítsa a **Cél URL-cím** beállítását a `https` értékre `http` helyett. Ezután kattintson a „Közzététel” gombra a webalkalmazás telepítéséhez:  

      ![VS – Webalkalmazás közzététele – Az App Service szolgáltatás közzététele](media/tutorial-create-tsi-sample-spa/vs-publish-publish.png)  

   f. A Visual Studio **Output** (Kimenet) ablakában látnia kell egy sikeres közzétételi naplót. A telepítés befejeztével a Visual Studio meg is nyitja a webalkalmazást egy böngészőlapon, és a bejelentkezési adatokat kéri. A sikeres bejelentkezést követően látni fogja, hogy az összes TSI-vezérlő fel van töltve adatokkal:  

      [![VS – Webalkalmazás közzététele – A közzétételi napló kimenete](media/tutorial-create-tsi-sample-spa/vs-publish-output.png)](media/tutorial-create-tsi-sample-spa/vs-publish-output.png#lightbox)

      ![TSI SPA alkalmazás – bejelentkezés](media/tutorial-create-tsi-sample-spa/tsispaapp-azurewebsites-net.png)  

## <a name="troubleshooting"></a>Hibaelhárítás  

Hibakód/állapot | Leírás
---------------------| -----------
*AADSTS50011: A válaszcím nem az alkalmazás regisztrálva van.* | Az Azure AD-regisztrációból hiányzik a „Válasz-URL” tulajdonság. Lépjen az Azure AD-alkalmazásregisztráció **Beállítások** / **Válasz URL-címek** oldalára. Ellenőrizze, hogy meg van-e adva az a **bejelentkezési** URL-cím, amelyet [Az alkalmazás regisztrálása az Azure AD-ben](#register-the-application-with-azure-ad) 3. lépésében megadott. 
*AADSTS50011: A válasz URL-címe a kérelemben megadott nem egyezik az alkalmazáshoz konfigurált válasz URL: "<Application ID GUID>".* | A [Webalkalmazás létrehozása és közzététele](#build-and-publish-the-web-application) 4.b lépésében megadott `postLogoutRedirectUri` értéknek ugyanannak kell lennie, mint az Azure AD-alkalmazásregisztráció **Beállítások** / **Válasz URL-címek** tulajdonságánál megadott érték. Módosítsa a **Cél URL-címet** `https` használatára, a [Webalkalmazás létrehozása és közzététele](#build-and-publish-the-web-application) 5.e lépésében leírtak szerint.
A webalkalmazás betöltődik, de egy stílus nélküli, csak szöveges bejelentkezési oldalt jelenít meg fehér háttérrel. | Ellenőrizze, hogy a [Webalkalmazás létrehozása és közzététele](#build-and-publish-the-web-application) 4.a lépésében megadott elérési utak helyesek-e. Ha a webalkalmazás nem találja a .css fájlokat, akkor a lapok nem a megfelelő stílussal jelennek meg.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez az oktatóanyag több futó Azure-szolgáltatást hoz létre. Ha nem tervezi teljesíteni ezt az oktatóanyag-sorozatot, javasoljuk, hogy a felesleges költségek elkerülése érdekében törölje az összes erőforrást. 

Az Azure Portal bal oldali menüjében:

1. Kattintson az **Erőforráscsoportok** ikonra, majd válassza ki a TSI-környezethez létrehozott erőforráscsoportot. Az oldal tetején kattintson az **Erőforráscsoport törlése** elemre, írja be az erőforráscsoport nevét, majd kattintson a **Törlés** gombra. 
1. Kattintson az **Erőforráscsoportok** ikonra, majd válassza ki az eszközszimulációs megoldásgyorsító által létrehozott erőforráscsoportot. Az oldal tetején kattintson az **Erőforráscsoport törlése** elemre, írja be az erőforráscsoport nevét, majd kattintson a **Törlés** gombra. 

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az alkalmazás kialakítása
> * Az alkalmazás regisztrálása az Azure Active Directory (AD) szolgáltatásban
> * A webalkalmazás létrehozása, közzététele és tesztelése 

Ez az oktatóanyag együttműködik az Azure AD-vel, és a bejelentkezett felhasználó identitását használja egy hozzáférési token lekéréséhez. A TSI API egy szolgáltatás/démon alkalmazás identitásával való elérését a következő cikk ismerteti:

> [!div class="nextstepaction"]
> [Hitelesítés és engedélyezés az Azure Time Series Insights API-hoz](time-series-insights-authentication-and-authorization.md)
