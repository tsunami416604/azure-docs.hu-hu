---
title: "Hitelesítési és engedélyezési egy egyéni alkalmazás, amely a Azure idő adatsorozat Insights API konfigurálása |} Microsoft Docs"
description: "Ez az oktatóanyag azt ismerteti, hogyan konfigurálhatja a hitelesítési és engedélyezési, amely a Azure idő adatsorozat Insights API-egyéni alkalmazás"
keywords: 
services: time-series-insights
documentationcenter: 
author: dmdenmsft
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/24/2017
ms.author: dmden
ms.openlocfilehash: abc3b5400e6961a798e1e6bf9e4986304046e845
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Hitelesítési és engedélyezési Azure idő adatsorozat Insights API-hoz.

Ez a cikk ismerteti, amely a Azure idő adatsorozat Insights API-egyéni alkalmazás konfigurálása.

## <a name="service-principal"></a>Egyszerű szolgáltatásnév

Ez a szakasz ismerteti a idő adatsorozat Hirdetéselemző API-t nevében az alkalmazás eléréséhez alkalmazások konfigurálása. Az alkalmazás ezután kérdezhet le adatokat, vagy hivatkozási adatok közzététele a idő adatsorozat Insights környezetben alkalmazás hitelesítő adatait, és nem a felhasználó hitelesítő adatait.

Ha egy alkalmazást, amelyet a hozzáférés idejének adatsorozat Insights, állítson be egy Azure Active Directory-alkalmazás, és rendelje hozzá az adat-hozzáférési házirendjeit a idő adatsorozat Insights környezetben. Ez a megközelítés célszerű a saját credentials az alkalmazást futtató, mert:

* Engedélyeket rendelhet a app identitása eltér a saját engedélyeit. Ezek az engedélyek általában korlátozódik, hogy mit az alkalmazás kell tennie. Például engedélyezheti az alkalmazásnak, hogy csak olvasható adatokat egy adott idő adatsorozat Insights környezetben.
* Nem kell módosítani az alkalmazás hitelesítő adatokat, ha az Ön feladatkörei módosítása.
* Egy tanúsítvány vagy egy alkalmazás kulcs segítségével automatizálhatja a hitelesítés, amikor egy felügyelet nélküli parancsfájllal futtatja.

Ez a cikk bemutatja, hogyan hajtsa végre ezeket a lépéseket az Azure portálon keresztül. A single-bérlői alkalmazások, ahol az alkalmazás futtatásához csak egy szervezet célja összpontosít. Single-bérlő alkalmazásokat a szervezet futó üzleti alkalmazásokhoz általában használ.

A telepítő folyamat három magas szintű lépésekből áll:

1. Alkalmazás létrehozása az Azure Active Directoryban.
2. Engedélyezi az alkalmazásnak a idő adatsorozat Insights környezet eléréséhez.
3. Az alkalmazás Azonosítóját és kulcsát használja a jogkivonatot a `"https://api.timeseries.azure.com/"` célközönségtől vagy erőforrástól. A jogkivonat az idő adatsorozat Insights API majd használható.

Részletes lépései a következők:

1. Válassza ki az Azure-portálon **Azure Active Directory** > **App regisztrációk** > **új alkalmazás regisztrációja**.

   ![Új alkalmazás regisztrálása az Azure Active Directoryban](media/authentication-and-authorization/active-directory-new-application-registration.png)  

2. Nevezze el az alkalmazást, jelölje ki a kell **webalkalmazás / API**, válassza ki a bármilyen érvényes URI-JÁNAK **bejelentkezési URL-cím**, és kattintson a **létrehozása**.

   ![Az alkalmazás létrehozása az Azure Active Directoryban](media/authentication-and-authorization/active-directory-create-web-api-application.png)

3. Válassza ki az újonnan létrehozott alkalmazást, és másolja az alkalmazás Azonosítóját a kedvenc szövegszerkesztőjével.

   ![Másolja át az Alkalmazásazonosítót](media/authentication-and-authorization/active-directory-copy-application-id.png)

4. Válassza ki **kulcsok**, adja meg a kulcs nevét, válassza ki a lejárati, és kattintson **mentése**.

   ![Kulcsok – alkalmazás választása](media/authentication-and-authorization/active-directory-application-keys.png)

   ![Adja meg a kulcs nevét és a lejárati, és kattintson a Mentés gombra](media/authentication-and-authorization/active-directory-application-keys-save.png)

5. A kulcs másolása kedvenc szövegszerkesztőjével.

   ![Az alkalmazás kulcs másolása](media/authentication-and-authorization/active-directory-copy-application-key.png)

6. Az idő adatsorozat Insights környezetben, válassza a **adat-hozzáférési házirendjeit** kattintson **Hozzáadás**.

   ![Új adatok házirend hozzáadása a idő adatsorozat Insights környezet](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)

7. Az a **felhasználó kiválasztása** párbeszédpanelen illessze be az alkalmazás neve (a 2. lépés) vagy alkalmazás-azonosító (a 3. lépés).

   ![Alkalmazások keresésére a felhasználó kiválasztása párbeszédpanel](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)

8. Válassza ki a szerepkört (**olvasó** a lekérdezésre adatok, **közreműködő** az adatok lekérdezése és referenciaadatok módosítása), majd **Ok**.

   ![Válassza ki az olvasó vagy közreműködői szerepkör kiválasztása párbeszédpanel](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)

9. A szabályzat mentése kattintva **Ok**.

10. Az alkalmazás nevében jogkivonat használja az alkalmazás-azonosító (a 3. lépés) és az alkalmazás kulcsot (az 5. lépés). A token majd adhatók át a a `Authorization` fejléc, ha az alkalmazás meghívja a idő adatsorozat Hirdetéselemző API-t.

    Használata C#, a következő kód segítségével nevében az alkalmazás jogkivonat. Egy teljes mintát, lásd: [adatait használó C#](time-series-insights-query-data-csharp.md).

    ```csharp
    var authenticationContext = new AuthenticationContext(
        "https://login.microsoftonline.com/common",
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

## <a name="next-steps"></a>Következő lépések

Használja az alkalmazás Azonosítóját és kulcsát az alkalmazásban. Az idő adatsorozat Hirdetéselemző API-t behívó kód a minta, lásd: [adatait használó C#](time-series-insights-query-data-csharp.md).

## <a name="see-also"></a>Lásd még:

* [Lekérdezési API](/rest/api/time-series-insights/time-series-insights-reference-queryapi) a teljes lekérdezés API-referencia
* [Egy egyszerű szolgáltatás létrehozása az Azure-portálon](../azure-resource-manager/resource-group-create-service-principal-portal.md)
