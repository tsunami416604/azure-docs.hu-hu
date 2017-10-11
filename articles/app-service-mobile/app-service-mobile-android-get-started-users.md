---
title: "Hitelesítés hozzáadása az Android a Mobile Apps |} Microsoft Docs"
description: "Útmutató az Azure App Service Mobile Apps szolgáltatásának segítségével hitelesíti a felhasználókat identitás-szolgáltatóktól, beleértve a Google, a Facebook, a Twitter és a Microsoft számos az Android-alkalmazás."
services: app-service\mobile
documentationcenter: android
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 1fc8e7c1-6c3c-40f4-9967-9cf5e21fc4e1
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 10/01/2016
ms.author: glenga
ms.openlocfilehash: 81331142aa6110d4e29e6fb30a90ce6e3a853439
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="add-authentication-to-your-android-app"></a>Hitelesítés hozzáadása az Android-alkalmazás
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Összefoglalás
Ebben az oktatóanyagban hozzáadhat hitelesítési a todolist gyorsútmutató-projekt az Android támogatott identitásszolgáltató használatával. Ez az oktatóanyag alapul a [Ismerkedés a Mobile Apps] oktatóanyag, amely először el kell végeznie.

## <a name="register"></a>Hitelesítés az alkalmazás regisztrálása és konfigurálása az Azure App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Az alkalmazás hozzáadása az engedélyezett külső átirányítási URL-címek

Biztonságos hitelesítéshez az szükséges, hogy az alkalmazás adja meg egy új URL-sémát. Ez lehetővé teszi a hitelesítési rendszer visszairányítja az alkalmazás a hitelesítési folyamat befejezése után. Ebben az oktatóanyagban az URL-séma használjuk _appname_ egész. Bármely választja URL-sémát is használhatja. A mobilalkalmazás egyedinek kell lennie. A kiszolgáló oldalán engedélyezése:

1. Válassza ki az App Service az [Azure-portálon].

2. Kattintson a **hitelesítési / engedélyezési** menüjét.

3. Az a **engedélyezett külső átirányítási URL-t**, adja meg `appname://easyauth.callback`.  A _appname_ ezt a karakterláncot a rendszer az URL-séma, a mobilalkalmazás.  Akkor érdemes követnie normál URL-cím meghatározása (használata betűket és számokat csak, és betűvel kezdődik) protokoll.  Meg kell győződnie, jegyezze fel a karakterláncot, amely úgy dönt, mivel úgy, hogy az URL-séma több helyen a mobilalkalmazás kódot kell.

4. Kattintson az **OK** gombra.

5. Kattintson a **Save** (Mentés) gombra.

## <a name="permissions"></a>A hitelesített felhasználókhoz jogosultságok korlátozása
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* Az Android Studióban nyissa meg a projektet, elvégezte az oktatóanyag [Ismerkedés a Mobile Apps]. Az a **futtassa** menüben kattintson a **-alkalmazás futtatása**, és győződjön meg arról, hogy egy állapotkód: nem kezelt kivétel a 401-es (nem engedélyezett) jelenik meg, az alkalmazás indítása után.

     Ezt a kivételt az oka, hogy az alkalmazás megpróbál hozzáférni a háttérben, nem hitelesített felhasználóként, de a *TodoItem* tábla most hitelesítést igényel.

Ezt követően a felhasználók hitelesítésére, mielőtt kérelmezi erőforrások a Mobile Apps háttérből alkalmazást frissíti. 

## <a name="add-authentication-to-the-app"></a>Hitelesítés hozzáadása az alkalmazáshoz
[!INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]



## <a name="cache-tokens"></a>Gyorsítótár a hitelesítési tokenek az ügyfélen
[!INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Következő lépések
Most, hogy elvégezte az oktatóanyag az egyszerű hitelesítés, fontolja meg valamelyik az alábbi oktatóanyagok folytatása:

* [Leküldéses értesítések hozzáadása az Android-alkalmazás](app-service-mobile-android-get-started-push.md).
  Megtudhatja, hogyan konfigurálhatja a Mobile Apps háttér Azure notification hubs használata leküldéses értesítések küldéséhez.
* [Az Android-alkalmazás kapcsolat nélküli szinkronizálásának engedélyezése](app-service-mobile-android-get-started-offline-data.md).
  Megtudhatja, hogyan adhat offline támogatást az alkalmazást a Mobile Apps háttérből segítségével. Kapcsolat nélküli szinkronizálás, a felhasználók használhatják a mobilalkalmazás&mdash;megtekintését, hozzáadását és módosítását adatok&mdash;akkor is, ha nincs hálózati kapcsolat.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[Ismerkedés a Mobile Apps]: app-service-mobile-android-get-started.md
