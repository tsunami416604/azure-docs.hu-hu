---
title: "Azure tároló példányok útmutató – Azure tároló beállításjegyzék előkészítése"
description: "Az Azure tároló példányok útmutató 2. rész 3 - Azure tároló beállításjegyzék előkészítése"
services: container-instances
author: neilpeterson
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: c0aad1f9bbaac9a456b34f75633faba92f57f498
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2018
---
# <a name="deploy-and-use-azure-container-registry"></a>Üzembe helyezés és használat Azure tároló beállításjegyzék

Ez az egy háromrészes oktatóanyag második rész. Az a [előző lépésben](container-instances-tutorial-prepare-app.md), a tároló-lemezkép létrejött egy egyszerű webes alkalmazáshoz írt [Node.js][nodejs]. Ebben az oktatóanyagban a lemezkép egy Azure-tároló beállításjegyzék leküldéses. A tároló kép nem hozott létre, ha vissza [oktatóanyag 1 – tároló kép létrehozása](container-instances-tutorial-prepare-app.md).

Az Azure-tároló beállításjegyzék egy Azure-alapú, személyes beállításjegyzék Docker-tároló lemezképek. Ez az oktatóanyag végigvezeti Azure tároló beállításjegyzék-példány telepítését, valamint a végez leküldést a tároló-lemezkép.

Ez a cikk második rész a sorozat meg:

> [!div class="checklist"]
> * Azure-tároló beállításjegyzék-példányt telepítése
> * Egy tároló lemezképet az Azure-tárolót beállításjegyzék címke
> * Feltölti a lemezképet a beállításjegyzéket

A következő cikkben, az adatsorozat utolsó oktatóanyag telepít a tároló a személyes beállításjegyzékből Azure tároló példányok.

## <a name="before-you-begin"></a>Előkészületek

Ez az oktatóanyag megköveteli, hogy futnak-e az Azure parancssori felület 2.0.23 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha szeretné telepíteni vagy frissíteni, lásd: [Azure CLI 2.0 telepítése][azure-cli-install].

Az oktatóanyag elvégzéséhez egy Docker fejlesztőkörnyezet helyileg telepíteni kell. Docker biztosít, amely egyszerű konfigurálását a Docker bármely csomagok [Mac][docker-mac], [Windows][docker-windows], vagy [Linux] [ docker-linux] rendszer.

Azure Cloud rendszerhéj nem tartalmazza a Docker-összetevők minden egyes lépéseinek befejezéséhez szükséges az oktatóanyag. Az oktatóanyag teljesítéséhez a helyi számítógépen telepítenie kell az Azure CLI és a Docker fejlesztési környezet.

## <a name="deploy-azure-container-registry"></a>Telepítse az Azure tároló beállításjegyzék

Egy Azure-tároló beállításjegyzék való telepítésekor, először egy erőforráscsoportot. Egy Azure-erőforráscsoportot gyűjteményei logikai mely Azure az erőforrások telepítése és kezelése.

Hozzon létre egy erőforráscsoportot az [az group create][az-group-create] paranccsal. Ebben a példában az erőforráscsoport neve *myResourceGroup* jön létre a *eastus* régióban.

```azurecli
az group create --name myResourceGroup --location eastus
```

Hozzon létre egy Azure-tárolót beállításjegyzéket a [az acr létrehozása] [ az-acr-create] parancsot. A tároló neve **egyedinek kell lennie** Azure,-ban, és 5-50 alfanumerikus karaktereket tartalmazhat. Cserélje le `<acrName>` a beállításjegyzék egyedi nevére:

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

Például hozzon létre egy Azure-tárolót beállításjegyzék nevű *mycontainerregistry082*:

```azurecli
az acr create --resource-group myResourceGroup --name mycontainerregistry082 --sku Basic --admin-enabled true
```

Ez az oktatóanyag a többi, egész használjuk `<acrName>` a tároló neve választott számára.

## <a name="container-registry-login"></a>Tároló beállításjegyzék bejelentkezés

Be kell jelentkezni Azure tároló beállításjegyzék-példány előtt képek rá. Használja a [az acr bejelentkezési] [ az-acr-login] parancs használatával végrehajtani a műveletet. Meg kell adnia a tároló beállításjegyzék létrehozásakor megadott egyedi nevét.

```azurecli
az acr login --name <acrName>
```

A parancs visszaadja a `Login Succeeded` üzenet amint befejeződött.

## <a name="tag-container-image"></a>Címke tároló kép

