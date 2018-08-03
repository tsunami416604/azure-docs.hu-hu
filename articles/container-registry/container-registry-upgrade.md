---
title: A klasszikus Azure-tároló beállításjegyzékének frissítése
description: Kihasználhatja a kibontott szolgáltatáskészletére vonatkozóan a Basic, Standard és prémium szintű tároló-beállításjegyzékek felügyelt a nem felügyelt klasszikus tárolóregisztrációs adatbázis frissítésével.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 03/15/2018
ms.author: marsma
ms.openlocfilehash: 562bd8da54605986e95d8105782ce7ebb9b359ea
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39432388"
---
# <a name="upgrade-a-classic-container-registry"></a>Klasszikus tároló-beállításjegyzék frissítése

Többféle szolgáltatáscsomagban érhető el az Azure Container Registry (ACR) [termékváltozatok néven](container-registry-skus.md). ACR kezdeti verziójában érhető el egy egyetlen Termékváltozat klasszikus, amely nem rendelkezik a Basic, Standard és prémium szintű csomag beépített szolgáltatásai számos funkciót (összefoglaló néven *felügyelt* beállításjegyzékek). Ez a cikk részletesen a nem felügyelt klasszikus beállításjegyzékben áttelepítése a felügyelt termékváltozatok közül, így azok továbbfejlesztett szolgáltatáskészlet előnyeit is.

## <a name="why-upgrade"></a>Miért érdemes frissíteni?

Miatt a klasszikus nem felügyelt beállításjegyzékek korlátozott képességeit javasoljuk, hogy az összes klasszikus beállításjegyzékek frissített alapszintű, Standard vagy prémium szintű felügyelt beállításjegyzékek lehet. Ezek a magasabb szintű termékváltozatok a beállításjegyzék így integrálható az Azure funkcióit.

Adja meg a felügyelt beállításjegyzékek:

