---
title: Támogatott adatforrások érhető el az Azure Machine Learning adatok előkészítése |} Microsoft Docs
description: Ez a dokumentum az Azure Machine Learning adatok előkészítése érhető el a támogatott adatforrások teljes listáját tartalmazza.
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 7b42080ea4bf9a9e49f2695ab8746d9ead7348bd
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="supported-data-sources-for-azure-machine-learning-data-preparation"></a>A támogatott adatforrások az Azure Machine Learning adatok előkészítése 
Ez a cikk ismerteti az Azure Machine Learning adatok előkészítése a jelenleg támogatott adatforrások.

A támogatott adatforrások ebben a kiadásban a következők:

## <a name="types"></a>Típusok 

### <a name="sql-server"></a>SQL Server
Helyszíni SQL server vagy az Azure SQL database olvasni.

#### <a name="options"></a>Beállítások
- Kiszolgálócím
- Megbízható kiszolgáló (páros, ha a tanúsítványt a kiszolgálón nem érvényes. Óvatosan)
- Hitelesítési típus (Windows, a kiszolgáló)
- Felhasználónév
- Jelszó
- Az adatbázishoz való kapcsolódáshoz
- SQL-lekérdezés

#### <a name="notes"></a>Megjegyzések
- SQL-variant oszlopok nem támogatottak.
- Idő oszlop adatbázis 1970/1/1 dátum és idő hozzáfűzésével alakítja át a dátum és idő
- A Spark-fürt végrehajtásakor kapcsolódó összes adatok (dátum, dátum és idő, datetime2, datetimeoffset típusú) oszlopok helytelen értékeket kiértékelik a dátumok előtt 1583
- Decimális oszlopokban szereplő értékek miatt átalakítása decimális pontosság megszakadhat.

### <a name="directory-vs-file"></a>Fájl vagy könyvtár
Válasszon egy fájlt, és olvassa el, az adatok előkészítése. A fájl típusa határozza meg a fájl kapcsolat a következő képernyőn megjelenő alapértelmezett paraméterek elemzett.

Válasszon egy könyvtárat vagy a fájlokat egy könyvtárat (a fájlválasztó multiselect) belül. Bármelyik módszert használja a fájlok olvashatóak egy egyetlen folyamatként, és lesz hozzáfűzve egymással, a fejlécek, szükség esetén eltávolítja.

A támogatott típusok a következők:
- Tagolt (.csv .tsv, .txt, stb.)
- A rögzített szélességű
- Egyszerű szöveg
- JSON-fájl

### <a name="csv-file"></a>A CSV-fájl
A vesszővel tagolt fájl olvasásához tárolóból.

#### <a name="options"></a>Beállítások
- Elválasztó
- Megjegyzés
- Fejlécek
- Tizedesjel
- Fájlkódolás
- Átugrandó sorok

### <a name="tsv-file"></a>TSV fájl
A lapon tagolt fájl olvasásához a tárolóból.

#### <a name="options"></a>Beállítások
- Megjegyzés
- Fejlécek
- Fájlkódolás
- Átugrandó sorok

### <a name="excel-xlsxlsx"></a>Excel (.xls/.xlsx)
Olvassa el egyszerre egy Excel-fájl egy lapot a lap nevének vagy számának megadásával.

#### <a name="options"></a>Beállítások
- A lap nevének vagy száma
- Fejlécek
- Átugrandó sorok

### <a name="json-file"></a>JSON-fájl
A JSON-fájl tárolási olvasni. A fájl "egybesimított" olvasáskor.

#### <a name="options"></a>Beállítások
- None

### <a name="parquet"></a>Parquet
Olvassa el a Parquet adatkészletet, vagy egyetlen fájl vagy mappa.

Parquet, formátum különböző formát ölthetnek tároló. A kisebb adatkészletek egyetlen .parquet fájl használható. Különböző Python könyvtárak olvasása vagy írása egyetlen .parquet fájlokat támogatja. Egyelőre Azure Machine Learning adatok előkészítése támaszkodik a PyArrow Python kódtár Parquet olvasásához helyi interaktív használat során. Támogatja a egyetlen .parquet fájlok (amennyiben ezek készültek, így, és nem nagyobb adatkészlet részeként), valamint Parquet adatkészletek.

Parquet adatkészlet egynél több .parquet fájl, a nagyobb adatkészletet kisebb partíciójának jelöli, amelyek mindegyike gyűjteménye. Adatkészletek általában egy mappában található és platformokon, például a Spark- és Hive alapértelmezett parquet kimeneti formátumát.

>[!NOTE]
>Parquet adatokat, amelyek egy mappában, amelynek több .parquet fájl olvasása közben, esetén válassza ki azt a címtárat olvasását, a legbiztonságosabb és a **Parquet adatkészlet** lehetőséget. Így az egész mappa helyett a fájlok olvasása PyArrow. Ez biztosítja, hogy a támogatási bonyolultabb módokat Parquet tárolása a lemezen, például a particionálás mappa olvasásához.

Kibővített végrehajtása a Spark Parquet képességek olvasása alapul, és egyetlen fájlok, valamint a mappákat, hasonló helyi interaktív használatát támogatja.

#### <a name="options"></a>Beállítások
- Parquet adatkészlet. Ez a beállítás meghatározza, hogy Azure Machine Learning adatok előkészítése bővíti az adott címtárak-e, és megkísérli beolvasni külön-külön minden fájl (a ki nem jelölt üzemmód), vagy hogy az értékként kezelje a könyvtárban, a teljes adatkészlet (a kijelölt mód). A kijelölt üzemmódot PyArrow úgy dönt, a legjobb módja a fájlok értelmezhetők.


## <a name="locations"></a>Helyek
### <a name="local"></a>Helyi
A helyi merevlemez-meghajtóról vagy egy csatlakoztatott hálózati tárolási helyét.

### <a name="sql-server"></a>SQL Server
Helyszíni SQL Server, vagy az Azure SQL database.

### <a name="azure-blob-storage"></a>Azure Blob Storage
Az Azure Blob storage, amelyhez az Azure-előfizetés szükséges.

