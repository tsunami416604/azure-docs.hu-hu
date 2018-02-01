---
title: "Külső gyártótól származó Hadoop-alkalmazások telepítése az Azure HDInsighton | Microsoft Docs"
description: "Megismerheti, hogyan telepíthet külső gyártótól származó Hadoop-alkalmazásokat az Azure HDInsighton."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: eaf5904d-41e2-4a5f-8bec-9dde069039c2
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/06/2017
ms.author: jgao
ms.openlocfilehash: 9924a9656f2e2e268356b8ce293d58afc3d535a9
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2018
---
# <a name="install-third-party-hadoop-applications-on-azure-hdinsight"></a>Külső gyártótól származó Hadoop-alkalmazások telepítése az Azure HDInsighton

Útmutató: Azure hdinsight külső Hadoop alkalmazások telepítésére. A saját alkalmazások telepítéséről az [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md) című cikk tartalmaz útmutatást.

HDInsight-alkalmazások olyan alkalmazás, amely a felhasználók telepíthetik a HDInsight-fürtöt. Ezek az alkalmazások lehetnek a Microsoft, független szoftvergyártók (ISV-k) vagy a felhasználók fejlesztései.  

Az alábbi lista tartalmazza a közzétett alkalmazásokhoz:

* **AtScale Eszközintelligencia Platform** bekapcsolja a HDInsight-fürt kibővített OLAP-kiszolgálót. Az alkalmazás lehetővé teszi a lekérdezés egy interaktív használatával az Üzletiintelligencia-eszközök a Microsoft Excel, a Power bi Tableau szoftver a QlikView adatsorokat.
* **A HDInsight CDAP cask** az első egyesített integrációs platformot kínál a big Data típusú adatok, alkalmazások és adatok tavakat üzemi 80 %-kal kivágása az állásidő. Ez az alkalmazás kizárólag a Standard HBase 3.4-fürtöket támogatja.
* **A HDInsight DATAIKU DDS** lehetővé teszi az adatok szakemberek prototípusként, elkészítéséhez és magas adott szolgáltatások telepítését, amelyek nyers adatok átalakítása impactful üzleti előrejelzéseket.
* **Datameer**: a [Datameer](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft) interaktív módot kínál az elemzőknek a Big Data-eredmények felfedezésére, elemzésére és vizuális megjelenítésére. Vonjon be további adatforrásokat, fedezzen fel új kapcsolatokat, és kapjon még gyorsabban választ kérdéseire.
* **A HDInsight (béta) H2O mesterséges Eszközintelligencia** H2O készült vízjel támogatja a következő elosztott algoritmusok: GLM, natív Bayes, elosztott véletlenszerű erdő, átmenetes kiemelése gép, Neurális hálózatokat, mély tanulási, a K-közép, PEM, Alacsony Rank modellek, Anomáliadetektálás és Autoencoders általánosítva van.
* **Kyligence Analytics Platform** Kyligence Analytics Platform (KAP) Apache Kylin és Apache Hadoop kapcsolva, nagyvállalati használatra kész adatraktár; a alárendelt második biztosít késés jelentős mértékű adatkészlethez lekérdezésére és egyszerűbbé teszi a adatelemzés üzleti felhasználók és az elemzők. 
* **Paxata önkiszolgáló adatok előkészítése**
* **SnapLogic Hadooplex** hdinsighton futó a SnapLogic Hadooplex lehetővé teszi az ügyfelek önkiszolgáló adatfeldolgozást és szinte bármilyen forrás előkészítése a Microsoft Azure cloud platform megadásával üzleti elemzéseket gyorsabb eléréséhez.
* **Spark-feladatkiszolgálót KNIME Spark végrehajtó** Spark-feladatkiszolgálót KNIME Spark végrehajtó és a KNIME Analytics Platform csatlakoztatásával a HDInsight-fürtök szolgál.
* A **Streamsets Data Collector for HDInsight** olyan, átfogó funkciókat kínáló integrált fejlesztőkörnyezetet (IDE) biztosít, amelyben bármely irányú, stream- és batch-adatokat összefűző feldolgozási folyamatok tervezése, tesztelése, üzembe helyezése és felügyelete lehetséges, továbbá különféle streamen belüli transzformációkat is tartalmaz, és mindehhez nincs szükség egyedi kód írására. 
* **WANdisco Fusion HDI App** lehetővé teszi, hogy konzisztens létesített kapcsolat adatok bárhol található változásakor. Ön hozzáférést bármikor elérheti az adatait és biztosít bárhol nincs állásidő és a nem működőképes.

