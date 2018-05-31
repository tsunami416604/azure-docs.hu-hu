---
title: Kubernetes fejlesztői környezet létrehozása a felhőben | Microsoft Docs
titleSuffix: Azure Dev Spaces
author: ghogen
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: quickstart
description: Gyors Kubernetes-fejlesztés tárolókkal és mikroszolgáltatásokkal az Azure-ban
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, tárolók
manager: douge
ms.openlocfilehash: 9c9a485a5c59342149027798e118b97b7305c640
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361532"
---
# <a name="quickstart-create-a-kubernetes-development-environment-with-azure-dev-spaces-nodejs"></a>Rövid útmutató: Kubernetes fejlesztői környezet létrehozása az Azure Dev Spaces segítségével (Node.js)


[!INCLUDE[](includes/learning-objectives.md)]

[!INCLUDE[](includes/see-troubleshooting.md)]

Készen áll létrehozni egy Kubernetes-alapú fejlesztői környezetet az Azure-ban.

[!INCLUDE[](includes/portal-aks-cluster.md)]

## <a name="install-the-azure-cli"></a>Telepítse az Azure CLI-t
Az Azure Dev Spaces használatához minimális helyi gépbeállítás szükséges. A fejlesztési környezet konfigurációjának legnagyobb része a felhőben van tárolva, és megosztható más felhasználókkal. Első lépésként töltse le és futtassa az [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) parancssori felületet. 

> [!IMPORTANT]
> Ha az Azure CLI már telepítve van, győződjön meg arról, hogy a 2.0.32-es vagy újabb verziót használja.

[!INCLUDE[](includes/sign-into-azure.md)]

[!INCLUDE[](includes/use-dev-spaces.md)]

[!INCLUDE[](includes/install-vscode-extension.md)]

Miközben a környezet létrehozására vár, elkezdheti írni a kódot.

## <a name="create-a-nodejs-container-in-kubernetes"></a>Node.js-tároló létrehozása a Kubernetesben

Ebben a szakaszban egy Node.js-alapú webalkalmazást hozunk létre, és futtatjuk egy Kubernetes-tárolóban.

### <a name="create-a-nodejs-web-app"></a>Node.js-alapú webes alkalmazás létrehozása
A kód a GitHubról való letöltéséhez lépjen a https://github.com/Azure/dev-spaces helyre, és válassza a **Klónozás vagy letöltés** lehetőséget a GitHub-adattár a helyi környezetbe való letöltéséhez. A jelen útmutatóban használt kód a következő helyen található: `samples/nodejs/getting-started/webfrontend`.

[!INCLUDE[](includes/azds-prep.md)]

[!INCLUDE[](includes/build-run-k8s-cli.md)]

### <a name="update-a-content-file"></a>Tartalomfájlok frissítése
Az Azure Dev Spaces nem csupán a Kubernetesben futó kódok lekérésére szolgál – a segítségével gyorsan és iteratívan lehet megtekinteni a kódmódosítások életbe lépését a felhőben lévő Kubernetes-környezetben.

1. Keresse meg a `./public/index.html` fájlt, és szerkessze a HTML-kódot. Például módosítsa az oldal háttérszínét a kék egy árnyalatára:

    ```html
    <body style="background-color: #95B9C7; margin-left:10px; margin-right:10px;">
    ```

2. Mentse a fájlt. Néhány pillanat múlva egy, a terminálablakban megjelenő üzenet tájékoztatja, hogy a futó tárolóban lévő egyik fájl frissült.
1. Lépjen a böngészőre, és frissítse az oldalt. Látnia kell, ahogy a szín megváltozik.

Mi történt? A tartalomfájlok (például HTML és CSS) módosításai nem igénylik a Node.js folyamat újraindítását, így az aktív `azds up` parancs automatikusan és közvetlenül szinkronizálja a módosított tartalomfájlokat az Azure-ban futó tárolóba, így gyorsan megtekintheti a tartalmak módosításait.

### <a name="test-from-a-mobile-device"></a>Tesztelés mobileszközről
Ha a webalkalmazást egy mobileszközön nyitja meg, megfigyelheti, hogy a felhasználói felület nem megfelelően jelenik meg a kisméretű eszközökön.

Ennek kiküszöbölésére hozzáadunk egy `viewport` metacímkét:
1. Nyissa meg a `./public/index.html` fájlt.
1. Adja hozzá a `viewport` metacímkét a meglévő `head` elemhez:

    ```html
    <head>
        <!-- Add this line -->
        <meta name="viewport" content="width=device-width, initial-scale=1">
    </head>
    ```

1. Mentse a fájlt.
1. Frissítse a böngészőt az eszközön. Most a webalkalmazásnak megfelelően renderelve kell megjelennie. 

Ez a példa azt mutatja be, hogy bizonyos problémák csak akkor figyelhetők meg, ha olyan eszközökön teszteli az alkalmazást, amelyekre azt szánták. A VS Azure Dev Spaces segítségével a kód gyorsan iterálható, és a módosítások is gyorsan ellenőrizhetők a céleszközökön.

### <a name="update-a-code-file"></a>Kódfájlok frissítése
A kiszolgálóoldali kódfájlok frissítése egy kicsit több munkát igényel, mert újra kell indítani egy Node.js-alkalmazást.

1. A terminálablakban nyomja le a `Ctrl+C` billentyűkombinációt (az `azds up` leállításához).
1. Nyissa meg a `server.js` nevű kódfájlt, és szerkessze a szolgáltatás üdvözlőüzenetét: 

    ```javascript
    res.send('Hello from webfrontend running in Azure!');
    ```

3. Mentse a fájlt.
1. Futtassa az `azds up` parancsot a terminálablakban. 

