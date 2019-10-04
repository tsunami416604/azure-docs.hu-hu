---
title: Az Azure HPC cache előzetes adatgyűjtése – msrsync
description: Az msrsync használata az Azure HPC cache-ben lévő blob Storage-tárolóba való áthelyezéshez
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: v-erkell
ms.openlocfilehash: 265ec55a6e013a37cf963b6256e900c070311f72
ms.sourcegitcommit: a19bee057c57cd2c2cd23126ac862bd8f89f50f5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/23/2019
ms.locfileid: "71180938"
---
# <a name="azure-hpc-cache-preview-data-ingest---msrsync-method"></a>Azure HPC cache (előzetes verzió) adatfeldolgozás – msrsync metódus

Ez a cikk részletes útmutatást nyújt a ``msrsync`` segédprogram használatával az Azure-beli blob Storage-tárolóba való adatmásoláshoz az Azure HPC cache szolgáltatással való használathoz.

Ha többet szeretne megtudni arról, hogy az Azure HPC gyorsítótára hogyan helyezi át az adatátvitelt a blob Storage-ba, olvassa el az [adatáthelyezés Azure-beli blob Storage](hpc-cache-ingest.md)-ba Azure

Az ``msrsync`` eszköz használatával az Azure HPC cache-re helyezheti át az adatháttérbeli tárolási célhelyre. Ez az eszköz úgy lett kialakítva, hogy több párhuzamos ``rsync`` folyamat futtatásával optimalizálja a sávszélesség-használatot. A GitHubon https://github.com/jbd/msrsync érhető el.

``msrsync``elkülöníti a forrás könyvtárat külön "gyűjtő" értékre, majd minden ``rsync`` gyűjtőn futtatja az egyes folyamatokat.

A négy Magos virtuális géppel végzett előzetes tesztelés az 64-es folyamatok használatakor a legjobb hatékonyságot mutatja. A következő ``-p`` beállítással állíthatja be a folyamatok számát 64-re. ``msrsync``

Vegye figyelembe ``msrsync`` , hogy csak a helyi kötetek és a-ból tud írni. A forrásnak és a célhelynek a parancs kiadásához használt munkaállomáson helyi csatlakoztatásként kell elérhetőnek lennie.

Kövesse ezeket az utasításokat az ``msrsync`` Azure Blob Storage Azure HPC cache használatával történő feltöltéséhez:

1. Telepítés ``msrsync`` és előfeltételei (``rsync`` és Python 2,6 vagy újabb)
1. A másolandó fájlok és könyvtárak teljes számának meghatározása.

   Használja például a segédprogramot ``prime.py`` argumentumokkal ```prime.py --directory /path/to/some/directory``` (a letöltéssel <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py>érhető el).

   Ha nem használja ``prime.py``, a következő módon számíthatja ki az elemek számát a ``find`` GNU eszközzel:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Az elemek számának felosztása a 64 alapján az elemek számának megállapítása folyamatban. Ezt a számot ``-f`` használhatja a gyűjtők méretének beállításához a parancs futtatásakor.

1. Adja ki ``msrsync`` a parancsot a fájlok másolásához:

   ```bash
   msrsync -P --stats -p64 -f<ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Ez a parancs például úgy lett kialakítva, hogy 11 000-es fájlokat helyezzen át a 64 folyamatokban a/test/Source-repository-ről a/mnt/hpccache/repository-re:

   ``mrsync -P --stats -p64 -f170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/hpccache/repository``
