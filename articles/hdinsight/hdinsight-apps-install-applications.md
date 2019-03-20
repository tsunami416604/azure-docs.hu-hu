---
title: Harmadik féltől származó alkalmazások telepítése Azure HDInsight
description: Megismerheti, hogyan telepíthet külső gyártótól származó Hadoop-alkalmazásokat az Azure HDInsighton.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: hrasheed
ms.openlocfilehash: 810f9bb81d367cfe70e59d62d81a9e129cf80e6b
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58225980"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>Az Azure HDInsight külső Apache Hadoop-alkalmazások telepítése

Ismerje meg, hogyan telepítheti egy harmadik fél [Apache Hadoop](https://hadoop.apache.org/) alkalmazás az Azure HDInsight a. A saját alkalmazások telepítéséről az [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md) című cikk tartalmaz útmutatást.

Egy HDInsight-alkalmazás olyan alkalmazás, amely a felhasználók telepíthetik az HDInsight-fürtön. Ezek az alkalmazások lehetnek a Microsoft, független szoftvergyártók (ISV-k) vagy a felhasználók fejlesztései.  

Az alábbi listában a közzétett alkalmazást mutatja be:

|Alkalmazás |Fürt típusa | Leírás |
|---|---|---|
|AtScale Intelligence Platform |Hadoop |AtScale bekapcsolja a HDInsight-fürt horizontális felskálázás OLAP-kiszolgálón, amely lehetővé teszi a lekérdezés több milliárd interaktív módon BI-eszközökkel a már ismert, a tulajdonosa, és dolgozhat – a Microsoft Excel, a Power BI, Tableau Software QlikView az adatsorokat. |
|CDAP 4.2, a HDInsight 4.3. |HBase |CDAP az első egységes integrációs platform big Data-gyorsító idő értékre a önkiszolgáló Hadoop és lehetővé teszi, hogy informatikai. Nyílt forráskódú és bővíthető CDAP eltávolítja a korlátok az innovációt. Követelmények: 4 régió csomópontok, D3 v2 perc. |
|Datameer |Hadoop |A Datameer önkiszolgáló skálázható platformot való előkészítéséhez, vizsgálatára, és az adatok elemzési szabályozó gyorsítja információkká értékes üzleti használatra kész, gyorsabb és okosabb insights jusson el egy vállalati szintű összetett többforrású adatokat bekapcsolásával. |
|A Dataiku DSS a HDInsight |Hadoop, Spark |A Dataiku DSS a egy vállalati adatelemzési platform, amely lehetővé teszi az adatszakértők és adatelemzők együttműködésének köszönhetően csökkenthetőek tervezését és új adatok termékek és -szolgáltatások hatékony, futtatását bekapcsolásával a nyers adatok hatásos előrejelzéseket be. |
|A WANdisco Fusion HDI-alkalmazás – 2.12.3, 2.12.1, 2.11.2 |Hadoop, Spark,HBase,Storm,Kafka |Adatok egységessége az elosztott környezetekben az adatmennyiség nagymértékű műveletek kihívást jelent. A WANdisco Fusion, egy nagyvállalati szintű szoftver platform szerint teszi lehetővé a strukturálatlan adatok konzisztenciájának bármilyen környezetben erre a kérdésre. |
|A HDInsight H2O SparklingWater |Spark |H2O Sparkling Water a következő elosztott algoritmusokat támogatja: GLM, Naiv Bayes, elosztott, véletlenszerű erdő, átmenetes kiemelési gép, Neurális hálózatokat, mély tanulás, K-közép, PEM, alacsony rang modellek általánosítani, rendellenességek észlelése, Autoencoders. |
|A Striim valós idejű adatok HDInsight-integráció |Hadoop,HBase,Storm,Spark,Kafka |A Striim (jelentős "stream") egy – teljes körű streamelési adatok integrálását és adatelemzési platform, folyamatos adatfeldolgozást, feldolgozási és elemzési különálló adatfolyamok engedélyezése. |
|A HDInsight Jumbune |Hadoop, Spark |Magas szinten Jumbune segíti a vállalatok által, 1. Tez felgyorsítása, a MapReduce és a Spark-motort alapú Hive, a Java, a Scala számítási feladatok teljesítményére. 2. Proaktív Hadoop-fürt figyelése, 3. Az elosztott fájlrendszer-kezelés Data Quality meghatározása. |
|Kyligence Enterprise |Hadoop,HBase,Spark |Apache Kylin működteti, a Kyligence vállalati BI, a Big Data lehetővé teszi. Modulként vállalati OLAP hadoop Kyligence vállalati bi-ban hadoop tervezhet az iparági szabványnak megfelelő data warehouse és BI módszertan üzleti elemző lehetővé teszi. |
|Spark-feladatkiszolgálót KNIME Spark-végrehajtó |Spark |Spark-feladatkiszolgálót KNIME Spark-végrehajtó HDInsight-fürtök csatlakoztatásához az KNIME elemzési Platform szolgál. |
|Buborék Presto az Azure HDInsight, a buborék Presto (v0.213-e) |Hadoop |Presto van egy gyors, skálázható elosztott SQL lekérdezési motorja. Tárolási és számítási szétválasztása lett tervezve, Presto tökéletes megoldás az adatok az Azure Data Lake Storage, Azure Blob Storage, SQL és NoSQL-adatbázisok és más adatforrásokhoz. |
|StreamSets Data Collector for HDInsight felhőalapú |Hadoop, HBase, Spark, Kafka |StreamSets Data Collector egy könnyen használható, nagy teljesítményű motor, amely valós időben streameli. Útvonal és az adatok feldolgozása a data-adatfolyamok a gyűjtő használatával. Ez tartalmaz egy 30 napos próbaverziós licencre. |
|[Trifacta Wrangler Enterprise](https://www.trifacta.com/) |Hadoop, Spark, HBase |Trifacta Wrangler Enterprise HDInsight a vállalati adatok rugalmasan méretezhető, az adatok konvertálását támogatja. Azure-on futó Trifacta költsége Trifacta előfizetés költségek és az Azure infrastrukturális költségeit a virtuális gépek kombinációját. |
|3.1 Unifi Data Platform |Hadoop,HBase,Storm,Spark |Lehetővé teheti az üzleti felhasználónak, amelynek célja a adatkezelési kihívást a meghajtó növekményes bevétel, csökkentheti a költségeket és az üzemeltetés bonyolult önkiszolgáló olyan eszközöket, zökkenőmentesen integrált Unifi Adatplatformon olyan. |
|Unraveldata APM |Spark |HDInsight Spark-fürt adatok alkalmazás unravel. |
|A Data Catalog vízvonallal |Spark |Vízvonallal katalógusokban, rendezi és mesterséges Intelligencia használatával automatikus címkeadatok üzleti feltételeket adatok szabályozza. Üzleti literate katalógus vízvonallal a része a kritikus fontosságú, sikeres önkiszolgáló elemzési, megfelelőségi és szabályozási és informatikai kezdeményezések előrehaladását. |

A cikkben szereplő utasítások az Azure Portalon alapulnak. Az Azure Resource Manager-sablonok exportálása a portálról vagy a Resource Manager-sablon másolatának beszerzése szállítóktól származó, és a sablon üzembe helyezése az Azure PowerShell és a klasszikus Azure CLI használatával.  Lásd: [Apache Hadoop-fürtök létrehozása a Resource Manager-sablonok használatával HDInsight](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>Előfeltételek
Ha egy meglévő HDInsight-fürtre kíván HDInsight-alkalmazásokat telepíteni, HDInsight-fürttel kell rendelkeznie. A fürt létrehozását lásd: [Fürtök létrehozása](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster). HDInsight-alkalmazásokat HDInsight-fürt létrehozása közben is telepíthet.

## <a name="install-applications-to-existing-clusters"></a>Alkalmazások telepítése a meglévő fürtökre
Az alábbi eljárásból megtudhatja, hogyan telepíthet HDInsight-alkalmazásokat egy meglévő HDInsight-fürtbe.

**Egy HDInsight-alkalmazás telepítése**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Lépjen a bal oldali menüből **minden szolgáltatás** > **Analytics** > **HDInsight-fürtök**.
3. Válasszon ki egy HDInsight-fürtöt a listából.  Ha még nincs ilyen fürtje, hozzon létre egyet most.  Lásd: [Fürtök létrehozása](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
4. Alatt a **beállítások** kategória, jelölje be **alkalmazások**. Láthatja, hogy a fő ablakban a telepített alkalmazások listáját. 
   
    ![HDInsight-alkalmazások menü a portálon](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. Válassza ki **+ Hozzáadás** a menüből. Láthatja, hogy az elérhető alkalmazások listáját.  Ha **+ Hozzáadás** szürkén jelenik meg, hogy a hiba azt jelenti, hogy a HDInsight-fürt jelen verziójához nem alkalmazások.
   
    ![HDInsight-alkalmazások, elérhető alkalmazások](./media/hdinsight-apps-install-applications/hdinsight-apps-list.png)
6. Válasszon egyet az elérhető alkalmazásokat, és kövesse az utasításokat követve fogadja el a jogi feltételeket.

A telepítési állapotát a portál értesítései (válassza a Harang ikon a portál tetején) látható. Az alkalmazás telepítése után az alkalmazás megjelenik a telepített alkalmazások listájában.

## <a name="install-applications-during-cluster-creation"></a>Alkalmazások telepítése fürtlétrehozás során
HDInsight-alkalmazásokat fürt létrehozása közben is telepíthet. A HDInsight-alkalmazásokat azt követően telepíti a rendszer, hogy a fürt létrejött, és futó állapotba került. Alkalmazások telepítése során a fürt létrehozása az Azure portal használatával, használhatja a **egyéni** helyett az alapértelmezett beállítás **Gyorslétrehozás** lehetőséget.

## <a name="list-installed-hdinsight-apps-and-properties"></a>Telepített HDInsight-alkalmazások és az alkalmazástulajdonságok listázása
A portál megjeleníti a fürthöz tartozó telepített HDInsight-alkalmazásokat, valamint az egyes telepített alkalmazásokhoz tartozó tulajdonságokat.

**HDInsight-alkalmazások listázása és az alkalmazástulajdonságok megjelenítése**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Lépjen a bal oldali menüből **minden szolgáltatás** > **Analytics** > **HDInsight-fürtök**.
3. Válasszon ki egy HDInsight-fürtöt a listából.
4. Alatt a **beállítások** kategória, jelölje be **alkalmazások**. Láthatja, hogy a fő ablakban a telepített alkalmazások listáját. 
   
    ![HDInsight-alkalmazások, telepített alkalmazások](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. Válassza ki a kívánt telepített alkalmazásra a tulajdonságok megjelenítéséhez. A tulajdonság tartalmazza:

    |Tulajdonság | Leírás |
    |---|---|
    |App neve |Alkalmazás neve. |
    |status |Az alkalmazás állapota. |
    |Weblap |A központilag telepített az élcsomóponthoz webalkalmazás URL-címe A hitelesítő adatok ugyanazok, mint amelyeket a fürthöz beállított HTTP-felhasználónál használt. |
    |SSH-végpont |Az SSH segítségével kapcsolódni az élcsomóponthoz. Az SSH hitelesítő adatai ugyanazok, mint amelyeket a fürthöz beállított SSH-felhasználónál használt. További információk: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md). |
    |Leírás | Az alkalmazás leírása. |

6. Egy alkalmazás törléséhez kattintson a jobb gombbal az alkalmazást, és kattintson **törlése** a helyi menüből.

## <a name="connect-to-the-edge-node"></a>Csatlakozás az élcsomóponthoz
Az élcsomóponthoz HTTP és SSH segítségével csatlakozhat. A végpont adatait megtalálja a [portálon](#list-installed-hdinsight-apps-and-properties). További információk: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

A HTTP végpont hitelesítő adatai ugyanazok, mint amelyeket a HDInsight-fürthöz beállított HTTP-felhasználónál használt; az SSH-végpont hitelesítő adatai ugyanazok, mint amelyeket a HDInsight-fürthöz beállított SSH-felhasználónál használt.

## <a name="troubleshoot"></a>Hibaelhárítás
Lásd: [A telepítési folyamat hibaelhárítása](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation).

## <a name="next-steps"></a>További lépések
* [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md): útmutató HDInsight közzé nem tett HDInsight-alkalmazás üzembe helyezése.
* [HDInsight-alkalmazások közzététele](hdinsight-apps-publish-applications.md): Ismerje meg, hogyan teheti közzé egyéni HDInsight-alkalmazásait akár az Azure Marketplace-en.
* [MSDN: Egy HDInsight-alkalmazás telepítése](https://msdn.microsoft.com/library/mt706515.aspx): Ismerje meg, hogyan adhat meg HDInsight-alkalmazások.
* [Customize Linux-based HDInsight clusters using Script Action](hdinsight-hadoop-customize-cluster-linux.md) (Linux-alapú HDInsight-fürtök testreszabása parancsfájlműveletek segítségével): megtudhatja, hogyan telepíthet további alkalmazásokat parancsfájlműveletek használatával.
* [Linux-alapú Apache Hadoop-fürtök létrehozása a Resource Manager-sablonok használatával HDInsight](hdinsight-hadoop-create-linux-clusters-arm-templates.md): ismerje meg, hogyan hívhat meg Resource Manager-sablonokat HDInsight-fürtök létrehozásához.
* [Use empty edge nodes in HDInsight](hdinsight-apps-use-edge-node.md) (Üres élcsomópontok használata a HDInsightban): a cikk bemutatja, hogyan lehet üres élcsomópontot használni egy HDInsight-fürt elérésére, HDInsight-alkalmazások tesztelésére és HDInsight-alkalmazások üzemeltetésére.

