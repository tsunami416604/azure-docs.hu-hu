---
title: Terminológia – Azure szinapszis Analytics (munkaterületek – előzetes verzió)
description: Útmutató a felhasználónak az Azure szinapszis Analytics szolgáltatással való bejárásához
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 11/18/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 4d69c1d69de63dc0d4c3c8f8e46e73f9efc72933
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2020
ms.locfileid: "95244718"
---
# <a name="azure-synapse-analytics-terminology"></a>Az Azure szinapszis Analytics terminológiája

[!INCLUDE [preview](includes/note-preview.md)]

Ez a dokumentum végigvezeti az Azure szinapszis Analytics alapfogalmait.

## <a name="basics"></a>Alapbeállítások

A **szinapszis munkaterület** biztonságos együttműködési határ az Azure-beli felhőalapú vállalati elemzésekhez. A munkaterület egy adott régióban van üzembe helyezve, és rendelkezik egy társított ADLS Gen2 fiókkal és fájlrendszerrel (az ideiglenes adattárolók tárolásához). A munkaterület egy erőforráscsoport alatt található.

A munkaterület lehetővé teszi, hogy az SQL és az Apache Spark használatával végezzen elemzéseket. Az SQL és a Spark Analytics számára elérhető erőforrások SQL-és Spark- **készletekbe** vannak rendezve. 

## <a name="linked-services"></a>Társított szolgáltatások

A munkaterület tetszőleges számú **társított szolgáltatást** tartalmazhat, amelyek lényegében kapcsolati karakterláncokat határoznak meg, amelyek meghatározzák a munkaterület külső erőforrásokhoz való kapcsolódásához szükséges kapcsolati adatokat.

## <a name="synapse-sql"></a>Synapse SQL

A **SZINAPSZIS SQL** lehetővé teszi a T-SQL-alapú elemzések elvégzését a szinapszis munkaterületen. A szinapszis SQL két felhasználási modellt tartalmaz: dedikált és kiszolgáló nélküli.  A dedikált modell esetében használjon **DEDIKÁLT SQL-készleteket**. A munkaterület tetszőleges számú készlettel rendelkezhet. A kiszolgáló nélküli modell használatához használja a kiszolgáló nélküli **SQL-készleteket**. Minden munkaterülethez tartozik egy ilyen készlet.

* **SQL-kérelem** – művelet, például egy lekérdezés dedikált SQL-készleten vagy kiszolgáló nélküli SQL-készleten keresztüli futtatása.
* **SQL-parancsfájl** – egy fájlba mentett SQL-parancsok készlete. Egy SQL-parancsfájl egy vagy több SQL-utasítást is tartalmazhat. Az SQL-kérelmeket dedikált SQL-készlet vagy kiszolgáló nélküli SQL-készlet használatával futtathatja.

## <a name="apache-spark-for-synapse"></a>A szinapszis Apache Spark

A Spark Analytics használatához hozzon létre és használjon **kiszolgáló nélküli Apache Spark készleteket** a szinapszis munkaterületen.

* **Apache Spark a szinapszisok számára** – a Spark futási ideje kiszolgáló nélküli Spark-készletben használt. A jelenleg támogatott verzió a Spark 2,4 a Python 3.6.1, a Scala 2.11.12, a .NET-támogatás a Apache Spark 0,5 és a Delta Lake 0,3.  
* **Apache Spark készlet** – 0 – N Spark által kiépített, a hozzájuk tartozó adatbázisokkal rendelkező erőforrások a munkaterületen helyezhetők üzembe. A Spark-készlet lehet automatikusan szüneteltethető, folytatható és méretezhető.  
* **Spark-alkalmazás** – egy illesztőprogram-folyamatból és egy végrehajtó folyamatokból áll. A Spark-alkalmazások kiszolgáló nélküli Spark-készleten futnak.            
* Spark- **munkamenet**– a Spark-alkalmazás egyesített belépési pontja. Lehetővé teszi a Spark különböző funkcióinak és kisebb számú szerkezetének kezelését. Jegyzetfüzet futtatásához létre kell hoznia egy munkamenetet. Egy munkamenet konfigurálható úgy, hogy egy adott méretű, meghatározott számú végrehajtón fusson. A notebook-munkamenet alapértelmezett konfigurációja 2 közepes méretű végrehajtón fut.
* **Notebook** -interaktív és reaktív adatelemzési és-mérnöki felület, amely támogatja a Scala, a PySpark, a C# és a SparkSQL.
* **Spark Job definition** – a felületet, amely a kódot és annak függőségeit tartalmazó szerelvény-jar-feladatokat küld el a Spark-feladatokhoz.

## <a name="pipelines"></a>Pipelines

* **Adatintegráció** – lehetővé teszi az adatgyűjtést a különböző források között, és a munkaterületen belül vagy a munkaterületen kívül futó tevékenységek szervezését.
* **Adatfolyam** – teljes körű vizuális élményt nyújt Big Data átalakításhoz szükséges kódolás nélkül. Az optimalizálást és a végrehajtást kiszolgáló nélküli módon kezeljük.
* **Folyamat** – a tevékenységeket végrehajtó tevékenységek logikai csoportosítása.
* **Tevékenység** – az adatokon végrehajtandó műveletek, például adatok másolása, jegyzetfüzetek vagy SQL-parancsfájlok futtatása.
* **Trigger** – folyamat végrehajtása. Futtatható manuálisan vagy automatikusan (Schedule, bukdácsoló ablak vagy eseményvezérelt)
* **Integrációs adatkészlet** – olyan adatok elnevezett nézete, amelyek egyszerűen rámutatnak vagy hivatkoznak egy tevékenységben bemenetként és kimenetként használandó adatokra. Egy társított szolgáltatáshoz tartozik.

## <a name="next-steps"></a>Következő lépések

* [Ismerkedés az Azure szinapszis Analytics szolgáltatással](get-started.md)
* [Munkaterület létrehozása](quickstart-create-workspace.md)
* [Kiszolgáló nélküli SQL-készlet használata](quickstart-sql-on-demand.md)

