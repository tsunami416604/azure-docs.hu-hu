---
title: Azure-ban üzemeltetett API exportálása PowerApps és Microsoft Flow
description: A App Serviceben üzemeltetett API-k közzététele PowerApps és Microsoft Flow
ms.topic: conceptual
ms.date: 04/28/2020
ms.reviewer: sunayv
ms.openlocfilehash: 7d968c62afbfc92952f747e1e7627c98fe07436d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87015085"
---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>Azure-ban üzemeltetett API exportálása PowerApps és Microsoft Flow

A [PowerApps](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/) olyan egyéni üzleti alkalmazások létrehozására és használatára szolgál, amelyek az adataihoz kapcsolódnak, és a különböző platformokon működnek. A [Microsoft flow](/learn/modules/get-started-with-flow/index) megkönnyíti a munkafolyamatok és az üzleti folyamatok automatizálását kedvenc alkalmazásai és szolgáltatásai között. A PowerApps és a Microsoft Flow számos beépített összekötőt kínál az adatforrásokhoz, például az Office 365, a Dynamics 365, a Salesforce és sok más szolgáltatáshoz. Bizonyos esetekben az alkalmazás-és a flow-építők a szervezete által készített adatforrásokhoz és API-khoz is csatlakozni kívánnak.

Hasonlóképpen, az API-kat a szervezeten belül szélesebb körben támogató fejlesztőknek teheti elérhetővé az API-kat az alkalmazások és a flow-építők számára. Ez a cikk bemutatja, hogyan exportálhat [Azure functions](../azure-functions/functions-overview.md) vagy [Azure app Service](../app-service/overview.md)beépített API-t. Az exportált API egy *Egyéni összekötővé*válik, amelyet a rendszer a PowerApps és a Microsoft flow használ, mint a beépített összekötő.

