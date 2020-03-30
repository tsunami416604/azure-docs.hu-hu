---
title: Támogatott adatplatformok
titleSuffix: Azure Data Science Virtual Machine
description: Ismerje meg az Azure Data Science virtuális gép támogatott adatplatformjait és eszközeit.
keywords: adatelemzési eszközök, adatelemző virtuális gép, eszközök adatelemzéshez, linux adatelemzés
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: cd787881957d78f179107e46b2650de4618c7724
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282324"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>Az adatelemzési virtuális gépen támogatott adatplatformok

Az adatelemzési virtuális gép (DSVM) segítségével az adatplatformok széles körére hozhat létre elemzéseket. A távoli adatplatformokhoz való kapcsolódási pontok mellett a DSVM helyi példányt biztosít a gyors fejlesztéshez és prototípus-készítéshez.

A dsvm a következő adatplatform-eszközöket támogatja.

## <a name="sql-server-developer-edition"></a>SQL Server Developer Edition

| | |
| ------------- | ------------- |
| Mi ez?   | Helyi relációs adatbázispéldány      |
| Támogatott DSVM-kiadások      | Windows 2016: SQL Server 2017, Windows 2019: SQL Server 2019      |
| Tipikus felhasználások      | Gyors fejlesztés helyileg kisebb adatkészlettel <br/> Adatbázison belüli R futtatása   |
| A mintákra mutató hivatkozások      |    Egy New York-i adatkészlet egy kis mintája betöltődik az SQL-adatbázisba:<br/>  `nyctaxi` <br/> A Jupyter minta, amely a Microsoft Machine Learning Server t és az adatbázison belüli elemzéseket mutatja be, a következő helyen található:<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| Kapcsolódó eszközök a DSVM       | SQL Server Management Studio <br/> ODBC/JDBC illesztőprogramok<br/> pyodbc, RODBC<br />Apache Drill      |

> [!NOTE]
> Az SQL Server Developer Edition csak fejlesztési és tesztelési célokra használható. Az éles környezetben való futtatásához licencre vagy az SQL Server egyik virtuális gépére van szükség.


### <a name="setup"></a>Telepítés

Az adatbázis-kiszolgáló már előre van konfigurálva, és `SQL Server (MSSQLSERVER)`az SQL Server rel kapcsolatos Windows-szolgáltatások (például ) automatikusan futnak. Az egyetlen manuális lépés az adatbázison belüli elemzés engedélyezése a Microsoft Machine Learning Server használatával. Az analitikát úgy engedélyezheti, hogy a következő parancsot egyszeri műveletként futtatja az SQL Server Management Studio (SSMS) rendszerben. Futtassa ezt a parancsot, miután bejelentkezett a számítógép rendszergazdájaként, `master`nyisson meg egy új lekérdezést az SSMS-ben, és győződjön meg arról, hogy a kijelölt adatbázis:

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Replace %COMPUTERNAME% with your VM name.)
       
Az SQL Server Management Studio futtatásához keressen rá az "SQL Server Management Studio" kifejezésre a programlistában, vagy a Windows Search segítségével keresse meg és futtassa azt. Amikor hitelesítő adatokat kér, válassza a **Windows-hitelesítés** lehetőséget, és használja a számítógép nevét vagy ```localhost``` az SQL Server **Name** mezőt.

### <a name="how-to-use-and-run-it"></a>Hogyan kell használni és futtatni

Alapértelmezés szerint az alapértelmezett adatbázis-példányt tartalmazó adatbázis-kiszolgáló automatikusan fut. A virtuális gépEN található SQL Server Management Studio eszközök kel-kel helyileg érheti el az SQL Server adatbázist. A helyi rendszergazdai fiókok rendszergazdai hozzáféréssel rendelkeznek az adatbázishoz.

Emellett a DSVM odbc és JDBC illesztőprogramokkal rendelkezik, amelyek az SQL Server, az Azure SQL-adatbázisok és az Azure SQL Data Warehouse segítségével több nyelven írt alkalmazásokból, például a Python és a Machine Learning Server alkalmazásokkal is beszélgetnek.

### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Hogyan van beállítva és telepítve a DSVM? 

 Az SQL Server a szokásos módon van telepítve. Megtalálható a . `C:\Program Files\Microsoft SQL Server` Az adatbázison belüli Machine Learning `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES`Server példány a található. A DSVM külön önálló Machine Learning Server-példányt `C:\Program Files\Microsoft\R Server\R_SERVER`is rendelkezik, amely a rendszerben van telepítve. Ez a két Machine Learning Server-példány nem oszt meg könyvtárakat.


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2.x (önálló)

