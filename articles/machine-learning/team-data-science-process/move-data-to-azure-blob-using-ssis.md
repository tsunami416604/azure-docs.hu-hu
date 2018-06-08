---
title: Helyezze át az adatokat, vagy az Azure Blob Storage SSIS-összekötők használata |} Microsoft Docs
description: Azure Blob Storage SSIS-összekötők használata adatok áthelyezéséről.
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: 96a1b5fb-34d1-4b9b-8d99-2bb8289e0398
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: 165180c49de75cf4b635a89dacec311d5370acd3
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837745"
---
# <a name="move-data-to-or-from-azure-blob-storage-using-ssis-connectors"></a>Helyezze át az adatokat, vagy az Azure Blob Storage SSIS-összekötők használata
A [SQL Server Integration Services funkciócsomag a Azure](https://msdn.microsoft.com/library/mt146770.aspx) biztosít az Azure-ban való kapcsolódáshoz összetevők adatátviteli Azure és a helyszíni adatforrások és az Azure-ban tárolt folyamat adatok között.

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

Ügyfelek kiterjeszti a felhőbe a helyszíni adatok került át, ha az összes Azure szolgáltatáson történő kialakíthatják a teljes Azure technológiák suite hozzáférhetne. Akkor használható, például az Azure Machine Learning vagy egy HDInsight-fürtre.

Ez az általában az első lépés lehet a [SQL](sql-walkthrough.md) és [HDInsight](hive-walkthrough.md) forgatókönyvek.

Canonical SSIS segítségével az üzleti igények hibrid adatok integrációs feladatokhoz a gyakori forgatókönyvek tárgyalását lásd: [ezzel nagyobb az SQL Server Integration Services funkciócsomag a Azure](http://blogs.msdn.com/b/ssis/archive/2015/06/25/doing-more-with-sql-server-integration-services-feature-pack-for-azure.aspx) blog.

> [!NOTE]
> Az Azure blob storage teljes bevezetéséhez hivatkozik [Azure Blob alapjai](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) és [Azure Blob szolgáltatás](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="prerequisites"></a>Előfeltételek
Ebben a cikkben ismertetett feladatok végrehajtásához rendelkeznie kell Azure-előfizetés és Azure-tárfiók beállítása. Ismernie kell az Azure storage név és fiókkulcs fiókkulcs feltölteni, vagy töltse le az adatok.

* Beállítása egy **Azure-előfizetés**, lásd: [ingyenes egy hónapos próbaverzió](https://azure.microsoft.com/pricing/free-trial/).
* Létrehozásával egy **tárfiók** és az első fiók és a kulcsadatokat, lásd: [tudnivalók az Azure storage-fiókok](../../storage/common/storage-create-storage-account.md).

Használatához a **SSIS-összekötők**, akkor le kell töltenie:

* **Az SQL Server 2014 vagy 2016 szabvány (vagy újabb)**: telepítése magában foglalja az SQL Server Integration Services.
* **Microsoft SQL Server 2014 vagy 2016 Integration Services funkciócsomag az Azure-**: ezek tölthető le, osztályban, az a [SQL Server 2014 Integration Services](http://www.microsoft.com/download/details.aspx?id=47366) és [az SQL Server 2016 Integration Szolgáltatások](https://www.microsoft.com/download/details.aspx?id=49492) lapokat.

> [!NOTE]
> SSIS SQL Server telepítve van, de nem szerepel az Express verzió. Milyen alkalmazások szerepelnek az SQL Server különböző kiadásai a további információkért lásd: [SQL Server kiadásai](http://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/)
> 
> 

Oktatási anyagok SSIS lásd [beavatkozás nélküli a képzés SSIS](http://www.microsoft.com/download/details.aspx?id=20766)

Fel és futó beszerzésére információt SISS használatával állítanak össze egyszerű kinyerési, átalakítási és betöltési (ETL) csomagok, lásd: [SSIS-Útmutató: egyszerű ETL-csomag létrehozása](https://msdn.microsoft.com/library/ms169917.aspx).

## <a name="download-nyc-taxi-dataset"></a>Töltse le a NYC Taxi adatkészlet
A bemutatott példában a nyilvánosan elérhető adatkészlet--itt használata a [NYC Taxi Utazgatással](http://www.andresmh.com/nyctaxitrips/) adatkészlet. Az adatkészlet hamarosan 173 millió taxi fel a következőt: az év 2013 áll. Az adatok két típusa van: út részletezi az adatok és a jegy ára adatokat. Minden hónapban van egy fájlt, mert tudunk 24 fájlok összes, amely egy tömörítetlen körülbelül 2GB.

## <a name="upload-data-to-azure-blob-storage"></a>Adatok feltöltése az Azure blob storage
A SSIS adatok funkciócsomag a helyszíni az Azure blob storage áthelyezéséhez példányának használjuk a [ **Azure Blob feltöltése feladat**](https://msdn.microsoft.com/library/mt146776.aspx), itt látható:

![Konfigurálja-adatok-tudományos-vm](./media/move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)

A paraméterek, a feladat használja a dokumentum ismerteti:

| Mező | Leírás |
| --- | --- |
| **AzureStorageConnection** |Egy meglévő Azure Storage Connection Manager határozza meg, vagy létrehoz egy új Azure-tárfiók a blob-fájlokat a rendszer hol tárolja mutató hivatkozik. |
| **BlobContainer** |Megadja a blob-tároló, amely a feltöltött fájlok, blobok tárolására nevét. |
| **BlobDirectory** |Megadja a blob könyvtárat egy blokkblob, a feltöltött fájl tárolására. A blob könyvtár virtuális hierarchikus. Ha a blob már létezik, it ia helyett. |
| **LocalDirectory** |Megadja a helyi könyvtárat, amely tartalmazza a feltölteni kívánt fájlokat. |
| **FileName** |Adja meg a név szűrőt a megadott minta fájlok. Például MySheet\*.xls\* például MySheet001.xls és MySheetABC.xlsx fájlokat tartalmazza |
| **TimeRangeFrom/TimeRangeTo** |Megadja, idő tartomány szűrőt. A fájlok után módosítva *TimeRangeFrom* és előtt *TimeRangeTo* tartalmazza. |

> [!NOTE]
> A **AzureStorageConnection** hitelesítő adatokat kell csak akkor lehet helyes, és a **BlobContainer** már léteznie kell az átvitel kísérlet történik.
> 
> 

## <a name="download-data-from-azure-blob-storage"></a>Az Azure blob storage adatok letöltése
Az Azure blob storage adatok letöltése a helyi tároló SSIS a,-példány használata a [Azure Blob feltöltése feladat](https://msdn.microsoft.com/library/mt146779.aspx).

## <a name="more-advanced-ssis-azure-scenarios"></a>Speciális SSIS-Azure-forgatókönyvek
A SSIS funkciócsomag lehetővé teszi, hogy összetettebb folyamatok által kezelhető csomagolási feladatok együtt. Például a blob sikerült adatcsatorna közvetlenül a HDInsight-fürtöt, amelynek kimenete vissza a blob és helyszíni tárterületre letöltése sikerült. SSIS egy HDInsight-fürt további SSIS-összekötők használata futtathat Hive és a Pig feladatot:

* A Hive parancsfájl futtatásához SSIS az Azure HDInsight-fürtök használata [Azure HDInsight Hive feladat](https://msdn.microsoft.com/library/mt146771.aspx).
* A Pig-parancsprogram futtatásához SSIS az Azure HDInsight-fürtök használata [Azure HDInsight Pig feladatot](https://msdn.microsoft.com/library/mt146781.aspx).

