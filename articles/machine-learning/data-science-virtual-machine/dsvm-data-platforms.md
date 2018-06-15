---
title: Az adatok tudományos virtuálisgép - Azure adatok platformok |} Microsoft Docs
description: Adatok platformok esetében az adatok tudományos virtuális gép.
keywords: adatok tudományos eszközök, adatok tudományos virtuális gép, adattudomány, linux adattudomány eszközei
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: b3f340006801287383c2afb2924706affbd77a51
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31411029"
---
# <a name="data-platforms"></a>Adatplatformok

A tudományos virtuális gép (DSVM) lehetővé teszi a analytics számos különböző adatok platformok elleni létrehozásához. Távoli adatok platformok felületet, mellett a DSVM biztosít a gyors fejlesztés és prototípusának helyi példányát. 

Az alábbiakban a támogatott a DSVM adatok platform eszközök. 

## <a name="sql-server-2016-developer-edition"></a>SQL Server 2016 Developer Edition

| | |
| ------------- | ------------- |
| Mi ez?   | Helyi relációs adatbázispéldány      |
| Támogatott DSVM kiadás      | Windows      |
| A gyakori felhasználási      | Gyors fejlesztést helyileg a kisebb adatkészlet <br/> Futtassa az adatbázis-K   |
| Minták mutató hivatkozások      |    Az SQL-adatbázis-bA betöltött New York City adatkészlet mintát `nyctaxi`. <br/> Microsoft R és az adatbázis-elemzések Jupyter minta helyen találhatók:<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| A DSVM a kapcsolódó eszközök       | SQL Server Management Studio <br/> ODBC/JDBC illesztőprogramok<br/> pyodbc, RODBC<br />Apache részletezés      |

> [!NOTE]
> Az SQL Server 2016 developer kiadásában csak fejlesztési és tesztelési célokra használható. Licenc vagy egy SQL Server virtuális gépek éles környezetben futtatásához szükséges. 


### <a name="setup"></a>Beállítás

Az adatbázis-kiszolgáló már előre be van állítva, és a központi Windows-szolgáltatások kapcsolódó SQL Server (például `SQL Server (MSSQLSERVER)`) automatikus futtatásra vannak beállítva. A csak manuális lépés futtatásának az, hogy adatbázis-analytics Microsoft R. használatának engedélyezése Ehhez futtassa a következő parancs csak egyszer módosítható az SQL Server Management Studio (SSMS) művelet után a gép rendszergazdaként bejelentkezett nyílt "Új lekérdezés" szolgáltatáshoz az ssms, a kijelölt adatbázis nem biztosítására `master` , majd futtassa: 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Please replace the %COMPUTERNAME% with your VM name)
       
Ahhoz, hogy fut az SQL Server Management Studio eszközt, keresse meg a program listán az "SQL Server Management Studio", vagy használja a Windows keresési keresse meg és futtassa azt. Ha a hitelesítő adatok megadását kéri, válassza a "Windows-hitelesítés", és használja a számítógépnév vagy ```localhost``` az SQL Server neve. 

### <a name="how-to-use--run-it"></a>Hogyan használja az / futtatni?  

Az adatbázis-kiszolgálón, az alapértelmezett adatbázis-példány alapértelmezés szerint automatikusan fut. A virtuális Gépre például az SQL Server Management Studio segítségével érik el az SQL Server-adatbázist helyileg. Helyi rendszergazdai fiók rendszergazdai hozzáféréssel rendelkezik az adatbázishoz. 

A DSVM is részeként elérhető ODBC-illesztőprogramok és JDBC illesztőprogramok felvegye a SQL Server, az Azure SQL-adatbázisok, és az Azure SQL Data Warehouse a Python, R. például különböző nyelveken írt alkalmazások 

### <a name="how-is-it-configured--installed-on-the-dsvm"></a>Hogyan van azt konfigurált / a DSVM telepítve? 

Az SQL Server telepítve van a szokásos módon. Helyen találhatók `C:\Program Files\Microsoft SQL Server`. Az adatbázis-része az R-példány a következő címen található `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES`. A DSVM is rendelkezik egy külön önálló R Server-példány telepített `C:\Program Files\Microsoft\R Server\R_SERVER`. Ezek a két-R-példányok nem ugyanazt a könyvtárak.


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2.x (önálló)

