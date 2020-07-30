---
title: Rövid útmutató – Docker-tároló üzembe helyezése tároló példánya – Docker CLI
description: Ebben a rövid útmutatóban a Docker CLI használatával gyorsan üzembe helyezhet egy elkülönített Azure Container-példányban futó tároló-webalkalmazást
ms.topic: quickstart
ms.date: 07/16/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: 0e4569904ef6aee304518ce012889d10dc2ecbce
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87408084"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-docker-cli"></a>Gyors útmutató: tároló-példány üzembe helyezése az Azure-ban a Docker CLI használatával

A Azure Container Instances használatával a kiszolgáló nélküli Docker-tárolókat az Azure-ban, az egyszerűség és a gyorsaság segítségével futtathatja. A Felhőbeli natív alkalmazások fejlesztésekor igény szerint üzembe helyezhető egy tároló-példányon, és zökkenőmentesen válthat a helyi fejlesztésből a felhőbe történő üzembe helyezésig.

Ebben a rövid útmutatóban natív Docker CLI-parancsokat használ a Docker-tárolók üzembe helyezéséhez és az alkalmazás elérhetővé tételéhez Azure Container Instances. Ezt a funkciót a [Docker és az Azure](https://docs.docker.com/engine/context/aci-integration/) (Beta) integrációja teszi lehetővé. Néhány másodperccel a parancs végrehajtása után `docker run` megkeresheti a tárolóban futó alkalmazást:

:::image type="content" source="media/container-instances-quickstart-docker-cli/view-application-running-in-an-azure-container-instance.png" alt-text="Az Azure Container Instances használatával üzembe helyezett alkalmazás képe a böngészőben":::

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot][azure-account], mielőtt hozzákezd.

