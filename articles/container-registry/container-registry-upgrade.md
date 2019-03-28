---
title: A klasszikus Azure-tároló beállításjegyzékének frissítése
description: Kihasználhatja a kibontott szolgáltatáskészletére vonatkozóan a Basic, Standard és prémium szintű tároló-beállításjegyzékek felügyelt a nem felügyelt klasszikus tárolóregisztrációs adatbázis frissítésével.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/26/2019
ms.author: danlep
ms.openlocfilehash: a5099feee34eb5497b68987485412e29ad5d5365
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58521516"
---
# <a name="upgrade-a-classic-container-registry"></a>Klasszikus tároló-beállításjegyzék frissítése

Többféle szolgáltatáscsomagban érhető el az Azure Container Registry (ACR) [termékváltozatok néven](container-registry-skus.md). ACR kezdeti verziójában érhető el egy egyetlen Termékváltozat klasszikus, amely nem rendelkezik a Basic, Standard és prémium szintű csomag beépített szolgáltatásai számos funkciót (összefoglaló néven *felügyelt* beállításjegyzékek).

A klasszikus Termékváltozat hamarosan elavulttá válik, és április 2019 után nem lesz elérhető. Ez a cikk részletesen a nem felügyelt klasszikus beállításjegyzékben áttelepítése a felügyelt termékváltozatok közül, így azok továbbfejlesztett szolgáltatáskészlet előnyeit is.

## <a name="why-upgrade"></a>Miért érdemes frissíteni?

A klasszikus beállításjegyzékben folyamatban van a Termékváltozat **elavult**, és nem lesz elérhető után **április 2019**. Az összes meglévő klasszikus beállításjegyzékek április 2019 előtt kell frissíteni. A beállításjegyzékek klasszikus portál felügyeleti funkcióinak kivezetése lesz. Az új klasszikus nyilvántartó létrehozása után április 2019 letiltásra kerül.

Tervezett elavulásának és klasszikus nem felügyelt beállításjegyzékek korlátozott képességeit az összes klasszikus beállításjegyzékek felügyelt beállításjegyzékek (alapszintű, Standard vagy prémium) kell frissíteni. Ezek a magasabb szintű termékváltozatok a beállításjegyzék így integrálható az Azure funkcióit. A díjszabásról és a funkciókról a különböző szolgáltatásszintek kapcsolatos további információkért lásd: [Container Registry Termékváltozatai](container-registry-skus.md).

A klasszikus beállításjegyzékben attól függ, hogy a tárfiók, amely az Azure automatikusan látja el az Azure-előfizetésben a beállításjegyzék létrehozásakor. Ezzel szemben a Basic, Standard és prémium szintű termékváltozatok kihasználhatja az Azure [speciális tárolási szolgáltatások](container-registry-storage.md) átlátható módon kezeli az Ön számára a lemezképek tárolását. Egy önálló tárfiókot a saját előfizetése nem jön létre.

Felügyelt beállításjegyzék tárterületét az alábbi előnyöket nyújtja:

