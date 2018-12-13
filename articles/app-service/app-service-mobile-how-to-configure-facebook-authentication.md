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
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: cc10c9be5bab3b84c8773d8a930473267db353ab
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53256774"
---
# <a name="how-to-configure-your-app-service-application-to-use-facebook-login"></a>App Service-alkalmazás konfigurálása Facebook-bejelentkezés használatához
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Ez a témakör bemutatja, hogyan konfigurálhatja az Azure App Service egy hitelesítésszolgáltató Facebook használandó.

Ebben a témakörben az eljárás végrehajtásához egy hitelesített e-mail-cím és mobiltelefonszám Facebook fiókkal kell rendelkeznie. Hozzon létre egy új Facebook-fiókban, lépjen a [Facebook.com weboldalt].

## <a name="register"> </a>Az alkalmazás regisztrálása a Facebookkal
1. Jelentkezzen be a [Azure Portal], és keresse meg az alkalmazást. Másolás a **URL-cím**. A Facebook-alkalmazás konfigurálása akkor fogja használni.
2. Egy másik böngészőablakban navigáljon a [Facebook-fejlesztőknek] webhelyet, és jelentkezzen be a Facebook-fiók hitelesítő adatait.
3. (Nem kötelező) Ha még nem regisztrált, kattintson a **alkalmazások** > **fejlesztőként regisztrálása**, majd fogadja el a szabályzatot, és hajtsa végre a regisztrációs lépéseket.
4. Kattintson a **saját alkalmazások** > **adjon hozzá egy új alkalmazást**.
5. A **megjelenítendő név**, írjon be egy egyedi nevet az alkalmazáshoz. Emellett adja meg a **kapcsolattartó E-mail**, és kattintson a **Alkalmazásazonosító létrehozása** és a biztonsági ellenőrzés. Ekkor megjelenik az új Facebook-alkalmazás a fejlesztői irányítópult.
6. Alatt **Facebook-bejelentkezés**, kattintson a **beállítása**, és válassza a **beállítások** bal oldali navigációs területen **Facebook-bejelentkezés**.
7. Adja hozzá az alkalmazást **átirányítási URI-t** való **érvényes OAuth átirányítási URI-k**, majd kattintson a **módosítások mentése**.
   
   > [!NOTE]
   > Az átirányítási URI-ja URL-címét az elérési utat, kiegészítve az alkalmazás */.auth/login/facebook/callback*. Például: `https://contoso.azurewebsites.net/.auth/login/facebook/callback`. Győződjön meg arról, hogy használja a HTTPS-sémát.
   > 
   > 
8. A bal oldali navigációs sávján kattintson **beállítások** > **alapszintű**. Az a **titkos Alkalmazáskulcs** mezőben kattintson **megjelenítése**, adja meg a jelszót, ha a kért, majd jegyezze fel az értékét a **Alkalmazásazonosító** és **titkos Alkalmazáskulcs** . Használja ezeket később-alkalmazás konfigurálása az Azure-ban.
   
   > [!IMPORTANT]
   > Az alkalmazás titkos kulcsát egy fontos biztonsági hitelesítő adat. Ne a titkos kulcs bárkivel megoszthatja, és egy ügyfélalkalmazás belül terjeszthet.
   > 
   > 
9. Az alkalmazás regisztrálásához használt Facebook-fiókban az alkalmazás az rendszergazdájának. Csak a rendszergazdák ezen a ponton az alkalmazás be tud jelentkezni. Más Facebook-fiókok hitelesítése, kattintson a **alkalmazás felülvizsgálati** , és engedélyezze **< saját-alkalmazás-neve > tegyék közzé** a Facebook-hitelesítés használatával általános nyilvános hozzáférés engedélyezésére.

## <a name="secrets"> </a>Facebook-információk hozzáadása az alkalmazáshoz
1. Térjen vissza a [Azure Portal], keresse meg az alkalmazást. Kattintson a **beállítások** > **hitelesítési / engedélyezési**, és ellenőrizze, hogy **App Service-hitelesítés** van **a**.
2. Kattintson a **Facebook**, illessze be az Alkalmazásazonosítót és titkos Alkalmazáskulcs értékeket, amelyek korábban beszerzett, igény szerint engedélyezése bármely hatókörök, az alkalmazás által igényelt, majd kattintson a **OK**.
   
    ![][0]
   
    Alapértelmezés szerint az App Service-ben hitelesítést biztosít, de a webhely tartalmát és API-k nem korlátozza a hitelesített hozzáférést. Felhasználók engedélyeznie kell az alkalmazás kódjában.
3. (Nem kötelező) A hely csak a felhasználók Facebook-alapú hitelesítéssel való hozzáférés korlátozásához, állítsa be **elvégzendő művelet, ha a kérés nincs hitelesítve** való **Facebook**. Ehhez az szükséges, hogy minden kérelmet hitelesíteni, és minden nem hitelesített kérelmeket a rendszer átirányítja Facebook-hitelesítéshez.
4. Ha befejezte a hitelesítés konfigurálását, kattintson **mentése**.

Most már készen áll az alkalmazás-hitelesítéshez használja a Facebookot.

## <a name="related-content"> </a>Kapcsolódó tartalom
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook-fejlesztőknek]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[Facebook.com weboldalt]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure Portal]: https://portal.azure.com/
