---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/21/2019
ms.author: alkohli
ms.openlocfilehash: 8c87e14071b3bb40421ab655c172df739570e295
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56660146"
---
Alábbi korlátozásokkal adatokra vonatkoznak, ahogy azt az Azure-bA helyezi át.

- Javasoljuk, hogy több eszköz nem kell írni ugyanazt a tárolót.
- Ha egy meglévő Azure objektum (például egy blobba vagy egy fájlt) ezzel a névvel, az objektum, amely a másolásakor a felhőben, az eszköz felülírja a fájl a felhőben.
- Egy üres könyvtár-hierarchia (nélküli fájlokat) a megosztás mappák létrehozása a blob-tárolók nem van feltöltve.
- Húzza át az adatokat másolja, és dobja el a fájlkezelő vagy az parancssor segítségével. Ha éppen másolt fájlok összesített mérete 10 GB-nál nagyobb, javasoljuk, például Robocopy vagy rsync tömeges másolási programot használ. A tömeges másolási eszközök ismételje meg a másolási művelet időszakos hibák, és nagyobb rugalmasság biztosítása érdekében.
- Ha a társított Azure storage-tároló megosztás feltölti a blobok, amelyek nem egyeznek meg van határozva a megosztás létrehozásakor blobok típusát, majd az ilyen blobok nem frissülnek. Például létrehozhat egy block blob megosztás az eszközön. A megosztás társítása meglévő felhőalapú tároló, amely rendelkezik a lapblobokat. Frissítse a fájlok letöltéséhez használt megosztást. Módosítsa a frissített fájlokat már lapblobként a felhőben tárolt némelyike. Látni fogja feltölteni a hibák.
