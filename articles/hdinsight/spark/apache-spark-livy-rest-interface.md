---
title: A Livy Spark használatával feladatokat küldhet a Spark-fürtnek az Azure HDInsightban
description: Ismerje meg, hogyan küldhet idáig az Apache Spark REST API-val a Spark-feladatok távolról történő elküldéséhez egy Azure HDInsight-fürtbe.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/28/2020
ms.openlocfilehash: ac3904284ebf20fa1d5e75f9249732be3963f677
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206282"
---
# <a name="use-apache-spark-rest-api-to-submit-remote-jobs-to-an-hdinsight-spark-cluster"></a>Az Apache Spark REST API használata távoli feladatok küldéséhez egy HDInsight Spark-fürtre

Ismerje meg, hogyan használhatja az [Apache Livyt,](https://livy.incubator.apache.org/)az Apache Spark REST API-t, amely távoli feladatok azure HDInsight Spark-fürtbe küldésére szolgál. A részletes dokumentációt az [Apache Livy című témakörben találja.](https://livy.incubator.apache.org/docs/latest/rest-api.html)

Livy segítségével interaktív Spark-rendszerhéjak futtatásához, vagy kötegelt feladatok futtatásához a Spark. Ez a cikk a Livy kötegelt feladatok küldésére való használatáról szól. Ebben a cikkben a kódrészletek cURL használatával rest API-hívások a Livy Spark-végpont.

## <a name="prerequisites"></a>Előfeltételek

Apache Spark-fürt megléte a HDInsightban. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).

## <a name="submit-an-apache-livy-spark-batch-job"></a>Apache Livy Spark kötegelt feladat elküldése

A kötegelt feldolgozás elküldése előtt fel kell töltenie az alkalmazásjart a fürthöz társított fürttárolón. Ehhez az [AzCopy](../../storage/common/storage-use-azcopy.md) parancssori segédprogramot használhatja. Vannak különböző más ügyfelek segítségével adatok at feltölteni. Ezekről többet a [HDInsight-ban található Apache Hadoop-feladatok adatainak feltöltése területen](../hdinsight-upload-data.md)talál.

```cmd
curl -k --user "admin:password" -v -H "Content-Type: application/json" -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches' -H "X-Requested-By: admin"
```

### <a name="examples"></a>Példák

* Ha a jar fájl a fürttárolón (WASBS) található

    ```cmd  
    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

* Ha a jar fájlnevet és az osztálynevet egy bemeneti fájl részeként szeretné átadni (ebben a példában a input.txt fájlban)

    ```cmd
    curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

## <a name="get-information-on-livy-spark-batches-running-on-the-cluster"></a>Információk a fürtön futó Livy Spark-kötegekről

Szintaxis:

```cmd
curl -k --user "admin:password" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"
```

### <a name="examples"></a>Példák

* Ha a fürtön futó összes Livy Spark-köteget le szeretné kérni:

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
    ```

* Ha egy adott köteget egy adott kötegazonosítóval szeretne beolvasni

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"
    ```

## <a name="delete-a-livy-spark-batch-job"></a>Livy Spark kötegelt feldolgozás törlése

```cmd
curl -k --user "admin:mypassword1!" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"
```

### <a name="example"></a>Példa

Kötegelt feldolgozás törlése kötegelt azonosítóval. `5`

```cmd
curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/5"
```

## <a name="livy-spark-and-high-availability"></a>Livy Spark és a magas rendelkezésre állás

A Livy magas rendelkezésre állást biztosít a fürtön futó Spark-feladatokszámára. Íme néhány példa.

* Ha a Livy szolgáltatás leáll, miután távolról elküldte a feladatot egy Spark-fürthöz, a feladat továbbra is fut a háttérben. Amikor Livy újra működik, visszaállítja a feladat állapotát, és jelenti azt.
* A HDInsight Jupyter-jegyzetfüzeteit livy a háttérben működteti. Ha egy jegyzetfüzet egy Spark-feladatot futtat, és a Livy szolgáltatás újraindul, a jegyzetfüzet továbbra is futtatja a kódcellákat.

## <a name="show-me-an-example"></a>Példa megjelenítése

Ebben a szakaszban példákat tekintünk a Livy Spark használatával kötegelt feldolgozás elküldésére, a feladat előrehaladásának figyelésére, majd törlésére. Az ebben a példában használt alkalmazás az, amelyet a Cikkben kifejlesztett [Hozzon létre egy önálló Scala alkalmazással, és futtasson a HDInsight Spark-fürtön.](apache-spark-create-standalone-application.md) Az itt leírt lépések a következőket feltételezik:

