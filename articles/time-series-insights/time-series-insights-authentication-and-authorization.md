---
title: Hitelesítés és engedélyezés az API-k használatával az Azure Time Series Insightsban |} A Microsoft Docs
description: Ez a cikk ismerteti a hitelesítés és a egy egyéni alkalmazáshoz, amely az Azure Time Series Insights API-hívások engedélyezési konfigurálása.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/29/2019
ms.custom: seodec18
ms.openlocfilehash: 899bcffaf3a54bd541d488f99c35ec6721d751ca
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543891"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Hitelesítés és engedélyezés az Azure Time Series Insights API-hoz

Ez a dokumentum ismerteti az alkalmazás regisztrálása az Azure Active Directory használatával, az új Azure Active Directory panel. Az Azure Active Directoryban regisztrált alkalmazás engedélyezése a felhasználók számára a hitelesítést és az Azure Time Series Insight API egy Time Series Insights-környezethez társított használatához engedélyezni kell.

## <a name="service-principal"></a>Egyszerű szolgáltatás

Az alábbi szakaszok ismertetik a Time Series Insights API elérése nevében egy alkalmazás egy alkalmazást. Az alkalmazás akkor kérdezheti le vagy referenciaadatok közzététele a Time Series Insights-környezet saját alkalmazás hitelesítő adatait az Azure Active Directoryn keresztül.

## <a name="summary-and-best-practices"></a>Összegzés és ajánlott eljárások

Az Azure Active Directory alkalmazás regisztrációs folyamat három fő lépésből áll.

1. [Alkalmazás regisztrálása](#azure-active-directory-app-registration) az Azure Active Directoryban.
1. Engedélyezze az alkalmazás számára, hogy rendelkezik [adatokhoz való hozzáférést a Time Series Insights-környezet](#granting-data-access).
1. Használja a **Alkalmazásazonosító** és **titkos Ügyfélkód** származó jogkivonat beszerzésére `https://api.timeseries.azure.com/` a a [ügyfélalkalmazás](#client-app-initialization). A jogkivonat ezután használható a Time Series Insights API meghívásához.

/ **3. lépés**, az alkalmazás és a felhasználói hitelesítő adatok szétválasztása lehetővé teszi, hogy:

* Engedélyek hozzárendelése a alkalmazás identitását, amelyek nem azonosak a saját engedélyeit. Általában ezek az engedélyek korlátozva csak meg az alkalmazás által igényelt. Például engedélyezheti az alkalmazásnak, hogy csak egy adott Time Series Insights-környezet olvassák az adatokat.
* A hitelesítő adatok a felhasználó létrehozása az alkalmazás biztonsági elkülönítés használatával egy **titkos Ügyfélkód** vagy biztonsági tanúsítvány. Ennek eredményeképpen az alkalmazás hitelesítő adatok nem függnek egy adott felhasználó hitelesítő adatait. Ha a felhasználó szerepkörét megváltozik, akkor az alkalmazás nem feltétlenül kell új hitelesítő adatokat, vagy további konfiguráció. Ha a felhasználó megváltoztatja a jelszavát, az összes hozzáférni az alkalmazáshoz nincs szükség új hitelesítő adatokhoz vagy kulcsokhoz.
* Futtatás egy felügyelet nélküli parancsfájl használatával egy **titkos Ügyfélkód** vagy biztonsági tanúsítvány helyett egy adott felhasználó hitelesítő adatait (beágyazás jelen).
* Jelszó helyett egy biztonsági tanúsítvány használatával az Azure Time Series Insights API való biztonságos hozzáférést.

> [!IMPORTANT]
> Kövesse a elvét **elkülönítésének, szempontjai** (lásd a fenti forgatókönyv) Ha az Azure Time Series Insights-biztonsági szabályzat konfigurálása.

> [!NOTE]
> * A cikk egy egybérlős alkalmazást, ahol az alkalmazás futtatásához csak egy szervezet célja összpontosít.
> * Egybérlős alkalmazások általában az üzleti alkalmazások futtatására, a szervezet fogja használni.

## <a name="detailed-setup"></a>Részletes beállítása

### <a name="azure-active-directory-app-registration"></a>Az Azure Active Directory alkalmazásregisztráció

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>Adathozzáférés biztosítása

1. Válassza ki a Time Series Insights-környezet **az adathozzáférési házirendek** válassza **Hozzáadás**.

   [![A Time Series Insights-környezet új adat-hozzáférési szabályzat hozzáadása](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. Az a **felhasználó kiválasztása** párbeszédpanel mezőbe illessze be, vagy a **alkalmazásnév** vagy a **Alkalmazásazonosító** az Azure Active Directory-alkalmazás regisztrációs szakaszban.

   [![Alkalmazás keresése a felhasználó kiválasztása párbeszédpanel](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Válassza ki a szerepkört. Válassza ki **olvasó** adatokat szeretne lekérdezni vagy **közreműködői** kérdezhet le adatokat, és módosítsa a referenciaadatokat. Kattintson az **OK** gombra.

   [![Válassza ki az olvasó és közreműködő a felhasználói szerepkör kiválasztása párbeszédpanel](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Mentse a szabályzatot kiválasztásával **OK**.

   > [!TIP]
   > További információ [adathozzáférés biztosítása](./time-series-insights-data-access.md) a Time Series Insights-környezetbe az Azure Active Directoryban.

### <a name="client-app-initialization"></a>Ügyfél-alkalmazás inicializálása

1. Használja a **Alkalmazásazonosító** és **titkos Ügyfélkód** (Alkalmazáskulcs) az Azure Active Directory alkalmazás regisztrációs szakaszban az alkalmazás nevében a token beszerzéséhez.

    A C#, a következő kódot az alkalmazás nevében a token vásárolhatja meg. A teljes minta: [adatok lekérdezése a C# használatával](time-series-insights-query-data-csharp.md).

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

1. A jogkivonat ezután adhatók át a `Authorization` fejléc, amikor az alkalmazás meghívja a Time Series Insights API.

## <a name="next-steps"></a>További lépések

- Lásd: a GA Time Series Insights API-mintakód [lekérdezés adatait az C# ](./time-series-insights-query-data-csharp.md).

- Előzetes verzió Time Series Insights API-Kódminták, lásd: [lekérdezés előnézeti adatokat az C# ](./time-series-insights-update-query-data-csharp.md).

- API-referencia információkért lásd: [lekérdezési API-referencia](/rest/api/time-series-insights/ga-query-api).

- Ismerje meg, hogyan [egyszerű szolgáltatás létrehozása](../active-directory/develop/howto-create-service-principal-portal.md).