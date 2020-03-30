---
title: Hitelesítés hozzáadása Androidon
description: Ismerje meg, hogyan hitelesítheti androidos alkalmazása felhasználóit az Android-alkalmazás felhasználóinak hitelesítésére olyan identitásszolgáltatókkal, mint a Google, a Facebook, a Twitter és a Microsoft.
ms.assetid: 1fc8e7c1-6c3c-40f4-9967-9cf5e21fc4e1
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 705ebb5809840155e6bbf3f8eef091eb95f63e63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461640"
---
# <a name="add-authentication-to-your-android-app"></a>Hitelesítés hozzáadása az Android-alkalmazáshoz
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Összefoglalás
Ebben az oktatóanyagban egy támogatott identitásszolgáltató használatával adja hozzá a hitelesítést a todolista gyorsindítási projekthez Androidon. Ez az oktatóanyag az Első lépések a [mobilalkalmazásokkal] oktatóprogramon alapul, amelyet először el kell végeznie.

## <a name="register-your-app-for-authentication-and-configure-azure-app-service"></a><a name="register"></a>Regisztrálja az alkalmazást hitelesítésre és konfigurálja az Azure App Service szolgáltatást
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a><a name="redirecturl"></a>Az alkalmazás hozzáadása az engedélyezett külső átirányítási URL-címekhez

A biztonságos hitelesítéshez új URL-sémát kell definiálnia az alkalmazáshoz. Ez lehetővé teszi, hogy a hitelesítési rendszer a hitelesítési folyamat befejezése után visszairányítsa az alkalmazásba. Ebben az oktatóanyagban az URL-séma _alkalmazásnevét_ használjuk. Azonban bármilyen URL-sémát használhat. Meg kell egyedi a mobil alkalmazás. Az átirányítás engedélyezése a kiszolgálóoldalon:

1. Az [Azure Portalon]válassza ki az App Service.In the Azure Portal , select your App Service.

2. Kattintson a **Hitelesítés / Engedélyezés** menügombra.

3. Az **Engedélyezett külső átirányítási URL-címek**mezőbe írja be a . `appname://easyauth.callback`  A karakterláncban szereplő _alkalmazásnév_ a mobilalkalmazás URL-séma.  A protokoll normál URL-specifikációját kell követnie (csak betűket és számokat használjon, és betűvel kezdje).  Meg kell jegyeznie a karakterláncot, hogy úgy dönt, ahogy meg kell igazítani a mobil alkalmazás kódját az URL-rendszer több helyen.

4. Kattintson az **OK** gombra.

5. Kattintson a **Mentés** gombra.

## <a name="restrict-permissions-to-authenticated-users"></a><a name="permissions"></a>Engedélyek korlátozása hitelesített felhasználókra
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* Az Android Studio alkalmazásban nyissa meg az oktatóanyaggal befejezett projektet [A mobilalkalmazások kalkulálása]. A **Futtatás** menüben kattintson az **Alkalmazás futtatása**parancsra, és ellenőrizze, hogy az alkalmazás indítása után megjelenik-e egy 401 -es állapotkóddal rendelkező kezeletlen kivétel.

     Ez a kivétel azért fordul elő, mert az alkalmazás nem hitelesített felhasználóként próbál hozzáférni a háttérrendszerhez, de a *TodoItem* tábla hitelesítést igényel.

Ezután frissítse az alkalmazást, hogy hitelesítse a felhasználókat, mielőtt erőforrásokat kérne a mobilalkalmazások háttérrendszeréből.

## <a name="add-authentication-to-the-app"></a>Hitelesítés hozzáadása az alkalmazáshoz
[!INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]



## <a name="cache-authentication-tokens-on-the-client"></a><a name="cache-tokens"></a>Hitelesítési tokenek gyorsítótárazása az ügyfélen
[!INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

## <a name="next-steps"></a>További lépések
Most, hogy befejezte ezt az alapszintű hitelesítési oktatóanyagot, fontolja meg az alábbi oktatóanyagok egyikének folytatását:

* [Push-értesítések hozzáadása androidos alkalmazásához.](app-service-mobile-android-get-started-push.md)
  Ismerje meg, hogyan konfigurálhatja a mobilalkalmazások háttérrendszeréből az Azure értesítési központok használatával leküldéses értesítések küldéséhez.
* [Engedélyezze az offline szinkronizálást az Android-alkalmazásban.](app-service-mobile-android-get-started-offline-data.md)
  Megtudhatja, hogy miként adhat hozzá offline támogatást az alkalmazáshoz a mobilalkalmazások háttérrendszerével. Az offline szinkronizálással a felhasználók&mdash;akkor is kezelhetik a&mdash;mobilalkalmazásokat, hogy megtekintsék, hozzáadják vagy módosítsák az adatokat, ha nincs hálózati kapcsolat.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[A mobilalkalmazások – első lépések]: app-service-mobile-android-get-started.md
[Azure-portál]: https://portal.azure.com/