* Már másolta át az alkalmazás jar a fürthöz társított tárfiókba.
* A CuRL telepítve van arra a számítógépre, amelyen ezeket a lépéseket próbálja.

Hajtsa végre a következő lépéseket:

1. A könnyű használat érdekében állítsa be a környezeti változókat. Ez a példa egy Windows-környezeten alapul, a környezethez szükséges változók at vizsgálja felül. Cserélje `CLUSTERNAME`ki `PASSWORD` a megfelelő értékeket.

    ```cmd
    set clustername=CLUSTERNAME
    set password=PASSWORD
    ```

1. Ellenőrizze, hogy a Livy Spark fut-e a fürtön. Ezt úgy tehetjük meg, hogy megszerzünk egy listát a futó kötegekről. Ha a Livy használatával futtat egy feladatot, a kimenetnek nullát kell visszaadnia.

    ```cmd
    curl -k --user "admin:%password%" -v -X GET "https://%clustername%.azurehdinsight.net/livy/batches"
    ```

    A következő kódrészlethez hasonló kimenetet kell kapnia:

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

    Figyelje meg, hogy a kimenet utolsó sora **a total:0-t**mondja, ami azt sugallja, hogy nincs enek futó köteg.

1. Most küldjünk be egy kötegelt feladatot. A következő kódrészlet egy bemeneti fájlt (input.txt) használ a jar név és az osztály nevének paraméterként való átadására. Ha ezeket a lépéseket Windows rendszerű számítógépről futtatja, az ajánlott megközelítés egy beviteli fájl használata.

    ```cmd
    curl -k --user "admin:%password%" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://%clustername%.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

    A **input.txt** fájl paraméterei a következők:

    ```text
    { "file":"wasbs:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }
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

    Figyelje meg, hogy a kimenet utolsó sora az **állapot: indítás.** Azt is mondja, **id:0**. Itt **a 0** a kötegazonosító.

1. Most már lekérheti az adott köteg állapotát a kötegazonosító használatával.

    ```cmd
    curl -k --user "admin:%password%" -v -X GET "https://%clustername%.azurehdinsight.net/livy/batches/0"
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
    {"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://myspar.lpel.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    A kimenet most az **state:success-t**mutatja, ami azt jelzi, hogy a feladat sikeresen befejeződött.

1. Ha szeretné, most törölheti a köteget.

    ```cmd
    curl -k --user "admin:%password%" -v -X DELETE "https://%clustername%.azurehdinsight.net/livy/batches/0"
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

    A kimenet utolsó sora azt mutatja, hogy a köteg et sikeresen törölték. Ha egy munka törlése, miközben fut, az is megöli a munkát. Ha töröl egy sikeresen vagy más módon befejeződött feladatot, akkor teljesen törli a feladatadatait.

## <a name="updates-to-livy-configuration-starting-with-hdinsight-35-version"></a>A Livy-konfiguráció frissítései a HDInsight 3.5 verziójával kezdve

A HDInsight 3.5-ös és újabb fürtjei alapértelmezés szerint letiltják a helyi fájlelérési utak használatát a mintaadatfájlok vagy -üvegek eléréséhez. Azt javasoljuk, hogy `wasbs://` használja az elérési utat, hanem a fürtből származó üvegek vagy minta adatfájlok eléréséhez.

## <a name="submitting-livy-jobs-for-a-cluster-within-an-azure-virtual-network"></a>Livy-feladatok küldése egy fürthöz egy Azure virtuális hálózaton belül

Ha egy Azure virtuális hálózaton belül csatlakozik egy HDInsight Spark-fürthöz, közvetlenül csatlakozhat livy-hez a fürtön. Ebben az esetben a Livy-végpont `http://<IP address of the headnode>:8998/batches`URL-címe . Itt a **8998** az a port, amelyen Livy a fürt csomópontján fut. A nem nyilvános portokon lévő szolgáltatások eléréséről az [Apache Hadoop-szolgáltatások által a HDInsight szolgáltatáson használt portok](../hdinsight-hadoop-port-settings-for-services.md)című témakörben talál további információt.

## <a name="next-steps"></a>További lépések

* [Apache Livy REST API dokumentáció](https://livy.incubator.apache.org/docs/latest/rest-api.html)
* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)