Személyes beállításjegyzékből tároló lemezkép telepítéséhez, a lemezkép kell címkét a `loginServer` nevét a beállításjegyzékben.

Lemezképek aktuális listájának megtekintéséhez használja a [docker képek] [ docker-images] parancsot.

```bash
docker images
```

Kimenet:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
aci-tutorial-app             latest              5c745774dfa9        39 seconds ago       68.1 MB
```

Ahhoz, hogy a loginServer nevét, futtassa a [az acr megjelenítése] [ az-acr-show] parancsot. Cserélje le `<acrName>` a tároló beállításjegyzék nevével.

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

Példa a kimenetre:

```
Result
------------------------
mycontainerregistry082.azurecr.io
```

Címke a *aci-oktatóanyag – alkalmazás* a tároló beállításjegyzék loginServer lemezkép. Továbbá adja hozzá `:v1` , a lemezkép neve végén. Ezt a címkét azt jelzi, hogy a lemezkép verziószámát. Cserélje le `<acrLoginServer>` eredményével a [az acr megjelenítése] [ az-acr-show] parancs csak hajtja végre.

```bash
docker tag aci-tutorial-app <acrLoginServer>/aci-tutorial-app:v1
```

Miután megjelölve, futtassa az `docker images` ellenőrzése a műveletet.

```bash
docker images
```

Kimenet:

```bash
REPOSITORY                                                TAG                 IMAGE ID            CREATED             SIZE
aci-tutorial-app                                          latest              5c745774dfa9        39 seconds ago      68.1 MB
mycontainerregistry082.azurecr.io/aci-tutorial-app        v1                  a9dace4e1a17        7 minutes ago       68.1 MB
```

## <a name="push-image-to-azure-container-registry"></a>Azure-tároló beállításjegyzék leküldéses kép

Leküldéses a *aci-oktatóanyag – alkalmazás* lemezképet a beállításjegyzéket a [docker leküldéses] [ docker-push] parancsot. Cserélje le `<acrLoginServer>` a korábbi lépésben beszerzése a teljes bejelentkezési kiszolgáló nevével.

```bash
docker push <acrLoginServer>/aci-tutorial-app:v1
```

A `push` művelet néhány másodpercre kell néhány percet, attól függően, hogy az internetkapcsolat, és a kimenete az alábbihoz hasonló:

```bash
The push refers to a repository [mycontainerregistry082.azurecr.io/aci-tutorial-app]
3db9cac20d49: Pushed
13f653351004: Pushed
4cd158165f4d: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:ed67fff971da47175856505585dcd92d1270c3b37543e8afd46014d328f05715 size: 1576
```

## <a name="list-images-in-azure-container-registry"></a>Azure-tároló beállításjegyzék lemezképek felsorolása

Olyan lemezképkészlet, amellyel az Azure-tárolóba beállításjegyzék értesítését listáját adja vissza, használja a [az acr tárház lista] [ az-acr-repository-list] parancsot. A parancs frissíti a tároló neve.

```azurecli
az acr repository list --name <acrName> --output table
```

Kimenet:

```azurecli
Result
----------------
aci-tutorial-app
```

A címkék azokba megtekintéséhez használja a [az acr tárház megjelenítése-címkék] [ az-acr-repository-show-tags] parancsot.

```azurecli
az acr repository show-tags --name <acrName> --repository aci-tutorial-app --output table
```

Kimenet:

```azurecli
Result
--------
v1
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure-tároló beállításjegyzék kész az Azure-tároló példányaival használatra, és a tároló lemezkép leküldeni a beállításjegyzékben. A következő lépéseket hajtotta végre:

> [!div class="checklist"]
> * Egy Azure-tároló beállításjegyzék-példány telepítése
> * A tároló-lemezkép az Azure-tároló beállításjegyzék címkézett
> * Azure tároló beállításjegyzék lemezkép feltöltése

A következő oktatóanyag az Azure-ban Azure tároló példányok a tároló telepítésével kapcsolatos további továbblépés.

> [!div class="nextstepaction"]
> [Azure-tároló példányok – tárolók üzembe helyezése](./container-instances-tutorial-deploy-app.md)

<!-- LINKS - External -->
[docker-build]: https://docs.docker.com/engine/reference/commandline/build/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-hub-nodeimage]: https://store.docker.com/images/node
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[nodejs]: http://nodejs.org

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-repository-list]: /cli/azure/acr/repository#az_acr_list
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az_acr_repository_show_tags
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-group-create]: /cli/azure/group#az_group_create
[azure-cli-install]: /cli/azure/install-azure-cli
