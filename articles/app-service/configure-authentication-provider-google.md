---
title: A Google-hitelesítés konfigurálása – Azure App Service
description: Megtudhatja, hogyan konfigurálhatja a Google-hitelesítést a App Service alkalmazáshoz.
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/02/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: fcbb284a0807ef88c5f40a7c8b65398d45bf73d7
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232147"
---
# <a name="how-to-configure-your-app-service-application-to-use-google-login"></a>A App Service alkalmazás konfigurálása a Google bejelentkezés használatára
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Ebből a témakörből megtudhatja, hogyan konfigurálhatja a Azure App Servicet a Google hitelesítési szolgáltatóként való használatához.

A témakörben ismertetett eljárás végrehajtásához rendelkeznie kell egy ellenőrzött e-mail-címmel rendelkező Google-fiókkal. Új Google-fiók létrehozásához látogassa meg az [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302) webhelyet.

## <a name="register"> </a>Alkalmazás regisztrálása a Google-ban
1. Az ügyfél-azonosító és az ügyfél titkos kódjának létrehozásához kövesse a Google bejelentkezési adatait a [kiszolgálóoldali alkalmazások](https://developers.google.com/identity/sign-in/web/server-side-flow) számára, a következő információkkal (nem szükséges a kód módosításának elvégzése):
    - A **JavaScript**-alapú engedélyezéshez `https://<app-name>.azurewebsites.net` használja az alkalmazás  *\<nevét az App-Name >ban*.
    - A **jóváhagyott átirányítási URI**- `https://<app-name>.azurewebsites.net/.auth/login/google/callback`hoz használja a következőt:.
1. Az ügyfél-azonosító és az ügyfél titkos kulcsainak létrehozása után másolja az értékeket.

    > [!IMPORTANT]
    > Az ügyfél titkos kulcsa fontos biztonsági hitelesítő adat. Ezt a titkos kódot Ne ossza meg senkivel, vagy Ossza szét egy ügyfélalkalmazás alkalmazásán belül.


## <a name="secrets"> </a>Google-információk hozzáadása az alkalmazáshoz
1. A [Azure Portal]navigáljon a app Service alkalmazáshoz. A bal oldali menüben válassza a **hitelesítés/engedélyezés**lehetőséget.
2. Ha a hitelesítés/engedélyezés funkció nincs engedélyezve, kapcsolja be a kapcsolót **a**következőre:.
3. Kattintson a **Google**elemre. Illessze be a korábban beszerzett alkalmazás-azonosítót és az alkalmazás titkos adatait, és opcionálisan engedélyezze az alkalmazás által igényelt hatóköröket. Ezután kattintson az **OK** gombra.

   A App Service hitelesítést biztosít, de nem korlátozza a webhely tartalmához és API-khoz való jogosult hozzáférést. További információ: [felhasználók engedélyezése vagy](app-service-authentication-how-to.md#authorize-or-deny-users)megtagadása.
4. Választható Ha csak a Google által hitelesített felhasználók számára szeretné korlátozni a webhelyhez való hozzáférést, állítsa be a **végrehajtandó műveletet, ha a kérés nincs hitelesítve** a **Google**-ban. Ehhez minden kérelmet hitelesíteni kell, és az összes nem hitelesített kérést a rendszer átirányítja a Google-ba a hitelesítéshez.

    > [!NOTE]
    > A hozzáférés ezen a módon való korlátozása az alkalmazás összes hívására vonatkozik, ami nem kívánatos, ha az alkalmazások nyilvánosan elérhető kezdőlapot szeretnének, például sok egyoldalas alkalmazásban. Ilyen alkalmazások esetén **engedélyezze a névtelen kérelmeket (nincs művelet)** előnyben részesített, ha az alkalmazás manuálisan indítja el a bejelentkezést, az [itt](overview-authentication-authorization.md#authentication-flow)leírtak szerint.
    
5. Kattintson a **Save** (Mentés) gombra.

Most már készen áll a Google használatára a hitelesítéshez az alkalmazásban.

## <a name="related-content"> </a>Related Content
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure Portal]: https://portal.azure.com/

