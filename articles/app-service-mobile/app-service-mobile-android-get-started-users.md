---
title: Hitelesítés hozzáadása Androidon
description: Megtudhatja, hogyan használhatja a Azure App Servicet az Android-alkalmazás felhasználóinak hitelesítésére, például a Google, a Facebook, a Twitter és a Microsoft identitás-szolgáltatókkal.
ms.assetid: 1fc8e7c1-6c3c-40f4-9967-9cf5e21fc4e1
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 705ebb5809840155e6bbf3f8eef091eb95f63e63
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461640"
---
# <a name="add-authentication-to-your-android-app"></a>Hitelesítés hozzáadása az Android-alkalmazáshoz
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Összegzés
Ebben az oktatóanyagban egy támogatott identitás-szolgáltató használatával adja hozzá a hitelesítést az Android rendszerhez készült ToDoList gyors üzembe helyezési projekthez. Ez az oktatóanyag az első [Ismerkedés a Mobile Apps] oktatóanyagon alapul, amelyet először végre kell hajtania.

## <a name="register"></a>Az alkalmazás regisztrálása a hitelesítéshez és a Azure App Service konfigurálása
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Az alkalmazás hozzáadása az engedélyezett külső átirányítási URL-címekhez

A biztonságos hitelesítéshez meg kell adnia egy új URL-sémát az alkalmazáshoz. Ez lehetővé teszi, hogy a hitelesítési rendszer visszairányítsa az alkalmazást az alkalmazásba a hitelesítési folyamat befejezése után. Ebben az oktatóanyagban az URL-séma _AppName_ használjuk az egészben. Használhat azonban bármely kiválasztott URL-címet. Egyedinek kell lennie a Mobile-alkalmazás számára. Az átirányítás engedélyezése a kiszolgálóoldali oldalon:

1. A [Azure Portalra]válassza ki a app Service.

2. Kattintson a **hitelesítés/engedélyezés** menüpontra.

3. Az **engedélyezett külső átirányítási URL-címek**mezőben adja meg a `appname://easyauth.callback`.  A karakterláncban szereplő _AppName_ a Mobile-alkalmazás URL-sémája.  A protokollnak normál URL-specifikációt kell követnie (csak betűket és számokat kell használnia, és betűvel kell kezdődnie).  Jegyezze fel a kiválasztott karakterláncot, mivel a mobil alkalmazás kódját több helyen is módosítania kell az URL-sémával.

4. Kattintson az **OK** gombra.

5. Kattintson a **Save** (Mentés) gombra.

## <a name="permissions"></a>A hitelesített felhasználók engedélyeinek korlátozása
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* A Android Studioban nyissa meg az oktatóanyag [Ismerkedés a Mobile Apps]végzett projektet. A **Futtatás** menüben kattintson az **alkalmazás futtatása**elemre, és ellenőrizze, hogy az alkalmazás elindítása után az 401 (nem engedélyezett) állapotkód miatt nem kezelt kivétel következik-e be.

     Ez a kivétel azért fordul elő, mert az alkalmazás nem hitelesített felhasználóként próbál hozzáférni a háttér-végponthoz, de a *TodoItem* -táblához már hitelesítés szükséges.

Ezután frissítse az alkalmazást a felhasználók hitelesítéséhez, mielőtt erőforrásokat kérne a Mobile Apps háttérből.

## <a name="add-authentication-to-the-app"></a>Hitelesítés hozzáadása az alkalmazáshoz
[!INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]



## <a name="cache-tokens"></a>Gyorsítótár-hitelesítési tokenek az ügyfélen
[!INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Következő lépések
Most, hogy elvégezte ezt az alapszintű hitelesítési oktatóanyagot, tekintse át a következő oktatóanyagok egyikét:

* [Leküldéses értesítések hozzáadása az Android-alkalmazáshoz](app-service-mobile-android-get-started-push.md).
  Megtudhatja, hogyan konfigurálhatja a Mobile Apps háttérrendszer segítségével, hogy az Azure Notification hub használatával küldjön le leküldéses értesítéseket.
* [Az Android-alkalmazás offline szinkronizálásának engedélyezése](app-service-mobile-android-get-started-offline-data.md).
  Megtudhatja, hogyan adhat offline támogatást az alkalmazáshoz egy Mobile Apps háttér használatával. A kapcsolat nélküli szinkronizálással a felhasználók a Mobile apps szolgáltatással kezelhetik az adat&mdash;t&mdash;megtekinteni, hozzáadni vagy módosítani, még akkor is, ha nincs hálózati kapcsolat.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[Ismerkedés a Mobile Apps]: app-service-mobile-android-get-started.md
[Azure Portalra]: https://portal.azure.com/
