---
title: Az Azure HPC cache-adatok betöltése - msrsync
description: Az msrsync használata adatok áthelyezése blobtároló-tárolóba az Azure HPC-gyorsítótárban
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 4f8863d706d623d613ac156cf202c3b7b12f2ae0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168420"
---
# <a name="azure-hpc-cache-data-ingest---msrsync-method"></a>Az Azure HPC cache-adatok betöltése – msrsync metódus

Ez a cikk részletes ``msrsync`` utasításokat ad a segédprogram használatával adatok másolása egy Azure Blob storage-tároló azure HPC-gyorsítótár használata.

Ha többet szeretne megtudni az adatok Blob storage-ba való áthelyezéséről az Azure HPC-gyorsítótárszámára, olvassa el [az Adatok áthelyezése az Azure Blob storage-ba .](hpc-cache-ingest.md)

Az ``msrsync`` eszköz segítségével áthelyezheti az adatokat az Azure HPC-gyorsítótár háttértárolási céljába. Ez az eszköz több párhuzamos ``rsync`` folyamat futtatásával optimalizálja a sávszélesség-használatot. A GitHubról érhető https://github.com/jbd/msrsyncel a.

``msrsync``a forráskönyvtárat külön "gyűjtőkre" bontja, majd minden egyes gyűjtőben futtatja az egyes ``rsync`` folyamatokat.

A négymagos virtuális gép használatával végzett előzetes tesztelés 64 folyamat használata esetén mutatta a legjobb hatékonyságot. Ezzel ``msrsync`` a ``-p`` beállítással 64-re állíthatja a folyamatok számát.

Ne ``msrsync`` feledje, hogy csak írni, és a helyi kötetek. A forrásnak és a célnak elérhetőnek kell lennie a parancs kiadásához használt munkaállomás helyi csatlakoztatásaként.

Kövesse az alábbi ``msrsync`` utasításokat az Azure Blob storage Azure HPC-gyorsítótárral való feltöltéséhez:

1. Telepítés ``msrsync`` és előfeltételei``rsync`` ( és Python 2.6 vagy újabb)
1. Határozza meg a másolandó fájlok és könyvtárak teljes számát.

   Használja például a ``prime.py`` segédprogramot ```prime.py --directory /path/to/some/directory``` argumentumokkal <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py>(letölthető).

   Ha nem ``prime.py``használja a programot, a GNU ``find`` eszközzel az alábbiak szerint számíthatja ki az elemek számát:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Az elemek számának felosztása 64-re az elemek folyamatonkénti számának meghatározásához. Ezzel a ``-f`` számmal állíthatja be a gyűjtők méretét a parancs futtatásakor.

1. Fájlok ``msrsync`` másolására a parancs kiadása:

   ```bash
   msrsync -P --stats -p64 -f<ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Ez a parancs például 11 000 fájlt helyezhet át 64 folyamatban a /test/source-repository könyvtárból a /mnt/hpccache/repository-ba:

   ``mrsync -P --stats -p64 -f170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/hpccache/repository``
