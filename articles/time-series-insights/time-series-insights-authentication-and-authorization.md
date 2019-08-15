---
title: Hitelesítés és engedélyezés a Azure Time Series Insights API használatával | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan konfigurálható a hitelesítés és engedélyezés egy olyan egyéni alkalmazáshoz, amely meghívja a Azure Time Series Insights API-t.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/08/2019
ms.custom: seodec18
ms.openlocfilehash: 602623d48457498963cb5928081d24c1d1132ad4
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935242"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Hitelesítés és engedélyezés Azure Time Series Insights API-hoz

Ez a dokumentum azt ismerteti, hogyan regisztrálhat egy alkalmazást Azure Active Directory az új Azure Active Directory panel használatával. A Azure Active Directory regisztrált alkalmazások lehetővé teszik a felhasználók számára, hogy hitelesítsék magukat a Time Series Insights-környezethez társított Azure Time Series Insight API használatával.

## <a name="service-principal"></a>Szolgáltatásnév

Az alábbi szakaszok azt ismertetik, hogyan konfigurálható egy alkalmazás a Time Series Insights API-nak egy alkalmazás nevében való eléréséhez. Előfordulhat, hogy az alkalmazás a saját alkalmazás hitelesítő adataival lekérdezi vagy közzéteszi a Time Series Insights környezetben a Azure Active Directory használatával.

## <a name="summary-and-best-practices"></a>Összefoglalás és ajánlott eljárások

A Azure Active Directory alkalmazás regisztrációs folyamata három fő lépést foglal magában.

1. [Alkalmazás regisztrálása](#azure-active-directory-app-registration) Azure Active Directoryban.
1. Engedélyezze, hogy az alkalmazás [hozzáférjen a Time Series Insights](#granting-data-access)-környezethez.
1. Az `https://api.timeseries.azure.com/` **alkalmazás-azonosító** és az **ügyfél titka** segítségével szerezzen be tokent az [ügyfélalkalmazás](#client-app-initialization)számára. A jogkivonat ezután felhasználható a Time Series Insights API meghívásához.

A **3. lépésben**az alkalmazás és a felhasználói hitelesítő adatok elkülönítése lehetővé teszi a következőket:

* Rendeljen engedélyeket az alkalmazás identitásához, amely nem azonos a saját engedélyeivel. Ezek az engedélyek jellemzően csak az alkalmazás által igényelt jogosultságokra korlátozódnak. Engedélyezheti például, hogy az alkalmazás csak egy adott Time Series Insights-környezetből olvassa be az adatokat.
* A felhasználó hitelesítési hitelesítő adatainak létrehozása az **ügyfél titkos** vagy biztonsági tanúsítványának használatával elkülönítheti az alkalmazás biztonságát. Ennek eredményeképpen az alkalmazás hitelesítő adatai nem függenek egy adott felhasználó hitelesítő adataitól. Ha a felhasználó szerepköre megváltozik, az alkalmazás nem feltétlenül igényel új hitelesítő adatokat vagy további konfigurálást. Ha a felhasználó megváltoztatja a jelszavát, az alkalmazáshoz való összes hozzáféréshez nincs szükség új hitelesítő adatokra vagy kulcsokra.
* Egy felügyelet nélküli parancsfájlt nem egy adott felhasználó hitelesítő adatai, hanem egy **titkos** vagy egy biztonsági tanúsítvány használatával futtathat.
* Jelszó helyett biztonsági tanúsítványt használjon a Azure Time Series Insights API-hoz való hozzáférés biztonságossá tételéhez.

> [!IMPORTANT]
> A Azure Time Series Insights biztonsági házirend konfigurálásakor kövesse az alábbi, a fenti forgatókönyvben ismertetett problémáinak elkülönítésének elvét.

> [!NOTE]
> * A cikk egy egybérlős alkalmazásra koncentrál, amelyben az alkalmazás csak egy szervezeten belül fut.
> * Általában egybérlős alkalmazásokat fog használni a szervezetében futó üzletági alkalmazásokhoz.

## <a name="detailed-setup"></a>Részletes beállítás

### <a name="azure-active-directory-app-registration"></a>Azure Active Directory alkalmazás regisztrálása

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>Adathozzáférés biztosítása

1. Az Time Series Insights-környezethez válassza az adatelérési **házirendek** elemet, majd válassza a **Hozzáadás**lehetőséget.

   [![Új adathozzáférési szabályzat hozzáadása a Time Series Insights-környezethez](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. A **felhasználó kiválasztása** párbeszédpanelen illessze be az **alkalmazás nevét** vagy az **alkalmazás azonosítóját** a Azure Active Directory-alkalmazás regisztrációja szakaszba.

   [![Alkalmazás keresése a felhasználó kiválasztása párbeszédpanelen](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Válassza ki a szerepkört. Válassza ki az olvasót az adatlekérdezéshez vagy a **közreműködőhöz** az adatgyűjtés és a hivatkozási adatváltozások lekérdezéséhez. Kattintson az **OK** gombra.

   [![Válasszon olvasót vagy közreműködőt a felhasználói szerepkör kiválasztása párbeszédpanelen](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Mentse a szabályzatot az **OK gombra**kattintva.

   > [!TIP]
   > További információ a Time Series Insights-környezethez [való adathozzáférés biztosításáról](./time-series-insights-data-access.md) Azure Active Directoryban.

### <a name="client-app-initialization"></a>Ügyfélalkalmazás inicializálása

1. A jogkivonat az alkalmazás nevében történő beszerzéséhez használja az **alkalmazás-azonosító** és az **ügyfél titkos** kulcsát (application Key) a Azure Active Directory-alkalmazás regisztrációja szakaszban.

    A C#-ben a következő kód beolvashatja a jogkivonatot az alkalmazás nevében. Teljes minta: az adatlekérdezés a [használatával C# ](time-series-insights-query-data-csharp.md).

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

## <a name="next-steps"></a>További lépések

- A GA Time Series Insights API-t meghívó mintakód: az [adatlekérdezés a C#használatával ](./time-series-insights-query-data-csharp.md).

- Az előzetes verziójú Time Series Insights API-kódokra vonatkozó példákat lásd: az [előzetes verziójú adatlekérdezés használata C# ](./time-series-insights-update-query-data-csharp.md).

- Az API-referenciákkal kapcsolatos információkért lásd: [lekérdezési API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api)-referenciák.

- Megtudhatja, hogyan [hozhat létre egyszerű szolgáltatásnevet](../active-directory/develop/howto-create-service-principal-portal.md).