---
title: Google-hitelesítés konfigurálása
description: Ismerje meg, hogyan konfigurálhatja a Google-hitelesítést identitásszolgáltatóként az App Service-alkalmazáshoz.
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.topic: article
ms.date: 09/02/2019
ms.custom: seodec18
ms.openlocfilehash: 81ce3e393d308323c8d5a3d688c16c9b45e7be9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74670813"
---
# <a name="configure-your-app-service-app-to-use-google-login"></a>Az App Service alkalmazás konfigurálása a Google bejelentkezési adatainak használatára

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Ez a témakör bemutatja, hogyan konfigurálhatja az Azure App Service-t a Google hitelesítésszolgáltatóként való használatára.

A témakörben szereplő eljárás végrehajtásához ellenőrzött e-mail címmel rendelkezik Google-fiókkal. Új Google-fiók létrehozásához látogassa meg az [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302) webhelyet.

## <a name="register-your-application-with-google"></a><a name="register"> </a>Az alkalmazás regisztrálása a Google-nál

1. Az ügyfélazonosító és az ügyféltitok létrehozásához kövesse a Google dokumentációját a Google Bejelentkezés kori részén [a kiszolgálóoldali alkalmazásokhoz.](https://developers.google.com/identity/sign-in/web/server-side-flow) Nincs szükség kódmódosításra. Csak használja a következő információkat:
    - **Engedélyezett JavaScript Origins**esetén `https://<app-name>.azurewebsites.net` használja az alkalmazás nevét az * \<alkalmazásnév->. *
    - **Az engedélyezett átirányítási**URI `https://<app-name>.azurewebsites.net/.auth/login/google/callback`esetén használja a használatát.
1. Másolja az alkalmazásazonosítót és az alkalmazás titkos értékeit.

    > [!IMPORTANT]
    > Az alkalmazás titkos ítin egy fontos biztonsági hitelesítő adatok. Ezt a titkot ne ossza meg senkivel, és ne ossza el egy ügyfélalkalmazáson belül.

## <a name="add-google-information-to-your-application"></a><a name="secrets"> </a>Google-adatok hozzáadása az alkalmazáshoz

1. Az [Azure Portalon]nyissa meg az App Service-alkalmazást.
1. Válassza a **Beállítások** > **hitelesítése / engedélyezés**lehetőséget, és győződjön meg arról, hogy az **App Service-hitelesítés** be van **kapcsolva.**
1. Válassza a **Google**lehetőséget, majd illessze be a korábban kapott alkalmazásazonosító és alkalmazástitkos értékeket. Engedélyezze az alkalmazás által szükséges hatóköröket.
1. Válassza **az OK gombot.**

   Az App Service hitelesítést biztosít, de nem korlátozza a webhely tartalmához és API-khoz való hivatalos hozzáférést. További információt a [Felhasználók engedélyezése vagy megtagadása](app-service-authentication-how-to.md#authorize-or-deny-users)című témakörben talál.

1. (Nem kötelező) Ha csak a Google által hitelesített felhasználókra szeretné korlátozni a webhelyhez való hozzáférést, állítsa be **a Művelet műveletet, ha a kérés nincs hitelesítve a** Google **számára.** Ha beállítja ezt a funkciót, az alkalmazás megköveteli, hogy minden kérelmet hitelesíteni kell. Emellett átirányítja az összes nem hitelesített kérelmet a Google-hoz hitelesítésre.

    > [!CAUTION]
    > A hozzáférés ily módon történő korlátozása az alkalmazás minden hívására vonatkozik, ami nem feltétlenül kívánatos a nyilvánosan elérhető kezdőlappal rendelkező alkalmazások esetében, mint sok egyoldalas alkalmazásban. Az ilyen alkalmazások esetében **előfordulhat, hogy a névtelen kérelmek engedélyezése (nincs művelet)** előnyben részesíthető, így az alkalmazás manuálisan indítja el magát a hitelesítést. További információt a [Hitelesítési folyamat című témakörben talál.](overview-authentication-authorization.md#authentication-flow)

1. Kattintson a **Mentés** gombra.

Most már készen áll arra, hogy a Google-t használja az alkalmazásban történő hitelesítéshez.

## <a name="next-steps"></a><a name="related-content"> </a>További lépések

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure-portál]: https://portal.azure.com/