A cikkben szereplő utasítások az Azure Portalon alapulnak. Az Azure Resource Manager-sablont a portálról is exportálhatja, vagy beszerezheti a Resource Manager-sablon egy példányát a szállítóktól, majd az Azure PowerShell és az Azure parancssori felület segítségével üzembe helyezheti a sablont.  Lásd: [Hadoop létrehozása a Resource Manager-sablonok használatával HDInsight clusters](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>Előfeltételek
Ha egy meglévő HDInsight-fürtre kíván HDInsight-alkalmazásokat telepíteni, HDInsight-fürttel kell rendelkeznie. A fürt létrehozását lásd: [Fürtök létrehozása](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster). HDInsight-alkalmazásokat HDInsight-fürt létrehozása közben is telepíthet.

## <a name="install-applications-to-existing-clusters"></a>Alkalmazások telepítése a meglévő fürtökre
Az alábbi eljárásból megtudhatja, hogyan telepíthet HDInsight-alkalmazásokat egy meglévő HDInsight-fürtbe.

**HDInsight-alkalmazások telepítése**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali menüben kattintson a **HDInsight Clusters** (HDInsight-fürtök) elemre.
3. Kattintson a kívánt HDInsight-fürtre.  Ha még nincs ilyen fürtje, hozzon létre egyet most.  Lásd: [Fürtök létrehozása](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
4. Kattintson az **Applications** (Alkalmazások) elemre a **Configurations** (Konfigurációk) kategóriában. Láthatja, hogy a telepített alkalmazások listáját. Ha nem látja az Alkalmazások elemet, az azt jelenti, hogy a HDInsight-fürt jelen verziójához nem érhetők el alkalmazások.
   
    ![HDInsight-alkalmazások menü a portálon](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. Kattintson a **Hozzáadás** a menüből. 
   
    ![HDInsight-alkalmazások, telepített alkalmazások](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps.png)
   
    Megjelenik a meglévő HDInsight-alkalmazások listája.
   
    ![HDInsight-alkalmazások, elérhető alkalmazások](./media/hdinsight-apps-install-applications/hdinsight-apps-list.png)
6. Kattintson a kívánt alkalmazásra, fogadja el a jogi feltételeket, majd kattintson a **Kiválasztás** gombra.

A portál értesítései között megtekintheti a telepítési állapotot (ehhez kattintson a portál felső részén található harang ikonra). Az alkalmazás telepítése után az alkalmazás megjelenik a telepített alkalmazások listájában.

## <a name="install-applications-during-cluster-creation"></a>Alkalmazások telepítése fürtlétrehozás során
HDInsight-alkalmazásokat fürt létrehozása közben is telepíthet. A HDInsight-alkalmazásokat azt követően telepíti a rendszer, hogy a fürt létrejött, és futó állapotba került. Alkalmazások telepítése a fürt létrehozása az Azure portál használata során, szülőcsoportnak – egyéni – helyett az alapértelmezett – gyors létrehozása – lehetőséget.

## <a name="list-installed-hdinsight-apps-and-properties"></a>Telepített HDInsight-alkalmazások és az alkalmazástulajdonságok listázása
A portál megjeleníti a fürthöz tartozó telepített HDInsight-alkalmazásokat, valamint az egyes telepített alkalmazásokhoz tartozó tulajdonságokat.

**HDInsight-alkalmazások felsorolása, és a Tulajdonságok megjelenítése**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali menüben kattintson a **HDInsight Clusters** (HDInsight-fürtök) elemre. 
3. Kattintson a kívánt HDInsight-fürtre.
4. A **beállítások**, kattintson a **alkalmazások** alatt a **általános** kategóriát. A telepített alkalmazások találhatók, a jobb oldalon. 
   
    ![HDInsight-alkalmazások, telepített alkalmazások](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. A tulajdonságok megjelenítéséhez kattintson a kívánt telepített alkalmazásra. Az XML-tulajdonságlisták:
   
   * Alkalmazásnév: az alkalmazás neve.
   * Állapot: az alkalmazás állapota. 
   * A weblap: Az URL-cím a webalkalmazás az élcsomóponthoz központilag telepített. A hitelesítő adatok ugyanazok, mint amelyeket a fürthöz beállított HTTP-felhasználónál használt.
   * HTTP-végpont: a hitelesítő adatok ugyanazok, mint amelyeket a fürthöz beállított HTTP-felhasználónál használt. 
   * SSH-végpont: Csatlakozás az élcsomóponthoz SSH használhatja. Az SSH hitelesítő adatai ugyanazok, mint amelyeket a fürthöz beállított SSH-felhasználónál használt. További információk: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).
6. Egy alkalmazás törléséhez kattintson a jobb gombbal az alkalmazást, és kattintson **törlése** a helyi menüből.

## <a name="connect-to-the-edge-node"></a>Csatlakozás az élcsomóponthoz
Az élcsomóponthoz HTTP és SSH segítségével csatlakozhat. A végpont adatait megtalálja a [portálon](#list-installed-hdinsight-apps-and-properties). További információk: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

A HTTP végpont hitelesítő adatai ugyanazok, mint amelyeket a HDInsight-fürthöz beállított HTTP-felhasználónál használt; az SSH-végpont hitelesítő adatai ugyanazok, mint amelyeket a HDInsight-fürthöz beállított SSH-felhasználónál használt.

## <a name="troubleshoot"></a>Hibaelhárítás
Lásd: [A telepítési folyamat hibaelhárítása](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation).

## <a name="next-steps"></a>További lépések
* [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md): telepítése egy közzé nem tett HDInsight-alkalmazást.
* [HDInsight-alkalmazások közzététele](hdinsight-apps-publish-applications.md): Megtudhatja, hogyan teheti közzé egyéni HDInsight-alkalmazásait az Azure Piactéren.
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: HDInsight-alkalmazás telepítése): Megtudhatja, hogyan adhat meg HDInsight-alkalmazásokat.
* [Customize Linux-based HDInsight clusters using Script Action](hdinsight-hadoop-customize-cluster-linux.md) (Linux-alapú HDInsight-fürtök testreszabása parancsfájlműveletek segítségével): megtudhatja, hogyan telepíthet további alkalmazásokat parancsfájlműveletek használatával.
* [Create Linux-based Hadoop clusters in HDInsight using Resource Manager templates](hdinsight-hadoop-create-linux-clusters-arm-templates.md) (Linux-alapú Hadoop-fürtök létrehozása a HDInsightban Resource Manager-sablonok segítségével): Megtudhatja, hogyan hívhat meg Resource Manager-sablonokat HDInsight-fürtök létrehozásához.
* [Use empty edge nodes in HDInsight](hdinsight-apps-use-edge-node.md) (Üres élcsomópontok használata a HDInsightban): a cikk bemutatja, hogyan lehet üres élcsomópontot használni egy HDInsight-fürt elérésére, HDInsight-alkalmazások tesztelésére és HDInsight-alkalmazások üzemeltetésére.

