---
title: A klasszikus Azure Container Registry frissítése
description: A nem felügyelt klasszikus tároló beállításjegyzékének frissítésével kihasználhatja az alapszintű, standard és prémium szintű felügyelt tároló-nyilvántartások kibővített funkcióinak előnyeit.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 03/26/2019
ms.author: danlep
ms.openlocfilehash: 05c227e7de078c6bb371049f16e191598b9ca4e5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68310380"
---
# <a name="upgrade-a-classic-container-registry"></a>Klasszikus tároló beállításjegyzékének frissítése

A Azure Container Registry (ACR) számos szolgáltatási szinten, az [úgynevezett SKU](container-registry-skus.md)-ban érhető el. Az ACR első kiadása egyetlen olyan SKU-t kínált, amely az alapszintű, a standard és a prémium SKU-hoz (együttesen felügyelt beállításjegyzékek néven ismert) számos funkciót nem tartalmaz.

A klasszikus SKU elavult, és április 2019 után elérhetetlenné válik. Ez a cikk részletesen ismerteti, hogyan telepítheti át a nem felügyelt klasszikus beállításjegyzéket az egyik felügyelt SKU-ra, hogy kihasználhassa a kibővített szolgáltatáskészlet előnyeit.

## <a name="why-upgrade"></a>Miért érdemes frissíteni?

A klasszikus beállításjegyzékbeli SKU **elavult**, és **április 2019**. után nem lesz elérhető. Az összes meglévő klasszikus beállításjegyzéket a 2019. április előtt frissíteni kell. A klasszikus kibocsátásiegység-forgalmi jegyzékek portál-felügyeleti funkcióit fokozatosan kivezetjük. Az új klasszikus kibocsátásiegység-forgalmi jegyzékek létrehozása a 2019. április után le lesz tiltva.

A klasszikus, nem felügyelt kibocsátásiegység-forgalmi jegyzékek tervezett elavult és korlátozott képességei miatt minden klasszikus beállításjegyzéket frissíteni kell a felügyelt beállításjegyzékekre (alapszintű, standard vagy prémium). Ezek a magasabb szintű SKU-i mélyebben integrálják a beállításjegyzéket az Azure képességeibe. A különböző szolgáltatási szintek díjszabásával és képességeivel kapcsolatos további információkért lásd: [Container Registry SKU](container-registry-skus.md)-ban.

A klasszikus beállításjegyzék az Azure-előfizetésben a beállításjegyzék létrehozásakor automatikusan kiépített Storage-fióktól függ. Ezzel szemben az alapszintű, a standard és a prémium SKU kihasználja az Azure [speciális tárolási funkcióit](container-registry-storage.md) azáltal, hogy transzparens módon kezeli a lemezképek tárolását. Külön Storage-fiók nem jön létre a saját előfizetésében.

A felügyelt beállításjegyzékbeli tárolás a következő előnyöket biztosítja:

