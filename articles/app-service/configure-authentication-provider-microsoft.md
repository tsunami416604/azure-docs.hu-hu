---
title: Microsoft-hitelesítés konfigurálása
description: Ismerje meg, hogyan konfigurálhatja a Microsoft-fiók hitelesítését identitásszolgáltatóként az App Service vagy az Azure Functions alkalmazáshoz.
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.topic: article
ms.date: 08/08/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: ea745aa00f8990a2d1232a19780fdc70e2f78996
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437965"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-microsoft-account-login"></a>Az App Service vagy az Azure Functions alkalmazás konfigurálása a Microsoft-fiók bejelentkezéséhez

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Ez a témakör bemutatja, hogyan konfigurálhatja az Azure App Service vagy az Azure Functions az AAD használatával a személyes Microsoft-fiók bejelentkezések.

> [!NOTE]
> Mind a személyes Microsoft-fiókok, mind a szervezeti fiókok az AAD-identitásszolgáltatót használják. Jelenleg nem lehet beállítani ezt az identitásszolgáltatót mindkét típusú bejelentkezéstámogatására.

## <a name="register-your-app-with-microsoft-account"></a><a name="register-microsoft-account"> </a>Az alkalmazás regisztrálása a Microsoft-fiókkal

1. Nyissa meg [**az alkalmazásregisztrációkat**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) az Azure Portalon. Ha szükséges, jelentkezzen be Microsoft-fiókjával.
1. Válassza **az Új regisztráció**lehetőséget, majd adjon meg egy alkalmazásnevet.
1. A **Támogatott fióktípusok**csoportban válassza a **Fiókok lehetőséget bármely szervezeti címtárban (Bármilyen Azure AD-címtárban – többbérlős) és személyes Microsoft-fiókokban (pl. Skype, Xbox)**
1. Az **Irányított URI-k átirányítása** `https://<app-domain-name>/.auth/login/aad/callback`csoportban válassza a **Web**lehetőséget, majd írja be a parancsot. Cserélje le * \<az alkalmazástartomány-név>* az alkalmazás tartománynevére.  Például: `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Ügyeljen arra, hogy használja a HTTPS-séma az URL-ben.

1. Kattintson a **Register** (Regisztrálás) elemre.
1. Másolja az **alkalmazás (ügyfél) azonosítóját**. Erre később még szüksége lesz.
1. A bal oldali ablaktáblában válassza **a Tanúsítványok & titkos kulcsok** > **Új ügyféltitok**lehetőséget. Adjon meg egy leírást, válassza ki az érvényesség időtartamát, és válassza a **Hozzáadás**lehetőséget.
1. Másolja a **másoláshoz** a Tanúsítványok & titkos kulcsok lapon megjelenő értéket. Miután elhagyta az oldalt, az nem jelenik meg újra.

    > [!IMPORTANT]
    > Az ügyféltitkos érték (jelszó) fontos biztonsági hitelesítő adat. Ne ossza meg a jelszót senkivel, és ne terjessze egy ügyfélalkalmazáson belül.

## <a name="add-microsoft-account-information-to-your-app-service-application"></a><a name="secrets"> </a>Microsoft-fiókadatok hozzáadása az App Service-alkalmazáshoz

1. Nyissa meg az alkalmazást az [Azure Portalon.]
1. Válassza a **Beállítások** > **hitelesítése / engedélyezés**lehetőséget, és győződjön meg arról, hogy az **App Service-hitelesítés** be van **kapcsolva.**
1. A **Hitelesítésszolgáltatók csoportban**válassza az **Azure Active Directory**lehetőséget. Válassza **a Speciális** lehetőséget a Felügyeleti **módban.** Illessze be az alkalmazás (ügyfél) azonosítóját és a korábban beszerzett ügyféltitkos kulcsot. A **https://login.microsoftonline.com/9188040d-6c67-4c5b-b112-36a304b66dad/v2.0** **Kiállító url-cím** mezőjéhez használja.
1. Válassza **az OK gombot.**

   Az App Service hitelesítést biztosít, de nem korlátozza az engedélyezett hozzáférést a webhely tartalmához és API-ihoz. Az alkalmazáskódban engedélyeznie kell a felhasználókat.

1. (Nem kötelező) A Microsoft-fiók felhasználóihoz való hozzáférés korlátozásához állítsa be a **Művelet et, ha a kérelem nincs hitelesítve az** **Azure Active Directoryval való bejelentkezéshez.** Ha ezt a funkciót állítja be, az alkalmazás minden kérelem hitelesítését igényli. Emellett átirányítja az összes nem hitelesített kérelmet az AAD hitelesítéshez való használatára. Ne feledje, hogy mivel a **kiállító url-címét** a Microsoft-fiók bérlőjének használatára állította be, csak a személyes acccounts sikeresen hitelesíti magát.

   > [!CAUTION]
   > A hozzáférés ily módon történő korlátozása az alkalmazás minden hívására vonatkozik, ami nem feltétlenül kívánatos a nyilvánosan elérhető kezdőlappal rendelkező alkalmazások esetében, mint sok egyoldalas alkalmazásban. Az ilyen alkalmazások esetében **előfordulhat, hogy a névtelen kérelmek engedélyezése (nincs művelet)** előnyben részesíthető, így az alkalmazás manuálisan indítja el magát a hitelesítést. További információt a [Hitelesítési folyamat című témakörben talál.](overview-authentication-authorization.md#authentication-flow)

1. Kattintson a **Mentés** gombra.

Most már készen áll a Microsoft-fiók használatára az alkalmazásban történő hitelesítéshez.

## <a name="next-steps"></a><a name="related-content"> </a>További lépések

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure-portál]: https://portal.azure.com/
