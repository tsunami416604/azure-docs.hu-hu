---
title: Az adatplatformok számára az adatelemző virtuális gép – Azure |} A Microsoft Docs
description: További tudnivalók az adatplatformok és a Data Science virtuális gépen támogatott eszközök.
keywords: adatelemzési eszközök, adatelemző virtuális gép, eszközök adatelemzéshez, linux adatelemzés
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: adaed5f15facb00b6ed624234ebad31f60782de1
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55094325"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>A Data Science virtuális gépen támogatott adatplatform

Az adatelemzési virtuális gép (DSVM) lehetővé teszi, hogy hozhat létre az elemzéseket az adatplatformok számos ellen. A DSVM felületek távoli azokat a platformokat, gyors fejlesztési lehetőségeket és prototípus-készítés helyi példányt nyújt. 

Az alábbiakban a DSVM támogatott adatplatformeszközeinkkel. 

## <a name="sql-server-2016-developer-edition"></a>SQL Server 2016 Developer Edition

| | |
| ------------- | ------------- |
| Mi ez?   | Egy helyi relációs adatbázis-példányt      |
| Támogatott DSVM-kiadások      | Windows      |
| Gyakori használati      | Gyors fejlesztés helyileg kisebb adatkészlet <br/> Futtassa az adatbázis-R   |
| A minták mutató hivatkozások      |    New York City adatkészlet néhányat példaként betöltése az SQL database-be `nyctaxi`. <br/> A Microsoft R- és adatbázis-analytics Jupyter példa tekinthet meg:<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| A dsvm-hez kapcsolódó eszközök       | SQL Server Management Studio <br/> ODBC/JDBC illesztőprogramok<br/> a pyodbc RODBC<br />Apache Drill      |

> [!NOTE]
> Az SQL Server 2016 developer Edition verziót csak fejlesztési és tesztelési célokra használható. Licenc vagy egy, az SQL Serveres virtuális gépek üzemi környezetben való futtatás szükséges. 


### <a name="setup"></a>Beállítás

Az adatbázis-kiszolgáló már előre konfigurálva van, és a kapcsolódó SQL Server a Windows-szolgáltatások (például `SQL Server (MSSQLSERVER)`) automatikus futásra vannak beállítva. A csak manuális lépés futtatásának engedélyezése az adatbázison belüli elemzések használatával a Microsoft R. Ehhez futtassa a következő parancsot egy alkalommal a gép rendszergazdaként a bejelentkezés után az SQL Server Management Studio (SSMS) művelet nyílt "Új lekérdezés" az ssms-ben is, győződjön meg, hogy a kijelölt adatbázis `master` , majd futtassa: 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Please replace the %COMPUTERNAME% with your VM name)
       
Annak érdekében, hogy az SQL Server Management Studióban futtassa, keressen a "SQL Server Management Studio" a program listában, vagy használja a Windows Search található, majd futtassa. Amikor a rendszer kéri a hitelesítő adatokat, válassza a "Windows-hitelesítés", és használja a számítógépnév vagy ```localhost``` SQL-kiszolgáló nevét. 

### <a name="how-to-use--run-it"></a>Hogyan futtathatja / használata?  

Alapértelmezés szerint automatikusan fut az adatbázis-kiszolgáló az az alapértelmezett adatbázispéldányhoz. A virtuális gép eszközökkel, mint például az SQL Server Management Studio használatával helyben SQL Server adatbázisának elérésére. Helyi rendszergazdai fiókot rendszergazdai hozzáféréssel rendelkezik az adatbázishoz. 

A dsvm-hez is tartalmaz felvenni a kapcsolatot az SQL Server, az Azure SQL Database, és az Azure SQL Data Warehouse több, többek között a Python, r nyelven írt alkalmazások ODBC-illesztőprogramok és a JDBC-illesztőprogramok 

### <a name="how-is-it-configured--installed-on-the-dsvm"></a>Hogyan van azt konfigurálni / telepíteni a dsvm-hez? 

Az SQL Server telepítve van a szokásos módon. Címen található `C:\Program Files\Microsoft SQL Server`. Az R In-database-példány a következő címen található `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES`. A dsvm-hez is rendelkezik egy külön önálló R Server-példány telepített `C:\Program Files\Microsoft\R Server\R_SERVER`. Ezek a példányok két-R ne ossza meg a kódtárakat.


## <a name="apache-spark-2x-standalone"></a>Az Apache Spark-2.x (önálló)

