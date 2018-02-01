---
title: "Telepítés közzétett alkalmazás - StreamSets adatgyűjtő - Azure HDInsight |} Microsoft Docs"
description: "Telepítheti és használhatja a StreamSets adatgyűjtő külső Hadoop alkalmazás."
services: hdinsight
documentationcenter: 
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: 90775452c58457ae8ecc73687a375606474158f5
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2018
---
# <a name="install-published-application---streamsets-data-collector"></a>Telepítse a közzétett alkalmazás - StreamSets adatgyűjtő

Ez a cikk ismerteti, hogyan telepíthetnek és futtathatnak a [StreamSets adatgyűjtő hdinsight](https://streamsets.com/) on Azure HDInsight Hadoop-alkalmazások közzététele. A HDInsight-alkalmazás platform áttekintését, és a rendelkezésre álló független szoftverszállító (ISV) listáját közzétett alkalmazások: [külső Hadoop-alkalmazások telepítése](hdinsight-apps-install-applications.md). A saját alkalmazások telepítéséről az [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md) című cikk tartalmaz útmutatást.

## <a name="about-streamsets-data-collector"></a>Kapcsolatos StreamSets adatgyűjtő

A StreamSets adatgyűjtő telepíti az Azure HDInsight-alkalmazások felett. StreamSets adatgyűjtő biztosít egy teljes körű integrált fejlesztőkörnyezetet (IDE), hogy lehetővé kialakítása, teszteléséhez, telepítéséhez és felügyeletéhez bármely közöttiként feldolgozó folyamatok. Ezek a folyamatok háló adatfolyam és kötegelt adatokat, és adatfolyam átalakítások, számos tartalmazza az összes anélkül, hogy egyéni kód írását.

StreamSets adatgyűjtő lehetővé teszi számos Big Data-összetevők, például a HDFS, Kafka, Solr, struktúra, a HBASE és a Kudu build adatfolyamok. Ha StreamSets adatgyűjtő fut egy biztonsági kiszolgálót, vagy a Hadoop-fürt, kap valós idejű figyelési adatok rendellenességek észlelését és adatok folyamata műveletek. A figyelés magában foglalja a riasztási küszöbérték-alapú, anomáliadetektálás és automatikus javítási hiba rekordok.

StreamSets adatgyűjtő logikailag különítheti el minden szakasza a folyamatnak, megfelelhetnek az új üzleti követelmények által kódolás nélkül és minimális állásidővel új processzorok és összekötők ejtésével tervezték.

### <a name="streamsets-resource-links"></a>StreamSets erőforrás-hivatkozások

* [Dokumentáció](https://streamsets.com/documentation/datacollector/latest/help/#Getting_Started/GettingStarted_Title.html)
* [Blog](https://streamsets.com/blog/)
* [oktatóanyagokat](https://github.com/streamsets/tutorials)
* [Fejlesztői támogatási fórum](https://groups.google.com/a/streamsets.com/forum/#!forum/sdc-user)
* [Slackhez nyilvános StreamSets csatorna](https://streamsetters.slack.com/)
* [Forráskód](https://github.com/streamsets)

## <a name="prerequisites"></a>Előfeltételek

Ez az alkalmazás egy új HDInsight-fürtöt, vagy egy meglévő fürt telepítéséhez a következő konfigurációval kell rendelkeznie:

* A fürt tier(s): Standard vagy prémium
* A fürt verzió(k): 3.5-ös vagy újabb verzió

## <a name="install-the-streamsets-data-collector-published-application"></a>Telepítse a StreamSets adatgyűjtő közzétett alkalmazás

Ez, és más elérhető ISV alkalmazások telepítésével kapcsolatos részletes útmutatás olvasható [külső Hadoop-alkalmazások telepítése](hdinsight-apps-install-applications.md).

## <a name="launch-streamsets-data-collector"></a>Indítsa el a StreamSets adatgyűjtő

1. A telepítés után is elindíthatja, StreamSets Azure-portálon fürtről címen a **beállítások** panelen, jelölje be **alkalmazások** alatt a **általános** kategória. A telepített alkalmazások ablaktábla listázza a telepített alkalmazások.

    ![Telepített StreamSets alkalmazás](./media/hdinsight-apps-install-streamsets/streamsets.png)

2. Ha StreamSets adatgyűjtő választja, megjelenik egy hivatkozást a weblapot, és az SSH-végpont elérési útja. Válassza ki annak hivatkozására.

3. A bejelentkezési párbeszédpanelen a következő hitelesítő adatok segítségével jelentkezzen be: `admin` és `admin`.

4. Kattintson az első lépések lap **hozzon létre új adatcsatorna**.

    ![Új folyamat létrehozása](./media/hdinsight-apps-install-streamsets/get-started.png)

5. Az új folyamat ablakban adja meg az adatcsatorna (a "Hello World") nevét, opcionálisan adjon meg egy leírást, és válassza ki **mentése**.

6. Az adatgyűjtő-konzolon is megjelenik. A Tulajdonságok panelen az adatcsatorna tulajdonságokat jeleníti meg.
 
    ![Adatokat gyűjtő konzol](./media/hdinsight-apps-install-streamsets/pipeline-canvas.png)

7. Most már készen áll a kövesse a [StreamSets oktatóanyag](https://streamsets.com/documentation/datacollector/latest/help/#Tutorial/Tutorial-title.html). Az oktatóanyag első folyamatát létrehozására vonatkozó részletes utasításokat biztosít.

## <a name="next-steps"></a>További lépések

* [StreamSets adatgyűjtő dokumentáció](https://streamsets.com/documentation/datacollector/latest/help/#Getting_Started/GettingStarted_Title.html#concept_htw_ghg_jq).
* [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md): telepítése egy közzé nem tett HDInsight-alkalmazást.
* [HDInsight-alkalmazások közzététele](hdinsight-apps-publish-applications.md): Megtudhatja, hogyan teheti közzé egyéni HDInsight-alkalmazásait az Azure Piactéren.
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: HDInsight-alkalmazás telepítése): Megtudhatja, hogyan adhat meg HDInsight-alkalmazásokat.
* [Parancsfájlművelet Linux-alapú HDInsight-fürtök testreszabása](hdinsight-hadoop-customize-cluster-linux.md): megtudhatja, hogyan telepíthet további alkalmazásokat parancsfájl művelettel.
* [Üres peremhálózati csomópontok használata a Hdinsightban](hdinsight-apps-use-edge-node.md): egy üres élcsomópontot használata a HDInsight-fürtök eléréséhez, és a teszteléshez és a HDInsight-alkalmazások üzemeltetéséhez.