Ez újraépíti a tárolórendszerképet, és újra üzembe helyezi a Helm-diagramot. Töltse be újra az oldalt a böngészőben a módosított kód életbe léptetéséhez.

Azonban a kódfejlesztésnek van egy még ennél is *gyorsabb módszere*, amelyet a következő szakaszban mutatunk be. 

## <a name="debug-a-container-in-kubernetes"></a>Tároló hibakeresése a Kubernetesben

[!INCLUDE[](includes/debug-intro.md)]

[!INCLUDE[](includes/init-debug-assets-vscode.md)]

### <a name="select-the-azds-debug-configuration"></a>Az AZDS hibakeresési konfiguráció kiválasztása
1. A hibakeresési nézet megnyitásához kattintson a **tevékenységsáv** Hibakeresés ikonjára a VS Code oldalán.
1. Válassza a **Program indítása (AZDS)** lehetőséget mint aktív hibakeresési konfigurációt.

![](media/get-started-node/debug-configuration-nodejs.png)

> [!Note]
> Ha nem lát Azure Dev Spaces-parancsokat a parancspalettán, győződjön meg róla, hogy a VS Code Azure Dev Spaces-hez készült bővítménye telepítve van.

### <a name="debug-the-container-in-kubernetes"></a>A tároló hibakeresése a Kubernetesben
A kód a Kubernetesben való hibakereséséhez nyomja le az **F5** billentyűt.

Az `up` parancshoz hasonlóan a hibakeresés indításakor a kód szinkronizálva lesz a fejlesztési környezetbe, továbbá létrejön és települ egy tároló a Kubernetesben. Ezúttal a hibakereső a távoli tárolóhoz van csatlakoztatva.

[!INCLUDE[](includes/tip-vscode-status-bar-url.md)]

Állítson be egy töréspontot egy kiszolgálóoldali kódfájlban, például az `app.get('/api'...` helyen a `server.js` fájlban. Frissítse az oldalt a böngészőben vagy kattintson a „Kimondás újra” gombra. Ezzel elvileg elérheti a töréspontot, és írhatja a kódot.

Ugyanúgy teljes körű hozzáférése van a hibakeresési információkhoz, mintha helyileg futna a kód (pl. hívási verem, helyi változók, kivételek adatai stb.).

### <a name="edit-code-and-refresh-the-debug-session"></a>A kód szerkesztése és a hibakeresési munkamenet frissítése
Miközben a hibakereső aktív, szerkessze a kódot, például módosítsa megint az üdvözlőüzenetet:

```javascript
app.get('/api', function (req, res) {
    res.send('**** Hello from webfrontend running in Azure! ****');
});
```

Mentse a fájlt, és a **Hibakeresési műveletek panelen** kattintson a **Frissítés** gombra. 

![](media/get-started-node/debug-action-refresh-nodejs.png)

Ahelyett, hogy a kód minden szerkesztése alkalmával újra létrehozna és üzembe helyezne egy új tárolórendszerképet, ami általában sok időt vesz igénybe, az Azure Dev Spaces a hibakeresési munkamenetek közt újraindítja a Node.js-folyamatot, hogy gyorsabb szerkesztési/hibakeresési ciklust biztosítson.

Frissítse a webalkalmazást a böngészőben, vagy kattintson a *Kimondás újra* gombra. Az egyedi üzenetnek meg kell jelennie a felhasználói felületen.

### <a name="use-nodemon-to-develop-even-faster"></a>Még gyorsabb fejlesztés a NodeMon használatával
A *Nodemon* egy népszerű eszköz, amelyet a Node.js–fejlesztők használnak a fejlesztés felgyorsítására. Ahelyett, hogy a kiszolgálóoldali kód szerkesztésekor minden alkalommal manuálisan újra kellene indítani a Node-folyamatot, a fejlesztők gyakran úgy konfigurálják a Node-projektet, hogy a *nodemon* monitorozza a fájlok változását, és automatikusan újraindítsa a kiszolgálói folyamatot. Ha ez a módszer működik, a fejlesztő a kódszerkesztés végrehajtása után egyszerűen frissíti a böngészőjét.

Az Azure Dev Spaces használatával számos olyan fejlesztési munkafolyamatot alkalmazhat, amelyeket a helyi fejlesztés során is használ. Ennek bemutatására a `webfrontend` mintaprojektet beállítottuk a *nodemon* használatára (fejlesztői függőségként van konfigurálva a `package.json` fájlban).

Próbálja meg a következőket:
1. Állítsa le a VS Code hibakeresőt.
1. Kattintson a **tevékenységsáv** Hibakeresés ikonjára a VS Code oldalán. 
1. Válassza a **Csatolás (AZDS)** lehetőséget mint aktív hibakeresési konfigurációt.
1. Nyomja le az F5 billentyűt.

Ebben a konfigurációban a tároló úgy van beállítva, hogy elindítsa a *nodemon* eszközt. A kiszolgálói kód módosításakor a *nodemon* automatikusan újraindítja a Node-folyamatot, ahogy azt helyi fejlesztés esetén is tenné. 
1. Módosítsa újra az üdvözlőüzenetet a `server.js` fájlban, majd mentse azt.
1. Frissítse a böngészőt vagy kattintson a *Kimondás újra* gombra, és láthatja, ahogy a módosítások érvénybe lépnek.

**Most tehát rendelkezésére áll egy módszer, amellyel gyorsan iterálhatja a kódot, és közvetlenül a Kubernetesben végezheti a hibakeresést!**

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Több tároló használata és fejlesztés csapatban](get-started-nodejs.md#call-a-service-running-in-a-separate-container)