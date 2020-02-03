---
title: A Blob storage adatok áthelyezése az SSIS-összekötők – a csoportos adatelemzési folyamat
description: Megtudhatja, hogyan helyezheti át az Azure Blob Storageba vagy az Azure-ba való adatátvitelt az Azure SQL Server Integration Services Feature Pack használatával.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 77bfd9d5bcae7bedd673354e32464d5f59bdc9b4
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720871"
---
# <a name="move-data-to-or-from-azure-blob-storage-using-ssis-connectors"></a>Adatok áthelyezése vagy onnan máshová SSIS-összekötők használatával az Azure Blob Storage
Az [azure SQL Server Integration Services Feature Pack](https://msdn.microsoft.com/library/mt146770.aspx) az Azure-hoz való kapcsolódáshoz, az Azure-beli és a helyszíni adatforrások közötti adatátvitelhez, valamint az Azure-ban tárolt adatok feldolgozásához biztosít összetevőket.

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

Miután az ügyfelek áthelyezték a helyszíni adatait a felhőbe, hozzáférhetnek az adatokhoz bármely Azure-szolgáltatásból, hogy kihasználják az Azure-technológiák teljes funkcionalitását. Előfordulhat, hogy az ilyen típusú adathalmazok például Azure Machine Learning vagy egy HDInsight-fürtön használhatók.

Az Azure-erőforrások használatára vonatkozó példák az [SQL](sql-walkthrough.md) -és a [HDInsight](hive-walkthrough.md) -forgatókönyvekben találhatók.

A hibrid adatintegrációs forgatókönyvekben a SSIS használatával közös üzleti igényeket biztosító kanonikus forgatókönyvek megvitatására a további tudnivalókat lásd: [SQL Server Integration Services Feature Pack for Azure](https://blogs.msdn.com/b/ssis/archive/2015/06/25/doing-more-with-sql-server-integration-services-feature-pack-for-azure.aspx) blog.

> [!NOTE]
> Az Azure Blob Storage teljes körű bevezetéséhez tekintse meg az [Azure Blob alapjai](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) és az [Azure Blob Service](https://msdn.microsoft.com/library/azure/dd179376.aspx)témakört.
> 
> 

## <a name="prerequisites"></a>Előfeltételek
A cikkben ismertetett feladatok végrehajtásához rendelkeznie kell egy Azure-előfizetéssel és egy beállított Azure Storage-fiókkal. Az adatok feltöltéséhez és letöltéséhez az Azure Storage-fiók neve és a fiók kulcsa szükséges.

* **Azure-előfizetés**beállításához tekintse meg az [ingyenes egy hónapos próbaverziót](https://azure.microsoft.com/pricing/free-trial/).
* A **Storage-fiók** létrehozásával, valamint a fiók-és a kulcsfontosságú információk beszerzésével kapcsolatos útmutatásért lásd: [Tudnivalók az Azure Storage-fiókokról](../../storage/common/storage-create-storage-account.md).

Az SSIS- **Összekötők**használatához le kell töltenie a következőket:

* **SQL Server 2014 vagy 2016 standard (vagy újabb)** : a telepítés magában foglalja a SQL Server Integration Services.
* **Microsoft SQL Server 2014 vagy 2016 Integration Services szolgáltatáscsomag az Azure**-hoz: ezek az összekötők a [SQL Server 2014 Integration services](https://www.microsoft.com/download/details.aspx?id=47366) és az [SQL Server 2016 Integration Services](https://www.microsoft.com/download/details.aspx?id=49492) oldalain tölthetők le.

> [!NOTE]
> SSIS az SQL Server telepítve van, de nem szerepel az Express verzió. További információ a SQL Server különböző kiadásaiban található alkalmazásokról: [SQL Server Editions](https://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/)
> 
> 

A SSIS kapcsolatos tananyagért lásd: [Hands on Training for SSIS](https://www.microsoft.com/sql-server/training-certification)

Az egyszerű kinyerési, átalakítási és betöltési (ETL) csomagok létrehozásával kapcsolatban a SISS használatával történő használatáról további információt a [SSIS oktatóanyag: egyszerű ETL-csomag létrehozása](https://msdn.microsoft.com/library/ms169917.aspx)című témakörben talál.

## <a name="download-nyc-taxi-dataset"></a>Töltse le a NYC Taxi adatkészlet
Az itt leírt példa egy nyilvánosan elérhető adathalmazt használ – a [New York-i taxis](https://www.andresmh.com/nyctaxitrips/) adatkészletet. Az adatkészlet készül 173 millió i taxik fel a következőt: az év 2013 áll. Az adatok két típusa van: trip adatokat és diszkont adatok részleteit. Mivel minden hónapban van egy fájl, a rendszer 24 fájlt tartalmaz, amelyek mindegyike körülbelül 2 GB-ot tömörít.

## <a name="upload-data-to-azure-blob-storage"></a>Adatfeltöltés az Azure blob storage-bA
Ha a helyszíni rendszerről az Azure Blob Storage-ba helyezi át az adatok áthelyezését a SSIS szolgáltatással, az [**Azure Blob feltöltési feladatának**](https://msdn.microsoft.com/library/mt146776.aspx)egy példányát használjuk, amely itt látható:

![Konfigurálja-data-adatelemzési – a virtuális gép](./media/move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)

A paraméterek, a feladat használja ebben a témakörben találhatók:

| Mező | Leírás |
| --- | --- |
| **AzureStorageConnection** |Megad egy meglévő Azure Storage-Csatlakozáskezelőt, vagy létrehoz egy újat, amely egy olyan Azure Storage-fiókra hivatkozik, amely a blob-fájlok tárolási helyét mutat. |
| **BlobContainer** |Annak a blob-tárolónak a nevét adja meg, amely blobként tárolja a feltöltött fájlokat. |
| **BlobDirectory** |Megadja a blob könyvtárat a blokkblobok, a feltöltött fájl tárolására. A blob-könyvtár virtuális hierarchikus struktúrát. Ha a blob már létezik, informatikai ia cserélni. |
| **LocalDirectory** |Megadja a helyi könyvtárat, amely tartalmazza a feltölteni kívánt fájlokat. |
| **FileName** |Megad egy jelölje ki a megadott név mintával rendelkező fájlokat. Például a MySheet\*. xls\* olyan fájlokat tartalmaz, mint a MySheet001. xls és a MySheetABC. xlsx. |
| **TimeRangeFrom/TimeRangeTo** |Megadja a tartomány Időszűrő. A *TimeRangeFrom* után módosított fájlok és a *TimeRangeTo* előtt is szerepelnek. |

> [!NOTE]
> A **AzureStorageConnection** hitelesítő adatait helyesnek kell lennie, és a **BlobContainer** az átvitel megkísérlése előtt léteznie kell.
> 
> 

## <a name="download-data-from-azure-blob-storage"></a>Töltse le az adatok Azure blob storage-ból
Az Azure Blob Storage-ból a helyszíni SSIS-be történő adatletöltéshez használja az [Azure Blob letöltési feladatának](https://msdn.microsoft.com/library/mt146779.aspx)egy példányát.

## <a name="more-advanced-ssis-azure-scenarios"></a>Speciális SSIS-Azure-forgatókönyvek
Az SSIS-feature pack lehetővé teszi, hogy komplex folyamatok által kezelendő csomagolási feladatok együtt. Sikerült a Blobadatok például közvetlenül egy HDInsight-fürtöt, vissza egy blobot, majd a helyszíni tárolók kimenete sikerült letölteni a hírcsatorna. SSIS futtathatja a Hive és Pig-feladatok további SSIS-összekötők használata egy HDInsight-fürtön:

* Ha SSIS-alapú Azure HDInsight-fürtön szeretné futtatni a kaptár-parancsfájlt, használja az [Azure HDInsight kaptár feladatot](https://msdn.microsoft.com/library/mt146771.aspx).
* Ha SSIS-alapú Azure HDInsight-fürtön szeretné futtatni a Pig-szkriptet, használja az [Azure HDInsight Pig feladatot](https://msdn.microsoft.com/library/mt146781.aspx).

