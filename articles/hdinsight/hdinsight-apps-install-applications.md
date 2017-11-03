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
ms.date: 08/16/2017
ms.author: jgao
ms.openlocfilehash: 3b3ff8d33959978ddd648e59a6a301f00c247964
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="install-third-party-hadoop-applications-on-azure-hdinsight"></a>Külső gyártótól származó Hadoop-alkalmazások telepítése az Azure HDInsighton

Ebből a cikkből megtanulhatja a már közzétett külső Hadoop alkalmazás telepítése az Azure HDInsight. A saját alkalmazások telepítéséről az [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md) című cikk tartalmaz útmutatást.

A HDInsight-alkalmazások olyan alkalmazások, amelyeket a felhasználók egy Linux-alapú HDInsight-fürtre telepíthetnek. Ezek az alkalmazások lehetnek a Microsoft, független szoftvergyártók (ISV-k) vagy a felhasználók fejlesztései.  

Jelenleg négy közzétett alkalmazás érhető el:

* **DATAIKU DDS on HDInsight**: A Dataiku DSS (Data Science Studio) egy olyan szoftver, amely lehetővé teszi az adatokat professzionálisan célra használók (adatszakértők, üzleti elemzők, fejlesztők…) számára a nyers adatokat hatékony üzleti előrejelzésekké alakító rendkívül specifikus szolgáltatások prototípusként való használatát, létrehozását és központi telepítését.
* **Datameer**: a [Datameer](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft) interaktív módot kínál az elemzőknek a Big Data-eredmények felfedezésére, elemzésére és vizuális megjelenítésére. Vonjon be további adatforrásokat, fedezzen fel új kapcsolatokat, és kapjon még gyorsabban választ kérdéseire.
* A **Streamsets Data Collector for HDInsight** olyan, átfogó funkciókat kínáló integrált fejlesztőkörnyezetet (IDE) biztosít, amelyben bármely irányú, stream- és batch-adatokat összefűző feldolgozási folyamatok tervezése, tesztelése, üzembe helyezése és felügyelete lehetséges, továbbá különféle streamen belüli transzformációkat is tartalmaz, és mindehhez nincs szükség egyedi kód írására. 
* **A HDInsight CDAP cask** az első egyesített integrációs platformot kínál a big Data típusú adatok, alkalmazások és adatok tavakat üzemi 80 %-kal kivágása az állásidő. Ez az alkalmazás kizárólag a Standard HBase 3.4-fürtöket támogatja.
* **A HDInsight (béta) H2O mesterséges Eszközintelligencia** H2O készült vízjel támogatja a következő elosztott algoritmusok: GLM, natív Bayes, elosztott véletlenszerű erdő, átmenetes kiemelése gép, Neurális hálózatokat, mély tanulási, a K-közép, PEM, Alacsony Rank modellek, Anomáliadetektálás és Autoencoders általánosítva van.
* **Kyligence Analytics Platform** Kyligence Analytics Platform (KAP) Apache Kylin és Apache Hadoop kapcsolva, nagyvállalati használatra kész adatraktár; a lehetővé teszi az alárendelt második lekérdezés késés jelentős mértékű adatkészlethez, és leegyszerűsíti az adatelemzés üzleti felhasználók és az elemzők. 
* **SnapLogic Hadooplex** hdinsighton futó a SnapLogic Hadooplex lehetővé teszi az ügyfelek önkiszolgáló adatfeldolgozást és szinte bármilyen forrás előkészítése a Microsoft Azure cloud platform megadásával üzleti elemzéseket gyorsabb eléréséhez.
* **Spark-feladatkiszolgálót KNIME Spark végrehajtó** Spark-feladatkiszolgálót KNIME Spark végrehajtó és a KNIME Analytics Platform csatlakoztatásával a HDInsight-fürtök szolgál.

A cikkben szereplő utasítások az Azure Portalon alapulnak. Az Azure Resource Manager-sablont a portálról is exportálhatja, vagy beszerezheti a Resource Manager-sablon egy példányát a szállítóktól, majd az Azure PowerShell és az Azure parancssori felület segítségével üzembe helyezheti a sablont.  Lásd: [Create Linux-based Hadoop clusters in HDInsight using Resource Manager templates](hdinsight-hadoop-create-linux-clusters-arm-templates.md) (Linux-alapú Hadoop-fürtök létrehozása a HDInsightban Resource Manager-sablonok segítségével).