| | |
| ------------- | ------------- |
| Mi ez?   | A népszerű Apache Spark platform önálló (folyamaton ként iható egyetlen csomópont) példánya; a gyors, nagy léptékű adatfeldolgozáshoz és gépi tanuláshoz     |
| Támogatott DSVM-kiadások      | Linux     |
| Tipikus felhasználások      | * A Spark/PySpark alkalmazások gyors fejlesztése helyileg kisebb adatkészlettel és későbbi telepítéssel nagy Spark-fürtökön, például az Azure HDInsight-on<br/> * Microsoft Machine Learning Server Spark környezet tesztelése <br />* Használja a SparkML vagy a Microsoft nyílt forráskódú [MMLSpark](https://github.com/Azure/mmlspark) könyvtárát ml-alkalmazások létrehozásához |
| A mintákra mutató hivatkozások      |    Jupyter minta: <br />&nbsp;&nbsp;* ~/notebookok/SparkML/pySpark <br /> &nbsp;&nbsp;* ~/notebookok/MMLSpark <br /> Microsoft Machine Learning Server (Spark-környezet): /dsvm/samples/MRS/MRSSparkContextSample.R |
| Kapcsolódó eszközök a DSVM       | PySpark, Scala<br/>Jupyter (Spark/PySpark kernelek)<br/>Microsoft Machine Learning Server, SparkR, Sparklyr <br />Apache Drill      |

### <a name="how-to-use-it"></a>Használat
A parancs vagy parancs `spark-submit` futtatásával spark-feladatokat küldhet a `pyspark` parancssorba. Jupyter-jegyzetfüzetet is létrehozhat, ha új jegyzetfüzetet hoz létre a Spark kernelnel.

A Spark from R használatával, például a SparkR, a Sparklyr és a Microsoft Machine Learning Server használatával, amelyek a DSVM-en érhetők el. Tekintse meg az előző táblázatban szereplő mintákra mutató mutatókat.

### <a name="setup"></a>Telepítés
Mielőtt egy Spark-környezetben futna a Microsoft Machine Learning Server-ben ubuntu Linux DSVM-kiadáson, el kell végeznie egy egyszeri beállítási lépést, hogy egy helyi egyetlen csomópont Hadoop HDFS és Yarn példány. Alapértelmezés szerint a Hadoop-szolgáltatások telepítve vannak, de le vannak tiltva a DSVM-en. Az engedélyezésükhöz első alkalommal futtassa a következő parancsokat gyökérként:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

Leállíthatja a Hadoop-hoz kapcsolódó szolgáltatásokat, ha ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```már nincs rájuk szüksége a futtatásával.

Egy minta, amely bemutatja, hogyan fejleszthet és tesztelhet mrs egy távoli Spark-környezetben (amely `/dsvm/samples/MRS` a DSVM önálló Spark-példánya) rendelkezésre áll, és elérhető a címtárban.


### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Hogyan van beállítva és telepítve a DSVM? 
|Platform|Telepítési hely ($SPARK_HOME)|
|:--------|:--------|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


Az Azure Blob storage-ból vagy az Azure Data Lake Storage-ból származó adatok eléréséhez a Microsoft MMLSpark gépi tanulási kódtárak használatával való hozzáféréstára imassa elő van telepítve $SPARK_HOME/üvegekben. Ezek a JARs automatikusan betöltődnek, amikor a Spark elindul. Alapértelmezés szerint a Spark a helyi lemezen lévő adatokat használja. 

A Spark-példány a DSVM a Blob storage vagy az Azure Data Lake Storage `core-site.xml` tárolt adatok eléréséhez, létre kell hoznia és konfigurálnia kell a fájlt a $SPARK_HOME/conf/core-site.xml.template található sablon alapján. A Blob storage és az Azure Data Lake Storage eléréséhez is rendelkeznie kell a megfelelő hitelesítő adatokkal. (Vegye figyelembe, hogy a sablonfájlok helyőrzőket használnak a Blob storage és az Azure Data Lake Storage konfigurációkhoz.)

Az Azure Data Lake Storage-szolgáltatás hitelesítő adatainak létrehozásáról a [Hitelesítés az Azure Data Lake Storage gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory)című témakörben talál további információt. Miután a Blob storage vagy az Azure Data Lake Storage hitelesítő adatait beírta a core-site.xml fájlba, hivatkozhat az ezekben a forrásokban tárolt adatokra a wasb:// vagy adl:// URI-előtagja in.

