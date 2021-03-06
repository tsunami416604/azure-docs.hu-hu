---
title: API-hitelesítés és-engedélyezés – Azure Time Series Insights | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan konfigurálható a hitelesítés és engedélyezés egy olyan egyéni alkalmazáshoz, amely meghívja a Azure Time Series Insights API-t.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: shresha
manager: dpalled
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: d1bd3c5796658663b6111723829cbe620346002c
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2020
ms.locfileid: "95016241"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Hitelesítés és engedélyezés az Azure Time Series Insights API-hoz

Ez a dokumentum azt ismerteti, hogyan regisztrálhat egy alkalmazást Azure Active Directory az új Azure Active Directory panel használatával. A Azure Active Directory regisztrált alkalmazások lehetővé teszik a felhasználók számára, hogy hitelesítsék magukat a Azure Time Series Insights-környezethez társított Azure Time Series Insight API használatával.

## <a name="service-principal"></a>Szolgáltatásnév

Az alábbi szakaszok azt ismertetik, hogyan konfigurálható egy alkalmazás a Azure Time Series Insights API-nak egy alkalmazás nevében való eléréséhez. Előfordulhat, hogy az alkalmazás a saját alkalmazás hitelesítő adataival lekérdezi vagy közzéteszi a Azure Time Series Insights környezetben a Azure Active Directory használatával.

## <a name="summary-and-best-practices"></a>Összefoglalás és ajánlott eljárások

A Azure Active Directory alkalmazás regisztrációs folyamata három fő lépést foglal magában.

