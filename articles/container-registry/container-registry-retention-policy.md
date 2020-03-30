---
title: A címkézetlen jegyzékek megőrzésére vonatkozó házirend
description: Megtudhatja, hogyan engedélyezheti az adatmegőrzési szabályzatot az Azure-tároló beállításjegyzékében, a címkézetlen jegyzékek automatikus törléséhez egy meghatározott időszak után.
ms.topic: article
ms.date: 10/02/2019
ms.openlocfilehash: 912616b6ab95cdff91e70477c7d6de476ccfdfa7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74454811"
---
# <a name="set-a-retention-policy-for-untagged-manifests"></a>Adatmegőrzési házirend beállítása a címkézetlen jegyzékekhez

Az Azure Container Registry lehetőséget ad arra, hogy *adatmegőrzési szabályzatot* állítson be a tárolt rendszerfájllistákhoz, amelyek nem rendelkeznek társított címkékkel (*címkézetlen jegyzékek).* Ha egy adatmegőrzési házirend engedélyezve van, a beállításjegyzékben lévő címkézetlen jegyzékek a beállított napok után automatikusan törlődnek. Ez a szolgáltatás megakadályozza, hogy a beállításjegyzék olyan összetevőkkel töltse ki a hibákat, amelyekre nincs szükség, és segít a tárolási költségek megtakarításában. Ha `delete-enabled` egy címkézetlen jegyzékfájl attribútuma `false`be van állítva, a jegyzékfájl nem törölhető, és az adatmegőrzési házirend nem alkalmazható.

Használhatja az Azure Cloud Shell vagy az Azure CLI helyi telepítését a cikkben szereplő parancspéldák futtatásához. Ha helyileg szeretné használni, a 2.0.74-es vagy újabb verzió szükséges. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli].

