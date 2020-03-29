---
title: Harmadik féltől származó alkalmazások telepítése az Azure HDInsight szolgáltatásban
description: Ismerje meg, hogyan telepíthet külső gyártótól származó Apache Hadoop-alkalmazásokat az Azure HDInsight szolgáltatásban.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: hrasheed
ms.openlocfilehash: 22d27d50a7d2c304e5d1a04a9a5eaa03d2f0bfa6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366342"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>Külső gyártótól származó Apache Hadoop-alkalmazások telepítése az Azure HDInsight szolgáltatásban

Ismerje meg, hogyan telepíthet egy külső [gyártótól származó Apache Hadoop-alkalmazást](https://hadoop.apache.org/) az Azure HDInsight-ra. A saját alkalmazások telepítéséről az [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md) című cikk tartalmaz útmutatást.

A HDInsight-alkalmazások olyan alkalmazások, amelyeket a felhasználók hdinsight-fürtre telepíthetnek. Ezek az alkalmazások lehetnek a Microsoft, független szoftvergyártók (ISV-k) vagy a felhasználók fejlesztései.  

Az alábbi lista a közzétett alkalmazásokat tartalmazza:

|Alkalmazás |Fürt típusa(i) | Leírás |
|---|---|---|
|[Méretarányos intelligencia platform](https://azuremarketplace.microsoft.com/marketplace/apps/atscaleinc.atscale) |Hadoop |Az AtScale a HDInsight-fürtöt kibővített OLAP-kiszolgálóvá alakítja, így több milliárd adatsort kérdezhet le interaktívan a már ismert, saját és kedvelt üzletiintelligencia-eszközökkel – a Microsoft Exceltől, a Power BI-tól, a Tableau Software-től a QlikView-ig. |
|[CDAP a HDInsighthoz](https://azuremarketplace.microsoft.com/marketplace/apps/cask.cdap-for-hdinsight) |HBase |A CDAP a big data első egységes integrációs platformja, amely felgyorsítja a Hadoop értékéhez szükséges időt, és lehetővé teszi az informatikai szolgáltatások önkiszolgáló adatok szolgáltatását. A nyílt forráskódú és bővíthető CDAP megszünteti az innováció akadályait. Követelmények: 4 régió csomópontok, min D3 v2. |
|[Datameer között](https://azuremarketplace.microsoft.com/marketplace/apps/datameer.datameer) |Hadoop |A Datameer önkiszolgáló, méretezhető platformja az adatok elemzésre való előkészítéséhez, feltárásához és szabályozásához felgyorsítja az összetett többforrású adatok értékes, üzleti használatra kész információkká történő beszámítását, és gyorsabb, intelligensebb betekintést nyújt nagyvállalati szinten. |
|[Dataiku DSS a HDInsight-on](https://azuremarketplace.microsoft.com/marketplace/apps/dataiku.dss-on-hdi) |Hadoop, Szikra |A Dataiku DSS egy vállalati adatelemzési platformon, amely lehetővé teszi az adatszakértők és adatelemzők együttműködését az új adattermékek és szolgáltatások hatékonyabb tervezésében és futtatásában, a nyers adatok hatásos előrejelzésekké alakítása érdekében. |
|[WANdisco Fusion HDI alkalmazás](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.fusion-hdi-app) |Hadoop, Szikra,HBase,Vihar,Kafka |Az adatok konzisztensként tartása elosztott környezetben hatalmas adatműveletek kihívás. A WANdisco Fusion, egy nagyvállalati szintű szoftverplatform, megoldja ezt a problémát azáltal, hogy lehetővé teszi a strukturálatlan adatok konzisztenciáját bármilyen környezetben. |
|[H2O SparklingWater a HDInsight-hoz](https://azuremarketplace.microsoft.com/marketplace/apps/h2o-ai.h2o-sparklingwater) |Spark |A H2O Sparkling Water a következő elosztott algoritmusokat támogatja: GLM, Naiv Bayes, Elosztott véletlenszerű erdő, Színátmenet-kisegítő gép, Mély neurális hálózatok, mély tanulás, K-eszközök, PCA, generalizált alacsony rangú modellek, anomáliadetektálás, autoenkódolók. |
|[Striim a HDInsight valós idejű adatintegrációjához](https://azuremarketplace.microsoft.com/marketplace/apps/striim.hdinsightintegration) |Hadoop,HBase,Vihar,Szikra,Kafka |A Striim (ejtsd: "stream") egy végpontok közötti streamelési adatintegráció + intelligencia platform, amely lehetővé teszi a különböző adatfolyamok folyamatos betöltését, feldolgozását és elemzését. |
|[Jumbune Nagyvállalati gyorsuló BigData-elemzés](https://azuremarketplace.microsoft.com/marketplace/apps/impetus-infotech-india-pvt-ltd.impetus_jumbune) |Hadoop, Szikra |Magas szinten, Jumbune segíti a vállalkozások által, 1. Tez, MapReduce & Spark motor alapú Hive, Java, Scala számítási feladatok teljesítményének felgyorsítása. 2. Proaktív Hadoop-fürt figyelése, 3. Adatminőség-kezelés létrehozása elosztott fájlrendszeren. |
|[Kyligence Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/kyligence.kyligence) |Hadoop,HBase,Szikra |Powered by Apache Kylin, Kyligence Enterprise lehetővé teszi BI a Big Data. A Hadoop vállalati OLAP motorjaként a Kyligence Enterprise lehetővé teszi az üzleti elemző számára, hogy a Hadoop BI-ját az iparági szabványnak megfelelő adattárház- és BI-módszertannal építészítse meg. |
|[Starburst Presto az Azure HDInsight-hoz](https://azuremarketplace.microsoft.com/marketplace/apps/starburst.starburst-presto) |Hadoop |A Presto egy gyors és skálázható elosztott SQL-lekérdezési motor. A tárolás és a számítás elkülönítésére kiépülő Presto tökéletes az Azure Data Lake Storage, az Azure Blob Storage, az SQL és a NoSQL-adatbázisok és más adatforrások adatainak lekérdezéséhez. |
|[StreamSets adatgyűjtő a HDInsight-felhőhöz](https://azuremarketplace.microsoft.com/marketplace/apps/streamsets.streamsets-data-collector-hdinsight) |Hadoop,HBase,Spark,Kafka |A StreamSets Data Collector egy könnyű, nagy teljesítményű motor, amely valós időben streameli az adatokat. Az Adatgyűjtő segítségével irányíthatja és dolgozhatja fel az adatfolyamokban lévő adatokat. Jön-val egy 30 napos próba engedély. |
|[Trifacta Wrangler Vállalat](https://azuremarketplace.microsoft.com/marketplace/apps/trifacta.tr01) |Hadoop, Szikra,HBase |A Trifacta Wrangler Enterprise for HDInsight támogatja a nagyvállalati szintű adathuzavgot bármilyen adatmérethez. A Trifacta Azure-on való futtatásának költsége a Trifacta előfizetési költségek és a virtuális gépek Azure-infrastruktúra költségeinek kombinációja. |
|[Unifi adatplatform](https://unifisoftware.com/platform/) |Hadoop,HBase,Vihar,Szikra |Az Unifi adatplatform egy zökkenőmentesen integrált önkiszolgáló adateszköz-csomag, amelynek célja, hogy képessé tegye az üzleti felhasználókat a növekvő bevételt, a költségek csökkentésére vagy a működési összetettségre irányuló adatkihívások kezelésére. |
|[Unraveldata APM](https://azuremarketplace.microsoft.com/marketplace/apps/unravel-data.unravel-app) |Spark |A HDInsight Spark-fürt adatkezelési alkalmazásának megfejtése. |
|[Vízvonal AI-alapú adatkatalógus](https://azuremarketplace.microsoft.com/marketplace/apps/waterline_data.waterline_data) |Spark |A vízvonal-katalógusok, -rendszerezések és -szabályozások az adatok at AI használatával automatikusan címkézhetik az adatokat üzleti feltételekkel. A Waterline üzleti írástudó katalógusa kritikus fontosságú, sikeres összetevő az önkiszolgáló elemzéshez, a megfelelőséghez és irányításhoz, valamint az informatikai menedzsment kezdeményezésekhez. |

A cikkben szereplő utasítások az Azure Portalon alapulnak. Exportálhatja az Azure Resource Manager-sablont a portálról, vagy beszerezheti az Erőforrás-kezelő sablon másolatát a szállítóktól, és használhatja az Azure PowerShellt és az Azure Classic CLI-t a sablon üzembe helyezéséhez.  Lásd: [Apache Hadoop-fürtök létrehozása a HDInsight-on az Erőforrás-kezelő sablonok használatával című témakört.](hdinsight-hadoop-create-linux-clusters-arm-templates.md)

## <a name="prerequisites"></a>Előfeltételek
Ha egy meglévő HDInsight-fürtre kíván HDInsight-alkalmazásokat telepíteni, HDInsight-fürttel kell rendelkeznie. A fürt létrehozását lásd: [Fürtök létrehozása](hadoop/apache-hadoop-linux-tutorial-get-started.md). HDInsight-alkalmazásokat HDInsight-fürt létrehozása közben is telepíthet.

## <a name="install-applications-to-existing-clusters"></a>Alkalmazások telepítése a meglévő fürtökre
Az alábbi eljárásból megtudhatja, hogyan telepíthet HDInsight-alkalmazásokat egy meglévő HDInsight-fürtbe.

**HDInsight-alkalmazás telepítése**

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. A bal oldali menüben keresse meg az **Összes szolgáltatás** > **Analytics** > **HDInsight-fürtjét.**
3. Válasszon ki egy HDInsight-fürtöt a listából.  Ha még nincs ilyen fürtje, hozzon létre egyet most.  Lásd: [Fürtök létrehozása](hadoop/apache-hadoop-linux-tutorial-get-started.md).
4. A **Beállítások** kategóriában válassza az **Alkalmazások**lehetőséget. A telepített alkalmazások listáját a főablakban láthatja. 
   
    ![HDInsight-alkalmazások menü a portálon](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. Válassza a **menü +Hozzáadás parancsát.** Megtekintheti az elérhető alkalmazások listáját.  Ha **a +Add** szürkén jelenik meg, az azt jelenti, hogy a HDInsight-fürt ezen verziójához nincsenek alkalmazások.
   
    ![HDInsight-alkalmazások, elérhető alkalmazások](./media/hdinsight-apps-install-applications/hdinsight-apps-list1.png)
6. Válassza ki az egyik rendelkezésre álló alkalmazások, majd kövesse az utasításokat, hogy elfogadja a jogi feltételeket.

A telepítési állapota a portálértesítésekből látható (válassza a csengő ikonját a portál tetején). Az alkalmazás telepítése után az alkalmazás megjelenik a Telepített alkalmazások listában.

## <a name="install-applications-during-cluster-creation"></a>Alkalmazások telepítése fürtlétrehozás során

HDInsight-alkalmazásokat fürt létrehozása közben is telepíthet. A HDInsight-alkalmazásokat azt követően telepíti a rendszer, hogy a fürt létrejött, és futó állapotba került. Ha alkalmazásokat szeretne telepíteni a fürt létrehozása során az Azure Portalon, a **Konfiguráció + árképzés** lapon válassza a + Alkalmazás **hozzáadása**lehetőséget.

![Az Azure Portal fürtkonfigurációs alkalmazásai](./media/hdinsight-apps-install-applications/azure-portal-cluster-configuration-applications.png)

## <a name="list-installed-hdinsight-apps-and-properties"></a>Telepített HDInsight-alkalmazások és az alkalmazástulajdonságok listázása
A portál megjeleníti a fürthöz tartozó telepített HDInsight-alkalmazásokat, valamint az egyes telepített alkalmazásokhoz tartozó tulajdonságokat.

**HDInsight-alkalmazások és megjelenítési tulajdonságok listázása**

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. A bal oldali menüben keresse meg az **Összes szolgáltatás** > **Analytics** > **HDInsight-fürtjét.**
3. Válasszon ki egy HDInsight-fürtöt a listából.
4. A **Beállítások** kategóriában válassza az **Alkalmazások**lehetőséget. A telepített alkalmazások listáját a főablakban láthatja. 
   
    ![HDInsight-alkalmazások, telepített alkalmazások](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. Válassza ki a tulajdonság megjelenítéséhez a telepített alkalmazások egyikét. A tulajdonságlista:

    |Tulajdonság | Leírás |
    |---|---|
    |App neve |Alkalmazás neve. |
    |status |Alkalmazás állapota. |
    |Weboldal |A peremhálózati csomópontra telepített webalkalmazás URL-címe. A hitelesítő adatok ugyanazok, mint amelyeket a fürthöz beállított HTTP-felhasználónál használt. |
    |SSH-végpont |Az SSH segítségével csatlakozhat a peremhálózati csomóponthoz. Az SSH hitelesítő adatai ugyanazok, mint amelyeket a fürthöz beállított SSH-felhasználónál használt. További információk: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md). |
    |Leírás | A pályázat leírása. |

6. Alkalmazás törléséhez kattintson a jobb gombbal az alkalmazásra, majd kattintson a helyi menü **Törlés parancsára.**

## <a name="connect-to-the-edge-node"></a>Csatlakozás az élcsomóponthoz
Az élcsomóponthoz HTTP és SSH segítségével csatlakozhat. A végpont adatait megtalálja a [portálon](#list-installed-hdinsight-apps-and-properties). További információk: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

A HTTP végpont hitelesítő adatai ugyanazok, mint amelyeket a HDInsight-fürthöz beállított HTTP-felhasználónál használt; az SSH-végpont hitelesítő adatai ugyanazok, mint amelyeket a HDInsight-fürthöz beállított SSH-felhasználónál használt.

## <a name="troubleshoot"></a>Hibaelhárítás
Lásd: [A telepítési folyamat hibaelhárítása](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation).

## <a name="next-steps"></a>További lépések
* [Egyéni HDInsight-alkalmazások telepítése:](hdinsight-apps-install-custom-applications.md)megtudhatja, hogyan telepíthet közzé nem közzétett HDInsight-alkalmazásokat a HDInsightba.
* [HDInsight-alkalmazások közzététele](hdinsight-apps-publish-applications.md): Megtudhatja, hogyan teheti közzé egyéni HDInsight-alkalmazásait az Azure Piactéren.
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: HDInsight-alkalmazás telepítése): Megtudhatja, hogyan adhat meg HDInsight-alkalmazásokat.
* [Customize Linux-based HDInsight clusters using Script Action](hdinsight-hadoop-customize-cluster-linux.md) (Linux-alapú HDInsight-fürtök testreszabása parancsfájlműveletek segítségével): megtudhatja, hogyan telepíthet további alkalmazásokat parancsfájlműveletek használatával.
* [Linux-alapú Apache Hadoop-fürtöket hozhat létre a HDInsightban az Erőforrás-kezelő sablonjaival:](hdinsight-hadoop-create-linux-clusters-arm-templates.md)ismerje meg, hogyan hívhatja meg az Erőforrás-kezelő sablonokat HDInsight-fürtök létrehozásához.
* [Use empty edge nodes in HDInsight](hdinsight-apps-use-edge-node.md) (Üres élcsomópontok használata a HDInsightban): a cikk bemutatja, hogyan lehet üres élcsomópontot használni egy HDInsight-fürt elérésére, HDInsight-alkalmazások tesztelésére és HDInsight-alkalmazások üzemeltetésére.

