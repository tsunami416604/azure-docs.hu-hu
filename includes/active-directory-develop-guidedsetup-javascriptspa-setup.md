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
ms.openlocfilehash: 5ce0f18c1ec7a0fcb6465ab20e774976552687f1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133408"
---
## <a name="set-up-your-web-server-or-project"></a>A webalkalmazás-kiszolgáló vagy a projekt beállítása

> Töltse le a mintaprojektet ehelyett inkább? A következő lehetőségek közül választhat:
> 
> - A projekt futtathat helyi webkiszolgálót Node.js, például [letölteni a projektfájlokat](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).
>
> - (Nem kötelező) Futtassa a projektet az IIS-kiszolgálóval való [töltse le a Visual Studio-projekt](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).
>
> És ezt követően a kód minta konfigurálásához, végrehajtása előtt ugorjon a [konfigurációs lépés](#register-your-application).

## <a name="prerequisites"></a>Előfeltételek

* Ez az oktatóanyag futtatásához kell egy helyi webkiszolgálót, például [Node.js](https://nodejs.org/en/download/), [.NET Core](https://www.microsoft.com/net/core), vagy az IIS Express integrációs [Visual Studio 2017](https://www.visualstudio.com/downloads/).

* Ha a Node.js használatával futtassa a projektet, telepítenek egy integrált fejlesztőkörnyezet (IDE), például [Visual Studio Code](https://code.visualstudio.com/download), szerkesztheti a projektfájlokat.

* Ez az útmutató utasításait a Node.js és a Visual Studio 2017 alapulnak, de használhatja a fejlesztői környezetben vagy webalkalmazás-kiszolgáló.

## <a name="create-your-project"></a>A projekt létrehozása

> ### <a name="option-1-nodejs-or-other-web-servers"></a>Option 1: NODE.js vagy más webes kiszolgálókhoz
> Győződjön meg arról, hogy telepítve van-e [Node.js](https://nodejs.org/en/download/), majd tegye a következőket:
> - Hozzon létre egy mappát az alkalmazások üzemeltetéséhez.
>
> ### <a name="option-2-visual-studio"></a>Option 2: Visual Studio
> Ha a Visual Studio használata, és a egy új projektet hoz létre, tegye a következőket:
> 1. A Visual Studióban válassza ki a **fájl** > **új** > **projekt**.
> 1. A **Visual C#\Web** területen válassza az **ASP.NET Web Application (.NET Framework)** (ASP.NET-webalkalmazás (.NET-keretrendszer)) lehetőséget.
> 1. Adja meg az alkalmazás nevét, és válassza **OK**.
> 1. A **új ASP.NET-webalkalmazás**válassza **üres**.

## <a name="create-the-spa-ui"></a>Az SPA felhasználói felület létrehozása
1. Hozzon létre egy *index.html* fájlt a JavaScript SPA-ALKALMAZÁSOKBA. Ha Visual Studio használata esetén válassza a project (projekt gyökérmappájában), kattintson a jobb gombbal, majd válassza **Hozzáadás** > **új elem** > **HTML-oldalt**, és a fájl neve *index.html*.

1. Az a *index.html* fájlt, adja hozzá a következő kódot:

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>Quickstart for MSAL JS</title>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
       <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
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

   > [!TIP]
   > Az előző szkriptben MSAL.js verzióját a legújabb elérhető verzió alatt lecserélheti [MSAL.js kiadások](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).