> [!IMPORTANT]
> Ez a funkció jelenleg előzetes verzióban érhető el, és bizonyos [korlátozások érvényesek.](#preview-limitations) Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

> [!WARNING]
> Adatmegőrzési szabály beállítása care-törölt képadatok nem helyreállítható. Ha olyan rendszerekkel rendelkezik, amelyek a listákat tartalmazó kivonatolással lekérik a képeket (ellentétben a lemezkép nevével), ne állítson be adatmegőrzési házirendet a címkézetlen jegyzékekhez. A címkézetlen képek törlése megakadályozza, hogy ezek a rendszerek lehúzzák a képeket a rendszerleíró adatbázisból. Ahelyett, hogy a manifesztet húzná, fontolja meg egy *egyedi címkézési* rendszer elfogadását, amely [ajánlott ajánlott eljárás.](container-registry-image-tag-version.md)

## <a name="preview-limitations"></a>Előnézeti korlátozások

* Csak egy **prémium szintű** tároló beállításjegyzék konfigurálható adatmegőrzési házirenddel. A beállításjegyzék-szolgáltatási szintekről az [Azure Container Registry ska](container-registry-skus.md)című témakörben talál további információt.
* Csak a címkézetlen jegyzékjegyzékek adatmegőrzési házirendje állítható be.
* Az adatmegőrzési szabály jelenleg csak azokra a jegyzéklistákra vonatkozik, amelyek a házirend engedélyezése *után* nincsenek címkézve. A beállításjegyzékben meglévő, címkézetlen jegyzékfájlokra nem vonatkozik a házirend. A meglévő címkézetlen jegyzékek törléséhez tekintse meg a példákat a [Tárolórendszerképek törlése az Azure Container Registry alkalmazásban.](container-registry-delete.md)

## <a name="about-the-retention-policy"></a>Az adatmegőrzési szabályról

Az Azure Container Registry a jegyzékben lévő jegyzékjegyzékek számlálására hivatkozik. Ha egy jegyzékfájl nincs címkézve, ellenőrzi az adatmegőrzési házirendet. Ha egy adatmegőrzési házirend engedélyezve van, a jegyzékfájl törlése művelet várólistára kerül, egy adott dátummal, a házirendben beállított napok számának megfelelően.

Egy külön várólista-kezelési feladat folyamatosan feldolgozza az üzeneteket, szükség szerint skálázva. Tegyük fel például, hogy két, 1 óra különbséggel lévő jegyzékben címkézetlen két jegyzéket címkézett, 30 napos adatmegőrzési házirenddel. Két üzenet lenne várólistára. Ezután 30 nappal később, körülbelül 1 óra különbséggel az üzenetek et a rendszer lekéri a várólistából, és feldolgozza, feltételezve, hogy a házirend még érvényben van.

## <a name="set-a-retention-policy---cli"></a>Adatmegőrzési szabály beállítása - CLI

A következő példa bemutatja, hogyan használhatja az Azure CLI-t a nem címkézett jegyzékek adatmegőrzési szabályzatának beállítására.

### <a name="enable-a-retention-policy"></a>Adatmegőrzési házirend engedélyezése

Alapértelmezés szerint nincs adatmegőrzési szabály beállítva a tároló beállításjegyzékében. Adatmegőrzési szabályzat beállítása vagy frissítése, futtassa az [az acr config megőrzési frissítő][az-acr-config-retention-update] parancsot az Azure CLI.To set or update a retention policy, run the az acr config retention update command in the Azure CLI. 0 és 365 között megadhat néhány napot a címkézetlen jegyzékek megőrzéséhez. Ha nem ad meg napokszámát, a parancs 7 napos alapértelmezett értéket állít be. A megőrzési időszak után a rendszerleíró adatbázisban lévő összes címkézetlen jegyzék automatikusan törlődik.

A következő példa 30 napos adatmegőrzési házirendet állít be a rendszerleíró *adatbázisban*lévő címkézetlen jegyzékek esetében:

```azurecli
az acr config retention update --registry myregistry --status enabled --days 30 --type UntaggedManifests
```

A következő példa beállítja a házirendet, hogy törölje a jegyzékben, amint az nem címkézett. Hozza létre ezt a házirendet 0 napos megőrzési idő beállításával. 

```azurecli
az acr config retention update --registry myregistry --status enabled --days 0 --type UntaggedManifests
```

### <a name="validate-a-retention-policy"></a>Adatmegőrzési szabály ellenőrzése

Ha az előző házirendet 0 napos megőrzési idővel engedélyezi, gyorsan ellenőrizheti, hogy a címkézetlen jegyzékek törlődnek-e:

1. Nyomjon le `hello-world:latest` egy tesztlemezképet a rendszerleíró adatbázisba, vagy helyettesítsen egy másik, ön által kiválasztott tesztképet.
1. A rendszerkép címkézésének `hello-world:latest` visszavonása például az az [acr repository untag][az-acr-repository-untag] paranccsal. A címkézetlen jegyzékfájl a rendszerleíró adatbázisban marad.
    ```azurecli
    az acr repository untag --name myregistry --image hello-world:latest
    ```
1. Néhány másodpercen belül a címkézetlen jegyzékfájl törlődik. A törlést a jegyzékjegyzékek listázásával ellenőrizheti a tárházban, például az [az acr repository show-manifests][az-acr-repository-show-manifests] paranccsal. Ha a tesztkép volt az egyetlen a tárházban, maga a tárház törlődik.

### <a name="disable-a-retention-policy"></a>Adatmegőrzési házirend letiltása

A rendszerleíró adatbázisban beállított adatmegőrzési házirend megtekintéséhez futtassa az [az acr config retention show][az-acr-config-retention-show] parancsot:

```azurecli
az acr config retention show --registry myregistry
```

Ha le szeretne tiltani egy adatmegőrzési házirendet egy beállításjegyzékben, futtassa az [azacr config retention update][az-acr-config-retention-update] parancsot, és állítsa be a következőt: `--status disabled`

```azurecli
az acr config retention update --registry myregistry --status disabled --type UntaggedManifests
```

## <a name="set-a-retention-policy---portal"></a>Adatmegőrzési szabály beállítása - portál

A beállításjegyzék adatmegőrzési szabályzatát az [Azure Portalon](https://portal.azure.com)is beállíthatja. A következő példa bemutatja, hogyan használhatja a portált a nem címkézett jegyzékek adatmegőrzési házirendjének beállítására.

### <a name="enable-a-retention-policy"></a>Adatmegőrzési házirend engedélyezése

1. Keresse meg az Azure-tároló beállításjegyzékét. A **Házirendek**csoportban válassza **az Adatmegőrzés** (előzetes verzió) lehetőséget.
1. Az **Állapot csoportban**válassza **az Engedélyezve**lehetőséget.
1. A címkézetlen jegyzékek megőrzéséhez jelöljön ki néhány napot 0 és 365 között. Kattintson a **Mentés** gombra.

![Adatmegőrzési házirend engedélyezése az Azure Portalon](media/container-registry-retention-policy/container-registry-retention-policy01.png)

### <a name="disable-a-retention-policy"></a>Adatmegőrzési házirend letiltása

1. Keresse meg az Azure-tároló beállításjegyzékét. A **Házirendek**csoportban válassza **az Adatmegőrzés** (előzetes verzió) lehetőséget.
1. Az **Állapot csoportban**válassza **a Letiltva**lehetőséget. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>További lépések

* További információ a [képek és adattárak azure](container-registry-delete.md) container registry szolgáltatásában való törlésének lehetőségeiről

* A kijelölt képek és jegyzékek [automatikus törlése](container-registry-auto-purge.md) a rendszerleíró adatbázisból

* További információ a [képek és jegyzékek rendszerleíró](container-registry-image-lock.md) adatbázisban való zárolásának lehetőségeiről

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/


<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-retention-update]: /cli/azure/acr/config/retention#az-acr-config-retention-update
[az-acr-config-retention-show]: /cli/azure/acr/config/retention#az-acr-config-retention-show
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
