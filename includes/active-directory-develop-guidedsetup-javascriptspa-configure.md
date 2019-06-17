---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 050bae64a62e90bdb74c93948109e255b69d7518
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133106"
---
## <a name="register-your-application"></a>Alkalmazás regisztrálása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Ha a fiókja egynél több bérlőhöz hozzáférést biztosít, válassza ki a fiókot a jobb felső sarokban, és majd állítsa be a webportál-munkamenetet, amely a használni kívánt Azure AD-bérlőhöz.
1. Nyissa meg a fejlesztők a Microsoft identity platform [alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lapot.
1. Ha a **alkalmazás regisztrálása** lap, adja meg az alkalmazás nevét.
1. A **támogatott fióktípusok**válassza **fiókok minden olyan szervezeti directory és személyes Microsoft-fiókok**.
1. Alatt a **átirányítási URI-t** szakaszban, a legördülő listában válassza ki a **webes** platformmal, és ezután adja meg az alkalmazás URL-cím, amely azon alapul, a webkiszolgálón. 

   Beállítás és az átirányítási URL-címet a Visual Studio és a Node.js beszerzésével kapcsolatos információkért tekintse meg a következő két szakasz.

1. Kattintson a **Register** (Regisztrálás) elemre.
1. Az alkalmazás **áttekintése** lapon, vegye figyelembe a **Alkalmazásazonosítót (ügyfél)** értéket későbbi használatra.
1. Ez a rövid útmutatóhoz a [Implicit folyamat megadása](../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md) engedélyezni kell. A regisztrált alkalmazás bal oldali panelén válassza **hitelesítési**.
1. A **speciális beállítások**alatt **típusú Implicit engedélyezés**, jelölje be a **azonosító-jogkivonatokat** és **hozzáférési jogkivonatokat** jelölőnégyzeteket. Azonosító-jogkivonatokat és hozzáférési tokenek szükség, mert ez az alkalmazás a felhasználók és a egy API-t.
1. Kattintson a **Mentés** gombra.

> #### <a name="set-a-redirect-url-for-nodejs"></a>Egy átirányítási URL-Címének beállítása a node.js-ben
> A node.js-ben, beállíthatja a webes kiszolgálóport a *server.js* fájlt. Ebben az oktatóanyagban port 30662 referenciaként, de az összes rendelkezésre álló portot is használhat. 
>
> Egy átirányítási URL-címet az alkalmazás regisztrációs adatainak beállításához váltson vissza a **Alkalmazásregisztráció** ablaktáblán tegye a következők egyikét:
>
> - Állítsa be *`http://localhost:30662/`* , a **átirányítási URL-cím**.
> - Ha egy egyéni TCP-portot használja, használja a *`http://localhost:<port>/`* (ahol  *\<port >* a egyéni TCP-port száma).
>
> #### <a name="set-a-redirect-url-for-visual-studio"></a>Egy átirányítási URL-Címének beállítása a Visual Studio
> Az átirányítási URL-cím beszerzése a Visual Studióhoz, tegye a következőket:
> 1. A **Megoldáskezelőben**, válassza ki a projektet.
>
>    A **tulajdonságok** ablak nyílik meg. Ha nem nyílik meg, nyomja le az **F4**.
>
>    ![A projekt tulajdonságai JavaScriptSPA ablak](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)
>
> 1. Másolás a **URL-cím** értéket.
 
> 1. Váltson vissza a **Alkalmazásregisztráció** ablaktáblán, és illessze be a másolt érték a **átirányítási URL-cím**.

#### <a name="configure-your-javascript-spa"></a>A JavaScript SPA konfigurálása

1. Az a *index.html* projekt a telepítés során létrehozott fájlt adja hozzá az alkalmazás regisztrációs adatok. A fájl elején belül a `<script></script>` címkéket, adja hozzá a következő kódot:

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "<Enter_the_Application_Id_here>",
            authority: "https://login.microsoftonline.com/<Enter_the_Tenant_info_here>"
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    Az elemek magyarázata:
    - *\<Enter_the_Application_Id_here >* van a **Alkalmazásazonosítót (ügyfél)** a regisztrált alkalmazás.
    - *\<Enter_the_Tenant_info_here >* értéke a következők egyikét:
       - Ha az alkalmazás támogatja a *ebben a könyvtárban szervezeti fiókok*, cserélje le ezt az értéket a **Bérlőazonosító** vagy **bérlőnevet** (például  *contoso.microsoft.com*).
       - Ha az alkalmazás támogatja a *bármely szervezeti directory fiókok*, cserélje le ezt az értéket **szervezetek**.
       - Ha az alkalmazás támogatja a *fiókok minden olyan szervezeti directory és személyes Microsoft-fiókok*, cserélje le ezt az értéket **közös**. A támogatási korlátozása *személyes Microsoft-fiókok csak*, cserélje le ezt az értéket **fogyasztók**.
