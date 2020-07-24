---
title: Azure HPC cache tárolási célok frissítése
description: Az Azure HPC cache tárolási céljainak szerkesztése
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/02/2020
ms.author: v-erkel
ms.openlocfilehash: f11e12c4f30977514e04b09c7e1c3012eb7888a7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87092456"
---
# <a name="edit-storage-targets"></a>Céltárolók szerkesztése

A tárolási célpontokat eltávolíthatja vagy módosíthatja a gyorsítótár **tárolási célpontok** portálján vagy az Azure CLI használatával.

> [!TIP]
> Az [Azure HPC cache-gyorsítótárának kezelése videó](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) bemutatja, hogyan szerkesztheti a tárolási célt a Azure Portal.

## <a name="remove-a-storage-target"></a>Tárolási cél eltávolítása

### <a name="portal"></a>[Portál](#tab/azure-portal)

A tárolási cél eltávolításához jelölje ki azt a listában, majd kattintson a **Törlés** gombra.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Az [az HPC-cache Storage-Target Remove](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-remove) paranccsal törölheti a tárolási célt a gyorsítótárból.

```azurecli
$ az hpc-cache storage-target remove --resource-group cache-rg --cache-name doc-cache0629 --name blob1

{- Finished ..
  "endTime": "2020-07-09T21:45:06.1631571+00:00",
  "name": "2f95eac1-aded-4860-b19c-3f089531a7ec",
  "startTime": "2020-07-09T21:43:38.5461495+00:00",
  "status": "Succeeded"
}
```

---

Ez a művelet eltávolítja a tárolási cél társítását ezzel az Azure HPC cache-rendszerrel, de nem módosítja a háttér-tárolási rendszereket. Ha például egy Azure Blob Storage-tárolót használ, a tároló és annak tartalma továbbra is fennáll, miután törölte a gyorsítótárból. A tárolót hozzáadhatja egy másik Azure HPC-gyorsítótárhoz, hozzáadhatja újra a gyorsítótárhoz, vagy törölheti a Azure Portal.

A gyorsítótárban tárolt összes változás a tárolási cél eltávolítása előtt a háttérrendszer tárolási rendszerébe íródik. Ez a folyamat akár egy órát is igénybe vehet, ha a módosult adatmennyiségek nagy része a gyorsítótárban van.

## <a name="update-storage-targets"></a>Tárolási célok frissítése

Szerkesztheti a tárolási célokat, hogy módosítsa a tulajdonságait. A különböző tulajdonságok a különböző típusú tárolók számára szerkeszthetők:

* A blob Storage-célok esetében módosíthatja a névtér elérési útját.

* Az NFS-tárolási célok esetében a következő tulajdonságokat módosíthatja:

  * Névtér elérési útja
  * Használati modell
  * Exportálás
  * Alkönyvtár exportálása

Nem szerkesztheti a tárolási cél nevét, típusát vagy háttérbeli tárolási rendszerét (blob-tároló vagy NFS-állomásnév/IP-cím). Ha módosítania kell ezeket a tulajdonságokat, törölje a tárolási célt, és hozzon létre egy cserét az új értékkel.

A Azure Portalban láthatja, hogy mely mezők szerkeszthető a tárolási cél nevére kattintva, és megnyithatja a részleteket tartalmazó lapot. Emellett módosíthatja a tárolási célokat az Azure CLI-vel.

![az NFS-tárolási cél szerkesztési oldalának képernyőképe](media/hpc-cache-edit-storage-nfs.png)

## <a name="update-an-nfs-storage-target"></a>NFS-tárolási cél frissítése

NFS-tárolási cél esetén több tulajdonságot is frissíthet. (Tekintse meg a fenti képernyőképet egy példa szerkesztésre szolgáló oldalon.)

