---
title: "Az Azure által üzemeltetett API-k exportálása a PowerApps és Microsoft Flow |} Microsoft Docs"
description: "Hogyan teszi közzé a PowerApps és Microsoft Flow App Service-ben üzemeltetett API"
services: app-service
documentationcenter: 
author: mattchenderson
manager: erikre
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 09/06/2017
ms.author: mahender; mblythe
ms.openlocfilehash: 358c2f7ce568707ea67cfa669de07dc3fb0135f7
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2017
---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>Az Azure által üzemeltetett API-k PowerApps és Microsoft Flow exportálása

[PowerApps](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/) kialakításához, és kapcsolódhat adataihoz, és a platformok közötti használathoz egyéni üzleti alkalmazások szolgáltatás. [Microsoft Flow](https://flow.microsoft.com/guided-learning/learning-introducing-flow/) megkönnyíti a munkafolyamatok és a kedvenc alkalmazások és szolgáltatások közötti üzleti folyamatok automatizálásához. PowerApps és Microsoft Flow is rendelkeznek beépített összekötők különböző adatforrásokhoz, mint az Office 365, Dynamics 365, Salesforce. Bizonyos esetekben alkalmazás és a folyamat rendszerépítők is csatlakoztatható adatforrások és a szervezet által API-k.

Hasonló módon teszi közzé az API-szélesebb körben a szervezeten belül a fejlesztők is elérhetővé az API-alkalmazást, és a folyamat szerkesztők. Ez a témakör bemutatja, hogyan exportálása egy API-val készült [Azure Functions](../azure-functions/functions-overview.md) vagy [Azure App Service](../app-service/app-service-web-overview.md). Az exportált API válik a *egyéni összekötő*, amellyel a PowerApps és Microsoft Flow hasonlóan egy beépített összekötőt.

## <a name="create-and-export-an-api-definition"></a>Hozzon létre és API-definíció exportálása
Az API-k az exportálás előtt le kell írnia az API-t egy OpenAPI definíció használatával (korábbi néven egy [Swagger](http://swagger.io/) fájl). Ez a definíció milyen műveleteket elérhetők az API-k, és hogy a kérés- és az API-adatszerkezet információkat tartalmaz. PowerApps és Microsoft Flow bármely OpenAPI 2.0 definíciójának egyéni összekötőket hozhat létre. Az Azure Functions és Azure App Service rendelkezik létrehozására, üzemeltető és OpenAPI definíciók kezelésére beépített támogatása. További információkért lásd: [RESTful API létrehozása az Azure Web Apps](../app-service/app-service-web-tutorial-rest-api.md).

> [!NOTE]
> A PowerApps és a Microsoft Flow UI, az egyéni összekötők egy OpenAPI definíciója használata nélkül is létrehozható. További információkért lásd: [regisztrálása és -felhasználási (PowerApps) egyéni összekötő](https://powerapps.microsoft.com/tutorials/register-custom-api/) és [regisztrálása és -felhasználási (Microsoft Flow) egyéni összekötő](https://flow.microsoft.com/documentation/register-custom-api/).

Az API-definíció exportálásához kövesse az alábbi lépéseket:

1. Az a [Azure-portálon](https://portal.azure.com), keresse meg az Azure Functions vagy egy másik App Service-alkalmazás.

    Ha az Azure Functions használatával, jelölje ki a függvény alkalmazást, **Platform funkciói**, majd **API-definíció**.

    ![Az Azure Functions API-definíció](media/app-service-export-api-to-powerapps-and-flow/api-definition-function.png)

    Ha használja az Azure App Service, válassza ki a **API-definíció** beállítások listájáról.

    ![Az App Service API definition](media/app-service-export-api-to-powerapps-and-flow/api-definition-app.png)

2. A **exportálja a PowerApps és Microsoft Flow** gomb elérhetőnek kell lennie (Ha nem, először létre kell hoznia egy OpenAPI-definíciója). Kattintson erre a gombra kattintva az exportálási folyamat megkezdéséhez.

    ![A PowerApps és Microsoft Flow Exportálás gomb](media/app-service-export-api-to-powerapps-and-flow/export-apps-flow.png)

3. Válassza ki a **Exportálás módja**:

    **Express** készíthet az egyéni összekötő az Azure portálon. Bejelentkezett a powerapps segítségével vagy a Microsoft Flow, és hozhat létre összekötőket célkörnyezetben igényel. Ez az az ajánlott módszer, ha a két követelménynek érheti el. Ha ezt a módot használ, kövesse a [expressz exportálással](#express) az alábbi utasításokat.

    **Manuális** lehetővé teszi az API-definíciót, amely exportálása, majd importálja a PowerApps és Microsoft Flow portálokon használatával. Ez az ajánlott módszer az, ha az Azure felhasználói és összekötők létrehozásához engedéllyel rendelkező felhasználók más személyeknek, vagy az összekötő kell létrehozni egy másik Azure-bérlőhöz. Ha ezt a módot használ, kövesse a [manuális exportálással](#manual) az alábbi utasításokat.

    ![Exportálás módja](media/app-service-export-api-to-powerapps-and-flow/export-mode.png)

> [!NOTE]
> Az egyéni összekötő egy *másolási* az API-definíció, PowerApps és Microsoft Flow nem azonnal megtudják, ha módosítja az alkalmazás és annak API-definíció. Ha módosítja, ismételje meg az Exportálás az új verzióhoz tartozó.

<a name="express"></a>
## <a name="use-express-export"></a>Az expressz exportálással

Az exportálás végrehajtásához **Express** mód, kövesse az alábbi lépéseket:

1. Győződjön meg arról, hogy a powerapps segítségével vagy a Microsoft Flow-bérlőjéhez tartozik, amelyhez az exportálni kívánt van bejelentkezve. 

2. A táblázatban megadott beállításokkal.

    |Beállítás|Leírás|
    |--------|------------|
    |**Környezet**|Válassza ki a környezetben, ami az egyéni összekötő menteni kell. További információkért lásd: [környezetek – áttekintés](https://powerapps.microsoft.com/tutorials/environments-overview/).|
    |**Egyéni API neve**|Adjon meg egy nevet, mely PowerApps és Microsoft Flow rendszerépítők megjelenik-e az összekötő listában.|
    |**Készítse elő a biztonsági beállítások**|Szükség esetén adja meg az API-hoz való hozzáférés engedélyezése a felhasználók számára szükséges biztonsági konfigurációs adatait. Ez a példa bemutatja egy API-kulcsot. További információkért lásd: [adja meg a hitelesítési típus](#auth) alatt.|
 
    ![Exportálás PowerApps és Microsoft Flow Express](media/app-service-export-api-to-powerapps-and-flow/export-express.png)

3. Kattintson az **OK** gombra. Az egyéni összekötő most már a beépített, és felveszi a megadott környezetbe.

A példák **Express** mód az Azure Functions, lásd: [meghívni a függvényt a PowerApps](functions-powerapps-scenario.md) és [meghívni a függvényt a Microsoft Flow](functions-flow-scenario.md).

<a name="manual"></a>
## <a name="use-manual-export"></a>Manuális exportálással

Az exportálás végrehajtásához **manuális** mód, kövesse az alábbi lépéseket:

1. Kattintson a **letöltése** és mentse a fájlt, vagy kattintson a Másolás gombra, és mentse az URL-címet. Az importálás során a letöltendő fájl vagy az URL-címet fogja használni.
 
    ![Exportálni abba a PowerApps és Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-manual.png)
 
2. Ha az API-definíció bármilyen biztonsági definíciók tartalmaz, ezek az úgynevezett a #2. Importálás során PowerApps és Microsoft Flow ezek és megállapítja biztonsági információt. Gyűjtse össze a hitelesítő adatokat használja a következő szakasz mindegyik definíciója kapcsolódik. További információkért lásd: [adja meg a hitelesítési típus](#auth) alatt.

    ![Biztonsági manuális exporthoz](media/app-service-export-api-to-powerapps-and-flow/export-manual-security.png)

    Ez a példa bemutatja a OpenAPI definícióban szereplő API-kulcs biztonsági definíció.

Most, hogy az API-definíció exportált, importálja azt a PowerApps és Microsoft Flow egyéni összekötő létrehozásához. Az alábbi példában a powerapps segítségével, de egyéni összekötők a két szolgáltatás, így csak egyszer a-definíciójának importálásához között vannak megosztva.

Az API-definíció PowerApps és Microsoft Flow importálásához kövesse az alábbi lépéseket:

1. Jelentkezzen be a [web.powerapps.com](https://web.powerapps.com) vagy [flow.microsoft.com](https://flow.microsoft.com/). 

2. Kattintson a **beállítások** (fogaskerék ikonra) gombra a képernyő jobb felső sarkában a lapot, és válassza ki a **egyéni összekötők**.

    ![egyéni összekötők](media/app-service-export-api-to-powerapps-and-flow/custom-connectors.png)

3. Kattintson a **hozzon létre egyéni összekötő**.

4. Az a **általános** lapon adjon meg egy nevet az API-, és majd töltse fel a OpenAPI definition vagy illessze be a metaadatainak URL-CÍMÉT. Kattintson a **feltöltése**, majd **továbbra is**.

    ![Általános lap](media/app-service-export-api-to-powerapps-and-flow/tab-general.png)

5. Az a **biztonsági** lapon, ha a rendszer felszólítja a hitelesítési részletesen adja meg a hitelesítési típushoz megfelelő értékeket. Kattintson a **továbbra is**.

    ![Biztonság lap](media/app-service-export-api-to-powerapps-and-flow/tab-security.png)

    Ez a példa bemutatja a kötelező mezőket API kulcsos hitelesítéséhez. A mezők eltérőek lehetnek attól függően, hogy a hitelesítési típus.

6. Az a **definíciók** lapon, a OpenAPI fájlban meghatározott összes műveletek esetében, automatikus feltöltve. Ha a szükséges műveletek vannak meghatározva, megnyithatja a következő lépéssel. Ha nem, akkor adja hozzá, és itt műveletek módosítása.

    ![Definíciók lap](media/app-service-export-api-to-powerapps-and-flow/tab-definitions.png)

    Ebben a példában egy művelet nevű rendelkezik `CalculateCosts`. A metaadatokat, például **leírás**, az összes OpenAPI fájlból származik.

7. Kattintson a **-összekötő létrehozása** az oldal tetején.

Az egyéni összekötő PowerApps és Microsoft Flow is csatlakozhat. A PowerApps és Microsoft Flow portálokon összekötők létrehozásáról további információk: [az egyéni connector (PowerApps) regisztrálása](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector) és [az egyéni connector (a Microsoft-Flow) regisztrálása](https://flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector).

<a name="auth"></a>
## <a name="specify-authentication-type"></a>Adja meg a hitelesítés típusa

PowerApps és Microsoft Flow támogatja az egyéni összekötők hitelesítésre identitás-szolgáltatóktól gyűjteménye. Ha az API-hitelesítés szükséges, győződjön meg arról, mint a rögzítés egy _biztonsági definíciójának_ a OpenAPI dokumentum, az alábbi példához hasonló:

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
Exportálás során, amelyek lehetővé teszik a felhasználók hitelesítésére PowerApps és Microsoft Flow konfigurációs értékeket ad meg.

Ez a fejezet a hitelesítési típus által támogatott **Express** mód: API key, az Azure Active Directory és a általános OAuth 2.0-s. PowerApps és Microsoft Flow is támogatja az egyszerű hitelesítés és az OAuth 2.0 adott szolgáltatások, például a dropbox-ba, a Facebook-on és a SalesForce.

### <a name="api-key"></a>Az API-kulcs
API-kulcs használata esetén a rendszer kéri a felhasználókat az összekötő a kulcs megadására, amikor azok a kapcsolat létrehozása. Megadhatja az API-kulcsnév felhasználóinál megérteni, hogy melyik kulcs szükséges. A korábbi példában használjuk a neve `API Key (contact meganb@contoso.com)` , személyek tudja, honnan szerezhetők be az API-kulccsal kapcsolatos információkat. Az Azure Functions a kulcs egyike általában az állomások kulcsait, a függvény alkalmazásban több funkciók lefedik.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)
Az Azure AD, ha szüksége van-e két az Azure AD alkalmazás-regisztrációk: egyet az API-t, és az egyéni összekötő egy:

- Az API-regisztrációs konfigurálásához használja a [App Service hitelesítési/engedélyezési](../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md) szolgáltatás.

- Regisztráció az összekötő konfigurálásához kövesse a [hozzáadása egy Azure AD-alkalmazást](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-an-application). A regisztrációs kell delegált hozzáférés az API-t és a válasz URL- `https://msmanaged-na.consent.azure-apim.net/redirect`. 

További információkért lásd: az Azure AD regisztrációs példák [PowerApps](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/) és [Microsoft Flow](https://flow.microsoft.com/documentation/customapi-azure-resource-manager-tutorial/). Ezekben a példákban az Azure Resource Manager használata az API-t; helyettesítse be az API-t, ha a lépések végrehajtásával.

A következő konfigurációs értékeket szükségesek:
- **Ügyfél-azonosító** – az ügyfél-azonosító az Azure AD-regisztrációval összekötője
- **Titkos ügyfélkulcsot** – a titkos ügyfélkulcs az Azure AD-regisztrációval összekötője
- **Bejelentkezési URL-cím** -alap URL-cím, az Azure AD. Azure-ban ez az általában `https://login.windows.net`.
- **A bérlői azonosító** – a bérlő által használt bejelentkezési azonosító. Ez legyen "általános" vagy az összekötő létrehozása, amelyben a bérlő azonosítója.
- **Forrás URL-cím** – az Azure AD-regisztrációval az API-erőforrás URL-címe

> [!IMPORTANT]
> Ha valaki más importálja az API-definíció PowerApps és Microsoft Flow a manuális folyamat részeként, meg kell adnia azokat az ügyfél-azonosító és a titkos ügyfélkulcs a *összekötő regisztrálásának*, valamint az API-erőforrás URL-CÍMÉT. Győződjön meg arról, hogy ezeknek a kulcsoknak biztonságosan kezeli-e. **Az API-t a biztonsági hitelesítő adatokat nem osztják meg.**

### <a name="generic-oauth-20"></a>Általános OAuth 2.0
Általános OAuth 2.0 használatakor integrálható bármely OAuth 2.0-s szolgáltató. Ez lehetővé teszi, hogy egyéni szolgáltatók nem natív módon támogatott alkalmazásában.

A következő konfigurációs értékeket szükségesek:
- **Ügyfél-azonosító** -az OAuth 2.0 ügyfél-azonosító
- **Titkos ügyfélkulcsot** -az OAuth 2.0 ügyfél titkos kulcs
- **Engedélyezési URL-címet** -az OAuth 2.0-engedélyezési URL-cím
- **A token URL-cím** -OAuth 2.0 token URL-címe
- **Frissítse az URL-cím** -az OAuth 2.0-s frissítési URL-cím