Ebben a rövid útmutatóban telepítenie kell a Docker Desktop Edge verziójának 2.3.2.0 vagy újabb verzióját, amely Windows vagy [MacOS](https://desktop.docker.com/mac/edge/Docker.dmg) [rendszeren](https://desktop.docker.com/win/edge/Docker%20Desktop%20Installer.exe) érhető el. Vagy telepítse a [Docker ACI Integration CLI for Linux](https://docs.docker.com/engine/context/aci-integration/#install-the-docker-aci-integration-cli-on-linux) (Beta) rendszert. 

> [!IMPORTANT]
> Ez a funkció jelenleg előzetes verzióban érhető el, és a Beta (előzetes verzió) funkcióit igényli a Docker-ben. További információ [a Docker Desktop stabil és peremhálózati verzióiról](https://docs.docker.com/desktop/#stable-and-edge-versions). A Azure Container Instances egyik funkciója sem támogatott. Küldjön visszajelzést a Docker-Azure-integrációról azáltal, hogy problémát hoz létre az [ACI-Integration-Beta GitHub-](https://github.com/docker/aci-integration-beta) tárházban.

## <a name="create-azure-context"></a>Azure-környezet létrehozása

Ha a Azure Container Instances tárolók futtatásához Docker-parancsokat szeretne használni, először jelentkezzen be az Azure-ba:

```bash
docker login azure
```

Ha a rendszer kéri, adja meg vagy válassza ki az Azure-beli hitelesítő adatait.


Az `docker context create aci` ACI-környezet létrehozásához futtassa a parancsot. Ez a kontextus az Azure-előfizetéshez társítja a Docker-t, így tároló-példányokat hozhat létre. Például egy *myacicontext*nevű környezet létrehozásához:

```
docker context create aci myacicontext
```

Ha a rendszer kéri, válassza ki az Azure-előfizetés AZONOSÍTÓját, válasszon ki egy meglévő erőforráscsoportot, vagy **hozzon létre egy új erőforráscsoportot**. Ha új erőforráscsoportot választ, akkor a rendszer által létrehozott névvel jön létre. Az Azure Container Instancest – mint minden Azure-erőforrást – egy erőforráscsoportban kell üzembe helyezni. Az erőforráscsoportok lehetővé teszik az egymáshoz kapcsolódó Azure-erőforrások rendszerezését és kezelését.


A futtatásával `docker context ls` erősítse meg, hogy hozzáadta az ACI környezetet a Docker-környezetekhez:

```
docker context ls
```

## <a name="create-a-container"></a>Tároló létrehozása

A Docker-környezet létrehozása után létrehozhat egy tárolót az Azure-ban. Ebben a rövid útmutatóban a nyilvános `mcr.microsoft.com/azuredocs/aci-helloworld` rendszerképet használhatja. Ez a rendszerkép egy olyan kisméretű webalkalmazást csomagol, amely a statikus HTML-lapokat Node.js.

Először váltson át az ACI-környezetre. Az összes további Docker-parancs ebben a környezetben fut.

```
docker context use myacicontext
```

Futtassa az alábbi `docker run` parancsot az Azure Container instance létrehozásához az interneten elérhető 80-as porton:

```
docker run -p 80:80 mcr.microsoft.com/azuredocs/aci-helloworld
```

A sikeres üzembe helyezés mintájának kimenete:

```
[+] Running 2/2
 ⠿ hungry-kirch            Created                                                                               5.1s
 ⠿ single--container--aci  Done                                                                                 11.3s
hungry-kirch
```

A futtatásával `docker ps` részletes információkat kaphat a futó tárolóról, beleértve a nyilvános IP-címet is:

```
docker ps
```


A minta kimenet egy nyilvános IP-címet mutat be, ebben az esetben a *52.230.225.232*:

```
CONTAINER ID        IMAGE                                        COMMAND             STATUS              PORTS
hungry-kirch        mcr.microsoft.com/azuredocs/aci-helloworld                       Running             52.230.225.232:80->80/tcp
```

 Most nyissa meg az IP-címet a böngészőben. Ha egy, az alábbihoz hasonló weboldal jelenik meg, gratulálunk! Sikeresen üzembe helyezett egy Docker-tárolóban futó alkalmazást az Azure-ban.

:::image type="content" source="media/container-instances-quickstart-docker-cli/view-application-running-in-an-azure-container-instance.png" alt-text="Az Azure Container Instances használatával üzembe helyezett alkalmazás képe a böngészőben":::

## <a name="pull-the-container-logs"></a>A tároló naplóinak lekérése

Amikor hibaelhárítást kell végrehajtania egy tárolón, vagy az abban futtatott alkalmazáson (vagy csak meg kell néznie a kimenetet), először tekintse meg a tárolópéldány naplóit.

Például a parancs futtatásával `docker logs` tekintheti meg az *éhes-Kirch* tároló naplóit az ACI kontextusban:

```azurecli-interactive
docker logs hungry-kirch
```

A kimenet megjeleníti a tároló naplóit, és a HTTP GET kéréseknek is meg kell jelenniük, amelyek akkor jöttek létre, amikor az alkalmazást a böngészőjében megtekintette.

```output
listening on port 80
::ffff:10.240.255.55 - - [07/Jul/2020:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [07/Jul/2020:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [07/Jul/2020:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült a tárolóval, futtassa a parancsot `docker rm` az eltávolításához. Ez a parancs leállítja és törli az Azure Container-példányt.

```
docker rm hungry-kirch
```


## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Azure Container-példányt egy nyilvános rendszerképből a Docker és az Azure közötti integráció használatával. További információ az integrációs forgatókönyvekről a [Docker dokumentációjában](https://docs.docker.com/engine/context/aci-integration/). 

A [Docker-bővítményt](https://aka.ms/VSCodeDocker) a Visual Studio Code-hoz is használhatja a tárolók, képek és környezetek fejlesztéséhez, futtatásához és kezeléséhez.

A tároló-példányok létrehozásához és kezeléséhez szükséges Azure-eszközök használatához tekintse meg az [Azure CLI](container-instances-quickstart.md), [Azure PowerShell](container-instances-quickstart-powershell.md), [Azure Portal](container-instances-quickstart-portal.md)és [Azure Resource Manager sablont](container-instances-quickstart-template.md)használó egyéb gyors útmutatókat.

Ha szeretne létrehozni és üzembe helyezni egy tárolórendszerképet egy privát Azure-tárolóregisztrációs adatbázisból, lépjen tovább az Azure Container Instances oktatóanyagára.

> [!div class="nextstepaction"]
> [Az Azure Container Instances oktatóanyaga](./container-instances-tutorial-prepare-app.md)

<!-- LINKS - External -->

[azure-account]: https://azure.microsoft.com/free/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

