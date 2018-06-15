---
title: Az App Service szolgáltatások alkalmazás Twitter-hitelesítés konfigurálása
description: Tudnivalók az App Service szolgáltatások alkalmazás Twitter-hitelesítés konfigurálása.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.openlocfilehash: f6449f99fda9c1a612ed9f9134751ff76b25904c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32149952"
---
# <a name="how-to-configure-your-app-service-application-to-use-twitter-login"></a>Az App Service alkalmazás használhatja Twitter-bejelentkezés konfigurálása
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Ez a témakör bemutatja, hogyan konfigurálhatja az Azure App Service egy hitelesítésszolgáltatót Twitter használandó.

Ebben a témakörben az eljárás végrehajtásához rendelkeznie kell egy Twitter-fiók, amely rendelkezik egy hitelesített e-mail címét és telefonszámát számát. Új Twitter-fiók létrehozásához, lépjen <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.

## <a name="register"> </a>Az alkalmazás regisztrálása a Twitteren
1. Jelentkezzen be a [Azure-portálon], és keresse meg az alkalmazás. Másolás a **URL-cím**. Ez a Twitter-alkalmazás beállítása használandó.
2. Keresse meg a [Twitter fejlesztők] webhelyét, jelentkezzen be Twitter-fiók hitelesítő adataival, majd kattintson a **új alkalmazás létrehozása**.
3. Írja be a **neve** és egy **leírás** az új alkalmazás. Illessze be az alkalmazás **URL-cím** a a **webhely** érték. Ezt követően az a **visszahívási URL-cím**, illessze be a **visszahívási URL-cím** korábban kimásolt. Ez az a mobilalkalmazás átjáró fűzhető hozzá az elérési út */.auth/login/twitter/callback*. Például: `https://contoso.azurewebsites.net/.auth/login/twitter/callback`. Győződjön meg arról, hogy a HTTPS protokollt használ.
4. A lap alsó olvassa el és fogadja el a feltételeket. Kattintson a **az Twitter-alkalmazás létrehozása**. Ezzel regisztrálja az alkalmazás megjelenik az alkalmazás részletes adatainak.
5. Kattintson a **beállítások** lapon jelölje **teszik lehetővé az alkalmazás aláírásához twitterrel**, majd kattintson a **frissítési beállítások**.
6. Válassza ki a **kulcsok és a hozzáférési jogkivonatok** fülre. Jegyezze fel a közül **(API-kulcs) kulcsa** és **felhasználói titok (API titkos)**.
   
   > [!NOTE]
   > A felhasználó titkos kulcsot egy fontos biztonsági hitelesítő adatok. Ne ossza meg senkivel ezt a titkos kulcsot, és eloszthatják azt az alkalmazással.
   > 
   > 

## <a name="secrets"> </a>Twitter-adatokat hozzáadni az alkalmazáshoz
1. Vissza a [Azure-portálon], keresse meg az alkalmazás. Kattintson a **beállítások**, majd **hitelesítési / engedélyezési**.
2. Ha a hitelesítési / engedélyezési funkció nincs engedélyezve, kapcsolja be a kapcsoló **a**.
3. Kattintson a **Twitter**. Illessze be az alkalmazás Azonosítóját és alkalmazás titkos kulcs értékek, amelyek korábban beszerzett. Ezután kattintson az **OK** gombra.
   
   ![][1]
   
   Alapértelmezés szerint az App Service hitelesítést nyújt, de nem engedélyezett hozzáférés korlátozása a tartalom és API-k. Kell engedélyeznie a felhasználók az alkalmazás kódját.
4. (Választható) Csak a Twitter által hitelesített felhasználók a helyhez való hozzáférésének korlátozásához, állítsa be **hitelesítetlen kérés esetén elvégzendő művelet** való **Twitter**. Ehhez szükséges, hogy az összes kérelem hitelesítését, és minden nem hitelesített kérelmek Twitter-hitelesítéshez van átirányítva.
5. Kattintson a **Save** (Mentés) gombra.

Most már készen áll a Twitteren az alkalmazáson belüli hitelesítéshez használandó.

## <a name="related-content"> </a>Kapcsolódó tartalom
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter fejlesztők]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Azure-portálon]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