* A tárolói [](container-registry-storage.md#encryption-at-rest)lemezképek inaktív állapotban vannak titkosítva.
* A rendszerképeket a rendszer a [földrajzilag redundáns tárolással](container-registry-storage.md#geo-redundant-storage)tárolja, és biztosítja a lemezképek többrégiós replikálással történő biztonsági mentését (csak prémium SKU esetén).
* A magasabb szintű SKU kiválasztásakor a rendszer szabadon [mozoghat a SKU](container-registry-skus.md#changing-skus)-ban, így magasabb átviteli sebességet tesz lehetővé. Az ACR minden SKU-val megfelel az átviteli sebesség követelményeinek.
* A beállításjegyzék és a hozzá tartozó tároló egységes biztonsági modellje egyszerűsített Rights managementet biztosít. Az engedélyeket csak a tároló-beállításjegyzékhez kell kezelnie, anélkül, hogy a különálló Storage-fiókra vonatkozó engedélyeket kellene kezelnie.

További információ az ACR-beli képtárolásról: [tároló](container-registry-storage.md)képtárolója Azure Container Registryban.

## <a name="migration-considerations"></a>Migrálási szempontok

Ha a klasszikus beállításjegyzéket felügyelt beállításjegyzékre frissíti, az Azure-ban az előfizetéshez tartozó összes meglévő tároló-lemezképet az Azure által kezelt Storage-fiókba kell másolnia az Ön előfizetésének ACR által létrehozott Storage-fiókjából. A beállításjegyzék méretétől függően ez a folyamat több óráig is eltarthat. Becslési célokra várhatóan percenként körülbelül 0,5 GiB áttelepítési időt számítunk fel.

Az átalakítási folyamat során `docker push` a rendszer letiltja a műveleteket az áttelepítés utolsó 10%-ában. `docker pull`a továbbra is a megszokott módon működik.

Az átalakítási folyamat során ne törölje vagy módosítsa a klasszikus beállításjegyzéket támogató Storage-fiók tartalmát. Ennek hatására a tároló lemezképei sérülést okozhatnak.

Az áttelepítés befejezése után az előfizetésében lévő olyan Storage-fiók, amely eredetileg a klasszikus beállításjegyzéket mentette, már nem használja Azure Container Registry. Miután meggyőződött róla, hogy az áttelepítés sikeres volt, érdemes lehet törölni a Storage-fiókot, hogy csökkentse a költségeket.

>[!IMPORTANT]
> A Klasszikusról az egyik felügyelt SKU-ra való frissítés egy egyirányú **folyamat**. Ha alapszintű, standard vagy prémium szintre alakította át a klasszikus beállításjegyzéket, nem lehet visszaállítani a Klasszikusra. A felügyelt SKU-ket azonban szabadon áthelyezheti a beállításjegyzék megfelelő kapacitásával.

## <a name="how-to-upgrade"></a>A frissítés módja

A nem felügyelt klasszikus beállításjegyzéket többféleképpen is frissítheti az egyik felügyelt SKU-ra. A következő szakaszok leírják az [Azure CLI][azure-cli] és a [Azure Portal][azure-portal]használatának folyamatát.

## <a name="upgrade-in-azure-cli"></a>Frissítés az Azure CLI-ben

Ha a klasszikus beállításjegyzéket az Azure CLI-ben szeretné frissíteni, hajtsa végre az az [ACR Update][az-acr-update] parancsot, és határozza meg a beállításjegyzékhez tartozó új SKU-t. A következő példában egy *myclassicregistry* nevű klasszikus beállításjegyzéket frissítünk a prémium SKU-ra:

```azurecli-interactive
az acr update --name myclassicregistry --sku Premium
```

Az áttelepítés befejezésekor a következőhöz hasonló kimenetnek kell megjelennie. Figyelje meg, `sku` hogy a "Prémium", `storageAccount` a `null`pedig az, ami azt jelzi, hogy az Azure most felügyeli a beállításjegyzék rendszerkép-tárolóját.

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

Ha olyan felügyelt beállításjegyzékbeli SKU-t ad meg, amelynek maximális kapacitása kevesebb, mint a klasszikus beállításjegyzék mérete, az alábbihoz hasonló hibaüzenetet kap.

`Cannot update the registry SKU due to reason: Registry size 12936251113 bytes exceeds the quota value 10737418240 bytes for SKU Basic. The suggested SKU is Standard.`

Ha hasonló hibaüzenetet kap, futtassa újra az [az ACR Update][az-acr-update] parancsot, és adja meg a javasolt SKU-t, amely a lemezképek számára elérhető következő legmagasabb szintű SKU.

## <a name="upgrade-in-azure-portal"></a>Frissítés Azure Portal

Ha a Azure Portal használatával frissít egy klasszikus beállításjegyzéket, az Azure automatikusan kiválasztja a standard vagy a prémium SKU-t, attól függően, hogy melyik SKU képes a lemezképek elhelyezésére. Ha például a beállításjegyzékben kevesebb mint 100 GiB szerepel a lemezképekben, az Azure automatikusan kiválasztja és átalakítja a klasszikus beállításjegyzéket standard (100 GiB maximum) értékre.

Ha a Azure Portal használatával szeretné frissíteni a klasszikus beállításjegyzéket, navigáljon a tároló beállításjegyzékének áttekintéséhez, és válassza a **frissítés a felügyelt beállításjegyzékre**lehetőséget.

![A klasszikus beállításjegyzék frissítése gomb a Azure Portal felhasználói felületen][update-classic-01-upgrade]

Az **OK** gombra kattintva erősítse meg, hogy egy felügyelt beállításjegyzékre szeretne frissíteni.

Az áttelepítés során a portál azt jelzi, hogy a beállításjegyzék **kiépítési állapota** *frissül*. Ahogy azt korábban említettük, a rendszer letiltja a műveleteket az áttelepítés utolsó 10%- `docker push` ában. Ne törölje vagy frissítse a klasszikus beállításjegyzék által használt Storage-fiókot, miközben az áttelepítés folyamatban van – ennek hatására a rendszerképek sérülését okozhatják.

![A beállításjegyzék klasszikus verziófrissítésének folyamata a Azure Portal felhasználói felületen][update-classic-03-updating]

Az áttelepítés befejezésekor a **kiépítési állapot** jelzi a *sikeres*műveletet, és folytathatja a normál műveleteket a beállításjegyzékben.

![A beállításjegyzék klasszikus frissítése befejezési állapota a Azure Portal felhasználói felületen][update-classic-04-updated]

## <a name="next-steps"></a>További lépések

Miután frissített egy klasszikus beállításjegyzéket egy felügyelt beállításjegyzékbe, az Azure már nem használja azt a Storage-fiókot, amely eredetileg a klasszikus beállításjegyzéket mentette. A Cost csökkentése érdekében érdemes törölni a Storage-fiókot vagy a BLOB-tárolót a régi tároló lemezképeit tartalmazó fiókon belül.

<!-- IMAGES -->
[update-classic-01-upgrade]: ./media/container-registry-upgrade/update-classic-01-upgrade.png
[update-classic-02-confirm]: ./media/container-registry-upgrade/update-classic-02-confirm.png
[update-classic-03-updating]: ./media/container-registry-upgrade/update-classic-03-updating.png
[update-classic-04-updated]: ./media/container-registry-upgrade/update-classic-04-updated.png

<!-- LINKS - internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[azure-cli]: /cli/azure/install-azure-cli
[azure-portal]: https://portal.azure.com