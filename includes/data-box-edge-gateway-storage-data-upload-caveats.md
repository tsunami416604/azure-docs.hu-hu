---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/07/2019
ms.author: alkohli
ms.openlocfilehash: ad7ceffed61665a729d4391b5f0ff2935375c253
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55967299"
---
Alábbi korlátozásokkal adatokra vonatkoznak, ahogy azt az Azure-bA helyezi át.

- Javasoljuk, hogy több eszköz nem kell írni ugyanazt a tárolót.
- Ha egy meglévő Azure objektum (például egy blobba vagy egy fájlt) ezzel a névvel, az objektum, amely a másolásakor a felhőben, az eszköz felülírja a fájl a felhőben.
- Egy üres könyvtár-hierarchia (nélküli fájlokat) a megosztás mappák létrehozása a blob-tárolók nem van feltöltve.
- Nagy fájlok javasoljuk a robocopy használni, mert újra megpróbálja a másolási művelet időszakos hibák.
