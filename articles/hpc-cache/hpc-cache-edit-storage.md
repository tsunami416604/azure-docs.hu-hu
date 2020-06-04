---
title: Azure HPC cache tárolási célok frissítése
description: Az Azure HPC cache tárolási céljainak szerkesztése
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 1/08/2020
ms.author: v-erkel
ms.openlocfilehash: 9bce4ec383b8ac7efaa9b00237044424ffb58fc0
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84344254"
---
# <a name="edit-storage-targets"></a>Céltárolók szerkesztése

A tárolási célokat a gyorsítótár **tárolási célok** lapjáról távolíthatja el vagy módosíthatja.

> [!TIP]
> Az [Azure HPC cache-gyorsítótárának kezelése videó](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) bemutatja, hogyan szerkeszthető egy tárolási cél.

## <a name="remove-a-storage-target"></a>Tárolási cél eltávolítása

A tárolási cél eltávolításához jelölje ki azt a listában, majd kattintson a **Törlés** gombra.

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

A tárolási cél módosításához kattintson a tárolási cél nevére a Részletek lap megnyitásához. A lap egyes mezői szerkeszthető.

![az NFS-tárolási cél szerkesztési oldalának képernyőképe](media/hpc-cache-edit-storage-nfs.png)

## <a name="update-an-nfs-storage-target"></a>NFS-tárolási cél frissítése

NFS-tárolási cél esetén több tulajdonságot is frissíthet. (Tekintse meg a fenti képernyőképet egy példa szerkesztésre szolgáló oldalon.)

* **Használati modell** – a használati modell befolyásolja, hogy a gyorsítótár hogyan őrizze meg az adatokat. További információért olvassa el [a használati modell kiválasztása](hpc-cache-add-storage.md#choose-a-usage-model) című témakört.
* **Virtuális névtér elérési útja** – az az elérési út, amelyet az ügyfelek a tárolási cél csatlakoztatására használnak. A részletekért olvassa el [az összesített névtér megtervezése](hpc-cache-namespace.md) című leírást.
* **NFS-exportálási útvonal** – a tárolási rendszer a névtér elérési útján való exportálására használható.
* **Alkönyvtár elérési útja** – az Exportálás alatt lévő alkönyvtár, amely a névtér elérési útjával társítva van. Ha nem kell megadnia egy alkönyvtárat, hagyja üresen ezt a mezőt.

Minden névtér elérési útjának az Exportálás és az alkönyvtár egyedi kombinációja szükséges. Ez azt okozhatja, hogy nem tud két különböző, az ügyfél felé irányuló elérési utat pontosan ugyanarra a könyvtárra tenni a háttér-tárolási rendszeren.

A módosítások végrehajtása után kattintson **az OK** gombra a tárolási cél frissítéséhez, vagy kattintson a **Mégse** gombra a módosítások elvetéséhez.

## <a name="update-an-azure-blob-storage-target"></a>Azure Blob Storage-tároló frissítése

A blob Storage-tároló részletek lapja lehetővé teszi a virtuális névtér elérési útjának módosítását.

![a blob Storage-tároló szerkesztési oldalának képernyőképe](media/hpc-cache-edit-storage-blob.png)

Ha elkészült, kattintson **az OK** gombra a tárolási cél frissítéséhez, vagy kattintson a **Mégse** gombra a módosítások elvetéséhez.

## <a name="next-steps"></a>Következő lépések

* A beállításokkal kapcsolatos további információkért olvassa el a [tárolási célok hozzáadása](hpc-cache-add-storage.md) című témakört.
* A virtuális elérési utak használatával kapcsolatos további tippekért olvassa el [az összesített névtér megtervezése](hpc-cache-namespace.md) című cikkét.
