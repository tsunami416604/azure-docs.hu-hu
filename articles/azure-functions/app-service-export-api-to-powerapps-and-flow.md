---
title: Azure-ban tárolt API exportálása a PowerApps be és a Microsoft Flow szolgáltatásba
description: Az App Service-ben tárolt API-k elérhetővé tévő részének áttekintése a PowerApps és a Microsoft Flow számára
ms.topic: conceptual
ms.date: 12/15/2017
ms.reviewer: sunayv
ms.openlocfilehash: 632818bf82e41e6be0a96d30cc1c4fa631718a3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74233077"
---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>Azure-ban tárolt API exportálása a PowerApps be és a Microsoft Flow szolgáltatásba

[A PowerApps](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/) olyan szolgáltatás, amely olyan egyéni üzleti alkalmazások at hoz létre és használ, amelyek kapcsolódnak az adatokhoz, és különböző platformokon működnek. [A Microsoft Flow](/learn/modules/get-started-with-flow/index) megkönnyíti a munkafolyamatok és üzleti folyamatok automatizálását kedvenc alkalmazásai és szolgáltatásai között. Mind a PowerApps, mind a Microsoft Flow számos beépített összekötővel rendelkezik az adatforrásokhoz, például az Office 365, a Dynamics 365, a Salesforce és más adatforrásokhoz. Bizonyos esetekben az alkalmazás- és folyamatépítők is szeretnének csatlakozni a szervezetük által készített adatforrásokhoz és API-khoz.

Hasonlóképpen, azok a fejlesztők, akik szélesebb körben szeretnék elérhetővé tenni API-jaikat egy szervezeten belül, elérhetővé tehetik API-jaikat az alkalmazás- és folyamatépítők számára. Ez a témakör bemutatja, hogyan exportálhat az [Azure Functions](../azure-functions/functions-overview.md) vagy az Azure App Service használatával készült [API-t.](../app-service/overview.md) Az exportált API *egy egyéni összekötővé*válik, amelyet a PowerApps és a Microsoft Flow ugyanúgy használ, mint egy beépített összekötő.

