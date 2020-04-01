---
title: Oktatóanyag – Eseményindító tárolócsoport az Azure függvény szerint
description: HTTP-triggeres, kiszolgáló nélküli PowerShell-függvény létrehozása az Azure-tárolópéldányok létrehozásának automatizálásához
ms.topic: tutorial
ms.date: 09/20/2019
ms.custom: ''
ms.openlocfilehash: 9dbb22a2449e4c41bff802ab827da4489fc7ffeb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78331025"
---
# <a name="tutorial-use-an-http-triggered-azure-function-to-create-a-container-group"></a>Oktatóanyag: Tárolócsoport létrehozásához használjon HTTP-alapú Azure-függvényt

[Az Azure Functions](../azure-functions/functions-overview.md) egy kiszolgáló nélküli számítási szolgáltatás, amely parancsfájlokat vagy kódot futtathat különböző eseményekre adott válaszként, például EGY HTTP-kérelemre, egy időzítőre vagy egy Azure Storage-várólistában lévő üzenetre.

Ebben az oktatóanyagban létrehoz egy Azure-függvényt, amely http-kérelmet vesz fel, és elindítja egy [tárolócsoport](container-instances-container-groups.md)üzembe helyezését. Ez a példa az Azure Functions használatával automatikusan hozzon létre erőforrásokat az Azure Container Instances. Módosítsa vagy bővítse a példát összetettebb forgatókönyvek vagy más eseményindítók esetén. 

Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * A Visual Studio-kód az [Azure Functions bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) egy egyszerű HTTP-aktivált PowerShell-függvény létrehozásához.
> * Engedélyezze az identitást a függvényalkalmazásban, és adjon neki engedélyeket az Azure-erőforrások létrehozásához.
> * Módosítsa és tegye közzé újra a PowerShell-függvényt az egytárolós tárolócsoport üzembe helyezésének automatizálásához.
> * Ellenőrizze a tároló HTTP által aktivált központi telepítését.

> [!IMPORTANT]
> Az Azure Functionshez való PowerShell jelenleg előzetes verzióban érhető el. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="prerequisites"></a>Előfeltételek

