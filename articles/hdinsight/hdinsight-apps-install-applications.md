---
title: Harmadik féltől származó alkalmazások telepítése Azure HDInsight
description: Megismerheti, hogyan telepíthet külső gyártótól származó Hadoop-alkalmazásokat az Azure HDInsighton.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 47a634ca1c4e904cc1054db3a834483489ade0ec
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53093556"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>Az Azure HDInsight külső Apache Hadoop-alkalmazások telepítése

Ismerje meg, hogyan telepítheti egy harmadik fél [Apache Hadoop](https://hadoop.apache.org/) alkalmazás az Azure HDInsight a. A saját alkalmazások telepítéséről az [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md) című cikk tartalmaz útmutatást.

Egy HDInsight-alkalmazás olyan alkalmazás, amely a felhasználók telepíthetik az HDInsight-fürtön. Ezek az alkalmazások lehetnek a Microsoft, független szoftvergyártók (ISV-k) vagy a felhasználók fejlesztései.  

Az alábbi listában a közzétett alkalmazást mutatja be:

* **AtScale adatelemzési Platform** bekapcsolja a HDInsight-fürt horizontális felskálázás OLAP-kiszolgálón. Az alkalmazás lehetővé teszi interaktív használatával a BI-eszközök a Microsoft Excel, a Power BI, a Tableau Software, a QlikView adatsor milliárd lekérdezéséhez.
* **A cask CDAP for HDInsight** nyújt az első egységes integrációs platform big Data-80 %-kal kivágása idejét és data Lake tárolók éles környezetben. Ez az alkalmazás kizárólag a Standard HBase 3.4-fürtöket támogatja.
* **A HDInsight DATAIKU DDS** lehetővé teszi az adatok szakemberek prototípusként, felépíthet és telepíthet rendkívül specifikus szolgáltatások, amelyek átalakítják a nyers adatokat hatékony üzleti előrejelzésekké.
* **Datameer** önkiszolgáló méretezhető platformra való előkészítéséhez, feltárása, és gyorsítja vonatkozó az adatok elemzési információkká értékes üzleti használatra kész, gyorsabb és okosabb elemzések kézbesítése összetett többforrású adatokat bekapcsolása egy a nagyvállalati szintű.
* **A HDInsight (béta) a H2O mesterséges intelligencia** H2O Sparkling Water a következő elosztott algoritmusokat támogatja: GLM, Naiv Bayes, elosztott, véletlenszerű erdő, átmenetes kiemelési gép, Neurális hálózatokat, Deep learning, a K-közép, PEM, Alacsony rangsorolják modellek, rendellenességek észlelése és Autoencoders általánosítva.
* **Kyligence vállalati**, Apache Kylin működteti, azonnali elemzéseket biztosít a nagy méretű adatkészletek az üzleti felhasználók és az adatelemzők. Az élvonalbeli gépi tanulási technológia és intelligens adatmodellezési funkciót Ez nagyban növeli a hatékonyságot, big data-elemzés. 
* **Adat-előkészítési Paxata önkiszolgáló**
* **Spark-feladatkiszolgálót KNIME Spark-végrehajtó** Spark-feladatkiszolgálót KNIME Spark-végrehajtó HDInsight-fürtök csatlakoztatásához az KNIME elemzési Platform szolgál.
* **A Presto buborék** Presto van a gyors, skálázható elosztott SQL lekérdezési motorja. Tárolási és számítási szétválasztása lett tervezve, Presto tökéletes megoldás az adatok az Azure Data Lake Storage, Azure Blob Storage, SQL és NoSQL-adatbázisok és más adatforrásokhoz.
* A **Streamsets Data Collector for HDInsight** olyan, átfogó funkciókat kínáló integrált fejlesztőkörnyezetet (IDE) biztosít, amelyben bármely irányú, stream- és batch-adatokat összefűző feldolgozási folyamatok tervezése, tesztelése, üzembe helyezése és felügyelete lehetséges, továbbá különféle streamen belüli transzformációkat is tartalmaz, és mindehhez nincs szükség egyedi kód írására. 
* **Striim** (ejtsd a "stream") van egy teljes körű streamelési adatok integrálását és üzletiintelligencia-platform, folyamatos adatfeldolgozást, feldolgozási és elemzési különálló adatfolyamok engedélyezése.
* **[Trifacta](http://www.trifacta.com/)**  lehetővé teszi, az adatmérnökök és az elemzők hatékonyabban vizsgálata, és már ma a különféle adatok előkészítése gépi tanulással biztosít egy átütő felhasználói élmény, a munkafolyamat és architektúra használatával történt.
* **Unifi Adatplatform** zökkenőmentesen integrált csomagja, önkiszolgáló eszközöket a kiszolgálókon adatkezelési kihívást, hogy a meghajtó növekményes bevétel, csökkentheti a költségeket és az üzemeltetés bonyolult üzleti felhasználó ellátására készült. 
* **A WANdisco Fusion HDI alkalmazás** lehetővé teszi, hogy egységes kapcsolat adatait, ha azok módosulnak, ahol nem található. Is tartalmaz a hozzáférést az adataihoz bármikor és bárhol az állásidő nélkül és megszakítás nélkül.
* **Vízvonallal** katalógusokban, rendezi, és automatikus címkézés adatok az üzleti feltételeinek AI használata az adatok szabályozza. Üzleti literate katalógus vízvonallal a része a kritikus fontosságú, sikeres önkiszolgáló elemzési, megfelelőségi és szabályozási és informatikai kezdeményezések előrehaladását.

A cikkben szereplő utasítások az Azure Portalon alapulnak. Az Azure Resource Manager-sablonok exportálása a portálról vagy a Resource Manager-sablon másolatának beszerzése szállítóktól származó, és a sablon üzembe helyezése az Azure PowerShell és a klasszikus Azure CLI használatával.  Lásd: [Apache Hadoop-fürtök létrehozása a Resource Manager-sablonok használatával HDInsight](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

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
* [Linux-alapú Apache Hadoop-fürtök létrehozása a Resource Manager-sablonok használatával HDInsight](hdinsight-hadoop-create-linux-clusters-arm-templates.md): ismerje meg, hogyan hívhat meg Resource Manager-sablonokat HDInsight-fürtök létrehozásához.
* [Use empty edge nodes in HDInsight](hdinsight-apps-use-edge-node.md) (Üres élcsomópontok használata a HDInsightban): a cikk bemutatja, hogyan lehet üres élcsomópontot használni egy HDInsight-fürt elérésére, HDInsight-alkalmazások tesztelésére és HDInsight-alkalmazások üzemeltetésére.

