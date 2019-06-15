---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: b159ec8620fa8c93e4917f73be9b9898e1b4fbcc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66244581"
---
- Ne másolja a fájlokat közvetlenül a precreated megosztások bármelyikét a. Hozzon létre egy mappát a megosztást, és másolja fájlok mappájából kell.
- Egy mappát a *StorageAccount_BlockBlob* és *StorageAccount_PageBlob* tárolója. Például jönnek létre tárolók *StorageAccount_BlockBlob/tároló* és *StorageAccount_PageBlob/tároló*.
- Minden közvetlenül alatt létrehozott mappa *StorageAccount_AzureFiles* lefordítását egy Azure-fájlmegosztást.
- Ha egy meglévő Azure objektum (például egy blobba vagy egy fájlt) ezzel a névvel, az objektum, amely a másolásakor a felhőben, a Data Box felülírja a fájl a felhőben.
- Minden fájl kerülnek *StorageAccount_BlockBlob* és *StorageAccount_PageBlob* megosztások töltenek fel a blokkblobok és lapblobok jelölik.
- Az Azure blob storage nem támogatja a címtárakat. Ha létrehoz egy mappát a *StorageAccount_BlockBlob* mappát, majd virtuális mappák jönnek létre a blob nevében. Az Azure Files között a tényleges könyvtárstruktúrát változatlan marad.
- Bármely üres könyvtár-hierarchia (nélküli fájlok) alatt létrehozott *StorageAccount_BlockBlob* és *StorageAccount_PageBlob* mappák nincs feltöltve.
- Ha bármilyen hiba merül fel, ha az adatok feltöltése az Azure-ba, hibanaplót a célként megadott tárfiók létrejön. Az elérési útját a hibanapló érhető el, ha a feltöltés befejeződött, és a naplóban korrekciós műveletek végrehajtására. Ne törölje adatokat a forrásból a feltöltött adatok ellenőrzése nélkül.
