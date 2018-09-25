---
title: Támogatott adatforrások az Azure Machine Learning adat-előkészítési elérhető |} A Microsoft Docs
description: Ebben a dokumentumban az Azure Machine Learning adat-előkészítési érhető el a támogatott adatforrások teljes listája.
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ROBOTS: NOINDEX
ms.openlocfilehash: c4686bf01031e30bd9dc94f42d80da524db82931
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46969257"
---
# <a name="supported-data-sources-for-azure-machine-learning-data-preparation"></a>Támogatott adatforrások az Azure Machine Learning adat-előkészítés 

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 

Ez a cikk ismerteti az Azure Machine Learning adat-előkészítés a jelenleg támogatott adatforrások.

A támogatott adatforrások ebben a kiadásban az alábbiak szerint.

## <a name="types"></a>Típusok 

### <a name="sql-server"></a>SQL Server
A helyszíni SQL server vagy az Azure SQL database olvasni.

#### <a name="options"></a>Beállítások
- Kiszolgáló címe
- Megbízhatósági kiszolgáló (még ha a tanúsítványt a kiszolgálón nem érvényes. Körültekintően használja)
- Hitelesítési típus (Windows, a kiszolgáló)
- Felhasználónév
- Jelszó
- Csatlakozás adatbázishoz
- SQL-lekérdezés

#### <a name="notes"></a>Megjegyzések
- SQL-változatot oszlopok nem támogatottak.
- Idő típusú oszlop dátum/idő alakítja át hozzáfűzésével adatbázis 1970/1/1 dátum és idő
- Amikor Spark-fürtön hajtja végre, az összes adat kapcsolatos oszlopok (dátum, dátum és idő, datetime2, datetimeoffset) a rendszer helytelen értékeit értékelheti ki dátumok előtt 1583
- Tizedes tört oszlopokban szereplő értékek miatt átalakítása decimális pontosság elveszhetnek.

### <a name="directory-vs-file"></a>Fájl és könyvtár
Válasszon egy fájlt, és olvassa el, az adat-előkészítési. A fájl típusa határozza meg a fájl-kapcsolat a következő képernyőn megjelenő alapértelmezett paraméterek a rendszer elemzi.

Válasszon egy könyvtárat vagy fájlokat egy könyvtárat (a Fájlkereső a többszörös kijelöléses) belül. Bármelyik módszert használja a fájlok egyetlen folyamat, az olvasott és lesz hozzáfűzve egymással, a fejlécek, szükség esetén eltávolítja.

A támogatott típusok a következők:
- Tagolt (.csv .tsv, .txt, stb.)
- Rögzített szélesség
- Egyszerű szöveg
- JSON-fájl

### <a name="csv-file"></a>CSV-fájl
Egy vesszővel tagolt fájl olvasása a storage-ból.

#### <a name="options"></a>Beállítások
- Elválasztó
- Megjegyzés
- Fejlécek
- Tizedesjel
- Fájlkódolás
- Kihagyandó sorok

### <a name="tsv-file"></a>TSV-fájlt
A lap tagolt fájl olvasásához storage-ból.

#### <a name="options"></a>Beállítások
- Megjegyzés
- Fejlécek
- Fájlkódolás
- Kihagyandó sorok

### <a name="excel-xlsxlsx"></a>Excel (.xls/.xlsx)
Olvassa el egyszerre egy Excel-fájlt egy munkalapot lap nevét vagy szám megadásával.

#### <a name="options"></a>Beállítások
- A lap nevének vagy száma
- Fejlécek
- Kihagyandó sorok

### <a name="json-file"></a>JSON-fájl
Storage-ból, olvassa el a JSON-fájlt. A fájl "simítják" Olvasás.

#### <a name="options"></a>Beállítások
- None

### <a name="parquet"></a>Parquet eszközökben
Olvassa el a parquet eszközökben adatkészletet, vagy egy adott fájl vagy mappa.

Parquet formátum is különféle formákat ölthet storage-ban. A kisebb adatkészletek egyetlen .parquet fájl is használják. Python-kódtárakat támogatja egyetlen .parquet fájlok írása és olvasása. Egyelőre az Azure Machine Learning adat-előkészítés a parquet eszközökben olvasásához helyi interaktív használata során PyArrow Python-kódtár támaszkodik. Támogatja a egyetlen .parquet fájlok (amennyiben ezek készültek, így, és a egy nagyobb méretű adathalmaz részeként nem), valamint a parquet vagy egyéb adatkészletek.

Parquet adatkészlet gyűjteménye, amelyek mindegyike egy kisebb partíció egy nagyobb méretű adathalmaz jelöli, egynél több .parquet fájl. Az adatkészletek általában egy mappában található, és olyan platformokhoz, mint a Spark- és Hive alapértelmezett parquet kimeneti formátum.

>[!NOTE]
>Több .parquet fájl egy mappa a parquet vagy egyéb adatok olvasása közben,, válassza ki azt a címtárat olvasásra, legbiztonságosabb és a **Parquet adatkészlet** lehetőséget. Ez lehetővé teszi a PyArrow, olvassa el az egyes fájlok helyett a teljes mappát. Ez biztosítja, hogy a parquet eszközökben tárolásához a lemezen, például a mappát a particionálás bonyolultabb módjait olvasása.

Horizontális felskálázás végrehajtási képességek olvasása a Spark a parquet eszközökben támaszkodik, és támogatja egyetlen fájlok és mappák helyi interaktív használatra hasonló.

#### <a name="options"></a>Beállítások
- Parquet adatkészlet. Ez a beállítás meghatározza, hogy az Azure Machine Learning adat-előkészítési bővül egy adott címtár-e, és megpróbálja beolvasni külön-külön az egyes fájlok (a ki nem jelölt üzemmód), vagy hogy kezeli a könyvtárban, a teljes adatkészletet (a kijelölt mód). A kiválasztott móddal PyArrow úgy dönt, a legjobb módja a fájlok értelmezése.


## <a name="locations"></a>Helyek
### <a name="local"></a>Helyi
A helyi merevlemezre vagy egy csatlakoztatott hálózati tárhelyet.

### <a name="sql-server"></a>SQL Server
Helyszíni SQL Server, vagy az Azure SQL database.

### <a name="azure-blob-storage"></a>Azure Blob Storage
Az Azure Blob storage, amely egy Azure-előfizetés szükséges.

