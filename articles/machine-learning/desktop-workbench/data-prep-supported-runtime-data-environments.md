---
title: Támogatott végrehajtási és környezetek kombinációját az Azure Machine Learning adat előkészített |} A Microsoft Docs
description: A dokumentum az Azure Machine Learning adat előkészített biztosít támogatott kombinációk különböző modulok és adatforrások teljes listája
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
ms.openlocfilehash: 9168ac1d26432ca3eee5a59b63aa0cec3ae72856
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989056"
---
# <a name="supported-matrix-for-this-release"></a>Ebben a kiadásban támogatott mátrix 

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Ha a kódot az Azure Machine Learning-adatforrások vagy az Azure Machine Learning adat előkészített, vagy egy Pandas első adatokat tölt, vagy Spark dataframe, kísérlet a következő fájlattribútumok számítási környezet és helyek támogatottak:

|     |Helyi fájlok  |Azure Blob Storage  |Az SQL Server adatbázis x  |
|---------|---------|---------|---------|---------|
|Helyi Python    |     Támogatott    |Nem támogatott         | Nem támogatott        |         |
|(A Linux rendszerű virtuális gép) docker-Python     |Soubory projektu: csak a támogatott *         | Nem támogatott        |        Nem támogatott |         |
|Docker (a Linux rendszerű virtuális gép) PySpark     |Soubory projektu: csak a támogatott *     |Támogatott         | Támogatott**        |         |
|Az Azure Data Science virtuális gép Python     |Soubory projektu: csak a támogatott *         |Nem támogatott         |Nem támogatott         |         |
|Az Azure Data Science virtuális gép PySPark     | Soubory projektu: csak a támogatott *        |Nem támogatott         |Nem támogatott         |         |
|Azure HDInsight PySpark     | Nem támogatott        |Támogatott         |Támogatott**         |         |
|Az Azure HDInsight – Python     | Nem támogatott        | Nem támogatott        | Nem támogatott        |         |

Az Azure Data Lake Store jelenleg nem támogatott a bármely számítási célnak.

Ha a fájl helyi elérési út szerepel, fájlt a projektben másolni a számítási környezetben, és ezután olvassa el a hiba. A projekt kívül nem másolódnak át, és az elérési utak már nem feloldja a számítási környezetben. Érdemes lehet adatokat forrás behelyettesítési, hogy a kód egy helyi fájlt is használhat, ha helyileg futtatja a. Ezután váltson egy másik futtatási konfigurációt az Azure Storage-blobba. Is használhatja mintavételi támogatási adatforrások fut csak bizonyos futtatási konfigurációt a nagy mennyiségű adat kezelésére.

** A Maven JDBC SQL Server-illesztőt 6.2.1 használja. Biztosítania kell, hogy ez a csomag (vagy annak egy kompatibilis) az a számítási környezet spark_dependencies.yml fájl tartalmazza.

Támogatja az Azure SQL Database vagy SQL Server megadott, az adatbázis elérhető, a számítási környezetben. 
