---
title: Facebook-hitelesítés konfigurálása
description: További információ arról, hogyan konfigurálhatja a Facebook-hitelesítést identitásszolgáltatóként az App Service vagy az Azure Functions alkalmazáshoz.
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.topic: article
ms.date: 06/06/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 9a2d390a5647ed90284730e9186e981b8e699d10
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80438022"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-facebook-login"></a>Az App Service vagy az Azure Functions alkalmazás konfigurálása a Facebook bejelentkezési funkciójának használatára

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Ez a cikk bemutatja, hogyan konfigurálhatja az Azure App Service vagy az Azure Functions a Facebook hitelesítési szolgáltatóként való használata.

A cikkben szereplő eljárás végrehajtásához olyan Facebook-fiókra van szükség, amely ellenőrzött e-mail címmel és mobiltelefonszámmal rendelkezik. Új Facebook-fiók létrehozásához nyissa meg [a facebook.com.]

## <a name="register-your-application-with-facebook"></a><a name="register"> </a>Regisztrálja jelentkezését a Facebookon

1. Nyissa meg a [Facebook Developers] webhelyét, és jelentkezzen be a Facebook-fiókjának hitelesítő adataival.

   Ha nem rendelkezik Facebook for Developers fiókkal, válassza **az Első lépések** lehetőséget, és kövesse a regisztrációs lépéseket.
1. Válassza **a Saját alkalmazások** > **új alkalmazás hozzáadása**lehetőséget.
1. A **Megjelenítendő név** mezőben:
   1. Írja be az alkalmazás egyedi nevét.
   1. Adja meg **a kapcsolattartó e-mail címét.**
   1. Válassza **az Alkalmazásazonosító létrehozása**lehetőséget.
   1. Végezd el a biztonsági ellenőrzést.

   Megnyílik az új Facebook-alkalmazás fejlesztői irányítópultja.
1. Válassza **az Irányítópult** > **Facebook Bejelentkezés** > **beállítása** > **Web**lehetőséget .
1. A Bal oldali navigációs alatt **Facebook Bejelentkezés**, válassza a **Beállítások**lehetőséget.
1. Az **Érvényesség oauth átirányítása URI-k** mezőbe írja be a következőt: `https://<app-name>.azurewebsites.net/.auth/login/facebook/callback`. Ne felejtse el lecserélni `<app-name>` az Azure App Service-alkalmazás nevét.
1. Válassza a **Módosítások mentése lehetőséget.**
1. A bal oldali ablaktáblában válassza az **Alapbeállítások** > **lehetőséget.** 
1. Az **Alkalmazástitkos kulcsot mezőben** válassza a **Megjelenítés**lehetőséget. Másolja az **Alkalmazásazonosító** és az **Alkalmazástitkos ítmény értékeit.** Később az App Service-alkalmazás azure-beli konfigurálásához használja őket.

   > [!IMPORTANT]
   > Az alkalmazás titkosíthatósága fontos biztonsági hitelesítő adat. Ezt a titkot ne ossza meg senkivel, és ne ossza el egy ügyfélalkalmazáson belül.
   >

1. Az alkalmazás regisztrálásához használt Facebook-fiók az alkalmazás rendszergazdája. Ezen a ponton csak a rendszergazdák jelentkezhetnek be ebbe az alkalmazásba.

   Más Facebook-fiókok hitelesítéséhez válassza az **Alkalmazás-ellenőrzés** lehetőséget, és engedélyezze **az \<alkalmazásnév nyilvánossá tétele>,** hogy a nyilvánosság facebookos hitelesítéssel férhessen hozzá az alkalmazáshoz.

## <a name="add-facebook-information-to-your-application"></a><a name="secrets"> </a>Facebook-adatok hozzáadása az alkalmazáshoz

1. Jelentkezzen be az [Azure Portalon,] és keresse meg az App Service-alkalmazást.
1. Válassza a **Beállítások** > **hitelesítése / engedélyezés**lehetőséget, és győződjön meg arról, hogy az **App Service-hitelesítés** be van **kapcsolva.**
1. Válassza a **Facebook**lehetőséget, majd illessze be a korábban kapott alkalmazásazonosító és alkalmazástitkos értékeket. Engedélyezze az alkalmazás által szükséges hatóköröket.
1. Válassza **az OK gombot.**

   ![Képernyőkép a Facebook mobilalkalmazás beállításairól][0]

    Alapértelmezés szerint az App Service hitelesítést biztosít, de nem korlátozza a webhely tartalmához és API-hoz való engedélyezett hozzáférést. Engedélyeznie kell a felhasználókat az alkalmazáskódban.
1. (Nem kötelező) Ha csak a Facebook által hitelesített felhasználókra szeretné korlátozni a hozzáférést, állítsa be **a Művelet et, ha a kérés nincs hitelesítve a** **Facebook**számára. Ha ezt a funkciót állítja be, az alkalmazás minden kérelem hitelesítését igényli. Emellett átirányítja az összes nem hitelesített kérelmet a Facebookra hitelesítésre.

   > [!CAUTION]
   > A hozzáférés ily módon történő korlátozása az alkalmazás minden hívására vonatkozik, ami nem feltétlenül kívánatos a nyilvánosan elérhető kezdőlappal rendelkező alkalmazások esetében, mint sok egyoldalas alkalmazásban. Az ilyen alkalmazások esetében **előfordulhat, hogy a névtelen kérelmek engedélyezése (nincs művelet)** előnyben részesíthető, így az alkalmazás manuálisan indítja el magát a hitelesítést. További információt a [Hitelesítési folyamat című témakörben talál.](overview-authentication-authorization.md#authentication-flow)

1. Kattintson a **Mentés** gombra.

Most már készen állsz arra, hogy a Facebookot használd hitelesítésre az alkalmazásodban.

## <a name="next-steps"></a><a name="related-content"> </a>További lépések

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook fejlesztők]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure-portál]: https://portal.azure.com/
