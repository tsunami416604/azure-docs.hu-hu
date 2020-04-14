---
title: API-hitelesítés és -engedélyezés – Azure Time Series Insights | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan konfigurálhatja a hitelesítési és engedélyezési egy egyéni alkalmazás, amely meghívja az Azure Time Series Insights API-t.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 373fc2829e599d0989b59c6386757c8f5f3e1d66
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81251724"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Hitelesítés és engedélyezés az Azure Time Series Insights API-hoz

Ez a dokumentum ismerteti, hogyan regisztrálhat egy alkalmazást az Azure Active Directoryban az új Azure Active Directory panel használatával. Az Azure Active Directoryban regisztrált alkalmazások lehetővé teszik a felhasználók számára, hogy hitelesítsék magukat, és jogosultak legyenek a Time Series Insights-környezethez társított Azure Time Series Insight API használatára.

> [!IMPORTANT]
> Az Azure Time Series Insights mindkét alábbi hitelesítési kódtárat támogatja:
> * A legújabb [Microsoft authentication library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)
> * Az [Azure Active Directory hitelesítési könyvtára (ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

## <a name="service-principal"></a>Szolgáltatásnév

Az alábbi szakaszok ismertetik, hogyan konfigurálhat egy alkalmazást a Time Series Insights API eléréséhez egy alkalmazás nevében. Az alkalmazás ezután lekérdezheti vagy közzéteheti a referenciaadatokat a Time Series Insights környezetben a saját alkalmazáshitelesítő adatainak használatával az Azure Active Directoryhasználatával.

## <a name="summary-and-best-practices"></a>Összefoglalás és bevált módszerek

Az Azure Active Directory alkalmazás regisztrációs folyamat három fő lépésből áll.

1. [Regisztráljon egy alkalmazást](#azure-active-directory-app-registration) az Azure Active Directoryban.
1. Engedélyezze, hogy az alkalmazás [adathozzáféréssel rendelkezzen a Time Series Insights környezethez.](#granting-data-access)
1. Az **alkalmazásazonosító** és az **ügyféltitok** segítségével `https://api.timeseries.azure.com/` jogkivonatot szerezhet be az [ügyfélalkalmazásban.](#client-app-initialization) A jogkivonat ezután a Time Series Insights API-t hívja meg.

**Lépésenként,** az alkalmazás és a felhasználói hitelesítő adatok elkülönítése lehetővé teszi a következőket:

* Rendeljen olyan engedélyeket az alkalmazásidentitáshoz, amelyek eltérnek a saját engedélyeitől. Ezek az engedélyek általában csak az alkalmazás által igényelt engedélyekre korlátozódnak. Például engedélyezheti, hogy az alkalmazás csak egy adott Time Series Insights-környezetből olvassa az adatokat.
* Az **ügyféltitkos adat** vagy biztonsági tanúsítvány használatával elkülönítheti az alkalmazás biztonságát a felhasználó hitelesítési hitelesítő adatainak létrehozásától. Ennek eredményeképpen az alkalmazás hitelesítő adatai nem függnek egy adott felhasználó hitelesítő adataitól. Ha a felhasználó szerepköre megváltozik, az alkalmazás nem feltétlenül igényel új hitelesítő adatokat vagy további konfigurációt. Ha a felhasználó módosítja a jelszavát, az alkalmazáshoz való hozzáféréshez nincs szükség új hitelesítő adatokra vagy kulcsokra.
* Felügyelet nélküli parancsfájl futtatása **ügyféltitkos vagy** biztonsági tanúsítvánnyal, nem pedig egy adott felhasználó hitelesítő adataival (megkövetelve, hogy jelen legyenek).
* Az Azure Time Series Insights API-hoz való biztonságos hozzáféréshez jelszó helyett használjon biztonsági tanúsítványt.

> [!IMPORTANT]
> Kövesse az Azure Time Series Insights biztonsági szabályzatának konfigurálásakor az **aggodalmak elkülönítése** (a fenti forgatókönyvben ismertetett) elvet.

> [!NOTE]
> * A cikk egy egybérlős alkalmazásra összpontosít, ahol az alkalmazás csak egy szervezetben való futtatásra szolgál.
> * Általában egy-bérlős alkalmazásokat fog használni a szervezetben futó üzletági alkalmazásokhoz.

## <a name="detailed-setup"></a>Részletes beállítás

### <a name="azure-active-directory-app-registration"></a>Az Azure Active Directory alkalmazás regisztrációja

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>Adathozzáférés megadása

1. A Time Series Insights környezetben válassza az **Adatelérési házirendek** lehetőséget, és válassza a **Hozzáadás lehetőséget.**

   [![Új adatelérési házirend hozzáadása a Time Series Insights környezethez](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. A **Felhasználó kiválasztása** párbeszédpanelen illessze be az **alkalmazás nevét** vagy az **alkalmazásazonosítót** az Azure Active Directory alkalmazás regisztrációs szakaszából.

   [![Alkalmazás keresése a Felhasználó kiválasztása párbeszédpanelen](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Válassza ki a szerepkört. Válassza a **Reader** lehetőséget az adatok lekérdezéséhez vagy **a közreműködőhöz** az adatok lekérdezéséhez és a referenciaadatok módosításához. Válassza **az OK gombot.**

   [![Az Olvasó vagy a közreműködő kiválasztása a Felhasználói szerepkör kiválasztása párbeszédpanelen](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Mentse a házirendet az **OK**gombra kattintva.

   > [!TIP]
   > A speciális adatelérési lehetőségekhez olvassa el [az adatelérés megadását.](./time-series-insights-data-access.md)

### <a name="client-app-initialization"></a>Ügyfélalkalmazás inicializálása

* A fejlesztők a [Microsoft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview) vagy az [Azure Active Directory hitelesítési könyvtár (ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) segítségével hitelesíthetik magukat az Azure Time Series Insights segítségével.

* Például az ADAL használatával történő hitelesítéshez:

   1. Használja az **alkalmazásazonosító** és **az ügyfélkulcs** (alkalmazáskulcs) az Azure Active Directory alkalmazás regisztrációs szakaszban a jogkivonat beszerzéséhez az alkalmazás nevében.

   1. A C#-ban a következő kód beszerezheti a jogkivonatot az alkalmazás nevében. A teljes minta olvasásához olvassa el [a Lekérdezési adatok c# használatával olvasását.](time-series-insights-query-data-csharp.md)

        [!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs?range=170-199)]

   1. A jogkivonat ezután `Authorization` átadható a fejlécben, amikor az alkalmazás meghívja a Time Series Insights API-t.

* Azt is megteheti, hogy a fejlesztők úgy döntenek, hogy hitelesítik az MSAL használatával. További információ az [MSAL-ra való migrálásról,](https://docs.microsoft.com/azure/active-directory/develop/msal-net-migration) és tekintse meg az [Azure Time Series Insights-környezet ga referenciaadatainak kezelése c#](time-series-insights-manage-reference-data-csharp.md) cikk használatával című témakört. 

## <a name="common-headers-and-parameters"></a>Gyakori fejlécek és paraméterek

Ez a szakasz a Time Series Insights GA és az előzetes verzió API-k lekérdezéséhez használt gyakori HTTP-kérelemfejléceket és paramétereket ismerteti. Az API-specifikus követelményeket részletesebben ismerteti a [Time Series Insights REST API referenciadokumentációja.](https://docs.microsoft.com/rest/api/time-series-insights/)

> [!TIP]
> Olvassa el az [Azure REST API-hivatkozást,](https://docs.microsoft.com/rest/api/azure/) amelyből többet tudhat meg a REST API-k felhasználásáról, a HTTP-kérések lebonyolításáról és a HTTP-válaszok kezeléséről.

### <a name="authentication"></a>Hitelesítés

A [Time Series Insights REST API-k](https://docs.microsoft.com/rest/api/time-series-insights/)hitelesített lekérdezéseinek végrehajtásához egy érvényes OAuth 2.0-s tulajdonosi jogkivonatot kell átadni az Engedélyezési [fejlécben](/rest/api/apimanagement/2019-12-01/authorizationserver/createorupdate) egy ön által választott REST-ügyfél (Postman, JavaScript, C#) használatával. 

> [!TIP]
> Olvassa el a üzemeltetett Azure Time Series Insights [ügyfél SDK-mintavizualizációt,](https://tsiclientsample.azurewebsites.net/) amelyből megtudhatja, hogyan hitelesítheti magát a Time Series Insights API-kkal programozott módon a [JavaScript-ügyfél SDK-val,](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) valamint diagramokkal és grafikonokkal.

### <a name="http-headers"></a>HTTP-fejlécek

A szükséges kérelemfejléceket az alábbiakban ismertetjük.

| Kötelező kérelemfejléc | Leírás |
| --- | --- |
| Engedélyezés | A Time Series Insights segítségével történő hitelesítéshez egy érvényes OAuth 2.0-s tulajdonosi jogkivonatot kell átadni az **Engedélyezés** fejlécben. | 

> [!IMPORTANT]
> A jogkivonatot pontosan `https://api.timeseries.azure.com/` az erőforrásnak kell kiadni (más néven a jogkivonat "közönségének").
> * Az Ön [Postás](https://www.getpostman.com/) **Autinfóka** ezért:`https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=https://api.timeseries.azure.com/`
> * `https://api.timeseries.azure.com/`érvényes, `https://api.timeseries.azure.com` de nem.

A választható kérelemfejléceket az alábbiakban ismertetjük.

| Választható kérelemfejléc | Leírás |
| --- | --- |
| Tartalomtípus | csak `application/json` támogatott. |
| x-ms-client-request-id | Ügyfélkérelem-azonosító. A szolgáltatás rögzíti ezt az értéket. Lehetővé teszi, hogy a szolgáltatás nyomon kövesse a szolgáltatások közötti működést. |
| x-ms-client-session-id | Ügyfélmunkamenet-azonosító. A szolgáltatás rögzíti ezt az értéket. Lehetővé teszi, hogy a szolgáltatás nyomon kövesse a kapcsolódó műveletek egy csoportját a szolgáltatások között. |
| x-ms-client-alkalmazás-név | A kérelmet létrehozó alkalmazás neve. A szolgáltatás rögzíti ezt az értéket. |

A választható, de ajánlott válaszfejléceket az alábbiakban ismertetjük.

| Válasz fejléce | Leírás |
| --- | --- |
| Tartalomtípus | Csak `application/json` támogatott. |
| x-ms-request-id | Kiszolgáló által létrehozott kérelemazonosító. A microsoftos kapcsolatfelvételre használható a kérés kivizsgálásához. |
| x-ms-tulajdonság-nem talált-viselkedés | GA API választható válaszfejléc. A lehetséges `ThrowError` értékek (alapértelmezett) vagy `UseNull`. |

### <a name="http-parameters"></a>HTTP-paraméterek

> [!TIP]
> A szükséges és választható lekérdezési információkról a [referenciadokumentációban](https://docs.microsoft.com/rest/api/time-series-insights/)talál további információt.

A szükséges URL-lekérdezési karakterlánc-paraméterek az API-verziótól függenek.

| Kiadás | Lehetséges API-verzióértékek |
| --- |  --- |
| Általános elérhetőség | `api-version=2016-12-12`|
| Előzetes verzió | `api-version=2018-11-01-preview` |
| Előzetes verzió | `api-version=2018-08-15-preview` |

A választható URL-lekérdezési karakterlánc-paraméterek közé tartozik a HTTP-kérelmek végrehajtási idejének időmeghosszabbítása.

| Nem kötelező lekérdezési paraméter | Leírás | Verzió |
| --- |  --- | --- |
| `timeout=<timeout>` | Kiszolgálóoldali időtúllépések a HTTP-kérelmek végrehajtásához. Csak a [Környezeti események beési és](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api) [a Get Environment Aggregátumok API-k.](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api) Az időtúlértéknek például `"PT20S"` ISO 8601 időtartam formátumban `1-30 s`kell lennie, és a tartományban kell lennie. Az alapértelmezett `30 s`érték a . | FE |
| `storeType=<storeType>` | A meleg tárolóval rendelkező előzetes verziójú környezetekben `WarmStore` a `ColdStore`lekérdezés végrehajtható a vagy a rendszeren. Ez a paraméter a lekérdezésben határozza meg, hogy melyik tárolón kell végrehajtani a lekérdezést. Ha nincs megadva, a lekérdezés a hűtőtárolóban lesz végrehajtva. A meleg tároló lekérdezéséhez a **storeType** típust a számára `WarmStore`kell állítani. Ha nincs megadva, a lekérdezés a hűtőtárolón lesz végrehajtva. | Előzetes verzió |

## <a name="next-steps"></a>További lépések

- A GA Time Series Insights API-t meghívja, mintakód esetén olvassa el a [Lekérdezési adatokat a C# használatával.](./time-series-insights-query-data-csharp.md)

- A Time Series Insights API-kódminták megtekintéséhez olvassa el [a Lekérdezés előzetes adatait a C# használatával.](./time-series-insights-update-query-data-csharp.md)

- Az API-val kapcsolatos referenciainformációkért olvassa el a [Query API referenciadokumentációját.](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api)

- Megtudhatja, hogyan [hozhat létre egyszerű szolgáltatást.](../active-directory/develop/howto-create-service-principal-portal.md)
