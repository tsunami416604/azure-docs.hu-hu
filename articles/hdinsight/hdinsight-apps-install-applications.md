---
title: Külső gyártótól származó Hadoop-alkalmazások telepítése az Azure HDInsighton
description: Megismerheti, hogyan telepíthet külső gyártótól származó Hadoop-alkalmazásokat az Azure HDInsighton.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jasonh
ms.openlocfilehash: c4d8f6fb1804ff48899ebb96d4c4248f337b56ad
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39591975"
---
# <a name="install-third-party-hadoop-applications-on-azure-hdinsight"></a>Külső gyártótól származó Hadoop-alkalmazások telepítése az Azure HDInsighton

Ismerje meg, hogyan telepítheti egy külső Hadoop-alkalmazásokat Azure HDInsight. A saját alkalmazások telepítéséről az [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md) című cikk tartalmaz útmutatást.

Egy HDInsight-alkalmazás olyan alkalmazás, amely a felhasználók telepíthetik az HDInsight-fürtön. Ezek az alkalmazások lehetnek a Microsoft, független szoftvergyártók (ISV-k) vagy a felhasználók fejlesztései.  

Az alábbi listában a közzétett alkalmazást mutatja be:

* **AtScale adatelemzési Platform** bekapcsolja a HDInsight-fürt horizontális felskálázás OLAP-kiszolgálón. Az alkalmazás lehetővé teszi interaktív használatával a BI-eszközök a Microsoft Excel, a Power BI, a Tableau Software, a QlikView adatsor milliárd lekérdezéséhez.
* **A cask CDAP for HDInsight** nyújt az első egységes integrációs platform big Data-80 %-kal kivágása idejét és data Lake tárolók éles környezetben. Ez az alkalmazás kizárólag a Standard HBase 3.4-fürtöket támogatja.
* **A HDInsight DATAIKU DDS** lehetővé teszi az adatok szakemberek prototípusként, felépíthet és telepíthet rendkívül specifikus szolgáltatások, amelyek átalakítják a nyers adatokat hatékony üzleti előrejelzésekké.
* **A HDInsight (béta) a H2O mesterséges intelligencia** H2O Sparkling Water a következő elosztott algoritmusokat támogatja: GLM, Naiv Bayes, elosztott, véletlenszerű erdő, átmenetes kiemelési gép, Neurális hálózatokat, Deep learning, a K-közép, PEM, Alacsony rangsorolják modellek, rendellenességek észlelése és Autoencoders általánosítva.
* **Kyligence Analytics Platform** Kyligence Analytics Platform (másolatot KAP) egy nagyvállalati használatra kész Apache Kylin és Apache Hadoop technológián adatraktár; a másodperc törtrésze biztosít nagy méretű adatkészlet késés lekérdezése és egyszerűbbé teszi az adatelemzés az üzleti felhasználók és az elemzők. 
* **Adat-előkészítési Paxata önkiszolgáló**
* **Spark-feladatkiszolgálót KNIME Spark-végrehajtó** Spark-feladatkiszolgálót KNIME Spark-végrehajtó HDInsight-fürtök csatlakoztatásához az KNIME elemzési Platform szolgál.
* A **Streamsets Data Collector for HDInsight** olyan, átfogó funkciókat kínáló integrált fejlesztőkörnyezetet (IDE) biztosít, amelyben bármely irányú, stream- és batch-adatokat összefűző feldolgozási folyamatok tervezése, tesztelése, üzembe helyezése és felügyelete lehetséges, továbbá különféle streamen belüli transzformációkat is tartalmaz, és mindehhez nincs szükség egyedi kód írására. 
* **[Trifacta](http://www.trifacta.com/)**  lehetővé teszi, az adatmérnökök és az elemzők hatékonyabb vizsgálata, és már ma a különféle adatok előkészítése felügyelniük gépi tanulási átütő felhasználói élmény, a munkafolyamat és architektúra kell megadnia.
* **A WANdisco Fusion HDI alkalmazás** lehetővé teszi, hogy egységes kapcsolat adatait, ha azok módosulnak, ahol nem található. Is tartalmaz a hozzáférést az adataihoz bármikor és bárhol az állásidő nélkül és megszakítás nélkül.

A cikkben szereplő utasítások az Azure Portalon alapulnak. Az Azure Resource Manager-sablont a portálról is exportálhatja, vagy beszerezheti a Resource Manager-sablon egy példányát a szállítóktól, majd az Azure PowerShell és az Azure parancssori felület segítségével üzembe helyezheti a sablont.  Lásd: [Hadoop-fürtök létrehozása a Resource Manager-sablonok használatával HDInsight](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>Előfeltételek
Ha egy meglévő HDInsight-fürtre kíván HDInsight-alkalmazásokat telepíteni, HDInsight-fürttel kell rendelkeznie. A fürt létrehozását lásd: [Fürtök létrehozása](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster). HDInsight-alkalmazásokat HDInsight-fürt létrehozása közben is telepíthet.

## <a name="install-applications-to-existing-clusters"></a>Alkalmazások telepítése a meglévő fürtökre
Az alábbi eljárásból megtudhatja, hogyan telepíthet HDInsight-alkalmazásokat egy meglévő HDInsight-fürtbe.

**Egy HDInsight-alkalmazás telepítése**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali menüben kattintson a **HDInsight Clusters** (HDInsight-fürtök) elemre.
3. Kattintson a kívánt HDInsight-fürtre.  Ha még nincs ilyen fürtje, hozzon létre egyet most.  Lásd: [Fürtök létrehozása](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
4. Kattintson az **Applications** (Alkalmazások) elemre a **Configurations** (Konfigurációk) kategóriában. Láthatja, hogy a telepített alkalmazások listáját. Ha nem látja az Alkalmazások elemet, az azt jelenti, hogy a HDInsight-fürt jelen verziójához nem érhetők el alkalmazások.
   
    ![HDInsight-alkalmazások menü a portálon](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. Kattintson a **Hozzáadás** a menüből. Megjelenik a meglévő HDInsight-alkalmazások listája.
   
    ![HDInsight-alkalmazások, elérhető alkalmazások](./media/hdinsight-apps-install-applications/hdinsight-apps-list.png)
6. Kattintson az egyik az elérhető alkalmazásokat, és kövesse az utasításokat követve fogadja el a jogi feltételeket.

A portál értesítései között megtekintheti a telepítési állapotot (ehhez kattintson a portál felső részén található harang ikonra). Az alkalmazás telepítése után az alkalmazás megjelenik a telepített alkalmazások listájában.

## <a name="install-applications-during-cluster-creation"></a>Alkalmazások telepítése fürtlétrehozás során
HDInsight-alkalmazásokat fürt létrehozása közben is telepíthet. A HDInsight-alkalmazásokat azt követően telepíti a rendszer, hogy a fürt létrejött, és futó állapotba került. Alkalmazások telepítése az Azure portal használatával, a fürt létrehozása során, használja a--egyéni – a beállítás helyett az alapértelmezett – gyors létrehozása – lehetőséget.

## <a name="list-installed-hdinsight-apps-and-properties"></a>Telepített HDInsight-alkalmazások és az alkalmazástulajdonságok listázása
A portál megjeleníti a fürthöz tartozó telepített HDInsight-alkalmazásokat, valamint az egyes telepített alkalmazásokhoz tartozó tulajdonságokat.

**HDInsight-alkalmazások listázása és az alkalmazástulajdonságok megjelenítése**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali menüben kattintson a **HDInsight Clusters** (HDInsight-fürtök) elemre. 
3. Kattintson a kívánt HDInsight-fürtre.
4. A **beállítások**, kattintson a **alkalmazások** alatt a **konfigurációs** kategória. A telepített alkalmazások a jobb oldalon jelennek meg. 
   
    ![HDInsight-alkalmazások, telepített alkalmazások](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. A tulajdonságok megjelenítéséhez kattintson a kívánt telepített alkalmazásra. A tulajdonság tartalmazza:
   
   * Alkalmazásnév: az alkalmazás neve.
   * Állapot: az alkalmazás állapota. 
   * Weblap: Az URL-cím a webalkalmazás üzembe helyezett az élcsomópont felé. A hitelesítő adatok ugyanazok, mint amelyeket a fürthöz beállított HTTP-felhasználónál használt.
   * HTTP-végpont: a hitelesítő adatok ugyanazok, mint amelyeket a fürthöz beállított HTTP-felhasználónál használt. 
   * SSH-végpont: SSH segítségével kapcsolódni az élcsomóponthoz. Az SSH hitelesítő adatai ugyanazok, mint amelyeket a fürthöz beállított SSH-felhasználónál használt. További információk: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).
6. Egy alkalmazás törléséhez kattintson a jobb gombbal az alkalmazást, és kattintson **törlése** a helyi menüből.

## <a name="connect-to-the-edge-node"></a>Csatlakozás az élcsomóponthoz
Az élcsomóponthoz HTTP és SSH segítségével csatlakozhat. A végpont adatait megtalálja a [portálon](#list-installed-hdinsight-apps-and-properties). További információk: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

A HTTP végpont hitelesítő adatai ugyanazok, mint amelyeket a HDInsight-fürthöz beállított HTTP-felhasználónál használt; az SSH-végpont hitelesítő adatai ugyanazok, mint amelyeket a HDInsight-fürthöz beállított SSH-felhasználónál használt.

## <a name="troubleshoot"></a>Hibaelhárítás
Lásd: [A telepítési folyamat hibaelhárítása](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation).

## <a name="next-steps"></a>További lépések
* [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md): útmutató HDInsight közzé nem tett HDInsight-alkalmazás üzembe helyezése.
* [HDInsight-alkalmazások közzététele](hdinsight-apps-publish-applications.md): Megtudhatja, hogyan teheti közzé egyéni HDInsight-alkalmazásait az Azure Piactéren.
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: HDInsight-alkalmazás telepítése): Megtudhatja, hogyan adhat meg HDInsight-alkalmazásokat.
* [Customize Linux-based HDInsight clusters using Script Action](hdinsight-hadoop-customize-cluster-linux.md) (Linux-alapú HDInsight-fürtök testreszabása parancsfájlműveletek segítségével): megtudhatja, hogyan telepíthet további alkalmazásokat parancsfájlműveletek használatával.
* [Create Linux-based Hadoop clusters in HDInsight using Resource Manager templates](hdinsight-hadoop-create-linux-clusters-arm-templates.md) (Linux-alapú Hadoop-fürtök létrehozása a HDInsightban Resource Manager-sablonok segítségével): Megtudhatja, hogyan hívhat meg Resource Manager-sablonokat HDInsight-fürtök létrehozásához.
* [Use empty edge nodes in HDInsight](hdinsight-apps-use-edge-node.md) (Üres élcsomópontok használata a HDInsightban): a cikk bemutatja, hogyan lehet üres élcsomópontot használni egy HDInsight-fürt elérésére, HDInsight-alkalmazások tesztelésére és HDInsight-alkalmazások üzemeltetésére.

