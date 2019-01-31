---
title: A Blob storage adatok áthelyezése az SSIS-összekötők – a csoportos adatelemzési folyamat
description: Adatok áthelyezése vagy onnan máshová SSIS-összekötők használatával az Azure Blob Storage.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d8cb0829698b13e2193850c29f98492df6c48d4f
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55468512"
---
# <a name="move-data-to-or-from-azure-blob-storage-using-ssis-connectors"></a>Adatok áthelyezése vagy onnan máshová SSIS-összekötők használatával az Azure Blob Storage
A [SQL Server Integration Services funkciócsomag Azure](https://msdn.microsoft.com/library/mt146770.aspx) szeretne csatlakozni az Azure-összetevők közötti Azure-ban és a helyszíni adatforrások, és dolgozza fel az adatokat az Azure-ban tárolt adatok átvitele biztosít.

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

Ügyfelek áttelepítette a helyszíni adatokat a felhőbe, ha azok bármely Azure-szolgáltatáshoz az Azure-technológiák a csomag teljes kihasználva is hozzáférhet. Azt is használható, például az Azure Machine Learning vagy HDInsight-fürtön.

Ez a általában az első lépés lehet a [SQL](sql-walkthrough.md) és [HDInsight](hive-walkthrough.md) forgatókönyvek.

Az SSIS használata gyakori hibrid adatintegrációs forgatókönyveket az üzleti igények elvégzéséhez kanonikus forgatókönyvek, lásd: [ezzel nagyobb az SQL Server Integration Services funkciócsomag Azure](https://blogs.msdn.com/b/ssis/archive/2015/06/25/doing-more-with-sql-server-integration-services-feature-pack-for-azure.aspx) blog.

> [!NOTE]
> Egy teljes körű Bevezetés az Azure blob storage, tekintse meg a [Azure Blob alapjai](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) és [Azure Blob Service-ben](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="prerequisites"></a>Előfeltételek
Ebben a cikkben ismertetett feladatok végrehajtásához Azure-előfizetéssel, és állítsa be Azure storage-fiókkal kell rendelkeznie. Ismernie kell a az Azure storage tárfióknév és fiókkulcs feltölteni, vagy töltse le az adatokat.

* Állíthat be egy **Azure-előfizetés**, lásd: [ingyenes egy hónapos próbaidőszak](https://azure.microsoft.com/pricing/free-trial/).
* Létrehozásával egy **tárfiók** és a fiók és a legfontosabb adatok lekérését, lásd: [tudnivalók az Azure storage-fiókok](../../storage/common/storage-create-storage-account.md).

Használatához a **SSIS-összekötők**, le kell töltenie:

* **Az SQL Server 2014 vagy 2016 Standard (vagy újabb)**: Install SQL Server Integration Services tartalmazza.
* **A Microsoft SQL Server 2014 vagy 2016 Integration Services, Azure Feature Pack**: Ezek letölthető, az a [SQL Server 2014 Integration Services](https://www.microsoft.com/download/details.aspx?id=47366) és [SQL Server 2016 Integration Services](https://www.microsoft.com/download/details.aspx?id=49492) oldalakat.

> [!NOTE]
> SSIS az SQL Server telepítve van, de nem szerepel az Express verzió. Milyen alkalmazások szerepelnek-e az SQL Server különböző kiadásainak kapcsolatos információkért lásd: [SQL Server-verziók](https://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/)
> 
> 

Lásd az SSIS oktatási anyagok [kéz a betanítási SSIS-hez](https://www.microsoft.com/sql-server/training-certification)

Információk beszerzése a felfelé és a futó használatával hozhat létre egyszerű kinyerési, átalakítási és betöltési (ETL) csomagok, lásd: SISS [SSIS-oktatóanyag: Egy egyszerű ETL-csomag létrehozása](https://msdn.microsoft.com/library/ms169917.aspx).

## <a name="download-nyc-taxi-dataset"></a>Töltse le a NYC Taxi adatkészlet
A bemutatott példában a nyilvánosan elérhető adatkészlet – ide használata a [NYC Taxi lelassítja](http://www.andresmh.com/nyctaxitrips/) adatkészlet. Az adatkészlet készül 173 millió i taxik fel a következőt: az év 2013 áll. Az adatok két típusa van: trip adatokat és diszkont adatok részleteit. Mivel minden hónapban egy fájlt, az összes, amelyeken, hogy megközelítőleg 2GB tömörítetlen 24 fájlok van.

## <a name="upload-data-to-azure-blob-storage"></a>Adatfeltöltés az Azure blob storage-bA
Az SSIS használata az adatok funkciócsomag a helyszínről az Azure blob storage-áthelyezéséhez egy példányát használjuk a [ **Azure Blob feltöltése feladat**](https://msdn.microsoft.com/library/mt146776.aspx), itt látható:

![Konfigurálja-data-adatelemzési – a virtuális gép](./media/move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)

A paraméterek, a feladat használja ebben a témakörben találhatók:

| Mező | Leírás |
| --- | --- |
| **AzureStorageConnection** |Megadja egy meglévő Azure Storage Connection Manager, vagy létrehoz egy újat, amely egy Azure storage-fiókot, amely a blob-fájlokat üzemeltető vonatkozik. |
| **BlobContainer** |Itt adhatja meg, amelyek rendelkeznek a feltöltött fájlok blobként a blob-tároló neve. |
| **BlobDirectory** |Megadja a blob könyvtárat a blokkblobok, a feltöltött fájl tárolására. A blob-könyvtár virtuális hierarchikus struktúrát. Ha a blob már létezik, informatikai ia cserélni. |
| **LocalDirectory** |Megadja a helyi könyvtárat, amely tartalmazza a feltölteni kívánt fájlokat. |
| **FileName** |Megad egy jelölje ki a megadott név mintával rendelkező fájlokat. Ha például MySheet\*.xls\* fájlok, például MySheet001.xls és MySheetABC.xlsx tartalmaz |
| **TimeRangeFrom/TimeRangeTo** |Megadja a tartomány Időszűrő. Után módosult fájlok *TimeRangeFrom* és előtt *TimeRangeTo* tartalmazza. |

> [!NOTE]
> A **AzureStorageConnection** hitelesítő adatokat kell csak akkor lehet helyes, és a **BlobContainer** léteznie kell az átvitel kísérlet történik.
> 
> 

## <a name="download-data-from-azure-blob-storage"></a>Töltse le az adatok Azure blob storage-ból
Adatok letöltése az Azure blob storage-ból a helyszíni tárolók az SSIS használatával, egy példányát használja a [Azure Blob letöltése feladat](https://msdn.microsoft.com/library/mt146779.aspx).

## <a name="more-advanced-ssis-azure-scenarios"></a>Speciális SSIS-Azure-forgatókönyvek
Az SSIS-feature pack lehetővé teszi, hogy komplex folyamatok által kezelendő csomagolási feladatok együtt. Sikerült a Blobadatok például közvetlenül egy HDInsight-fürtöt, vissza egy blobot, majd a helyszíni tárolók kimenete sikerült letölteni a hírcsatorna. SSIS futtathatja a Hive és Pig-feladatok további SSIS-összekötők használata egy HDInsight-fürtön:

* Egy Hive-szkript futtatásához az Azure HDInsight-fürtön az SSIS használatával használja [Azure HDInsight Hive-tevékenység](https://msdn.microsoft.com/library/mt146771.aspx).
* A Pig-parancsprogram futtatásához az Azure HDInsight-fürtön az SSIS használatával használja [Azure HDInsight Pig-tevékenység](https://msdn.microsoft.com/library/mt146781.aspx).