Lásd: [Az első függvény létrehozása az Azure-ban](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-powershell#configure-your-environment) előfeltételek a Visual Studio-kód telepítéséhez és használatához az Azure Functions operációs rendszerén.

Ebben a cikkben néhány lépés az Azure CLI használatával. Használhatja az Azure Cloud Shell vagy az Azure CLI helyi telepítését a lépések végrehajtásához. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="create-a-basic-powershell-function"></a>Alapvető PowerShell-függvény létrehozása

Kövesse [az első PowerShell-függvény létrehozása az Azure-ban](../azure-functions/functions-create-first-function-powershell.md) lépéseket, hogy hozzon létre egy PowerShell-függvényt a HTTP Trigger sablon használatával. Használja az alapértelmezett Azure-függvénynevét **HttpTrigger**. Ahogy a rövid útmutatóban látható, tesztelje a függvényt helyileg, és tegye közzé a projektet egy függvényalkalmazásban az Azure-ban. Ez a példa egy alapvető HTTP-aktivált függvény, amely szöveges karakterláncot ad vissza. A cikk későbbi lépéseiben módosíthatja a függvényt egy tárolócsoport létrehozásához.

Ez a cikk feltételezi, hogy a projektet a *myfunctionapp*név vel teszi közzé egy olyan Azure-erőforráscsoportban, amely automatikusan elvan nevezve a függvényalkalmazás neve (szintén *myfunctionapp)* szerint. Helyettesítse az egyedi függvényalkalmazás nevét és az erőforráscsoport nevét a későbbi lépésekben.

## <a name="enable-an-azure-managed-identity-in-the-function-app"></a>Azure által felügyelt identitás engedélyezése a függvényalkalmazásban

Most engedélyezze a rendszer által hozzárendelt [felügyelt identitást](../app-service/overview-managed-identity.md?toc=/azure/azure-functions/toc.json#add-a-system-assigned-identity) a függvényalkalmazásban. Az alkalmazást futtató PowerShell-állomás automatikusan hitelesítheti magát ezzel az identitással, lehetővé téve a függvények számára, hogy műveleteket hajtanak végre az Azure-szolgáltatásokon, amelyekhez az identitás hozzáférést kapott. Ebben az oktatóanyagban megadja a felügyelt identitás engedélyeket a függvényalkalmazás erőforráscsoportjában lévő erőforrások létrehozásához. 

Először használja az [az csoport show][az-group-show] parancsot a függvényalkalmazás erőforráscsoportjának azonosítójának lekérése és környezeti változóban való tárolása. Ebben a példában feltételezi, hogy a parancsot egy Bash shell futtatja.

```azurecli
rgID=$(az group show --name myfunctionapp --query id --output tsv)
```

Futtassa [az functionapp identitásalkalmazás hozzárendelése][az-functionapp-identity-app-assign] a helyi identitást a függvényalkalmazáshoz, és rendeljen hozzá egy közreműködői szerepkört az erőforráscsoporthoz. Ez a szerepkör lehetővé teszi, hogy az identitás további erőforrásokat, például tárolócsoportokat hozzon létre az erőforráscsoportban.

```azurecli
az functionapp identity assign \
  --name myfunctionapp \
  --resource-group myfunctionapp \
  --role contributor --scope $rgID
```

## <a name="modify-httptrigger-function"></a>HttpTrigger függvény módosítása

Módosítsa a **HttpTrigger** függvény PowerShell-kódját egy tárolócsoport létrehozásához. A `run.ps1` függvény fájljában keresse meg a következő kódblokkot. Ez a kód egy névértéket jelenít meg, ha az URL-cím ben lekérdezési karakterláncként kerül átadásra:

```powershell
[...]
if ($name) {
    $status = [HttpStatusCode]::OK
    $body = "Hello $name"
}
[...]
```

Cserélje le ezt a kódot a következő példablokkra. Itt, ha egy név értéket ad át a lekérdezési karakterlánc, akkor a [new-azcontainergroup][new-azcontainergroup] parancsmag használatával elnevezheti és létrehozhat egy tárolócsoportot. Győződjön meg arról, hogy lecseréli a *myfunctionapp* erőforráscsoport nevét a függvényalkalmazás erőforráscsoportjának nevére:

```powershell
[...]
if ($name) {
    $status = [HttpStatusCode]::OK
    New-AzContainerGroup -ResourceGroupName myfunctionapp -Name $name `
        -Image alpine -OsType Linux `
        -Command "echo 'Hello from an Azure container instance triggered by an Azure function'" `
        -RestartPolicy Never
    $body = "Started container group $name"
}
[...]
```

Ebben a példában létrehoz egy tárolócsoportot, `alpine` amely egyetlen tárolópéldányból áll, amely a lemezképet futtatja. A tároló egyetlen `echo` parancsot futtat, majd leáll. Egy valós példában egy vagy több tárolócsoport létrehozását aktiválhatja egy kötegelt feladat futtatásához.
 
## <a name="test-function-app-locally"></a>Tesztfüggvény alkalmazás helyileg

Győződjön meg arról, hogy a függvény megfelelően fut helyileg, mielőtt újra közzétenné a függvényalkalmazás-projektet az Azure-ban. Ahogy a [PowerShell-gyorsindítás,](../azure-functions/functions-create-first-function-powershell.md)helyezzen be egy helyi `Wait-Debugger` töréspontot a PowerShell-parancsfájlba, és egy hívás felette. A hibakeresési útmutatásért olvassa el a [PowerShell Azure Functions helyi hibakeresését.](../azure-functions/functions-debug-powershell-local.md)


## <a name="republish-azure-function-app"></a>Az Azure függvényalkalmazás újbóli közzététele

Miután meggyőződött arról, hogy a függvény megfelelően fut a helyi számítógépen, itt az ideje, hogy újra közzétegye a projektet a meglévő függvényalkalmazásban az Azure-ban.

> [!NOTE]
> Ne felejtse el `Wait-Debugger` eltávolítani a hívásokat, mielőtt közzészeretné tenni a függvényeket az Azure-ban.

1. A Visual Studio-kódban nyissa meg a parancspalettát. Keresse meg `Azure Functions: Deploy to function app...`és válassza a lehetőséget.
1. Jelölje ki az aktuálisan tömöríteni és telepíteni kívánt munkamappát.
1. Válassza ki az előfizetést, majd a meglévő függvényalkalmazás nevét (*myfunctionapp*). Ellenőrizze, hogy felül kívánja-e írni az előző központi telepítést.

A függvényalkalmazás létrehozása és a telepítőcsomag alkalmazása után megjelenik egy értesítés. Válassza a **Kimenet megtekintése** ebben az értesítésben a létrehozási és üzembe helyezési eredmények megtekintéséhez, beleértve a frissített Azure-erőforrásokat is.

## <a name="run-the-function-in-azure"></a>A funkció futtatása az Azure-ban

A központi telepítés sikeres befejezése után a függvény URL-címét. Például használja az **Azure: Functions** terület Visual Studio-kód másolja a **HttpTrigger** függvény URL-címét, vagy a függvény URL-címét az [Azure Portalon.](../azure-functions/functions-create-first-azure-function.md#test-the-function)

A függvény URL-címe egyedi kódot tartalmaz, és a következő formában érhető el:

```
https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==
```

### <a name="run-function-without-passing-a-name"></a>Függvény futtatása név átadása nélkül

Első tesztként futtassa a `curl` parancsot, és `name` adja át a függvény URL-címét anélkül, hogy lekérdezési karakterláncot fűzne hozzá. Győződjön meg róla, hogy tartalmazza a funkció egyedi kódját.

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M=="
```

A függvény a 400-as `Please pass a name on the query string or in the request body`állapotkódot és a következő szöveget adja vissza:

```
[...]
> GET /api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M== HTTP/2
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.54.0
> Accept: */*
> 
* Connection state changed (MAX_CONCURRENT_STREAMS updated)!
< HTTP/2 400 
< content-length: 62
< content-type: text/plain; charset=utf-8
< date: Mon, 05 Aug 2019 22:08:15 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
Please pass a name on the query string or in the request body.
```

### <a name="run-function-and-pass-the-name-of-a-container-group"></a>Futtassa a függvényt, és adja át egy tárolócsoport nevét

Most futtassa a `curl` parancsot egy tárolócsoport *(mycontainergroup)* `&name=mycontainergroup`nevének lekérdezési karakterláncként való hozzáfűzésével:

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==&name=mycontainergroup"
```

A függvény 200-as állapotkódot ad vissza, és elindítja a tárolócsoport létrehozását:

```
[...]
> GET /api/HttpTrigger?ode=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==&name=mycontainergroup HTTP/2
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.54.0
> Accept: */*
> 
* Connection state changed (MAX_CONCURRENT_STREAMS updated)!
< HTTP/2 200 
< content-length: 28
< content-type: text/plain; charset=utf-8
< date: Mon, 05 Aug 2019 22:15:30 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
Started container group mycontainergroup
```

Ellenőrizze, hogy a tároló az az tárolónaplók paranccsal [futott-e:][az-container-logs]

```azurecli
az container logs --resource-group myfunctionapp --name mycontainergroup
```

Példa a kimenetre:

```console
Hello from an Azure container instance triggered by an Azure function
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az ebben az oktatóanyagban létrehozott erőforrásokra, az [az csoport törlési][az-group-delete] parancsával eltávolíthatja az erőforráscsoportot és az összes benne lévő erőforrást. Ez a parancs törli a létrehozott tárolóregisztrációs adatbázist, valamint a futó tárolót és annak minden kapcsolódó erőforrását is.

```azurecli-interactive
az group delete --name myfunctionapp
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy Azure-függvényt, amely egy HTTP-kérelmet vesz fel, és elindítja egy tárolócsoport üzembe helyezését. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A Visual Studio-kód az Azure Functions bővítmény egy egyszerű HTTP-aktivált PowerShell-függvény létrehozásához.
> * Engedélyezze az identitást a függvényalkalmazásban, és adjon neki engedélyeket az Azure-erőforrások létrehozásához.
> * Módosítsa a PowerShell-függvénykódot egy egytárolós tárolócsoport üzembe helyezésének automatizálásához.
> * Ellenőrizze a tároló HTTP által aktivált központi telepítését.

Egy tárolóba helyezett feladat elindításához és figyeléséhez részletes példát a [PowerShell Azure-funkciókkal és az Azure Container-példányokkal rendelkező eseményvezérelt kiszolgálónélküli tárolók](https://dev.to/azure/event-driven-serverless-containers-with-powershell-azure-functions-and-azure-container-instances-e9b) és a kísérő [kódminta](https://github.com/anthonychu/functions-powershell-run-aci)című blogbejegyzésben talál.

Tekintse meg az [Azure Functions dokumentációját](/azure/azure-functions/) az Azure-függvények létrehozásához és egy függvényprojekt közzétételéhez. 

<!-- IMAGES -->


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->

[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-show]: /cli/azure/group#az-group-show
[az-group-delete]: /cli/azure/group#az-group-delete
[az-functionapp-identity-app-assign]: /cli/azure/functionapp/identity#az-functionapp-identity-assign
[new-azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[az-container-logs]: /cli/azure/container#az-container-logs
