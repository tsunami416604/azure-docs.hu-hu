---
title: "Hitelesítés hozzáadása az Apache Cordova Mobile alkalmazásokkal |} Microsoft Docs"
description: "Útmutató az Azure App Service Mobile Apps segítségével hitelesíti a felhasználókat identitás-szolgáltatóktól, beleértve a Google, a Facebook, a Twitter és a Microsoft számos az Apache Cordova-alkalmazás."
services: app-service\mobile
documentationcenter: javascript
author: conceptdev
manager: crdun
editor: 
ms.assetid: 10dd6dc9-ddf5-423d-8205-00ad74929f0d
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: b5cce832ae7ae83552c2a5ded2f5f5bda0ac76bf
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2018
---
# <a name="add-authentication-to-your-apache-cordova-app"></a>Hitelesítés hozzáadása az Apache Cordova-alkalmazás
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Összegzés
Ebben az oktatóanyagban hozzáadja a todolist gyorsútmutató-projekt Apache Cordova segítségével egy támogatott identitásszolgáltató a hitelesítés. Ez az oktatóanyag alapul a [Ismerkedés a Mobile Apps] oktatóanyag, amely először el kell végeznie.

## <a name="register"></a>Regisztrálja az alkalmazást a hitelesítéshez, és az App Service konfigurálása
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

[Tekintse meg hasonló lépéseket ismertető videót](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-8-Azure-authentication)

## <a name="permissions"></a>A hitelesített felhasználókhoz jogosultságok korlátozása
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Most ellenőrizheti, hogy a háttér névtelen hozzáférés le van tiltva. A Visual Studio:

* Nyissa meg a projektet az oktatóanyag befejezésekor létrehozott [Ismerkedés a Mobile Apps].
* Futtassa az alkalmazást a **Google Android Emulator**.
* Győződjön meg arról, hogy az alkalmazás indítása után megjelenik-e a kapcsolat váratlan hiba.

Következő lépésként frissítse az alkalmazásnak, hogy a felhasználók hitelesítésére előtti erőforrások kér a Mobile Apps-háttéralkalmazás.

## <a name="add-authentication"></a>Hitelesítés hozzáadása az alkalmazáshoz
1. Nyissa meg a projektet a **Visual Studio**, majd nyissa meg a `www/index.html` fájlt szerkesztésre.
2. Keresse meg a `Content-Security-Policy` metaadatként központi szakaszában.  Adja hozzá az OAuth gazdagépet engedélyezett adatforrások listáját.

   | Szolgáltató | SDK-szolgáltató neve | OAuth-állomás |
   |:--- |:--- |:--- |
   | Azure Active Directory | aad-ben | https://login.microsoftonline.com |
   | Facebook | Facebook-on | https://www.Facebook.com |
   | Google | Google | https://Accounts.google.com |
   | Microsoft | MicrosoftAccount | https://Login.live.com |
   | Twitter | Twitter | https://API.Twitter.com |

    Tartalom-biztonsági-Policy (az Azure Active Directory megvalósítva) például a következőképpen történik:

        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
            data: gap: https://login.microsoftonline.com https://yourapp.azurewebsites.net; style-src 'self'">

    Cserélje le `https://login.microsoftonline.com` a OAuth-állomással, a fenti táblázatból.  A tartalom-security-házirend metaadatként kapcsolatos további információkért tekintse meg a [tartalom-Security-házirend dokumentációját].

    Néhány hitelesítésszolgáltatókat tartalom-Security-házirend módosítja a megfelelő mobileszközök használatakor nem szükséges.  Például nem tartalom-Security-házirend módosításai szükségesek, ha a Google-hitelesítést használó Android-eszközön.

3. Nyissa meg a `www/js/index.js` szerkesztésre fájlt, keresse meg a `onDeviceReady()` metódust, és az ügyfél létrehozása alatt kódot adja hozzá a következő kódot:

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

    A login() metódus hitelesítési elindítja a szolgáltatónál. A login() metódus JavaScript ígéret visszaadó aszinkron függvényre.  Az inicializálás részeinek ígéret válasz belül kerül, hogy nem végre, amíg befejeződik a login() metódus.

4. Cserélje le a kódot, amely az előzőekben adott hozzá, `SDK_Provider_Name` a bejelentkezés-szolgáltató neve. Például az Azure Active Directory használata `client.login('aad')`.
5. Futtatja a projektet.  Ha a projekt inicializálása befejeződött, az alkalmazás OAuth bejelentkezési lapját a kiválasztott hitelesítési szolgáltató jelennek meg.

## <a name="next-steps"></a>További lépések
* További [vonatkozó hitelesítési] Azure App Service szolgáltatással.
* Az oktatóanyag folytatásához hozzáadásával [leküldéses értesítések] az Apache Cordova-alkalmazáshoz.

Útmutató az SDK-k használatához.

* [Apache Cordova SDK]
* [ASP.NET Server SDK]
* [Node.js Server SDK]

<!-- URLs. -->
[Ismerkedés a Mobile Apps]: app-service-mobile-cordova-get-started.md
[tartalom-Security-házirend dokumentációját]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[leküldéses értesítések]: app-service-mobile-cordova-get-started-push.md
[vonatkozó hitelesítési]: app-service-mobile-auth.md
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
