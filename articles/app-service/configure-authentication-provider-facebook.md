---
title: Facebook-hitelesítés konfigurálása
description: Ismerje meg, hogyan konfigurálhatja a Facebook-hitelesítést identitás-szolgáltatóként a App Service alkalmazáshoz.
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.topic: article
ms.date: 06/06/2019
ms.custom: seodec18
ms.openlocfilehash: cd9c8a1bab3616b9b4eb1fe97ee3a9b2307ba77b
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671939"
---
# <a name="configure-your-app-service-app-to-use-facebook-login"></a>A App Service-alkalmazás konfigurálása Facebook-Bejelentkezés használatára

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Ez a cikk bemutatja, hogyan konfigurálhatja a Azure App Servicet a Facebook hitelesítési szolgáltatóként való használatához.

A cikkben ismertetett eljárás végrehajtásához olyan Facebook-fiókra van szükség, amely ellenőrzött e-mail-címmel és mobiltelefon-számmal rendelkezik. Új Facebook-fiók létrehozásához nyissa meg a [Facebook.com].

## <a name="register"> </a>Alkalmazás regisztrálása a Facebook-ban

1. Nyissa meg a [Facebook-fejlesztők] webhelyet, és jelentkezzen be Facebook-fiókjának hitelesítő adataival.

   Ha nem rendelkezik Facebook for Developers-fiókkal, kattintson az első **lépések** lehetőségre, és kövesse a regisztrációs lépéseket.
1. Válassza **az alkalmazások** > **új alkalmazás hozzáadása**lehetőséget.
1. A **megjelenítendő név** mezőben:
   1. Adjon egyedi nevet az alkalmazásnak.
   1. Adja meg a **kapcsolattartási E-mail címét**.
   1. Válassza az **alkalmazás-azonosító létrehozása**lehetőséget.
   1. Fejezze be a biztonsági ellenőrzését.

   Megnyílik az új Facebook-alkalmazás fejlesztői irányítópultja.
1. Válassza az **irányítópult** > **Facebook-Bejelentkezés** >  > **web** **beállítása** lehetőséget.
1. A **Facebook-Bejelentkezés**alatt a bal oldali navigációs sávon válassza a **Beállítások**lehetőséget.
1. Az **érvényes OAuth átirányítási URI** -k mezőben adja meg a `https://<app-name>.azurewebsites.net/.auth/login/facebook/callback`. Ne felejtse el lecserélni a `<app-name>`t a Azure App Service alkalmazás nevére.
1. Válassza a **módosítások mentése**lehetőséget.
1. A bal oldali ablaktáblán válassza a **beállítások** > **alapszintű**lehetőséget. 
1. Az **alkalmazás titkos kulcsa** mezőben válassza a **Megjelenítés**lehetőséget. Másolja az alkalmazás- **azonosító** és az **alkalmazás titkos kulcsának**értékeit. Később a App Service alkalmazást az Azure-ban konfigurálhatja.

   > [!IMPORTANT]
   > Az alkalmazás titkos kulcsa egy fontos biztonsági hitelesítő adat. Ezt a titkos kódot Ne ossza meg senkivel, vagy Ossza szét egy ügyfélalkalmazás alkalmazásán belül.
   >

1. Az alkalmazás regisztrálásához használt Facebook-fiók az alkalmazás rendszergazdája. Ezen a ponton csak a rendszergazdák jelentkezhetnek be ebbe az alkalmazásba.

   Más Facebook-fiókok hitelesítéséhez válassza az **alkalmazás-áttekintés** lehetőséget, és engedélyezze, hogy a felhasználók a Facebook-hitelesítéssel hozzáférjenek az alkalmazáshoz a **\<saját alkalmazás-neve > nyilvános** .

## <a name="secrets"> </a>Facebook-információk hozzáadása az alkalmazáshoz

1. Jelentkezzen be a [Azure Portalra] , és navigáljon a app Service alkalmazáshoz.
1. Válassza a **beállítások** > a **hitelesítés/engedélyezés**lehetőséget, és győződjön meg arról, hogy a **app Service hitelesítés** **be van kapcsolva**.
1. Válassza a **Facebook**lehetőséget, majd illessze be a korábban beszerzett alkalmazás-azonosító és alkalmazás titkos értékeit. Engedélyezze az alkalmazás által igényelt hatóköröket.
1. Kattintson az **OK** gombra.

   ![Képernyőkép a Mobile App Facebook beállításairól][0]

    Alapértelmezés szerint a App Service hitelesítést biztosít, de nem korlátozza a webhely tartalmához és API-khoz való jogosult hozzáférést. Engedélyeznie kell a felhasználókat az alkalmazás kódjában.
1. Választható Ha csak a Facebook által hitelesített felhasználók számára szeretné korlátozni a hozzáférést, akkor állítsa be **a végrehajtandó műveletet, ha a kérés nincs hitelesítve** a **Facebookon**. Ha beállítja ezt a funkciót, az alkalmazásnak minden kérelmet hitelesítenie kell. Emellett az összes nem hitelesített kérelmet is átirányítja a Facebookon a hitelesítéshez.

   > [!CAUTION]
   > A hozzáférés ily módon való korlátozása az alkalmazás összes hívására vonatkozik, ami nem kívánatos olyan alkalmazások esetében, amelyek nyilvánosan elérhető kezdőlaptal rendelkeznek, mint sok egyoldalas alkalmazásban. Ilyen alkalmazások esetén **engedélyezze a névtelen kérelmeket (nincs művelet)** előnyben részesített, hogy az alkalmazás manuálisan megkezdse a hitelesítést. További információ: [hitelesítési folyamat](overview-authentication-authorization.md#authentication-flow).

1. Kattintson a **Mentés** gombra.

Most már készen áll a Facebook használatára a hitelesítéshez az alkalmazásban.

## <a name="related-content"></a>Következő lépések

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook-fejlesztők]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure Portalra]: https://portal.azure.com/
