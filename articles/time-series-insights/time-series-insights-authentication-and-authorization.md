---
title: API-hitelesítés és-engedélyezés – Azure Time Series Insights | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan konfigurálható a hitelesítés és engedélyezés egy olyan egyéni alkalmazáshoz, amely meghívja a Azure Time Series Insights API-t.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/23/2019
ms.custom: seodec18
ms.openlocfilehash: 37a409ab28728fe40c5f054d5e9a40cb20774450
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74007116"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Hitelesítés és engedélyezés Azure Time Series Insights API-hoz

Ez a dokumentum azt ismerteti, hogyan regisztrálhat egy alkalmazást Azure Active Directory az új Azure Active Directory panel használatával. A Azure Active Directory regisztrált alkalmazások lehetővé teszik a felhasználók számára, hogy hitelesítsék magukat a Time Series Insights-környezethez társított Azure Time Series Insight API használatával.

## <a name="service-principal"></a>Egyszerű szolgáltatásnév

Az alábbi szakaszok azt ismertetik, hogyan konfigurálható egy alkalmazás a Time Series Insights API-nak egy alkalmazás nevében való eléréséhez. Előfordulhat, hogy az alkalmazás a saját alkalmazás hitelesítő adataival lekérdezi vagy közzéteszi a Time Series Insights környezetben a Azure Active Directory használatával.

## <a name="summary-and-best-practices"></a>Összefoglalás és ajánlott eljárások

A Azure Active Directory alkalmazás regisztrációs folyamata három fő lépést foglal magában.