| | |
| ------------- | ------------- |
| Mi ez?   | A népszerű Apache Spark-platform, a rendszer a gyors nagyméretű adatfeldolgozás és a machine learning (egyetlen csomópont folyamaton belüli) önálló példányának     |
| Támogatott DSVM-kiadások      | Linux <br /> Windows (kísérleti funkció)      |
| Gyakori használati      | * Spark-/ PySpark-alkalmazások helyi kisebb adatkészlettel, és később gyors fejlesztését üzembe helyezni a nagy méretű, például az Azure HDInsight Spark-fürtökön<br/> * A Microsoft R Server Spark tesztkörnyezetben <br />* A könnyen használható, vagy a Microsoft nyílt forráskódú használatát [MMLSpark](https://github.com/Azure/mmlspark) library gépi Tanulási alkalmazások létrehozásához  |
| A minták mutató hivatkozások      |    Jupyter-minta: <br />&nbsp;&nbsp;* ~/notebooks/SparkML/pySpark <br /> &nbsp;&nbsp;* ~/notebooks/MMLSpark <br /> A Microsoft R Server (a Spark környezet): /dsvm/samples/MRS/MRSSparkContextSample.R |
| A dsvm-hez kapcsolódó eszközök       | PySpark, Scala<br/>Jupyter (Spark-/ PySpark-kernelek)<br/>A Microsoft R Server, SparkR, Sparklyr <br />Apache Drill      |

### <a name="how-to-use-it"></a>Hogyan használható a
A Spark futtathatja a parancssorban a Spark-feladatok elküldése `spark-submit` vagy `pyspark` parancsokat. Jupyter notebook hozzon létre egy új jegyzetfüzetet a Spark-kernel is létrehozhat. 

Az R-kódtárak SparkR, a Sparklyr vagy a Microsoft R Server elérhető a DSVM használata Spark is használhatja. Tekintse meg az előző táblázatban szereplő minták mutatókat tartalmaznak. 

> [!NOTE]
> Az Ubuntu Linux-DSVM kiadás csak támogatott futó Microsoft R Server DSVM-, Spark-környezetben. 



### <a name="setup"></a>Beállítás
A Spark környezet a Microsoft R Server Ubuntu Linux-DSVM Edition rendszeren fut, mielőtt kell tennie a telepítő lépést ahhoz, hogy a helyi Hadoop HDFS és Yarn-példány egyetlen csomópont csak egyszer. Alapértelmezés szerint Hadoop-szolgáltatásokhoz vannak telepítve, de a dsvm-hez a letiltva. Az engedélyezéshez, először futtassa a következő parancsokat rendszergazdaként kell:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

Állítsa le a Hadoop kapcsolódó szolgáltatások, amikor nem kell őket futtatásával ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn``` egy minta bemutatja, hogyan lehet fejlesztési és tesztelési Asszony (amely a különálló a dsvm-hez a Spark-példányt) távoli Spark környezetben megadott és elérhető a `/dsvm/samples/MRS` a könyvtár. 


### <a name="how-is-it-configured--installed-on-the-dsvm"></a>Hogyan van azt konfigurálni / telepíteni a dsvm-hez? 
|Platform|Telepítési hely ($SPARK_HOME)|
|:--------|:--------|
|Windows | c:\dsvm\tools\spark-X.X.X-bin-hadoopX.X|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


Az alkalmazandó kódtárak el az adatokat az Azure Blob- vagy Azure Data Lake storage (ADLS) és a Microsoft MMLSpark machine learning kódtárak használatával $SPARK_HOME/jegyzékfájlok vannak telepítve. Spark indulásakor a rendszer automatikusan betölti a JAR-fájlok kivételével. Alapértelmezés szerint a Spark használja a helyi lemezen lévő adatokat. Ahhoz, hogy a Spark-példányt az Azure blob vagy ADLS tárolt adatok elérését a DSVM létrehozni és konfigurálni kell a `core-site.xml` fájl található $SPARK_HOME/conf/core-site.xml.template a sablon alapján (Ha a Blob és ADLS helyőrzők konfigurációk) az Azure blob és az Azure Data Lake Storage megfelelő hitelesítő adatokkal. Látja, hogy az ADLS szolgáltatás hitelesítő adatainak létrehozásakor lépéseket részletes [Itt](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory#create-an-active-directory-application). Miután megadta a hitelesítő adatokat az Azure blob vagy ADLS a core-site.xml fájlban, az URI-előtag a wasb forrásokra tárolt adatokat is lehet hivatkozni: / / vagy az adl: / /. 

