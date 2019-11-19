---
title: Azure HPC cache-adatfeldolgozás – msrsync
description: Az msrsync használata az Azure HPC cache-ben lévő blob Storage-tárolóba való áthelyezéshez
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 4f8863d706d623d613ac156cf202c3b7b12f2ae0
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74168420"
---
# <a name="azure-hpc-cache-data-ingest---msrsync-method"></a>Azure HPC cache-adatfeldolgozás – msrsync metódus

Ez a cikk részletes útmutatást nyújt a ``msrsync`` segédprogram használatával történő adatmásoláshoz az Azure-beli blob Storage-tárolóba az Azure HPC cache használatával történő használathoz.

Ha többet szeretne megtudni arról, hogy az Azure HPC gyorsítótára hogyan helyezi át az adatátvitelt a blob Storage-ba, olvassa el az [Azure Blob Storage](hpc-cache-ingest.md)-ba

Az ``msrsync`` eszköz használatával az Azure HPC cache-re helyezheti át az adatháttérbeli tárolási célhelyre. Ez az eszköz úgy lett kialakítva, hogy optimalizálja a sávszélesség-használatot több párhuzamos ``rsync`` folyamat futtatásával. A GitHubról https://github.com/jbd/msrsynccímen érhető el.

``msrsync`` a forrás könyvtárat külön "gyűjtőre" bontja, majd az egyes gyűjtők egyéni ``rsync`` folyamatait futtatja.

A négy Magos virtuális géppel végzett előzetes tesztelés az 64-es folyamatok használatakor a legjobb hatékonyságot mutatja. A 64-es folyamatok számának megadásához használja a ``-p`` ``msrsync`` lehetőséget.

Vegye figyelembe, hogy a ``msrsync`` csak helyi kötetektől tud írni. A forrásnak és a célhelynek a parancs kiadásához használt munkaállomáson helyi csatlakoztatásként kell elérhetőnek lennie.

Kövesse ezeket az utasításokat az Azure Blob Storage Azure HPC cache-vel való feltöltéséhez ``msrsync`` használatával:

1. ``msrsync`` telepítése és előfeltételei (``rsync`` és Python 2,6 vagy újabb)
1. A másolandó fájlok és könyvtárak teljes számának meghatározása.

   Használja például a segédprogram ``prime.py`` argumentumait ```prime.py --directory /path/to/some/directory``` (a <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py>letöltésével érhető el).

   Ha nem használja a ``prime.py``, a következő módon számíthatja ki az elemek számát a GNU ``find`` eszközzel:

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
