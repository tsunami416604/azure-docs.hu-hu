---
title: Docker-rendszerkép leküldése a privát Azure container registrybe
description: Docker-rendszerképek leküldése és lekérése egy Azure-beli privát tároló beállításjegyzékébe és -jegyzékéből a Docker parancssori felületével
services: container-registry
author: dlepow
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 01/23/2019
ms.author: danlep
ms.custom: seodec18, H1Hack27Feb2017
ms.openlocfilehash: d2aa05d6648a76b02e09c660d7e6f6f30988db53
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57542165"
---
# <a name="push-your-first-image-to-a-private-docker-container-registry-using-the-docker-cli"></a>Az első rendszerkép leküldése egy privát Docker-tároló beállításjegyzékébe a Docker parancssori felületével

Az Azure-beli tároló-beállításjegyzékek privát [Docker](https://hub.docker.com)-tárolórendszerképeket tárol és felügyel, hasonlóan ahhoz, ahogyan a [Docker Hub](https://hub.docker.com/) nyilvános Docker-rendszerképeket tárol. Használhatja a [Docker parancssori felületével](https://docs.docker.com/engine/reference/commandline/cli/) (Docker CLI) a [bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/), [leküldéses](https://docs.docker.com/engine/reference/commandline/push/), [lekéréses](https://docs.docker.com/engine/reference/commandline/pull/), és a tároló egyéb műveletek beállításjegyzék.

A következő lépésekben egy hivatalos letöltési [Nginx rendszerképet](https://store.docker.com/images/nginx) a nyilvános Docker Hub beállításjegyzékből felcímkézésével a privát Azure container Registry, küldheti el azt a tárolójegyzékbe, és majd lekérheti a beállításjegyzékből.

## <a name="prerequisites"></a>Előfeltételek

* **Azure Container Registry** – Létrehozhat egy tároló-beállításjegyzéket Azure-előfizetésében. Például a [az Azure portal](container-registry-get-started-portal.md) vagy a [Azure CLI-vel](container-registry-get-started-azure-cli.md).
* **Docker CLI** – Docker helyi telepítése is szükséges. Docker csomagokat biztosít, a Docker egyszerűen konfigurálható bármely [macOS] [docker-mac], [Windows] [docker-windows] vagy [Linux] [docker-linux] rendszerhez.

## <a name="log-in-to-a-registry"></a>Bejelentkezés beállításjegyzékbe

Nincsenek [többféleképpen hitelesítéséhez](container-registry-authentication.md) a privát tárolóregisztrációs adatbázisba. Az ajánlott módszer használatakor a parancssorban van az Azure CLI-paranccsal [az acr bejelentkezési](/cli/azure/acr?view=azure-cli-latest#az-acr-login). Jelentkezzen be a beállításjegyzék neve például *myregistry*:

```azurecli
az acr login --name myregistry
```

Is bejelentkezhet a következővel [docker login](https://docs.docker.com/engine/reference/commandline/login/). Előfordulhat például, hogy [hozzárendelt egy egyszerű szolgáltatást](container-registry-authentication.md#service-principal) a beállításjegyzékhez egy automatizálási forgatókönyvhöz. A következő parancs futtatásakor adja meg a szolgáltatás egyszerű appID (felhasználónév) és a jelszót, amikor a rendszer kéri. Az ajánlott eljárásokat, és bejelentkezési hitelesítő adatok kezelése, tekintse meg a [docker bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/) referencia parancsot:

```
docker login myregistry.azurecr.io
```

Mindkét parancsot vissza `Login Succeeded` Ha befejeződött.

> [!TIP]
> Mindig adja meg a teljes tartománynév (csak kisbetűkkel) használatakor `docker login` és mikor, címkézhet meg képeket a tárházat a tárolójegyzékbe. A példákban Ez a cikk a teljes tartománynévvel van *myregistry.azurecr.io*.

## <a name="pull-the-official-nginx-image"></a>A hivatalos Nginx rendszerkép lekérése

Először kérje le a nyilvános Nginx-rendszerképet a helyi számítógépen.

```
docker pull nginx
```

## <a name="run-the-container-locally"></a>Futtassa helyileg a tárolót

Hajtsa végre következő [futtatása docker](https://docs.docker.com/engine/reference/run/) parancsot egy helyi példányát az Nginx-tároló interaktívan (`-it`) a 8080-as porton. A `--rm` argumentum meghatározza, hogy a tároló el kell távolítani, leállításakor.

```
docker run -it --rm -p 8080:80 nginx
```

Keresse meg a [ http://localhost:8080 ](http://localhost:8080) megtekintéséhez a kiszolgálása az internetszolgáltatójuk által a futó tárolót az Nginx alapértelmezett weblapot. Egy oldal az alábbihoz hasonlóan kell megjelennie:

![Nginx egy helyi számítógépen](./media/container-registry-get-started-docker-cli/nginx.png)

Mivel elindítottuk a tárolóhoz, az interaktív módon `-it`, láthatja, hogy az Nginx-kiszolgálón a parancssorban kimeneti követően navigáljon a böngészőben.

Állítsa le, és távolítsa el a tároló, nyomja le az `Control` + `C`.

## <a name="create-an-alias-of-the-image"></a>A rendszerkép aliasának létrehozása

Használat [docker címke](https://docs.docker.com/engine/reference/commandline/tag/) hozhat létre egy aliast a rendszerképről a teljes elérési útja a tárolójegyzékbe. A példa a(z) `samples` névteret határozza meg, hogy ne legyen zsúfolt a beállításjegyzék gyökere.

```
docker tag nginx myregistry.azurecr.io/samples/nginx
```

Címkézés a névterek kapcsolatos további információkért lásd: a [adattárnévterek](container-registry-best-practices.md#repository-namespaces) szakaszában [ajánlott eljárások az Azure Container Registry](container-registry-best-practices.md).

## <a name="push-the-image-to-your-registry"></a>A rendszerkép leküldése a tárolójegyzékbe

Most, hogy miután ellátta azt, hogy a kép a teljes elérési útja és a privát regisztrációs adatbázisba, leküldheti a tárolójegyzékbe [docker leküldéses](https://docs.docker.com/engine/reference/commandline/push/):

```
docker push myregistry.azurecr.io/samples/nginx
```

## <a name="pull-the-image-from-your-registry"></a>A rendszerkép lekérése a beállításjegyzékből

Használja a [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) parancsot a rendszerkép lekérése a beállításjegyzékből:

```
docker pull myregistry.azurecr.io/samples/nginx
```

## <a name="start-the-nginx-container"></a>A Nginx-tároló indítása

Használja a [futtatása docker](https://docs.docker.com/engine/reference/run/) parancs futtatása a lemezképet, már a regisztrációs adatbázisból beolvasott:

```
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

Keresse meg a [ http://localhost:8080 ](http://localhost:8080) a futó tároló megtekintéséhez.

Állítsa le, és távolítsa el a tároló, nyomja le az `Control` + `C`.

## <a name="remove-the-image-optional"></a>(Nem kötelező) a rendszerkép eltávolítása

Ha már nincs szüksége az Nginx rendszerképet, törölheti a helyben a [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) parancsot.

```
docker rmi myregistry.azurecr.io/samples/nginx
```

Az Azure container registry rendszerképek eltávolításához használhatja az Azure CLI-paranccsal [az acr-adattár törlése](/cli/azure/acr/repository#az-acr-repository-delete). Például a következő parancs törli a hivatkozik a jegyzékfájlt a `samples/nginx:latest` címke, bármilyen egyedi rendszerképréteg-adatot és minden egyéb címkét hivatkozik a jegyzékfájl.

```azurecli
az acr repository delete --name myregistry --image samples/nginx:latest
```

## <a name="next-steps"></a>További lépések

Most, hogy elsajátította az alapokat, készen áll a beállításjegyzéke használatára! Például helyezhet üzembe rendszerképeket, a regisztrációs adatbázisból:

* [Az Azure Kubernetes Service (AKS)](../aks/tutorial-kubernetes-prepare-app.md)
* [Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md)
* [Service Fabric](../service-fabric/service-fabric-tutorial-create-container-images.md)
