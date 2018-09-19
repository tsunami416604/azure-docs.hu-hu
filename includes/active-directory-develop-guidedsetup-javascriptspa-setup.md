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
ms.openlocfilehash: 724166d402f81fa3a2c977d107111f5a0c32571d
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46293632"
---
## <a name="setting-up-your-web-server-or-project"></a>A webalkalmazás-kiszolgáló vagy a projekt beállítása

> Töltse le a mintaprojektet ehelyett inkább?
> - [A Visual Studio-projekt letöltése](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/VisualStudio.zip)
>
> vagy
> - [Töltse le a projektfájlok](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/core.zip) helyi webkiszolgáló, például a csomópont
>
> És folytassa a [konfigurációs lépés](#register-your-application) a kódminta konfigurálása előtt futtassa a jelentést.

## <a name="prerequisites"></a>Előfeltételek
Például egy helyi webkiszolgálót [Node.js](https://nodejs.org/en/download/), [.NET Core](https://www.microsoft.com/net/core), vagy az IIS Express integrációs [Visual Studio 2017](https://www.visualstudio.com/downloads/) ebben az oktatóanyagban futtatásához szükséges.

Az útmutató utasításainak Node.js és a Visual Studio 2017 alapulnak, de nyugodtan használhatja a fejlesztői környezetben vagy webalkalmazás-kiszolgáló.

## <a name="create-your-project"></a>A projekt létrehozása

> ### <a name="option-1-visual-studio"></a>1. lehetőség: A Visual Studio
> Ha Visual Studiót használja, és a egy új projektet hoz létre, kövesse az alábbi lépéseket egy új Visual Studio-megoldás létrehozása:
> 1.    A Visual Studióban:  `File` > `New` > `Project`
> 2.    A `Visual C#\Web`, jelölje be `ASP.NET Web Application (.NET Framework)`
> 3.    Adjon nevet az alkalmazásnak, és kattintson a *OK*
> 4.    A `New ASP.NET Web Application`, jelölje be `Empty`

<p/><!-- -->

> ### <a name="option-2-node-other-web-servers"></a>2. lehetőség: Csomópont / egyéb webkiszolgáló
> Győződjön meg arról, hogy telepítve van-e [Node.js](https://nodejs.org/en/download/), hajtsa végre az alábbi lépést:
> - Hozzon létre egy mappát az alkalmazások üzemeltetéséhez.


## <a name="create-your-single-page-applications-ui"></a>Az egyoldalas alkalmazás felhasználói felület létrehozása
1.  Hozzon létre egy *index.html* fájlt a JavaScript SPA-ALKALMAZÁSOKBA. Ha a Visual Studiót használja, válassza ki a projektet (projekt gyökérmappájában), kattintson a jobb gombbal, és válassza: `Add`  >  `New Item`  >  `HTML page` , és nevezze el index.html
2.  Adja hozzá a következő kódot a oldalon:
```html
<!DOCTYPE html>
<html>
<head>
    <!-- bootstrap reference used for styling the page -->
    <link href="//maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
    <title>JavaScript SPA Guided Setup</title>
</head>
<body style="margin: 40px">
    <button id="callGraphButton" type="button" class="btn btn-primary" onclick="callGraphApi()">Call Microsoft Graph API</button>
    <div id="errorMessage" class="text-danger"></div>
    <div class="hidden">
        <h3>Graph API Call Response</h3>
        <pre class="well" id="graphResponse"></pre>
    </div>
    <div class="hidden">
        <h3>Access Token</h3>
        <pre class="well" id="accessToken"></pre>
    </div>
    <div class="hidden">
        <h3>ID Token Claims</h3>
        <pre class="well" id="userInfo"></pre>
    </div>
    <button id="signOutButton" type="button" class="btn btn-primary hidden" onclick="signOut()">Sign out</button>

    <!-- This app uses cdn to reference msal.js (recommended). 
         You can also download it from: https://github.com/AzureAD/microsoft-authentication-library-for-js -->
    <script src="https://secure.aadcdn.microsoftonline-p.com/lib/0.1.3/js/msal.min.js"></script>

    <!-- The 'bluebird' and 'fetch' references below are required if you need to run this application on Internet Explorer -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/fetch/2.0.3/fetch.min.js"></script>

    <script type="text/javascript" src="msalconfig.js"></script>
    <script type="text/javascript" src="app.js"></script>
</body>
</html>
````
