---
title: Az App Service szolgáltatások alkalmazás Facebook-hitelesítés konfigurálása
description: Tudnivalók az App Service szolgáltatások alkalmazásbeli Facebook hitelesítés konfigurálása.
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
ms.openlocfilehash: 1d2b294fc0663770f9a699e300672695225dfdfd
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32152091"
---
# <a name="how-to-configure-your-app-service-application-to-use-facebook-login"></a>App Service-alkalmazás konfigurálása Facebook-bejelentkezés használatához
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Ez a témakör bemutatja, hogyan konfigurálhatja az Azure App Service egy hitelesítésszolgáltatót Facebook használandó.

Ebben a témakörben az eljárás végrehajtásához egy hitelesített e-mail címet és a mobiltelefonszám Facebook fiókkal kell rendelkeznie. Hozzon létre egy új Facebook-fiókkal, Ugrás [Facebook.com weboldalt].

## <a name="register"> </a>Az alkalmazás regisztrálása a Facebook-on
1. Jelentkezzen be a [Azure-portálon], és keresse meg az alkalmazás. Másolás a **URL-cím**. A Facebook-alkalmazást konfigurálásához használandó.
2. Egy másik böngészőablakban, navigáljon a [Facebook fejlesztők] webhelyet, és jelentkezzen be a Facebook-fiók hitelesítő adatait.
3. (Választható) Ha már nem regisztrált, kattintson a **alkalmazások** > **fejlesztőként nyilvántartásba**, majd fogadja el a szabályzatot, és kövesse a regisztráció lépéseit.
4. Kattintson a **alkalmazásaimat** > **fel egy új alkalmazást**.
5. A **megjelenített név**, adjon meg egy egyedi nevet az alkalmazás. Is megadhatja a **kapcsolattartó E-mail**, és kattintson a **Alkalmazásazonosító létrehozása** és a biztonsági ellenőrzés. Ezzel megnyitná a fejlesztői irányítópult az új Facebook-alkalmazás.
7. A **Facebook bejelentkezési**, kattintson a **beállítása**, és válassza a **beállítások** a bal oldali navigációs **Facebook bejelentkezési**.
8. Adja hozzá az alkalmazást **átirányítási URI-** való **érvényes OAuth-alapú átirányítási URI-azonosítók**, majd kattintson a **módosítások mentése**.
   
   > [!NOTE]
   > Az átirányítási URI megadása az alkalmazás fűzhető hozzá az elérési út az URL-cím */.auth/login/facebook/callback*. Például: `https://contoso.azurewebsites.net/.auth/login/facebook/callback`. Győződjön meg arról, hogy a HTTPS protokollt használ.
   > 
   > 
7. Kattintson a bal oldali navigációs **beállítások** > **alapvető**. Az a **alkalmazás titkos kulcs** , majd kattintson **megjelenítése**, adja meg a jelszót, ha a kért, majd jegyezze fel a közül **Alkalmazásazonosító** és **alkalmazás titkos kulcs**. Ezek később konfigurálására használt az alkalmazás az Azure-ban.
   
   > [!IMPORTANT]
   > Az alkalmazás titkos kulcs egy fontos biztonsági hitelesítő adatok. Ne ossza meg senkivel ezt a titkos kulcsot, és eloszthatják azt egy ügyfél-alkalmazással.
   > 
   > 
8. A Facebook-fiók, amely az alkalmazás regisztrálása lett megadva a rendszergazda az alkalmazás. Ezen a ponton csak a rendszergazdák jelentkezhetnek be az alkalmazás. Más Facebook fiókok hitelesítéséhez kattintson **App felülvizsgálati** , és engedélyezze **< saját-alkalmazás-neve > tegyék közzé** a Facebook-hitelesítéssel általános nyilvános hozzáférés engedélyezésére.

## <a name="secrets"> </a>Facebook-adatokat hozzáadni az alkalmazáshoz
1. Vissza a [Azure-portálon], keresse meg az alkalmazás. Kattintson a **beállítások** > **hitelesítési / engedélyezési**, és győződjön meg arról, hogy **App Service hitelesítés** van **a**.
2. Kattintson a **Facebook**, illessze be az alkalmazás Azonosítóját és az alkalmazás titkos kulcs értékek, amelyek korábban beszerzett, nem kötelezően bármely hatókörök, az alkalmazás által igényelt engedélyezése, majd kattintson **OK**.
   
    ![][0]
   
    Alapértelmezés szerint az App Service hitelesítést nyújt, de nem engedélyezett hozzáférés korlátozása a tartalom és API-k. Kell engedélyeznie a felhasználók az alkalmazás kódját.
3. (Választható) Csak azok a felhasználók hitelesítése Facebook-on a helyhez való hozzáférésének korlátozásához, állítsa be **hitelesítetlen kérés esetén elvégzendő művelet** való **Facebook**. Ehhez szükséges, hogy az összes kérelem hitelesítését, és minden nem hitelesített kérelmek Facebook-hitelesítéshez van átirányítva.
4. Konfigurálás hitelesítési végzett, kattintson a **mentése**.

Most már készen áll a Facebook az alkalmazáson belüli hitelesítéshez használandó.

## <a name="related-content"> </a>Kapcsolódó tartalom
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook fejlesztők]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[Facebook.com weboldalt]: http://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure-portálon]: https://portal.azure.com/
