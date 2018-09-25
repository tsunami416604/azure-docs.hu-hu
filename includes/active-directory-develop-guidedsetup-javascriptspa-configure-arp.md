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
ms.openlocfilehash: eead4c6a66a317c7404205415cbf04c442ffe8d1
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47061017"
---
## <a name="add-the-applications-registration-information-to-your-app"></a>Az alkalmazás regisztrációs adatok hozzáadása az alkalmazáshoz

Ebben a lépésben azt kell átirányítási URL-címe az alkalmazás regisztrációs adatok konfigurálása, majd vegye fel az Alkalmazásazonosítót a JavaScript SPA-alkalmazás.

### <a name="configure-redirect-url"></a>Átirányítási URL-cím konfigurálása

Konfigurálja a `Redirect URL` az index.html oldalon, a webkiszolgáló-alapú URL-mezőt, majd kattintson a *frissítés*.


> #### <a name="visual-studio-instructions-for-obtaining-the-redirect-url"></a>A Visual Studio utasításokat az átirányítási URL-cím beszerzése
> Kövesse az alábbi lépéseket az átirányítási URL-cím:
> 1.    A **Megoldáskezelőben**, válassza ki a projektet, és tekintse meg a **tulajdonságok** ablak. Ha nem látja a **tulajdonságok** ablakban, majd nyomja le a **F4**.
> 2.    Másolja az értéket a **URL-cím** a vágólapra:<br/> ![Projekt tulajdonságai](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    Illessze be az értéket, egy **átirányítási URL-cím** ezt oldal felső részén kattintson a **Update**

<p/>

> #### <a name="setting-redirect-url-for-node"></a>A Node beállítás átirányítási URL-cím
> A node.js-ben, beállíthatja a webes kiszolgálóport a *server.js* fájlt. Ebben az oktatóanyagban a port 30662 referenciaként, de bármilyen más szabad portot is használhat. Kövesse az alábbi utasításokat egy átirányítási URL-címet az alkalmazás regisztrációs adatainak beállításához:<br/>
> Állítsa be `http://localhost:30662/` , egy **átirányítási URL-cím** felső részén ezt oldal vagy használja `http://localhost:[port]/` használata egy egyéni TCP-port (ahol *[port]* a egyéni TCP-port száma) és kattintson a  **Frissítés**

### <a name="configure-your-javascript-spa-application"></a>A JavaScript SPA-alkalmazás konfigurálása

1.  Az a `index.html` projekt beállítása során létrehozott fájlt, adja hozzá az alkalmazás regisztrációs adatok. Adja meg a következő kódot a felül található a `<script></script>` címkék törzsében a `index.html` fájlt:

```javascript
var applicationConfig = {
    clientID: "[Enter the application Id here]",
    graphScopes: ["user.read"],
    graphEndpoint: "https://graph.microsoft.com/v1.0/me"
};
```
