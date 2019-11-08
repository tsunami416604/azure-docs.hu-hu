---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 11/05/2019
ms.author: alkohli
ms.openlocfilehash: 16647b6a13e64073ab570d36a8a380d0e36bd855
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73799881"
---
- Ne másolja a fájlokat közvetlenül az előlétrehozott megosztások egyikére. Létre kell hoznia egy mappát a megosztás alatt, majd át kell másolnia a fájlokat a mappába.
- A *StorageAccount_BlockBlob* és a *StorageAccount_PageBlob* alá tartozó mappa egy tároló. A tárolók például *StorageAccount_BlockBlob/tárolóként* és *StorageAccount_PageBlob/tárolóként*jönnek létre.
- A közvetlenül a *StorageAccount_AzureFiles* alatt létrehozott mappákat egy Azure-fájlmegosztás fordítja le.
- Ha van egy meglévő Azure-objektum (például egy blob vagy egy fájl) a felhőben az éppen másolt objektum nevével, Data Box felülírja a fájlt a felhőben.
- A rendszer a *StorageAccount_BlockBlob* és a *StorageAccount_PageBlob* -megosztásba írt összes fájlt feltöltötte egy blokk-blobként és egy oldal blobként.
- Az Azure Blob Storage nem támogatja a címtárakat. Ha a *StorageAccount_BlockBlob* mappában hoz létre egy mappát, a virtuális mappák a blob nevében jönnek létre. Azure Files esetén a rendszer karbantartja a tényleges címtár-struktúrát.
- A *StorageAccount_BlockBlob* és a *StorageAccount_PageBlob* mappák alatt létrehozott összes üres címtár-hierarchia (fájl nélkül) nincs feltöltve.
- Ha bármilyen hiba merül fel az Azure-ba történő adatfeltöltés során, a rendszer egy naplófájlt hoz létre a célként szolgáló Storage-fiókban. A hibanapló elérési útja akkor érhető el, ha a feltöltés befejeződött, és a javítási művelet végrehajtásához tekintse át a naplót. Ne töröljön adatok a forrásból a feltöltött adatok ellenőrzése nélkül.
- A fájl-metaadatok és az NTFS-engedélyek nem őrződnek meg, amikor a rendszer feltölti az adatokat Azure Filesba. A fájlok *utolsó módosított attribútuma* például nem lesz megtartva az adatmásoláskor.
