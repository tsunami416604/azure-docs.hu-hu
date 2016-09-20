<properties
    pageTitle="A Visual Studio Hadoop-eszközök használatának megismerése a HDInsight eszközhöz | Microsoft Azure"
    description="Megtudhatja, hogyan telepítheti és használhatja a Visual Studio Hadoop-eszközöket a HDInsight alkalmazáshoz a Hadoop fürtök csatlakoztatásához és a Hive-lekérdezések futtatásához."
    keywords="hadoop-eszközök,hive-lekérdezés,visual studio"
    services="HDInsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="08/10/2016"
    ms.author="jgao"/>

# Bevezetés a Visual Studio Hadoop-eszközök használatába a HDInsight alkalmazáshoz és Hive-lekérdezések futtatásához

Megtudhatja, hogyan használhat HDInsight eszközöket a Visual Studio alkalmazáshoz, hogy HDInsight fürtökhöz csatlakozzon és Hive-lekérdezéseket adjon le. A HDInsight használatáról további információért lásd: [Introduction to HDInsight][hdinsight.introduction] (A HDInsight bemutatása) és [Get Started with HDInsight][hdinsight.get.started] (HDInsight – első lépések). További információ a Storm fürthöz végzett csatlakozásról: [Develop C# topologies for Apache Storm on HDInsight using Visual Studio][hdinsight.storm.visual.studio.tools] (C#-topológiák fejlesztése HDInsighton futó Apache Stormra a Visual Studio használatával).

**Előfeltételek**

Ezen oktatóprogram elvégzéséhez és a Hadoop-eszközök Visual Studio programban végzett használatához a következőkre van szüksége:

- Egy Azure HDInsight fürt: A Linux-alapú vagy a Windows-alapú fürtök is működnek ezen dokumentum lépéseivel. A következők egyikében talál információt a fürtök létrehozásáról:

    - [Ismerkedés a Linux alapú HDInsight eszközzel](hdinsight-hadoop-linux-tutorial-get-started.md)
    - [Ismerkedés a Windows alapú HDInsight eszközzel](hdinsight-hadoop-tutorial-get-started-windows.md)

- Egy munkaállomás a következő szoftverrel:

    - Windows 8.1, Windows 8 vagy Windows 7
    - Visual Studio (a következő verziók egyike):
        - Visual Studio 2013 Community/Professional/Premium/Ultimate [Update 4](https://www.microsoft.com/download/details.aspx?id=44921) frissítéssel
        - Visual Studio 2015 (Community/Enterprise)

    >[AZURE.NOTE] Jelenleg a HDInsight Tools for Visual Studio csak angol nyelvű verzióban kapható.


## A HDInsight Tools for Visual Studio telepítése

A HDInsight Tools for Visual Studio és a Microsoft Hive ODBC-illesztő a Microsoft Azure SDK for .NET 2.5.1-es vagy későbbi verzióval van egy csomagban. A [Webplatform-telepítővel](http://go.microsoft.com/fwlink/?LinkId=255386) telepítheti ezt. Ki kell választania azt, amely megfelel a Visual Studio verziójának. Ha nincs telepítve a Visual Studio, telepítheti a legújabb Visual Studio Community és Azure SDK eszközöket a [Webplatform-telepítővel](http://go.microsoft.com/fwlink/?LinkId=255386) vagy a következő hivatkozásokkal:

- [Visual Studio Community 2015 Microsoft Azure SDK-val](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/VS2015CommunityAzurePack.appids)
- [Visual Studio Community 2013 Microsoft Azure SDK-val](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/VS2013CommunityAzurePack.appids)
- [Microsoft Azure SDK for .NET (VS 2015)](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/VWDOrVs2015AzurePack.appids)
- [Microsoft Azure SDK for .NET (VS 2013)](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/VWDOrVs2013AzurePack.appids)

![Hadoop-eszközök: HDinsight Tools for Visual Studio Webplatform-telepítő.][1]

## Csatlakozás Azure-előfizetésekhez
A HDInsight Tools for Visual Studio lehetővé teszi, hogy csatlakozzon a HDInsight fürtökhöz, néhány alapvető felügyeleti műveletet végezzen, és Hive-lekérdezéseket futtasson.

>[AZURE.NOTE] Az általános Hadoop fürthöz végzett csatlakozásról információkért lásd: [Write and submit Hive queries using Visual Studio](http://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx) (Hive-lekérdezések írása és elküldése a Visual Studio eszközzel).


**Csatlakozás az Azure-előfizetéshez**

1.  Nyissa meg a Visual Studiót.
2.  A **View** (Nézet) menüben kattintson a **Server Explorer** elemre a Server Explorer ablak megnyitásához.
3.  Bontsa ki az **Azure** elemet, majd bontsa ki a **HDInsight** elemet.

    >[AZURE.NOTE]Figyelje meg, hogy a **HDInsight TaskList** (HDInsight feladatlista) ablaknak nyitva kell lennie. Ha nem látja, kattintson az **Other Windows** (Egyéb ablakok) elemre a **View** (Nézet) menüben, majd kattintson a **HDInsight TaskList** (HDInsight feladatlista) ablakra.  
4.  Írja be az Azure-előfizetés hitelesítő adatait, majd kattintson a **Sign In** (Bejelentkezés) gombra. Erre csak akkor van szükség, ha még soha nem csatlakozott az Azure-előfizetéshez a Visual Studio eszközből ezen a munkaállomáson.
5.  A Server Explorer eszközben meglévő HDInsight-fürtök listáját láthatja. Ha nincsenek fürtjei, megadhat egyet az Azure portál, az Azure PowerShell vagy a HDInsight SDK használatával. További információkért lásd: [Provision HDInsight clusters][hdinsight-provision] HDInsight-fürtök üzembe helyezése.

    ![Hadoop-eszközök: HDInsight Tools for Visual Studio Server Explorer fürtlista][5]
6.  Bontson ki egy HDInsight-fürtöt. Látja a **Hive adatbázisokat**, egy alapértelmezett tárfiókot, a társított tárfiókokat és a **Hadoop szolgáltatásnaplót**. Ennél jobban is kibonthatja az elemeket.

Miután csatlakozott az Azure-előfizetéshez, a következőket teheti:

**Csatlakozás az Azure portálhoz a Visual Studio eszközből**

- A Server Explorer eszközből bontsa ki az **Azure** > **HDInsight** elemet, kattintson a jobb gombbal egy HDInsight-fürtre, majd kattintson a **Manage Cluster in Azure Portal** (Fürt kezelése az Azure Portalon) parancsra.

**Kérdések felvetése és visszajelzés nyújtása a Visual Studióból**

- A **Tools** (Eszközök) menüben kattintson a **HDInsight** elemre, majd kattintson az **MSDN Forum** elemre kérdések felvetéséhez, vagy kattintson a **Give Feedback** (Visszajelzés küldése) parancsra.

## Navigálás a kapcsolt erőforrásokban

A Server Explorer eszközből láthatja az alapértelmezett tárfiókot és az összes kapcsolt tárfiókot. Ha kibontja az alapértelmezett tárfiókot, láthatja a tárfiókon lévő tárolókat. Az alapértelmezett tárfiók és az alapértelmezett tároló meg van jelölve. Bármely tárolóra a jobb gombbal kattintva azok tartalmát is megtekintheti.

![HDInsight Tools for Visual Studio Server Explorer fürtlista][2]

Egy tároló megnyitása után a következő gombokkal tölthet fel, törölhet vagy tölthet le blobokat:

![A HDInsight Tools for Visual Studio Server Explorer blobműveletei](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.blob.operations.png)


## Hive-lekérdezések futtatása
Az [Apache Hive][apache.hive] egy, a Hadoop technológiára épülő adatraktár infrastruktúra az adatösszegzések, lekérdezések és elemzések nyújtásához. A HDInsight Tools for Visual Studio támogatja a Hive-lekérdezések Visual Studio eszközből végzett futtatását. A Hive rendszerrel kapcsolatban további információért lásd: [Use Hive with HDInsight][hdinsight.hive] (A Hive használata a HDInsightban).

Időigényes a Hive parancsfájlok tesztelése a HDInsight fürtökhöz képest időigényes. Percekbe vagy akár több időbe is telhet. A HDInsight Tools for Visual Studio képes helyben érvényesíteni a Hive parancsfájlokat anélkül, hogy egy élő fürthöz csatlakozna.

A HDInsight Tools for Visual Studio azt is lehetővé teszi, hogy a felhasználók lássák, mi található a Hive-feladatokban. Ehhez begyűjthetik és a felszínre hozhatják bizonyos Hive-feladatok YARN naplóit.

### A **hivesampletable** megtekintése
Az összes HDInsight-fürt a *hivesampletable* nevű minta Hive táblát tartalmazza. Ezzel a táblával mutatjuk be, hogyan listázhatja a Hive táblákat, hogyan tekintheti meg a táblasémákat és hogyan listázhatja a Hive táblában lévő sorokat.



**A Hive táblák listázása és a Hive táblaséma megtekintése**

1.  A **Server Explorer** eszközből bontsa ki az **Azure** > **HDInsight** > a választott fürt > **Hive Databases** >  (Hive adatbázisok) **Deafult** > **hivesampletable** (Alapértelmezett) elemet a táblaséma megtekintéséhez.
4.  Kattintson a jobb gombbal a **hivesampletable** elemre, majd kattintson a **View Top 100 Rows** (Első 100 sor megtekintése) parancsra a sorok listázásához. Ez a következő Hive-lekérdezés Hive ODBC-illesztővel végzett futtatásával egyenlő:

        SELECT * FROM hivesampletable LIMIT 100

    Testreszabhatja a sorok számát.

    ![Hadoop-eszközök: HDinsight Hive Visual Studio sémalekérdezés][6]

### Hive táblák létrehozása

A grafikus felhasználói felülettel Hive táblákat hozhat létre, vagy Hive-lekérdezéseket használhat. A Hive-lekérdezésekkel kapcsolatban információért lásd: [Run Hive queries](#run.queries) (Hive-lekérdezések futtatása).

**Hive tábla létrehozása**

1. A **Server Explorer** eszközből bontsa ki az **Azure** > **HDInsight-fürtök** egy HDInsight-fürt > **Hive Databases** (Hive-adatbázisok) elemet, majd kattintson a jobb gombbal az **alapértelmezett** elemre, és kattintson a **Create Table** (Tábla létrehozása) parancsra.
2. Konfigurálja a táblát.
3. Kattintson a **Create Table** (Tábla létrehozása) parancsra, hogy elküldje az új Hive tábla létrehozására szolgáló feladatot.

    ![Hadoop-eszközök: hdinsight visual studio eszközök hive tábla létrehozásához][7]

### <a name="run.queries"></a>Hive-lekérdezések érvényesítése és futtatása
Kétféleképpen hozhat létre és futtathat Hive-lekérdezéseket:

- Alkalmi lekérdezések létrehozása
- Hive alkalmazás létrehozása

**Alkalmi lekérdezések létrehozása, érvényesítése és futtatása**

1. A **Server Explorer** eszközből bontsa ki az **Azure** elemet, majd bontsa ki a **HDInsight Clusters** (HDInsight-fürtök) elemet.
2. Kattintson a jobb gombbal a fürtre, ahol futtatni szeretné a lekérdezést, majd kattintson a **Write a Hive Query** (Hive-lekérdezés írása) parancsra.
3. Adja meg a Hive-lekérdezéseket. Figyelje meg, hogy a Hive szerkesztő támogatja az IntelliSense eszközt. A HDInsight Tools for Visual Studio támogatja a távoli metaadatok betöltését a Hive parancsfájl szerkesztésekor. Amikor például a "SELECT * FROM" parancsot írja be, az IntelliSense listázza az összes javasolt táblanevet. Amikor meg van adva egy táblanév, az oszlopnevek az IntelliSense alapján vannak listázva. Az eszköz szinte az összes Hive DML utasítást, segédlekérdezést és beépített UDF-et támogatja.

    ![Hadoop-eszközök: HDInsight Visual Studio eszközök IntelliSense][13]

    ![Hadoop-eszközök: HDInsight Visual Studio eszközök IntelliSense][14]

    > [AZURE.NOTE] A rendszer csak a HDInsight eszköztáron kijelölt fürtök metaadatait javasolja.
4. (Elhagyható): Kattintson a **Parancsfájl érvényesítése** gombra a parancsfájl szintaxishibáinak ellenőrzéséhez.

    ![Hadoop-eszközök: hdinsight tools for Visual Studio helyi érvényesítés][10]

4. Kattintson a **Submit** (Küldés) vagy a **Submit (Advanced)** (Küldés (Speciális)) elemre. A speciális küldés lehetőséggel konfigurálja a parancsfájl **Job Name** (Feladat neve), **Arguments** (Argumentumok), **Additional Configurations** (További konfigurációk) és **Status Directory** (Állapot könyvtár) elemeit:

    ![hdinsight hadoop hive lekérdezés][9]

    A feladat elküldése után egy **Hive Job Summary** (Hive-feladat összegzése) ablakot lát.

    ![HDInsight Hadoop Hive-lekérdezések összegzése][8]
5. A **Refresh** (Frissítés) gombbal frissítheti az állapotot, amíg a feladat állapota **Completed** (Befejezve) állapotúvá nem válik.
6. Kattintson az alul lévő hivatkozásokra a következők megtekintéséhez: **Job Query** (Feladat lekérdezése), **Job Output** (Feladatkimenet), **Job log** (Feladatnapló) vagy **Yarn log** (Yarn napló).



**Hive megoldás létrehozása és futtatása**

1. Kattintson a **File** (Fájl) menüben a **New** (Új), majd a **Project** (Projekt) elemre.
2. Válassza ki a bal oldali panelen a **HDInsight** elemet, válassza a középső panelen a **Hive Application** (Hive alkalmazás) elemet, írja be a tulajdonságokat, majd kattintson az **OK** gombra.

    ![Hadoop-eszközök: hdinsight visual studio eszközök új hive projekt][11]
3. A **Solution Explorer** (Megoldáskezelő) ablakban kattintson duplán a **Script.hql** fájlra a megnyitásához.
4. A Hive-parancsfájl érvényesítéséhez a **Validate Script** (Parancsfájl érvényesítése) gombra kattinthat, vagy kattintson a jobb gombbal a parancsfájlra a Hive szerkesztőben, majd kattintson a **Validate Script** (Parancsfájl érvényesítése) parancsra a helyi menüben.


### Hive-feladatok megtekintése
Megtekintheti a Hive-feladatok feladatlekérdezéseit, feladatkiemenetét, feladatnaplóit és Yarn naplóit. További információért tekintse meg az előző képernyőképet.

Az eszköz legújabb kiadásával láthatja, hogy mi van a Hive-feladatokban, ha begyűjti és felszínre hozza a YARN naplókat. A YARN naplók segíthetnek a teljesítménnyel kapcsolatos problémák vizsgálatában. További információ arról, hogy a HDInsight hogyan gyűjti be a YARN naplókat: [Access HDInsight Application Logs Programmatically][hdinsight.access.application.logs] (HDInsight alkalmazásnaplók szoftveres elérése).

**Hive-feladatok megtekintése**

1. A **Server Explorer** eszközből bontsa ki az **Azure** elemet, majd bontsa ki a **HDInsight** elemet.
2. Kattintson a jobb gombbal egyyyy HDInsight-fürtre, majd kattintson a **View Jobs** (Feladatok megyytekintése) parancsra. Láthatja azon Hive-feladatok listáját, amelyek a fürtön futottak.
3. Kattintson egy feladatra a feladatlistában a kiválasztásához, majd a **Hive Job Summary** (Hive feladat összegzése) ablakban nyissa meg a **Job Query** (Feladat lekérdezése), **Job Output** (Feladatkimenet), **Job Log** (Feladatnapló) vagy **Yarn log** (Yarn napló) elemet.

    ![Hadoop-eszközök: HDInsight Visual Studio eszközök Hive-feladatok megtekintése][12]

### Gyorsabb elérési úttal rendelkező Hive végrehajtás HiveServer2-n keresztül

>[AZURE.NOTE] Ez a szolgáltatás csak a HDInsight fürt 3.2-es és újabb verzióin működik.

A Hive-feladatok [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) ( más néven Templeton) eszközön keresztül végzett küldéséhez használt HDInsight eszközök. Hosszú időbe telt a feladatrészletek és a hibainformációk visszaadása.
Ezen teljesítményprobléma megoldása érdekében a HDInsight-eszközök közvetlenül a fürtön futtatják a Hive-feladatokat a HiveServer2-n keresztül, kihagyva az RDP/SSH védelmet.
A jobb teljesítmény mellett a felhasználók Hive on Tez ábrákat és a feladat részleteit is megtekinthetik.

A HDInsight-fürt 3.2-es vagy újabb verziói esetén egy **Execute via HiveServer2** (Végrehajtás HiveServer2-n keresztül) gomb látható:

![hdinsight visual studio eszközök végrehajtás hiveserver2-n keresztül](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.execute.via.hiveserver2.png)

A streamelt naplók valós időben láthatók, és láthatja a feladatábrákat is, ha a Hive-lekérdezést a Tezben futtatja.

![hdinsight visual studio eszközök gyors elérési út hive végrehajtás](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.fast.path.hive.execution.png)

**A lekérdezések HiveServer2-n végzett végrehajtása és a lekérdezések WebHCaten végzett elküldése közötti különbségek**

Bár a lekérdezések HiveServer2-n keresztül végzett végrehajtásának számos előnnyel rendelkezik a teljesítmény szempontjából, vannak korlátozásai is. A korlátozások némelyike nem megfelelő az éles használathoz. Az alábbi táblázat a különbségeket mutatja:

| |Végrehajtás HiveServer2-n keresztül |Elküldés WebHCaten keresztül|
|---|---|---|
|Lekérdezések végrehajtása|Kiküszöböli a WebHCat többletterhelését (amely elindítja a „TempletonControllerJob” nevű MapReduce feladatot).|Amikor a WebHCaten keresztül hajtja végre a lekérdezést, a WebHCat elindít MapReduce feladatot, amely további késleltetést okoz.|
|Stream naplók vissza|Majdnem valós időben.|A feladatvégrehajtási naplók csak a feladat elvégzése után érhetők el.|
|Feladatelőzmények megtekintése|Ha egy lekérdezést a HiveServer2-n keresztül futtat, a feladatelőzményei (feladatnapló, feladatkimenet) nem maradnak meg. Az alkalmazás a YARN felhasználói felületen tekinthető meg korlátozott információkkal.|Ha egy lekérdezést a WebHCaten keresztül futtat, a feladatelőzményei (feladatnapló, feladatkimenet) megmaradnak és megtekinthetők a Visual Studio/HDInsight SDK/PowerShell használatával. |
|Ablak bezárása|  A HiveServer2-n keresztüli végrehajtás „szinkronos” módot jelent, így nyitva kell hagynia az ablakokat; ha bezárja az ablakokat, akkor megszakítja a lekérdezés végrehajtását.|A WebHCaten keresztül végzett küldés „aszinkronos” mód, így elküldheti a lekérdezést a WebHCaten keresztül, és bezárhatja a Visual Studio eszközt. Bármikor visszatérhet és megtekintheti az eredményeket.|


### Tez Hive feladat teljesítménye ábra

A HDInsight Visual Studio eszközök támogatják a Tez végrehajtómotor által végrehajtott Hive-feladatok teljesítményi ábráinak megjelenítését. A Tez engedélyezéséről további információért lásd: [use Hive in HDInsight][hdinsight.hive] (A Hive használata a HDInsightban). Miután elküldött egy Hive-feladatot a Visual Studióban, a Visual Studio megjeleníti az ábrát a feladat elvégzésekor.  Elképzelhető, hogy a **Refresh** (Frissítés) gombra kell kattintania a legfrissebb feladatállapot lekérdezéséhez.

> [AZURE.NOTE] Ez a szolgáltatás csak a HDInsight-fürt 3.2.4.593-as vagy újabb verzióihoz érhető el, és csak a befejezett feladatokhoz működik (ha a WebHCaten keresztül küldte el a feladatot; ez az ábra akkor jelenik meg, ha a lekérdezést a HiveServer2-n keresztül futtatja). Ez Windows- és Linux-alapú fürtökhöz is működik.

![hadoop hive tez teljesítmény ábra](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.hive.tez.performance.graph.png)

Hogy jobban megértse a Hive-lekérdezést, ez a kiadás a Hive operátor hozzáadása nézettel bővült. Csak duplán kell kattintania a feladat ábra csúcsaira, és láthatja a csúcsban lévő összes operátort. Egy adott operátor fölé helyezve a mutatót további részleteket is megtudhat az operátorról.

### Feladatvégrehajtási nézet a Hive on Tez feladatokhoz

A feladatvégrehajtási nézet a Hive on Tez feladatokhoz eszközzel strukturált és ábrázolt információkat szerezhet be a Hive-feladatokhoz, és további részleteket a feladatokról. Ahol probléma van a teljesítménnyel, a nézettel szerezhet be további részleteket. Például arról, hogyan működnek az egyes feladatok, és részletes információt kaphat mindegyik feladatról (adatírás/-olvasás, ütemezés/kezdő/záró idő stb.), hogy a megjelenített információk alapján hangolhassa a feladatkonfigurációkat vagy a rendszerarchitektúrát.

![hdinsight visual studio eszközök feladatvégrehajtási nézet](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.task.execution.view.png)

## Pig-parancsfájlok futtatása

A HDInsight Tools for Visual Studio támogatja a Pig-parancsfájlok létrehozását és HDInsight-fürtökbe végzett elküldését. A felhasználók Pig-projektet hozhatnak létre sablonból, majd elküldhetik a parancsfájlt a HDInsight-fürtökhöz.

## Visszajelzések és ismert problémák

- Jelenleg a HiveServer2 eredmények tiszta szöveggel jelennek meg, ami nem ideális. Dolgozunk ennek megoldásán.

- Ha az eredmények NULL értékekkel kezdődnek, az eredmények jelenleg nem jelennek meg. Megoldottuk ezt a problémát, és ha elakad ennél a hibánál, nyugodtan küldjön egy e-mailt, vagy lépjen kapcsolatba a Terméktámogatással.

- A Visual Studio által létrehozott HQL-parancsfájl a felhasználó helyi régióbeállításaitól függően kódolt. Lehet, hogy nem fut megfelelően, ha a felhasználó binárisként tölti fel a parancsfájlt a fürtbe.

Ha javaslata vagy visszajelzése van, vagy ha problémát észlel az eszköz használatakor, nyugodtan küldjön e-mailt a hdivstool@microsoft.com címre.

## Következő lépések
Ebben a cikkben megtanulta, hogyan csatlakozhat a HDInsight-fürtökhöz a Visual Studio eszközből a Hadoop-eszközök csomaggal, és hogyan futtathat Hive-lekérdezéseket. További információkért lásd:

- [Use Hadoop Hive in HDInsight][hdinsight.hive] (A Hadoop Hive használata a HDInsightban).
- [Get started using Hadoop in HDInsight][hdinsight.get.started] (A Hadoop első lépései a HDInsightban)
- [Submit Hadoop jobs in HDInsight][hdinsight.submit.jobs] (Hadoop-feladatok elküldése a HDInsightban)
- [Analyze Twitter data with Hadoop in HDInsight][hdinsight.analyze.twitter.data] (Twitter-adatok elemzése a Hadoop programmal a HDInsightban)


<!--Anchors-->
[Telepítés]: #installation
[Csatlakozás az Azure-előfizetéshez]: #connect-to-your-azure-subscription
[Navigálás a kapcsolt erőforrásokban]: #navigate-the-linked-resources
[Hive-lekérdezések futtatása]: #run-hive-queries
[Következő lépések]: #next-steps

<!--Image references-->
[1]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.wpi.png
[2]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.linked.resources.png
[5]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.server.explorer.png
[6]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.hive.schema.png
[7]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.create.hive.table.png
[8]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.run.hive.job.summary.png
[9]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.submit.jobs.advanced.png
[10]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.validate.hive.script.png
[11]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.new.hive.project.png
[12]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.view.hive.jobs.png
[13]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.table.names.png
[14]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.column.names.png


<!--Link references-->
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight.introduction]: hdinsight-hadoop-introduction.md
[hdinsight.get.started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight.hive]: hdinsight-use-hive.md
[hdinsight.submit.jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight.analyze.twitter.data]: hdinsight-analyze-twitter-data.md
[hdinsight.storm.visual.studio.tools]: hdinsight-storm-develop-csharp-visual-studio-topology.md
[hdinsight.access.application.logs]: hdinsight-hadoop-access-yarn-app-logs.md

[apache.hive]: http://hive.apache.org



<!--HONumber=sep16_HO1-->


