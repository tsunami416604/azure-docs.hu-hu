---
title: Hitelesítés és engedélyezés az Azure Time Series Insightsban API hogyan |} A Microsoft Docs
description: Ez a cikk ismerteti a hitelesítés és a egy egyéni alkalmazáshoz, amely az Azure Time Series Insights API-hívások engedélyezési konfigurálása.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: seodec18
ms.openlocfilehash: 5fb2802bfe9cc0a4d3297e6fa749e5b94008c616
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65472598"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Hitelesítés és engedélyezés az Azure Time Series Insights API-hoz

Ez a cikk bemutatja, hogyan konfigurálhatja a hitelesítési és engedélyezési egy egyéni alkalmazást az Azure Time Series Insights API meghívásához használt.

> [!TIP]
> További információ [adathozzáférés biztosítása](./time-series-insights-data-access.md) a Time Series Insights-környezetbe az Azure Active Directoryban.

## <a name="service-principal"></a>Szolgáltatásnév

Ez, és a következő szakaszok azt ismertetik, hogyan konfigurálhatja a Time Series Insights API elérése az alkalmazás nevében egy alkalmazás. Az alkalmazás akkor kérdezheti le vagy referenciaadatok közzététele a felhasználói hitelesítő adatok helyett az alkalmazás hitelesítő adatait a Time Series Insights-környezetet.

## <a name="best-practices"></a>Ajánlott eljárások

Ha rendelkezik olyan alkalmazás, amely hozzáférési Time Series Insights kell:

1. Az Azure Active Directory-alkalmazás beállítása.
1. [Az adathozzáférési házirendek hozzárendelése](./time-series-insights-data-access.md) a Time Series Insights-környezetben.

Az alkalmazás a felhasználói hitelesítő adatait helyett célszerű óta:

* Az alkalmazás identitás, amely nem azonos a saját engedélyek engedélyeket rendelhet hozzá. Általában ezek az engedélyek korlátozva csak meg az alkalmazás által igényelt. Engedélyezheti például az alkalmazás csak az egy adott Time Series Insights-környezetben lévő adatok olvasásához.
* Nem kell az alkalmazás hitelesítő adatait, módosíthatja, ha az Ön feladatkörei módosítja.
* Egy tanúsítványt, vagy egy alkalmazáskulcsot segítségével automatizálhatja a hitelesítés, ha felügyelet nélküli szkriptet futtat.

A következő szakaszok bemutatják, hogyan hajthat végre ezeket a lépéseket az Azure Portalon keresztül. A cikk egy egybérlős alkalmazást, ahol az alkalmazás futtatásához csak egy szervezet célja összpontosít. Egybérlős alkalmazások általában az üzleti alkalmazások futtatására, a szervezet fogja használni.

## <a name="set-up-summary"></a>Állítsa be a összegzése

A beállítási folyamat három lépésből áll:

1. Alkalmazás létrehozása az Azure Active Directoryban.
1. Engedélyezi az alkalmazásnak a Time Series Insights-környezet elérésére.
1. Alkalmazás Azonosítóját és kulcsát használjuk a tokenek beszerzésére `https://api.timeseries.azure.com/`. A jogkivonat ezután használható a Time Series Insights API meghívásához.

## <a name="detailed-setup"></a>Részletes beállítása

1. Az Azure Portalon válassza ki a **Azure Active Directory** > **alkalmazásregisztrációk** > **új alkalmazásregisztráció**.

   [![Új alkalmazás regisztrálása az Azure Active Directoryban](media/authentication-and-authorization/active-directory-new-application-registration.png)](media/authentication-and-authorization/active-directory-new-application-registration.png#lightbox)

1. Adja meg az alkalmazás nevét, válassza ki a kívánt **webalkalmazás / API**, bármely érvényes URI-JÁNAK kiválasztása **bejelentkezési URL-**, és kattintson a **létrehozás**.

   [![Az Azure Active Directory-alkalmazás létrehozása](media/authentication-and-authorization/active-directory-create-web-api-application.png)](media/authentication-and-authorization/active-directory-create-web-api-application.png#lightbox)

1. Válassza ki az újonnan létrehozott alkalmazást, és másolja az alkalmazás azonosítója a kedvenc szövegszerkesztőjével.

   [![Az Alkalmazásazonosító másolása](media/authentication-and-authorization/active-directory-copy-application-id.png)](media/authentication-and-authorization/active-directory-copy-application-id.png#lightbox)

1. Válassza ki **kulcsok**, adja meg a kulcs nevét, válassza ki az érvényessége, és kattintson a **mentése**.

   [![Kulcsok – alkalmazás választása](media/authentication-and-authorization/active-directory-application-keys.png)](media/authentication-and-authorization/active-directory-application-keys.png#lightbox)

   [![Adja meg a kulcs nevét és a lejárati, és kattintson a Mentés gombra](media/authentication-and-authorization/active-directory-application-keys-save.png)](media/authentication-and-authorization/active-directory-application-keys-save.png#lightbox)

1. Másolja a kulcsot a kedvenc szövegszerkesztőjével.

   [![Az alkalmazás-kulcs másolása](media/authentication-and-authorization/active-directory-copy-application-key.png)](media/authentication-and-authorization/active-directory-copy-application-key.png#lightbox)

1. Válassza ki a Time Series Insights-környezet **az adathozzáférési házirendek** kattintson **Hozzáadás**.

   [![A Time Series Insights-környezet új adat-hozzáférési szabályzat hozzáadása](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. Az a **felhasználó kiválasztása** párbeszédpanel mezőbe illessze be az alkalmazás nevét (a **2. lépés**) vagy Alkalmazásazonosítót (a **3. lépés**).

   [![Alkalmazás keresése a felhasználó kiválasztása párbeszédpanel](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Válassza ki a szerepkört (**olvasó** adatok lekérdezéséhez **közreműködői** adatok lekérdezése és a referenciaadatok módosítása), és kattintson a **OK**.

   [![Válassza ki az olvasó vagy a közreműködői szerepkör kiválasztása párbeszédpanel](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Mentse a szabályzatot kattintva **OK**.

1. Használja az Alkalmazásazonosítót (a **3. lépés**) és az alkalmazáskulcsot (a **5. lépés**) beszerezni a jogkivonatot az alkalmazás nevében. A jogkivonat ezután adhatók át a `Authorization` fejléc, amikor az alkalmazás meghívja a Time Series Insights API.

    Használata a C#, a következő kódot használhatja az alkalmazás nevében a token beszerzéséhez. A teljes minta: [adatok lekérdezése a C# használatával](time-series-insights-query-data-csharp.md).

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

Használja a **Alkalmazásazonosító** és **kulcs** az alkalmazás az Azure Time Series Insight-hitelesítést.

## <a name="next-steps"></a>További lépések

- Lásd: a Time Series Insights API-mintakód [adatok lekérdezése a C# használatával](time-series-insights-query-data-csharp.md).

- API-referencia információkért lásd: [lekérdezési API-referencia](/rest/api/time-series-insights/ga-query-api).

- Ismerje meg, hogyan [egyszerű szolgáltatás létrehozása](../active-directory/develop/howto-create-service-principal-portal.md).
