---
title: A klasszikus Azure-tároló beállításjegyzék frissítése
description: A kibontott előnyeinek kihasználása szolgáltatáskészlet a Basic, Standard és prémium szintű tároló nyilvántartó kezeli a nem felügyelt klasszikus tároló beállításjegyzék frissítése.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 03/15/2018
ms.author: marsma
ms.openlocfilehash: 084dfc8f87aaea4b5bbad7cb5fdb9d445d566206
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32168707"
---
# <a name="upgrade-a-classic-container-registry"></a>A klasszikus tároló beállításjegyzék frissítése

Az Azure tároló beállításjegyzék (ACR) érhető el többféle szolgáltatáscsomagban kapható, [termékváltozatok néven](container-registry-skus.md). ACR kezdeti kiadásában érhető el egyetlen SKU, klasszikus, amely nem rendelkezik a Basic, Standard és Premium termékváltozat rejlő számos szolgáltatást (összefoglaló néven *felügyelt* nyilvántartó). Ez a cikk részletesen, hogyan telepítheti át a nem felügyelt klasszikus beállításjegyzék egy, a felügyelt SKU, hogy a bővített szolgáltatáskészlet előnyeit élvezheti.

## <a name="why-upgrade"></a>Miért frissíteni?

Nem felügyelt klasszikus nyilvántartó korlátozott szolgáltatásait, elkerülése érdekében javasoljuk összes klasszikus nyilvántartó alapszintű, Standard vagy prémium szintű frissített felügyelt nyilvántartó lehet. A magasabb szintű termékváltozatok a beállításjegyzék mélyebb integrálása az Azure képességeit.

Adja meg a felügyelt nyilvántartó:

