---
title: Push & pull Docker-kép
description: Docker-rendszerképek leküldése és lekérése egy Azure-beli privát tároló beállításjegyzékébe és -jegyzékéből a Docker parancssori felületével
ms.topic: article
ms.date: 01/23/2019
ms.custom: seodec18, H1Hack27Feb2017
ms.openlocfilehash: 6751a04c3c1bfe826334161704c20c1ba2e5a6d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456353"
---
# <a name="push-your-first-image-to-a-private-docker-container-registry-using-the-docker-cli"></a>Az első rendszerkép leküldése egy privát Docker-tároló beállításjegyzékébe a Docker parancssori felületével

Az Azure-beli tároló-beállításjegyzékek privát [Docker](https://hub.docker.com)-tárolórendszerképeket tárol és felügyel, hasonlóan ahhoz, ahogyan a [Docker Hub](https://hub.docker.com/) nyilvános Docker-rendszerképeket tárol. Használhatja a [Docker parancssori felület](https://docs.docker.com/engine/reference/commandline/cli/) (Docker CLI) [a bejelentkezési,](https://docs.docker.com/engine/reference/commandline/login/) [push,](https://docs.docker.com/engine/reference/commandline/push/) [pull,](https://docs.docker.com/engine/reference/commandline/pull/)és egyéb műveletek a tároló rendszerleíró adatbázisban.

A következő lépésekben letölt egy hivatalos [Nginx-lemezképet](https://store.docker.com/images/nginx) a nyilvános Docker Hub-beállításjegyzékből, megcímkézi a privát Azure-tároló beállításjegyzékhez, lenyomja a rendszerleíró adatbázisba, majd lekéri a rendszerleíró adatbázisból.

## <a name="prerequisites"></a>Előfeltételek

* **Azure Container Registry** – Létrehozhat egy tároló-beállításjegyzéket Azure-előfizetésében. Használja például az [Azure Portalt](container-registry-get-started-portal.md) vagy az [Azure CLI-t.](container-registry-get-started-azure-cli.md)
* **Docker CLI** – A Docker-t is helyileg kell telepítenie. A Docker csomagokat biztosít, amelyekkel a Docker egyszerűen konfigurálható bármely [macOS][docker-mac], [Windows][docker-windows] vagy [Linux][docker-linux] rendszeren.

## <a name="log-in-to-a-registry"></a>Bejelentkezés beállításjegyzékbe

A magántároló-beállításjegyzékben [többféleképpen is hitelesíthető.](container-registry-authentication.md) A parancssorban végzett munka ajánlott módszer az Azure CLI parancs [az acr bejelentkezés.](/cli/azure/acr?view=azure-cli-latest#az-acr-login) Például a *myregistry*nevű rendszerleíró adatbázisba való bejelentkezéshez:

```azurecli
az acr login --name myregistry
```

A [docker bejelentkezéssel](https://docs.docker.com/engine/reference/commandline/login/)is bejelentkezhet. Előfordulhat például, hogy egy egyszerű [szolgáltatáshoz rendelt](container-registry-authentication.md#service-principal) egy egyszerű szolgáltatást egy automatizálási forgatókönyvhöz. A következő parancs futtatásakor interaktívmódon adja meg a szolgáltatásegyszerű alkalmazásazonosítót (felhasználónevet) és jelszót, amikor a rendszer kéri. A bejelentkezési hitelesítő adatok kezelésével kapcsolatos gyakorlati tanácsokkal a [docker bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/) parancs hivatkozási útmutatójában a következő ta-

```
docker login myregistry.azurecr.io
```

Mindkét parancs `Login Succeeded` visszatér, ha befejeződött.

> [!TIP]
> Mindig adja meg a teljesen minősített rendszerleíró adatbázis `docker login` nevét (minden kisbetűs), amikor használja, és amikor képeket címkéz a rendszerleíró adatbázisba való lenyomásához. Ebben a cikkben szereplő példákban a teljesen minősített név *myregistry.azurecr.io*.

## <a name="pull-the-official-nginx-image"></a>Húzza ki a hivatalos Nginx kép

Először húzza ki a nyilvános Nginx képet a helyi számítógépre.

```
docker pull nginx
```

## <a name="run-the-container-locally"></a>Futtassa helyileg a tárolót

A [docker run](https://docs.docker.com/engine/reference/run/) parancsot követően indítsa el a Nginx-tároló helyi példányát interaktívan (`-it`) a 8080-as porton. Az `--rm` argumentum azt adja meg, hogy a tárolót el kell távolítani, amikor leállítja.

```
docker run -it --rm -p 8080:80 nginx
```

Tallózással tekintse `http://localhost:8080` meg a Nginx által kiszolgált alapértelmezett weblapot a futó tárolóban. A következőhöz hasonló oldalt kell látnia:

![Nginx egy helyi számítógépen](./media/container-registry-get-started-docker-cli/nginx.png)

Mivel a tárolót interaktívan indította el a programmal, `-it`a Nginx-kiszolgáló kimenete a parancssorban látható, miután a böngészőben rá navigált.

A tartály leállításához és `Control` + `C`eltávolításához nyomja meg a gombot.

## <a name="create-an-alias-of-the-image"></a>A kép aliasának létrehozása

A [docker-címke](https://docs.docker.com/engine/reference/commandline/tag/) segítségével hozzon létre egy aliast a lemezképa teljesen minősített elérési útját a rendszerleíró adatbázisba. A példa a(z) `samples` névteret határozza meg, hogy ne legyen zsúfolt a beállításjegyzék gyökere.

```
docker tag nginx myregistry.azurecr.io/samples/nginx
```

A névterekkel való címkézésről az [Azure Container Registry gyakorlati tanácsok](container-registry-best-practices.md)című témakör [adattárnévterek](container-registry-best-practices.md#repository-namespaces) című szakaszában talál további információt.

## <a name="push-the-image-to-your-registry"></a>A rendszerkép leküldése a beállításjegyzékbe

Most, hogy megjelölte a lemezképet a privát rendszerleíró adatbázis teljesen minősített elérési útjával, a [docker push](https://docs.docker.com/engine/reference/commandline/push/)segítségével lenyomhatja a rendszerleíró adatbázisba:

```
docker push myregistry.azurecr.io/samples/nginx
```

## <a name="pull-the-image-from-your-registry"></a>A rendszerkép lekérése a beállításjegyzékből

A [docker lekéréseparanccsal](https://docs.docker.com/engine/reference/commandline/pull/) lekéri a lemezképet a rendszerleíró adatbázisból:

```
docker pull myregistry.azurecr.io/samples/nginx
```

## <a name="start-the-nginx-container"></a>A Nginx-tároló indítása

A [docker-futtatás](https://docs.docker.com/engine/reference/run/) paranccsal futtathatja a rendszerleíró adatbázisból levett lemezképet:

```
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

Tallózással tekintse `http://localhost:8080` meg a futó tárolót.

A tartály leállításához és `Control` + `C`eltávolításához nyomja meg a gombot.

## <a name="remove-the-image-optional"></a>A kép eltávolítása (nem kötelező)

Ha már nincs szüksége a Nginx-lemezképre, a [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) paranccsal helyileg törölheti.

```
docker rmi myregistry.azurecr.io/samples/nginx
```

A lemezképek eltávolítása az Azure container registry, használhatja az Azure CLI parancs [az acr repository törlése](/cli/azure/acr/repository#az-acr-repository-delete). A következő parancs például törli a címke `samples/nginx:latest` által hivatkozott jegyzékfájlt, az egyedi rétegadatokat és a jegyzékfájlra hivatkozó összes többi címkét.

```azurecli
az acr repository delete --name myregistry --image samples/nginx:latest
```

## <a name="next-steps"></a>További lépések

Most, hogy már ismeri az alapokat, készen áll a rendszerleíró adatbázis használatára! Például telepítse a tárolórendszerképeket a rendszerleíró adatbázisból a következő célokra:

* [Azure Kubernetes Service (AKS)](../aks/tutorial-kubernetes-prepare-app.md)
* [Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md)
* [Service Fabric](../service-fabric/service-fabric-tutorial-create-container-images.md)

Igény szerint telepítheti a [Docker-bővítmény t a Visual Studio-kódhoz](https://code.visualstudio.com/docs/azure/docker) és az [Azure-fiókbővítményt](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) az Azure-tároló-beállításjegyzékekkel való együttműködésre. Lekérése és leküldéses lemezképek egy Azure-tároló beállításjegyzékbe, vagy futtassa az ACR-feladatok, mindezt a Visual Studio-kód.


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/
