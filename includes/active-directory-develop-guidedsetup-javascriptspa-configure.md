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
ms.openlocfilehash: c0dcfc4ad7edf4d9203b807aa799eb047c753bed
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551664"
---
## <a name="register-your-application"></a>Alkalmazás regisztrálása

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) kell regisztrálni egy alkalmazást.
1. Ha a fiókja több bérlőhöz is biztosít hozzáférést, válassza ki a fiókot az oldal jobb felső sarkában, és állítsa a portálmunkamenetét a kívánt Azure AD-bérlőre.
1. Keresse meg a fejlesztők a Microsoft identity platform [alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lapot.
1. Ha a **alkalmazás regisztrálása** lap, adja meg az alkalmazás nevét.
1. A **támogatott fióktípusok**válassza **fiókok minden olyan szervezeti directory és személyes Microsoft-fiókok**.
1. Alatt a **átirányítási URI-t** szakaszban jelölje be a **webes** platform és az alkalmazás URL-CÍMÉT az értéket a webkiszolgáló alapján állítja be. Tekintse át az utasításokat az alábbi szakaszokban és az átirányítási URL-cím a Visual Studio és a csomópont.
1. Miután végzett, válassza a **Regisztrálás** lehetőséget.
1. Az alkalmazás **áttekintése** lapon, jegyezze fel a **Alkalmazásazonosítót (ügyfél)** értéket.
1. Ez a rövid útmutatóhoz a [Implicit folyamat megadása](../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md) engedélyezni kell. A regisztrált alkalmazás bal oldali navigációs panelén válassza **hitelesítési**.
1. A **speciális beállítások**alatt **típusú Implicit engedélyezés**, mindkettő engedélyezéséhez **azonosító-jogkivonatokat** és **hozzáférési jogkivonatokat** jelölőnégyzeteket. Azonosító-jogkivonatokat és hozzáférési tokenek szükség, mivel ez az alkalmazás a felhasználók és a egy API-t kell.
1. Kattintson a **Mentés** gombra.

> #### <a name="setting-the-redirect-url-for-node"></a>Csomópont átirányítási URL-Címének beállítása
> A node.js-ben, beállíthatja a webes kiszolgálóport a *server.js* fájlt. Ebben az oktatóanyagban a port 30662 referenciaként, de bármilyen más szabad portot is használhat. Kövesse az alábbi utasításokat egy átirányítási URL-címet az alkalmazás regisztrációs adatainak beállításához:<br/>
> - Váltson vissza a *Alkalmazásregisztráció* és állítsa be `http://localhost:30662/` , egy `Redirect URL`, vagy használjon `http://localhost:[port]/` Ha egy egyéni TCP-portot használja (ahol *[port]* a egyéni TCP-port száma).

<p>

> #### <a name="visual-studio-instructions-for-obtaining-the-redirect-url"></a>A Visual Studio utasításokat az átirányítási URL-cím beszerzése
> Kövesse az alábbi lépéseket az átirányítási URL-cím:
> 1. A **Megoldáskezelőben**, válassza ki a projektet, és tekintse meg a **tulajdonságok** ablak. Ha nem látja a **tulajdonságok** ablakban, majd nyomja le a **F4**.
> 2. Másolja az értéket a **URL-cím** a vágólapra:<br/> ![Projekt tulajdonságai](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3. Váltson vissza a *Alkalmazásregisztráció* és állítsa az értékét, mint egy **átirányítási URL-cím**.

#### <a name="configure-your-javascript-spa"></a>A JavaScript SPA konfigurálása

1. Az a `index.html` projekt beállítása során létrehozott fájlt, adja hozzá az alkalmazás regisztrációs adatok. Adja meg a következő kódot a felül található a `<script></script>` címkék törzsében a `index.html` fájlt:

    ```javascript
    var applicationConfig = {
        clientID: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here",
        graphScopes: ["user.read"],
        graphEndpoint: "https://graph.microsoft.com/v1.0/me"
    };
    ```

    Az elemek magyarázata:
    - `Enter_the_Application_Id_here` – ez a regisztrált alkalmazáshoz tartozó **Alkalmazás (ügyfél) azonosítója** érték.
    - `Enter_the_Tenant_Info_Here` – az alábbi lehetőségek egyike lesz:
       - Ha az alkalmazás **az adott szervezeti címtárban lévő fiókokat** támogatja, ezt az értéket a **Bérlőazonosítóra** vagy a **Bérlő nevére** cserélje le (például contoso.microsoft.com)
       - Ha az alkalmazás **bármely szervezeti címtárban lévő fiókot** támogat, ezt az értéket az `organizations` értékre cserélje le
       - Ha az alkalmazás **bármely szervezeti címtárban lévő fiókot és a személyes Microsoft-fiókokat** támogatja, ezt az értéket a `common` értékre cserélje le
