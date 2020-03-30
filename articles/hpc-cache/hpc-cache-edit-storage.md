---
title: Az Azure HPC-gyorsítótár tárolási céljainak frissítése
description: Az Azure HPC-gyorsítótár tárolási céljainak szerkesztése
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 1/08/2020
ms.author: rohogue
ms.openlocfilehash: 5635bfc6ea5faea41b125037c76c0b8635e0f528
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75867009"
---
# <a name="edit-storage-targets"></a>Céltárolók szerkesztése

A tárolócélt eltávolíthatja vagy módosíthatja a gyorsítótár **tárolócélok** lapjáról.

## <a name="remove-a-storage-target"></a>Tárolási cél eltávolítása

Tárolócél eltávolításához jelölje ki azt a listában, és kattintson a **Törlés** gombra.

Ez a művelet eltávolítja a tárolási cél társítása ezzel az Azure HPC cache rendszer, de nem módosítja a háttér-tároló rendszer. Ha például egy Azure Blob-tárolót használt, a tároló és annak tartalma továbbra is fennáll, miután törölte a gyorsítótárból. Hozzáadhatja a tárolót egy másik Azure HPC-gyorsítótárhoz, újra hozzáadhatja a gyorsítótárhoz, vagy törölheti az Azure Portalon.

A gyorsítótárban tárolt fájlmódosításokat a rendszer a tárolócél eltávolítása előtt a háttértároló rendszerbe írja. Ez a folyamat egy órát vagy többet is igénybe vehet, ha sok megváltozott adat van a gyorsítótárban.

## <a name="update-storage-targets"></a>Tárcélok frissítése

A tárolási célok szerkesztésével módosíthatja azok egy részét. A különböző tulajdonságok különböző típusú tárolókesetében szerkeszthetők:

* A Blob storage-tárolók esetében módosíthatja a névtér elérési útját.

* NFS-tárolócélok esetén módosíthatja a következő tulajdonságokat:

  * Névtér elérési útja
  * Használati modell
  * Exportálás
  * Alkönyvtár exportálása

A tárolási cél neve, típusa vagy háttértároló rendszere (Blob-tároló vagy NFS-állomásnév/IP-cím) nem szerkeszthető. Ha módosítania kell ezeket a tulajdonságokat, törölje a tárolási célt, és hozzon létre egy cserét az új értékkel.

A tárolási cél módosításához kattintson a tárolócél nevére a részleteket tartalmazó lap megnyitásához. A lap egyes mezői szerkeszthetők.

![képernyőkép egy NFS-tárolócél szerkesztési lapjáról](media/hpc-cache-edit-storage-nfs.png)

## <a name="update-an-nfs-storage-target"></a>NFS-tárolócéljának frissítése

NFS-tárolócél esetén több tulajdonságot is frissíthet. (Lásd a fenti képernyőkép egy példa szerkesztési oldalon.)

* **Használati modell** – A használati modell befolyásolja, hogy a gyorsítótár hogyan őrzi meg az adatokat. Olvassa [el A használati modell kiválasztása](hpc-cache-add-storage.md#choose-a-usage-model) további információkért.
* **Virtuális névtér elérési útja** – Az az elérési út, amelyet az ügyfelek a tárolási cél csatlakoztatására használnak. Olvassa [el az összesített névtér megtervezése](hpc-cache-namespace.md) a részletekért.
* **NFS-exportálási útvonal** – A névtér elérési útvonalához használandó tárolórendszer-exportálás.
* **Alkönyvtár elérési útja** – A névtér elérési úthoz társítani való alkönyvtár (az exportálás alatt). Ha nem kell alkönyvtárat megadnia, hagyja üresen ezt a mezőt.

Minden névtérelérési útvonalhoz az exportálás és az alkönyvtár egyedi kombinációjára van szükség. Ez azt, hogy nem lehet két különböző ügyfél felé néző elérési utat a háttér-tároló rendszer ugyanazon könyvtárhoz.

A módosítások elvégzése után kattintson az **OK** gombra a tárolási cél frissítéséhez, vagy kattintson a **Mégse gombra** a módosítások elvetéséhez.

## <a name="update-an-azure-blob-storage-target"></a>Azure Blob-tárolócél frissítése

A Blob-tároló cél részletes lapja lehetővé teszi a virtuális névtér elérési útjának módosítását.

![képernyőkép egy blobstorage-tároló hoz a szerkesztési lapról](media/hpc-cache-edit-storage-blob.png)

Ha végzett, kattintson az **OK** gombra a tárolási cél frissítéséhez, vagy kattintson a **Mégse gombra** a módosítások elvetéséhez.

## <a name="next-steps"></a>További lépések

* A [beállításokról](hpc-cache-add-storage.md) a Tárolási célok hozzáadása című részt című, további tudnivalókat.
* Olvassa [el az összesített névtér megtervezése](hpc-cache-namespace.md) további tippeket a virtuális elérési utak használatával kapcsolatban.
