---
title: Livy a Spark használata Azure HDInsight alapú Spark-fürt-feladatok elküldése
description: Ismerje meg, hogyan lehet Apache Spark REST API segítségével távolról egy Azure HDInsight-fürtön a Spark-feladatok elküldése.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 6a0a2dec6beeecca3779f4b047d3b5fe6295a1e6
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52495303"
---
# <a name="use-apache-spark-rest-api-to-submit-remote-jobs-to-an-hdinsight-spark-cluster"></a>Az Apache Spark REST API használata egy HDInsight Spark-fürt távoli feladatok elküldéséhez

Ismerje meg, hogyan használható [Apache Livy](https://livy.incubator.apache.org/), a [Apache Spark](https://spark.apache.org/) REST API-t, amely egy Azure HDInsight Spark-fürt távoli feladatok elküldéséhez használható. További részletes dokumentációt: [ http://livy.incubator.apache.org/ ](http://livy.incubator.apache.org/).

Livy használatával futtassa az interaktív Spark parancskörnyezet vagy futtatható Spark, a batch-feladatok elküldése. Ez a cikk ismerteti a batch-feladatok elküldése a Livy használatával. A kódrészletek ebben a cikkben a cURL használatával REST API-hívásokat a Livy Spark-végpontra.

**Előfeltételek:**

* Apache Spark-fürt megléte a HDInsightban. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).

