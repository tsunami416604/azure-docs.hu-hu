---
title: Hitelesítés hozzáadása az Android a Mobile Apps |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja az Azure App Service Mobile Apps funkciójával hitelesíti a felhasználókat az Androidos alkalmazás identitás-szolgáltatóktól, beleértve a Google, Facebook, Twitter és a Microsoft számos.
services: app-service\mobile
documentationcenter: android
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 1fc8e7c1-6c3c-40f4-9967-9cf5e21fc4e1
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 11/16/2017
ms.author: crdun
ms.openlocfilehash: 4ee71e00807fcfe698a7e965979434f338f5b870
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38595528"
---
# <a name="add-authentication-to-your-android-app"></a>Hitelesítés hozzáadása az Android-alkalmazás
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Összegzés
Ebben az oktatóanyagban, hitelesítés hozzáadása a todolist gyorsútmutató-projekt az Android által támogatott identitásszolgáltatóval. Ez az oktatóanyag alapján a [A Mobile Apps használatának első lépései] oktatóanyaggal, amely először el kell végeznie.

## <a name="register"></a>Regisztrálja az alkalmazást a hitelesítéshez, és az Azure App Service konfigurálása
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Az alkalmazás hozzáadása az engedélyezett külső átirányítási URL-címek

Hitelesítésre van szükség, hogy az alkalmazás egy új URL-séma meghatározása. Ez lehetővé teszi a hitelesítési rendszer visszairányítja az alkalmazás a hitelesítési folyamat befejeződése után. Ebben az oktatóanyagban azt használja az URL-séma _appname_ során. Bármely URL-séma választja is használhatja. A mobilalkalmazás egyedinek kell lennie. A kiszolgálói oldalon az átirányítás engedélyezése:

1. Az a [Azure Portal], válassza ki az App Service.

2. Kattintson a **hitelesítési / engedélyezési** menüpont.

3. Az a **engedélyezett külső átirányítási URL-címeket**, adja meg `appname://easyauth.callback`.  A _appname_ Ez a karakterlánc a következő az URL-séma esetében.  Normál URL-cím-specifikáció (használata betűket és csak számokból állhat, és betűvel kezdődhet) protokoll, érdemes követnie.  Meg kell jegyezze fel a karakterlánc, amely úgy dönt, mert szüksége lesz a mobilalkalmazás-kód az URL-séma több helyen való beállításához.

4. Kattintson az **OK** gombra.

5. Kattintson a **Save** (Mentés) gombra.

## <a name="permissions"></a>A hitelesített felhasználók engedélyeinek korlátozása
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* Az Android Studióban nyissa meg a projektet, elvégezte az oktatóanyag [A Mobile Apps használatának első lépései]. A a **futtassa** menüben kattintson a **alkalmazás futtatása**, és győződjön meg arról, hogy 401-es (nem engedélyezett) állapotkódot a nem kezelt kivétel jelenik meg, az alkalmazás indítása után.

     Ehhez a kivételhez akkor fordul elő, mert az alkalmazást próbál meg hozzáférni a háttéralkalmazás nem hitelesített felhasználóként, de a *TodoItem* táblázat most már hitelesítést igényel.

Ezután frissítse az alkalmazást, hogy a felhasználók hitelesítése előtt erőforrásokat kér a Mobile Apps-háttéralkalmazást.

## <a name="add-authentication-to-the-app"></a>Hitelesítés hozzáadása az alkalmazáshoz
[!INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]



## <a name="cache-tokens"></a>Az ügyfél hitelesítési jogkivonatok gyorsítótárazása
[!INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

## <a name="next-steps"></a>További lépések
Most, hogy elvégezte az oktatóanyag az egyszerű hitelesítés, vegye figyelembe a folyamatos be az alábbi oktatóanyagok egyikét:

* [Leküldéses értesítések hozzáadása az Android-alkalmazás](app-service-mobile-android-get-started-push.md).
  Ismerje meg, hogyan konfigurálhatja az Azure notification hubs használata leküldéses értesítések küldéséhez a Mobile Apps-háttéralkalmazást.
* [Android-alkalmazások az offline szinkronizálás engedélyezése](app-service-mobile-android-get-started-offline-data.md).
  Megtudhatja, hogyan adhat offline támogatást alkalmazásához egy Mobile Apps-háttéralkalmazás segítségével. Offline szinkronizálás, a felhasználók kezelhessék a mobilalkalmazás&mdash;megtekintését, hozzáadását és módosítását adatok&mdash;akkor is, ha nincs hálózati kapcsolat.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[A Mobile Apps használatának első lépései]: app-service-mobile-android-get-started.md
[Azure Portal]: https://portal.azure.com/
