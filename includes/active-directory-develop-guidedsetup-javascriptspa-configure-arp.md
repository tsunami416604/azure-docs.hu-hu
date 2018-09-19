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
ms.openlocfilehash: fc06da3b1ad66aa15237a25d2f50374043c860ba
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46293834"
---
## <a name="add-the-applications-registration-information-to-your-app"></a>Az alkalmazás regisztrációs adatok hozzáadása az alkalmazáshoz

Ebben a lépésben azt kell átirányítási URL-címe az alkalmazás regisztrációs adatok konfigurálása, majd vegye fel az Alkalmazásazonosítót a JavaScript SPA-alkalmazás.

### <a name="configure-redirect-url"></a>Átirányítási URL-cím konfigurálása

Konfigurálja a `Redirect URL` az index.html oldalon, a webkiszolgáló-alapú URL-mezőt, majd kattintson a *frissítés*.


> #### <a name="visual-studio-instructions-for-obtaining-redirect-url"></a>A Visual Studio utasításokat átirányítási URL-cím beszerzése
> Az átirányítási URL-cím beszerzése:
> 1.    A *Megoldáskezelőben*, válassza ki a projektet, és tekintse meg a `Properties` ablak (Ha nem látja a Tulajdonságok ablakban, nyomja le az `F4`)
> 2.    Másolja az értéket a `URL` a vágólapra:<br/> ![Projekt tulajdonságai](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    Illessze be az értéket, egy `Redirect URL` ezt oldal felső részén kattintson a `Update`

<p/>

> #### <a name="setting-redirect-url-for-node"></a>A Node beállítás átirányítási URL-cím
> A node.js-ben, beállíthatja a webes kiszolgálóport a *server.js* fájlt. Ebben az oktatóanyagban referencia azonban nyugodtan elérhető más port használata 30662 portot használja. Minden esetben kövesse az alábbi utasításokat egy átirányítási URL-címet az alkalmazás regisztrációs adatainak beállításához:<br/>
> Állítsa be `http://localhost:30662/` , egy `Redirect URL` felső részén ezt oldal vagy használja `http://localhost:[port]/` használata egy egyéni TCP-portot (ahol *[port]* a egyéni TCP-port száma) és kattintson a "Frissítés"

### <a name="configure-your-javascript-spa-application"></a>A JavaScript SPA-alkalmazás konfigurálása

1.  Hozzon létre egy fájlt `msalconfig.js` tartalmazó az alkalmazás regisztrációs adatok. Ha a Visual Studiót használja, válassza ki a project (projekt gyökérmappájában), kattintson a jobb gombbal, majd válassza: `Add`  >  `New Item`  >  `JavaScript File`. Nevezze el `msalconfig.js`
2.  Adja hozzá a következő kódot a `msalconfig.js` fájlt:

```javascript
var msalconfig = {
    clientID: "[Enter the application Id here]",
    redirectUri: location.origin
};
``` 
