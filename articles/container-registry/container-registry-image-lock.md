---
title: Rendszerképek zárolása
description: Állítsa be a tárolórendszerkép vagy tárház attribútumait, így nem törölhető vagy felülírható egy Azure-tároló beállításjegyzékében.
ms.topic: article
ms.date: 09/30/2019
ms.openlocfilehash: da84767523bb6d948b71b1c1ad2ddaffb628354a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659696"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>Tárolórendszerkép zárolása egy Azure-tároló beállításjegyzékében

Egy Azure-tároló beállításjegyzékben zárolhat egy lemezkép-verziót vagy egy tárházat, hogy ne lehessen törölni vagy frissíteni. Egy lemezkép vagy tárház zárolásához frissítse attribútumait az Azure CLI command [az acr repository frissítésével.][az-acr-repository-update] 

Ez a cikk megköveteli, hogy futtassa az Azure CLI az Azure Cloud Shell vagy helyileg (2.0.55-ös vagy újabb verzió ajánlott). A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli].

> [!IMPORTANT]
> Ez a cikk nem vonatkozik a teljes beállításjegyzék zárolására, például a `az lock` Beállítások > **zárolások** az Azure Portalon, vagy parancsokat az Azure CLI. A rendszerleíró adatbázis-erőforrások zárolása nem akadályozza meg az adattárakban lévő adatok létrehozását, frissítését vagy törlését. A rendszerleíró adatbázis zárolása csak a felügyeleti műveletekre van hatással, például a replikációk hozzáadására vagy törlésére, illetve magára a beállításjegyzékre. További információ az [Erőforrások zárolása a váratlan változások megelőzése érdekében.](../azure-resource-manager/management/lock-resources.md)

## <a name="scenarios"></a>Forgatókönyvek

Alapértelmezés szerint egy címkézett lemezkép az Azure Container Registry *módosítható,* így a megfelelő engedélyekkel többször frissítheti, és leküldéses egy lemezképet ugyanazzal a címkével egy beállításjegyzékbe. A tárolórendszerképek szükség szerint is [törölhetők.](container-registry-delete.md) Ez a viselkedés akkor hasznos, ha képeket fejleszt, és meg kell őriznie a rendszerleíró adatbázis méretét.

Azonban, ha éles környezetben telepít egy tárolórendszerképet, előfordulhat, hogy egy *nem módosítható* tárolórendszerképre van szüksége. A nem módosítható kép olyan kép, amelyet nem lehet véletlenül törölni vagy felülírni.

Tekintse meg [a címkézésre és a verziószámozásra vonatkozó javaslatokat](container-registry-image-tag-version.md) a beállításjegyzékben lévő címke- és verziórendszerképek címkézésére és verziószámozására.

Az [az acr repository update][az-acr-repository-update] parancs segítségével állítsa be a tárház attribútumait, így:

* Lemezkép-verzió vagy teljes tárház zárolása

* A lemezkép-verzió vagy -tárház védelme a törléssel szemben, de frissítések engedélyezése

* Olvasási (lekéréses) műveletek megakadályozása lemezkép-verzión vagy teljes tárházon

A példákat az alábbi szakaszokban talál. 

## <a name="lock-an-image-or-repository"></a>Kép vagy tárház zárolása 

### <a name="show-the-current-repository-attributes"></a>Az aktuális tárházattribútumok megjelenítése
A tárház aktuális attribútumainak megtekintéséhez futtassa a következő [az acr repository show][az-acr-repository-show] parancsot:

```azurecli
az acr repository show \
    --name myregistry --repository myrepo \
    --output jsonc
```

### <a name="show-the-current-image-attributes"></a>Az aktuális képjellemzők megjelenítése
A címke aktuális attribútumainak megtekintéséhez futtassa a következő [az acr repository show][az-acr-repository-show] parancsot:

```azurecli
az acr repository show \
    --name myregistry --image image:tag \
    --output jsonc
```

### <a name="lock-an-image-by-tag"></a>Kép zárolása címke szerint

A *myrepo/myimage:tag* image zárolásához a *myregistry*alkalmazásban futtassa a következő [az acr repository update][az-acr-repository-update] parancsot:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>Kép zárolása jegyzékfájl kivonatolója szerint

A manifest digest (SHA-256 hash, ) néven `sha256:...`ábrázolt *myrepo/myimage* kép zárolásához futtassa a következő parancsot. (Egy vagy több képcímkéhez társított jegyzékfájl kivonatának megkereséséhez futtassa az [az acr repository show-manifests parancsot.)][az-acr-repository-show-manifests]

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>Tárház zárolása

A *myrepo/myimage* tárház és a benne lévő összes kép zárolásához futtassa a következő parancsot:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>Kép vagy tárház védelme a törléstől

### <a name="protect-an-image-from-deletion"></a>Kép védelme a törléstől

A *myrepo/myimage:tag* image frissítésének, de törlésének engedélyezéséhez futtassa a következő parancsot:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>Tárház védelme a törléssel szemben

A következő parancs úgy állítja be a *myrepo/myimage* repository-t, hogy ne lehessen törölni. Az egyes képek továbbra is frissíthetők vagy törölhetők.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>Lemezképvagy adattár olvasási műveleteinek megakadályozása

A *myrepo/myimage:tag* image olvasási (lekéréses) műveleteinek megakadályozásához futtassa a következő parancsot:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --read-enabled false
```

A *myrepo/myimage* tárházban lévő összes lemezképen az olvasási műveletek megakadályozásához futtassa a következő parancsot:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>Kép vagy tárház zárolásának feloldása

A *myrepo/myimage:tag* kép alapértelmezett viselkedésének visszaállításához, hogy az törölhető és frissíthető legyen, futtassa a következő parancsot:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled true --write-enabled true
```

A *myrepo/myimage* tárház és az összes kép alapértelmezett viselkedésének visszaállításához, hogy azok törölhetők és frissíthetők legyenek, futtassa a következő parancsot:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben az [az acr repository update][az-acr-repository-update] parancs használatával megakadályozhatja a lemezkép-verziók törlését vagy frissítését egy tárházban. További attribútumok beállításához tekintse meg az [az acr repository update][az-acr-repository-update] parancs hivatkozását.

A lemezkép-verzióhoz vagy -tárházhoz beállított attribútumok megtekintéséhez használja az [az acr repository show][az-acr-repository-show] parancsot.

A törlési műveletekről a [Tárolórendszerképek törlése az Azure Container Registry alkalmazásban (Delete container images in Azure Container Registry) (Tárolórendszerképek törlése az Azure Container Registry alkalmazásban) (Tárolórendszerképek törlése) (Tárolórendszerképek törlése) (Tárolórendszer][container-registry-delete]

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az-acr-repository-update
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md

