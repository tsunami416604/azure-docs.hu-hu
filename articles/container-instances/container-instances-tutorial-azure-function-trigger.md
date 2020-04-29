---
title: Oktatóanyag – a tárolók csoportjának elindítása az Azure Function alapján
description: HTTP-alapú, kiszolgáló nélküli PowerShell-függvény létrehozása az Azure Container instances létrehozásának automatizálásához
ms.topic: tutorial
ms.date: 09/20/2019
ms.custom: ''
ms.openlocfilehash: 9dbb22a2449e4c41bff802ab827da4489fc7ffeb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78331025"
---
# <a name="tutorial-use-an-http-triggered-azure-function-to-create-a-container-group"></a>Oktatóanyag: egy HTTP-triggert használó Azure-függvény használata tároló csoport létrehozásához

[Azure functions](../azure-functions/functions-overview.md) egy kiszolgáló nélküli számítási szolgáltatás, amely parancsfájlokat vagy kódokat futtathat különböző eseményekre, például egy http-kérelemre, egy időzítőre vagy egy Azure Storage-várólistában lévő üzenetre válaszul.

Ebben az oktatóanyagban egy Azure-függvényt hoz létre, amely HTTP-kérést végez, és elindítja egy [tároló csoport](container-instances-container-groups.md)üzembe helyezését. Ez a példa a Azure Functions használatának alapjait mutatja be Azure Container Instances erőforrásainak automatikus létrehozásához. Módosítsa vagy bővítse a példát összetettebb forgatókönyvek vagy más eseményindítók esetén. 

Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * A Visual Studio Code és a [Azure functions bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) használatával hozzon létre egy alapszintű http-triggeres PowerShell-függvényt.
> * Engedélyezzen egy identitást a Function alkalmazásban, és adja meg az IT-engedélyeket Azure-erőforrások létrehozásához.
> * Módosítsa és tegye közzé újra a PowerShell-függvényt egy egytárolós tároló-csoport üzembe helyezésének automatizálásához.
> * Ellenőrizze a tároló HTTP-triggeres telepítését.

> [!IMPORTANT]
> A Azure Functions PowerShell-je jelenleg előzetes verzióban érhető el. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="prerequisites"></a>Előfeltételek