* Az Azure Active Directory-integráció [egyedi bejelentkezési](container-registry-authentication.md#individual-login-with-azure-ad)
* Kép és a címke törlése támogatása
* [Georeplikáció](container-registry-geo-replication.md)
* [Webhooks](container-registry-webhook.md)

A klasszikus beállításjegyzék leginkább az összes, a tárfiók, hogy Azure automatikusan látja el az Azure-előfizetése a beállításjegyzék létrehozásakor függ. Ezzel szemben a Basic, Standard és Premium termékváltozat előnyeit Azure [speciális tárolási szolgáltatások](container-registry-storage.md) átláthatóan hajtják végre a képek tárolási az Ön által. Külön tárfiókot nem jön létre a saját előfizetésének.

Felügyelt beállításjegyzék storage a következő előnyöket nyújtja:

* Tároló lemezképek [titkosítása](container-registry-storage.md#encryption-at-rest).
* Lemezképek használatával tárolja [georedundáns tárolás](container-registry-storage.md#geo-redundant-storage)modulhoz, biztonsági mentési több területi replikáció a képek.
* Képes szabadon [termékváltozatok közötti áthelyezése](container-registry-skus.md#changing-skus), ha úgy dönt, hogy egy magasabb szintű SKU, amely lehetővé teszi, hogy nagyobb átviteli sebesség. Az egyes SKU ACR is megfeleljenek a átviteli sebesség növelése az igényeinek.
* A beállításjegyzék és annak tárolóját egységes biztonsági modellt biztosít egyszerűsített rights management. Csak a tároló beállításjegyzék engedélyeit is kezeli az engedélyeket egy külön tárfiókhoz anélkül kezelheti.

A lemezkép tárolási ACR a további részletekért lásd: [tároló lemezkép tárolási Azure tároló beállításjegyzék](container-registry-storage.md).

## <a name="migration-considerations"></a>Az áttelepítés szempontjai

Egy felügyelt beállításjegyzék módosításakor a klasszikus beállításjegyzék Azure át kell másolnia az összes meglévő tároló lemezképek ACR által létrehozott tárfiók az előfizetésben a kezeli az Azure storage-fiók. A rendszerleíró adatbázis méretétől függően a folyamat eltarthat néhány percig, amíg a több órát.

A konvertálás során minden `docker push` a letiltott műveletek során `docker pull` továbbra is működik.

Nem törölni vagy módosítani a tárfiók a klasszikus beállításjegyzék biztonsági a konvertálás során tartalmát. Ennek során azt eredményezheti, hogy a sérülés, a tároló képek.

Ha az áttelepítés befejeződött, a tárfiók az előfizetésben, amelyek eredetileg biztonsági a klasszikus beállításjegyzék ACR már használja. Miután ellenőrizte, hogy az áttelepítés sikerességét, fontolja meg a költségek csökkentése érdekében a tárfiók törlése.

>[!IMPORTANT]
> A felügyelt termékváltozatok egyikét a klasszikus frissítése egy **egyirányú folyamat**. A klasszikus beállításjegyzék alapszintű, Standard vagy prémium konvertált, nem térhet vissza klasszikus. Azonban szabadon áthelyezhet elegendő kapacitással a beállításjegyzékben a felügyelt SKU.

## <a name="how-to-upgrade"></a>Frissítése

Egy nem felügyelt klasszikus beállításjegyzék egy, a felügyelt SKU többféle módon frissítheti. Az alábbi szakaszok azt ismertetik az folyamatát a [Azure CLI] [ azure-cli] és a [Azure-portálon][azure-portal].

## <a name="upgrade-in-azure-cli"></a>Frissítés az Azure parancssori felület

Az Azure parancssori felület a klasszikus beállításjegyzékbeli frissítéséhez hajtsa végre a [az acr frissítés] [ az-acr-update] parancsot, és adja meg a beállításjegyzék az új Termékváltozat. A következő példában egy klasszikus beállításjegyzék nevű *myclassicregistry* nem frissíti a Premium Termékváltozat:

```azurecli-interactive
az acr update --name myclassicregistry --sku Premium
```

Ha az áttelepítés befejeződött, a következőhöz hasonló kimenetnek kell megjelennie. Figyelje meg, hogy a `sku` "prémium" és a `storageAccount` "NULL" jelzi, hogy Azure most kezeli a lemezképet tároló a beállításjegyzékben.

```JSON
{
  "adminUserEnabled": false,
  "creationDate": "2017-12-12T21:23:29.300547+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry",
  "location": "eastus",
  "loginServer": "myregistry.azurecr.io",
  "name": "myregistry",
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "name": "Premium",
    "tier": "Premium"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Ha egy felügyelt beállításjegyzék SKU, amelynek maximális kapacitás nem éri el a klasszikus beállításjegyzék méretét adja meg, az alábbihoz hasonló hibaüzenetet fog kapni.

`Cannot update the registry SKU due to reason: Registry size 12936251113 bytes exceeds the quota value 10737418240 bytes for SKU Basic. The suggested SKU is Standard.`

Ha egy hasonló hibaüzenet jelenik meg, futtassa a [az acr frissítés] [ az-acr-update] újra a parancsot, és adja meg a javasolt SKU, amely a következő legmagasabb szintű SKU, amely lehetővé teszi a képek.

## <a name="upgrade-in-azure-portal"></a>Az Azure portál frissítése

Ha egy klasszikus beállításjegyzék frissítése az Azure-portál használatával, az Azure automatikusan kijelöli a legalacsonyabb szintű SKU, amely lehetővé teszi a képek. Például, ha a beállításjegyzék képek 12 GiB tartalmaz, Azure automatikusan kiválasztja, és konvertálja a klasszikus beállításjegyzék szabvány (maximum: 100 GiB).

A klasszikus beállításjegyzék frissítése az Azure-portál használatával, nyissa meg a tároló beállításjegyzék **áttekintése** válassza **felügyelt beállításjegyzék frissítése**.

![Klasszikus beállításjegyzék frissítése gomb az Azure portálon felhasználói felület][update-classic-01-upgrade]

Válassza ki **OK** annak ellenőrzéséhez, hogy szeretné-e a felügyelt beállításjegyzék frissítése.

![Klasszikus beállításjegyzék frissítése megerősítő az Azure portálon felhasználói felület][update-classic-02-confirm]

Az áttelepítés során a portálon azt jelzi, hogy a beállításjegyzék **üzembe helyezési állapota** van *Frissítéskísérleti*. A korábbiak `docker push` műveletek le vannak tiltva, az áttelepítés során, és nem kell törölni, vagy a tárfiók a klasszikus beállításjegyzék által használt, amíg az áttelepítés van folyamatban – így frissítés eredményezhet a kép sérült.

![Klasszikus beállításjegyzék folyamatban lévő frissítés az Azure portál felhasználói felület][update-classic-03-updating]

Az áttelepítés akkor fejeződött be, amikor a **üzembe helyezési állapota** jelzi *sikeres*, és ismét is `docker push` a beállításjegyzékhez.

![Klasszikus beállításjegyzék frissítésének Azure portál felhasználói felületének befejezési állapota][update-classic-04-updated]

## <a name="next-steps"></a>További lépések

Amennyiben már végzett korábban is klasszikus beállításjegyzék alapszintű, Standard vagy Premium, Azure már nem használja a tárfiók a klasszikus beállításjegyzék eredetileg biztonsági. Költségek csökkentése érdekében fontolja meg a tárfiók vagy belül a fiókot, amelyet a régi tároló lemezképet is tartalmaz a Blob-tároló törlése.

<!-- IMAGES -->
[update-classic-01-upgrade]: ./media/container-registry-upgrade\update-classic-01-upgrade.png
[update-classic-02-confirm]: ./media/container-registry-upgrade\update-classic-02-confirm.png
[update-classic-03-updating]: ./media/container-registry-upgrade\update-classic-03-updating.png
[update-classic-04-updated]: ./media/container-registry-upgrade\update-classic-04-updated.png

<!-- LINKS - internal -->
[az-acr-update]: /cli/azure/acr#az_acr_update
[azure-cli]: /cli/azure/install-azure-cli
[azure-portal]: https://portal.azure.com