---
title: Hitelesítés hozzáadása az Apache Cordova és a Mobile Apps között | Microsoft Docs
description: Megtudhatja, hogyan használhatja a Mobile Apps a Azure App Serviceban az Apache Cordova-alkalmazás felhasználóinak a különböző identitás-szolgáltatók, például a Google, a Facebook, a Twitter és a Microsoft segítségével történő hitelesítéséhez.
services: app-service\mobile
documentationcenter: javascript
author: elamalani
manager: crdun
editor: ''
ms.assetid: 10dd6dc9-ddf5-423d-8205-00ad74929f0d
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: c42cf2c944457b6b738578f738cd338977df8185
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388810"
---
# <a name="add-authentication-to-your-apache-cordova-app"></a>Hitelesítés hozzáadása az Apache Cordova-alkalmazáshoz
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> A Visual Studio App Center támogatja a végpontok közötti, valamint az integrált szolgáltatásközpont és a mobilalkalmazás közötti fejlesztést. A fejlesztők **buildelési**, **tesztelési** és **elosztási** szolgáltatásokkal állíthatják be a folyamatos integrációval és szolgáltatásnyújtással kapcsolatos folyamatot. Az alkalmazás üzembe helyezése után a fejlesztők **elemzési** és **diagnosztikai** szolgáltatásokkal monitorozhatják az alkalmazás állapotát és használatát, illetve **leküldéses** szolgáltatással kommunikálhatnak a felhasználókkal. Emellett a fejlesztők a **Hitelesítés** szolgáltatással hitelesíthetik felhasználóikat, az **Adatok** szolgáltatással pedig megőrizhetik és szinkronizálhatják az alkalmazásadatokat a felhőben.
>
> Ha szeretné integrálni a Cloud Servicest a mobil alkalmazásban, regisztráljon [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) még ma.

## <a name="summary"></a>Összefoglalás
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

    Cserélje le a `https://login.microsoftonline.com` értéket a OAuth gazdagépre az előző táblázatból.  A Content-Security – Policy meta címkével kapcsolatos további információkért tekintse meg a [Content-Security – Policy dokumentáció].

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

4. Az imént hozzáadott kódban cserélje le a `SDK_Provider_Name` értéket a bejelentkezési szolgáltatójának nevére. Azure Active Directory például a következőt használja: `client.login('aad')`.
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
