---
title: Adatok áthelyezése Egy Azure HPC cache felhőtárolóba
description: Az Azure Blob storage feltöltése az Azure HPC-gyorsítótárral való használatra
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: fd21a78d0271f91d334bba5aba748f3770ad38cf
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537933"
---
# <a name="move-data-to-azure-blob-storage"></a>Adatok áthelyezése az Azure Blob storage-ba

Ha a munkafolyamat magában foglalja az adatok áthelyezését az Azure Blob storage-ba, győződjön meg arról, hogy hatékony stratégiát használ. Előre betöltheti az adatokat egy új Blob-tárolóban, mielőtt tárolócélként definiálná őket, vagy hozzáadhatja a tárolót, majd másolja az adatokat az Azure HPC-gyorsítótár használatával.

Ez a cikk ismerteti az adatok áthelyezése a Blob storage-ba az Azure HPC-gyorsítótárhasználata.

Tartsa szem előtt ezeket a tényeket:

* Az Azure HPC cache egy speciális tárolási formátumot használ az adatok blob storage-ban történő rendszerezéséhez. Ez az oka annak, hogy a Blob storage-tároló nak egy új, üres tárolónak vagy egy Olyan Blob-tárolónak kell lennie, amelyet korábban az Azure HPC-gyorsítótár adataihoz használt.

* Az adatok másolása az Azure HPC-gyorsítótáron keresztül egy háttértároló-tároló cél hatékonyabb, ha több ügyfél és párhuzamos műveletek használata. Egy egyszerű másolási parancs az egyik ügyféltől lassan mozgatja az adatokat.