* [cURL](http://curl.haxx.se/). Ez a cikk a curl használatával bemutatják, hogyan lehet REST API-hívásokat egy HDInsight Spark-fürt ellen.

## <a name="submit-an-apache-livy-spark-batch-job"></a>Az Apache Spark-Livy batch-feladatok elküldése
Mielőtt egy batch-feladatot, fel kell tölteni a fürthöz társított fürt tárolására az alkalmazás jar. Ehhez az [**AzCopy**](../../storage/common/storage-use-azcopy.md) parancssori segédprogramot használhatja. Nincsenek használatával töltse fel az adatokat különböző ügyfelek számára. További információk a csatlakoztatásukról annak [Upload data for HDInsight az Apache Hadoop-feladatok](../hdinsight-upload-data.md).

    curl -k --user "<hdinsight user>:<user password>" -v -H <content-type> -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches' -H "X-Requested-By: admin"

**Példák**:

* Ha a jar-fájlt a fürt storage (WASB)
  
        curl -k --user "admin:mypassword1!" -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
* Ha szeretné a jar-fájl nevére, és a classname fázis részeként egy bemeneti fájlt (ebben a példában input.txt)
  
        curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"

## <a name="get-information-on-livy-spark-batches-running-on-the-cluster"></a>Ismerje meg a fürtben futó Livy Spark-kötegek információit
    curl -k --user "<hdinsight user>:<user password>" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"

**Példák**:

* Ha azt szeretné, a fürtben futó Livy Spark köteg lekérdezheti az összes:
  
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches" 
* Ha szeretné lekérdezni egy adott batch-egy adott kötegazonosító
  
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## <a name="delete-a-livy-spark-batch-job"></a>Livy Spark batch-feladat törlése
    curl -k --user "<hdinsight user>:<user password>" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"

**Példa**:

    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## <a name="livy-spark-and-high-availability"></a>Livy Spark és a magas rendelkezésre állás
Livy biztosít magas rendelkezésre állású Spark a fürtön futó feladatok. Íme néhány példa.

* Ha a Livy szolgáltatás leáll, miután elküldte távolról egy feladatot egy Spark-fürtöt, a feladat fut a háttérben folytatódik. Ha Livy biztonsági mentése, a feladat és a jelentések, a biztonsági állapotát állítja vissza.
* Jupyter notebookok a HDInsight Livy működteti a háttérben. Ha egy jegyzetfüzet egy Spark-feladat fut, és a Livy-szolgáltatás újraindítása lekérdezi, a notebook továbbra is fut a kód cellákat. 

## <a name="show-me-an-example"></a>Példa megjelenítése
Ebben a szakaszban áttekintjük, példák, Livy Spark használata a batch-feladat elküldése, a feladat előrehaladásának figyeléséhez, és törölje azt. Az alkalmazás ebben a példában használjuk a fejlesztett ki, a cikkben egy [hozzon létre egy önálló Scala-alkalmazások és a HDInsight Spark-fürt futtatásához](apache-spark-create-standalone-application.md). Itt lépései azt feltételezik, hogy:

* Már másolta át az alkalmazás jar a fürthöz társított tárfiókba.
* Rendelkezik a CuRL telepítve azon a számítógépen, amelyre próbálja ezeket a lépéseket.

Hajtsa végre az alábbi lépéseket:

1. Ossza meg velünk először győződjön meg arról, hogy a Livy Spark fut-e a fürtön. Hogy futó kötegek listája úgy teheti meg. Ha egy feladat Livy használatával az első alkalommal futtatja, a kimeneti nulla adja vissza.
   
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
   
    Egy kimenetet kell kapnia az alábbi kódrészlethez hasonló:
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:47:53 GMT
        < Content-Length: 34
        <
        {"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    Figyelje meg, hogyan kimenet utolsó sora szerint **összesen: 0**, ami arra utal, hogy nem futó kötegek.

2. Ossza meg velünk most küldje el egy batch-feladatot. A következő kódrészlet egy bemeneti fájlt (input.txt) paraméterként átadni a jar-nevét és az osztály nevét. Ha ezeket a lépéseket egy Windows-számítógép futtatja, az ajánlott módszer használatával egy bemeneti fájlt.
   
        curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
   
    A fájlban a paraméterek **input.txt** meghatározása a következő:
   
        { "file":"wasb:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }
   
    Az alábbi kódrészlethez hasonló kimenetnek kell megjelennie:
   
        < HTTP/1.1 201 Created
        < Content-Type: application/json; charset=UTF-8
        < Location: /0
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:51:30 GMT
        < Content-Length: 36
        <
        {"id":0,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    Figyelje meg, hogyan a kimenet utolsó sora szerint **állapota: indítása**. Emellett ugyanakkor **azonosító: 0**. Itt **0** a batch-azonosítója.

3. Most már kérheti le a batch-azonosítójával az adott batch állapota
   
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/0"
   
    Az alábbi kódrészlethez hasonló kimenetnek kell megjelennie:
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:54:42 GMT
        < Content-Length: 509
        <
        {"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://hn0-myspar.lpel1gnnvxne3gwzqkfq5u5uzh.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    A kimenet látható **állapota: sikeres**, ami arra utal, hogy a feladat sikeresen befejeződött.

4. Ha azt szeretné, most törölheti a köteget.
   
        curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/0"
   
    Az alábbi kódrészlethez hasonló kimenetnek kell megjelennie:
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Sat, 21 Nov 2015 18:51:54 GMT
        < Content-Length: 17
        <
        {"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    A kimenet utolsó sora mutatja, hogy a Köteg törlése sikerült. Is egy feladat törlése közben fut, a feladatot megszakítja. Ha töröl egy feladatot, amely befejeződött, sikeres, vagy ellenkező esetben teljesen törli a feladatok adatait.

## <a name="updates-to-livy-configuration-starting-with-hdinsight-35-version"></a>HDInsight 3.5-ös verziójától kezdve Livy-konfiguráció frissítéseit

HDInsight 3.5-fürtök, és a fenti alapértelmezés szerint letiltja a hozzáférést mintaadatfájlok vagy JAR-fájlok kivételével helyi Fájlelérési utak használatát. Javasoljuk, hogy használja a `wasb://` elérési út inkább JAR-fájlok kivételével eléréséhez, vagy a mintaadatok fájlok a fürtből. Amennyiben szeretné, hogy a helyi elérési utat használja, ennek megfelelően frissítse az Ambari-konfiguráció. Ehhez tegye a következőket:

1. Nyissa meg az Ambari portal, a fürt számára. Az Ambari webes felhasználói felület érhető el a HDInsight-fürtön: https://**CLUSTERNAME**. azurehdidnsight.net, ahol CLUSTERNAME a fürt.

2. A bal oldali navigációs sávján kattintson **Livy**, és kattintson a **Configs**.

3. A **livy-alapértelmezett** adja hozzá a tulajdonságnév `livy.file.local-dir-whitelist` és a hozzá tartozó érték beállítása **"/"** Ha azt szeretné, hogy a fájlrendszer a teljes hozzáférést. Ha csak egy adott címtárhoz hozzáférést szeretne, adja meg, hogy a könyvtár elérési útja értéket.

## <a name="submitting-livy-jobs-for-a-cluster-within-an-azure-virtual-network"></a>Egy Azure virtuális hálózaton belüli fürt Livy-feladatok elküldése

Ha egy Azure virtuális hálózaton belül egy HDInsight Spark-fürthöz csatlakozik, közvetlenül csatlakozhat Livy a fürtön. Ebben az esetben a Livy-végpont URL-je `http://<IP address of the headnode>:8998/batches`. Itt **8998** a portot, amelyen Livy a fürt átjárócsomópontjával futtat. A nem nyilvános portot a szolgáltatások eléréséhez további információkért lásd: [HDInsight az Apache Hadoop-szolgáltatások által használt portok](../hdinsight-hadoop-port-settings-for-services.md).

## <a name="troubleshooting"></a>Hibaelhárítás

Az alábbiakban néhány problémát, miközben a távoli feladatok elküldéséhez a Spark-fürtök a Livy használatával mutatjuk be.

### <a name="using-an-external-jar-from-the-additional-storage-is-not-supported"></a>Egy külső jar, a további tárhely használata nem támogatott.

**Probléma:** a Livy Spark-feladat egy külső jar hivatkozik a fürthöz társított további storage-fiókból, ha a feladat sikertelen lesz.

**Megoldás:** győződjön meg arról, hogy a használni kívánt jar érhető el az alapértelmezett Storage a HDInsight-fürthöz társított.





## <a name="next-step"></a>Következő lépés

* [Az Apache Livy REST API-dokumentáció](http://livy.incubator.apache.org/docs/latest/rest-api.html)
* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)

