---
title: Közzétett alkalmazás – StreamSets Data Collector – Azure HDInsight telepítése
description: Telepítheti és használhatja a StreamSets Data Collector külső Apache Hadoop-alkalmazásokat.
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: ac287f2ee50501d703b7d7b79a436ecb5335d1bd
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52497382"
---
# <a name="install-published-application---streamsets-data-collector"></a>Közzétett alkalmazás – StreamSets Data Collector telepítése

Ez a cikk bemutatja, hogyan telepítheti és futtathatja a [StreamSets Data Collector for HDInsight](https://streamsets.com/) közzétett [Apache Hadoop](https://hadoop.apache.org/) alkalmazás az Azure HDInsight a. A HDInsight-alkalmazásplatform áttekintése és a egy listát az elérhető független szoftverszállító (ISV) közzétett alkalmazások létrehozásáról: [külső Apache Hadoop-alkalmazások telepítése](hdinsight-apps-install-applications.md). A saját alkalmazások telepítéséről az [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md) című cikk tartalmaz útmutatást.

## <a name="about-streamsets-data-collector"></a>Tudnivalók a StreamSets Data Collector

A StreamSets Data Collector helyezhető üzembe egy Azure HDInsight-alkalmazás. StreamSets Data Collector egy teljes értékű integrált fejlesztőkörnyezet (IDE), hogy lehetővé teszi, hogy tervezése, tesztelése, üzembe helyezése és felügyelete – bármely betöltési folyamatok biztosít. Ezek a folyamatok tervezhetők stream és kötegelt adatokat, és közé tartozik a stream átalakítások, számos egyéni kódolás nélkül.

StreamSets Data Collector lehetővé teszi, hogy használjon számos Big Data-összetevőket, például a build adatfolyamok [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html), [Apache Kafka](https://kafka.apache.org/), [Apache Solr](https://lucene.apache.org/solr/), [ Az Apache Hive](https://hive.apache.org/), [Apache HBase](https://hbase.apache.org/), és [Apache Kudu](https://kudu.apache.org/). Miután a StreamSets Data Collector fut egy biztonsági kiszolgálót, vagy a Hadoop-fürt, a valós idejű monitorozási adatok rendellenességeket és a data flow műveletek kap. A monitorozás tartalmazza, küszöbalapú riasztások, rendellenességek észlelése és automatikus szervizelés hiba rekordok.

StreamSets Data Collector célja egy folyamatot, minden egyes fázisában logikailag elkülönítése, így az új üzleti követelmények teljesítésében elvetését az új feldolgozók és összekötők, kódírás nélkül és minimális állásidővel.

### <a name="streamsets-resource-links"></a>Erőforrás-hivatkozások StreamSets

* [Dokumentáció](https://streamsets.com/documentation/datacollector/latest/help/#Getting_Started/GettingStarted_Title.html)
* [Blog](https://streamsets.com/blog/)
* [oktatóanyagokat](https://github.com/streamsets/tutorials)
* [Fejlesztői támogatási fórum](https://groups.google.com/a/streamsets.com/forum/#!forum/sdc-user)
* [Slack nyilvános StreamSets csatorna](https://streamsetters.slack.com/)
* [Forráskód](https://github.com/streamsets)

## <a name="prerequisites"></a>Előfeltételek

Telepíti az alkalmazást egy új HDInsight-fürtöt, vagy egy meglévő fürthöz, a következő konfigurációval kell rendelkeznie:

* A fürt tier(s): Standard vagy prémium szint
* A fürt verziót: 3.5-ös vagy újabb verzió

## <a name="install-the-streamsets-data-collector-published-application"></a>A közzétett alkalmazás telepítése a StreamSets Data Collector

Ezzel és más elérhető ISV-alkalmazások telepítésének lépésenkénti útmutatójáért olvassa el a [külső Apache Hadoop-alkalmazások telepítése](hdinsight-apps-install-applications.md).

## <a name="launch-streamsets-data-collector"></a>Indítsa el a StreamSets Data Collector

1. A telepítés után már el is indíthatja StreamSets a fürtből az Azure Portalon lépjen a **beállítások** ablaktáblán, majd válassza **alkalmazások** alatt a **általános** kategória. A telepített alkalmazások panelen a telepített alkalmazás fel van sorolva.

    ![A StreamSets telepített alkalmazás](./media/hdinsight-apps-install-streamsets/streamsets.png)

2. StreamSets Data Collector kiválasztásakor megjelenik egy hivatkozás a weblapot, és az SSH-végpont elérési útja. Válassza ki annak hivatkozására.

3. A bejelentkezési párbeszédpanelen a következő hitelesítő adatok használatával jelentkezzen be: `admin` és `admin`.

4. Első lépések oldalán kattintson **új folyamat létrehozása**.

    ![Új folyamat létrehozása](./media/hdinsight-apps-install-streamsets/get-started.png)

5. Az új folyamat ablakban írja be egy nevet, a folyamat (a "Hello World"), igény szerint adjon meg egy leírást, és válassza ki **mentése**.

6. A Data Collector konzol jelenik meg. A Tulajdonságok panelen folyamat tulajdonságait jeleníti meg.
 
    ![Data Collector konzol](./media/hdinsight-apps-install-streamsets/pipeline-canvas.png)

7. Most már készen áll, kövesse a [StreamSets oktatóanyag](https://streamsets.com/documentation/datacollector/latest/help/#Tutorial/Tutorial-title.html). Az oktatóanyag az első folyamat létrehozása a lépésenkénti utasításokat biztosít.

## <a name="next-steps"></a>További lépések

* [Dokumentáció a StreamSets Data Collector](https://streamsets.com/documentation/datacollector/latest/help/#Getting_Started/GettingStarted_Title.html#concept_htw_ghg_jq).
* [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md): útmutató HDInsight közzé nem tett HDInsight-alkalmazás üzembe helyezése.
* [HDInsight-alkalmazások közzététele](hdinsight-apps-publish-applications.md): Megtudhatja, hogyan teheti közzé egyéni HDInsight-alkalmazásait az Azure Piactéren.
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: HDInsight-alkalmazás telepítése): Megtudhatja, hogyan adhat meg HDInsight-alkalmazásokat.
* [Szkriptműveletek használatával Linux-alapú HDInsight-fürtök testre szabása](hdinsight-hadoop-customize-cluster-linux.md): ismerje meg, hogyan telepíthet további alkalmazásokat használ.
* [Üres élcsomópontok használata a HDInsight](hdinsight-apps-use-edge-node.md): üres élcsomópontot használata a HDInsight-fürtök eléréséhez, és a teszteléshez és a HDInsight-alkalmazások üzemeltetése.
