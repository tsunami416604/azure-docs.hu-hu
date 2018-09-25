---
title: Támogatott adatcélok és elérhető az Azure Machine Learning adat-előkészítési kimenetek |} A Microsoft Docs
description: Ez a dokumentum támogatott destinations teljes listáját tartalmazza, és arcokhoz elérhető az Azure Machine Learning adat-előkészítés
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
ms.openlocfilehash: f3d7d10db8cdeb4a1a409650f17d2fc39bf7971f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46969505"
---
# <a name="supported-data-exports-for-this-preview"></a>Adatexportálást támogatott az előzetes verzió 

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Számos különböző formátumokban való exportálása lehetőség. Ezek a formátumok használatával megőrzi az adat-előkészítés a köztes eredményeket előtt az eredményeket a Machine Learning munkafolyamatának a többi alkalmazásba integrálja.

## <a name="types"></a>Típusok 
### <a name="csv-file"></a>CSV-fájl 
A storage vesszővel tagolt fájl írása.

#### <a name="options"></a>Beállítások
- Sorvégződések
- Cserélje le a NULL értékek
- A hibás értékek cseréje 
- Elválasztó


### <a name="parquet"></a>Parquet eszközökben 
Kiírhatja olyan tárhelyekre egy adatkészletet, Parquet.

Parquet formátum is különféle formákat ölthet storage-ban. -Kisebb adatkészletek esetében egyetlen .parquet fájl is használják. Python-kódtárakat olvasását és írását egyetlen .parquet fájlokat támogatja. 

Az Azure Machine Learning Workbench jelenleg írásra Parquet ki helyi interaktív használata során a PyArrow Python-kódtár támaszkodik. Ez azt jelenti, hogy egy fájlból Parquet jelenleg az egyetlen Parquet kimeneti formátum támogatott helyi interaktív használata során.

Során kibővített fut (Spark) az Azure Machine Learning Workbench a Spark a parquet vagy egyéb képességek írásakor vagy olvasásakor támaszkodik. A Spark az alapértelmezett kimeneti formátum a parquet eszközökben (jelenleg az egyetlen támogatott) a Hive adatkészlet szerkezete hasonló. Ez azt jelenti, hogy egy mappa számos .parquet fájlokat tartalmaz, amelyek mindegyike egy nagyobb adatkészletet kisebb eloszlása. 

#### <a name="caveats"></a>Figyelmeztetések 
Parquet formátum viszonylag fiatalok, és néhány megvalósítás inkonzisztens. több különböző szalagtárat. Például a Spark korlátozásokat alkalmaz, amelyen karakterek érvényesek az oszlopnevekhez a parquet eszközökben való írás esetén. PyArrow ezt nem teszi. Egy oszlop neve nem lehet a következő karaktereket: 
- ,
- ;
- {}
- ()
- \\n
- \\T
- =

>[!NOTE]
>- Spark való kompatibilitás biztosítása érdekében adatokat írni az Parquet, amikor ezek a karakterek az oszlopnevekhez előfordulását cserélése és aláhúzásjellel (_).
>- Konzisztencia biztosításához frissítési kísérletei során a helyi és a horizontális felskálázás, Parquet, az alkalmazás, a Python vagy a Spark, bármely adat rendelkezik az oszlopnevek fertőtlenítsék Spark kompatibilitás érdekében. Parquet eszközökben a Spark karakter való írás esetén várható oszlopneveket érdekében távolítsa el az érvénytelen, az oszlopok rögzíti őket mielőtt állítható be.



## <a name="locations"></a>Helyek 
### <a name="local"></a>Helyi 
Helyi merevlemez-meghajtóról vagy a csatlakoztatott hálózati tárolási helye.

### <a name="azure-blob-storage"></a>Azure Blob Storage
Az Azure Blob storage Azure-előfizetés szükséges.