1. [Alkalmazás regisztrálása](#azure-active-directory-app-registration) Azure Active Directoryban.
1. Engedélyezze, hogy az alkalmazás [hozzáférjen a Azure Time Series Insights-környezethez](#granting-data-access).
1. Az **alkalmazás-azonosító** és az **ügyfél titka** segítségével szerezzen be tokent az `https://api.timeseries.azure.com/` [ügyfélalkalmazás](#client-app-initialization)számára. A jogkivonat ezután felhasználható a Azure Time Series Insights API meghívásához.

A **3. lépésben** az alkalmazás és a felhasználói hitelesítő adatok elkülönítése lehetővé teszi a következőket:

* Rendeljen engedélyeket az alkalmazás identitásához, amely nem azonos a saját engedélyeivel. Ezek az engedélyek jellemzően csak az alkalmazás által igényelt jogosultságokra korlátozódnak. Engedélyezheti például, hogy az alkalmazás csak egy adott Azure Time Series Insights-környezetből olvassa be az adatokat.
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

1. Az Azure Time Series Insights-környezethez válassza az **adatelérési házirendek** elemet, majd válassza a **Hozzáadás** lehetőséget.

   [![Új adathozzáférési szabályzat hozzáadása a Azure Time Series Insights-környezethez](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. A **felhasználó kiválasztása** párbeszédpanelen illessze be az **alkalmazás nevét** vagy az **alkalmazás azonosítóját** a Azure Active Directory-alkalmazás regisztrációja szakaszba.

   [![Alkalmazás keresése a felhasználó kiválasztása párbeszédpanelen](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Válassza ki a szerepkört. Válassza ki az **olvasót** az adatlekérdezéshez vagy a **közreműködőhöz** az adatgyűjtés és a hivatkozási adatváltozások lekérdezéséhez. Válassza az **OK** lehetőséget.

   [![Válasszon olvasót vagy közreműködőt a felhasználói szerepkör kiválasztása párbeszédpanelen](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Mentse a szabályzatot az **OK gombra** kattintva.

   > [!TIP]
   > A speciális adatelérési lehetőségekért olvassa el az [adathozzáférés megadását](./concepts-access-policies.md)ismertetőt.

### <a name="client-app-initialization"></a>Ügyfélalkalmazás inicializálása

* A fejlesztők a [Microsoft Authentication Library (MSAL) használatával végezhetik el a hitelesítést a Azure Time Series Insights.

* Hitelesítés a MSAL használatával:

   1. A jogkivonat az alkalmazás nevében történő beszerzéséhez használja az **alkalmazás-azonosító** és az **ügyfél titkos** kulcsát (application Key) a Azure Active Directory-alkalmazás regisztrációja szakaszban.

   1. A C# nyelvben a következő kód az alkalmazás nevében tudja beszerezni a jogkivonatot. Az adatok Gen1-környezetből való lekérdezésének teljes mintáját a [lekérdezési adatok a C# használatával](time-series-insights-query-data-csharp.md)című témakörben olvashatja.

        A C#-kód eléréséhez tekintse meg a [Azure Time Series Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen1-sample/csharp-tsi-gen1-sample/Program.cs)] tárházat.

   1. Ezután a jogkivonat átadható a `Authorization` fejlécben, amikor az alkalmazás meghívja a Azure Time Series INSIGHTS API-t.

> [!IMPORTANT]
> Ha [Azure Active Directory Authentication Libraryt (ADAL)](../active-directory/azuread-dev/active-directory-authentication-libraries.md) használ, olvassa el a [MSAL-re való áttelepítéssel](../active-directory/develop/msal-net-migration.md)kapcsolatos tudnivalókat.

## <a name="common-headers-and-parameters"></a>Gyakori fejlécek és paraméterek

Ez a szakasz a gyakori HTTP-kérelmek fejléceit és a Azure Time Series Insights Gen1 és Gen2 API-kkal való lekérdezések végrehajtásához használt paramétereket ismerteti. Az API-specifikus követelmények részletesebben szerepelnek a [Azure Time Series Insights REST API](/rest/api/time-series-insights/)dokumentációjában.

> [!TIP]
> A REST API-k használatáról, a HTTP-kérelmek elvégzéséről és a HTTP-válaszok kezeléséről az [Azure REST API dokumentációjában](/rest/api/azure/) olvashat bővebben.

### <a name="authentication"></a>Hitelesítés

Ha hitelesített lekérdezéseket szeretne végrehajtani a [Azure Time Series INSIGHTS REST API](/rest/api/time-series-insights/)-kkal szemben, egy érvényes OAuth 2,0 tulajdonosi jogkivonatot kell átadni az [engedélyezési fejlécben](/rest/api/apimanagement/2019-12-01/authorizationserver/createorupdate) az Ön által választott Rest-ügyfél (Poster, JavaScript, C#) használatával.

> [!TIP]
> Tekintse át az üzemeltetett Azure Time Series Insights [Client SDK minta vizualizációját](https://tsiclientsample.azurewebsites.net/) , amelyből megtudhatja, hogyan végezheti el a hitelesítést az Azure Time Series Insights API-kkal programozott módon a [JavaScript ügyféloldali SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) -val a diagramok és diagramok használatával.

### <a name="http-headers"></a>HTTP-fejlécek

Az alábbiakban a szükséges kérések fejléceit mutatjuk be.

| Kötelező kérelem fejléce | Description |
| --- | --- |
| Engedélyezés | Azure Time Series Insights használatával történő hitelesítéshez érvényes OAuth 2,0 tulajdonosi jogkivonatot kell átadni az **engedélyezési** fejlécben. |

> [!IMPORTANT]
> A jogkivonatot pontosan az erőforráshoz kell kiadni `https://api.timeseries.azure.com/` (más néven a token célközönsége).

> * A [Poster](https://www.getpostman.com/) **AuthURL** ezért a következő lesz: `https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?scope=https://api.timeseries.azure.com//.default`
> * `https://api.timeseries.azure.com/` érvényes, de `https://api.timeseries.azure.com` nem.

Az opcionális kérések fejlécei alább olvashatók.

| Választható kérelemfejléc | Description |
| --- | --- |
| Content-Type | csak `application/json` a támogatott. |
| x-MS-Client-Request-ID | Egy ügyfél-kérelem azonosítója. A szolgáltatás rögzíti ezt az értéket. Lehetővé teszi, hogy a szolgáltatás nyomkövetési műveletet végez a szolgáltatások között. |
| x-MS-Client-Session-ID | Ügyfél-munkamenet-azonosító. A szolgáltatás rögzíti ezt az értéket. Lehetővé teszi, hogy a szolgáltatás a kapcsolódó műveletek egy csoportját nyomon követhessék a szolgáltatások között. |
| x-MS-Client-Application-Name | Annak az alkalmazásnak a neve, amely a kérelmet generálta. A szolgáltatás rögzíti ezt az értéket. |

Nem kötelező, de az ajánlott válasz fejlécek az alábbiakban olvashatók.

| Válasz fejléce | Description |
| --- | --- |
| Content-Type | Csak `application/json` a támogatott. |
| x-MS-Request-ID | Kiszolgáló által generált kérelem azonosítója. Felhasználható arra, hogy felvegye a kapcsolatot a Microsofttal egy kérelem kivizsgálására. |
| x-MS-Property – nem található – viselkedés | A GA API opcionális válaszának fejléce. A lehetséges értékek a következők: `ThrowError` (alapértelmezett) vagy `UseNull` . |

### <a name="http-parameters"></a>HTTP-paraméterek

> [!TIP]
> A szükséges és választható lekérdezési információkkal kapcsolatos további információkért tekintse meg a [dokumentációt](/rest/api/time-series-insights/).

A kötelező URL-lekérdezési karakterlánc paraméterei az API-verziótól függenek.

| Kiadás | Lehetséges API-verziók értékei |
| --- |  --- |
| Gen1 | `api-version=2016-12-12`|
| Gen2 | `api-version=2020-07-31` és `api-version=2018-11-01-preview`|

> [!IMPORTANT]
>
> A `api-version=2018-11-01-preview` verzió hamarosan elavulttá válik. Javasoljuk, hogy a felhasználóknak váltson az újabb verzióra.

A nem kötelező URL-lekérdezési karakterlánc paraméterei közé tartozik a HTTP-kérelem végrehajtási időkorlátjának beállítása.

| Választható lekérdezési paraméter | Leírás | Verzió |
| --- |  --- | --- |
| `timeout=<timeout>` | Kiszolgálóoldali időtúllépés a HTTP-kérelmek végrehajtásához. Csak a [környezeti események beolvasása](/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) és a [környezeti összesítések API-k beszerzése](/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-api) esetén alkalmazható. Az időtúllépési értéknek ISO 8601 időtartam formátumúnak kell lennie, például a tartományon belül kell lennie `"PT20S"` `1-30 s` . Az alapértelmezett érték `30 s`. | Gen1 |
| `storeType=<storeType>` | A meleg tárolást engedélyező Gen2 környezetek esetén a lekérdezés a vagy a rendszeren is végrehajtható `WarmStore` `ColdStore` . A lekérdezésben szereplő paraméter határozza meg, hogy a lekérdezés melyik tárolóban legyen végrehajtva. Ha nincs meghatározva, a rendszer végrehajtja a lekérdezést a hűtőházi tárolón. A meleg tároló lekérdezéséhez be kell állítani a **storeType** `WarmStore` . Ha nincs meghatározva, a rendszer végrehajtja a lekérdezést a hűtőházi tárolón. | Gen2 |

## <a name="next-steps"></a>Következő lépések

* A Gen1 Azure Time Series Insights API-t meghívó mintakód esetében olvassa el a [lekérdezési Gen1 a C# használatával](./time-series-insights-query-data-csharp.md)című részt.

* A Gen2 Azure Time Series Insights API-kód mintáit meghívó mintakód a C# használatával olvassa be a [lekérdezési Gen2](./time-series-insights-update-query-data-csharp.md).

* Az API-referenciákkal kapcsolatos információkért olvassa el a [lekérdezési API](/rest/api/time-series-insights/gen1-query-api) dokumentációját.

* Megtudhatja, hogyan [hozhat létre egyszerű szolgáltatásnevet](../active-directory/develop/howto-create-service-principal-portal.md).