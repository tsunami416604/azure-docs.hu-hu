---
title: Facebook-hitelesítés konfigurálása – Azure App Service
description: Ismerje meg, hogyan konfigurálhatja a Facebook-hitelesítést a App Services alkalmazáshoz.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: f4d26572dc21e2c7454fb739cb4b5fb5665bd1db
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098583"
---
# <a name="how-to-configure-your-app-service-application-to-use-facebook-login"></a>App Service-alkalmazás konfigurálása Facebook-bejelentkezés használatához
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Ebből a témakörből megtudhatja, hogyan konfigurálhatja a Azure App Servicet a Facebook hitelesítési szolgáltatóként való használatára.

A jelen témakörben ismertetett eljárás végrehajtásához olyan Facebook-fiókkal kell rendelkeznie, amely ellenőrzött e-mail-címmel és mobiltelefon-számmal rendelkezik. Új Facebook-fiók létrehozásához nyissa meg a [Facebook.com].

## <a name="register"> </a>Alkalmazás regisztrálása a Facebook-ban
1. Navigáljon a [Facebook-fejlesztők] webhelyére, és jelentkezzen be Facebook-fiókja hitelesítő adataival.
3. Választható Ha nem rendelkezik Facebook for Developers-fiókkal, kattintson az első **lépések** lehetőségre, és kövesse a regisztrációs lépéseket.
4. Kattintson **a saját alkalmazások** > **új alkalmazás hozzáadása**elemre.
5. A **megjelenítendő név**mezőbe írjon be egy egyedi nevet az alkalmazásnak. Adja meg a **kapcsolattartási E-mail címét**is, majd kattintson az **alkalmazás-azonosító létrehozása** lehetőségre, és fejezze be a biztonsági ellenőrzését. Ekkor megjelenik az új Facebook-alkalmazás fejlesztői irányítópultja.
6. Kattintson az **irányítópult** > **Facebook-Bejelentkezés** > **webes** **beállítása** > elemre.
1. A **Facebook-Bejelentkezés**alatt a bal oldali navigációs sávon kattintson a **Beállítások**elemre.
1. Az **érvényes OAuth átirányítási URI**- `https://<app-name>.azurewebsites.net/.auth/login/facebook/callback` k mezőbe írja be és cserélje le  *\<az App-Name >* nevet a Azure app Service alkalmazás nevével. Kattintson a **módosítások mentése**gombra.
8. A bal oldali navigációs sávon kattintson az alapbeállítások > elemre. Az **alkalmazás titka** mezőben kattintson a **Megjelenítés**elemre. Másolja az alkalmazás- **azonosító** és az **alkalmazás titkos kulcsának**értékeit. Ezeket később a App Service-alkalmazás Azure-ban való konfigurálásához használhatja.
   
   > [!IMPORTANT]
   > Az alkalmazás titkos kulcsa egy fontos biztonsági hitelesítő adat. Ezt a titkos kódot Ne ossza meg senkivel, vagy Ossza szét egy ügyfélalkalmazás alkalmazásán belül.
   > 
   > 
9. Az alkalmazás regisztrálásához használt Facebook-fiók az alkalmazás rendszergazdája. Ezen a ponton csak rendszergazdák jelentkezhetnek be ebbe az alkalmazásba. Más Facebook-fiókok hitelesítéséhez kattintson az **alkalmazás áttekintése** lehetőségre, és engedélyezze  **\<az alkalmazás-név > nyilvános** lehetőséget a Facebook-hitelesítéssel történő általános nyilvános hozzáférés engedélyezéséhez.

## <a name="secrets"> </a>Facebook-információk hozzáadása az alkalmazáshoz
1. Jelentkezzen be a [Azure Portal] , és navigáljon a app Service alkalmazáshoz. Kattintson a **Beállítások** > **hitelesítés/engedélyezés**elemre, és győződjön meg arról, hogy a **app Service hitelesítés** **be van kapcsolva**.
2. Kattintson a **Facebook**lehetőségre, illessze be az alkalmazás azonosítóját és az alkalmazás titkos kulcsát, amelyet korábban kapott, opcionálisan engedélyezheti az alkalmazáshoz szükséges hatóköröket, majd kattintson **az OK**gombra.
   
    ![][0]
   
    Alapértelmezés szerint a App Service hitelesítést biztosít, de nem korlátozza a webhely tartalmához és API-khoz való jogosult hozzáférést. Engedélyezni kell a felhasználókat az alkalmazás kódjában.
3. Választható Ha a webhelyhez való hozzáférést csak a Facebook által hitelesített felhasználóknak szeretné korlátozni, állítsa be a **végrehajtandó műveletet, ha a kérés nincs hitelesítve** a **Facebook**-ban. Ehhez minden kérést hitelesíteni kell, és az összes nem hitelesített kérelem át lesz irányítva a Facebookon a hitelesítéshez.
 
> [!CAUTION]
> A hozzáférés ezen a módon való korlátozása az alkalmazás összes hívására vonatkozik, ami nem kívánatos, ha az alkalmazások nyilvánosan elérhető kezdőlapot szeretnének, például sok egyoldalas alkalmazásban. Ilyen alkalmazások esetén **engedélyezze a névtelen kérelmeket (nincs művelet)** előnyben részesített, ha az alkalmazás manuálisan indítja el a bejelentkezést, az [itt](overview-authentication-authorization.md#authentication-flow)leírtak szerint.

4. Ha végzett a hitelesítés konfigurálásával, kattintson a **Mentés**gombra.

Most már készen áll a Facebook használatára a hitelesítéshez az alkalmazásban.

## <a name="related-content"> </a>Related Content
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook-fejlesztők]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure Portal]: https://portal.azure.com/