* Az Azure Active Directory-integráció [egyéni bejelentkezési](container-registry-authentication.md#individual-login-with-azure-ad)
* Kép és a címke törlése támogatása
* [Georeplikáció](container-registry-geo-replication.md)
* [Webhooks](container-registry-webhook.md)

Klasszikus beállításjegyzék legfontosabb, a storage-fiókot, hogy az Azure automatikusan látja el az Azure-előfizetésben a beállításjegyzék létrehozásakor függ. Ezzel szemben a Basic, Standard és prémium szintű termékváltozatok kihasználhatja az Azure [speciális tárolási szolgáltatások](container-registry-storage.md) átlátható módon kezeli az Ön számára a lemezképek tárolását. Egy önálló tárfiókot a saját előfizetése nem jön létre.

Felügyelt beállításjegyzék tárterületét az alábbi előnyöket nyújtja:

* Tárolórendszerkép [titkosítása](container-registry-storage.md#encryption-at-rest).
* Rendszerképek tárolása [georedundáns tárolás](container-registry-storage.md#geo-redundant-storage)modulhoz, biztonsági mentés, a képek a többrégiós replikációval.
* Lehetővé teszi, hogy szabadon [termékváltozatok közötti áthelyezése](container-registry-skus.md#changing-skus), ha úgy dönt, hogy egy magasabb szintű Termékváltozat, amely lehetővé teszi, hogy nagyobb átviteli sebességet. Minden SKU, az ACR megfelel az átviteli sebességet megkövetelő, növelje az igényeinek.
* A beállításjegyzék és a tárolás egységes biztonsági modellt biztosít egyszerűsített rights management. Kezelheti az engedélyeket, csak az a container registry esetében nem kell külön tárfiókra vonatkozó engedélyeket is tud kezelni.

Az ACR képtárolás a további részletekért lásd: [képtárolás tárolót az Azure Container Registry](container-registry-storage.md).

## <a name="migration-considerations"></a>Az áttelepítés szempontjai

Klasszikus beállításjegyzék módosítja egy felügyelt beállításjegyzéket, amikor az Azure egy tárfiókba, Azure által kezelt kell másolnia az összes meglévő tárolórendszerképek az ACR-létrehozott storage-fiókból az előfizetésében. A beállításjegyzék méretétől függően a folyamat eltarthat néhány percig akár néhány órát.

A konvertálás során minden `docker push` műveletek le vannak tiltva, amíg `docker pull` továbbra is működik.

Nem törölhető és nem módosíthatják a tárfiók a klasszikus beállításjegyzékben biztonsági a konvertálás során a tartalmát. Ezzel a tárolólemezképek sérülését eredményezheti.

Az áttelepítés befejezése után a ACR már használja a tárfiókot az előfizetésében, amelyet eredetileg biztonsági a klasszikus beállításjegyzékben. Miután meggyőződött arról, hogy az áttelepítés sikeres volt-e, vegye figyelembe a költségek csökkentése érdekében a tárfiók törlése.

>[!IMPORTANT]
> A felügyelt termékváltozatok egyik klasszikusból frissítése egy **egyirányú folyamat**. Klasszikus beállításjegyzék alapszintű, Standard vagy prémium konvertált, nem térhet vissza a klasszikus. Azonban szabadon áthelyezheti a beállításjegyzék elegendő kapacitással a felügyelt termékváltozatok között.

## <a name="how-to-upgrade"></a>Frissítése

Egy nem felügyelt klasszikus beállításjegyzék egy a felügyelt termékváltozatok számos módon frissítheti. A következő szakaszban ismertetünk folyamatát a [Azure CLI-vel] [ azure-cli] és a [az Azure portal][azure-portal].

## <a name="upgrade-in-azure-cli"></a>Frissítés az Azure CLI-ben

Az Azure CLI-ben klasszikus beállításjegyzék frissítése, hajtsa végre a [az acr update] [ az-acr-update] parancsot, majd adja meg az új Termékváltozat a beállításjegyzékhez. Klasszikus beállításjegyzék neve a következő példában *myclassicregistry* a prémium szintű termékváltozatra frissül:

```azurecli-interactive
az acr update --name myclassicregistry --sku Premium
```

Az áttelepítés befejeződése után az alábbihoz hasonló kimenetnek kell megjelennie. Figyelje meg, hogy a `sku` van "Prémium" és a `storageAccount` "NULL" jelzi, hogy az Azure már kezeli a lemezképet tároló ehhez a beállításjegyzékhez.

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

Klasszikus beállításjegyzék frissítése az Azure portal használatával, ha az Azure automatikusan kiválasztja a legalacsonyabb szintű Termékváltozat, amely lehetővé teszi a lemezképek. Például, ha a beállításjegyzék a rendszerképek 12 GiB tartalmaz, az Azure automatikusan kiválasztja és alakítja át a klasszikus beállításjegyzékben standard (100 GB maximális).

A klasszikus beállításjegyzék frissítése az Azure portal használatával, keresse meg a tároló-beállításjegyzék **áttekintése** válassza **frissítését a felügyelt beállításjegyzékre**.

![Klasszikus beállításjegyzék frissítése gomb az Azure Portal felhasználói felületén][update-classic-01-upgrade]

Válassza ki **OK** annak ellenőrzéséhez, hogy szeretné-e frissíteni egy felügyelt beállításjegyzéket.

![Klasszikus beállításjegyzék frissítése az Azure Portal felhasználói felületén megerősítése][update-classic-02-confirm]

Az áttelepítés során a portál jelzi, hogy a tárolójegyzék **üzembe helyezési állapota** van *Updating*. Ahogy korábban említettük, `docker push` műveletek le vannak tiltva, az áttelepítés során, és nem kell törölnie, vagy frissítés a tárfiók a klasszikus beállításjegyzékben által használt, amíg az áttelepítés van folyamatban – így kép sérülés eredményezhet.

![Klasszikus beállításjegyzék frissítése folyamatban van az Azure Portal felhasználói felületén][update-classic-03-updating]

Ha az áttelepítés befejeződött, a **üzembe helyezési állapota** azt jelzi, hogy *sikeres*, és ismét `docker push` a tárolójegyzékbe.

![Klasszikus beállításjegyzék frissítése az Azure Portal felhasználói felületén befejezési állapota][update-classic-04-updated]

## <a name="next-steps"></a>További lépések

Klasszikus beállításjegyzék alapszintű, Standard vagy prémium már frissített, miután az Azure már nem használja a storage-fiókot, amelyet eredetileg támogat a klasszikus beállításjegyzékben. Költségek csökkentése érdekében fontolja meg, törlését, a storage-fiók vagy a Blob-tároló, amely tartalmazza a régi tárolólemezképek a fiókon belül.

<!-- IMAGES -->
[update-classic-01-upgrade]: ./media/container-registry-upgrade\update-classic-01-upgrade.png
[update-classic-02-confirm]: ./media/container-registry-upgrade\update-classic-02-confirm.png
[update-classic-03-updating]: ./media/container-registry-upgrade\update-classic-03-updating.png
[update-classic-04-updated]: ./media/container-registry-upgrade\update-classic-04-updated.png

<!-- LINKS - internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[azure-cli]: /cli/azure/install-azure-cli
[azure-portal]: https://portal.azure.com