| | |
| ------------- | ------------- |
| Mi ez?   | Egy önálló (egycsomópontos folyamaton belüli) példánya a népszerű Apache Spark-platform, gyors nagy méretű adatok feldolgozása és a gépi tanulás     |
| Támogatott DSVM kiadás      | Linux <br /> Windows (kísérleti)      |
| A gyakori felhasználási      | * A Spark/PySpark alkalmazások helyileg kisebb adatkészlet vagy későbbi gyors fejlesztést nagy, például az Azure HDInsight Spark-fürtök a telepítéshez<br/> * Microsoft R Server Spark tesztkörnyezetben <br />* Használja SparkML vagy a Microsoft nyílt forráskódú [MMLSpark](https://github.com/Azure/mmlspark) ML alkalmazásokat hozhatnak létre könyvtár  |
| Minták mutató hivatkozások      |    Jupyter minta: <br />&nbsp;&nbsp;* ~/notebooks/SparkML/pySpark <br /> &nbsp;&nbsp;* ~/notebooks/MMLSpark <br /> Microsoft R Server (Spark környezet): /dsvm/samples/MRS/MRSSparkContextSample.R |
| A DSVM a kapcsolódó eszközök       | PySpark, Scala<br/>Jupyter (Spark/PySpark mag)<br/>Microsoft R Server, SparkR, Sparklyr <br />Apache részletezés      |

### <a name="how-to-use-it"></a>Hogy miképpen lehet vele
Spark futtathatja a parancssorban a Spark-feladatok elküldésekor `spark-submit` vagy `pyspark` parancsok. Hozzon létre egy újat a Spark kernel a Jupyter notebook is létrehozhat. 

Az R könyvtárak SparkR, Sparklyr vagy Microsoft R Server, a DSVM elérhető használata Spark is használhatja. Tekintse meg az előző táblázatban szereplő minták mutató hivatkozások. 

> [!NOTE]
> A Microsoft R Server DSVM Spark környezetében az Ubuntu Linux DSVM kiadás csak támogatott. 



### <a name="setup"></a>Beállítás
Futtatása Spark környezetben Microsoft R Server, az Ubuntu Linux DSVM edition, előtt kell tennie telepítő lépés ahhoz, hogy egy helyi csomóponthoz Hadoop HDFS és a Yarn csak egyszer módosítható. Alapértelmezés szerint Hadoop szolgáltatások vannak telepítve, de a DSVM a letiltva. Ahhoz, hogy az engedélyezéséhez először futtassa az alábbi parancsokat rendszergazdaként kell:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

A Hadoop leállíthatja kapcsolódó szolgáltatások, ha már nincs szükség futtatásával ````systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```` egy minta bemutatja, hogyan lehet fejlesztéséhez és teszteléséhez Asszony távoli Spark környezetben (amely a DSVM önálló Spark-példányhoz) egy megadott és elérhető a a `/dsvm/samples/MRS` könyvtár. 


### <a name="how-is-it-configured--installed-on-the-dsvm"></a>Hogyan van azt konfigurált / a DSVM telepítve? 
|Platform|Telepítési helyet ($SPARK_HOME)|
|:--------|:--------|
|Windows | c:\dsvm\tools\spark-X.X.X-bin-hadoopX.X|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


Szalagtárak adatok eléréséhez Azure Blob vagy Azure Data Lake-(ADLS-) és a Microsoft MMLSpark gépi tanulás könyvtárak segítségével előre vannak telepítve $SPARK_HOME/JAR-fájlok kivételével. Spark indításakor automatikusan tölti be a JAR-fájlok kivételével. Alapértelmezés szerint a Spark használja a helyi lemezen lévő adatokat. Ahhoz, hogy a hozzáférési adatok Azure blob vagy ADLS tárolt DSVM Spark-példányban létrehozása/konfigurálni kell a `core-site.xml` fájl található $SPARK_HOME/conf/core-site.xml.template a sablon alapján (ha vannak a helyőrzők Blob és ADLS konfigurációk) az Azure blob és az Azure Data Lake tárolási megfelelő hitelesítő adatokkal. ADLS szolgáltatás hitelesítő adatainak létrehozásakor a lépések részletes talált [Itt](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory#create-an-active-directory-application). Miután az Azure blob vagy ADLS hitelesítőadatok meg legyenek adva, a core-site.xml fájlban, a wasb URI-előtag forrásokra tárolt adatokat is hivatkozni: / / vagy az adl: / /. 

