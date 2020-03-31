---
title: Hitelesítés hozzáadása az Apache Cordova-n
description: Ismerje meg, hogyan használhatja a mobilalkalmazásokat az Azure App Service szolgáltatásban az Apache Cordova alkalmazás felhasználóinak hitelesítésére olyan identitásszolgáltatókkal, mint a Google, a Facebook, a Twitter és a Microsoft.
ms.assetid: 10dd6dc9-ddf5-423d-8205-00ad74929f0d
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 3714ce2a8098608851991115aa82afdc00d08a47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77459388"
---
# <a name="add-authentication-to-your-apache-cordova-app"></a>Hitelesítés hozzáadása az Apache Cordova alkalmazáshoz
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Összefoglalás
Ebben az oktatóanyagban hitelesítést ad hozzá a todolista gyorsindítási projekthez az Apache Cordova-on egy támogatott identitásszolgáltató használatával. Ez az oktatóanyag az Első lépések a [mobilalkalmazásokkal] oktatóprogramon alapul, amelyet először el kell végeznie.

## <a name="register-your-app-for-authentication-and-configure-the-app-service"></a><a name="register"></a>Az alkalmazás regisztrálása hitelesítésre és az App Service konfigurálása
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

[Tekintse meg hasonló lépéseket ismertető videót](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-8-Azure-authentication)

## <a name="restrict-permissions-to-authenticated-users"></a><a name="permissions"></a>Engedélyek korlátozása hitelesített felhasználókra
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Most ellenőrizheti, hogy a háttérrendszerhez való névtelen hozzáférés le van-e tiltva. A Visual Studio-ban:

* Nyissa meg a projektet, amelyet akkor hozott létre, amikor befejezte az [oktatóanyagot Az első lépések a mobilalkalmazásokkal].
* Futtassa alkalmazását a **Google Android Emulátor**.
* Ellenőrizze, hogy az alkalmazás indítása után nem várt csatlakozási hiba jelenik-e meg.

Ezután frissítse az alkalmazást a felhasználók hitelesítéséhez, mielőtt erőforrásokat kérne a mobilalkalmazás-háttérrendszertől.

## <a name="add-authentication-to-the-app"></a><a name="add-authentication"></a>Hitelesítés hozzáadása az alkalmazáshoz
1. Nyissa meg a projektet a `www/index.html` **Visual Studióban,** majd nyissa meg a fájlt szerkesztésre.
2. Keresse `Content-Security-Policy` meg a metacímkét a fejrészben.  Adja hozzá az OAuth állomást az engedélyezett források listájához.

   | Szolgáltató | SDK-szolgáltató neve | OAuth állomás |
   |:--- |:--- |:--- |
   | Azure Active Directory | aad | https://login.microsoftonline.com |
   | Facebook | Facebook | https://www.facebook.com |
   | Google | Google | https://accounts.google.com |
   | Microsoft | microsoftaccount | https://login.live.com |
   | Twitter | Twitter | https://api.twitter.com |

    Egy példa a Content-Security-Policy (az Azure Active Directoryszámára implementálva) a következő:

        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
            data: gap: https://login.microsoftonline.com https://yourapp.azurewebsites.net; style-src 'self'">

    Cserélje `https://login.microsoftonline.com` le az előző tábla OAuth állomására.  A tartalom-biztonság-biztonság-házirend metacímkéről a [Tartalom-biztonság-házirend dokumentációjában olvashat bővebben.]

    Egyes hitelesítésszolgáltatók nem követelik meg a tartalom-biztonság-házirend módosítását, ha megfelelő mobileszközökön használják őket.  Ha például Android-eszközön használja a Google-hitelesítést, nincs szükség a Tartalom-biztonság-biztonság irányelv módosítására.

3. Nyissa `www/js/index.js` meg a fájlt `onDeviceReady()` szerkesztésre, keresse meg a metódust, és az ügyfél létrehozási kódja alatt adja hozzá a következő kódot:

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

    Ez a kód felülírja a táblahivatkozást létrehozó meglévő kódot, és frissíti a felhasználói felületet.

    A login() metódus elindítja a hitelesítést a szolgáltatóval. A login() metódus egy aszinkron függvény, amely javascript-ígéretet ad vissza.  Az inicializálás többi része az ígéretválaszon belül reked, így a rendszer nem hajtja végre, amíg a login() metódus be nem fejeződik.

4. Az imént hozzáadott `SDK_Provider_Name` kódban cserélje le a bejelentkezési szolgáltató nevét. Az Azure Active Directory hoz `client.login('aad')`például használja a használatát.
5. Futtassa a projektet.  Amikor a projekt inicializálása befejeződött, az alkalmazás megjeleníti a kiválasztott hitelesítésszolgáltató OAuth bejelentkezési lapját.

## <a name="next-steps"></a><a name="next-steps"></a>Következő lépések
* További információ az Azure App [Service-hitelesítésről.]
* Folytassa az oktatóanyagot az Apache Cordova alkalmazáspush [értesítéseinek] hozzáadásával.

Útmutató az SDK-k használatához.

* [Apache Cordova SDK]
* [ASP.NET Server SDK]
* [Node.js Server SDK]

<!-- URLs. -->
[A mobilalkalmazások – első lépések]: app-service-mobile-cordova-get-started.md
[Tartalom-Biztonsági házirend dokumentációja]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[Leküldéses értesítések]: app-service-mobile-cordova-get-started-push.md
[A hitelesítés –]: app-service-mobile-auth.md
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
