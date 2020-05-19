---
title: API hozzáadása az Azure statikus Web Appshoz Azure Functions
description: 'Az Azure statikus Web Apps használatának első lépései: kiszolgáló nélküli API hozzáadása a statikus webalkalmazáshoz Azure Functions használatával.'
services: static-web-apps
author: manekinekko
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/08/2020
ms.author: wachegha
ms.openlocfilehash: a6aee5c8049e03a43c547f419f6c6646617e651c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598457"
---
# <a name="add-an-api-to-azure-static-web-apps-preview-with-azure-functions"></a>API hozzáadása az Azure statikus Web Apps előzetes verziójához Azure Functions

Kiszolgáló nélküli API-kat adhat hozzá az Azure statikus Web Appshoz a Azure Functions-nal való integráció segítségével. Ez a cikk bemutatja, hogyan adhat hozzá és helyezhet üzembe egy API-t egy Azure-beli statikus Web Apps-webhelyhez.

Az API-útvonalak biztonságossá tételével kapcsolatos információkért tekintse meg az [útválasztási útmutatót](routes.md).

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free).
- [Visual Studio Code](https://code.visualstudio.com/)
- [Azure functions-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) a Visual Studio Code-hoz
- [Élő kiszolgáló Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) bővítmény.

## <a name="create-a-git-repository"></a>Git-Tárház létrehozása 

A következő lépések bemutatják, hogyan hozhat létre új tárházat, és hogyan klónozott fájlokat a számítógépére.

1. Új tárház létrehozásához navigáljon a következőhöz: https://github.com/staticwebdev/vanilla-basic/generate .
1. Az _adattár neve_ mezőbe írja be **a My-Vanilla-API**értéket.
1. Kattintson **a tárház létrehozása sablonból**elemre.

   :::image type="content" source="media/add-api/create-repository.png" alt-text="Új Tárház létrehozása a vaníliatől – alapszintű":::

A projekt létrehozása után a Visual Studio Code használatával klónozott a git-tárházat.

1. Az **F1** billentyű lenyomásával nyissa meg a parancsot a parancs palettáján.
1. Illessze be az URL-címet a _git: Clone_ parancssorba, majd nyomja le az **ENTER**billentyűt.

   :::image type="content" source="media/add-api/vscode-git-0.png" alt-text="GitHub-projekt klónozása a Visual Studio Code használatával":::

   :::image type="content" source="media/add-api/github-clone-url.png" alt-text="GitHub-projekt klónozása a Visual Studio Code használatával":::


## <a name="create-your-local-project"></a>A helyi projekt létrehozása

Ebben a szakaszban a Visual Studio Code használatával hozzon létre egy helyi Azure Functions projektet. Később közzéteszi a functions alkalmazást az Azure-ban.

1. Hozzon létre egy **API**nevű almappát a _My-Vanilla-API_ projekten belül.

   > [!NOTE]
   > Ezt a mappát bármilyen nevet megadhatja. Ez a példa a következőt használja: `api` . 

2. Nyomja meg az **F1** billentyűt a parancs paletta megnyitásához
3. Írja be a **Azure functions: új projekt létrehozása...**
4. Nyomja le az **ENTER** billentyűt
5. **Tallózás** kiválasztása
6. Válassza ki az **API** -mappát a projekt-munkaterület könyvtáraként
7. Válassza a **kiválasztás** lehetőséget.

   :::image type="content" source="media/add-api/create-azure-functions-vscode-1.png" alt-text="Új Azure Functions létrehozása a Visual Studio Code használatával":::

8. Adja meg a következő információkat a kérdésekben:

    - _Válasszon egy nyelvet a függvény projekthez_: válassza a **JavaScript** lehetőséget.
    - _Válasszon sablont a projekt első függvényéhez_: válassza a **http-trigger** lehetőséget.
    - _Adja meg a függvény nevét_: Type **GetMessage**
    - _Engedélyezési szint_: válassza a **Névtelen**lehetőséget, amely lehetővé teszi, hogy bárki meghívja a függvény végpontját.
        - Az engedélyezési szintekről az [engedélyezési kulcsok](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)című témakörben olvashat bővebben.

9. Ezen információk használatával a Visual Studio Code egy Azure Functions projektet hoz létre egy HTTP-triggerrel.
    - A helyi projektfájlok a Visual Studio Code Explorer ablakában tekinthetők meg.
    - További információ a létrehozott fájlokról: [generált projektfájlok](https://docs.microsoft.com/azure/azure-functions/functions-develop-vs-code#generated-project-files).

10. Az alkalmazásnak most már ehhez a példához hasonló projekt-struktúrával kell rendelkeznie.

    ```files
    ├── api
    │   ├── GetMessage
    │   │   ├── function.json
    │   │   ├── index.js
    │   │   └── sample.dat
    │   ├── host.json
    │   ├── local.settings.json
    │   ├── package.json
    │   └── proxies.json
    ├── index.html
    ├── readme.md
    └── styles.css
    ```

11. Ezután frissítse a `GetMessage` függvényt az _API/GetMessage/index. js_ alatt a következő kóddal.

    ```JavaScript
    module.exports = async function (context, req) {
      context.res = {
        body: { 
          text: "Hello from the API" 
        }
      };
    };
    ```

12. Frissítse a `GetMessage` konfigurációt a `api/GetMessage/function.json` következő beállításokkal.

    ```json
    {
      "bindings": [
        {
          "authLevel": "anonymous",
          "type": "httpTrigger",
          "direction": "in",
          "name": "req",
          "methods": [
            "get"
          ],
          "route": "message"
        },
        {
          "type": "http",
          "direction": "out",
          "name": "res"
        }
      ]
    }
    ```
    
A fenti beállításokkal az API-végpont:

- HTTP-kérelemmel aktiválva a függvény
- Minden kérelem számára elérhető, a hitelesítési állapottól függetlenül
- A _/API/Message_ útvonalon keresztül elérhető

## <a name="run-the-function-locally"></a>Függvény helyi futtatása

A Visual Studio Code integrálva van [Azure functions Core Tools](https://docs.microsoft.com/azure/azure-functions/functions-run-local) , hogy lehetővé tegye a projekt futtatását a helyi fejlesztési számítógépen, mielőtt közzéteszi az Azure-ban.

1. Futtassa a függvényt az **F5** billentyű lenyomásával a functions alkalmazás elindításához, és a fő eszközök kimenete megjelenik a _terminál_ panelen.

2. Ha Azure Functions Core Tools még nincs telepítve, válassza a **telepítés** lehetőséget a parancssorban.

    A központi eszközök telepítésekor az alkalmazás elindul a _terminál_ panelen. A kimenet részeként megtekintheti a helyileg futtatott HTTP-triggeres függvény URL-végpontját.

    :::image type="content" source="media/add-api/create-azure-functions-vscode-2.png" alt-text="Új Azure Functions létrehozása a Visual Studio Code használatával":::

3. A-t futtató alapvető eszközökkel navigáljon a következő URL-címre egy kérelem végrehajtásához `GET` .

   <http://localhost:7071/api/message>

   A rendszer egy választ ad vissza, amely a következőhöz hasonlóan néz ki a böngészőben:

   :::image type="content" source="media/add-api/create-azure-functions-vscode-3.png" alt-text="Új Azure Functions létrehozása a Visual Studio Code használatával":::

Miután ellenőrizte, hogy a függvény megfelelően fut-e, meghívhatja az API-t a JavaScript-alkalmazásból.

### <a name="call-the-api-from-the-application"></a>Az API meghívása az alkalmazásból

[!INCLUDE [static-web-apps-local-proxy](../../includes/static-web-apps-local-proxy.md)]


#### <a name="update-files-to-access-the-api"></a>Fájlok frissítése az API-hoz való hozzáféréshez

1. Ezután frissítse az _index. html_ fájl tartalmát a következő kóddal, hogy beolvassa a szöveget az API-függvényből, és megjeleníti a képernyőn:

   ```html
   <!DOCTYPE html>
   <html lang="en">

   <head>
     <meta charset="UTF-8">
     <meta name="viewport" content="width=device-width, initial-scale=1.0">
     <link rel="stylesheet" href="styles.css">
     <title>Vanilla JavaScript App</title>
   </head>

   <body>
     <main>
       <h1>Vanilla JavaScript App</h1>
       <p>Loading message from the API: <b id="name">...</b></p>
     </main>

     <script>
       (async function() {
         let { text } = await( await fetch(`/api/message`)).json();
         document.querySelector('#name').textContent = text;
       }())
     </script>
   </body>

   </html>
   ```

   A-t futtató alapvető eszközökkel a [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) Visual Studio Code bővítmény használatával szolgálja ki az _index. html_ fájlt, és megnyithatja a böngészőben. 

2. Nyomja le az **F1** billentyűt, és válassza **az élő kiszolgáló: Megnyitás az élő kiszolgálóval**lehetőséget.

   :::image type="content" source="media/add-api/create-azure-functions-vscode-4.png" alt-text="Új Azure Functions létrehozása a Visual Studio Code használatával":::

   > [!NOTE]
   > A fájl kiszolgálásához más HTTP-kiszolgálókat vagy proxykat is használhat `index.html` . A `index.html` rendszerhez való hozzáférés `file:///` nem fog működni.

### <a name="commit-and-push-your-changes-to-github"></a>A módosítások elvégzése és leküldése a GitHubra

A Visual Studio Code használatával véglegesítheti és leküldheti a módosításokat a távoli git-tárházba.

1. Az **F1** billentyű lenyomásával megnyílik a parancs paletta
1. Írja be **a git: ALL commit**
1. Véglegesítő üzenet hozzáadása
1. Írja be a **git: push**

## <a name="create-a-static-web-app"></a>Statikus Webalkalmazás létrehozása

:::image type="content" source="media/add-api/create-static-app-on-azure-portal-1.png" alt-text="Statikus alkalmazás létrehozása a Azure Portal-képernyőn 1":::

1. Lépjen az [Azure Portalra](https://portal.azure.com)
1. Kattintson **az erőforrás létrehozása** elemre.
1. **Statikus Web Apps** keresése
1. Kattintson a **statikus Web Apps (előzetes verzió)** elemre.
1. Kattintson a **Létrehozás** gombra
1. Azure- _előfizetés_ kiválasztása
1. Válasszon ki vagy hozzon létre egy új _erőforráscsoportot_
1. Nevezze el az alkalmazást a **My-Vanilla-API**néven.
1. Válassza ki az Önhöz legközelebb eső _régiót_
1. Válassza ki az **ingyenes** _SKU_ -t
1. Kattintson a **Bejelentkezés a GitHub** használatával gombra, és hitelesítés a GitHub használatával
1. Válassza ki az előnyben részesített _szervezetet_
1. Válassza a **My-Vanilla-API** elemet az _adattár_ legördülő menüből
1. Válassza ki a **főkiszolgálót** az _ág_ legördülő menüből
1. Kattintson a **következőre: build >** gombra a Build konfigurációjának szerkesztéséhez

:::image type="content" source="media/add-api/create-static-app-on-azure-portal-2.png" alt-text="Statikus alkalmazás létrehozása a Azure Portal – 2. képernyő":::

Ezután adja hozzá a következőt a Build adataihoz.

1. Adja meg a **./** értéket az _alkalmazás helyének_.

1. Adja meg az **API** -t az _API helye_ mezőben.

   Ez az előző lépésben létrehozott API-mappa neve.
   
1. Törölje az alapértelmezett értéket az alkalmazás-összetevő _helyéről_, hagyja üresen a mezőt.

1. Kattintson az **Áttekintés + létrehozás** elemre.

| Beállítás | Leírás             | Kötelező |
| -------- | ----------------------- |
|  Alkalmazás helye | A statikus alkalmazás forráskódjának helye | Igen |
|  API-hely | Az API-háttér helye. Ez a Azure Functions alkalmazás-projekt gyökérkönyvtárára mutat | Nem |
|  Alkalmazás-összetevő helye | A Build kimeneti mappájának helye. Néhány előtér-JavaScript-keretrendszer rendelkezik egy olyan létrehozási lépéssel, amely egy mappában helyezi el a termelési fájlokat. Ez a beállítás a Build kimeneti mappájára mutat. | Nem |

:::image type="content" source="media/add-api/create-static-app-on-azure-portal-3.png" alt-text="Statikus alkalmazás létrehozása a Azure Portal-képernyőn 3":::

1. Kattintson a **Létrehozás** gombra
1. Várjon, amíg a telepítés befejeződik (ez egy percet is igénybe vehet)
1. Navigáljon a`https://github.com/<YOUR_GITHUB_ACCOUNT>/my-vanilla-api/actions?query=workflow%3A"Azure+Pages+CI%2FCD"`
1. Győződjön meg arról, hogy a Build sikeres

:::image type="content" source="media/add-api/github-workflow-0.png" alt-text="GitHub-munkafolyamat":::

Az üzembe helyezett API a következő címen érhető el: `https://<STATIC_APP_NAME>.azurestaticapps.net/api/<FUNCTION_OR_ROUTE_NAME>` .

:::image type="content" source="media/add-api/github-workflow-1.png" alt-text="GitHub-munkafolyamat":::

Az alkalmazás URL-címét a Azure Portal is megtalálhatja:

:::image type="content" source="media/add-api/static-app-url-from-portal.png" alt-text="A statikus alkalmazás URL-címének elérése a Azure Portal":::

Azt is megteheti, hogy közvetlenül hozzáfér az Azure-beli statikus webalkalmazáshoz `https://<STATIC_APP_NAME>.azurestaticapps.net` , és megtekinti az eredményt a böngészőben.

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha nem szeretné tovább használni ezt az alkalmazást, a következő lépésekkel törölheti az Azure statikus webalkalmazást és a kapcsolódó erőforrásokat.

1. Lépjen az [Azure Portalra](https://portal.azure.com)
1. A felső keresési sávban írja be az **erőforráscsoportok** kifejezést.
1. Kattintson az **erőforráscsoportok** elemre. 
1. **MyResourceGroup** kiválasztása
1. A _myResourceGroup_ lapon győződjön meg arról, hogy a felsorolt erőforrások közül azokat törölni kívánja.
1. **Törlés** kiválasztása
1. Írja be a **myResourceGroup** szöveget a szövegmezőbe
1. Válassza a **Törlés** elemet.


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alkalmazásbeállítások konfigurálása](./application-settings.md)
