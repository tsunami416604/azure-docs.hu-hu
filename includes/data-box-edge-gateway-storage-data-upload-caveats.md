---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/14/2019
ms.author: alkohli
ms.openlocfilehash: 8bc4b78d262cf5f30076e90b40b92c4f3237924a
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2019
ms.locfileid: "56333797"
---
Alábbi korlátozásokkal adatokra vonatkoznak, ahogy azt az Azure-bA helyezi át.

- Javasoljuk, hogy több eszköz nem kell írni ugyanazt a tárolót.
- Ha egy meglévő Azure objektum (például egy blobba vagy egy fájlt) ezzel a névvel, az objektum, amely a másolásakor a felhőben, az eszköz felülírja a fájl a felhőben.
- Egy üres könyvtár-hierarchia (nélküli fájlokat) a megosztás mappák létrehozása a blob-tárolók nem van feltöltve.
- Nagy fájlok javasoljuk a robocopy használni, mert újra megpróbálja a másolási művelet időszakos hibák.
- Ha a társított Azure storage-tároló megosztás feltölti a blobok, amelyek nem egyeznek meg van határozva a megosztás létrehozásakor blobok típusát, majd az ilyen blobok nem frissülnek. Például létrehozhat egy block blob megosztás az eszközön. A megosztás társítása meglévő felhőalapú tároló, amely rendelkezik a lapblobokat. Frissítse a fájlok letöltéséhez használt megosztást. Módosítsa a frissített fájlokat már lapblobként a felhőben tárolt némelyike. Látni fogja feltölteni a hibák.
