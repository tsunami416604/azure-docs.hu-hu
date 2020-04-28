---
title: Azure HPC cache-adatfeldolgozás – msrsync
description: Az msrsync használata az Azure HPC cache-ben lévő blob Storage-tárolóba való áthelyezéshez
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 2e0442b6aa1404ae5f57445179979496faa09863
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82194975"
---
# <a name="azure-hpc-cache-data-ingest---msrsync-method"></a>Azure HPC cache-adatfeldolgozás – msrsync metódus

Ez a cikk részletes útmutatást nyújt a ``msrsync`` segédprogram használatával az Azure-beli blob Storage-tárolóba való adatmásoláshoz az Azure HPC cache szolgáltatással való használathoz.

Ha többet szeretne megtudni arról, hogy az Azure HPC gyorsítótára hogyan helyezi át az adatátvitelt a blob Storage-ba, olvassa el az [Azure Blob Storage](hpc-cache-ingest.md)-ba

Az ``msrsync`` eszköz használatával az Azure HPC cache-re helyezheti át az adatátvitelt a háttérbeli tárolási célra. Ez az eszköz úgy lett kialakítva, hogy több párhuzamos ``rsync`` folyamat futtatásával optimalizálja a sávszélesség-használatot. A GitHubon érhető el https://github.com/jbd/msrsync.

``msrsync``elkülöníti a forrás könyvtárat külön "gyűjtő" értékre, majd minden ``rsync`` gyűjtőn futtatja az egyes folyamatokat.

A négy Magos virtuális géppel végzett előzetes tesztelés az 64-es folyamatok használatakor a legjobb hatékonyságot mutatja. A következő ``msrsync`` beállítással ``-p`` állíthatja be a folyamatok számát 64-re.

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
