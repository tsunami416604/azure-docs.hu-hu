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
ms.openlocfilehash: 917fa87a0cd0f7b0615a5139a7c15311f866739a
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176966"
---
# <a name="configure-your-app-service-app-to-use-google-login"></a>A App Service alkalmazás konfigurálása a Google bejelentkezés használatára

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Ebből a témakörből megtudhatja, hogyan konfigurálhatja a Azure App Servicet a Google hitelesítési szolgáltatóként való használatához.

A témakörben ismertetett eljárás végrehajtásához rendelkeznie kell egy ellenőrzött e-mail-címmel rendelkező Google-fiókkal. Új Google-fiók létrehozásához látogassa meg az [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302) webhelyet.

## <a name="register"> </a>Alkalmazás regisztrálása a Google-ban

1. Az ügyfél-azonosító és az ügyfél titkos kódjának létrehozásához kövesse a Google-beli Google-dokumentációt a [kiszolgálóoldali alkalmazásokhoz](https://developers.google.com/identity/sign-in/web/server-side-flow) . A kód módosítása nem szükséges. Csak használja a következő információkat:
    - A **JavaScriptek engedélyezéséhez**használja a `https://<app-name>.azurewebsites.net` nevet az alkalmazás nevével *\<app-Name >* .
    - A **hitelesített átirányítási URI**esetében használja az `https://<app-name>.azurewebsites.net/.auth/login/google/callback` értéket.
1. Másolja az alkalmazás AZONOSÍTÓját és az alkalmazás titkos értékeit.

    > [!IMPORTANT]
    > Az alkalmazás titkos kulcsa egy fontos biztonsági hitelesítő adat. Ezt a titkos kódot Ne ossza meg senkivel, vagy Ossza szét egy ügyfélalkalmazás alkalmazásán belül.

## <a name="secrets"> </a>Google-információk hozzáadása az alkalmazáshoz

1. A [Azure Portalra]nyissa meg a app Service alkalmazást.
1. Válassza a **beállítások** > **hitelesítés/engedélyezés**lehetőséget, és győződjön meg arról, hogy a **app Service hitelesítés** **be van kapcsolva**.
1. Válassza a **Google**lehetőséget, majd illessze be a korábban beszerzett alkalmazás-azonosító és alkalmazás titkos értékeit. Engedélyezze az alkalmazás által igényelt hatóköröket.
1. Kattintson az **OK** gombra.

   A App Service hitelesítést biztosít, de nem korlátozza a webhely tartalmához és API-khoz való jogosult hozzáférést. További információ: [felhasználók engedélyezése vagy megtagadása](app-service-authentication-how-to.md#authorize-or-deny-users).

1. Választható Ha csak a Google által hitelesített felhasználók számára szeretné korlátozni a hely elérését, állítsa be **a végrehajtandó műveletet, ha a kérés nincs hitelesítve** a **Google**-ban. Ha beállítja ezt a funkciót, az alkalmazásnak minden kérelmet hitelesítenie kell. Emellett az összes nem hitelesített kérelmet is átirányítja a Google-hoz a hitelesítéshez.

    > [!CAUTION]
    > A hozzáférés ily módon való korlátozása az alkalmazás összes hívására vonatkozik, ami nem kívánatos olyan alkalmazások esetében, amelyek nyilvánosan elérhető kezdőlaptal rendelkeznek, mint sok egyoldalas alkalmazásban. Ilyen alkalmazások esetén **engedélyezze a névtelen kérelmeket (nincs művelet)** előnyben részesített, hogy az alkalmazás manuálisan megkezdse a hitelesítést. További információ: [hitelesítési folyamat](overview-authentication-authorization.md#authentication-flow).

1. Kattintson a **Mentés** gombra.

Most már készen áll a Google használatára a hitelesítéshez az alkalmazásban.

## <a name="related-content"></a>Következő lépések

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure Portalra]: https://portal.azure.com/

