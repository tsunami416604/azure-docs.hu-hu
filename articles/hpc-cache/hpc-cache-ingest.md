---
title: Az Azure HPC cache Cloud Container-tárolóba való áthelyezése
description: Az Azure Blob Storage feltöltése az Azure HPC cache használatával
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: v-erkell
ms.openlocfilehash: 07a97b1afa8049ace97f1589393cd76c24f21368
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70775649"
---
# <a name="move-data-to-azure-blob-storage-for-azure-hpc-cache"></a>Adatáthelyezés az Azure Blob Storage-ba az Azure HPC cache szolgáltatásba

Ha a munkafolyamat az Azure Blob Storage-ba helyezi át az adatáthelyezést, győződjön meg arról, hogy hatékony stratégiát használ az adatmásoláshoz az Azure HPC cache használatával.

Ez a cikk azt ismerteti, hogyan helyezhetők át a blob Storage-ba az Azure HPC cache szolgáltatással.

Tartsa szem előtt ezeket a tényeket:

* Az Azure HPC cache egy speciális tárolási formátumot használ a blob Storage-ban tárolt adatrendszerezéshez. Ezért a blob Storage-tárolónak egy új, üres tárolónak vagy egy korábban az Azure HPC gyorsítótár-adateléréséhez használt BLOB-tárolónak kell lennie. ([A avere vFXT for Azure](https://azure.microsoft.com/services/storage/avere-vfxt/) ezt a felhőalapú fájlrendszert is használja.)

* Az adatok az Azure HPC cache használatával történő másolása a legjobb, ha több ügyfelet és párhuzamos műveletet használ. Az egyik ügyfél egyszerű másolási parancsa lassan áthelyezi az adatátvitelt.

Egy Python-alapú segédprogram használható a tartalom blob Storage-tárolóba való betöltésére. További információért olvassa el [a blob Storage-beli előzetes betöltését](#pre-load-data-in-blob-storage-with-clfsload) ismertető témakört.

Ha nem kívánja használni a betöltési segédprogramot, vagy ha tartalmat szeretne hozzáadni egy meglévő tárolási célhoz, kövesse a párhuzamos adatokat az [adatokat az Azure HPC-gyorsítótárból történő másolásával](#copy-data-through-the-azure-hpc-cache)kapcsolatos Tippekben. 

## <a name="pre-load-data-in-blob-storage-with-clfsload"></a>A blob Storage-ban tárolt előzetes betöltés a CLFSLoad-mel

A [avere CLFSLoad](https://aka.ms/avere-clfsload) segédprogrammal az új blob Storage-tárolóba másolhatja az Adatmásolást, mielőtt hozzáadja azt a tároló céljához. Ez a segédprogram Linux rendszerű virtuális gépen fut, és az Azure HPC cache-hez szükséges saját formátumban írja az adatot. Ez a leghatékonyabb módszer a blob Storage-tárolók feltöltésére a gyorsítótárral való használatra.

Ez a beállítás csak az új, üres tárolókkal működik. Hozza létre a tárolót a avere CLFSLoad használata előtt.

Részletes információkat a [avere CLFSLoad readme](https://github.com/microsoft/Avere-CLFSLoad/blob/master/README.md)tartalmaz. <!-- caution literal link -->

A folyamat általános áttekintése:

1. Készítse elő a linuxos rendszert (fizikai vagy virtuális gépet) a Python 3,6-es vagy újabb verziójával. (A Python 3,7 ajánlott a jobb teljesítmény érdekében.)
1. Telepítse a avere-CLFSLoad szoftvert a Linux rendszerre.
1. Hajtsa végre az átvitelt a Linux parancssorból.

A avere CLFSLoad segédprogramnak a következő információkra van szüksége:

* A blob Storage-tárolót tartalmazó Storage-fiók azonosítója
* Az üres blob Storage-tároló neve
* Közös hozzáférésű aláírási (SAS) token, amely lehetővé teszi a segédprogram számára a tárolóba való írást
* Az adatforrás helyi elérési útja – vagy egy helyi könyvtár, amely a másolni kívánt adatforrást vagy egy csatlakoztatott távoli rendszer helyi elérési útját tartalmazza.

A követelményeket részletesen ismertetjük a [avere CLFSLoad readme](https://aka.ms/avere-clfsload)-ban.

## <a name="copy-data-through-the-azure-hpc-cache"></a>Adatmásolás az Azure HPC cache használatával

Ha nem szeretné a avere CLFSLoad segédprogramot használni, vagy ha nagy mennyiségű adatmennyiséget szeretne hozzáadni egy meglévő blob Storage-tárolóhoz, akkor a gyorsítótárba másolhatja. Az Azure HPC cache több ügyfél egyidejű kiszolgálására lett kialakítva, így az adatok gyorsítótáron keresztül történő másolásához több ügyfélről is párhuzamos írásokat kell használnia.

![Több ügyfelet tartalmazó, többszálas adatáthelyezést ábrázoló diagram: A bal felső sarokban látható, hogy a helyszíni hardveres tárterület ikonja több nyilat tartalmaz. A nyilak négy ügyfélszámítógépre mutatnak. Az egyes ügyfélgépekről három nyíl mutat az Azure HPC cache felé. Az Azure HPC-gyorsítótárból több nyíl mutat a blob Storage-hoz.](media/hpc-cache-parallel-ingest.png) 

Azok ``cp`` a ``copy`` vagy parancsok, amelyek használatával az adatok átvitele az egyik tárolási rendszerről a másikra történik, egyetlen szálon futó folyamatok, amelyek egyszerre csak egy fájlt másolnak. Ez azt jelenti, hogy a fájlkiszolgáló egyszerre csak egy fájlt tölt be – ez a fürt erőforrásainak hulladéka.

Ez a szakasz a több ügyfelet tartalmazó, többszálas fájlmásolási rendszer létrehozására szolgáló stratégiákat ismerteti az adatok blob Storage-ba való áthelyezéséhez az Azure HPC cache használatával. Ismerteti a fájlátviteli fogalmakat és a döntési pontokat, amelyek segítségével több ügyfél és egyszerű másolási parancs használatával hatékony Adatmásolást lehet használni.

Emellett ismerteti azokat a segédprogramokat is, amelyek segíthetnek. A ``msrsync`` segédprogrammal részben automatizálható az adatkészlet gyűjtővé való osztása és az rsync-parancsok használata. A ``parallelcp`` parancsfájl egy másik segédprogram, amely beolvassa a forrás könyvtárat, és automatikusan kiadja a másolási parancsokat.

### <a name="strategic-planning"></a>Stratégiai tervezés

Amikor párhuzamosan másolja az adatmásolási stratégiát, ismernie kell a fájlok mérete, a fájlok száma és a könyvtár mélysége közötti kompromisszumokat.

* Ha a fájlok kicsik, a kamat a fájlok másodpercenkénti száma.
* Ha a fájlok nagy méretűek (10MiBi vagy nagyobbak), a kamat mérőszáma másodpercenként bájt.

Minden másolási folyamathoz tartozik egy átviteli sebesség és egy fájl – átvitt sebesség, amely a másolási parancs hosszának időzítésével és a fájlméret és a fájlok számának megadásával mérhető. A díjszabás mértékének megállapítása kívül esik a jelen dokumentum hatókörén, de fontos tisztában lennie azzal, hogy a kis-és nagyméretű fájlokat is érdemes-e kezelni.

Az Azure HPC cache-vel való párhuzamos adatfeldolgozási stratégiák a következők:

* Manuális másolás – manuálisan is létrehozhat többszálas másolatot egy ügyfélen, ha több másolási parancsot futtat egyszerre a háttérben a fájlok vagy elérési utak előre definiált készletei között. Az [Azure HPC Cloud-adatok beolvasása – manuális másolási módszer](hpc-cache-ingest-manual.md) a részletekért.

* A részlegesen automatizált ``msrsync`` másolás  -  ``msrsync`` egy olyan burkoló segédprogram, amely több párhuzamos ``rsync`` folyamatot futtat. Részletekért olvassa el az [Azure HPC cache-adatok betöltése – msrsync metódust](hpc-cache-ingest-msrsync.md).

* Parancsfájlba foglalt ``parallelcp`` másolás – megtudhatja, hogyan hozhat létre és futtathat párhuzamos másolási parancsfájlokat az [Azure HPC cache-adatokban – párhuzamos másolási parancsfájl-metódussal](hpc-cache-ingest-parallelcp.md).

## <a name="next-steps"></a>További lépések

A tárterület beállítása után megismerheti, hogyan csatlakoztathatók az ügyfelek a gyorsítótárhoz.

* [Hozzáférés az Azure HPC cache Systemhez](hpc-cache-mount.md)
