---
title: A Google-hitelesítés konfigurálása – Azure App Service
description: Megtudhatja, hogyan konfigurálhatja a Google-hitelesítést a App Services alkalmazáshoz.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 4e28f4e330fa24476b717334dfc6d3265640c62a
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70088213"
---
# <a name="how-to-configure-your-app-service-application-to-use-google-login"></a>A App Service alkalmazás konfigurálása a Google bejelentkezés használatára
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Ebből a témakörből megtudhatja, hogyan konfigurálhatja a Azure App Servicet a Google hitelesítési szolgáltatóként való használatához.

A témakörben ismertetett eljárás végrehajtásához rendelkeznie kell egy ellenőrzött e-mail-címmel rendelkező Google-fiókkal. Új Google-fiók létrehozásához látogassa meg az [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302) webhelyet.

## <a name="register"> </a>Alkalmazás regisztrálása a Google-ban
1. Jelentkezzen be a [Azure Portal], és navigáljon az alkalmazáshoz. Másolja az **URL-címet**, amelyet később a Google-alkalmazás konfigurálásához használ.
2. Navigáljon a [Google API](https://go.microsoft.com/fwlink/p/?LinkId=268303) -hoz tartozó webhelyre, jelentkezzen be Google-fiókja hitelesítő adataival, kattintson a **projekt létrehozása**gombra, adja meg a **projekt nevét**, majd kattintson a **Létrehozás**gombra.
3. A projekt létrehozása után válassza ki azt. A projekt Irányítópultján kattintson az **Ugrás az API**-khoz Áttekintés elemre.
4. Válassza **az API-k és szolgáltatások engedélyezése**lehetőséget. Keressen rá a **Google + API**kifejezésre, és válassza ki. Ezután kattintson az **Engedélyezés**gombra.
5. A bal oldali navigációs ablakban **a hitelesítő adatok** > **OAuth**, majd válassza ki az **e-mail-címét**, adja meg a **termék nevét**, majd kattintson a **Save (Mentés**) gombra.
6. A **hitelesítő adatok** lapon kattintson > a **hitelesítő adatok létrehozása** **OAuth ügyfél-azonosító**elemre.
7. Az "ügyfél-azonosító létrehozása" képernyőn válassza a **webalkalmazás**lehetőséget.
8. Illessze be a korábban átmásolt App Service **URL-címet** a megadott **JavaScript**-eredetbe, majd illessze be az átirányítási URI-t a **jóváhagyott átirányítási URI**- Az átirányítási URI az alkalmazás URL-címe, amely a */.auth/login/Google/callback*elérési úttal van hozzáfűzve. Például: `https://contoso.azurewebsites.net/.auth/login/google/callback`. Győződjön meg arról, hogy a HTTPS-sémát használja. Ezt követően kattintson a **Create** (Létrehozás) gombra.
9. A következő képernyőn jegyezze fel az ügyfél-azonosító és az ügyfél titkos kulcsának értékeit.

    > [!IMPORTANT]
    > Az ügyfél titkos kulcsa fontos biztonsági hitelesítő adat. Ezt a titkos kódot Ne ossza meg senkivel, vagy Ossza szét egy ügyfélalkalmazás alkalmazásán belül.


## <a name="secrets"> </a>Google-információk hozzáadása az alkalmazáshoz
1. Térjen vissza a [Azure Portal], navigáljon az alkalmazáshoz. Kattintson a **Beállítások**, majd a **hitelesítés/engedélyezés**elemre.
2. Ha a hitelesítés/engedélyezés funkció nincs engedélyezve, kapcsolja be a kapcsolót **a**következőre:.
3. Kattintson a **Google**elemre. Illessze be az alkalmazás AZONOSÍTÓját és a korábban beszerzett alkalmazás titkos értékeit, és szükség esetén engedélyezze az alkalmazás által igényelt hatóköröket. Ezután kattintson az **OK** gombra.
   
   ![][1]
   
   Alapértelmezés szerint a App Service hitelesítést biztosít, de nem korlátozza a webhely tartalmához és API-khoz való jogosult hozzáférést. Engedélyezni kell a felhasználókat az alkalmazás kódjában.
4. Választható Ha csak a Google által hitelesített felhasználók számára szeretné korlátozni a webhelyhez való hozzáférést, állítsa be a **végrehajtandó műveletet, ha a kérés nincs hitelesítve** a **Google**-ban. Ehhez minden kérelmet hitelesíteni kell, és az összes nem hitelesített kérést a rendszer átirányítja a Google-ba a hitelesítéshez.

> [!CAUTION]
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

