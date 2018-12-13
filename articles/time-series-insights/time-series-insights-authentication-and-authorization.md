---
title: Az Azure Time Series Insights-hitelesítés – hitelesítése és engedélyezése az Azure Time Series Insightsban API hogyan |} A Microsoft Docs
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
ms.date: 11/27/2017
ms.custom: seodec18
ms.openlocfilehash: e42a29152f3abb762e6c95833e8e2e5eddd6929b
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53273544"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Hitelesítés és engedélyezés az Azure Time Series Insights API-hoz

Ez a cikk bemutatja, hogyan konfigurálhatja a hitelesítési és engedélyezési egy egyéni alkalmazást az Azure Time Series Insights API meghívásához használt.

## <a name="service-principal"></a>Szolgáltatásnév

Ez a szakasz ismerteti, hogyan konfigurálhatja a Time Series Insights API elérése az alkalmazás nevében egy alkalmazás. Az alkalmazás és kérdezhet le adatokat, vagy referenciaadatok közzététele a felhasználói hitelesítő adatok helyett az alkalmazás hitelesítő adatait a Time Series Insights-környezet.

Ha egy alkalmazás, amely hozzáférési Time Series Insights kell, állítsa be az Azure Active Directory-alkalmazás, és rendelje hozzá az adathozzáférési házirendek a Time Series Insights-környezet. Ez a módszer előnyösebb az alkalmazást a saját hitelesítő adatokkal futtatja, mivel:

* Az alkalmazás identitást, amelyek eltérnek a saját engedélyek engedélyeket rendelhet hozzá. Általában ezek az engedélyek korlátozva csak meg az alkalmazás által igényelt. Engedélyezheti például az alkalmazás csak az egy adott Time Series Insights-környezetben lévő adatok olvasásához.
* Nem kell módosítani az alkalmazás hitelesítő adatait, ha az Ön feladatkörei módosítása.
* Egy tanúsítványt, vagy egy alkalmazáskulcsot segítségével automatizálhatja a hitelesítés, ha felügyelet nélküli szkriptet futtat.

Ez a cikk bemutatja, hogyan hajthat végre ezeket a lépéseket az Azure Portalon keresztül. Egy egybérlős alkalmazást, ahol az alkalmazás futtatásához csak egy szervezet célja összpontosít. Egybérlős alkalmazások az üzleti alkalmazások futtatására, a szervezet általában használnak.

A beállítási folyamat három magas szintű lépésekből áll:

1. Alkalmazás létrehozása az Azure Active Directoryban.
2. Engedélyezi az alkalmazásnak a Time Series Insights-környezet elérésére.
3. A tokenek beszerzésére alkalmazás Azonosítóját és kulcsát használjuk a `"https://api.timeseries.azure.com/"` célközönségtől vagy erőforrástól. A jogkivonat ezután használható a Time Series Insights API meghívásához.

A részletes lépések a következők:

1. Az Azure Portalon válassza ki a **Azure Active Directory** > **alkalmazásregisztrációk** > **új alkalmazásregisztráció**.

   ![Új alkalmazás regisztrálása az Azure Active Directoryban](media/authentication-and-authorization/active-directory-new-application-registration.png)  

2. Adja meg az alkalmazás nevét, válassza ki a kívánt **webalkalmazás / API**, bármely érvényes URI-JÁNAK kiválasztása **bejelentkezési URL-**, és kattintson a **létrehozás**.

   ![Az Azure Active Directory-alkalmazás létrehozása](media/authentication-and-authorization/active-directory-create-web-api-application.png)

3. Válassza ki az újonnan létrehozott alkalmazást, és másolja az alkalmazás azonosítója a kedvenc szövegszerkesztőjével.

   ![Az Alkalmazásazonosító másolása](media/authentication-and-authorization/active-directory-copy-application-id.png)

4. Válassza ki **kulcsok**, adja meg a kulcs nevét, válassza ki az érvényessége, és kattintson a **mentése**.

   ![Kulcsok – alkalmazás választása](media/authentication-and-authorization/active-directory-application-keys.png)

   ![Adja meg a kulcs nevét és a lejárati, és kattintson a Mentés gombra](media/authentication-and-authorization/active-directory-application-keys-save.png)

5. Másolja a kulcsot a kedvenc szövegszerkesztőjével.

   ![Az alkalmazás-kulcs másolása](media/authentication-and-authorization/active-directory-copy-application-key.png)

6. Válassza ki a Time Series Insights-környezet **az adathozzáférési házirendek** kattintson **Hozzáadás**.

   ![A Time Series Insights-környezet új adat-hozzáférési szabályzat hozzáadása](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)

7. Az a **felhasználó kiválasztása** párbeszédpanelen illessze be az alkalmazás nevét (a 2. lépés) vagy Alkalmazásazonosítót (a 3. lépés).

   ![Alkalmazás keresése a felhasználó kiválasztása párbeszédpanel](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)

8. Válassza ki a szerepkört (**olvasó** adatok lekérdezéséhez **közreműködői** adatok lekérdezése és a referenciaadatok módosítása), és kattintson a **Ok**.

   ![Válassza ki az olvasó vagy a közreműködői szerepkör kiválasztása párbeszédpanel](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)

9. Mentse a szabályzatot kattintva **Ok**.

10. Használja az Alkalmazásazonosítót (a 3. lépés) és az alkalmazáskulcsot (az 5. lépés) az alkalmazás nevében a token beszerzéséhez. A jogkivonat ezután adhatók át a `Authorization` fejléc, amikor az alkalmazás meghívja a Time Series Insights API.

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
            clientId: "1bc3af48-7e2f-4845-880a-c7649a6470b8", 
            // Application key of the application that's registered in Azure Active Directory
            clientSecret: "aBcdEffs4XYxoAXzLB1n3R2meNCYdGpIGBc2YC5D6L2="));

    string accessToken = token.AccessToken;
    ```

Használhatja az Azure Time Series Insight-hitelesítést az alkalmazás Azonosítóját és kulcsát az alkalmazásban. 

## <a name="next-steps"></a>További lépések
- Lásd: a Time Series Insights API-mintakód [adatok lekérdezése a C# használatával](time-series-insights-query-data-csharp.md).
- API-referencia információkért lásd: [lekérdezési API-referencia](/rest/api/time-series-insights/time-series-insights-reference-queryapi).

> [!div class="nextstepaction"]
> [Szolgáltatásnév létrehozása](../active-directory/develop/howto-create-service-principal-portal.md)
