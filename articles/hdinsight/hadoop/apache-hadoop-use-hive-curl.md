---
title: "Hadoop Hive használata a hdinsight - Azure Curl |} Microsoft Docs"
description: "Megtudhatja, hogyan távolról elküldeni a Pig-feladatokhoz a HDInsight használata Curl használatával."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 6ce18163-63b5-4df6-9bb6-8fcbd4db05d8
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/04/2017
ms.author: larryfr
ms.openlocfilehash: 07386c2fbb5fe4ce4fe1ca6844f6308cf59cbfb4
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2017
---
# <a name="run-hive-queries-with-hadoop-in-hdinsight-using-rest"></a>A többi használatával HDInsight Hadoop Hive-lekérdezések futtatása

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Megtudhatja, hogyan futtathat Hive-lekérdezéseket a Hadoop on Azure HDInsight-fürt a WebHCat REST API használatával.

[Curl](http://curl.haxx.se/) bemutatják, hogyan kezelheti a HDInsight nyers HTTP-kérelmek használatával szolgál. A [jq](http://stedolan.github.io/jq/) segédprogram segítségével dolgozza fel a többi kérelem által visszaadott JSON-adatokat.

> [!NOTE]
> Ha ismeri a Linux-alapú Hadoop-kiszolgálókat használ, de még nem használta a HDInsight, tekintse meg a [mit kell tudnia a Linux-alapú HDInsight Hadoop](../hdinsight-hadoop-linux-information.md) dokumentum.

## <a id="curl"></a>Hive-lekérdezések futtatása

> [!NOTE]
> Használata cURL vagy más REST kommunikációt használ a Webhcattel, hitelesítenie kell a kéréseket a HDInsight fürt rendszergazdája a felhasználónév és jelszó megadásával.
>
> Ezen szakasz parancsaiban cserélje le **admin** a felhasználó a fürtön. Cserélje le a **CLUSTERNAME** elemet a fürt nevére. Amikor a rendszer kéri, írja be a jelszót a felhasználói fiók.
>
> A REST API védelméről [alapszintű hitelesítés](http://en.wikipedia.org/wiki/Basic_access_authentication) gondoskodik. Győződjön meg arról, hogy a hitelesítő adatait biztonságos módon küldje a kiszolgáló érdekében mindig végezzen kérelmek HTTP Secure (HTTPS).

1. Egy parancssorból a következő paranccsal ellenőrizze, hogy tud-e kapcsolódni a HDInsight-fürthöz:

    ```bash
    curl -u admin -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    Az alábbihoz hasonló választ kapnak:

        {"status":"ok","version":"v1"}

    Ezen parancs paraméterei a következők:

    * **-u** - A kérés hitelesítéséhez használt felhasználónév és jelszó.
    * **-G** -azt jelzi, hogy a kérelem a GET műveletet.

   Az URL-cím, az elején **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, minden olyan kérelem esetében megegyezik. Az elérési út **/status**, azt jelzi, hogy a kérelem vissza WebHCat (más néven Templeton) állapota a kiszolgálóhoz. A Hive verzióját is kérheti a következő paranccsal:

    ```bash
    curl -u admin -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive
    ```

    A kérelem választ ad az alábbihoz hasonló:

        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}

2. Használja a következő nevű tábla létrehozása **log4jLogs**:

    ```bash
    curl -u admin -d user.name=admin -d execute="set+hive.execution.engine=tez;DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive
    ```

    Ehhez a kérelemhez használt a következő paramétereket:

   * **-d** - óta `-G` nem használ, akkor a kérelmet az alapértelmezett a POST metódussal. `-d`Megadja a küldött adatértékekkel mellékel a kérelemhez.

     * **User.name** – a parancsot futtató felhasználónak.
     * **végrehajtás** -a HiveQL utasítás végrehajtásához.
     * **statusdir** -címtárban, ami a feladat állapotát a rendszer írja.

   Ezekre az utasításokra hajtsa végre a következő műveleteket:
   
   * **DROP TABLE** -Ha a tábla már létezik, az törlődik.
   * **A külső tábla létrehozása** -táblát hoz létre egy új "külső" struktúra. Külső táblák csak a tábladefiníció Hive tárolja. Az adatok marad az eredeti helyen.

     > [!NOTE]
     > Külső táblák kell használni, amikor külső forrásból frissítenie kell az alapul szolgáló adatokat várt. Például egy automatizált adatok feltöltési folyamat vagy egy másik MapReduce művelet.
     >
     > A külső tábla eldobása does **nem** törli az adatokat, csak a tábla definíciójában.

   * **SOR formátum** – hogyan az adatok formázását. Az egyes naplókon mezők vannak szóközzel elválasztva.
   * **AS TEXTFILE helyen tárolt** - tárolja az adatokat (a példa/adatkönyvtár) és szövegként tárolt.
   * **Válassza ki** -választja ki az összes sorok számát ahol oszlop **t4** értéke **[hiba]**. A jelen nyilatkozat értéket ad vissza, **3** ahány három ezt az értéket tartalmazó sorok.

     > [!NOTE]
     > Figyelje meg, hogy a HiveQL utasítások szóközt helyébe a `+` karakter Curl használata esetén. Szóközt, például az elválasztó idézőjelek közé zárt értékek nem helyébe kell `+`.

   * **INPUT__FILE__NAME PÉLDÁUL "% 25.log"** – a jelen nyilatkozat korlátozza a keresési végződése fájlok csak használandó. napló.

     > [!NOTE]
     > A `%25` %, kódolású URL-cím formájában van, így a tényleges feltétel `like '%.log'`. A %-nak kell lennie a URL-kódolású, akkor a rendszer egy különleges karakterek URL-címeket.

   Ez a parancs visszaadja a Feladatazonosítót a feladat állapotának ellenőrzéséhez használható.

       {"id":"job_1415651640909_0026"}

3. A feladat állapotának ellenőrzéséhez használja a következő parancsot:

    ```bash
    curl -G -u admin -d user.name=admin https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    Cserélje le **JOBID** az előző lépésben visszaadott értékkel. Például, ha a visszatérési érték volt `{"id":"job_1415651640909_0026"}`, majd **JOBID** lenne `job_1415651640909_0026`.

    Ha a feladat befejeződött, az állapota **sikeres**.

   > [!NOTE]
   > A Curl kérelem egy JavaScript Object Notation (JSON) dokumentumot, a feladat információkat ad vissza. Csak a állapotérték beolvasásához használt Jq.

4. Ha a feladat állapota módosult **sikeres**, a feladat eredményeinek lekérése Azure Blob Storage tárolóban. A `statusdir` a lekérdezés átadott paraméter tartalmazza a hely a kimeneti fájl; ebben az esetben az **/példa/curl**. Ez a cím tárolja a kimenetét a **példa/curl** könyvtárban lévő a fürtök alapértelmezett tárolót.

    Listáról, és ezek a fájlok letöltésére a [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli). Az Azure Storage az Azure parancssori felület használatával kapcsolatos további információkért tekintse meg a [Azure CLI 2.0 használata Azure Storage](https://docs.microsoft.com/en-us/azure/storage/storage-azure-cli#create-and-manage-blobs) dokumentum.

5. A következő utasítások használatával hozzon létre egy új "belső" táblát nevű **errorLogs**:

    ```bash
    curl -u admin -d user.name=admin -d execute="set+hive.execution.engine=tez;CREATE+TABLE+IF+NOT+EXISTS+errorLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+STORED+AS+ORC;INSERT+OVERWRITE+TABLE+errorLogs+SELECT+t1,t2,t3,t4,t5,t6,t7+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log';SELECT+*+from+errorLogs;" -d statusdir="/example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive
    ```

    Ezekre az utasításokra hajtsa végre a következő műveleteket:

   * **Hozzon létre Ha nem létezik táblázat** -táblázatot hoz létre, ha még nem létezik. A jelen nyilatkozat létrehoz egy belső tábla, és a Hive-adatraktárban tárolja. Ez a táblázat Hive kezeli.

     > [!NOTE]
     > Ellentétben a külső táblákhoz az alapul szolgáló adatokat egy belső tábla eldobása törli.

   * **TÁROLT AS ORC** -tárolja az adatokat optimalizált sor oszlopos (ORC) formátumban. ORC formátuma egy magas optimalizált és hatékony Hive adatainak tárolásához.
   * **ÍRJA FELÜL AZ INSERT... Válassza ki** -sorát kiválasztja a **log4jLogs** tartalmazó **[hiba]**, majd szúrja be az adatokat a **errorLogs** tábla.
   * **Válassza ki** -választja ki az összes sort az új **errorLogs** tábla.

6. Használja ismét a feladat állapotának ellenőrzése a feladat Azonosítóját. Ha sikeres, az Azure parancssori felület leírt használja korábban töltse le, és tekintse meg az eredményeket. A kimeneti tartalmaznia kell a három sort, ezek mindegyike tartalmazhat **[hiba]**.

## <a id="nextsteps"></a>Következő lépések

Általános információk a hdinsight Hive:

* [A Hive használata a hdinsight Hadoop](hdinsight-use-hive.md)

Az egyéb lehetőségeiről a HDInsight Hadoop dolgozhat:

* [A Pig használata a HDInsight Hadoop](hdinsight-use-pig.md)
* [A HDInsight Hadoop MapReduce használata](hdinsight-use-mapreduce.md)

Ha Tez Hive használ, tekintse meg a hibakeresési információk a következő dokumentumokat:

* [Az Ambari Tez nézetben a Linux-alapú HDInsight-on](../hdinsight-debug-ambari-tez-view.md)

A REST API-val a jelen dokumentum további információkért tekintse meg a [WebHCat hivatkozás](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference) dokumentum.

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md




[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


