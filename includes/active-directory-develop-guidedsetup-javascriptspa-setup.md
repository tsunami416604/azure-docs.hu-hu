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
ms.openlocfilehash: 3274e5929985b2a78c5b463622be6cdbd7320d79
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47061062"
---
## <a name="setting-up-your-web-server-or-project"></a>A webalkalmazás-kiszolgáló vagy a projekt beállítása

> Töltse le a mintaprojektet ehelyett inkább?
> - [Töltse le a projektfájlok](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) helyi webkiszolgáló, például a csomópont
>
> vagy
> - [A Visual Studio-projekt letöltése](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)
>
> És folytassa a [konfigurációs lépés](#register-your-application) a kódminta konfigurálása előtt futtassa a jelentést.

## <a name="prerequisites"></a>Előfeltételek
Például egy helyi webkiszolgálót [Node.js](https://nodejs.org/en/download/), [.NET Core](https://www.microsoft.com/net/core), vagy az IIS Express integrációs [Visual Studio 2017](https://www.visualstudio.com/downloads/) ebben az oktatóanyagban futtatásához szükséges.

Az útmutató utasításainak Node.js és a Visual Studio 2017 alapulnak, de nyugodtan használhatja a fejlesztői környezetben vagy webalkalmazás-kiszolgáló.

## <a name="create-your-project"></a>A projekt létrehozása

> ### <a name="option-1-node-other-web-servers"></a>1. lehetőség: Csomópont / egyéb webkiszolgáló
> Győződjön meg arról, hogy telepítve van-e [Node.js](https://nodejs.org/en/download/), hajtsa végre az alábbi lépést:
> - Hozzon létre egy mappát az alkalmazások üzemeltetéséhez.

<p/><!-- -->

> ### <a name="option-2-visual-studio"></a>2. lehetőség: A Visual Studio
> Ha Visual Studiót használja, és a egy új projektet hoz létre, kövesse az alábbi lépéseket egy új Visual Studio-megoldás létrehozása:
> 1.    A Visual Studióban: **fájl > Új > Projekt**
> 2.    A **Visual C# \Web**válassza **ASP.NET Web Application (.NET-keretrendszer)**
> 3.    Adja meg az alkalmazás nevét, és válassza ki **OK**
> 4.    A **új ASP.NET-webalkalmazás**válassza **üres**


## <a name="create-your-single-page-applications-ui"></a>Az egyoldalas alkalmazás felhasználói felület létrehozása
1.  Hozzon létre egy `index.html` fájlt a JavaScript SPA-ALKALMAZÁSOKBA. Ha a Visual Studiót használja, válassza ki a projektet (projekt gyökérmappájában), kattintson a jobb gombbal, és válassza: **Hozzáadás > Új elem > HTML-oldalt** , és nevezze el index.html.

2.  Adja hozzá a következő kódot a oldalon:
```html
<!DOCTYPE html>
<html>
<head>
        <title>Quickstart for MSAL JS</title>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
        <script src="https://secure.aadcdn.microsoftonline-p.com/lib/0.2.3/js/msal.js"></script>
        <script src="https://code.jquery.com/jquery-3.2.1.min.js"></script>
</head>
<body>
        <h2>Welcome to MSAL.js Quickstart</h2><br/>
        <h4 id="WelcomeMessage"></h4>
        <button id="SignIn" onclick="signIn()">Sign In</button><br/><br/>
        <pre id="json"></pre>
        <script>
            //JS code
        </script>
</body>
</html>
```
