---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 2e65c5f0d6c2d493abcd61bea4077cbdd8ff95fa
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52331074"
---
## <a name="register-your-application"></a>Alkalmazás regisztrálása

Többféle módon kell regisztrálni egy alkalmazást. Válassza ki a lehetőséget, amely leginkább megfelel az igényeinek:
* [Expressz mód – az SPA-ALKALMAZÁSOKBA a rövid útmutató segítségével az alkalmazás konfigurálása](#option-1-register-your-application-express-mode)
* [Az alkalmazás-beállítások manuális konfigurálásával mód – speciális](#option-2-register-your-application-advanced-mode)

### <a name="option-1-register-your-application-express-mode"></a>1. lehetőség: Regisztrálja az alkalmazást (Expressz mód)

1. Jelentkezzen be a [az Azure portal alkalmazásregisztráció (előzetes verzió)](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs) kell regisztrálni egy alkalmazást.
1. Az a **alkalmazás regisztrálása** lap, adja meg az alkalmazás nevét.
1. A **támogatott fióktípusok**válassza **fiókok minden olyan szervezeti directory és személyes Microsoft-fiókok**.
1. Miután végzett, válassza a **Regisztrálás** lehetőséget.
1. Az utasítások a rövid útmutató letöltéséhez, és automatikusan konfigurálja az új alkalmazást, egyetlen kattintással.

### <a name="option-2-register-your-application-advanced-mode"></a>2. lehetőség: Regisztrálja az alkalmazást (Speciális mód)

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) kell regisztrálni egy alkalmazást.
1. Ha a fiókja több bérlőhöz is biztosít hozzáférést, válassza ki a fiókot az oldal jobb felső sarkában, és állítsa a portálmunkamenetét a kívánt Azure AD-bérlőre.
1. A bal oldali navigációs panelen válassza az **Azure Active Directory** szolgáltatást, majd az **Alkalmazásregisztrációk (előzetes verzió) > Új alkalmazás regisztrálása** lehetőséget.
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

<p/>

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
        authority: "https://login.microsoftonline.com/common",
        graphScopes: ["user.read"],
        graphEndpoint: "https://graph.microsoft.com/v1.0/me"
    };
    ```

<ol start="2">
<li>
Cserélje le <code>Enter the application Id here</code> az imént regisztrált alkalmazás azonosítójával.
</li>
</ol>
