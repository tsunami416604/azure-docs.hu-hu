---
title: "Támogatott adatforrások az Azure Machine Learning adatok előkészítése |} Microsoft Docs"
description: "Ez a dokumentum nyújt a támogatott adatforrások teljes listáját az Azure Machine Learning adatok előkészítése"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: 1ef4c5c33d98cfeb566e8fe23bda9e0d3f041781
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2017
---
# <a name="supported-data-sources-for-this-release"></a>Ebben a kiadásban támogatott adatforrások 
A következő dokumentum ismerteti az Azure Machine Learning adatok előkészítése a jelenleg támogatott adatforrások listáját.

A támogatott adatforrások ebben a kiadásban a következők:

## <a name="types"></a>Típusok 
### <a name="directory-versus-file"></a>Fájl vagy könyvtár
*Fájlok vagy könyvtárak*: Válasszon egy fájlt, és olvassa el, az adatok előkészítése. A fájl típusa határozza meg a fájl kapcsolattal, amely a következő képernyőn alapértelmezett paramétereinek elemzett. Válasszon egy könyvtárat vagy állítja be a fájlok egy könyvtárat (a fájlválasztó multiselect) belül. Mindkét megközelítés eredmények a hozzáíródik egymáshoz (szükség esetén eltávolítja fejlécek) fájlok olvasása egyetlen adatfolyamban, fájljaiban.

A fájl típusok a következők:
- Tagolt (.csv, .tsv, .txt, és így tovább) 
- A rögzített szélességű
- Egyszerű szöveg
- JSON-fájl

### <a name="csv-file"></a>A CSV-fájl
Tároló CSV-fájlból olvassa be.

#### <a name="options"></a>Beállítások
- Elválasztó
- Megjegyzés
- Fejlécek
- Tizedesjel
- Fájlkódolás
- Átugrandó sorok

### <a name="tsv-file"></a>TSV fájl
Tárolási egy TSV értékeket tartalmazó fájlból olvassa be.

#### <a name="options"></a>Beállítások
- Megjegyzés
- Fejlécek
- Fájlkódolás
- Átugrandó sorok

### <a name="excel-xlsxlsx"></a>Excel (.xls/.xlsx)
Egy Excel-fájl, egyszerre, egy lap beolvassa a lap nevének vagy számának megadásával.

#### <a name="options"></a>Beállítások
- Lap neve/száma
- Fejlécek
- Átugrandó sorok

### <a name="json-file"></a>JSON-fájl
A JSON-fájl tárolási olvasni. Vegye figyelembe az, hogy a fájl "egybesimított" olvasáskor.

#### <a name="options"></a>Beállítások
None

### <a name="parquet"></a>Parquet
Olvassa el a Parquet adatkészlet vagy egyetlen fájl vagy mappa.

Parquet, formátum különböző formát ölthetnek tároló. Kisebb adatkészletek esetében egyetlen .parquet fájl használható. Különböző Python könyvtárak olvasása vagy írása egyetlen .parquet fájl támogatja. Azure Machine Learning-munkaterület jelenleg támaszkodik a PyArrow Python kódtár Parquet olvasásához helyi interaktív használat során. Egyetlen .parquet fájlok (feltéve, írt, nem a nagyobb DataSet adatkészlet részeként) támogatja. Parquet adatkészletek is támogatja. 

Egy Parquet adatkészlet egynél több .parquet fájl, a nagyobb adatkészletek kisebb partíció jelöli, amelyek mindegyike gyűjteménye. Adatkészletek általában a mappa tartalmazza. Az alapértelmezett Parquet kimeneti formátum közös platformokon, például a Spark- és Hive.

>[!NOTE]
>A mappában, amelynek több .parquet fájl Parquet adatok olvasása közben, esetén válassza ki azt a címtárat, olvasási és az osztásjelek a legbiztonságosabb a **Parquet Dataset** lehetőséget. Így az egész mappa helyett a fájlok olvasása PyArrow. Ez biztosítja, hogy olvasási bonyolultabb módokat Parquet tárolása a lemezen (például mappa particionálás.) támogatása

Kibővített végrehajtása a Spark Parquet képességek olvasása alapul, és egyetlen fájlok, valamint a mappák támogatja.

#### <a name="options"></a>Beállítások
*A dataset parquet*: Ez a beállítás határozza meg, ha Azure Machine Learning-munkaterület használja-e a unticked vagy a ticked üzemmódban. A unticked mód adott címtárak bővíti, és megkísérli beolvasni egyenként mindegyik fájlban. A ticked módot a könyvtárat, a teljes adatkészlet kezeli, és lehetővé teszi, hogy a PyArrow mérje fel, a legjobb módja a fájlok értelmezhetők.


## <a name="locations"></a>Helyek
### <a name="local"></a>Helyi
Helyi merevlemez-meghajtóról vagy a csatlakoztatott hálózati tárolási helyét.

### <a name="azure-blob-storage"></a>Azure Blob Storage
Azure-előfizetés szükséges.