* **Használati modell** – a használati modell befolyásolja, hogy a gyorsítótár hogyan őrizze meg az adatokat. További információért olvassa el [a használati modell kiválasztása](hpc-cache-add-storage.md#choose-a-usage-model) című témakört.
* **Virtuális névtér elérési útja** – az az elérési út, amelyet az ügyfelek a tárolási cél csatlakoztatására használnak. A részletekért olvassa el [az összesített névtér megtervezése](hpc-cache-namespace.md) című leírást.
* **NFS-exportálási útvonal** – a tárolási rendszer a névtér elérési útján való exportálására használható.
* **Alkönyvtár elérési útja** – az Exportálás alatt lévő alkönyvtár, amely a névtér elérési útjával társítva van. Ha nem kell megadnia egy alkönyvtárat, hagyja üresen ezt a mezőt.

Minden névtér elérési útjának az Exportálás és az alkönyvtár egyedi kombinációja szükséges. Ez azt okozhatja, hogy nem tud két különböző, az ügyfél felé irányuló elérési utat pontosan ugyanarra a könyvtárra tenni a háttér-tárolási rendszeren.

### <a name="portal"></a>[Portál](#tab/azure-portal)

A módosítások végrehajtása után kattintson **az OK** gombra a tárolási cél frissítéséhez, vagy kattintson a **Mégse** gombra a módosítások elvetéséhez.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Az az [NFS-Storage-Target](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target) paranccsal módosíthatja a tárolási cél használati modelljét, a virtuális névtér elérési útját, valamint az NFS-exportálási vagy alkönyvtári értékeket.

* A használati modell módosításához használja a ``--nfs3-usage-model`` kapcsolót. Például: ``--nfs3-usage-model WRITE_WORKLOAD_15``

* Ha módosítani szeretné a névtér elérési útját, exportálja vagy exportálja az alkönyvtárat, használja a ``--junction`` kapcsolót.

  A ``--junction`` paraméter a következő értékeket használja:

  * ``namespace-path``– Az ügyfélre irányuló virtuális fájl elérési útja
  * ``nfs-export``– A tárolásirendszer exportálja az ügyfél felé irányuló elérési úttal való hozzárendeléshez
  * ``target-path``(nem kötelező) – szükség esetén az Exportálás alkönyvtára

  Például: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

A gyorsítótár neve, a tárolási cél neve és az erőforráscsoport szükséges az összes frissítési parancsban.

Példa a parancsra: <!-- having problem testing this -->

```azurecli
az hpc-cache nfs-storage-target update --cache-name mycache \
    --name rivernfs0 --resource-group doc-rg0619 \
    --nfs3-usage-model READ_HEAVY_INFREQ
```

Ha a gyorsítótár leáll, vagy nem kifogástalan állapotban van, a frissítés a gyorsítótár Kifogástalan állapotba helyezése után lesz érvényes.

---

## <a name="update-an-azure-blob-storage-target"></a>Azure Blob Storage-tároló frissítése

BLOB Storage-tároló esetén módosíthatja a virtuális névtér elérési útját.

### <a name="portal"></a>[Portál](#tab/azure-portal)

A blob Storage-tároló részletek lapja lehetővé teszi a virtuális névtér elérési útjának módosítását.

![a blob Storage-tároló szerkesztési oldalának képernyőképe](media/hpc-cache-edit-storage-blob.png)

Ha elkészült, kattintson **az OK** gombra a tárolási cél frissítéséhez, vagy kattintson a **Mégse** gombra a módosítások elvetéséhez.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

A cél névtérbeli elérési útjának frissítéséhez használja [az az HPC-cache blob-Storage-Target Update](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-update) paranccsal.

```azurecli
az hpc-cache blob-storage-target update --cache-name cache-name --name target-name \
    --resource-group rg --storage-account "/subscriptions/<subscription_ID>/resourceGroups/erinazcli/providers/Microsoft.Storage/storageAccounts/rg"  \
    --container-name "container-name" --virtual-namespace-path "/new-path"
```

Ha a gyorsítótár leáll, vagy nem kifogástalan állapotban van, a frissítés a gyorsítótár Kifogástalan állapotba helyezése után fog vonatkozni.

---

## <a name="next-steps"></a>További lépések

* A beállításokkal kapcsolatos további információkért olvassa el a [tárolási célok hozzáadása](hpc-cache-add-storage.md) című témakört.
* A virtuális elérési utak használatával kapcsolatos további tippekért olvassa el [az összesített névtér megtervezése](hpc-cache-namespace.md) című cikkét.
