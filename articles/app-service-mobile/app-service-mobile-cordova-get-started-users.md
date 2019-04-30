---
title: Hitelesítés hozzáadása az Apache Cordova Mobile Apps |} A Microsoft Docs
description: Útmutató az Azure App Service Mobile Apps segítségével hitelesítheti a felhasználókat az identitás-szolgáltatóktól, beleértve a Google, Facebook, Twitter és a Microsoft számos Apache Cordova-alkalmazás.
services: app-service\mobile
documentationcenter: javascript
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 10dd6dc9-ddf5-423d-8205-00ad74929f0d
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: 23b5967782cf237ed5af2b802aabbbf9c2f781e7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62114210"
---
# <a name="add-authentication-to-your-apache-cordova-app"></a>Hitelesítés hozzáadása az Apache Cordova-alkalmazáshoz
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Összegzés
Ebben az oktatóanyagban hozzáadja a todolist gyorsútmutató-projekt Apache Cordova támogatott identitásszolgáltatóval a hitelesítést. Ez az oktatóanyag alapján a [A Mobile Apps használatának első lépései] oktatóanyaggal, amely először el kell végeznie.

## <a name="register"></a>Regisztrálja az alkalmazást a hitelesítéshez, és konfigurálja az App Service-ben
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

[Tekintse meg hasonló lépéseket ismertető videót](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-8-Azure-authentication)

## <a name="permissions"></a>A hitelesített felhasználók engedélyeinek korlátozása
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Most ellenőrizheti, hogy a háttérrendszerhez való névtelen hozzáférés le van tiltva. A Visual Studióban:

* Nyissa meg a projektet, hogy elvégezte az oktatóanyag során létrehozott [A Mobile Apps használatának első lépései].
* Futtassa az alkalmazást a a **Google Android Emulator**.
* Győződjön meg arról, hogy nem várt kapcsolati hiba jelenik meg az alkalmazás indítása után.

Ezután frissítse az alkalmazást, hogy a felhasználók hitelesítése előtt erőforrásokat kér a Mobile Apps-háttéralkalmazást.

## <a name="add-authentication"></a>Hitelesítés hozzáadása az alkalmazáshoz
1. Nyissa meg a projektjét a **Visual Studio**, majd nyissa meg a `www/index.html` fájlt szerkesztésre.
2. Keresse meg a `Content-Security-Policy` metaadatként a fő szakaszban.  Adja hozzá az OAuth állomás engedélyezett források listájához.

   | Szolgáltató | SDK-szolgáltató neve | OAuth Host |
   |:--- |:--- |:--- |
   | Azure Active Directory | aad-ben | https://login.microsoftonline.com |
   | Facebook | facebook | https://www.facebook.com |
   | Google | google | https://accounts.google.com |
   | Microsoft | microsoftaccount | https://login.live.com |
   | Twitter | twitter | https://api.twitter.com |

    Tartalom-Security-Policy (az Azure Active Directory implementálva) például a következőképpen történik:

        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
            data: gap: https://login.microsoftonline.com https://yourapp.azurewebsites.net; style-src 'self'">

    Cserélje le `https://login.microsoftonline.com` a OAuth-gazdagéppel a fenti táblázatból.  A tartalom-security-policy metaadatként kapcsolatos további információkért lásd: a [tartalom-Security-Policy dokumentációja].

    Bizonyos hitelesítési szolgáltatók nem igénylik a tartalom-Security-az irányelvek változásai megfelelő mobileszközökön használatakor.  Ha például tartalom-Security-házirend módosítása nélkül szükség, az Android-eszközökön a Google-hitelesítés használatakor.

3. Nyissa meg a `www/js/index.js` fájlt szerkesztésre, keresse meg a `onDeviceReady()` metódust, és az ügyfél létrehozása alatt kódot adja hozzá a következő kódot:

        // Login to the service
        client.login('SDK_Provider_Name')
            .then(function () {

                // BEGINNING OF ORIGINAL CODE

                // Create a table reference
                todoItemTable = client.getTable('todoitem');

                // Refresh the todoItems
                refreshDisplay();

                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);

                // END OF ORIGINAL CODE

            }, handleError);

    Ez a kód felülírja a meglévő kódot, amely a táblahivatkozás hoz létre, és frissíti a felhasználói felület.

    A login() metódus elindítja a hitelesítési szolgáltatóval. A login() metódus egy aszinkron-függvény, amely egy JavaScript Promise adja vissza.  Az inicializálás részeinek helyezik a promise választ, hogy nem végre, amíg be nem fejeződik a login() metódus.

4. Cserélje le az imént hozzáadott kód, `SDK_Provider_Name` a bejelentkezés-szolgáltató nevét. Ha például az Azure Active Directory használata `client.login('aad')`.
5. Futtassa a projektet.  A projekt inicializálása befejeződött, ha az alkalmazás a választott hitelesítési szolgáltatót az OAuth-bejelentkezési oldal mutatja be.

## <a name="next-steps"></a>Következő lépések
* További [Hitelesítéssel kapcsolatban] az Azure App Service használatával.
* Folytassa az oktatóanyagot, hozzáadásával [leküldéses értesítések] az Apache Cordova-alkalmazáshoz.

Útmutató az SDK-k használatához.

* [Apache Cordova SDK]
* [ASP.NET Server SDK]
* [Node.js Server SDK]

<!-- URLs. -->
[A Mobile Apps használatának első lépései]: app-service-mobile-cordova-get-started.md
[Tartalom-Security-Policy dokumentációja]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[Leküldéses értesítések]: app-service-mobile-cordova-get-started-push.md
[Hitelesítéssel kapcsolatban]: app-service-mobile-auth.md
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
