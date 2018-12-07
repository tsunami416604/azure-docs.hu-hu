---
title: Egy Azure-ban üzemeltetett API exportálása a PowerApps és Microsoft Flow |} A Microsoft Docs
description: Tegye elérhetővé a PowerApps és Microsoft Flow App Service-ben üzemeltetett API áttekintése
services: app-service
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: app-service
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/15/2017
ms.author: glenga
ms.reviewer: sunayv
ms.openlocfilehash: c91508cbd98f18c875411cc7a36f9d71d817fb31
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52997877"
---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>Egy Azure-ban üzemeltetett API exportálása a PowerApps és Microsoft Flow

[A PowerApps](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/) egy szolgáltatás segítségével olyan egyéni üzleti alkalmazások, amelyek csatlakozhat az adataihoz, és a platformok közötti használathoz. [Microsoft Flow](https://flow.microsoft.com/guided-learning/learning-introducing-flow/) segítségével könnyebben automatizálhatja a munkafolyamatokat és üzleti folyamatokat a kedvenc alkalmazásai és szolgáltatásai között. A PowerApps és a Microsoft Flow jár a beépített összekötők különböző adatforrások, például az Office 365, Dynamics 365, Salesforce és több. Bizonyos esetekben alkalmazások és folyamatok is létrehozói számára az adatforrások és a szervezet által készített API-k csatlakozni.

Ehhez hasonlóan szeretne közzétenni az API-k szélesebb körben a szervezeten belül a fejlesztők elérhetővé teheti saját API-k, alkalmazások és folyamatok sikerei. Ez a témakör bemutatja, hogyan exportálni egy API-t a következővel [Azure Functions](../azure-functions/functions-overview.md) vagy [Azure App Service](../app-service/app-service-web-overview.md). Az exportált API lesz a *egyéni összekötő*, amely szolgál a PowerApps és Microsoft Flow hasonlóan egy beépített összekötő.

## <a name="create-and-export-an-api-definition"></a>Hozzon létre és API-definíció exportálása
API-k az exportálás előtt le kell írnia az API-t egy OpenAPI-definíció (korábban egy [Swagger](https://swagger.io/) fájlt). Ez a definíció tartalmazza az API-ban elérhető műveletekkel kapcsolatos információkat, illetve az API kérés- és válaszadatainak felépítését. A PowerApps és a Microsoft Flow OpenAPI 2.0-definíció bármilyen egyéni összekötőket hozhat létre. Az Azure Functions és az Azure App Service rendelkezik beépített támogatást nyújt a létrehozása, üzemeltetéséhez és felügyeletéhez az OpenAPI-definíció. További információkért lásd: [az Azure App Service cors-támogatással rendelkező RESTful API üzemeltetése](../app-service/app-service-web-tutorial-rest-api.md).

> [!NOTE]
> A PowerApps és a Microsoft Flow felhasználói felület, egyéni összekötőket egy OpenAPI-definíció használata nélkül is létrehozható. További információkért lásd: [regisztrálása és használata egy egyéni összekötőt (PowerApps)](https://powerapps.microsoft.com/tutorials/register-custom-api/) és [regisztrálása és használata egy egyéni összekötőt (Microsoft Flow)](https://flow.microsoft.com/documentation/register-custom-api/).

Az API-definíció exportálása, kövesse az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com), és keresse meg az Azure Functions vagy egy másik App Service-alkalmazás.

    Az Azure Functions használata esetén válassza ki a függvényalkalmazást, válassza a **platformfunkciók**, majd **API-definíció**.

    ![Az Azure Functions API-definíció](media/app-service-export-api-to-powerapps-and-flow/api-definition-function.png)

    Ha használja az Azure App Service-ben, válasszon **API-definíció** a beállítások listájában.

    ![Az App Service API-definíció](media/app-service-export-api-to-powerapps-and-flow/api-definition-app.png)

2. A **exportálás a Powerappsba és a Microsoft Flow** gomb elérhetőnek kell lennie (Ha nem, először létre kell hoznia egy OpenAPI-definíció). Kattintson erre a gombra kattintva az exportálási folyamat megkezdéséhez.

    ![Exportálás a Powerappsba és a Microsoft Flow-gomb](media/app-service-export-api-to-powerapps-and-flow/export-apps-flow.png)

3. Válassza ki a **Export mód**:

    **Express** készíthet az egyéni összekötőt az Azure Portalon. Szükséges, hogy bejelentkezett a PowerApps vagy Microsoft Flow és a célkörnyezetben összekötők létrehozásához szükséges engedéllyel rendelkezik. Ez az az ajánlott módszer, ha a két követelménynek érheti el. Ha ezt a módot használ, kövesse a [expressz exportálással](#express) az alábbi utasításokat.

    **Manuális** lehetővé teszi, hogy az API-definíciót, mely akkor exportálása, majd importálása a PowerApps vagy Microsoft Flow portálok használata. Ez az ajánlott módszer az, ha az Azure-felhasználó, és az összekötők létrehozásához szükséges engedéllyel rendelkező felhasználó a különböző emberek, vagy ha az összekötő hozható létre egy másik Azure-bérlőhöz kell. Ha ezt a módot használ, kövesse a [manuális exportálást](#manual) az alábbi utasításokat.

    ![Export mód](media/app-service-export-api-to-powerapps-and-flow/export-mode.png)

> [!NOTE]
> Az egyéni összekötőt használja egy *másolási* az API-definíció, így a PowerApps és a Microsoft Flow nem azonnal kap Ha módosítja az alkalmazást és annak API-definíció. Ha módosítja, ismételje meg az Exportálás az új verzióhoz tartozó.

<a name="express"></a>
## <a name="use-express-export"></a>Expressz exportálással

Az exportálás végrehajtásához **Express** mód, kövesse az alábbi lépéseket:

1. Ellenőrizze, hogy a PowerApps vagy Microsoft Flow bérlőn, amelyhez az exportálni kívánt van bejelentkezve. 

2. A táblázatban megadott beállításokkal.

    |Beállítás|Leírás|
    |--------|------------|
    |**Környezet**|Válassza ki a környezetet, amely az egyéni összekötő menteni kell. További információkért lásd: [környezetek áttekintése](https://powerapps.microsoft.com/tutorials/environments-overview/).|
    |**Egyéni API neve**|Adjon meg egy nevet, mely a PowerApps és Microsoft Flow sikerei jelenik meg az összekötő listájukban.|
    |**Biztonsági konfiguráció előkészítése**|Szükség esetén adja meg a felhasználók hozzáférést biztosítani az API-hoz szükséges biztonsági konfigurációs részleteit. Ez a példa bemutatja egy API-kulcsot. További információkért lásd: [adja meg a hitelesítési típus](#auth) alatt.|
 
    ![Exportálás a Powerappsbe és a Microsoft Flow Express](media/app-service-export-api-to-powerapps-and-flow/export-express.png)

3. Kattintson az **OK** gombra. Az egyéni összekötő most már készített, és felveszi a környezetbe a megadott.

A példa az **Express** mód az Azure Functions, lásd: [függvény hívása a Powerappsből](functions-powerapps-scenario.md) és [függvény hívása a Microsoft Flow](functions-flow-scenario.md).

<a name="manual"></a>
## <a name="use-manual-export"></a>Manuális exportálást

Az exportálás végrehajtásához **manuális** mód, kövesse az alábbi lépéseket:

1. Kattintson a **letöltése** , és mentse a fájlt, vagy kattintson a Másolás gombra, és mentse az URL-címet. A letöltendő fájl vagy az URL-címet fogja használni az importálás során.
 
    ![Manuális exportálás a Powerappsbe és a Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-manual.png)
 
2. Ha az API-definíció bármilyen biztonsági definíciókat tartalmazza, ezek az úgynevezett a #2. lépésben. Importálás során a PowerApps és a Microsoft Flow és megállapítja, ezek a biztonsággal kapcsolatos információk. Gyűjtse össze a hitelesítő adatokat használja a következő szakasz mindegyik definíciója kapcsolatos. További információkért lásd: [adja meg a hitelesítési típus](#auth) alatt.

    ![A manuális exportálást biztonsági](media/app-service-export-api-to-powerapps-and-flow/export-manual-security.png)

    Ez a példa bemutatja az OpenAPI-definíció található API-kulcs biztonsági definíció.

Most, hogy az API-definíció exportált importálása a PowerApps és Microsoft Flow egy egyéni összekötő létrehozása. Egyéni összekötők a két szolgáltatás, így csak importálnia kell a definíció egyszer között vannak megosztva.

Az API-definíció importálása a PowerApps és a Microsoft Flow, kövesse az alábbi lépéseket:

1. Nyissa meg a [powerapps.com](https://web.powerapps.com) vagy a [flow.microsoft.com](https://flow.microsoft.com) webhelyet.

2. A jobb felső sarokban, kattintson a fogaskerék ikonra, majd kattintson a **egyéni összekötők**.

   ![Fogaskerék ikon a szolgáltatásban](media/app-service-export-api-to-powerapps-and-flow/icon-gear.png)

3. Kattintson a **egyéni összekötő létrehozása**, majd kattintson a **OpenAPI-definíció importálása**.

   ![Egyéni összekötő létrehozása](media/app-service-export-api-to-powerapps-and-flow/flow-apps-create-connector.png)

4. Adja meg az egyéni összekötő nevét, majd keresse meg az exportált OpenAPI-definíciót, majd kattintson **Folytatás**.

   ![Töltse fel az OpenAPI-definíció](media/app-service-export-api-to-powerapps-and-flow/flow-apps-upload-definition.png)

4. Az a **általános** fülre, tekintse át az adatokat, amelyek az OpenAPI-definíció származik.

5. Az a **biztonsági** lapon, ha a rendszer kéri, adja meg hitelesítési adatokat, adja meg a hitelesítés típusának megfelelő értékeket. Kattintson a **Folytatás** gombra.

    ![Biztonság lap](media/app-service-export-api-to-powerapps-and-flow/tab-security.png)

    Ez a példa bemutatja a kötelező mezőket az API-kulcsos hitelesítést. A mezők a hitelesítési típusától függően eltérőek.

6. Az a **definíciók** lapon az OpenAPI-fájl által meghatározott összes műveletek rendszer automatikusan kitölti. A szükséges műveletek meg vannak határozva, akkor meg a következő lépéssel. Ha nem, akkor adhat hozzá és műveletek Itt módosíthatja.

    ![Definíciók lap](media/app-service-export-api-to-powerapps-and-flow/tab-definitions.png)

    Ebben a példában van egy művelet nevű `CalculateCosts`. A metaadatokat, például **leírás**, az összes származik az OpenAPI-fájl.

7. Kattintson a **összekötő létrehozása** az oldal tetején.

Az egyéni összekötőt a PowerApps és Microsoft Flow mostantól kapcsolódni. Összekötők létrehozásával a PowerApps és a Microsoft Flow portálon további információkért lásd: [az egyéni összekötő (PowerApps) regisztrálása](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector) és [az egyéni összekötő (Microsoft Flow) regisztrálása](https://flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector).

<a name="auth"></a>
## <a name="specify-authentication-type"></a>A hitelesítés típusának megadása

A PowerApps és a Microsoft Flow támogatja az Identitásszolgáltatók, amelyek az egyéni összekötők hitelesítésének biztosításához gyűjteménye. Ha az API-hoz hitelesítést igényel, győződjön meg arról, mint rögzített egy _biztonsági definíció_ az az OpenAPI-dokumentumot, az alábbi példához hasonlóan:

```json
"securityDefinitions": {
    "AAD": {
    "type": "oauth2",
    "flow": "accessCode",
    "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
    "scopes": {}
    }
}
``` 
Exportálás során, amelyek lehetővé teszik a felhasználók hitelesítése a PowerApps és a Microsoft Flow konfigurációs értékeket ad meg.

Ez a szakasz ismerteti a támogatott hitelesítési típusok **Express** mód: API key, az Azure Active Directory és általános OAuth 2.0. A PowerApps és a Microsoft Flow is támogatja az egyszerű hitelesítés és az OAuth 2.0 adott szolgáltatásokhoz, például Dropbox, a Facebook és a Salesforce-ban.

### <a name="api-key"></a>API-kulcs
API-kulcs használata esetén a kapcsolat létrehozásához, adja meg a kulcsot a rendszer kéri a a felhasználók az összekötő. Megadhatja az API-kulcsnév, amelyekkel megismerheti, hogy mely kulcs van szükség. A korábbi példában nevet használjuk `API Key (contact meganb@contoso.com)` , személyek, hogy hol az API-kulcs adatainak lekérése. Az Azure Functions szolgáltatáshoz, a kulcs a általában egyik gazdagép kulcsot, benne a függvényalkalmazás belül számos funkciót.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)
Azure AD-vel, amikor szüksége van-e két Azure AD alkalmazást az alkalmazásregisztrációk: az API-hoz egyet, és az egyéni összekötőt:

- Az API regisztrációs konfigurálásához használja a [App Service hitelesítés/engedélyezés](../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md) funkció.

- Regisztráció az összekötő konfigurálásához kövesse [hozzáadása az Azure AD-alkalmazást](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-an-application). A regisztráció hozzáférést az API-t és a egy válasz URL-címe van delegált `https://msmanaged-na.consent.azure-apim.net/redirect`. 

További információkért tekintse meg az Azure AD regisztrációs példák [PowerApps](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/) és [Microsoft Flow](https://flow.microsoft.com/documentation/customapi-azure-resource-manager-tutorial/). Ezek a példák az Azure Resource Manager a API;-ként helyettesítse be az API-t, ha végrehajtotta a lépéseket.

A következő konfigurációs értékek szükségesek:
- **Ügyfél-azonosító** – az összekötő Azure AD-regisztrációs ügyfél-azonosító
- **Titkos Ügyfélkód** – az ügyfél titkos kulcsát, az összekötő Azure AD-regisztrációs
- **Bejelentkezési URL-cím** – az Azure Active Directory alap URL-cím. Az Azure-ban, ez a művelet rendszerint `https://login.windows.net`.
- **Bérlőazonosító** – a bejelentkezéshez használandó a bérlő Azonosítóját. Ez legyen "közös" vagy az összekötő létrehozása, amelyben a bérlő Azonosítóját.
- **Erőforrás URL-címe** -erőforrás URL-címét az Azure AD-regisztrációs az API-hoz

> [!IMPORTANT]
> Ha valaki más fogja importálni az API-definíció a PowerApps és a Microsoft Flow a manuális folyamat részeként, meg kell adnia őket az ügyfél-azonosító és a titkos ügyfélkód a *összekötő regisztrálása*, valamint az erőforrás URL-címe az API-t. Győződjön meg arról, hogy biztonságosan felügyelt titkos adatokat. **Ne ossza meg az API-t, a biztonsági hitelesítő adatokat.**

### <a name="generic-oauth-20"></a>Általános OAuth 2.0
Általános OAuth 2.0-s használata esetén minden olyan OAuth 2.0-s szolgáltatóval integrálható. Ez lehetővé teszi, hogy egyéni szolgáltatókat, amelyek natív módon nem támogatottak.

A következő konfigurációs értékek szükségesek:
- **Ügyfél-azonosító** – az OAuth 2.0 ügyfél-azonosító
- **Titkos Ügyfélkód** – az OAuth 2.0 ügyfél titkos kulcsát
- **Engedélyezési URL-címet** – az OAuth 2.0 engedélyezési URL-címe
- **Jogkivonat URL-cím** – az OAuth 2.0 jogkivonat URL-címe
- **URL frissítése** – az OAuth 2.0-s frissítési URL-cím


