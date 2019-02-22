---
title: Az Azure Container Registry kép zárolása
description: Nem lehet törölni vagy felül az Azure container registry egy tárolórendszerképet vagy a tárház attribútumainak beállítása
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 02/19/2019
ms.author: danlep
ms.openlocfilehash: cdf457eefc88edcc22f1fbaab4859fbcf3b69bca
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56653695"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>Egy tárolórendszerképet az Azure container registry zárolása

Az Azure container registry zárolhatja egy lemezkép verziója vagy egy tárház, hogy nem kell törölt vagy frissített. Zárolása egy képet, vagy egy tárház, frissítse a hozzá tartozó attribútumok az Azure CLI-parancs használatával [az acr-adattárfrissítés][az-acr-repository-update]. 

Ez a cikk megköveteli, hogy az Azure CLI az Azure Cloud Shellben futtassa vagy helyileg (2.0.55 verzió vagy újabb ajánlott). A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli].

## <a name="scenarios"></a>Forgatókönyvek

Alapértelmezés szerint egy címkézett rendszerképet az Azure Container Registry a *ezekre kapott válaszokon*, így a megfelelő engedélyekkel rendelkező többször is frissítheti és az azonos címkével ellátott rendszerkép leküldése a tárolójegyzékbe. Tárolórendszerképek is lehet [törölt](container-registry-delete.md) igény szerint. Ez a viselkedés akkor hasznos, ha a lemezképek fejlesztése, és a méret a beállításjegyzék karbantartása szükséges.

Azonban, amikor egy tárolórendszerképet az éles környezetbe helyez üzembe, szüksége lehet egy *nem módosítható* tárolórendszerképet. Nem módosítható kép, amelyik véletlenül nem lehet törölni, vagy felülírja. Használja a [az acr-adattárfrissítés] [ az-acr-repository-update] tárház attribútumainak beállítása, így a parancsot:

* Egy lemezkép verziója, vagy egy teljes tárház zárolása

* Egy lemezkép verziója vagy egy tárház törlés elleni védelem, de a frissítések engedélyezése

* Egy lemezkép verziója vagy egy teljes tárház olvasási (lekéréses) művelet megakadályozása

Tekintse meg a következő szakaszok példákat.

## <a name="lock-an-image-or-repository"></a>Képet vagy tárház zárolása 

### <a name="lock-an-image-by-tag"></a>Kép zárolása címke szerint

A zárolás a *myrepo / myimage:tag* rendszerkép *myregistry*, futtassa a következő [az acr-adattárfrissítés] [ az-acr-repository-update] parancsot:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>Jegyzékfájl kivonatos kép zárolása

A zárolás egy *myrepo/myimage* jegyzékfájl kivonatoló által azonosított kép (SHA-256 kivonatoló,-kiszolgálókként `sha256:...`), futtassa a következő parancsot. (Egy vagy több címkéket társított jegyzékfájl digest megkereséséhez futtassa a [az acr tárház show-jegyzékfájlokká] [ az-acr-repository-show-manifests] parancsot.)

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>Egy adattár zárolása

A zárolás a *myrepo/myimage* adattárat és az összes lemezképet, futtassa a következő parancsot:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>Képet vagy tárház törlés elleni védelem

### <a name="protect-an-image-from-deletion"></a>Kép törlés elleni védelem

Hogy a *myrepo / myimage:tag* lemezkép frissítése, de nem törli, futtassa a következő parancsot:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>Egy adattár törlés elleni védelem

A következő parancsot a csoportok a *myrepo/myimage* tárház, ezért nem törölhető. Egyéni rendszerképek továbbra is frissült vagy törlődött.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>Az olvasási műveletek képet vagy tárház megakadályozása

Az olvasási (lekéréses) művelet elkerülése érdekében a *myrepo / myimage:tag* kép, futtassa a következő parancsot:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --read-enabled false
```

Az olvasási műveletek az összes rendszerkép elkerülése érdekében a *myrepo/myimage* tárházban, futtassa a következő parancsot:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>Képet vagy tárház zárolásának feloldása

Alapértelmezett viselkedése a visszaállítandó a *myrepo / myimage:tag* képen, hogy azt törölni, és frissíteni, futtassa a következő parancsot:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled true --write-enabled true
```

Alapértelmezett viselkedése a visszaállítandó a *myrepo/myimage* tárházat és az összes rendszerkép törlése és frissítése, futtassa a következő parancsot:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben megismerkedett használatával a [az acr-adattárfrissítés] [ az-acr-repository-update] parancs megakadályozza a Törlés és a egy lemezkép-verzió frissítése. További tulajdonságok beállításához tekintse meg a [az acr-adattárfrissítés] [ az-acr-repository-update] referencia parancsot.

Az attribútumok egy lemezkép verziója vagy a tárház beállított megtekintéséhez használja a [az acr-tárház show] [ az-acr-repository-show] parancsot.

Törlési műveletek kapcsolatos részletekért lásd: [törlése az Azure Container Registry a tárolólemezképek][container-registry-delete].

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az-acr-repository-update
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md

