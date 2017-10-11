---
title: "Hadoop a Pig használata a HDInsight - Azure REST |} Microsoft Docs"
description: "Megtudhatja, hogyan használja a többi Azure hdinsight Hadoop-fürthöz Pig Latin feladatok futtatásához."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ed5e10d1-4f47-459c-a0d6-7ff967b468c4
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/31/2017
ms.author: larryfr
ms.openlocfilehash: a86864a779b0de1c6d5669cfbba0f3e1a27f1ff1
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="run-pig-jobs-with-hadoop-on-hdinsight-by-using-rest"></a>Pig-feladatokhoz a Hadoop on HDInsight használatával futtassa REST

[!INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Útmutató: Azure HDInsight-fürtök REST-kérelmek, így a Pig Latin feladatok futtatásához. Curl használatával bemutatják, hogyan kezelheti a HDInsight a WebHCat REST API használatával.

> [!NOTE]
> Ha ismeri a Linux-alapú Hadoop-kiszolgálókat használ, de még nem használta a HDInsight, lásd: [Linux-alapú HDInsight tippek](hdinsight-hadoop-linux-information.md).

## <a id="prereq"></a>Előfeltételek

* (A HDInsight Hadoop) Azure HDInsight-fürtök (Linux- vagy Windows-alapú)

  > [!IMPORTANT]
  > A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).

* [Curl](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

## <a id="curl"></a>Pig-feladatokhoz Curl használatával futtassa

> [!NOTE]
> A REST API védelméről [alapszintű hitelesítés](http://en.wikipedia.org/wiki/Basic_access_authentication). Mindig végezzen kérelmek HTTP Secure (HTTPS) annak érdekében, hogy a hitelesítő adatait biztonságos módon küldje a kiszolgálóhoz.
>
> A parancsok használata ebben a szakaszban, cserélje le a `USERNAME` a fürtön, és cserélje le a felhasználó `PASSWORD` a felhasználói fiók jelszavával. Cserélje le a `CLUSTERNAME` elemet a fürt nevére.
>


1. Egy parancssorból a következő paranccsal ellenőrizze, hogy tud-e kapcsolódni a HDInsight-fürthöz:

    ```bash
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    A következő JSON-választ kell kapnia:

        {"status":"ok","version":"v1"}

    Ezen parancs paraméterei a következők:

    * **-u**: A felhasználói nevet és a kérés hitelesítéséhez használt jelszó
    * **-G**: azt jelzi, hogy a kérelem a GET kérés

     Az URL-cím, az elején **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, minden olyan kérelem esetében megegyezik. Az elérési út **/status**, azt jelzi, hogy a kérelem vissza WebHCat (más néven Templeton) állapotának a kiszolgálóhoz.

2. A következő kód segítségével elküldeni a Pig Latin feladatot a fürt:

    ```bash
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="LOGS=LOAD+'/example/data/sample.log';LEVELS=foreach+LOGS+generate+REGEX_EXTRACT($0,'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)',1)+as+LOGLEVEL;FILTEREDLEVELS=FILTER+LEVELS+by+LOGLEVEL+is+not+null;GROUPEDLEVELS=GROUP+FILTEREDLEVELS+by+LOGLEVEL;FREQUENCIES=foreach+GROUPEDLEVELS+generate+group+as+LOGLEVEL,COUNT(FILTEREDLEVELS.LOGLEVEL)+as+count;RESULT=order+FREQUENCIES+by+COUNT+desc;DUMP+RESULT;" -d statusdir="/example/pigcurl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/pig
    ```

    Ezen parancs paraméterei a következők:

    * **-d**: mivel `-G` nem használ, akkor a kérelmet az alapértelmezett a POST metódussal. `-d`Megadja a küldött adatértékekkel mellékel a kérelemhez.

    * **User.name**: a parancsot futtató felhasználó
    * **végrehajtás**: A Pig Latin utasítás végrehajtása
    * **statusdir**: A könyvtár írt meg a feladat állapota

    > [!NOTE]
    > Figyelje meg, hogy a tárolóhelyek a Pig Latin utasításokban helyébe a `+` karakter Curl használata esetén.

    Ez a parancs visszaadja-e a Feladatazonosítót a feladat állapotának ellenőrzéséhez például használható:

        {"id":"job_1415651640909_0026"}

3. A feladat állapotának ellenőrzéséhez használja a következő parancs

     ```bash
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

     Cserélje le `JOBID` az előző lépésben visszaadott értékkel. Például, ha a visszatérési érték volt `{"id":"job_1415651640909_0026"}`, majd `JOBID` van `job_1415651640909_0026`.

    Ha a feladat befejeződött, az állapota **sikeres**.

    > [!NOTE]
    > A Curl kérelem egy JavaScript Object Notation (JSON) dokumentumot, a feladat információkat ad vissza, és csak a állapotérték beolvasásához használt jq.

## <a id="results"></a>Az eredmények megtekintése

Ha a feladat állapota módosult **sikeres**, a feladat eredményeinek kérheti le. A `statusdir` a lekérdezés átadott paraméter tartalmazza a hely a kimeneti fájl; ebben az esetben az `/example/pigcurl`.

HDInsight a alapértelmezett adattárként Azure Storage vagy az Azure Data Lake Store is használhatja. Különböző módon lehet beolvasni az adatokat, attól függően, melyiket használja. További információkért lásd: a tárolási szakasza a [Linux-alapú HDInsight-információk](hdinsight-hadoop-linux-information.md#hdfs-azure-storage-and-data-lake-store) dokumentum.

## <a id="summary"></a>Summary (Összefoglalás)

Ahogyan az ebben a dokumentumban, használhatja a nyers HTTP-kérelmek futtatásához, a figyelheti és a Pig-feladatokhoz eredményeinek megtekintéséhez a HDInsight-fürt.

A cikk ezt használja a REST-felület kapcsolatos további információkért tekintse meg a [WebHCat hivatkozás](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

## <a id="nextsteps"></a>Következő lépések

A Pig általános információk a HDInsight:

* [A Pig használata a HDInsight Hadoop](hdinsight-use-pig.md)

Más módszerekkel kapcsolatos információk a HDInsight Hadoop dolgozhat:

* [A Hive használata a hdinsight Hadoop](hdinsight-use-hive.md)
* [A HDInsight Hadoop MapReduce használata](hdinsight-use-mapreduce.md)