1. [Alkalmazás regisztrálása](#azure-active-directory-app-registration) Azure Active Directoryban.
1. Engedélyezze, hogy az alkalmazás [hozzáférjen a Time Series Insights-környezethez](#granting-data-access).
1. Az **alkalmazás-azonosító** és az **ügyfél titka** segítségével szerezzen be egy jogkivonatot az [ügyfélalkalmazás](#client-app-initialization)`https://api.timeseries.azure.com/`ból. A jogkivonat ezután felhasználható a Time Series Insights API meghívásához.

A **3. lépésben**az alkalmazás és a felhasználói hitelesítő adatok elkülönítése lehetővé teszi a következőket:

* Rendeljen engedélyeket az alkalmazás identitásához, amely nem azonos a saját engedélyeivel. Ezek az engedélyek jellemzően csak az alkalmazás által igényelt jogosultságokra korlátozódnak. Engedélyezheti például, hogy az alkalmazás csak egy adott Time Series Insights-környezetből olvassa be az adatokat.
* A felhasználó hitelesítési hitelesítő adatainak létrehozása az **ügyfél titkos** vagy biztonsági tanúsítványának használatával elkülönítheti az alkalmazás biztonságát. Ennek eredményeképpen az alkalmazás hitelesítő adatai nem függenek egy adott felhasználó hitelesítő adataitól. Ha a felhasználó szerepköre megváltozik, az alkalmazás nem feltétlenül igényel új hitelesítő adatokat vagy további konfigurálást. Ha a felhasználó megváltoztatja a jelszavát, az alkalmazáshoz való összes hozzáféréshez nincs szükség új hitelesítő adatokra vagy kulcsokra.
* Egy felügyelet nélküli parancsfájlt nem egy adott felhasználó hitelesítő adatai, hanem egy **titkos** vagy egy biztonsági tanúsítvány használatával futtathat.
* Jelszó helyett biztonsági tanúsítványt használjon a Azure Time Series Insights API-hoz való hozzáférés biztonságossá tételéhez.

> [!IMPORTANT]
> A Azure Time Series Insights biztonsági házirend konfigurálásakor kövesse az alábbi, a fenti forgatókönyvben ismertetett **problémáinak elkülönítésének** elvét.

> [!NOTE]
> * A cikk egy egybérlős alkalmazásra koncentrál, amelyben az alkalmazás csak egy szervezeten belül fut.
> * Általában egybérlős alkalmazásokat fog használni a szervezetében futó üzletági alkalmazásokhoz.

## <a name="detailed-setup"></a>Részletes beállítás

### <a name="azure-active-directory-app-registration"></a>Azure Active Directory alkalmazás regisztrálása

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>Adathozzáférés biztosítása

1. Az Time Series Insights-környezethez válassza az **adatelérési házirendek** elemet, majd válassza a **Hozzáadás**lehetőséget.

   [![új adatelérési házirend hozzáadása a Time Series Insights-környezethez](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. A **felhasználó kiválasztása** párbeszédpanelen illessze be az **alkalmazás nevét** vagy az **alkalmazás azonosítóját** a Azure Active Directory-alkalmazás regisztrációja szakaszba.

   [alkalmazás keresése ![a felhasználó kiválasztása párbeszédpanelen](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Válassza ki a szerepkört. Válassza ki az **olvasót** az adatlekérdezéshez vagy a **közreműködőhöz** az adatgyűjtés és a hivatkozási adatváltozások lekérdezéséhez. Kattintson az **OK** gombra.

   [a felhasználói szerepkör kiválasztása párbeszédpanel ![válassza az olvasó vagy közreműködő elemet](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Mentse a szabályzatot az **OK gombra**kattintva.

   > [!TIP]
   > További információ a Time Series Insights-környezethez [való adathozzáférés biztosításáról](./time-series-insights-data-access.md) Azure Active Directoryban.

### <a name="client-app-initialization"></a>Ügyfélalkalmazás inicializálása

1. A jogkivonat az alkalmazás nevében történő beszerzéséhez használja az **alkalmazás-azonosító** és az **ügyfél titkos** kulcsát (application Key) a Azure Active Directory-alkalmazás regisztrációja szakaszban.

    A C#-ben a következő kód beolvashatja a jogkivonatot az alkalmazás nevében. Teljes minta: az [adatlekérdezés a használatával C# ](time-series-insights-query-data-csharp.md).

    ```csharp
    // Enter your Active Directory tenant domain name
    var tenant = "YOUR_AD_TENANT.onmicrosoft.com";
    var authenticationContext = new AuthenticationContext(
        $"https://login.microsoftonline.com/{tenant}",
        TokenCache.DefaultShared);

    AuthenticationResult token = await authenticationContext.AcquireTokenAsync(
        // Set the resource URI to the Azure Time Series Insights API
        resource: "https://api.timeseries.azure.com/",
        clientCredential: new ClientCredential(
            // Application ID of application registered in Azure Active Directory
            clientId: "YOUR_APPLICATION_ID",
            // Application key of the application that's registered in Azure Active Directory
            clientSecret: "YOUR_CLIENT_APPLICATION_KEY"));

    string accessToken = token.AccessToken;
    ```

1. Ezután a jogkivonat átadható a `Authorization` fejlécben, amikor az alkalmazás meghívja a Time Series Insights API-t.

## <a name="common-headers-and-parameters"></a>Gyakori fejlécek és paraméterek

Ez a szakasz a gyakori HTTP-kérelmek fejléceit és paramétereit ismerteti a Time Series Insights GA és az előzetes verziójú API-k használatával végzett lekérdezések végrehajtásához. Az API-specifikus követelmények részletesebben szerepelnek a [Time Series Insights REST API](https://docs.microsoft.com/rest/api/time-series-insights/)dokumentációjában.

### <a name="authentication"></a>Authentication

Ha hitelesített lekérdezéseket szeretne végrehajtani a [Time Series INSIGHTS REST API](https://docs.microsoft.com/rest/api/time-series-insights/)-kkal szemben, egy érvényes OAuth 2,0 tulajdonosi jogkivonatot kell átadni az [engedélyezési fejlécben](/rest/api/apimanagement/2019-01-01/authorizationserver/createorupdate) az Ön által választott Rest-ügyfél C#(Poster, JavaScript,) használatával. 

> [!IMPORTANT]
> A jogkivonatot pontosan ki kell adni a `https://api.timeseries.azure.com/` erőforrásnak (más néven a token célközönségének).
> * A [Poster](https://www.getpostman.com/) **AuthURL** ezért megfelel a következőknek: `https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=https://api.timeseries.azure.com/`

> [!TIP]
> Tekintse meg az üzemeltetett Azure Time Series Insights [ügyfél SDK-minta vizualizációját](https://tsiclientsample.azurewebsites.net/) , amelyből megtudhatja, hogyan végezheti el a hitelesítést az Time Series Insights API-kkal programozott módon a [JavaScript ügyféloldali SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) -val a diagramok és a diagramok használatával.

### <a name="http-headers"></a>HTTP-fejlécek

Szükséges kérések fejlécei:

- `Authorization` a hitelesítéshez és az engedélyezéshez érvényes OAuth 2,0 tulajdonosi jogkivonatot kell átadni az engedélyezési fejlécben. A jogkivonatot pontosan ki kell adni a `https://api.timeseries.azure.com/` erőforrásnak (más néven a token célközönségének).

Nem kötelező kérelmek fejlécei:

- csak `Content-type` `application/json` támogatott.
- `x-ms-client-request-id` – ügyfél-kérelem azonosítója. A szolgáltatás rögzíti ezt az értéket. Lehetővé teszi, hogy a szolgáltatás nyomkövetési műveletet végez a szolgáltatások között.
- `x-ms-client-session-id` – ügyfél-munkamenet-azonosító. A szolgáltatás rögzíti ezt az értéket. Lehetővé teszi, hogy a szolgáltatás a kapcsolódó műveletek egy csoportját nyomon követhessék a szolgáltatások között.
- `x-ms-client-application-name` – a kérelmet létrehozó alkalmazás neve. A szolgáltatás rögzíti ezt az értéket.

Válasz fejlécei:

- csak `Content-type` `application/json` támogatott.
- `x-ms-request-id` kiszolgáló által generált kérelem azonosítója. Felhasználható arra, hogy felvegye a kapcsolatot a Microsofttal egy kérelem kivizsgálására.

### <a name="http-parameters"></a>HTTP-paraméterek

Szükséges URL-lekérdezési karakterlánc paraméterei:

- `api-version=2016-12-12`
- `api-version=2018-11-01-preview`

Nem kötelező URL-lekérdezési karakterlánc paraméterei:

- `timeout=<timeout>` – kiszolgálóoldali időtúllépés a kérelem végrehajtásához. Csak a [környezeti események beolvasása](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api) és a [környezeti összesítések API-k beszerzése](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api) esetén alkalmazható. Az időtúllépési értéknek ISO 8601 időtartam formátumúnak kell lennie, például `"PT20S"`, és a tartományba kell esnie `1-30 s`. Az alapértelmezett érték `30 s`.

## <a name="next-steps"></a>Következő lépések

- A GA Time Series Insights API-t meghívó mintakód: az [adatlekérdezés a C#használatával ](./time-series-insights-query-data-csharp.md).

- Az előzetes verziójú Time Series Insights API-kódokra vonatkozó példákat lásd: az [előzetes verziójú adatlekérdezés használata C# ](./time-series-insights-update-query-data-csharp.md).

- Az API-referenciákkal kapcsolatos információkért lásd: [lekérdezési API-referenciák](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api).

- Megtudhatja, hogyan [hozhat létre egyszerű szolgáltatásnevet](../active-directory/develop/howto-create-service-principal-portal.md).