> [!IMPORTANT]
> A cikkben bemutatott API-definíciós funkciók csak az [Azure functions futtatókörnyezet 1. x verziójával és a](functions-versions.md#creating-1x-apps) app Services alkalmazásokkal támogatottak. A függvények 2. x verziója a API Management használatával integrálódik a OpenAPI-definíciók létrehozásához és karbantartásához. További információ: Create a [OpenAPI definition for a függvény az Azure API Management](functions-openapi-definition.md). 

## <a name="create-and-export-an-api-definition"></a>API-definíció létrehozása és exportálása
Az API exportálása előtt le kell írnia az API-t egy OpenAPI-definíció (korábbi nevén egy [hencegő](https://swagger.io/) fájl) használatával. Ez a definíció tartalmazza az API-ban elérhető műveletekkel kapcsolatos információkat, illetve az API kérés- és válaszadatainak felépítését. A PowerApps és a Microsoft Flow bármely OpenAPI 2,0-definícióhoz létrehozhat egyéni összekötőket. A Azure Functions és Azure App Service beépített támogatást nyújt a OpenAPI-definíciók létrehozásához, üzemeltetéséhez és kezeléséhez. További információkért lásd: [REST API üzemeltetése a CORS-ben Azure app Service](../app-service/app-service-web-tutorial-rest-api.md).

> [!NOTE]
> Emellett egyéni összekötőket is létrehozhat a PowerApps és Microsoft Flow felhasználói felületen, anélkül, hogy OpenAPI-definíciót kellene használnia. További információkért lásd: [Egyéni összekötők regisztrálása és használata (PowerApps)](https://powerapps.microsoft.com/tutorials/register-custom-api/) , valamint az [Egyéni összekötők regisztrálása és használata (Microsoft flow)](/power-automate/developer/register-custom-api).

Az API-definíció exportálásához kövesse az alábbi lépéseket:

1. A [Azure Portal](https://portal.azure.com)navigáljon a Function alkalmazáshoz vagy egy app Service alkalmazáshoz.

    A bal oldali menüben az **API**alatt válassza az **API-definíció**elemet.

    :::image type="content" source="media/app-service-export-api-to-powerapps-and-flow/api-definition-function.png" alt-text="Azure Functions API-definíció":::

2. Az **Exportálás PowerApps + Microsoft flow** gombra elérhetőnek kell lennie (ha nem, először létre kell hoznia egy OpenAPI-definíciót). Válassza ezt a gombot az exportálási folyamat megkezdéséhez.

    ![Exportálás PowerApps + Microsoft Flow gombra](media/app-service-export-api-to-powerapps-and-flow/export-apps-flow.png)

3. Válassza ki az **exportálási módot**:

    Az **Express** lehetővé teszi, hogy az egyéni összekötőt a Azure Portal belül hozza létre. Ehhez be kell jelentkeznie a PowerApps vagy a Microsoft Flowba, és rendelkeznie kell engedéllyel összekötők létrehozásához a célként megadott környezetben. Ez a módszer akkor ajánlott, ha ez a két követelmény teljesül. Ha ezt a módot használja, kövesse az alábbi [Express exportálási utasítások használatát](#express) .

    A **manuális** beállítással exportálhatja az API-definíciót, amelyet aztán importálhat a PowerApps vagy a Microsoft flow portál használatával. Ez a módszer akkor ajánlott, ha az Azure-felhasználó és az összekötők létrehozására jogosult felhasználó különböző személyek, vagy ha az összekötőt egy másik Azure-bérlőben kell létrehozni. Ha ezt a módot használja, kövesse az alábbi [manuális exportálási](#manual) utasításokat.

    ![Exportálási mód](media/app-service-export-api-to-powerapps-and-flow/export-mode.png)

> [!NOTE]
> Az egyéni összekötő az API-definíció egy *példányát* használja, így a PowerApps és a Microsoft flow nem fogja tudni azonnal, hogy módosítja-e az alkalmazást és az API-definícióját. Ha változtatásokat hajt végre, ismételje meg az új verzió exportálási lépéseit.

<a name="express"></a>
## <a name="use-express-export"></a>Expressz exportálás használata

Az Exportálás **expressz** módban való végrehajtásához kövesse az alábbi lépéseket:

1. Győződjön meg arról, hogy be van jelentkezve a PowerApps vagy Microsoft Flow-bérlőbe, amelybe exportálni szeretné. 

2. Használja a táblázatban megadott beállításokat.

    |Beállítás|Leírás|
    |--------|------------|
    |**Environment**|Válassza ki azt a környezetet, amelyre az egyéni összekötőt menteni kívánja. További információkat a [Környezetek áttekintésében](https://powerapps.microsoft.com/tutorials/environments-overview/) találhat.|
    |**Egyéni API neve**|Adjon meg egy nevet, amelyet a PowerApps és a Microsoft Flow-építők látnak majd az összekötők listájában.|
    |**Biztonsági konfiguráció előkészítése**|Ha szükséges, adja meg a biztonsági konfigurációs adatokat, amelyek szükségesek ahhoz, hogy hozzáférést biztosítson a felhasználóknak az API-hoz. Ez a példa egy API-kulcsot mutat be. További információ: az alábbi [hitelesítési típus megadása](#auth) .|
 
    ![Expressz exportálás PowerApps és Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-express.png)

3. Kattintson az **OK** gombra. Az egyéni összekötő most már a megadott környezethez lett létrehozva és hozzá lett adva.

<a name="manual"></a>
## <a name="use-manual-export"></a>Manuális exportálás használata

Az Exportálás **manuális** módban való végrehajtásához kövesse az alábbi lépéseket:

1. Kattintson a **Letöltés** elemre, és mentse a fájlt, vagy kattintson a Másolás gombra, és mentse az URL-címet. Az importálás során a fájl letöltése vagy az URL-cím lesz használatban.
 
    ![Manuális exportálás PowerApps és Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-manual.png)
 
2. Ha az API-definíciója tartalmaz bármilyen biztonsági definíciót, a rendszer a következő lépésben hívja fel ezeket a definíciókat: #2. Az importálás során a PowerApps és a Microsoft Flow észleli ezeket a definíciókat, és kéri a biztonsági információkat. Gyűjtse össze az egyes definíciókkal kapcsolatos hitelesítő adatokat a következő szakaszban való használatra. További információ: az alábbi [hitelesítési típus megadása](#auth) .

    ![A manuális exportálás biztonsága](media/app-service-export-api-to-powerapps-and-flow/export-manual-security.png)

    Ez a példa a OpenAPI-definícióban szereplő API-kulcs biztonsági definícióját mutatja be.

Most, hogy exportálta az API-definíciót, importálnia kell egy egyéni összekötő létrehozásához a PowerApps-ben és a Microsoft Flowban. Az egyéni összekötők a két szolgáltatás között vannak megosztva, így csak egyszer kell importálnia a definíciót.

Az API-definíciónak a PowerApps és a Microsoft Flowba történő importálásához kövesse az alábbi lépéseket:

1. Nyissa meg a [powerapps.com](https://web.powerapps.com) vagy a [flow.microsoft.com](https://flow.microsoft.com) webhelyet.

2. A jobb felső sarokban kattintson a fogaskerék ikonra, majd az **Egyéni összekötők**lehetőségre.

   ![Fogaskerék ikon a szolgáltatásban](media/app-service-export-api-to-powerapps-and-flow/icon-gear.png)

3. Kattintson az **egyéni összekötő létrehozása**, majd **az OpenAPI-definíció importálása**elemre.

   ![Egyéni összekötő létrehozása](media/app-service-export-api-to-powerapps-and-flow/flow-apps-create-connector.png)

4. Adja meg az egyéni összekötő nevét, majd keresse meg az exportált OpenAPI-definíciót, majd kattintson a **Folytatás**gombra.

   ![OpenAPI-definíció feltöltése](media/app-service-export-api-to-powerapps-and-flow/flow-apps-upload-definition.png)

4. Az **általános** lapon tekintse át a OpenAPI-definícióból származó információkat.

5. Ha a rendszer a hitelesítés részleteinek megadását kéri a **Biztonság** lapon, adja meg a hitelesítési típushoz megfelelő értékeket. Kattintson a **Folytatás** gombra.

    ![Biztonság lap](media/app-service-export-api-to-powerapps-and-flow/tab-security.png)

    Ez a példa a szükséges mezőket mutatja az API-kulcsos hitelesítéshez. A mezők a hitelesítési típustól függően eltérőek.

6. A **definíciók** lapon a OpenAPI-fájlban meghatározott összes művelet automatikusan ki van töltve. Ha minden szükséges művelet meg van határozva, folytassa a következő lépéssel. Ha nem, itt adhat hozzá és módosíthat műveleteket.

    ![Definíciók lap](media/app-service-export-api-to-powerapps-and-flow/tab-definitions.png)

    Ehhez a példához egy nevű művelet tartozik `CalculateCosts` . A metaadatok, például a **Leírás**a OpenAPI fájlból származnak.

7. Kattintson az **összekötő létrehozása** lehetőségre az oldal tetején.

Most már csatlakozhat az egyéni összekötőhöz a PowerApps-ben és a Microsoft Flowban. Az összekötők PowerApps és Microsoft Flow portálon való létrehozásával kapcsolatos további információkért lásd: [az egyéni összekötő regisztrálása (PowerApps)](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector) és [az egyéni összekötő (Microsoft flow) regisztrálása](/power-automate/get-started-flow-dev#create-a-custom-connector).

<a name="auth"></a>
## <a name="specify-authentication-type"></a>A hitelesítés típusának megadása

A PowerApps és a Microsoft Flow támogatja az egyéni összekötők hitelesítését biztosító identitás-szolgáltatók gyűjteményét. Ha az API hitelesítést igényel, győződjön meg róla, hogy az a OpenAPI-dokumentumban _biztonsági definícióként_ van rögzítve, például az alábbi példához:

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
Az exportálás során olyan konfigurációs értékeket adhat meg, amelyek lehetővé teszik a PowerApps és a Microsoft Flow számára a felhasználók hitelesítését.

Ez a szakasz az **expressz** módban támogatott hitelesítési típusokat ismerteti: API-kulcs, Azure Active Directory és általános OAuth 2,0. A PowerApps és a Microsoft Flow támogatja az alapszintű hitelesítést és a OAuth 2,0-et, például a Dropbox, a Facebook és a SalesForce szolgáltatásokat.

### <a name="api-key"></a>API-kulcs
Ha API-kulcsot használ, az összekötő felhasználóinak meg kell adniuk a kulcsot a kapcsolatok létrehozásakor. Meg kell adnia egy API-kulcs nevét, amely segít megérteni, hogy melyik kulcsra van szükség. A korábbi példában a nevet használjuk, hogy az `API Key (contact meganb@contoso.com)` emberek tudják, hol kaphatnak információt az API-kulcsról. Azure Functions esetén a kulcs általában az egyik gazdagép kulcsa, amely a Function alkalmazásban található több függvényt is magában foglalja.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)
Az Azure AD használatakor két Azure AD-alkalmazás regisztrálása szükséges: egyet az API-hoz, egyet pedig az egyéni összekötőhöz:

- Az API regisztrálásának konfigurálásához használja a [app Service hitelesítés/engedélyezés](../app-service/configure-authentication-provider-aad.md) funkciót.

- Az összekötő regisztrálásának konfigurálásához kövesse az [Azure ad-alkalmazás hozzáadása](../active-directory/develop/quickstart-register-app.md)című témakör lépéseit. A regisztrációhoz delegált hozzáféréssel kell rendelkeznie az API-hoz és a válasz URL-címéhez `https://msmanaged-na.consent.azure-apim.net/redirect` . 

További információkért tekintse meg az Azure AD-beli regisztrációs példákat a [PowerApps](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/) és a [Microsoft flow](/connectors/custom-connectors/azure-active-directory-authentication). Ezek a példák a Azure Resource Manager API-ként használják. Ha követi a lépéseket, cserélje ki az API-t.

A következő konfigurációs értékek szükségesek:
- **Ügyfél-azonosító** – az összekötő Azure ad-regisztrációjának ügyfél-azonosítója
- **Ügyfél titkos kulcsa** – az összekötő Azure ad-regisztrációjának ügyfél-titka
- **Bejelentkezési URL** -cím – az Azure ad alap URL-címe. Az Azure-ban általában `https://login.windows.net` .
- **Bérlő azonosítója** – a bejelentkezéshez használandó bérlő azonosítója. Ennek az AZONOSÍTÓnak a "Common" értéknek kell lennie, vagy annak a bérlőnek az azonosítója, amelyben az összekötőt létrehozták.
- **Erőforrás URL-címe** – az Azure ad-regisztráció erőforrás-URL-címe az API-hoz

> [!IMPORTANT]
> Ha valaki más fogja importálni az API-definíciót a PowerApps-be, és Microsoft Flow a manuális folyamat részeként, meg kell adnia az *összekötő regisztrációjának*ügyfél-azonosítóját és az ügyfél titkos kulcsát, valamint az API erőforrás URL-címét. Győződjön meg arról, hogy a titkos kódok kezelése biztonságos. **Ne ossza meg magával az API biztonsági hitelesítő adatait.**

### <a name="generic-oauth-20"></a>Általános OAuth 2.0
Az általános OAuth 2,0 használata esetén bármilyen OAuth 2,0-szolgáltatóval integrálható. Ez lehetővé teszi az olyan egyéni szolgáltatók használatát, amelyek nem támogatottak natív módon.

A következő konfigurációs értékek szükségesek:
- **Ügyfél-azonosító** – a OAuth 2,0 ügyfél-azonosítója
- **Ügyfél titkos kulcsa** – a OAuth 2,0-ügyfél titka
- **Engedélyezési URL** -cím – a OAuth 2,0 engedélyezési URL-címe
- **Jogkivonat URL-címe** – a OAuth 2,0 token URL-címe
- **URL-cím frissítése** – a OAuth 2,0 frissítési URL-cím
