---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 569a7e74df3016fae133066607fdc0bc32c0044d
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46293763"
---
## <a name="register-your-application"></a>Alkalmazás regisztrálása

Hozzon létre egy alkalmazást, válassza ki az egyiket több módja is van:

### <a name="option-1-register-your-application-express-mode"></a>1. lehetőség: Regisztrálja az alkalmazást (Expressz mód)
Most kell regisztrálnia az alkalmazást a *Microsoft alkalmazásregisztrációs portálon*:

1.  Regisztrálja az alkalmazást a [Microsoft alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/portal/register-app?appType=singlePageApp&appTech=javascriptSpa&step=configure)
2.  Adjon meg egy nevet az alkalmazás és az e-mailben
3.  Ellenőrizze, hogy a beállítás *interaktív telepítés* be van jelölve
4.  Kövesse az utasításokat az Alkalmazásazonosító beszerzése, és illessze be a kódot

### <a name="option-2-register-your-application-advanced-mode"></a>2. lehetőség: Regisztrálja az alkalmazást (Speciális mód)

1. Nyissa meg a [Microsoft alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/portal/register-app) alkalmazás regisztrálása
2. Adjon meg egy nevet az alkalmazás és az e-mailben 
3. Ellenőrizze, hogy a beállítás *interaktív telepítés* nincs bejelölve
4.  Kattintson a `Add Platform`, majd válassza ki `Web`
5. Adja hozzá a `Redirect URL` , amely megfelel az alkalmazás URL-címet, a webkiszolgáló-alapú. Tekintse át az utasításokat az alábbi szakaszokban beállítása / átirányítási URL-cím a Visual Studio és a Python.
6. Kattintson a *Mentés* gombra.

> #### <a name="visual-studio-instructions-for-obtaining-redirect-url"></a>A Visual Studio utasításokat átirányítási URL-cím beszerzése
> Kövesse az utasításokat az átirányítási URL-cím:
> 1.    A *Megoldáskezelőben*, válassza ki a projektet, és tekintse meg a `Properties` ablak (Ha nem látja a Tulajdonságok ablakban, nyomja le az `F4`)
> 2.    Másolja az értéket a `URL` a vágólapra:<br/> ![Projekt tulajdonságai](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    Váltson vissza a *alkalmazásregisztrációs portálon* illessze be az értéket, és a egy `Redirect URL` , és kattintson a "Mentés gombra"

<p/>

> #### <a name="setting-redirect-url-for-node"></a>A Node beállítás átirányítási URL-cím
> A node.js-ben, beállíthatja a webes kiszolgálóport a *server.js* fájlt. Ebben az oktatóanyagban referencia azonban nyugodtan elérhető más port használata 30662 portot használja. Minden esetben kövesse az alábbi utasításokat egy átirányítási URL-címet az alkalmazás regisztrációs adatainak beállításához:<br/>
> - Váltson vissza a *alkalmazásregisztrációs portálon* és `http://localhost:30662/` , egy `Redirect URL`, vagy használjon `http://localhost:[port]/` Ha egy egyéni TCP-portot használja (ahol *[port]* egyéni TCP-portja szám), és kattintson a "Mentés gombra"


#### <a name="configure-your-javascript-spa"></a>A JavaScript SPA konfigurálása

1.  Hozzon létre egy fájlt `msalconfig.js` tartalmazó az alkalmazás regisztrációs adatok. Ha a Visual Studiót használja, válassza ki a project (projekt gyökérmappájában), kattintson a jobb gombbal, majd válassza: `Add`  >  `New Item`  >  `JavaScript File`. Nevezze el `msalconfig.js`
2.  Adja hozzá a következő kódot a `msalconfig.js` fájlt:

```javascript
var msalconfig = {
    clientID: "Enter_the_Application_Id_here",
    redirectUri: location.origin
};
```
<ol start="3">
<li>
Cserélje le <code>Enter_the_Application_Id_here</code> az imént regisztrált alkalmazás azonosítójával
</li>
</ol>
