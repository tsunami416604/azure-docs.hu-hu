---
title: "Kapcsolódás az Azure HDInsighthoz a Data Lake Tools for Visual Studio használatával | Microsoft Docs"
description: "A cikk azt ismerteti, hogyan telepíthetők és használhatók a Visual Studio Data Lake-eszközök Hadoop-fürtök csatlakoztatásához az Azure HDInsightban és Hive-lekérdezések futtatásához."
keywords: "hadoop-eszközök,hive-lekérdezés,visual studio,visual studio hadoop"
services: HDInsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: ce9c572a-1e98-46bf-9581-13a9767f1fa5
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/23/2017
ms.author: jgao
ms.openlocfilehash: f7eff70ece5e8dc7e0c5983871d40bd1f6eafff2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="connect-to-azure-hdinsight-and-run-hive-queries-using-data-lake-tools-for-visual-studio"></a>Kapcsolódás az Azure HDInsighthoz és Hive-lekérdezések futtatása a Data Lake Tools for Visual Studio használatával

Megtudhatja, hogyan használhatja a Visual Studio Data Lake-eszközöket Hadoop-fürtök csatlakoztatásához az [Azure HDInsightban](hdinsight-hadoop-introduction.md) és Hive-lekérdezések beküldéséhez. További információ a HDInsight használatáról: [A HDInsight bemutatása](hdinsight-hadoop-introduction.md) és [HDInsight – első lépések](hdinsight-hadoop-linux-tutorial-get-started.md). További információ a Storm-fürtökhöz való csatlakozásról: [C#-topológiák fejlesztése HDInsighton futó Apache Stormra a Visual Studióval](hdinsight-storm-develop-csharp-visual-studio-topology.md).

A Data Lake Tools for Visual Studio mind a Data Lake Analytics, mind a HDInsight eléréséhez használható.  A Data Lake-eszközökkel kapcsolatos információkért lásd: [Oktatóanyag: U-SQL-parancsfájlok fejlesztése Data Lake Tools for Visual Studio használatával](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

**Előfeltételek**

Ezen oktatóprogram elvégzéséhez és a Data Lake-eszközök Visual Studio programban történő használatához a következőkre van szüksége:

* Egy Azure HDInsight-fürt. Ennek létrehozását lásd [a Linux-alapú HDInsight használatának első lépéseivel](hdinsight-hadoop-linux-tutorial-get-started.md) foglalkozó témakörben.
* Egy munkaállomás a következő szoftverrel:
  
  * Windows 10, Windows 8.1, Windows 8 vagy Windows 7.
  * Visual Studio 2013/2015/2017.
    
    > [!NOTE]
    > Jelenleg a Data Lake Tools for Visual Studio csak angol nyelvű verzióban kapható.
    > 
    > 

## <a name="install-data-lake-tools-for-visual-studio"></a>A Data Lake Tools for Visual Studio telepítése

A Data Lake Tools alapértelmezés szerint a Visual Studio 2017-tel együtt települ. A régebbi verziókat a [Webplatform-telepítővel](https://www.microsoft.com/web/downloads/) telepítheti. Ki kell választania azt, amely megfelel a Visual Studio verziójának. Ha nincs telepítve a Visual Studio, a legújabb Visual Studio Community- és Azure SDK-eszközöket a [Webplatform-telepítővel](https://www.microsoft.com/web/downloads/) telepítheti:

![Data Lake Tools for Visual Studio Webplatform-telepítő.](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.wpi.png "Data Lake Tools for Visual Studio telepítése a Webplatform-telepítővel")

## <a name="connect-to-azure-subscriptions"></a>Csatlakozás Azure-előfizetésekhez
A Data Lake Tools for Visual Studio lehetővé teszi, hogy csatlakozzon a HDInsight-fürtökhöz, néhány alapvető felügyeleti műveletet végezzen, és Hive-lekérdezéseket futtasson.

> [!NOTE]
> Az általános Hadoop fürthöz végzett csatlakozásról információkért lásd: [Write and submit Hive queries using Visual Studio](http://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx) (Hive-lekérdezések írása és elküldése a Visual Studio eszközzel).
> 
> 

**Csatlakozás az Azure-előfizetéshez**

1. Nyissa meg a Visual Studiót.
2. A **View** (Nézet) menüben kattintson a **Server Explorer** elemre a Server Explorer ablak megnyitásához.
3. Bontsa ki az **Azure** elemet, majd bontsa ki a **HDInsight** elemet.
   
   > [!NOTE]
   > Figyelje meg, hogy a **HDInsight TaskList** (HDInsight feladatlista) ablaknak nyitva kell lennie. Ha nem látja, kattintson az **Other Windows** (Egyéb ablakok) elemre a **View** (Nézet) menüben, majd kattintson a **HDInsight TaskList** (HDInsight feladatlista) ablakra.  
   > 
   > 
4. Írja be az Azure-előfizetés hitelesítő adatait, majd kattintson a **Sign In** (Bejelentkezés) gombra. Erre csak akkor van szükség, ha még soha nem csatlakozott az Azure-előfizetéshez a Visual Studio eszközből ezen a munkaállomáson.
5. A Server Explorer eszközben meglévő HDInsight-fürtök listáját láthatja. Ha nincsenek fürtjei, létrehozhat egyet az Azure Portal, az Azure PowerShell vagy a HDInsight SDK használatával. További információ: [HDInsight-fürtök létrehozása](hdinsight-hadoop-provision-linux-clusters.md).
   
   ![Fürtök listája a Data Lake Tools for Visual Studio Kiszolgálókezelőjében](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.server.explorer.png "Data Lake Tools for Visual Studio Kiszolgálókezelő")
6. Bontson ki egy HDInsight-fürtöt. Látja a **Hive adatbázisokat**, egy alapértelmezett tárfiókot, a társított tárfiókokat és a **Hadoop szolgáltatásnaplót**. Ennél jobban is kibonthatja az elemeket.

Miután csatlakozott az Azure-előfizetéshez, a következőket teheti:

**Csatlakozás az Azure Portalhoz a Visual Studióból**

* A kiszolgálóböngészőből bontsa ki az **Azure** > **HDInsight** elemet, kattintson a jobb gombbal egy HDInsight-fürtre, majd kattintson a **Manage Cluster in Azure Portal** (Fürt kezelése az Azure Portalon) parancsra.

**Kérdések felvetése és visszajelzés a Visual Studióból**

* A **Tools** (Eszközök) menüben kattintson a **HDInsight** elemre, majd kattintson az **MSDN Forum** elemre kérdések felvetéséhez, vagy kattintson a **Give Feedback** (Visszajelzés küldése) parancsra.

## <a name="navigate-the-linked-resources"></a>Navigálás a kapcsolt erőforrásokban
A Server Explorer eszközből láthatja az alapértelmezett tárfiókot és az összes kapcsolt tárfiókot. Ha kibontja az alapértelmezett tárfiókot, láthatja a tárfiókon lévő tárolókat. Az alapértelmezett tárfiók és az alapértelmezett tároló meg van jelölve. Bármely tárolóra a jobb gombbal kattintva azok tartalmát is megtekintheti.

![Kapcsolt erőforrások listázása a Data Lake Tools for Visual Studio kiszolgálókezelőjével](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.linked.resources.png "kapcsolt erőforrások listázása")

Egy tároló megnyitása után a következő gombokkal tölthet fel, törölhet vagy tölthet le blobokat:

![Blobműveletek a Data Lake Tools for Visual Studio kiszolgálókezelőjével](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.blob.operations.png "blobok feltöltése, törlése és letöltése")

## <a name="run-a-hive-query"></a>Hive-lekérdezések futtatása
Az [Apache Hive](http://hive.apache.org) egy Hadoop-alapú adattárház-infrastruktúra, amely adatösszegzéseket, lekérdezéseket és elemzéseket biztosít. A Data Lake Tools for Visual Studio támogatja a Hive-lekérdezések Visual Studióból végzett futtatását. További információ a Hive-ról: [A Hive használata a HDInsightban](hdinsight-use-hive.md).

Időigényes a Hive parancsfájlok tesztelése a HDInsight fürtökhöz képest időigényes. Percekbe vagy akár több időbe is telhet. A Data Lake Tools for Visual Studio képes helyben érvényesíteni a Hive-parancsfájlokat anélkül, hogy egy élő fürthöz csatlakozna.

A Data Lake Tools for Visual Studio azt is lehetővé teszi, hogy a felhasználók lássák, mi található a Hive-feladatokban. Ehhez begyűjthetik és a felszínre hozhatják bizonyos Hive-feladatok YARN-naplóit.

### <a name="view-the-hivesampletable"></a>A **hivesampletable** megtekintése
Az összes HDInsight-fürt a *hivesampletable* nevű minta Hive táblát tartalmazza. Ezzel a táblával mutatjuk be, hogyan listázhatja a Hive táblákat, hogyan tekintheti meg a táblasémákat és hogyan listázhatja a Hive táblában lévő sorokat.

**A Hive-táblák listázása és a Hive-táblaséma megtekintése**

1. A **Server Explorer** eszközből bontsa ki az **Azure** > **HDInsight** > a választott fürt > **Hive Databases** >  (Hive adatbázisok) **Deafult** > **hivesampletable** (Alapértelmezett) elemet a táblaséma megtekintéséhez.
2. Kattintson a jobb gombbal a **hivesampletable** elemre, majd kattintson a **View Top 100 Rows** (Első 100 sor megtekintése) parancsra a sorok listázásához. Ez a következő Hive-lekérdezés Hive ODBC-illesztővel végzett futtatásával egyenlő:
   
     SELECT * FROM hivesampletable LIMIT 100
   
   Testreszabhatja a sorok számát.
   
   ![Data Lake Tools: HDInsight Hive Visual Studio sémalekérdezés](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.hive.schema.png "Hive-lekérdezés eredményei")

### <a name="create-hive-tables"></a>Hive táblák létrehozása
A grafikus felhasználói felülettel Hive táblákat hozhat létre, vagy Hive-lekérdezéseket használhat. A Hive-lekérdezésekkel kapcsolatban információért lásd: [Run Hive queries](#run.queries) (Hive-lekérdezések futtatása).

**Hive-tábla létrehozása**

1. A **Server Explorer** eszközből bontsa ki az **Azure** > **HDInsight-fürtök** egy HDInsight-fürt > **Hive Databases** (Hive-adatbázisok) elemet, majd kattintson a jobb gombbal az **alapértelmezett** elemre, és kattintson a **Create Table** (Tábla létrehozása) parancsra.
2. Konfigurálja a táblát.
3. Kattintson a **Create Table** (Tábla létrehozása) parancsra, hogy elküldje az új Hive tábla létrehozására szolgáló feladatot.
   
    ![Data Lake Tools: HDInsight Visual Studio-eszközök Hive-tábla létrehozásához](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.create.hive.table.png "Hive-tábla létrehozása")

### <a name="run.queries"></a>Hive-lekérdezések érvényesítése és futtatása
Kétféleképpen hozhat létre és futtathat Hive-lekérdezéseket:

* Alkalmi lekérdezések létrehozása
* Hive alkalmazás létrehozása

**Alkalmi lekérdezések létrehozása, érvényesítése és futtatása**

1. A **Server Explorer** eszközből bontsa ki az **Azure** elemet, majd bontsa ki a **HDInsight Clusters** (HDInsight-fürtök) elemet.
2. Kattintson a jobb gombbal a fürtre, ahol futtatni szeretné a lekérdezést, majd kattintson a **Write a Hive Query** (Hive-lekérdezés írása) parancsra.
3. Adja meg a Hive-lekérdezéseket. Figyelje meg, hogy a Hive szerkesztő támogatja az IntelliSense eszközt. A Data Lake Tools for Visual Studio támogatja a távoli metaadatok betöltését a Hive-parancsfájl szerkesztésekor. Amikor például a "SELECT * FROM" parancsot írja be, az IntelliSense listázza az összes javasolt táblanevet. Amikor meg van adva egy táblanév, az oszlopnevek az IntelliSense alapján vannak listázva. Az eszköz szinte az összes Hive DML-utasítást, -segédlekérdezést és beépített UDF-et támogatja.
   
    ![Data Lake Tools: HDInsight Visual Studio Tools IntelliSense](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.table.names.png "U-SQL IntelliSense")
   
    ![Data Lake Tools: HDInsight Visual Studio Tools IntelliSense](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.column.names.png "U-SQL IntelliSense")
   
   > [!NOTE]
   > A rendszer csak a HDInsight eszköztáron kijelölt fürtök metaadatait javasolja.
   > 
   > 
4. (Elhagyható): Kattintson a **Parancsfájl érvényesítése** gombra a parancsfájl szintaxishibáinak ellenőrzéséhez.
   
    ![Data Lake Tools: Data Lake Tools for Visual Studio helyi érvényesítés](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.validate.hive.script.png "parancsfájl érvényesítése")
5. Kattintson a **Submit** (Küldés) vagy a **Submit (Advanced)** (Küldés (Speciális)) elemre. A speciális küldés lehetőséggel konfigurálja a parancsfájl **Job Name** (Feladat neve), **Arguments** (Argumentumok), **Additional Configurations** (További konfigurációk) és **Status Directory** (Állapot könyvtár) elemeit:
   
    ![HDInsight Hadoop Hive-lekérdezés](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.submit.jobs.advanced.png "Lekérdezések beküldése")
   
    A feladat elküldése után egy **Hive Job Summary** (Hive-feladat összegzése) ablakot lát.
   
    ![HDInsight Hadoop Hive-lekérdezés összegzése](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.run.hive.job.summary.png "Hive-feladat összegzése")
6. A **Refresh** (Frissítés) gombbal frissítheti az állapotot, amíg a feladat állapota **Completed** (Befejezve) állapotúvá nem válik.
7. Kattintson az alul lévő hivatkozásokra a következők megtekintéséhez: **Job Query** (Feladat lekérdezése), **Job Output** (Feladatkimenet), **Job log** (Feladatnapló) vagy **Yarn log** (Yarn napló).

**Hive-megoldás létrehozása és futtatása**

1. Kattintson a **File** (Fájl) menüben a **New** (Új), majd a **Project** (Projekt) elemre.
2. Válassza ki a bal oldali panelen a **HDInsight** elemet, válassza a középső panelen a **Hive Application** (Hive alkalmazás) elemet, írja be a tulajdonságokat, majd kattintson az **OK** gombra.
   
    ![A Data Lake Tools: HDInsight Visual Studio-eszközök Új Hive-projekt](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.new.hive.project.png "Hive-alkalmazások létrehozása a Visual Studióból")
3. A **Solution Explorer** (Megoldáskezelő) ablakban kattintson duplán a **Script.hql** fájlra a megnyitásához.
4. A Hive-parancsfájl érvényesítéséhez a **Validate Script** (Parancsfájl érvényesítése) gombra kattinthat, vagy kattintson a jobb gombbal a parancsfájlra a Hive szerkesztőben, majd kattintson a **Validate Script** (Parancsfájl érvényesítése) parancsra a helyi menüben.

### <a name="view-hive-jobs"></a>Hive-feladatok megtekintése
Megtekintheti a Hive-feladatok feladatlekérdezéseit, feladatkiemenetét, feladatnaplóit és Yarn naplóit. További információért tekintse meg az előző képernyőképet.

Az eszköz legújabb kiadásával láthatja, mi van a Hive-feladatokban, ha begyűjti és felszínre hozza a YARN-naplókat. A YARN naplók segíthetnek a teljesítménnyel kapcsolatos problémák vizsgálatában. További információ arról, hogy a HDInsight hogyan gyűjti be a YARN-naplókat: [HDInsight alkalmazásnaplók szoftveres elérése](hdinsight-hadoop-access-yarn-app-logs.md).

**Hive-feladatok megtekintése**

1. A **Server Explorer** eszközből bontsa ki az **Azure** elemet, majd bontsa ki a **HDInsight** elemet.
2. Kattintson a jobb gombbal egyyyy HDInsight-fürtre, majd kattintson a **View Jobs** (Feladatok megyytekintése) parancsra. Láthatja azon Hive-feladatok listáját, amelyek a fürtön futottak.
3. Kattintson egy feladatra a feladatlistában a kiválasztásához, majd a **Hive Job Summary** (Hive feladat összegzése) ablakban nyissa meg a **Job Query** (Feladat lekérdezése), **Job Output** (Feladatkimenet), **Job Log** (Feladatnapló) vagy **Yarn log** (Yarn napló) elemet.
   
    ![Data Lake Tools: HDInsight Visual Studio-eszközök – Hive-feladatok megtekintése](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.view.hive.jobs.png "Hive-feladatok megtekintése")

### <a name="faster-path-hive-execution-via-hiveserver2"></a>Gyorsabb elérési úttal rendelkező Hive végrehajtás HiveServer2-n keresztül
> [!NOTE]
> Ez a szolgáltatás csak a HDInsight fürt 3.2-es és újabb verzióin működik.
> 
> 

A Hive-feladatok [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) ( más néven Templeton) -eszközön keresztül végzett küldéséhez használt Data Lake-eszközök. Hosszú időbe telt a feladatrészletek és a hibainformációk visszaadása.
Ezen teljesítményprobléma megoldása érdekében a Data Lake-eszközök közvetlenül a fürtön futtatják a Hive-feladatokat a HiveServer2-n keresztül, kihagyva az RDP/SSH-védelmet.
A jobb teljesítmény mellett a felhasználók Hive on Tez ábrákat és a feladat részleteit is megtekinthetik.

A HDInsight-fürt 3.2-es vagy újabb verziói esetén egy **Execute via HiveServer2** (Végrehajtás HiveServer2-n keresztül) gomb látható:

![Data Lake Visual Studio Tools: Végrehajtás HiveServer2-n keresztül](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.execute.via.hiveserver2.png "Hive-lekérdezések futtatása a HiveServer2-n keresztül")

A továbbított naplók valós időben láthatók, és megtekintheti a feladatábrákat is, ha a Hive-lekérdezést a Tezben futtatja.

![Data Lake Visual Studio Tools: Hive-végrehajtás gyors elérési úton](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.fast.path.hive.execution.png "Feladatgrafikonok megjelenítése")

**A lekérdezések HiveServer2-n végzett végrehajtása és a lekérdezések WebHCaten keresztüli elküldése közötti különbségek**

Bár a lekérdezések HiveServer2-n keresztül végzett végrehajtásának számos előnnyel rendelkezik a teljesítmény szempontjából, vannak korlátozásai is. A korlátozások némelyike nem megfelelő az éles használathoz. Az alábbi táblázat a különbségeket mutatja:

|  | Végrehajtás HiveServer2-n keresztül | Elküldés WebHCaten keresztül |
| --- | --- | --- |
| Lekérdezések végrehajtása |Kiküszöböli a WebHCat többletterhelését (amely elindítja a „TempletonControllerJob” nevű MapReduce feladatot). |Amikor a WebHCaten keresztül hajtja végre a lekérdezést, a WebHCat elindít MapReduce feladatot, amely további késleltetést okoz. |
| Stream naplók vissza |Majdnem valós időben. |A feladatvégrehajtási naplók csak a feladat elvégzése után érhetők el. |
| Feladatelőzmények megtekintése |Ha egy lekérdezést a HiveServer2-n keresztül futtat, a feladatelőzményei (feladatnapló, feladatkimenet) nem maradnak meg. Az alkalmazás a YARN felhasználói felületen tekinthető meg korlátozott információkkal. |Ha egy lekérdezést a WebHCaten keresztül futtat, a feladatelőzményei (feladatnapló, feladatkimenet) megmaradnak és megtekinthetők a Visual Studio/HDInsight SDK/PowerShell használatával. |
| Ablak bezárása |A HiveServer2-n keresztüli végrehajtás „szinkronos” módot jelent, így nyitva kell hagynia az ablakokat; ha bezárja az ablakokat, akkor megszakítja a lekérdezés végrehajtását. |A WebHCaten keresztül végzett küldés „aszinkronos” mód, így elküldheti a lekérdezést a WebHCaten keresztül, és bezárhatja a Visual Studio eszközt. Bármikor visszatérhet és megtekintheti az eredményeket. |

### <a name="tez-hive-job-performance-graph"></a>Tez Hive feladat teljesítménye ábra
A Data Lake-eszközök támogatják a Tez végrehajtómotor által elvégzett Hive-feladatok teljesítményi ábráinak megjelenítését. További információ a Tez engedélyezéséről: [A Hive használata a HDInsightban](hdinsight-use-hive.md). Miután elküldött egy Hive-feladatot a Visual Studióban, a Visual Studio megjeleníti az ábrát a feladat elvégzésekor.  Elképzelhető, hogy a **Refresh** (Frissítés) gombra kell kattintania a legfrissebb feladatállapot lekérdezéséhez.

> [!NOTE]
> Ez a szolgáltatás csak a HDInsight-fürt 3.2.4.593-as vagy újabb verzióihoz érhető el, és csak a befejezett feladatokhoz működik (ha a WebHCaten keresztül küldte el a feladatot; ez az ábra akkor jelenik meg, ha a lekérdezést a HiveServer2-n keresztül futtatja). Ez Windows- és Linux-alapú fürtök esetén is működik.
> 
> 

![hadoop hive tez teljesítmény ábra](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.hive.tez.performance.graph.png "Feladat állapota")

Hogy jobban megértse a Hive-lekérdezést, ez a kiadás a Hive-operátor hozzáadása nézettel bővült. Csak duplán kell kattintania a feladatábra csúcsaira, és láthatja a csúcsponton belüli lévő összes operátort. Egy adott operátor fölé helyezve a mutatót további részleteket is megtudhat az operátorról.

### <a name="task-execution-view-for-hive-on-tez-jobs"></a>Feladatvégrehajtási nézet a Hive on Tez feladatokhoz
A feladatvégrehajtási nézet a Hive on Tez feladatokhoz eszközzel strukturált és ábrázolt információkat szerezhet be a Hive-feladatokhoz, és további részleteket a feladatokról. Ahol probléma van a teljesítménnyel, a nézettel szerezhet be további részleteket. Például arról, hogyan működnek az egyes feladatok, és részletes információt kaphat mindegyik feladatról (adatírás/-olvasás, ütemezés/kezdő/záró idő stb.), hogy a megjelenített információk alapján hangolhassa a feladatkonfigurációkat vagy a rendszerarchitektúrát.

![Data Lake Visual Studio Tools: feladat-végrehajtási nézet](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.task.execution.view.png "feladat-végrehajtási nézet")

## <a name="run-pig-scripts"></a>Pig-parancsfájlok futtatása
A Data Lake Tools for Visual Studio támogatja a Pig-parancsfájlok létrehozását és HDInsight-fürtökbe való elküldését. A felhasználók Pig-projektet hozhatnak létre sablonból, majd elküldhetik a parancsfájlt a HDInsight-fürtökhöz.

## <a name="feedbacks--known-issues"></a>Visszajelzések és ismert problémák
* Jelenleg a HiveServer2 eredmények tiszta szöveggel jelennek meg, ami nem ideális. Dolgozunk ennek megoldásán.
* Ha az eredmények NULL értékekkel kezdődnek, az eredmények jelenleg nem jelennek meg. Megoldottuk ezt a problémát, és ha elakad ennél a hibánál, nyugodtan küldjön egy e-mailt, vagy lépjen kapcsolatba a Terméktámogatással.
* A Visual Studio által létrehozott HQL-parancsfájl a felhasználó helyi régióbeállításaitól függően kódolt. Lehet, hogy nem fut megfelelően, ha a felhasználó binárisként tölti fel a parancsfájlt a fürtbe.

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben megtanulta, hogyan csatlakozhat a HDInsight-fürtökhöz a Visual Studio-eszközből a Data Lake (HDInsight) -eszközök csomaggal, és hogyan futtathat Hive-lekérdezéseket. További információkért lásd:

* [A Hadoop Hive használata a HDInsightban](hdinsight-use-hive.md)
* [A Hadoop első lépései a HDInsightban](hdinsight-hadoop-linux-tutorial-get-started.md)
* [Hadoop-feladatok elküldése a HDInsightban](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Twitter-adatok elemzése a Hadooppal a HDInsightban](hdinsight-analyze-twitter-data.md)

