---
title: Az Azure HPC cache előzetes adatgyűjtése – msrsync
description: Az msrsync használata az Azure HPC cache-ben lévő blob Storage-tárolóba való áthelyezéshez
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: rohogue
ms.openlocfilehash: 6eac6c367be42021a4654f85c8f4ec980c9f6925
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255281"
---
# <a name="azure-hpc-cache-preview-data-ingest---msrsync-method"></a>Azure HPC cache (előzetes verzió) adatfeldolgozás – msrsync metódus

Ez a cikk részletes útmutatást nyújt a ``msrsync`` segédprogram használatáról az Azure-beli blob Storage-tárolóba való adatmásoláshoz az Azure HPC cache használatával.

Ha többet szeretne megtudni arról, hogy az Azure HPC gyorsítótára hogyan helyezi át az adatátvitelt a blob Storage-ba, olvassa el az [adatáthelyezés Azure-beli blob Storage](hpc-cache-ingest.md)-ba Azure

A ``msrsync`` eszköz segítségével az Azure HPC cache háttérbeli tárolási céljára helyezheti át az adatátvitelt. Ez az eszköz úgy lett kialakítva, hogy optimalizálja a sávszélesség-használatot több párhuzamos @no__t – 0 folyamat futtatásával. A GitHubról https://github.com/jbd/msrsync címen érhető el.

@no__t – 0 a forrás könyvtárat külön "gyűjtőre" bontja, majd az egyes gyűjtők ``rsync`` folyamatait futtatja.

A négy Magos virtuális géppel végzett előzetes tesztelés az 64-es folyamatok használatakor a legjobb hatékonyságot mutatja. A 64-ig terjedő folyamatok számának beállításához használja a ``-p`` ``msrsync`` kapcsolót.

Vegye figyelembe, hogy a ``msrsync`` csak helyi kötetek és azok között tud írni. A forrásnak és a célhelynek a parancs kiadásához használt munkaállomáson helyi csatlakoztatásként kell elérhetőnek lennie.

Kövesse ezeket az utasításokat a ``msrsync`` használatára az Azure Blob Storage Azure HPC cache-vel való feltöltéséhez:

1. A ``msrsync`` telepítése és előfeltételei (``rsync`` és Python 2,6 vagy újabb)
1. A másolandó fájlok és könyvtárak teljes számának meghatározása.

   Használja például a ``prime.py`` segédprogramot a következő argumentumokkal: ```prime.py --directory /path/to/some/directory``` (az <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py> letöltésével érhető el).

   Ha nem a ``prime.py`` értéket használja, kiszámíthatja a GNU ``find`` eszközzel rendelkező elemek számát a következőképpen:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Az elemek számának felosztása a 64 alapján az elemek számának megállapítása folyamatban. Ezt a számot a ``-f`` kapcsolóval használva állíthatja be a gyűjtők méretét a parancs futtatásakor.

1. Adja ki a ``msrsync`` parancsot a fájlok másolásához:

   ```bash
   msrsync -P --stats -p64 -f<ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Ez a parancs például úgy lett kialakítva, hogy 11 000-es fájlokat helyezzen át a 64 folyamatokban a/test/Source-repository-ről a/mnt/hpccache/repository-re:

   ``mrsync -P --stats -p64 -f170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/hpccache/repository``