Lásd: [az első függvény létrehozása az Azure-ban](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-powershell#configure-your-environment) a Visual Studio Code telepítéséhez és használatához az operációs rendszer Azure Functionsjának előfeltételei.

A cikkben ismertetett lépések az Azure CLI-t használják. A lépések végrehajtásához használhatja az Azure CLI Azure Cloud Shell vagy helyi telepítését. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="create-a-basic-powershell-function"></a>Alapszintű PowerShell-függvény létrehozása

Kövesse az [első PowerShell-függvény létrehozása az Azure-ban](../azure-functions/functions-create-first-function-powershell.md) című témakör lépéseit, és hozzon létre egy PowerShell-függvényt a http-trigger sablon használatával. Használja az alapértelmezett Azure-függvény nevét **HttpTrigger**. Ahogy a gyors útmutatóban is látható, a függvényt helyileg tesztelheti, és közzéteheti a projektet egy Azure-beli Function alkalmazásban. Ez a példa egy alapszintű HTTP-triggert használó függvény, amely szöveges karakterláncot ad vissza. A cikk későbbi lépéseiben módosítja a függvényt egy tároló csoport létrehozásához.

Ez a cikk azt feltételezi, hogy közzéteszi a projektet a *myfunctionapp*név használatával, amely az Azure-erőforráscsoportban automatikusan elnevezett, a Function app neve ( *myfunctionapp*) alapján. Helyettesítse be az egyedi Function-alkalmazás nevét és az erőforráscsoport nevét a későbbi lépésekben.

## <a name="enable-an-azure-managed-identity-in-the-function-app"></a>Azure által felügyelt identitás engedélyezése a Function alkalmazásban

Mostantól engedélyezheti a rendszerhez rendelt [felügyelt identitást](../app-service/overview-managed-identity.md?toc=/azure/azure-functions/toc.json#add-a-system-assigned-identity) a Function alkalmazásban. Az alkalmazást futtató PowerShell-állomás automatikusan képes hitelesíteni ezt az identitást, és lehetővé teszi a függvények számára, hogy olyan Azure-szolgáltatásokon hajtson végre műveleteket, amelyekhez az identitás hozzáférést kapott. Ebben az oktatóanyagban megadhatja a felügyelt identitás engedélyeit ahhoz, hogy erőforrásokat hozzon létre a Function alkalmazás erőforráscsoporthoz. 

Először használja az az [Group show][az-group-show] parancsot a Function app erőforráscsoport azonosítójának lekéréséhez és egy környezeti változóban való tárolásához. Ez a példa feltételezi, hogy egy bash-rendszerhéjban futtatja a parancsot.

```azurecli
rgID=$(az group show --name myfunctionapp --query id --output tsv)
```

Futtassa az [az functionapp Identity app assign][az-functionapp-identity-app-assign] parancsot, és rendeljen hozzá egy helyi identitást a Function alkalmazáshoz, és rendeljen hozzá közreműködői szerepkört az erőforráscsoporthoz. Ez a szerepkör lehetővé teszi, hogy az identitás további erőforrásokat, például tároló csoportokat hozzon létre az erőforráscsoporthoz.

```azurecli
az functionapp identity assign \
  --name myfunctionapp \
  --resource-group myfunctionapp \
  --role contributor --scope $rgID
```

## <a name="modify-httptrigger-function"></a>HttpTrigger-függvény módosítása

Módosítsa a **HttpTrigger** függvény PowerShell-kódját egy tároló csoport létrehozásához. A függvény `run.ps1` fájljában keresse meg a következő kódrészletet. Ez a kód egy Name (név) értéket jelenít meg, ha a függvény URL-címében egy lekérdezési karakterláncként adja át a következőt:

```powershell
[...]
if ($name) {
    $status = [HttpStatusCode]::OK
    $body = "Hello $name"
}
[...]
```

Cserélje le ezt a kódot a következő példa blokkra. Itt, ha a lekérdezési karakterláncban egy név értéket ad át, a rendszer a [New-AzContainerGroup][new-azcontainergroup] parancsmaggal nevezi el és hozza létre a tároló csoportot. Győződjön meg arról, hogy az erőforráscsoport neve *myfunctionapp* az erőforráscsoport nevére cseréli a Function alkalmazáshoz:

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

Ez a példa egy tároló csoportot hoz létre, amely a `alpine` rendszerképet futtató egyetlen tároló példányból áll. A tároló egyetlen `echo` parancsot futtat, majd leáll. Valós példaként egy batch-feladatok futtatásához egy vagy több tároló-csoport létrehozását is elindíthatja.
 
## <a name="test-function-app-locally"></a>A Function alkalmazás helyi tesztelése

Győződjön meg arról, hogy a függvény helyileg fut, mielőtt újból közzéteszi a Function app-projektet az Azure-ban. Ahogy a [PowerShell](../azure-functions/functions-create-first-function-powershell.md)-gyors útmutatóban is látható, helyezzen be egy helyi töréspontot a `Wait-Debugger` PowerShell-parancsfájlba, és egy fölötte lévő hívást. Hibakeresési útmutatásért lásd: a [PowerShell Azure functions helyi hibakeresése](../azure-functions/functions-debug-powershell-local.md).


## <a name="republish-azure-function-app"></a>Azure Function-alkalmazás újbóli közzététele

Miután meggyőződött róla, hogy a függvény megfelelően fut a helyi számítógépen, ideje újra közzétenni a projektet a meglévő Function alkalmazásban az Azure-ban.

> [!NOTE]
> Ne felejtse el, hogy `Wait-Debugger` a függvények az Azure-ba való közzététele előtt el kell távolítania a hívásokat.

1. A Visual Studio Code-ban nyissa meg a parancs palettáját. Keresse meg és válassza `Azure Functions: Deploy to function app...`ki a következőt:.
1. Válassza ki az aktuális munkamappát a zip-ben és az üzembe helyezéshez.
1. Válassza ki az előfizetést, majd a meglévő Function app (*myfunctionapp*) nevét. Erősítse meg, hogy felül szeretné írni az előző telepítést.

A függvényalkalmazás létrehozása és a telepítőcsomag alkalmazása után megjelenik egy értesítés. Válassza ki az értesítés **kimenetének megtekintése** lehetőséget a létrehozási és a telepítési eredmények megtekintéséhez, beleértve a frissített Azure-erőforrásokat is.

## <a name="run-the-function-in-azure"></a>A függvény futtatása az Azure-ban

Miután az üzembe helyezés sikeresen befejeződött, szerezze be a függvény URL-címét. Például az **Azure: functions** területen a Visual Studio Code-ban másolja a **HttpTrigger** függvény URL-címét, vagy kérje le a függvény url-címét a [Azure Portal](../azure-functions/functions-create-first-azure-function.md#test-the-function).

A függvény URL-címe tartalmaz egy egyedi kódot, amely a következőkből áll:

```
https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==
```

### <a name="run-function-without-passing-a-name"></a>Függvény futtatása a név átadása nélkül

Első tesztként futtassa a `curl` parancsot, és adja át a függvény URL-címét a `name` lekérdezési karakterlánc hozzáfűzése nélkül. Ügyeljen arra, hogy a függvény egyedi kódját tartalmazza.

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M=="
```

A függvény a 400 állapotkódot és a szöveget `Please pass a name on the query string or in the request body`adja vissza:

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

### <a name="run-function-and-pass-the-name-of-a-container-group"></a>Futtassa a függvényt, és adja át egy tároló csoport nevét

Most futtassa a `curl` parancsot a Container Group (*mycontainergroup*) nevének lekérdezési karakterláncként `&name=mycontainergroup`való hozzáfűzésével:

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==&name=mycontainergroup"
```

A függvény a 200 állapotkódot adja vissza, és elindítja a tároló csoport létrehozását:

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

Győződjön meg arról, hogy a tároló az az [Container logs][az-container-logs] paranccsal futott:

```azurecli
az container logs --resource-group myfunctionapp --name mycontainergroup
```

Példa a kimenetre:

```console
Hello from an Azure container instance triggered by an Azure function
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az oktatóanyagban létrehozott erőforrásokra, az az [Group delete][az-group-delete] paranccsal eltávolíthatja az erőforráscsoportot és a benne található összes erőforrást. Ez a parancs törli a létrehozott tárolóregisztrációs adatbázist, valamint a futó tárolót és annak minden kapcsolódó erőforrását is.

```azurecli-interactive
az group delete --name myfunctionapp
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy Azure-függvényt, amely HTTP-kérést végez, és elindítja egy tároló csoport üzembe helyezését. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A Visual Studio Code és a Azure Functions bővítmény használatával hozzon létre egy alapszintű HTTP-triggeres PowerShell-függvényt.
> * Engedélyezzen egy identitást a Function alkalmazásban, és adja meg az IT-engedélyeket Azure-erőforrások létrehozásához.
> * Módosítsa a PowerShell-függvény kódját az egytárolós tároló-csoportok üzembe helyezésének automatizálásához.
> * Ellenőrizze a tároló HTTP-triggeres telepítését.

A tárolók elindítására és figyelésére vonatkozó részletes példákért tekintse meg az [eseményvezérelt kiszolgáló nélküli tárolók PowerShell-Azure functions és Azure Container instances és a hozzá](https://dev.to/azure/event-driven-serverless-containers-with-powershell-azure-functions-and-azure-container-instances-e9b) [tartozó kódrészletet](https://github.com/anthonychu/functions-powershell-run-aci)ismertető blogbejegyzést.

Az Azure functions létrehozásával és a functions-projektek közzétételével kapcsolatos részletes útmutatásért tekintse meg az [Azure functions dokumentációját](/azure/azure-functions/) . 

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
