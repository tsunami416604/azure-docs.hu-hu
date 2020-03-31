---
title: Blob-tárolási adatok áthelyezése SSIS-összekötőkkel – Csapatadat-elemzési folyamat
description: Megtudhatja, hogyan helyezhet át adatokat az Azure Blob Storage-ba vagy onnan az AZURE-hoz készült SQL Server Integration Services Feature Pack használatával.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720871"
---
# <a name="move-data-to-or-from-azure-blob-storage-using-ssis-connectors"></a>Adatok áthelyezése az Azure Blob Storage-ba vagy onnan SSIS-összekötők használatával
Az [SQL Server Integration Services Feature Pack for Azure](https://msdn.microsoft.com/library/mt146770.aspx) összetevőket biztosít az Azure-hoz való csatlakozáshoz, az adatok Azure és helyszíni adatforrások közötti átviteléhez, valamint az Azure-ban tárolt adatok feldolgozásához.

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

Miután az ügyfelek áthelyezték a helyszíni adatokat a felhőbe, bármely Azure-szolgáltatásból hozzáférhetnek az adataikhoz, hogy kiaknázhassák az Azure-technológiák csomagjának teljes erejét. Az adatok at később, például az Azure Machine Learning vagy egy HDInsight-fürt.

Az Azure-erőforrások használatára példák az [SQL](sql-walkthrough.md) és a [HDInsight](hive-walkthrough.md) forgatókönyvek.

A hibrid adatintegrációs forgatókönyvekben gyakori üzleti igények teljesítéséhez az SSIS használatával készült kanonikus forgatókönyvekről a További műveletek az [Azure-hoz készült témakörben](https://blogs.msdn.com/b/ssis/archive/2015/06/25/doing-more-with-sql-server-integration-services-feature-pack-for-azure.aspx) talál témakört.

> [!NOTE]
> Az Azure blob storage teljes bemutatását az Azure Blob Alapjai és az Azure Blob Service című dokumentumban [tájékformulmában](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) [tájékot](https://msdn.microsoft.com/library/azure/dd179376.aspx)yajzat.
> 
> 

## <a name="prerequisites"></a>Előfeltételek
A jelen cikkben leírt feladatok végrehajtásához rendelkeznie kell egy Azure-előfizetéssel és egy Azure Storage-fiókkal. Az adatok feltöltéséhez vagy letöltéséhez szüksége van az Azure Storage-fiók nevére és a fiókkulcsra.

* **Az Azure-előfizetés**beállításáról az [Ingyenes egyhónapos próbaverzió](https://azure.microsoft.com/pricing/free-trial/)ban.
* A **tárfiók** létrehozásával, valamint a fiók- és kulcsinformációk beszerzésével kapcsolatos tudnivalókat [az Azure Storage-fiókok – tudnivalók.](../../storage/common/storage-create-storage-account.md)

Az **SSIS-csatlakozók**használatához a következőket kell letöltenie:

* **SQL Server 2014 vagy 2016 Standard (vagy újabb) :** A telepítés tartalmazza az SQL Server integrációs szolgáltatásokat.
* **Microsoft SQL Server 2014 vagy 2016 Integration Services Feature Pack for Azure:** Ezek az összekötők letölthetők az [SQL Server 2014 Integrációs szolgáltatások](https://www.microsoft.com/download/details.aspx?id=47366) és az SQL Server [2016 Integrációs szolgáltatások](https://www.microsoft.com/download/details.aspx?id=49492) lapról.

> [!NOTE]
> Az SSIS az SQL Server kiszolgálóval van telepítve, de nem része az Express verziónak. Az SQL Server különböző kiadásaiban található alkalmazásokról az [SQL Server Editions című témakörben](https://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/) talál további információt.
> 
> 

Az SSIS-sel kapcsolatos képzési anyagokat lásd: [Hands On Training for SSIS](https://www.microsoft.com/sql-server/training-certification)

Ha további információra van arról, hogy miként lehet a SISS segítségével egyszerű kinyerési, átalakítási és betöltési (ETL) csomagokat létrehozni, az [SSIS oktatóanyaga: Egyszerű ETL-csomag létrehozása](https://msdn.microsoft.com/library/ms169917.aspx)című témakörben talál további információt.

## <a name="download-nyc-taxi-dataset"></a>NYC Taxi adatkészlet letöltése
Az itt ismertetett példa egy nyilvánosan elérhető adatkészletet használ – a [NYC Taxi Trips](https://www.andresmh.com/nyctaxitrips/) adatkészletet. Az adatkészlet körülbelül 173 millió taxiból áll Nyc-ben 2013-ban. Kétféle adat létezik: az utazás részleteinek adatai és a viteldíjadatok. Mivel minden hónapban van egy fájl, 24 fájlunk van, amelyek mindegyike körülbelül 2 GB tömörítetlen.

## <a name="upload-data-to-azure-blob-storage"></a>Adatok feltöltése az Azure blob storage-ba
Ha az SSIS-szolgáltatáscsomaggal szeretnénk áthelyezni az adatokat a helyszíni adatokról az Azure blob storage-ba, az [**Azure Blob Upload Task**](https://msdn.microsoft.com/library/mt146776.aspx)egy példányát használjuk, amely et itt mutatjuk be:

![konfigurálás-adat-tudomány-vm](./media/move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)

A feladat által használt paramétereket itt ismerteti:

| Mező | Leírás |
| --- | --- |
| **AzureStorageConnection** |Egy meglévő Azure Storage Connection Manager, vagy létrehoz egy újat, amely egy Azure Storage-fiók, amely rámutat arra, ahol a blob fájlok at üzemelteti. |
| **BlobContainer tároló** |Megadja a blobtároló nevét, amely a feltöltött fájlokat blobként tárolja. |
| **BlobDirectory** |Megadja azt a blobkönyvtárat, ahol a feltöltött fájl blokkblobként tárolódik. A blob könyvtár egy virtuális hierarchikus struktúra. Ha a blob már létezik, ia helyébe. |
| **Helyi könyvtár** |Megadja a feltöltendő fájlokat tartalmazó helyi könyvtárat. |
| **Fájlnév** |A megadott névmintával rendelkező fájlok kijelöléséhez névszűrőt ad meg. A MySheet\*.xls\* például olyan fájlokat tartalmaz, mint a MySheet001.xls és a MySheetABC.xlsx |
| **Időtartományfrom/timerangeto** |Időtartomány-szűrőt ad meg. A *TimeRangeFrom* és *a TimeRangeTo* előtt módosított fájlok is szerepelnek. |

> [!NOTE]
> Az **AzureStorageConnection** hitelesítő adatokhelyesnek kell lenniük, és a **BlobContainer** léteznie kell az átvitel megkísérlése előtt.
> 
> 

## <a name="download-data-from-azure-blob-storage"></a>Adatok letöltése az Azure blob storage-ból
Ha adatokat szeretne letölteni az Azure blob storage-ból a helyszíni tárhelyre az SSIS-szel, használja az Azure Blob letöltési feladat egy [példányát.](https://msdn.microsoft.com/library/mt146779.aspx)

## <a name="more-advanced-ssis-azure-scenarios"></a>Fejlettebb SSIS-Azure-forgatókönyvek
Az SSIS szolgáltatáscsomag lehetővé teszi, hogy a csomagolási feladatok együttesen kezeljék az összetettebb folyamatokat. Például a blob adatok közvetlenül egy HDInsight-fürtbe, amelynek kimenete lehet letölteni egy blobot, majd a helyszíni tárolóba. Az SSIS további SSIS-összekötők használatával futtathatja a Hive- és a Pig-feladatokat egy HDInsight-fürtön:

* Ha Egy Hive-parancsfájlt szeretne futtatni egy SSIS-sel rendelkező Azure HDInsight-fürtön, használja az [Azure HDInsight Hive-feladatot.](https://msdn.microsoft.com/library/mt146771.aspx)
* Ha Egy SSIS-sel rendelkező Azure HDInsight-fürtön szeretne Pig-parancsfájlt futtatni, használja az [Azure HDInsight Pig-feladatot.](https://msdn.microsoft.com/library/mt146781.aspx)

