---
title: Szabályzat a címkézetlen jegyzékfájlok megőrzéséhez Azure Container Registry
description: Megtudhatja, hogyan engedélyezheti az adatmegőrzési szabályzatot az Azure Container registryben a címkézetlen jegyzékfájlok meghatározott időszak után történő automatikus törléséhez.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 09/25/2019
ms.author: danlep
ms.openlocfilehash: 36d27bc6089bbe3f4ada6862a9c1be1fa0bdbae7
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71306002"
---
# <a name="set-a-retention-policy-for-untagged-manifests"></a>Adatmegőrzési szabályzat beállítása a címkézetlen jegyzékekhez

Azure Container Registry lehetővé teszi az *adatmegőrzési szabályzat* beállítását olyan tárolt képjegyzékek esetében, amelyek nem rendelkeznek társított címkékkel (*címkézett jegyzékfájlokkal*). Ha egy adatmegőrzési szabály engedélyezve van, a beállításjegyzékben lévő címkézetlen jegyzékfájlok automatikusan törlődnek a megadott számú nap elteltével. Ez a szolgáltatás megakadályozza, hogy a beállításjegyzék nem szükséges összetevőkkel töltse fel a szolgáltatást, és segít a tárolási költségek megtakarításában. Ha a `delete-enabled` címkézetlen jegyzékfájl attribútuma a értékre van `false`állítva, a jegyzékfájl nem törölhető, és a megőrzési szabály nem érvényes.

Az Azure CLI Azure Cloud Shell vagy helyi telepítése segítségével futtathatja a jelen cikkben szereplő példákat. Ha helyileg szeretné használni, a 2.0.74 vagy újabb verziót kötelező megadni. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli].

> [!IMPORTANT]
> Ez a funkció jelenleg előzetes verzióban érhető el, és bizonyos [korlátozások érvényesek](#preview-limitations). Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

> [!WARNING]
> Adatmegőrzési házirend beállítása gondossággal – törölt képadatokkal nem állítható helyre. Ha olyan rendszerekkel rendelkezik, amelyekben a manifest Digest (a rendszerkép neve helyett) lekéri a képeket, ne állítson be adatmegőrzési szabályt a címkézetlen jegyzékekhez. A címkézetlen lemezképek törlésével megakadályozhatja, hogy ezek a rendszerek kihúzzanak a lemezképeket a beállításjegyzékből. A jegyzékfájlok helyett érdemes lehet egy *egyedi címkézési* sémát alkalmazni, amely [ajánlott eljárás](container-registry-image-tag-version.md).

Ha egyetlen képcímkét vagy jegyzékfájlokat szeretne törölni az Azure CLI-parancsokkal, tekintse meg [a tároló lemezképek törlése a Azure Container Registryban](container-registry-delete.md)című témakört.

## <a name="preview-limitations"></a>Előzetes verzió korlátozásai

* Csak a **prémium** szintű tároló-beállításjegyzék állítható be adatmegőrzési házirenddel. További információ a beállításjegyzék szolgáltatási szintjeiről: [Azure Container Registry SKU](container-registry-skus.md)-ban.
* Csak a címkézetlen jegyzékek adatmegőrzési szabályzata állítható be.

## <a name="set-a-retention-policy---cli"></a>Adatmegőrzési szabály beállítása – parancssori felület

Az alábbi példa bemutatja, hogyan állíthatja be a beállításjegyzékben az Azure CLI használatával a címkézetlen jegyzékfájlok megőrzési szabályát.

### <a name="enable-a-retention-policy"></a>Adatmegőrzési szabályzat engedélyezése

Alapértelmezés szerint a tároló-beállításjegyzékben nincs beállítva adatmegőrzési szabály. Adatmegőrzési szabály beállításához vagy frissítéséhez futtassa az az [ACR config adatmegőrzési frissítés][az-acr-config-retention-update] parancsot az Azure CLI-ben. 0 és 365 közötti napokat is megadhat a címkézetlen jegyzékfájlok megőrzéséhez. Ha nem adja meg a napok számát, a parancs alapértelmezés szerint 7 napot állít be. A megőrzési időtartam után a beállításjegyzékben szereplő összes címkézetlen jegyzékfájl automatikusan törlődik.

A következő példa egy 30 napos adatmegőrzési szabályzatot állít be címkézetlen jegyzékekhez a beállításjegyzék *myregistry*:

```azurecli
az acr config retention update --name myregistry --status enabled --days 30 --type UntaggedManifests
```

A következő példa egy szabályzatot állít be a beállításjegyzékben lévő összes jegyzékfájl törlésére, amint a címke fel van jelölve. Hozza létre ezt a házirendet 0 napos megőrzési időtartam beállításával:

```azurecli
az acr config retention update --name myregistry --status enabled --days 0 --type UntaggedManifests
```

### <a name="disable-a-retention-policy"></a>Adatmegőrzési szabály letiltása

Ha meg szeretné tekinteni a beállításjegyzékben beállított adatmegőrzési szabályzatot, futtassa az az [ACR config adatmegőrzési show][az-acr-config-retention-show] parancsot:

```azurecli
az acr config retention show --name myregistry
```

Ha le szeretne tiltani egy adatmegőrzési szabályt egy beállításjegyzékben, futtassa az az [ACR config megőrzési frissítés][az-acr-config-retention-update] parancsot, és állítsa be `--status disabled`a következőt:

```azurecli
az acr config retention update --name myregistry --status disabled
```

## <a name="set-a-retention-policy---portal"></a>Adatmegőrzési szabályzat beállítása – portál

A beállításjegyzék adatmegőrzési házirendjét is beállíthatja a [Azure Portalban](https://portal.azure.com). A következő példa azt mutatja be, hogyan használható a portál a címkézetlen jegyzékfájlok megőrzési szabályának beállítására a beállításjegyzékben.

### <a name="enable-a-retention-policy"></a>Adatmegőrzési szabályzat engedélyezése

1. Navigáljon az Azure Container Registry szolgáltatáshoz. A **házirendek**területen válassza a **megőrzés** (előzetes verzió) lehetőséget.
1. Az **állapot**területen válassza az **engedélyezve**lehetőséget.
1. A címkézetlen jegyzékfájlok megőrzéséhez válassza ki a 0 és 365 közötti napok számát. Kattintson a **Mentés** gombra.

![Adatmegőrzési szabályzat engedélyezése Azure Portal](media/container-registry-retention-policy/container-registry-retention-policy01.png)

### <a name="disable-a-retention-policy"></a>Adatmegőrzési szabály letiltása

1. Navigáljon az Azure Container Registry szolgáltatáshoz. A **házirendek**területen válassza a **megőrzés** (előzetes verzió) lehetőséget.
1. Az **állapot**területen válassza a **Letiltva**lehetőséget. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>További lépések

* További információ a [lemezképek és adattárak Azure Container Registry való törlésének](container-registry-delete.md) lehetőségeiről

* Megtudhatja, hogyan lehet [automatikusan törölni](container-registry-auto-purge.md) a kijelölt lemezképeket és jegyzékeket egy beállításjegyzékből

* További információ a [lemezképek és](container-registry-image-lock.md) a jegyzékek beállításjegyzékben való zárolásának lehetőségeiről

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/


<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-retention-update]: /cli/azure/acr/config/retention#az-acr-config-retention-update
[az-acr-config-retention-show]: /cli/azure/acr/config/retention#az-acr-config-retention-show
