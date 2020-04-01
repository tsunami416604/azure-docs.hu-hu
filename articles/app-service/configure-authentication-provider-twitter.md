---
title: Twitter-hitelesítés konfigurálása
description: Ismerje meg, hogyan konfigurálhatja a Twitter-hitelesítést identitásszolgáltatóként az App Service vagy az Azure Functions alkalmazáshoz.
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.topic: article
ms.date: 02/28/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 3f85f30e0a64b6e39b905fc05503a445aa5876ba
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437998"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-twitter-login"></a>Az App Service vagy az Azure Functions alkalmazás konfigurálása a Twitter bejelentkezési használatához

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Ez a cikk bemutatja, hogyan konfigurálhatja az Azure App Service vagy az Azure Functions a Twitter hitelesítési szolgáltatóként való használata.

A cikkben szereplő eljárás végrehajtásához olyan Twitter-fiókra van szükség, amely ellenőrzött e-mail címmel és telefonszámmal rendelkezik. Új Twitter-fiók létrehozásához nyissa meg [a twitter.com.]

## <a name="register-your-application-with-twitter"></a><a name="register"> </a>Regisztrálja jelentkezését a Twitteren

1. Jelentkezzen be az [Azure Portalon,] és nyissa meg az alkalmazást. Másolja az **URL-címet**. Ezt fogja használni a Twitter alkalmazás konfigurálásához.
1. Nyissa meg a [Twitter Developers] webhelyet, jelentkezzen be twitteres fiókjának hitelesítő adataival, és válassza az **Alkalmazás létrehozása**lehetőséget.
1. Adja meg az **alkalmazás nevét** és az **alkalmazás leírását** az új alkalmazáshoz. Illessze be az alkalmazás **URL-címét** a **Webhely URL-címmezőjébe.** A **Visszahívási URL-címek csoportban** adja meg az App Service-alkalmazás `/.auth/login/twitter/callback`HTTPS-címét, és fűzze hozzá az elérési utat. Például: `https://contoso.azurewebsites.net/.auth/login/twitter/callback`.
1. A lap alján írjon be legalább 100 karaktert a **Mondja el, hogyan fogja használni ezt az alkalmazást,** majd válassza a **Létrehozás lehetőséget.** Kattintson a **Létrehozás** gombra az előugró ablakban. Megjelennek az alkalmazás részletei.
1. Válassza a **Kulcsok és hozzáférési jogkivonatok** lapot.

   Jegyezze fel ezeket az értékeket:
   - API-kulcs
   - API titkos kulcs

   > [!NOTE]
   > Az API titkos kulcs egy fontos biztonsági hitelesítő adatokat. Ne ossza meg ezt a titkot senkivel, és ne ossza el az alkalmazással.

## <a name="add-twitter-information-to-your-application"></a><a name="secrets"> </a>Twitter-információk hozzáadása az alkalmazáshoz

1. Nyissa meg az alkalmazást az [Azure Portalon.]
1. Válassza a **Beállítások** > **hitelesítése / engedélyezés**lehetőséget, és győződjön meg arról, hogy az **App Service-hitelesítés** be van **kapcsolva.**
1. Válassza a **Twitter**lehetőséget.
1. Illessze `API key` be `API secret key` a korábban kapott értékeket és értékeket.
1. Válassza **az OK gombot.**

   ![Képernyőkép a Twitter mobilalkalmazás-beállításairól][1]

   Alapértelmezés szerint az App Service hitelesítést biztosít, de nem korlátozza az engedélyezett hozzáférést a webhely tartalmához és API-khoz. Az alkalmazáskódban engedélyeznie kell a felhasználókat.

1. (Nem kötelező) Ha csak a Twitter által hitelesített felhasználókra szeretné korlátozni a webhelyhez való hozzáférést, állítsa be **a Művelet et, ha a kérés nincs hitelesítve a** **Twitteren.** Ha ezt a funkciót állítja be, az alkalmazás minden kérelem hitelesítését igényli. Emellett átirányítja az összes nem hitelesített kérelmet a Twitterhitelesítésre.

   > [!CAUTION]
   > A hozzáférés ily módon történő korlátozása az alkalmazás minden hívására vonatkozik, ami nem feltétlenül kívánatos a nyilvánosan elérhető kezdőlappal rendelkező alkalmazások esetében, mint sok egyoldalas alkalmazásban. Az ilyen alkalmazások esetében **előfordulhat, hogy a névtelen kérelmek engedélyezése (nincs művelet)** előnyben részesíthető, így az alkalmazás manuálisan indítja el magát a hitelesítést. További információt a [Hitelesítési folyamat című témakörben talál.](overview-authentication-authorization.md#authentication-flow)

1. Kattintson a **Mentés** gombra.

Most már készen áll arra, hogy a Twittert használja az alkalmazáshitelesítéshez.

## <a name="next-steps"></a><a name="related-content"> </a>További lépések

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter fejlesztők]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[twitter.com]: https://go.microsoft.com/fwlink/p/?LinkID=268287
[Azure-portál]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
