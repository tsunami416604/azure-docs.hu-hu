---
title: Válassza a Livy Spark elküldeni a feladatok a Spark on Azure HDInsight fürt |} Microsoft Docs
description: Útmutató Apache Spark REST API használatával távolról a feladatok Spark on Azure HDInsight-fürtök elküldéséhez.
keywords: az Apache Spark on rest API-t livy spark
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2817b779-1594-486b-8759-489379ca907d
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 12/11/2017
ms.author: nitinme
ms.openlocfilehash: 29cf245a03b38be4f5396a3c83c966a27cf038f3
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="use-apache-spark-rest-api-to-submit-remote-jobs-to-an-hdinsight-spark-cluster"></a>Egy HDInsight Spark-fürt távoli feladatok elküldéséhez az Apache Spark REST API használatával

Ismerje meg, hogyan Livy, az Apache Spark REST API, amely egy Azure HDInsight Spark-fürt távoli feladatok elküldéséhez használhatja. További részletes dokumentációt: [ http://livy.incubator.apache.org/ ](http://livy.incubator.apache.org/).

Livy használatával futtassa az interaktív Spark ismertetése vagy kötegelt feladatok futni a Spark on való elküldéséhez. Ez a cikk beszél Livy használatával kötegelt feladatok küldéséhez. Ebben a cikkben kódtöredékek cURL használatával REST API-hívások a Livy Spark-végponthoz.

**Előfeltételek:**

* A HDInsight az Apache Spark-fürt. Útmutatásért lásd: [létrehozása az Apache Spark on Azure hdinsight clusters](apache-spark-jupyter-spark-sql.md).

* [cURL](http://curl.haxx.se/). Ez a cikk a curl használatával bemutatják, hogyan lehet REST API-hívások egy HDInsight Spark-fürt ellen.

## <a name="submit-a-livy-spark-batch-job"></a>Livy Spark kötegelt feladat elküldése
A kötegelt mentése előtt a fürthöz tartozó fürt tárhelyen az alkalmazás jar kell feltöltenie. Használhat [ **AzCopy**](../../storage/common/storage-use-azcopy.md), parancssori segédprogram, ehhez. Nincsenek más ügyfelek különböző segítségével feltölteni az adatokat. A rájuk vonatkozó további található [feltölteni az adatokat a HDInsight Hadoop-feladatok](../hdinsight-upload-data.md).

    curl -k --user "<hdinsight user>:<user password>" -v -H <content-type> -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches'

**Példák**:

* Ha a jar-fájlra a fürt storage (WASB)
  
        curl -k --user "admin:mypassword1!" -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches"
* Ha a jar-fájl és az osztálynév bemeneti fájl részeként továbbítja szeretne (ebben a példában input.txt)
  
        curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

## <a name="get-information-on-livy-spark-batches-running-on-the-cluster"></a>Ismerje meg a fürtön a Livy Spark kötegek információit
    curl -k --user "<hdinsight user>:<user password>" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"

**Példák**:

* Ha szeretné beolvasni az összes, a fürtben futó Livy Spark kötegek:
  
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
* Ha azt szeretné, hogy egy adott kötegazonosító adott kötegben beolvasása
  
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## <a name="delete-a-livy-spark-batch-job"></a>A Livy Spark kötegelt törlése
    curl -k --user "<hdinsight user>:<user password>" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"

**Példa**:

    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## <a name="livy-spark-and-high-availability"></a>Livy Spark és magas rendelkezésre állású
Livy biztosít a magas rendelkezésre állású Spark a fürtön futó feladatok. Íme néhány példa.

* Ha a Livy szolgáltatás leáll, miután elküldte a feladat távolról Spark-fürt, a feladat továbbra is fut a háttérben. Készítsen biztonsági másolatot Livy esetén vissza jelentéseket, valamint a feladat állapotát állítja vissza.
* A HDInsight Jupyter notebookok Livy szerint vannak kapcsolva, a háttérben. Ha a notebook egy Spark feladat fut, és a Livy szolgáltatás újraindítása lekérdezi, a notebook a kód cellák fut tovább. 

## <a name="show-me-an-example"></a>Példa megjelenítése
Ebben a szakaszban úgy tekintünk Livy Spark használandó kötegelt elküldése, figyelheti a feladat állapotát, és törölje azt példákat is. Az alkalmazás ebben a példában használjuk, a cikkben található fejlesztett egy [hozzon létre egy önálló Scala alkalmazás, és futtassa a HDInsight Spark-fürt](apache-spark-create-standalone-application.md). Itt azt feltételezi, hogy:

* Már másolta az alkalmazás jar keresztül a fürthöz tartozó tárfiókba.
* Lehetősége van telepítve a számítógépen, amelyre ezeket a lépéseket próbálja CuRL.

Hajtsa végre a következő lépéseket:

1. Ossza meg velünk először győződjön meg arról, hogy Livy Spark fut a fürtön. Olvasson be kötegek futó azt is megteheti. Ha egy feladat Livy használatával az első alkalommal futtatja, a kimeneti nulla kell visszaadnia.
   
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
   
    Kimenettel kell kapnia hasonló a következő kódrészletet:
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:47:53 GMT
        < Content-Length: 34
        <
        {"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    Figyelje meg, hogyan kimenet utolsó sora szerint **összesen: 0**, amely nem futó kötegek javasol.

2. Ossza meg velünk most kötegelt feladat elküldése. Az alábbi részlet egy bemeneti fájl (input.txt) használatával paraméterként átadni a jar és az osztály nevét. Windows rendszerű számítógépről futtatja ezeket a lépéseket, ha egy bemeneti fájl használata javasolt.
   
        curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"
   
    A paraméterek, a fájl **input.txt** az alábbiak szerint definiáltuk:
   
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
   
    Figyelje meg, hogy a kimenet utolsó sora szerint **állapota: indítása**. Azt is jelzi, **azonosító: 0**. Itt **0** a batch-azonosító.

3. Most le a kötegelt azonosítójával a megadott köteg állapota
   
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
   
    A kimenet most mutatja **állapota: sikeres**, amelyek arra utalnak, hogy a feladat sikeresen befejeződött.

4. Ha azt szeretné, most törölheti a kötegben.
   
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
   
    A kimenet utolsó sora jeleníti meg, hogy a köteg sikeresen törölve lett. Is törlése egy feladat futtatása, használhatatlanná teszi a feladatot. Ha törli egy feladatot, amely már befejeződött, sikeres, vagy ellenkező esetben teljesen törli az adatok.

## <a name="using-livy-spark-on-hdinsight-35-clusters"></a>Livy Spark használatával HDInsight 3.5-fürtökön

HDInsight 3.5 fürt, alapértelmezés szerint letiltja a hozzáférés mintaadatfájlok vagy JAR-fájlok kivételével helyi Fájlelérési utak használatát. Javasoljuk, hogy használja a `wasb://` elérési út helyett JAR-fájlok kivételével eléréséhez, vagy az adatokat fájlok a fürtből. Ha használni kívánt helyi elérési utat, Ambari konfigurációját ennek megfelelően kell frissíteni. Ehhez tegye a következőket:

1. Nyissa meg a fürt az Ambari portálra. Az Ambari webes felhasználói felület érhető el a HDInsight-fürthöz: https://**CLUSTERNAME**. azurehdidnsight.net, ahol CLUSTERNAME-e a fürt neve.

2. A bal oldali navigációs sávon kattintson **Livy**, és kattintson a **Configs**.

3. A **livy alapértelmezett** adja hozzá a tulajdonság nevét `livy.file.local-dir-whitelist` majd az értékét állítsa **"/"** Ha azt szeretné, hogy a fájlrendszer teljes hozzáférést tesz lehetővé. Ha szeretné engedélyezni a hozzáférést csak egy meghatározott könyvtár, adja meg, hogy a könyvtár elérési útjának értékeként.

## <a name="submitting-livy-jobs-for-a-cluster-within-an-azure-virtual-network"></a>Egy Azure virtuális hálózaton belül fürt Livy feladatok elküldése

Ha egy Azure virtuális hálózaton belül egy HDInsight Spark-fürt csatlakozni, akkor közvetlenül kapcsolódhatnak Livy a fürtön. Ebben az esetben Livy végpont URL-je `http://<IP address of the headnode>:8998/batches`. Itt **8998** a portot, amelyen Livy fut a fürtön headnode. A nem nyilvános portokon szolgáltatások eléréséhez szükséges további információkért lásd: [a HDInsight Hadoop-szolgáltatás által használt portok](../hdinsight-hadoop-port-settings-for-services.md).

## <a name="troubleshooting"></a>Hibaelhárítás

Az alábbiakban néhány problémát, előfordulhat, hogy futtatja a Livy használatával a Spark-fürtök távoli feladat elküldése közben.

### <a name="using-an-external-jar-from-the-additional-storage-is-not-supported"></a>A további tárhely az egy külső jar használata nem támogatott

**Probléma:** a Livy Spark feladat a fürthöz tartozó további tárhely-fiókból egy külső jar hivatkozik, ha a feladat sikertelen lesz.

**Megoldás:** győződjön meg arról, hogy a használni kívánt jar érhető el a HDInsight-fürthöz tartozó alapértelmezett tárolására.





## <a name="next-step"></a>Következő lépés

* [Livy REST API-JÁNAK dokumentációja](http://livy.incubator.apache.org/docs/latest/rest-api.html)
* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)