> [!IMPORTANT]
> Az EBBEN a cikkben látható API-definíciós funkció csak az Azure Functions futásidejű és az App Services-alkalmazások [1.x-es verziója](functions-versions.md#creating-1x-apps) esetén támogatott. A Functions 2.x verziója integrálható az API Management szolgáltatással az OpenAPI-definíciók létrehozásához és karbantartásához. További információ: [OpenAPI-definíció létrehozása az Azure API Management szolgáltatással rendelkező függvényhez](functions-openapi-definition.md)című témakörben olvashat. 

## <a name="create-and-export-an-api-definition"></a>API-definíció létrehozása és exportálása
Api exportálása előtt az API-t egy OpenAPI-definíció (korábbi nevén [Swagger-fájl)](https://swagger.io/) használatával kell leírnia. Ez a definíció tartalmazza az API-ban elérhető műveletekkel kapcsolatos információkat, illetve az API kérés- és válaszadatainak felépítését. A PowerApps és a Microsoft Flow bármely OpenAPI 2.0-definícióhoz létrehozhat egyéni összekötőket. Az Azure Functions és az Azure App Service beépített támogatást nyújt az OpenAPI-definíciók létrehozásához, üzemeltetéséhez és kezeléséhez. További információ: [RESTful API üzemeltetése a CORS szolgáltatással az Azure App Service szolgáltatásban.](../app-service/app-service-web-tutorial-rest-api.md)

> [!NOTE]
> A PowerApps és a Microsoft Flow felhasználói felületén is létrehozhat egyéni összekötőket OpenAPI-definíció használata nélkül. További információt az [Egyéni összekötő (PowerApps) regisztrálása és használata,](https://powerapps.microsoft.com/tutorials/register-custom-api/) valamint [egyéni összekötő (Microsoft Flow) regisztrálása és használata című témakörben talál.](/power-automate/developer/register-custom-api)

Az API-definíció exportálásához hajtsa végre az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com)keresse meg az Azure Functions vagy egy másik App Service-alkalmazás.

    Ha az Azure Functions t használja, válassza ki a függvényalkalmazást, válassza a **Platform funkcióit,** majd **az API-definíciót.**

    ![Az Azure Functions API-definíciója](media/app-service-export-api-to-powerapps-and-flow/api-definition-function.png)

    Az Azure App Service használata esetén válassza ki **az API-definíciót** a beállítási listából.

    ![App Service API-definíciója](media/app-service-export-api-to-powerapps-and-flow/api-definition-app.png)

2. Az **Exportálás a PowerApps + Microsoft Flow** billentyűgombnak elérhetőnek kell lennie (ha nem, akkor először létre kell hoznia egy OpenAPI-definíciót). Az exportálási folyamat megkezdéséhez kattintson erre a gombra.

    ![Exportálás a PowerApps alkalmazásba + Microsoft Flow gomb](media/app-service-export-api-to-powerapps-and-flow/export-apps-flow.png)

3. Válassza az **Exportálási módot:**

    **Az Express** lehetővé teszi az egyéni összekötő létrehozását az Azure Portalon belül. Ehhez be kell jelentkeznie a PowerApps vagy a Microsoft Flow szolgáltatásba, és engedélyt kell adnia összekötők létrehozására a célkörnyezetben. Ez az ajánlott megközelítés, ha ez a két követelmény teljesíthető. Ha ezt a módot használja, kövesse az alábbi [Expressz exportálási](#express) utasításokat.

    **A Kézi** segítségével exportálhatja az API-definíciót, amelyet ezután importál a PowerApps vagy a Microsoft Flow portálok használatával. Ez az ajánlott megközelítés, ha az Azure-felhasználó és az összekötők létrehozására engedéllyel rendelkező felhasználó különböző személyek, vagy ha az összekötőt egy másik Azure-bérlőben kell létrehozni. Ha ezt a módot használja, kövesse az alábbi [Kézi exportálási](#manual) utasításokat.

    ![Exportálási mód](media/app-service-export-api-to-powerapps-and-flow/export-mode.png)

> [!NOTE]
> Az egyéni összekötő az API-definíció *egy példányát* használja, így a PowerApps és a Microsoft Flow nem fogja azonnal tudni, hogy módosítja-e az alkalmazást és annak API-definícióját. Ha módosításokat végez, ismételje meg az exportálási lépéseket az új verzióhoz.

<a name="express"></a>
## <a name="use-express-export"></a>Expressz exportálás használata

Az **exportálás express** módban történő befejezéséhez hajtsa végre az alábbi lépéseket:

1. Győződjön meg arról, hogy be van jelentkezve a PowerApps vagy a Microsoft Flow-bérlő, amelybe exportálni szeretne. 

2. Használja a táblázatban megadott beállításokat.

    |Beállítás|Leírás|
    |--------|------------|
    |**Környezet**|Válassza ki azt a környezetet, amelybe az egyéni összekötőt menteni kell. További információkat a [Környezetek áttekintésében](https://powerapps.microsoft.com/tutorials/environments-overview/) találhat.|
    |**Egyéni API-név**|Adjon meg egy nevet, amelyet a PowerApps és a Microsoft Flow-készítők látni fognak az összekötők listájában.|
    |**Biztonsági konfiguráció előkészítése**|Szükség esetén adja meg az API-hoz való hozzáférés biztosításához szükséges biztonsági konfigurációs adatokat. Ebben a példában egy API-kulcs látható. További információt az alábbi [Hitelesítéstípus megadása című](#auth) témakörben talál.|
 
    ![Expressz exportálás a PowerApps és a Microsoft Flow alkalmazásba](media/app-service-export-api-to-powerapps-and-flow/export-express.png)

3. Kattintson az **OK** gombra. Az egyéni összekötő most épül, és hozzáadja a megadott környezethez.

<a name="manual"></a>
## <a name="use-manual-export"></a>Kézi exportálás használata

Az exportálás **kézi** módban történő befejezéséhez hajtsa végre az alábbi lépéseket:

1. Kattintson **a Fájl letöltése** és mentése gombra, vagy kattintson a másolás gombra, és mentse az URL-címet. Az importálás során a letöltési fájlt vagy az URL-címet fogja használni.
 
    ![Kézi exportálás a PowerApps és a Microsoft Flow alkalmazásba](media/app-service-export-api-to-powerapps-and-flow/export-manual.png)
 
2. Ha az API-definíció tartalmaz biztonsági definíciókat, ezeket #2 lépésben nevezzük ki. Az importálás során a PowerApps és a Microsoft Flow észleli ezeket, és biztonsági információkat kér. Gyűjtse össze az egyes definíciókhoz kapcsolódó hitelesítő adatokat a következő szakaszban való használatra. További információt az alábbi [Hitelesítéstípus megadása című](#auth) témakörben talál.

    ![Kézi exportálás biztonsága](media/app-service-export-api-to-powerapps-and-flow/export-manual-security.png)

    Ez a példa az OpenAPI-definícióban szereplő API-kulcs biztonsági definícióját mutatja be.

Most, hogy exportálta az API-definíciót, importálja, hogy hozzon létre egy egyéni összekötőt a PowerApps ben és a Microsoft Flow-ban. Egyéni összekötők vannak megosztva a két szolgáltatás között, így csak egyszer kell importálnia a definíciót.

Az API-definíció PowerAppsbe és a Microsoft Flow-ba való importálásához hajtsa végre az alábbi lépéseket:

1. Nyissa meg a [powerapps.com](https://web.powerapps.com) vagy a [flow.microsoft.com](https://flow.microsoft.com) webhelyet.

2. A jobb felső sarokban kattintson a fogaskerék ikonra, majd az **Egyéni összekötők parancsra**.

   ![Fogaskerék ikon a szolgáltatásban](media/app-service-export-api-to-powerapps-and-flow/icon-gear.png)

3. Kattintson **az Egyéni összekötő létrehozása**gombra, majd az **OpenAPI-definíció importálása parancsra.**

   ![Egyéni összekötő létrehozása](media/app-service-export-api-to-powerapps-and-flow/flow-apps-create-connector.png)

4. Adja meg az egyéni összekötő nevét, majd keresse meg az exportált OpenAPI-definíciót, és kattintson a **Folytatás**gombra.

   ![OpenAPI-definíció feltöltése](media/app-service-export-api-to-powerapps-and-flow/flow-apps-upload-definition.png)

4. Az **Általános** lapon tekintse át az OpenAPI-definícióból származó információkat.

5. A **Biztonság** lapon, ha a rendszer kéri a hitelesítés részleteinek megadását, adja meg a hitelesítéstípusnak megfelelő értékeket. Kattintson a **Folytatás** gombra.

    ![Biztonság lap](media/app-service-export-api-to-powerapps-and-flow/tab-security.png)

    Ez a példa az API-kulcs hitelesítéséhez szükséges mezőket mutatja be. A mezők a hitelesítés típusától függően eltérőek lehetnek.

6. A **Definíciók** lapon az OpenAPI-fájlban definiált összes művelet automatikusan ki van töltve. Ha az összes szükséges művelet definiálva van, folytassa a következő lépéssel. Ha nem, itt adhat hozzá és módosíthat műveleteket.

    ![Definíciók lap](media/app-service-export-api-to-powerapps-and-flow/tab-definitions.png)

    Ebben a példában `CalculateCosts`van egy művelet, amelynek neve . A metaadatok, **például a Leírás**, mind az OpenAPI-fájlból származnak.

7. Kattintson az **összekötő létrehozása** gombra a lap tetején.

Most már csatlakozhat az egyéni összekötőhöz a PowerApps ben és a Microsoft Flow-ban. Az összekötők PowerApps- és Microsoft Flow-portálokon való létrehozásáról az [Egyéni összekötő (PowerApps) regisztrálása](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector) és [az egyéni összekötő (Microsoft Flow) regisztrálása című](/power-automate/get-started-flow-dev#create-a-custom-connector)témakörben talál további információt.

<a name="auth"></a>
## <a name="specify-authentication-type"></a>A hitelesítés típusának megadása

A PowerApps és a Microsoft Flow támogatja az egyéni összekötők hitelesítését biztosító identitásszolgáltatók gyűjteményét. Ha az API hitelesítést igényel, győződjön meg arról, hogy az OpenAPI-dokumentumbiztonsági _definícióként_ rögzítésre kerül, például a következő példában:

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
Az exportálás során olyan konfigurációs értékeket ad meg, amelyek lehetővé teszik a PowerApps és a Microsoft Flow számára a felhasználók hitelesítését.

Ez a szakasz az **Express** módban támogatott hitelesítési típusokat ismerteti: API-kulcs, Azure Active Directory és Általános OAuth 2.0. A PowerApps és a Microsoft Flow támogatja az alapfokú hitelesítést és az OAuth 2.0-t bizonyos szolgáltatásokhoz, például a Dropboxhoz, a Facebookhoz és a SalesForce-hoz.

### <a name="api-key"></a>API-kulcs
API-kulcs használata esetén az összekötő felhasználóinak meg kell adniuk a kulcsot, amikor kapcsolatot hoznak létre. Megkell adnia egy API-kulcs nevét, hogy segítsen nekik megérteni, hogy melyik kulcsra van szükség. A korábbi példában a nevet `API Key (contact meganb@contoso.com)` használjuk, hogy az emberek tudják, hol kaphatnak információt az API-kulcsról. Az Azure Functions esetében a kulcs általában az egyik gazdakulcs, amely a függvényalkalmazáson belül számos funkciót fed le.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)
Az Azure AD használataesetén két Azure AD-alkalmazásregisztrációra van szükség: egy magára az API-ra, a másikra pedig az egyéni összekötőre:

- Az API regisztrációjának konfigurálásához használja az [App Service hitelesítési/engedélyezési](../app-service/configure-authentication-provider-aad.md) szolgáltatását.

- Az összekötő regisztrációjának konfigurálásához kövesse az [Azure AD-alkalmazás hozzáadása](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)című szakaszlépéseit. A regisztrációnak delegált hozzáféréssel kell `https://msmanaged-na.consent.azure-apim.net/redirect`rendelkeznie az API-hoz és a válasz URL-címéhez. 

További információt az Azure AD regisztrációs példái a [PowerApps](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/) és a Microsoft Flow című [témakörben talál.](https://docs.microsoft.com/connectors/custom-connectors/azure-active-directory-authentication) Ezek a példák az Azure Resource Manager api-t használják; helyettesítheti az API-t, ha követi a lépéseket.

A következő konfigurációs értékek szükségesek:
- **Ügyfélazonosító** – az összekötő Azure AD-regisztrációjának ügyfélazonosítója
- **Ügyféltitok** – az összekötő Azure AD-regisztrációjának ügyféltka-tka
- **Bejelentkezési URL-cím** – az Azure AD alap URL-címe. Az Azure-ban ez `https://login.windows.net`általában .
- **Bérlőazonosító** – a bejelentkezéshez használandó bérlő azonosítója. Ennek "közösnek" vagy annak a bérlőnek az azonosítójának kell lennie, amelyben az összekötő jön létre.
- **Erőforrás URL-címe** – az API-hoz az Azure AD-regisztráció jának url-címe

> [!IMPORTANT]
> Ha valaki más importálja az API-definíciót a PowerApps-be és a Microsoft Flow-ba a manuális folyamat részeként, meg kell adnia nekik az *összekötő regisztrációjának*ügyfélazonosítóját és ügyféltitkát, valamint az API erőforrás-URL-címét. Győződjön meg arról, hogy ezeket a titkos kulcsokat biztonságosan kezelik. **Ne ossza meg magának az API-nak a biztonsági hitelesítő adatait.**

### <a name="generic-oauth-20"></a>Általános OAuth 2.0
Általános OAuth 2.0 használata esetén bármely OAuth 2.0 szolgáltatóval integrálható. Ez lehetővé teszi, hogy olyan egyéni szolgáltatókkal dolgozzon, amelyek nem natív módon támogatottak.

A következő konfigurációs értékek szükségesek:
- **Ügyfélazonosító** - az OAuth 2.0 ügyfélazonosító
- **Ügyféltitok** - az OAuth 2.0 ügyféltitkoskapcsolat
- **Engedélyezési URL** - az OAuth 2.0 engedélyezési URL-címe
- **Token URL-** az OAuth 2.0 token URL-címe
- **URL-cím frissítése** - az OAuth 2.0 frissítési URL-címe


