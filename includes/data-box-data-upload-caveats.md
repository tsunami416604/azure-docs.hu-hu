---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 11/05/2019
ms.author: alkohli
ms.openlocfilehash: 16647b6a13e64073ab570d36a8a380d0e36bd855
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73799881"
---
- Ne másolja a fájlokat közvetlenül az előre létrehozott megosztások egyikébe sem. Létre kell hoznia egy mappát a megosztás alatt, majd fájlokat kell másolnia a mappába.
- A *StorageAccount_BlockBlob* és *StorageAccount_PageBlob* alatti mappa egy tároló. Például a tárolók *StorageAccount_BlockBlob/tárolóként* és *StorageAccount_PageBlob/tárolóként*jönnek létre.
- A közvetlenül *StorageAccount_AzureFiles* alatt létrehozott mappák at egy Azure-fájlmegosztásba fordítják.
- Ha van egy meglévő Azure-objektum (például egy blob vagy egy fájl) a felhőben, amelynek neve megegyezik a másolt objektum nevével, a Data Box felülírja a fájlt a felhőben.
- A *StorageAccount_BlockBlob* és *StorageAccount_PageBlob* megosztásokba írt összes fájl blokkblobként, illetve lapblobként kerül feltöltésre.
- Az Azure blob storage nem támogatja a könyvtárakat. Ha létrehoz egy mappát a *StorageAccount_BlockBlob* mappában, akkor a virtuális mappák a blob nevében jönnek létre. Az Azure Files esetében a tényleges könyvtárstruktúra megmarad.
- A *StorageAccount_BlockBlob* és *StorageAccount_PageBlob* mappák alatt létrehozott üres könyvtárhierarchia (fájlok nélkül) nem kerül feltöltésre.
- Ha bármilyen hiba történik az adatok Azure-ba való feltöltésekénél, a céltárfiókban hibanapló jön létre. A hibanapló elérési útja akkor érhető el, ha a feltöltés befejeződött, és a javítási művelet elvégzéséhez áttekintheti a naplót. Ne törölje az adatokat a forrásból a feltöltött adatok ellenőrzése nélkül.
- A fájl metaadatok és az NTFS-engedélyek nem őrződnek meg, amikor az adatok at feltöltik az Azure Files-ba. Például a fájlok *Utolsó módosítás attribútuma* nem marad meg az adatok másolásakor.