* Tárolórendszerkép [titkosítása](container-registry-storage.md#encryption-at-rest).
* Rendszerképek tárolása [georedundáns tárolás](container-registry-storage.md#geo-redundant-storage)modulhoz, biztonsági mentés, a képek a többrégiós replikációval (csak prémium szintű Termékváltozat esetében).
* Lehetővé teszi, hogy szabadon [termékváltozatok közötti áthelyezése](container-registry-skus.md#changing-skus), ha úgy dönt, hogy egy magasabb szintű Termékváltozat, amely lehetővé teszi, hogy nagyobb átviteli sebességet. Minden SKU, az ACR megfelel az átviteli sebességet megkövetelő, növelje az igényeinek.
* A beállításjegyzék és a tárolás egységes biztonsági modellt biztosít egyszerűsített rights management. Kezelheti az engedélyeket, csak az a container registry esetében nem kell külön tárfiókra vonatkozó engedélyeket is tud kezelni.

Az ACR képtárolás a további részletekért lásd: [képtárolás tárolót az Azure Container Registry](container-registry-storage.md).

## <a name="migration-considerations"></a>Az áttelepítés szempontjai

Amikor frissít egy klasszikus beállításjegyzék egy felügyelt beállításjegyzéket, az Azure egy tárfiókba, Azure által kezelt kell másolnia összes meglévő tárolórendszerkép az ACR-létrehozott storage-fiókból az előfizetésében. A beállításjegyzék méretétől függően a folyamat eltarthat néhány percig akár néhány órát. Költségbecslési célokat a várt a migrálási idő körülbelül 0,5 GB / perc.

A konvertálás során `docker push` műveletek le vannak tiltva, az utolsó 10 %-a migrálás során. `docker pull` továbbra is megfelelően működik.

Nem törölhető és nem módosíthatják a tárfiók a klasszikus beállításjegyzékben biztonsági a konvertálás során a tartalmát. Ezzel a tárolólemezképek sérülését eredményezheti.

Az áttelepítés befejeződése után a tárfiókot az előfizetésében, amelyet eredetileg biztonsági a klasszikus beállításjegyzékben már nem használják az Azure Container Registry. Miután meggyőződött arról, hogy az áttelepítés sikeres volt-e, vegye figyelembe a költségek csökkentése érdekében a tárfiók törlése.

>[!IMPORTANT]
> A felügyelt termékváltozatok egyik klasszikusból frissítése egy **egyirányú folyamat**. Klasszikus beállításjegyzék alapszintű, Standard vagy prémium konvertált, nem térhet vissza a klasszikus. Azonban szabadon áthelyezheti a beállításjegyzék elegendő kapacitással a felügyelt termékváltozatok között.

## <a name="how-to-upgrade"></a>Frissítése

Egy nem felügyelt klasszikus beállításjegyzék egy a felügyelt termékváltozatok számos módon frissítheti. A következő szakaszban ismertetünk folyamatát a [Azure CLI-vel] [ azure-cli] és a [az Azure portal][azure-portal].

## <a name="upgrade-in-azure-cli"></a>Frissítés az Azure CLI-ben

Az Azure CLI-ben klasszikus beállításjegyzék frissítése, hajtsa végre a [az acr update] [ az-acr-update] parancsot, majd adja meg az új Termékváltozat a beállításjegyzékhez. Klasszikus beállításjegyzék neve a következő példában *myclassicregistry* a prémium szintű termékváltozatra frissül:

```azurecli-interactive
az acr update --name myclassicregistry --sku Premium
```

Az áttelepítés befejeződése után az alábbihoz hasonló kimenetnek kell megjelennie. Figyelje meg, hogy a `sku` van "Prémium" és a `storageAccount` van `null`, amely azt jelzi, hogy az Azure már kezeli a lemezképet tároló ehhez a beállításjegyzékhez.

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

Ha egy felügyelt beállításjegyzéket Termékváltozat, amelynek maximális kapacitása a kisebb, mint a klasszikus beállításjegyzékben méretét adja meg, az alábbihoz hasonló hibaüzenetet kap.

`Cannot update the registry SKU due to reason: Registry size 12936251113 bytes exceeds the quota value 10737418240 bytes for SKU Basic. The suggested SKU is Standard.`

Ha egy hasonló hibaüzenet jelenik meg, futtassa a [az acr update] [ az-acr-update] újra parancsot, majd adja meg, hogy a javasolt Termékváltozat, amely a következő legmagasabb szintű Termékváltozat, amely lehetővé teszi a lemezképek.

## <a name="upgrade-in-azure-portal"></a>Frissítse az Azure Portalon

Klasszikus beállításjegyzék frissítése az Azure portal használatával, ha az Azure automatikusan kiválasztja a Standard vagy prémium szintű Termékváltozat, attól függően, amelyek Termékváltozat lehetővé teszi a lemezképek. Például, ha a beállításjegyzék 100-nál kevesebb GiB képeken látható, az Azure automatikusan kiválasztja és alakítja át a klasszikus beállításjegyzékben standard (100 GB maximális).

A klasszikus beállításjegyzék frissítése az Azure portal használatával, keresse meg a tároló-beállításjegyzék **áttekintése** válassza **frissítését a felügyelt beállításjegyzékre**.

![Klasszikus beállításjegyzék frissítése gomb az Azure Portal felhasználói felületén][update-classic-01-upgrade]

Válassza ki **OK** annak ellenőrzéséhez, hogy szeretné-e frissíteni egy felügyelt beállításjegyzéket.

Az áttelepítés során a portál jelzi, hogy a tárolójegyzék **üzembe helyezési állapota** van *Updating*. Ahogy korábban említettük, `docker push` műveletek le vannak tiltva, az utolsó 10 %-a migrálás során. Ehhez nem delete vagy update mutatóval a tárfiók a klasszikus beállításjegyzékben által használt, amíg az áttelepítés van folyamatban – így eredményezhet kép sérülése.

![Klasszikus beállításjegyzék frissítése folyamatban van az Azure Portal felhasználói felületén][update-classic-03-updating]

Ha az áttelepítés befejeződött, a **üzembe helyezési állapota** azt jelzi, hogy *sikeres*, és folytathatja a normál működést a beállításjegyzék.

![Klasszikus beállításjegyzék frissítése az Azure Portal felhasználói felületén befejezési állapota][update-classic-04-updated]

## <a name="next-steps"></a>További lépések

Klasszikus beállításjegyzék egy felügyelt beállításjegyzéket, hogy frissített, miután az Azure már nem használja a tárfiókot, amelyet eredetileg támogat a klasszikus beállításjegyzékben. Költségek csökkentése érdekében fontolja meg, törlését, a storage-fiók vagy a Blob-tároló, amely tartalmazza a régi tárolólemezképek a fiókon belül.

<!-- IMAGES -->
[update-classic-01-upgrade]: ./media/container-registry-upgrade/update-classic-01-upgrade.png
[update-classic-02-confirm]: ./media/container-registry-upgrade/update-classic-02-confirm.png
[update-classic-03-updating]: ./media/container-registry-upgrade/update-classic-03-updating.png
[update-classic-04-updated]: ./media/container-registry-upgrade/update-classic-04-updated.png

<!-- LINKS - internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[azure-cli]: /cli/azure/install-azure-cli
[azure-portal]: https://portal.azure.com