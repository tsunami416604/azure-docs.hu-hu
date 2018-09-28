---
title: 'Oktatóanyag: Azure SignalR Service-hitelesítés az Azure Functions segítségével | Microsoft Docs'
description: Ez az oktatóanyag bemutatja, hogyan hitelesítheti az Azure SignalR Service-ügyfeleket
services: signalr
documentationcenter: ''
author: sffamily
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: signalr
ms.workload: tbd
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/18/2018
ms.author: zhshang
ms.openlocfilehash: 8af657c39217f3edcadef6ec0981a31ec7e89aa6
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978422"
---
# <a name="tutorial-azure-signalr-service-authentication-with-azure-functions"></a>Oktatóanyag: Azure SignalR Service-hitelesítés az Azure Functions segítségével

Részletes oktatóanyag arról, hogyan kell egy hitelesítési és privát üzenetküldési funkciókkal rendelkező csevegőszobát felépíteni az Azure Functions, az AppService-hitelesítés és a SignalR Service segítségével.

## <a name="introduction"></a>Bevezetés

### <a name="technologies-used"></a>Alkalmazott technológiák

* [Azure Functions](https://azure.microsoft.com/services/functions/?WT.mc_id=serverlesschatlab-tutorial-antchu) – Felhasználók hitelesítésére és csevegőüzenetek küldésére szolgáló háttérrendszeri API
* [Azure SignalR Service](https://azure.microsoft.com/services/signalr-service/?WT.mc_id=serverlesschatlab-tutorial-antchu) – Új üzenetek küldése a csatlakoztatott csevegőügyfeleknek
* [Azure Storage](https://azure.microsoft.com/services/storage/?WT.mc_id=serverlesschatlab-tutorial-antchu) – A csevegőügyfél felhasználói felületeként szolgáló statikus webhely üzemeltetése

### <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következő szoftverekre van szüksége.

* [Git](https://git-scm.com/downloads)
* [Node.js](https://nodejs.org/en/download/) (10.x verzió)
* [.NET SDK](https://www.microsoft.com/net/download) (2.x verzió, a Functions-bővítmények használatához szükséges)
* [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools) (2-es verzió)
* [Visual Studio Code](https://code.visualstudio.com/) (VS Code), a következő bővítményekkel:
    * [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) – az Azure Functions használatához a VS Code-ban
    * [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) – weblapok helyi kiszolgálása tesztelés céljából


## <a name="sign-into-the-azure-portal"></a>Bejelentkezés az Azure Portalra

Az [Azure Portalon](https://portal.azure.com/) jelentkezzen be a hitelesítő adataival.


## <a name="create-an-azure-signalr-service-instance"></a>Azure SignalR Service-példány létrehozása

Azure Functions-alkalmazását helyileg fogja összeállítani és tesztelni. Az alkalmazás hozzá fog férni egy SignalR Service-példányhoz az Azure-ban, amelyet előre létre kell hozni.

1. Új Azure-erőforrás létrehozásához kattintson az **Erőforrás létrehozása** (**+**) gombra.

1. Keresse meg és válassza ki a **SignalR Service** elemet. Kattintson a **Létrehozás** gombra.

    ![Új SignalR Service](media/signalr-authenticate-azure-functions/signalr-quickstart-new.png)

1. Adja meg a következő információkat:

    | Név | Érték |
    |---|---|
    | Erőforrás neve | A SignalR Service-példány egyedi neve |
    | Erőforráscsoport | Új erőforráscsoport létrehozása |
    | Hely | Válasszon ki egy Önhöz közel eső helyet |
    | Tarifacsomag | Ingyenes |
    
1. Kattintson a **Létrehozás** gombra.


## <a name="initialize-the-function-app"></a>A függvényalkalmazás inicializálása

### <a name="create-a-new-azure-functions-project"></a>Új Azure Functions-projekt létrehozása

1. Egy új VS Code ablak `File > Open Folder` menüpontjában hozzon létre és nyisson meg egy üres mappát egy alkalmas helyen. Ez lesz a létrehozni kívánt alkalmazás fő projektmappája.

1. A VS Code-ban az Azure Functions bővítmény segítségével inicializáljon egy függvényalkalmazást a fő projektmappában.
    1. A VS Code parancskatalógusának megnyitásához válassza a **View (Nézet) > Command Palette (Parancskatalógus)** elemet (billentyűparancs: `Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).
    1. Keresse meg és válassza ki az **Azure Functions: Create New Project** (Azure Functions: Új projekt létrehozása) parancsot.
    1. Megjelenik a projekt főmappája. Válassza ki (vagy keresse meg tallózással).
    1. Amikor a szoftver megkérdezi, hogy melyik nyelvet kívánja használni, válassza a **JavaScript** lehetőséget.

    ![Függvényalkalmazás létrehozása](media/signalr-authenticate-azure-functions/signalr-create-vscode-app.png)


### <a name="install-function-app-extensions"></a>A függvényalkalmazás-bővítmények telepítése

Ebben az oktatóanyagban Azure Functions-kötésekkel lépünk interakcióba az Azure SignalR Service-szel. A legtöbb más kötéshez hasonlóan a SignalR Service kötései is bővítményként érhetők el, amelyeket használat előtt az Azure Functions Core Tools CLI-vel telepíteni kell.

1. Nyisson meg egy terminált a VS Code-ban a **View (Nézet) > Integrated Terminal (Integrált terminál)** menüpontból (billentyűparancs: Ctrl-`).

1. Gondoskodjon róla, hogy a projekt főmappája legyen az aktuális mappa.

1. Telepítse a SignalR Service függvényalkalmazás-bővítményét.
    ```
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.SignalRService -v 1.0.0-preview1-10002
    ```

### <a name="configure-application-settings"></a>Alkalmazásbeállítások konfigurálása

Az Azure Functions futtatókörnyezetének helyi futtatása és hibakeresése során használt alkalmazásbeállítások a **local.settings.json** fájlban találhatóak. Frissítse ezt a fájlt az előzőekben létrehozott SignalR Service-példány kapcsolati sztringjével.

1. A VS Code Explorerben válassza ki a **local.settings.json** fájlt, és nyissa meg.

1. A fájl tartalmát cserélje le a következőre:
    ```json
    {
        "IsEncrypted": false,
        "Values": {
            "AzureSignalRConnectionString": "<signalr-connection-string>",
            "WEBSITE_NODE_DEFAULT_VERSION": "10.0.0",
            "FUNCTIONS_WORKER_RUNTIME": "node"
        },
        "Host": {
            "LocalHttpPort": 7071,
            "CORS": "*"
        }
    }
    ```

    * Adja meg az Azure SignalR Service kapcsolati sztringjét az `AzureSignalRConnectionString` beállítás értékeként. Ezt az értéket az Azure Portalon, az Azure SignalR Service erőforrás **Kulcsok** lapján találja meg. Az elsődleges és a másodlagos kapcsolati sztringek közül bármelyiket használhatja.
    * A `WEBSITE_NODE_DEFAULT_VERSION` beállítást a rendszer a helyi futtatás során nem használja. Akkor van rá szükség, amikor az Azure-ban végez üzembe helyezést.
    * A `Host` szakasz a helyi Functions-gazdagép port- és CORS-beállításait konfigurálja (Azure-beli futtatáskor nincs hatása).

    ![SignalR Service-kulcs megkeresése](media/signalr-authenticate-azure-functions/signalr-get-key.png)

1. Mentse a fájlt.

    ![Helyi beállítások frissítése](media/signalr-authenticate-azure-functions/signalr-update-local-settings.png)


## <a name="create-a-function-to-authenticate-users-to-signalr-service"></a>Függvény létrehozása a felhasználók SignalR Service-ben történő hitelesítéséhez

Amikor a csevegőalkalmazás először nyílik meg a böngészőben, szüksége lesz a kapcsolat hitelesítő adataira ahhoz, hogy kapcsolódhasson az Azure SignalR Service-hez. A függvényalkalmazásban létrehoz egy HTTP által kiváltott függvényt *SignalRInfo* néven, amely visszaadja a kapcsolati adatokat.

1. Nyissa meg a VS Code parancskatalógusát (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. Keresse meg és válassza ki az **Azure Functions: Create Function** (Azure Functions: Függvény létrehozása) parancsot.

1. Adja meg az alábbi információkat:

    | Név | Érték |
    |---|---|
    | Function app folder (Függvényalkalmazás mappája) | Válassza ki a projekt főmappáját |
    | Template (Sablon) | HTTP-trigger |
    | Name (Név) | SignalRInfo |
    | Authorization level (Engedélyszint) | Névtelen |
    
    Létrejön egy, az új függvényt tartalmazó, **SignalRInfo** nevű mappa.

1. Nyissa meg a **SignalRInfo/function.json** fájlt a függvény kötéseinek konfigurálásához. Módosítsa a fájl tartalmát az alábbiak szerint. Ez létrehoz egy bemeneti kötést, amely képes lesz olyan érvényes hitelesítő adatokat létrehozni, amelyekkel az ügyfél csatlakozhat a `chat` nevű Azure SignalR Service-központhoz.

    ```json
    {
        "disabled": false,
        "bindings": [
            {
                "authLevel": "anonymous",
                "type": "httpTrigger",
                "direction": "in",
                "name": "req"
            },
            {
                "type": "http",
                "direction": "out",
                "name": "res"
            },
            {
                "type": "signalRConnectionInfo",
                "name": "connectionInfo",
                "userId": "",
                "hubName": "chat",
                "direction": "in"
            }
        ]
    }
    ```

    A rendszer a `signalRConnectionInfo` kötés `userId` tulajdonságának felhasználásával hoz létre hitelesített SignalR Service-kapcsolatot. Mivel helyi fejlesztésről van szó, a tulajdonság értékét hagyja üresen. Akkor lesz rá szükség, amikor a függvényalkalmazást az Azure-ban telepíti.

1. Nyissa meg a **SignalRInfo/index.js** fájlt a függvénytörzs megtekintéséhez. Módosítsa a fájl tartalmát az alábbiak szerint.

    ```javascript
    module.exports = function (context, req, connectionInfo) {
        context.res = { body: connectionInfo };
        context.done();
    };
    ```

    A függvény a bemeneti kötés SignalR kapcsolati adatait adja vissza az ügyfélnek a HTTP-válasz törzsében.


## <a name="create-a-function-to-send-chat-messages"></a>Függvény létrehozása csevegőüzenetek küldéséhez

A webalkalmazásnak egy HTTP API-ra is szüksége lesz, hogy csevegőüzeneteket tudjon küldeni. Létre fog hozni egy *SendMessage* nevű, HTTP által kiváltott függvényt, amely képes lesz üzeneteket küldeni a SignalR Service-hez csatlakoztatott összes ügyfélnek.

1. Nyissa meg a VS Code parancskatalógusát (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. Keresse meg és válassza ki az **Azure Functions: Create Function** (Azure Functions: Függvény létrehozása) parancsot.

1. Adja meg az alábbi információkat:

    | Név | Érték |
    |---|---|
    | Function app folder (Függvényalkalmazás mappája) | válassza ki a projekt főmappáját |
    | Template (Sablon) | HTTP-trigger |
    | Name (Név) | SendMessage |
    | Authorization level (Engedélyszint) | Névtelen |
    
    Létrejön egy, az új függvényt tartalmazó, **SendMessage** nevű mappa.

1. A függvény kötéseinek konfigurálásához nyissa meg a **SendMessage/function.json** fájlt. Módosítsa a fájl tartalmát az alábbiak szerint.
    ```json
    {
        "disabled": false,
        "bindings": [
            {
                "authLevel": "anonymous",
                "type": "httpTrigger",
                "direction": "in",
                "name": "req",
                "route": "messages",
                "methods": [
                    "post"
                ]
            },
            {
                "type": "http",
                "direction": "out",
                "name": "res"
            },
            {
                "type": "signalR",
                "name": "signalRMessages",
                "hubName": "chat",
                "direction": "out"
            }
        ]
    }
    ```
    Az eredeti függvény két helyen változik meg:
    * Az útvonal `messages` értékre változik, és a HTTP-trigger a **POST** HTTP-metódusra korlátozódik.
    * Hozzáadja a SignalR Service kimeneti kötését, amely üzenete(ke)t küld minden, a `chat` nevű SignalR Service-központhoz csatlakoztatott ügyfélnek.

1. Mentse a fájlt.

1. Nyissa meg a **SendMessage/index.js** fájlt a függvénytörzs megtekintéséhez. Módosítsa a fájl tartalmát az alábbiak szerint.
    ```javascript
    module.exports = function (context, req) {
        const message = req.body;
        message.sender = req.headers && req.headers['x-ms-client-principal-name'] || '';
            
        let recipientUserId = '';
        if (message.recipient) {
            recipientUserId = message.recipient;
            message.isPrivate = true;
        }
    
        context.bindings.signalRMessages = [{
            'userId': recipientUserId,
            'target': 'newMessage',
            'arguments': [ message ]
        }];
        context.done();
    };
    ```
    Ez a függvény továbbküldi a HTTP-kérelem törzsét a SignalR Service-hez csatlakoztatott ügyfeleknek azzal, hogy minden egyes ügyfélen meghívja a `newMessage` nevű függvényt.

    A függvény képes beolvasni a küldő identitását és elfogadni az üzenet törzsében található *recipient* (címzett) nevű értéket, hogy az üzenetet privát módon, csak egy adott felhasználónak lehessen elküldeni. Ezekre a funkciókra az oktatóanyag későbbi részében lesz majd szükség.

1. Mentse a fájlt.


## <a name="create-and-run-the-chat-client-web-user-interface"></a>A csevegőügyfél webes felhasználói felületének létrehozása és futtatása

A csevegőalkalmazás felhasználói felülete egy egyszerű, a VUE JavaScript-keretrendszerrel létrehozott egyoldalas alkalmazás (single page application, SPA). Üzemeltetése a függvényalkalmazástól elválasztva történik. Helyileg a webes felületet a VS Code Live Server-bővítményével fogja futtatni.

1. A VS Code-ban hozzon létre egy új mappát **content** néven a fő projektmappa gyökerében.

1. A **content** mappában hozzon létre egy új fájlt **index.html** néven.

1. Másolja ki és illessze be az **[index.html](https://raw.githubusercontent.com/Azure-Samples/signalr-service-quickstart-serverless-chat/master/docs/demo/chat-with-auth/index.html)** fájl tartalmát.

1. Mentse a fájlt.

1. A függvényalkalmazás helyi futtatásához és hibakereső csatlakoztatásához nyomja le az **F5** billentyűt.

1. Nyissa meg az **index.html** fájlt a VS Code-ban, majd a parancskatalógusból (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`) válassza ki a **Live Server: Open with Live Server** (Live Server: Indítás Live Serverrel) menüpontot. Elindul a Live Server. A Live Server megnyitja az alkalmazást egy böngészőben.

1. Elindul az alkalmazás. A csevegőmezőbe írja be a kívánt üzenetet, majd nyomja le az ENTER billentyűt. Frissítse az alkalmazást, ha látni szeretné az új üzeneteket. Mivel hitelesítés nem lett konfigurálva, az összes üzenet feladója névtelen lesz.


## <a name="deploy-to-azure-and-enable-authentication"></a>Üzembe helyezés az Azure-ban és a hitelesítés engedélyezése

Eddig mind a függvényalkalmazást, mind a csevegőalkalmazást helyileg futtatta. Most viszont az Azure-ban fogja őket üzembe helyezni, és be fogja kapcsolni a hitelesítési és a privát üzenetküldési funkciókat.


### <a name="log-into-azure-with-vs-code"></a>Bejelentkezés az Azure-ba a VS Code-dal

1. Nyissa meg a VS Code parancskatalógusát (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. Keresse meg és válassza ki az **Azure: Sign in** (Azure: Bejelentkezés) parancsot.

1. A bejelentkezési folyamat végrehajtásához kövesse a böngészőben megjelenő utasításokat.


### <a name="configure-function-app-for-authentication"></a>A függvényalkalmazás konfigurálása hitelesítés használatához

Eddig a csevegőalkalmazás névtelenül működött. Az Azure-ban az [App Service-hitelesítéssel](https://docs.microsoft.com/azure/app-service/app-service-authentication-overview) fogja a felhasználókat hitelesíteni. A hitelesített felhasználó felhasználóazonosítója vagy felhasználóneve átadható a *SignalRConnectionInfo* kötésnek, amellyel a felhasználóhoz kapcsolt, hitelesítéshez használható kapcsolati adatokat lehet létrehozni.

Az alkalmazás eldöntheti, hogy az egyes üzeneteket minden csatlakoztatott ügyfélnek, vagy csak az adott felhasználó esetében hitelesített ügyfeleknek küldje-e el.

1. A VS Code-ban nyissa meg a **SendMessage/function.json** fájlt.

1. A*SignalRConnectionInfo* kötés *userId* tulajdonságában adjon meg egy [kötési kifejezést](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings#binding-expressions-and-patterns): `{headers.x-ms-client-principal-name}`. Ez az értéket a hitelesített felhasználó felhasználónevére állítja be. Az attribútumnak most így kell kinéznie:

    ```json
    {
        "type": "signalRConnectionInfo",
        "name": "connectionInfo",
        "userId": "{headers.x-ms-client-principal-name}",
        "hubName": "chat",
        "direction": "in"
    }
    ```

1. Mentse a fájlt.


### <a name="deploy-function-app"></a>A függvényalkalmazás üzembe helyezése

1. Nyissa meg a VS Code parancskatalógusát (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`) majd válassza ki az **Azure Functions: Deploy to Function App** (Azure Functions: Üzembe helyezés a függvényalkalmazásban) parancsot. 

1. Adja meg az alábbi információkat:

    | Név | Érték |
    |---|---|
    | Folder to deploy (Üzembe helyezni kívánt mappa) | Válassza ki a projekt főmappáját |
    | Subscription (Előfizetés) | Válassza ki előfizetését. |
    | Function app (Függvényalkalmazás) | Válassza ki a **Create New Function App** (Új függvényalkalmazás létrehozása) elemet |
    | Function app name (Függvényalkalmazás neve) | Adjon meg egy egyedi nevet |
    | Resource group (Erőforráscsoport) | Azt az erőforráscsoportot válassza ki, amelyikbe a SignalR Service-példány is tartozik. |
    | Storage account (Tárfiók) | Válassza ki a **Create new storage account** (Új tárfiók létrehozása) elemet |
    | Storage account name (Tárfiók neve) | Adjon meg egy egyedi nevet (3-24 karakter, csak betű vagy szám lehet) |
    | Location (Hely) | Válasszon ki egy Önhöz közel eső helyet |
    
    Az Azure-ban létrejön egy új függvényalkalmazás, és megindul az üzembehelyezési folyamat. Várjon, amíg az üzembe helyezés befejeződik.


### <a name="upload-function-app-local-settings"></a>A függvényalkalmazás helyi beállításainak feltöltése

1. Nyissa meg a VS Code parancskatalógusát (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. Keresse meg és válassza ki az **Azure Functions: Upload local settings** (Azure Functions: Helyi beállítások feltöltése) parancsot.

1. Adja meg az alábbi információkat:

    | Név | Érték |
    |---|---|
    | Local settings file (Helyi beállításfájl) | local.settings.json |
    | Subscription (Előfizetés) | Válassza ki előfizetését. |
    | Function app (Függvényalkalmazás) | Válassza ki az előbb telepített függvényalkalmazást |
    | Function app name (Függvényalkalmazás neve) | Adjon meg egy egyedi nevet |

A rendszer feltölti a helyi beállításokat az Azure-beli függvényalkalmazásba. A **Yes to all** (Igen, mindet) lehetőséggel írhatja felül a létező beállításokat, ha szükség van rá.


### <a name="enable-function-app-cross-origin-resource-sharing-cors"></a>Eltérő eredetű erőforrások megosztásának (CORS) engedélyezése a függvényalkalmazásokban

Bár a **local.settings.json** fájlban lehetőség van a CORS beállítására, ez nem érvényesül az Azure-ban futó függvényalkalmazásban. A CORS-t külön kell beállítania.

1. Nyissa meg a VS Code parancskatalógusát (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. Keresse meg és válassza ki az **Azure Functions: Open in portal** (Azure Functions: Megnyitás a portálon) parancsot.

1. Válassza ki az előfizetés és a függvényalkalmazás nevét az alkalmazás megnyitásához az Azure Portalon.

1. A megnyitott portál **A platform szolgáltatásai** lapján válassza ki a **CORS** elemet.

    ![A CORS helye](media/signalr-authenticate-azure-functions/signalr-find-cors.png)

1. Adjon hozzá egy bejegyzést a `*` értékkel.

1. Törölje az összes többi bejegyzést.

1. Kattintson a **Mentés** gombra a CORS-beállítások mentéséhez.

    ![A CORS beállítása](media/signalr-authenticate-azure-functions/signalr-set-cors.png)

> [!NOTE]
> Éles környezetben használt alkalmazások esetében ahelyett, hogy minden forráson egyszerre engedélyeznénk a CORS funkciót (`*`), biztonságosabb megoldás külön bejegyzést létrehozni minden egyes olyan tartományhoz, amelynek szüksége van rá.


### <a name="update-the-web-app"></a>A webalkalmazás frissítése

1. Nyissa meg a függvényalkalmazás áttekintő lapját az Azure Portalon.

1. Másolja ki a függvényalkalmazás URL-címét.

    ![URL-cím helye](media/signalr-authenticate-azure-functions/signalr-get-url.png)


1. A VS Code-ban nyissa meg az **index.html** fájlt, és cserélje le az `apiBaseUrl` értéket a függvényalkalmazás URL-címére.

1. Az alkalmazás Microsoft-fiókkal végzett, illetve Azure Active Directory-, Facebook-, Twitter- vagy Google-hitelesítések használatára konfigurálható be. Az `authProvider` érték beállításával válassza ki a használni kívánt hitelesítési szolgáltatót.

1. Mentse a fájlt.


### <a name="deploy-the-web-application-to-blob-storage"></a>A webalkalmazás üzembe helyezése blobtárolón

A webalkalmazást az Azure Blob Storage statikuswebhely-szolgáltatásával fogjuk üzemeltetni.

1. Új Azure-erőforrás létrehozásához kattintson az **Új** (**+**) gombra.

1. A **Storage** menüben válassza ki a **Tárfiók** elemet.

1. Adja meg a következő információkat:

    | Név | Érték |
    |---|---|
    | Név | Adjon meg egy egyedi nevet a blobtárfióknak |
    | Fióktípus | StorageV2 (általános célú v2) |
    | Hely | Ugyanazt a régiót válassza ki, mint amit a többi erőforrásnál |
    | Replikáció | Helyileg redundáns tárolás (LRS) |
    | Teljesítmény | Standard |
    | Hozzáférési szint | Gyakori |
    | Erőforráscsoport | Ugyanazt az erőforráscsoportot válassza ki, mint az oktatóanyag többi erőforrásánál |

1. Kattintson a **Létrehozás** gombra.

    ![Tároló létrehozása](media/signalr-authenticate-azure-functions/signalr-create-storage.png)

1. A létrehozott tárfiókot nyissa meg az Azure Portalon.

1. A bal oldali navigációs menüben válassza a **Statikus webhely (előzetes verzió)** lehetőséget.

1. Válassza az **Engedélyezés** lehetőséget.

1. Adja meg az `index.html` értéket az **Indexdokumentum neve** mezőben.

1. Kattintson a **Mentés** gombra.

    ![Statikus fájlok konfigurálása](media/signalr-authenticate-azure-functions/signalr-static-websites.png)

1. A lapon a **$web** hivatkozásra kattintva nyissa meg a blobtárolót.

1. Kattintson a **Feltöltés** lehetőségre, és töltse fel az **index.html** fájlt a **content** mappából.

1. Lépjen vissza a **Statikus webhely** lapra. Jegyezze fel az **Elsődleges végpont** mező értékét. Ez a webalkalmazás URL-címe.


### <a name="enable-app-service-authentication"></a>Az App Service-hitelesítés engedélyezése

Az App Service-hitelesítés a Microsoft-fiókkal végzett, illetve az Azure Active Directory-, Facebook-, Twitter- és Google-hitelesítést támogatja.

1. Hagyja megnyitva a függvényalkalmazást a portálon, keresse meg **A platform szolgáltatásai** lapot, és válassza ki a **Hitelesítés/Engedélyezés** lehetőséget.

1. Kapcsolja **Be** az App Service-hitelesítést.

1. Az **Elvégzendő művelet, ha a kérés nincs hitelesítve** területen válassza a „Bejelentkezés a következővel: {előzőleg kiválasztott hitelesítési szolgáltató}” lehetőséget.

1. Az **Engedélyezett külső átirányítási URL-címek** mezőben adja meg az előző lépésben a tárfiók létrehozásakor feljegyzett elsődleges webes végpontot.

1. A konfigurációs folyamat befejezéséhez kövesse a kiválasztott hitelesítési szolgáltató dokumentációját.

    - [Azure Active Directory](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-active-directory-authentication)
    - [Facebook](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-facebook-authentication)
    - [Twitter](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-twitter-authentication)
    - [Microsoft-fiók](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-microsoft-authentication)
    - [Google](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-google-authentication)


### <a name="try-the-application"></a>Az alkalmazás kipróbálása

1. A böngészőben nyissa meg a tárfiók elsődleges webes végpontját.

1. Válassza a **Bejelentkezést**, hogy hitelesíthesse magát a kiválasztott hitelesítési szolgáltatóval.

1. Küldjön nyilvános üzeneteket a fő csevegőmezőből.

1. Privát üzenetek küldéséhez kattintson a csevegési előzményekben egy felhasználónévre. Ezeket az üzeneteket csak a kijelölt címzett fogja megkapni.

Gratulálunk! Üzembe helyezett egy valós idejű, kiszolgáló nélküli csevegőalkalmazást!

![Bemutató](media/signalr-authenticate-azure-functions/signalr-serverless-chat.gif)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni szeretné a jelen oktatóanyag elvégzése során létrehozott erőforrásokat, törölje az erőforráscsoportot az Azure Portalon.


## <a name="next-steps"></a>További lépések

Ebből az oktatóanyagból elsajátította, hogyan használható az Azure Functions az Azure SignalR Service szolgáltatással. További információkat is találhat arról, hogyan lehet valós idejű, kiszolgáló nélküli alkalmazásokat létrehozni az Azure Functions SignalR Service-kötéseivel.

> [!div class="nextstepaction"]
> [Valós idejű alkalmazások összeállítása Azure Functions-függvényekkel](signalr-overview-azure-functions.md)
