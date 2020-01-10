---
title: Feladatok beküldése a Spark-fürtbe az Azure HDInsight Livy Spark használatával
description: Ismerje meg, hogyan küldhet el a Spark-feladatokat a Apache Spark REST API használatával egy Azure HDInsight-fürtön.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: da654beec730d0bfc04548402c1158ebaaf80c6f
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75748369"
---
# <a name="use-apache-spark-rest-api-to-submit-remote-jobs-to-an-hdinsight-spark-cluster"></a>Távoli feladatok küldése HDInsight Spark-fürtre Apache Spark REST API használatával

Ismerje meg, hogyan használható az [Apache Livy](https://livy.incubator.apache.org/), a [Apache Spark](https://spark.apache.org/) REST API, amely távoli feladatok Azure HDInsight Spark fürtbe való küldésére szolgál. Részletes dokumentáció: [https://livy.incubator.apache.org/](https://livy.incubator.apache.org/).

A Livy használatával interaktív Spark-rendszerhéjakat futtathat, vagy elküldheti a Spark-on futó kötegelt feladatokat. Ez a cikk a Livy a Batch-feladatok elküldésére való használatát ismerteti. A cikkben szereplő kódrészletek a cURL használatával kezdeményezik REST API hívásokat a Livy Spark-végpontra.

## <a name="prerequisites"></a>Előfeltételek

* Apache Spark-fürt megléte a HDInsightban. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).

