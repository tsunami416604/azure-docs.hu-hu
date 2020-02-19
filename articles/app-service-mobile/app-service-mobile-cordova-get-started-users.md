---
title: Hitelesítés hozzáadása az Apache Cordova-ben
description: Megtudhatja, hogyan használhatja a Mobile Apps a Azure App Serviceban az Apache Cordova-alkalmazás felhasználóinak hitelesítésére, például a Google, a Facebook, a Twitter és a Microsoft számára.
ms.assetid: 10dd6dc9-ddf5-423d-8205-00ad74929f0d
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 3714ce2a8098608851991115aa82afdc00d08a47
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77459388"
---
# <a name="add-authentication-to-your-apache-cordova-app"></a>Hitelesítés hozzáadása az Apache Cordova-alkalmazáshoz
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Összegzés
Ebben az oktatóanyagban egy támogatott identitás-szolgáltató használatával adja hozzá a hitelesítést az Apache Cordova-beli ToDoList gyors üzembe helyezési projekthez. Ez az oktatóanyag az első [Ismerkedés a Mobile Apps] oktatóanyagon alapul, amelyet először végre kell hajtania.

## <a name="register"></a>Az alkalmazás regisztrálása a hitelesítéshez és a App Service konfigurálása
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

[Tekintse meg hasonló lépéseket ismertető videót](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-8-Azure-authentication)

## <a name="permissions"></a>A hitelesített felhasználók engedélyeinek korlátozása
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Most ellenőrizheti, hogy a háttérbeli névtelen hozzáférés le van-e tiltva. A Visual Studióban:

* Nyissa meg az oktatóanyag [Ismerkedés a Mobile Apps]elvégzése Mobile Appsban létrehozott projektet.
* Futtassa az alkalmazást a **Google Android emulatorban**.
* Győződjön meg arról, hogy az alkalmazás elindítása után váratlan sikertelen kapcsolatfelvételi hiba jelenik meg.

Ezután frissítse az alkalmazást a felhasználók hitelesítéséhez, mielőtt erőforrásokat kérne a Mobile apps-háttérből.

## <a name="add-authentication"></a>Hitelesítés hozzáadása az alkalmazáshoz
1. Nyissa meg a projektet a **Visual Studióban**, majd nyissa meg szerkesztésre a `www/index.html` fájlt.
2. Keresse meg a `Content-Security-Policy` META címkét a Head szakaszban.  Adja hozzá a OAuth-gazdagépet az engedélyezett források listájához.

   | Szolgáltató | SDK-szolgáltató neve | OAuth-gazdagép |
   |:--- |:--- |:--- |
   | Azure Active Directory | HRE | https://login.microsoftonline.com |
   | Facebook | Facebook | https://www.facebook.com |
   | Google | Google | https://accounts.google.com |
   | Microsoft | microsoftaccount | https://login.live.com |
   | Twitter | Twitter | https://api.twitter.com |

    Egy példa a Content-Security-Policy (a Azure Active Directory esetében implementált) a következő:

        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
            data: gap: https://login.microsoftonline.com https://yourapp.azurewebsites.net; style-src 'self'">

    Cserélje le a `https://login.microsoftonline.com`t a OAuth gazdagépre az előző táblázatból.  A Content-Security – Policy meta címkével kapcsolatos további információkért tekintse meg a [Content-Security – Policy dokumentáció].

    Egyes hitelesítésszolgáltatók nem igénylik a tartalom-biztonsági házirend módosítását a megfelelő mobileszközökön való használatkor.  Például a Google-hitelesítés androidos eszközön való használata esetén nem szükséges a Content-Security-Policy módosítása.

3. Nyissa meg a `www/js/index.js` fájlt szerkesztésre, keresse meg a `onDeviceReady()` metódust, és az ügyfél-létrehozási kód alatt adja hozzá a következő kódot:

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

    Ez a kód helyettesíti a meglévő kódot, amely létrehozza a tábla hivatkozását, és frissíti a felhasználói felületet.

    A login () metódus elindítja a hitelesítést a szolgáltatóval. A login () metódus egy aszinkron függvény, amely egy JavaScript-ígéretet ad vissza.  Az inicializálás többi része az ígéretre adott válaszon belül kerül, így a rendszer nem hajtja végre, amíg a bejelentkezési () metódus be nem fejeződik.

4. Az imént hozzáadott kódban cserélje le a `SDK_Provider_Name` nevet a bejelentkezési szolgáltatójának nevére. Azure Active Directory esetében például használja a `client.login('aad')`.
5. Futtassa a projektet.  A projekt inicializálásának befejeződése után az alkalmazás megjeleníti a OAuth bejelentkezési oldalát a kiválasztott hitelesítési szolgáltatóhoz.

## <a name="next-steps"></a>Következő lépések
* További információ [A hitelesítés ismertetése] .
* A [leküldéses értesítések] az Apache Cordova-alkalmazásba való felvételével folytassa az oktatóanyagot.

Útmutató az SDK-k használatához.

* [Apache Cordova SDK]
* [ASP.NET Server SDK]
* [Node.js Server SDK]

<!-- URLs. -->
[Ismerkedés a Mobile Apps]: app-service-mobile-cordova-get-started.md
[Content-Security – Policy dokumentáció]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[Leküldéses értesítések]: app-service-mobile-cordova-get-started-push.md
[A hitelesítés ismertetése]: app-service-mobile-auth.md
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
