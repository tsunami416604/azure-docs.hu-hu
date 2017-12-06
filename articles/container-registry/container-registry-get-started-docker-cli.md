---
title: "A saját Azure beállításjegyzékbe leküldéses Docker kép"
description: "Docker-rendszerképek leküldése és lekérése egy Azure-beli privát tároló beállításjegyzékébe és -jegyzékéből a Docker parancssori felületével"
services: container-registry
author: stevelas
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 11/29/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 21d1abfbb49eaeae654a600d35ab350b96a12fd3
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2017
---
# <a name="push-your-first-image-to-a-private-docker-container-registry-using-the-docker-cli"></a>Az első rendszerkép leküldése egy privát Docker-tároló beállításjegyzékébe a Docker parancssori felületével

Az Azure-beli tároló-beállításjegyzékek privát [Docker](http://hub.docker.com)-tárolórendszerképeket tárol és felügyel, hasonlóan ahhoz, ahogyan a [Docker Hub](https://hub.docker.com/) nyilvános Docker-rendszerképeket tárol. Használhatja a [Docker parancssori felületet](https://docs.docker.com/engine/reference/commandline/cli/) (a Docker parancssori felületén) a [bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/), [leküldéses](https://docs.docker.com/engine/reference/commandline/push/), [lekéréses](https://docs.docker.com/engine/reference/commandline/pull/), és más műveleteket végez a tároló beállításjegyzék.

Az alábbi lépéseket, akkor töltse le egy hivatalos [Nginx kép](https://store.docker.com/images/nginx) a Docker Hub nyilvános beállításjegyzékből címkét azt a saját Azure-tárolót rendszerleíró, hogy a beállításjegyzék, és ezután húzza a beállításjegyzékből.

## <a name="prerequisites"></a>Előfeltételek

* **Azure Container Registry** – Létrehozhat egy tároló-beállításjegyzéket Azure-előfizetésében. Ehhez például használhatja az [Azure Portalt](container-registry-get-started-portal.md) vagy az [Azure CLI 2.0-t](container-registry-get-started-azure-cli.md).
* **A docker parancssori felület** – a Docker-gazdagépként a helyi számítógép beállítása és a Docker parancssori felület parancsait hozzáférni, telepítse [Docker](https://docs.docker.com/engine/installation/).

## <a name="log-in-to-a-registry"></a>Bejelentkezés beállításjegyzékbe

Nincsenek [többféleképpen hitelesítéséhez](container-registry-authentication.md) a személyes tárolót beállításjegyzék. Az ajánlott módszer használatakor a parancssorban van az Azure CLI paranccsal [az acr bejelentkezési](/cli/azure/acr?view=azure-cli-latest#az_acr_login). Például, jelentkezzen be a beállításjegyzék nevű *myregistry*:

```azurecli
az acr login --name myregistry
```

Azzal is bejelentkezhet [docker bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/). Az alábbi példában a rendszer egy Azure Active Directory [egyszerű szolgáltatás](../active-directory/active-directory-application-objects.md) azonosítóját és jelszavát adja át. Például lehetséges, hogy [szolgáltatásnevet hozzárendelt](container-registry-authentication.md#service-principal) a beállításjegyzéket az automation-forgatókönyv.

```Bash
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Mindkét parancs visszaadja `Login Succeeded` amint befejeződött. Ha `docker login`, is láthatja a biztonsági figyelmeztetés használatát javasolja a `--password-stdin` paraméter. A használatával nem ez a cikk hatókörén kívül, de javasolt követően a legmegfelelőbb eljárást alkalmazza. További információkért lásd: a [docker bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/) hivatkozás parancsot.

> [!TIP]
> Mindig adja meg a teljesen minősített neve (összes, kisbetű) használatakor `docker login` és mikor címkéz kérdez le, hogy a beállításjegyzék képek. A cikkben szereplő példák a teljesen minősített neve: *myregistry.azurecr.io*.

## <a name="pull-the-official-nginx-image"></a>A hivatalos Nginx kép lekéréses

Első lépésként lekéréses a nyilvános Nginx-lemezképet a helyi számítógépen.

```Bash
docker pull nginx
```

## <a name="run-the-container-locally"></a>Futtassa helyileg a tárolót

Végrehajtás következő [futtatása docker](https://docs.docker.com/engine/reference/run/) parancs interaktív módon elindítani a Nginx tároló helyi példányát (`-it`) a 8080-as porton. A `--rm` argumentum meghatározza, hogy a tároló el kell távolítani az leállításakor.

```Bash
docker run -it --rm -p 8080:80 nginx
```

Keresse meg a [8080](http://localhost:8080) szolgálja ki a futó tárolóban Nginx alapértelmezett weblap megtekintéséhez. A következőhöz hasonló lap kell megjelennie:

![Nginx egy helyi számítógépen](./media/container-registry-get-started-docker-cli/nginx.png)

Mivel a tárolóhoz interaktív használatba `-it`, megtekintheti a Nginx-kiszolgáló kimenetneve a parancssorban navigáljon a böngészőben után.

Állítsa le, és távolítsa el a tároló, nyomja meg a `Control` + `C`.

## <a name="create-an-alias-of-the-image"></a>A kép alias létrehozása

Használjon [docker címke](https://docs.docker.com/engine/reference/commandline/tag/) kép alias létrehozása az abszolút elérési útját a beállításjegyzékben. A példa a(z) `samples` névteret határozza meg, hogy ne legyen zsúfolt a beállításjegyzék gyökere.

```Bash
docker tag nginx myregistry.azurecr.io/samples/nginx
```

A névterek címkézés kapcsolatos további információkért tekintse meg a [tárház névterek](container-registry-best-practices.md#repository-namespaces) szakasza [gyakorlati tanácsok Azure tároló beállításjegyzék](container-registry-best-practices.md).

## <a name="push-the-image-to-your-registry"></a>A kép leküldése a beállításjegyzék

Most, hogy a kép és a teljes elérési útja a személyes beállítási címkéket, tolható ki azt a beállításjegyzék [docker leküldéses](https://docs.docker.com/engine/reference/commandline/push/):

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

## <a name="pull-the-image-from-your-registry"></a>A kép lekéréses a a beállításjegyzékből.

Használja a [docker lekéréses](https://docs.docker.com/engine/reference/commandline/pull/) parancs való lekérésére a lemezképet a beállításjegyzékből:

```Bash
docker pull myregistry.azurecr.io/samples/nginx
```

## <a name="start-the-nginx-container"></a>Indítsa el a Nginx tároló

Használja a [futtatása docker](https://docs.docker.com/engine/reference/run/) parancsot kell futtatni a lemezképet, a beállításjegyzék korábban lekért:

```Bash
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

Lépjen a [http://localhost:8080](http://localhost:8080) helyre a futó tároló megtekintéséhez.

Állítsa le, és távolítsa el a tároló, nyomja meg a `Control` + `C`.

## <a name="remove-the-image-optional"></a>Távolítsa el a lemezképet (nem kötelező)

Ha már nincs szüksége a Nginx-lemezképet, törölheti azt helyileg a [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) parancsot.

```Bash
docker rmi myregistry.azurecr.io/samples/nginx
```

Az Azure-tárolót beállításjegyzékből képek eltávolításához használhatja az Azure CLI parancsot [az acr tárház delete](/cli/azure/acr/repository#az_acr_repository_delete). A következő parancs például egy címkét, társított réteg adatokat és a jegyzék hivatkozó összes más címkék hivatkoznak. a jegyzék törli.

```azurecli
az acr repository delete --name myregistry --repository samples/nginx --tag latest --manifest
```

## <a name="next-steps"></a>Következő lépések

Most, hogy tudja, hogy az alapok, készen áll a beállításjegyzék elindítására! Például a a beállításjegyzékből, tároló-lemezképek központi telepítése egy [Azure tároló szolgáltatás (AKS)](../aks/tutorial-kubernetes-prepare-app.md) fürt.
