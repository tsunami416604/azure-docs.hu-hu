---
title: Azure HPC cache tárolási célok frissítése
description: Az Azure HPC cache tárolási céljainak szerkesztése
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: rohogue
ms.openlocfilehash: 115e75c0149a35104d9c3696710bf8231a98743d
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74168513"
---
# <a name="edit-storage-targets"></a>Céltárolók szerkesztése

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
