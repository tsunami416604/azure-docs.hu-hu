---
title: Twitter-hitelesítés konfigurálása
description: Megtudhatja, hogyan konfigurálhatja a Twitter-hitelesítést identitás-szolgáltatóként a App Service-alkalmazáshoz.
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.topic: article
ms.date: 04/19/2018
ms.custom: seodec18
ms.openlocfilehash: b5ec17c18cec8053f0732366c0cc5d0c5003e4de
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74670799"
---
# <a name="configure-your-app-service-app-to-use-twitter-login"></a>A App Service alkalmazás konfigurálása a Twitter-bejelentkezés használatára

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Ez a cikk bemutatja, hogyan konfigurálhatja a Azure App Servicet a Twitter hitelesítési szolgáltatóként való használatához.

A cikkben ismertetett eljárás végrehajtásához olyan Twitter-fiókra van szükség, amely ellenőrzött e-mail-címmel és telefonszámmal rendelkezik. Új Twitter-fiók létrehozásához nyissa meg a [Twitter.com].

## <a name="register"> </a>Alkalmazás regisztrálása a Twitteren

1. Jelentkezzen be a [Azure Portalra] , és lépjen az alkalmazáshoz. Másolja az **URL-címet**. Ezt fogja használni a Twitter-alkalmazás konfigurálásához.
1. Lépjen a [Twitter-fejlesztők] webhelyére, és jelentkezzen be a Twitter-fiókja hitelesítő adataival, és válassza az **új alkalmazás létrehozása**lehetőséget.
1. Adja meg az új alkalmazás **nevét** és **leírását** . Illessze be az alkalmazás **URL-címét** a **webhely** mezőbe. A **visszahívási URL-cím** mezőben adja meg app Service alkalmazásának URL-címét, és fűzze hozzá az elérési utat `/.auth/login/aad/callback`. Például: `https://contoso.azurewebsites.net/.auth/login/twitter/callback`. Ügyeljen arra, hogy a HTTPS-sémát használja.
1. A lap alján olvassa el és fogadja el a feltételeket. Válassza **a Twitter-alkalmazás létrehozása**lehetőséget. Megjelenik az alkalmazás részletei.
1. Válassza a **Beállítások** lapot, jelölje be az **alkalmazás használatának engedélyezése a Twitteren való bejelentkezéshez**lehetőséget, majd válassza a **beállítások módosítása**lehetőséget.
1. Válassza a **kulcsok és hozzáférési tokenek** fület.

   Jegyezze fel ezeket az értékeket:
   - Fogyasztói kulcs (API-kulcs)
   - Fogyasztói titok (API Secret)

   > [!NOTE]
   > A fogyasztói titok egy fontos biztonsági hitelesítő adat. Ezt a titkos kódot Ne ossza meg senkivel, vagy ossza meg az alkalmazásával.

## <a name="secrets"> </a>Twitter-információk hozzáadása az alkalmazáshoz

1. Nyissa meg az alkalmazást a [Azure Portalra].
1. Válassza a **beállítások** > a **hitelesítés/engedélyezés**lehetőséget, és győződjön meg arról, hogy a **app Service hitelesítés** **be van kapcsolva**.
1. Válassza a **Twitter**lehetőséget.
1. Illessze be a korábban beszerzett `API Key` és `API Secret` értékeket.
1. Kattintson az **OK** gombra.

   ![Képernyőkép a Mobile App Twitter-beállításairól][1]

   Alapértelmezés szerint a App Service hitelesítést biztosít, de nem korlátozza a webhely tartalmához és API-khoz való jogosult hozzáférést. Engedélyezni kell a felhasználókat az alkalmazás kódjában.

1. Választható Ha a webhelyhez való hozzáférést csak a Twitter által hitelesített felhasználókra szeretné korlátozni, állítsa be a **végrehajtandó műveletet, ha a kérés nincs hitelesítve** a **Twitteren**. Ha beállítja ezt a funkciót, az alkalmazásnak minden kérelmet hitelesítenie kell. Emellett az összes nem hitelesített kérelmet is átirányítja a Twitterre a hitelesítéshez.

   > [!CAUTION]
   > A hozzáférés ily módon való korlátozása az alkalmazás összes hívására vonatkozik, ami nem kívánatos olyan alkalmazások esetében, amelyek nyilvánosan elérhető kezdőlaptal rendelkeznek, mint sok egyoldalas alkalmazásban. Ilyen alkalmazások esetén **engedélyezze a névtelen kérelmeket (nincs művelet)** előnyben részesített, hogy az alkalmazás manuálisan megkezdse a hitelesítést. További információ: [hitelesítési folyamat](overview-authentication-authorization.md#authentication-flow).

1. Kattintson a **Mentés** gombra.

Most már készen áll a Twitter használatára a hitelesítéshez az alkalmazásban.

## <a name="related-content"></a>Következő lépések

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter-fejlesztők]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[twitter.com]: https://go.microsoft.com/fwlink/p/?LinkID=268287
[Azure Portalra]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