* [cURL](https://curl.haxx.se/). Ez a cikk a cURL használatával mutatja be, hogyan lehet REST API hívásokat kezdeményezni egy HDInsight Spark-fürtön.

## <a name="submit-an-apache-livy-spark-batch-job"></a>Apache Livy Spark batch-feladatok elküldése

A Batch-feladatok elküldése előtt fel kell töltenie az alkalmazás-jar-t a fürthöz társított fürt tárolójába. Ehhez használhatja a [AzCopy](../../storage/common/storage-use-azcopy.md)parancssori segédprogramot is. Számos más ügyfél is felhasználhatja az adatok feltöltését. További információt a [HDInsight-ben található Apache Hadoop feladatok feltöltésekor](../hdinsight-upload-data.md)talál.

```cmd
curl -k --user "<hdinsight user>:<user password>" -v -H "Content-Type: application/json" -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches' -H "X-Requested-By: admin"
```

### <a name="examples"></a>Példák

* Ha a jar-fájl a fürt tárterületén található (WASB)

    ```cmd  
    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST -d '{ "file":"wasb://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

* Ha a jar fájlnevét és az osztálynév-t egy bemeneti fájl részeként szeretné átadni (ebben a példában a input. txt)

    ```cmd
    curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

## <a name="get-information-on-livy-spark-batches-running-on-the-cluster"></a>Információk lekérése a fürtön futó Livy Spark-kötegekről

Szintaxis:

```cmd
curl -k --user "<hdinsight user>:<user password>" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"
```

### <a name="examples"></a>Példák

* Ha a fürtön futó összes Livy Spark-köteget le szeretné kérni:

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches" 
    ```

* Ha egy adott batch-AZONOSÍTÓval rendelkező köteget szeretne lekérni

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"
    ```

## <a name="delete-a-livy-spark-batch-job"></a>Livy Spark batch-feladatok törlése

```cmd
curl -k --user "<hdinsight user>:<user password>" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"
```

### <a name="example"></a>Példa

A Batch-AZONOSÍTÓval rendelkező batch-feladatok törlése `5`.

```cmd
curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/5"
```

## <a name="livy-spark-and-high-availability"></a>Livy Spark és magas rendelkezésre állás

A Livy magas rendelkezésre állást biztosít a fürtön futó Spark-feladatok számára. Íme néhány példa.

* Ha a Livy szolgáltatás leáll, miután a feladatot távolról elküldte egy Spark-fürtre, a rendszer továbbra is futtatja a feladatot a háttérben. Ha a Livy biztonsági mentést készít, a visszaállítja a feladatok állapotát, és visszaküldi azt.
* A HDInsight tartozó Jupyter-jegyzetfüzeteket a háttérbeli Livy látja el. Ha egy jegyzetfüzet egy Spark-feladatot futtat, és a Livy szolgáltatás újraindul, a jegyzetfüzet továbbra is futtatja a kódrészleteket. 

## <a name="show-me-an-example"></a>Példa megjelenítése

Ebben a szakaszban a Livy Spark használatával kötegelt feladatok küldésére, a feladatok előrehaladásának figyelésére, majd a törlésre vonatkozó példákat tekintjük át. Az ebben a példában használt alkalmazás egy [önálló Scala-alkalmazás létrehozása és a HDInsight Spark-fürtön való futtatása](apache-spark-create-standalone-application.md)során készült. Az itt leírt lépések azt feltételezik, hogy:

* Már átmásolta az Application jar-t a fürthöz társított Storage-fiókba.
* A fürtön telepítve van az a számítógép, amelyen a lépéseket megkísérli.

Hajtsa végre a következő lépéseket:

1. Először ellenőrizze, hogy a Livy Spark fut-e a fürtön. Ezt a futó kötegek listájának beszerzésével teheti meg. Ha első alkalommal futtat egy feladatot a Livy használatával, a kimenetnek nullát kell visszaadnia.

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
    ```

    A következő kódrészlethez hasonló kimenetnek kell megjelennie:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:47:53 GMT
    < Content-Length: 34
    <
    {"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    Figyelje meg, hogy a kimenet utolsó sora **összesen: 0**, amely nem javasol futó kötegeket.

2. Most küldje el a Batch-feladatot. Az alábbi kódrészlet egy bemeneti fájlt (input. txt) használ, hogy paraméterekként adja át a jar nevét és az osztály nevét. Ha ezeket a lépéseket egy Windows rendszerű számítógépről futtatja, az ajánlott módszer a bemeneti fájl használata.

    ```cmd
    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

    A file **input. txt** fájl paramétereinek meghatározása a következő:

    ```text
    { "file":"wasb:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }
    ```

    Az alábbi kódrészlethez hasonló kimenetnek kell megjelennie:

    ```output
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
    ```

    Figyelje meg, hogy a kimenet utolsó sora az **állapotot mutatja: indítás**. Azt is mondja, **azonosító: 0**. Itt a **0** a Batch-azonosító.

3. Most már lekérheti az adott köteg állapotát a Batch-azonosító használatával.

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/0"
    ```

    Az alábbi kódrészlethez hasonló kimenetnek kell megjelennie:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:54:42 GMT
    < Content-Length: 509
    <
    {"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://myspar.lpel1gnnvxne3gwzqkfq5u5uzh.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    Ekkor a kimenet **állapota: sikeres**, ami azt sugallja, hogy a feladatot sikerült befejezni.

4. Ha szeretné, most törölheti a köteget.

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/0"
    ```

    Az alábbi kódrészlethez hasonló kimenetnek kell megjelennie:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Sat, 21 Nov 2015 18:51:54 GMT
    < Content-Length: 17
    <
    {"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    A kimenet utolsó sora azt mutatja, hogy a köteg sikeresen törölve lett. A feladatok törlése a futása közben is megöli a feladatot. Ha töröl egy olyan feladatot, amely sikeresen vagy más módon lett végrehajtva, a feladathoz tartozó adatokat teljesen törli.

## <a name="updates-to-livy-configuration-starting-with-hdinsight-35-version"></a>A HDInsight 3,5 verziótól kezdődően a Livy-konfiguráció frissítései

A HDInsight 3,5-es és újabb fürtökön alapértelmezés szerint letiltja a helyi fájlelérési utak használatát a mintavételi adatfájlok vagy a tégelyek eléréséhez. Javasoljuk, hogy a `wasb://` elérési utat használja ahelyett, hogy az adattárolók vagy mintaadatok fájljait a fürtből hozzáférhessen.

## <a name="submitting-livy-jobs-for-a-cluster-within-an-azure-virtual-network"></a>Livy-feladatok elküldése egy fürthöz egy Azure-beli virtuális hálózaton belül

Ha egy Azure-Virtual Networkon belül csatlakozik egy HDInsight Spark-fürthöz, közvetlenül kapcsolódhat a Livy a fürtön. Ebben az esetben a Livy-végpont URL-címe `http://<IP address of the headnode>:8998/batches`. Itt a **8998** az a port, amelyen a Livy fut a fürt átjárócsomóponthoz. A nem nyilvános portokon található szolgáltatások elérésével kapcsolatos további információkért lásd: [Apache Hadoop Services által használt portok a HDInsight-on](../hdinsight-hadoop-port-settings-for-services.md).

## <a name="next-steps"></a>Következő lépések

* [Apache Livy REST API dokumentáció](https://livy.incubator.apache.org/docs/latest/rest-api.html)
* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)