## <a name="prerequisites"></a>Előfeltételek
Ha egy meglévő HDInsight-fürtre kíván HDInsight-alkalmazásokat telepíteni, HDInsight-fürttel kell rendelkeznie. A fürt létrehozását lásd: [Fürtök létrehozása](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster). HDInsight-alkalmazásokat HDInsight-fürt létrehozása közben is telepíthet.

## <a name="install-applications-to-existing-clusters"></a>Alkalmazások telepítése a meglévő fürtökre
Az alábbi eljárásból megtudhatja, hogyan telepíthet HDInsight-alkalmazásokat egy meglévő HDInsight-fürtbe.

**HDInsight-alkalmazások telepítése**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali menüben kattintson a **HDInsight Clusters** (HDInsight-fürtök) elemre.  Ha nem jelenik meg, kattintson a **További szolgáltatások**, majd a **HDInsight-fürtök** elemre.
3. Kattintson a kívánt HDInsight-fürtre.  Ha még nincs ilyen fürtje, hozzon létre egyet most.  Lásd: [Fürtök létrehozása](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster).
4. Kattintson az **Applications** (Alkalmazások) elemre a **Configurations** (Konfigurációk) kategóriában. Láthatja, hogy a telepített alkalmazások listáját. Ha nem látja az Alkalmazások elemet, az azt jelenti, hogy a HDInsight-fürt jelen verziójához nem érhetők el alkalmazások.
   
    ![HDInsight-alkalmazások menü a portálon](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. Kattintson a panel menüjének **Hozzáadás** elemére. 
   
    ![HDInsight-alkalmazások, telepített alkalmazások](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps.png)
   
    Megjelenik a meglévő HDInsight-alkalmazások listája.
   
    ![HDInsight-alkalmazások, elérhető alkalmazások](./media/hdinsight-apps-install-applications/hdinsight-apps-list.png)
6. Kattintson a kívánt alkalmazásra, fogadja el a jogi feltételeket, majd kattintson a **Kiválasztás** gombra.

A portál értesítései között megtekintheti a telepítési állapotot (ehhez kattintson a portál felső részén található harang ikonra). Az alkalmazás telepítését követően az alkalmazás bekerül a Telepített alkalmazások panelre.

## <a name="install-applications-during-cluster-creation"></a>Alkalmazások telepítése fürtlétrehozás során
HDInsight-alkalmazásokat fürt létrehozása közben is telepíthet. A HDInsight-alkalmazásokat azt követően telepíti a rendszer, hogy a fürt létrejött, és futó állapotba került. Az alábbi eljárás leírja, hogyan telepíthet HDInsight-alkalmazásokat a fürt létrehozása közben.

**HDInsight-alkalmazások telepítése**

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) webhelyre.
2. Kattintson az **ÚJ** elemre, majd az **Adatok + analitika**, végül a **HDInsight** elemre.
3. A **Fürt neve** mezőben adja meg a fürt nevét, amelynek globálisan egyedinek kell lennie.
4. Kattintson a **előfizetés** jelölje be az Azure-előfizetés, amely a fürt.
5. Kattintson a **Fürt típusának kiválasztása** elemre, majd válasszon:
   
   * **Fürt típusa**: ha nem tudja, hogy melyik a megfelelő, válassza a **Hadoop** lehetőséget. Ez a legnépszerűbb fürttípus.
   * **Operációs rendszer**: válassza a **Linux** lehetőséget.
   * **Verzió**: ha nem tudja, hogy melyik a megfelelő, válassza az alapértelmezett verziót. További tájékoztatás a [HDInsight cluster versions](hdinsight-component-versioning.md) (A HDInsight-fürtök verziói) című cikkben olvasható.
   * **Cluster Tier** (Fürt szintje): az Azure HDInsight a big data felhőajánlatokat kétféle – Standard és Prémium – szinten biztosítja. További tájékoztatás a [Cluster tiers](hdinsight-hadoop-provision-linux-clusters.md#cluster-tiers) (A fürtök szintjei) című cikkben olvasható.
6. Kattintson az **Alkalmazások** lehetőségre, kattintson a közzétett alkalmazások egyikére, majd a **Kiválasztás** elemre.
7. Kattintson a **Hitelesítő adatok** elemre, majd adja meg a rendszergazda felhasználó jelszavát. Is meg kell adnia egy **SSH felhasználónév** és vagy egy **jelszó** vagy **nyilvános kulcs**, amelyek segítségével hitelesíti az SSH-felhasználó. A Microsoft a nyilvános kulcs használatát javasolja. A hitelesítő adatok konfigurációjának mentéséhez kattintson az oldal alján található **Kiválasztás** elemre.
8. Kattintson a **adatforrás**, válasszon egyet a meglévő tárfiókokat, vagy hozzon létre egy új tárfiókot az alapértelmezett tárfiókot, a fürt használandó.
9. Kattintson az **Erőforráscsoport** elemre, és válasszon egy meglévő erőforráscsoportot, vagy kattintson az **Új** elemre egy új erőforráscsoport létrehozásához.
10. Az **Új HDInsight-fürt** panelen ellenőrizze, hogy be van-e jelölve a **Kezdőlapra rögzít** jelölőnégyzet, majd kattintson a **Létrehozás** gombra. 

## <a name="list-installed-hdinsight-apps-and-properties"></a>Telepített HDInsight-alkalmazások és az alkalmazástulajdonságok listázása
A portál megjeleníti a fürthöz tartozó telepített HDInsight-alkalmazásokat, valamint az egyes telepített alkalmazásokhoz tartozó tulajdonságokat.

**HDInsight-alkalmazások listázása és az alkalmazástulajdonságok megjelenítése**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali menüben kattintson a **HDInsight Clusters** (HDInsight-fürtök) elemre.  Ha nem látja, kattintson a **Browse** (Tallózás), majd a **HDInsight Clusters** (HDInsight-fürtök) elemre.
3. Kattintson a kívánt HDInsight-fürtre.
4. A **Settings** (Beállítások) panelen kattintson az **Applications** (Alkalmazások) elemre a **General** (Általános) kategóriában. A Telepített alkalmazások panelen az összes telepített alkalmazás fel van sorolva. 
   
    ![HDInsight-alkalmazások, telepített alkalmazások](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. A tulajdonságok megjelenítéséhez kattintson a kívánt telepített alkalmazásra. A tulajdonság panelen a következők láthatók:
   
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

## <a name="next-steps"></a>Következő lépések
* [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md): telepítése egy közzé nem tett HDInsight-alkalmazást.
* [HDInsight-alkalmazások közzététele](hdinsight-apps-publish-applications.md): Megtudhatja, hogyan teheti közzé egyéni HDInsight-alkalmazásait az Azure Piactéren.
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: HDInsight-alkalmazás telepítése): Megtudhatja, hogyan adhat meg HDInsight-alkalmazásokat.
* [Customize Linux-based HDInsight clusters using Script Action](hdinsight-hadoop-customize-cluster-linux.md) (Linux-alapú HDInsight-fürtök testreszabása parancsfájlműveletek segítségével): megtudhatja, hogyan telepíthet további alkalmazásokat parancsfájlműveletek használatával.
* [Create Linux-based Hadoop clusters in HDInsight using Resource Manager templates](hdinsight-hadoop-create-linux-clusters-arm-templates.md) (Linux-alapú Hadoop-fürtök létrehozása a HDInsightban Resource Manager-sablonok segítségével): Megtudhatja, hogyan hívhat meg Resource Manager-sablonokat HDInsight-fürtök létrehozásához.
* [Use empty edge nodes in HDInsight](hdinsight-apps-use-edge-node.md) (Üres élcsomópontok használata a HDInsightban): a cikk bemutatja, hogyan lehet üres élcsomópontot használni egy HDInsight-fürt elérésére, HDInsight-alkalmazások tesztelésére és HDInsight-alkalmazások üzemeltetésére.