A Python-alapú segédprogram érhető el a tartalom betöltéséhez egy Blob storage-tárolóba. További információért olvassa el [a Blob storage-ban az adatok előzetes betöltése.](#pre-load-data-in-blob-storage-with-clfsload)

Ha nem szeretné használni a betöltő segédprogramot, vagy ha tartalmat szeretne hozzáadni egy meglévő tárolási célhoz, kövesse az adatok másolása az [Azure HPC-gyorsítótáron keresztül történő másolási](#copy-data-through-the-azure-hpc-cache)tippeket.

## <a name="pre-load-data-in-blob-storage-with-clfsload"></a>Adatok előzetes betöltése a Blob storage-ban a CLFSLoad segítségével

Az Avere CLFSLoad segédprogram segítségével adatokat másolhat egy új Blob-tárolóba, mielőtt hozzáadja azokat tárolási célként. Ez a segédprogram egyetlen Linux-rendszeren fut, és az Azure HPC-gyorsítótárhoz szükséges saját formátumban írja az adatokat. ClFSLoad a leghatékonyabb módja a Blob storage tároló feltöltésének a gyorsítótárral való használatra.

Az Avere CLFSLoad segédprogram az Azure HPC cache csapata kérésére érhető el. Kérje meg a csapatpartnerét, vagy nyisson meg egy [támogatási jegyet,](hpc-cache-support-ticket.md) hogy segítséget kérjen.

Ez a beállítás csak új, üres tárolókkal működik. Az Avere CLFSLoad használata előtt hozza létre a tárolót.

Részletes információkat az Avere CLFSLoad disztribúció, amely az Azure HPC cache csapat kérésére érhető el.

A folyamat általános áttekintése:

1. Készítse elő a Linux-rendszer (VM vagy fizikai) python 3.6-os vagy újabb verzióval. Python 3.7 ajánlott a jobb teljesítmény érdekében.
1. Telepítse az Avere-CLFSLoad szoftvert a Linux rendszerre.
1. Hajtsa végre az átvitelt a Linux parancssorból.

Az Avere CLFSLoad segédprogramnak a következő információkra van szüksége:

* A Blob storage-tárolót tartalmazó tárfiók-azonosító
* Az üres Blob-tároló neve
* Egy sas-jogkivonat, amely lehetővé teszi, hogy a segédprogram a tárolóba írjon
* Az adatforrás helyi elérési útja – vagy egy helyi könyvtár, amely a másolandó adatokat tartalmazza, vagy egy csatlakoztatott távoli rendszer helyi elérési útja az adatokkal

## <a name="copy-data-through-the-azure-hpc-cache"></a>Adatok másolása az Azure HPC-gyorsítótáron keresztül

Ha nem szeretné használni az Avere CLFSLoad segédprogramot, vagy ha nagy mennyiségű adatot szeretne hozzáadni egy meglévő Blob-tároló célhoz, átmásolhatja azt a gyorsítótáron keresztül. Az Azure HPC-gyorsítótár célja, hogy egyszerre több ügyfelet szolgáljon ki, ezért az adatok másolásához a gyorsítótáron keresztül, több ügyféltől származó párhuzamos írásokat kell használnia.

![Többügyféles, többszálas adatmozgást bemutató diagram: A bal felső sarokban a helyszíni hardveres tárolás ikonja több nyíllal rendelkezik. A nyilak négy ügyfélgépre mutatnak. Minden ügyfélgépről három nyíl mutat az Azure HPC-gyorsítótár felé. Az Azure HPC-gyorsítótárból több nyíl mutat a Blob storage.From the Azure HPC Cache, multiple arrows point to Blob storage.](media/hpc-cache-parallel-ingest.png)

Az ``cp`` ``copy`` egyik tárolórendszerből a másikba általában az adatok átvitelére használt vagy parancsok egyszálas folyamatok, amelyek egyszerre csak egy fájlt másolnak. Ez azt jelenti, hogy a fájlkiszolgáló egyszerre csak egy fájlt vesz fel – ami a gyorsítótár erőforrásainak pazarlása.

Ez a szakasz ismerteti a többügyféles, többszálas fájlmásolási rendszer létrehozásához stratégiákat az adatok Blob storage-ba való áthelyezéséhez az Azure HPC-gyorsítótárral. Ismerteti a fájlátviteli fogalmakat és a döntési pontokat, amelyek felhasználhatók a több ügyfél és egyszerű másolási parancsok használatával történő hatékony adatmásoláshoz.

Azt is elmagyarázza, néhány segédprogramok, amelyek segíthetnek. A ``msrsync`` segédprogram segítségével részben automatizálható az adatkészlet eksztázisokra osztásának folyamata és az rsync parancsok használata. A ``parallelcp`` parancsfájl egy másik segédprogram, amely beolvassa a forráskönyvtárat, és automatikusan kiadja a másolási parancsokat.

### <a name="strategic-planning"></a>Stratégiai tervezés

Amikor stratégiát készít az adatok párhuzamos másolására, meg kell értenie a fájlméret, a fájlszám és a könyvtármélység kompromisszumait.

* Ha a fájlok kicsik, az érdeklődési mutató a fájlok másodpercenként.
* Ha a fájlok nagyok (10MiBi vagy nagyobb), az érdeklődési mutató bájt/másodperc.

Minden másolási folyamat áteresztőhet tesz le, és fájlokat továbbít, ami a másolási parancs hosszának időzítésével, valamint a fájlméret és a fájlszám tényezőjével mérhető. Az árak mérésének magyarázata nem tartozik a dokumentum hatálya alá, de feltétlenül meg kell értenie, hogy kis vagy nagy méretű fájlokkal fog-e foglalkozni.

Az Azure HPC-gyorsítótárral való párhuzamos adatbetöltési stratégiák a következők:

* Kézi másolás – Manuálisan is létrehozhat többszálas másolatot az ügyfélen, ha egyszerre több másolási parancsot futtat a háttérben előre meghatározott fájl- vagy elérésiutakkal szemben. Olvassa el [az Azure HPC cache adatok betöltése - manuális másolási módszer](hpc-cache-ingest-manual.md) a részletekért.

* A részlegesen ``msrsync``  -  ``msrsync`` automatizált másolás egy burkoló ``rsync`` segédprogram, amely több párhuzamos folyamatot futtat. A részletekért olvassa el [az Azure HPC cache-adatok betöltését – msrsync metódus.](hpc-cache-ingest-msrsync.md)

* Parancsfájlalapú másolás ``parallelcp`` a - Megtudhatja, hogyan hozhat létre és futtathat párhuzamos copy script et [az Azure HPC Cache adatok betöltése - párhuzamos copy script módszer](hpc-cache-ingest-parallelcp.md).

## <a name="next-steps"></a>További lépések

A tárhely beállítása után ismerje meg, hogyan csatlakoztathatják az ügyfelek a gyorsítótárat.

* [Az Azure HPC cache rendszer elérése](hpc-cache-mount.md)
