---
title: Facebook-hitelesítés – az Azure App Service konfigurálása
description: Ismerje meg, hogyan konfigurálja az App Services-alkalmazás Facebook-hitelesítését.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 06/06/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: e91d55c29d325301b8ac70ddc63fb408961fbb2c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66742979"
---
# <a name="how-to-configure-your-app-service-application-to-use-facebook-login"></a>App Service-alkalmazás konfigurálása Facebook-bejelentkezés használatához
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Ez a témakör bemutatja, hogyan konfigurálhatja az Azure App Service egy hitelesítésszolgáltató Facebook használandó.

Ebben a témakörben az eljárás végrehajtásához egy hitelesített e-mail-cím és mobiltelefonszám Facebook fiókkal kell rendelkeznie. Hozzon létre egy új Facebook-fiókban, lépjen a [Facebook.com weboldalt].

## <a name="register"> </a>Az alkalmazás regisztrálása a Facebookkal
1. Keresse meg a [Facebook-fejlesztőknek] webhelyet, és jelentkezzen be a Facebook-fiók hitelesítő adatait.
3. (Nem kötelező) Ha a Facebook, a fejlesztők fiók nem rendelkezik, kattintson a **Ismerkedés** , és kövesse a regisztrációs lépéseket.
4. Kattintson a **saját alkalmazások** > **új alkalmazás hozzáadása**.
5. A **megjelenítendő név**, írjon be egy egyedi nevet az alkalmazáshoz. Emellett adja meg a **kapcsolattartó E-mail**, és kattintson a **Alkalmazásazonosító létrehozása** és a biztonsági ellenőrzés. Ekkor megjelenik az új Facebook-alkalmazás a fejlesztői irányítópult.
6. Kattintson a **irányítópult** > **Facebook-bejelentkezés** > **beállítása** > **webes**.
1. A bal oldali navigációs **Facebook-bejelentkezés**, kattintson a **beállítások**.
1. A **érvényes OAuth átirányítási URI-k**, típus `https://<app-name>.azurewebsites.net/.auth/login/facebook/callback` , és cserélje le  *\<alkalmazás-neve >* az Azure App Service-alkalmazás nevét. Kattintson a **módosítások mentése**.
8. A bal oldali navigációs sávján kattintson **beállítások** > **alapszintű**. Az a **titkos Alkalmazáskulcs** mezőben kattintson **megjelenítése**. Másolja le az értékeket a **Alkalmazásazonosító** és **titkos Alkalmazáskulcs**. Használja ezeket később az App Service-alkalmazás konfigurálása az Azure-ban.
   
   > [!IMPORTANT]
   > Az alkalmazás titkos kulcsát egy fontos biztonsági hitelesítő adat. Ne a titkos kulcs bárkivel megoszthatja, és egy ügyfélalkalmazás belül terjeszthet.
   > 
   > 
9. Az alkalmazás regisztrálásához használt Facebook-fiókban az alkalmazás az rendszergazdájának. Csak a rendszergazdák ezen a ponton az alkalmazás be tud jelentkezni. Más Facebook-fiókok hitelesítése, kattintson a **alkalmazás felülvizsgálati** , és engedélyezze **győződjön meg arról, \<saját-alkalmazás-neve > nyilvános** a Facebook-hitelesítés használatával általános nyilvános hozzáférés engedélyezésére.

## <a name="secrets"> </a>Facebook-információk hozzáadása az alkalmazáshoz
1. Jelentkezzen be a [Azure Portal] , és keresse meg az App Service-alkalmazást. Kattintson a **beállítások** > **hitelesítési / engedélyezési**, és ellenőrizze, hogy **App Service-hitelesítés** van **a**.
2. Kattintson a **Facebook**, illessze be az Alkalmazásazonosítót és titkos Alkalmazáskulcs értékeket, amelyek korábban beszerzett, igény szerint engedélyezése bármely hatókörök, az alkalmazás által igényelt, majd kattintson a **OK**.
   
    ![][0]
   
    Alapértelmezés szerint az App Service-ben hitelesítést biztosít, de a webhely tartalmát és API-k nem korlátozza a hitelesített hozzáférést. Felhasználók engedélyeznie kell az alkalmazás kódjában.
3. (Nem kötelező) A hely csak a felhasználók Facebook-alapú hitelesítéssel való hozzáférés korlátozásához, állítsa be **elvégzendő művelet, ha a kérés nincs hitelesítve** való **Facebook**. Ehhez az szükséges, hogy minden kérelmet hitelesíteni, és minden nem hitelesített kérelmeket a rendszer átirányítja Facebook-hitelesítéshez.
4. Ha befejezte a hitelesítés konfigurálását, kattintson **mentése**.

Most már készen áll az alkalmazás-hitelesítéshez használja a Facebookot.

## <a name="related-content"> </a>Related Content
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook-fejlesztőknek]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[Facebook.com weboldalt]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure Portal]: https://portal.azure.com/
