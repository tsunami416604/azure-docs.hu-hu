---
title: "A HDInsight az Azure portál használatával Windows-alapú Hadoop-fürtök kezelése |} Microsoft Docs"
description: "Megtudhatja, hogyan felügyelheti a HDInsight-szolgáltatás. HDInsight-fürtök létrehozása, nyissa meg az interaktív JavaScript-konzolt, és nyissa meg a Hadoop parancskonzolról."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 9295a988-bd88-453a-8c8b-55fa103bf39c
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: 3f1c7bb1795828899148a8ba8a0461a06947d40d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="manage-windows-based-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>A HDInsight Windows-alapú Hadoop-fürtök kezelése az Azure portál használatával

Használja a [Azure-portálon][azure-portal], Windows-alapú Hadoop-fürtök létrehozása az Azure HDInsight, Hadoop felhasználói jelszó módosításához, és engedélyezze a Remote Desktop Protocol (RDP), hogy hozzáférhessen a Hadoop parancskonzolról a fürtön.

A cikkben szereplő információk csak a Windows-alapú HDInsight-fürtök vonatkozik. Linux-alapú fürtökön kezeléséről további információért lásd: [kezelése Hadoop-fürtök a HDInsight az Azure portál használatával](hdinsight-administer-use-portal-linux.md).

> [!IMPORTANT]
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).


## <a name="prerequisites"></a>Előfeltételek

A cikk elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Az Azure Storage-fiók** -egy HDInsight-fürthöz az Azure Blob storage tárolót használja, mint az alapértelmezett fájlrendszert. Hogyan nyújt az Azure Blob storage a HDInsight-fürtökkel zökkenőmentes élményt kapcsolatos további információkért lásd: [az Azure Blob Storage a hdinsight eszközzel](hdinsight-hadoop-use-blob-storage.md). Egy Azure Storage-fiók létrehozásával kapcsolatos részletekért lásd: [a Storage-fiók létrehozása](../storage/common/storage-create-storage-account.md).

## <a name="open-the-portal"></a>A portál megnyitása
1. Jelentkezzen be [https://portal.azure.com](https://portal.azure.com).
2. A portál megnyitása után végezhetők el:

   * Kattintson a **új** új fürt létrehozása a bal oldali menüből:

       ![a HDInsight-fürt új gomb](./media/hdinsight-administer-use-management-portal/azure-portal-new-button.png)
   * Kattintson a **a HDInsight-fürtök** a bal oldali menüből.

       ![Az Azure portál HDInsight fürt gomb](./media/hdinsight-administer-use-management-portal/azure-portal-hdinsight-button.png)

     Ha **HDInsight** nem jelenik meg a bal oldali menüben, kattintson a **Tallózás**.

     ![Az Azure portál fürt gomb](./media/hdinsight-administer-use-management-portal/azure-portal-browse-button.png)

## <a name="create-clusters"></a>Fürtök létrehozása
A létrehozása a portál használatával, lásd: [HDInsight-fürtök létrehozása](hdinsight-hadoop-provision-linux-clusters.md).

HDInsight Hadoop széles tartomány-összetevők működik. A ellenőrizni és a támogatott összetevők listáját lásd: [Azure HDInsight Hadoop verziójának van](hdinsight-component-versioning.md). Testre szabhatja a HDInsight az alábbi lehetőségek egyikének használatával:

* Parancsfájl művelet segítségével szabhatja testre a fürt konfigurációjának módosítása, vagy egyéni összetevők, például Giraph vagy Solr telepítése egy fürt egyéni parancsfájlok. További információkért lásd: [testreszabása HDInsight-fürtjéhez parancsfájlművelet](hdinsight-hadoop-customize-cluster.md).
* A testreszabási Fürtparaméterek a HDInsight .NET SDK vagy az Azure PowerShell használata a fürt létrehozása során. Ezek a konfigurációs módosítások majd megmaradnak a fürt élettartama keresztül, és nem érinti a fürt csomópont reimages Azure platformon rendszeres karbantartás hajtja végre. A fürt testreszabási paraméterek használatával további információkért lásd: [HDInsight-fürtök létrehozása](hdinsight-hadoop-provision-linux-clusters.md).
* Néhány natív Java-összetevők, például Mahout és kaszkádolás, JAR-fájlok formájában futtathatja a fürtön. A JAR-fájlok az Azure Blob storage terjeszt, és elküldi a HDInsight-fürtök Hadoop-feladat elküldése mechanizmusokon keresztül. További információkért lásd: [nyújt Hadoop feladatok programozott módon](hdinsight-submit-hadoop-jobs-programmatically.md).

  > [!NOTE]
  > Ha problémába ütközik JAR-fájlok telepítése a HDInsight-fürtök, vagy hívja a JAR-fájlok a HDInsight-fürtökön, forduljon a [Microsoft Support](https://azure.microsoft.com/support/options/).
  >
  > Egymásra épülő HDInsight nem támogatja, és nincs for Microsoft Support jogosult. Támogatott összetevők listáját lásd: [What's new in HDInsight által biztosított fürt verziók](hdinsight-component-versioning.md).
  >
  >

A fürt távoli asztali kapcsolat segítségével egyéni szoftver telepítése nem támogatott. Kerülje a meghajtókat, az átjárócsomópont lévő fájlok tárolásához, ezek elvesznek kell újból létrehozni a fürtöket. Javasoljuk, hogy az Azure Blob Storage tárolóban lévő fájlok tárolásához. A BLOB storage egy állandó.

## <a name="list-and-show-clusters"></a>Listában, és a fürt megjelenítése
1. Jelentkezzen be [https://portal.azure.com](https://portal.azure.com).
2. Kattintson a **a HDInsight-fürtök** a bal oldali menüből.
3. Kattintson a fürt nevére. Ha a fürt lista hosszú, az oldal tetején a szűrő is használhatja.
4. Kattintson duplán a fürt részleteinek megjelenítése a listáról.

    **Menü és essentials**:

    ![Az Azure portál HDInsight fürt alapjai](./media/hdinsight-administer-use-management-portal/hdinsight-essentials.png)

   * A menü testreszabásához kattintson a jobb gombbal a menüt, és kattintson **Testreszabás**.
   * **Beállítások** és **összes beállítás**: Megjeleníti a **beállítások** panel a fürt, amely lehetővé teszi, hogy a fürt részletes konfigurációs adatok eléréséhez.
   * **Irányítópult**, **fürt irányítópult** és **URL-címe: a következő összes eszközökkel, a fürt irányítópult, amely Ambari Web Linux-alapú fürtök eléréséhez. -**Biztonságos rendszerhéj **: A fürtjét Secure Shell (SSH) kapcsolaton keresztül csatlakozni utasításokat tartalmazza.
   * **Fürt méretezése**: lehetővé teszi a fürt feldolgozó csomópontok számának megváltoztatására.
   * **Törlés**: törli a fürtöt.
   * **Gyors üzembe helyezés**: információit jeleníti meg, amelyek segítségével a HDInsight használatának megkezdésében.
   * ** Felhasználók: Lehetővé teszi a engedélyeinek beállítása *portál felügyeleti* a fürt más felhasználók számára az Azure-előfizetése.

     > [!IMPORTANT]
     > Ez *csak* hatással van a hozzáférést és engedélyeket ehhez a fürthöz az Azure portálon, és nincs hatása, akik csatlakozni vagy a HDInsight-fürt feladatok elküldéséhez.
     >
     >
   * **Címkék**: címkék lehetővé teszik a felhőalapú szolgáltatások egyéni besorolás meghatározásához kulcs/érték párok. Például létrehozhat nevű kulcs **projekt**, majd használja az adott projekthez tartozó összes szolgáltatás közös értéket.
   * **Az Ambari nézetek**: Ambari Web mutató hivatkozásokat tartalmaz.

     > [!IMPORTANT]
     > A HDInsight-fürt által nyújtott szolgáltatások kezelésére, az Ambari Web vagy az Ambari REST API-t kell használnia. További információ az Ambari használatával, lásd: [kezelése HDInsight-fürtök Ambari használatával](hdinsight-hadoop-manage-ambari.md).
     >
     >

     **Használati**:

     ![Az Azure portál HDInsight fürt használata](./media/hdinsight-administer-use-management-portal/hdinsight-portal-cluster-usage.png)
5. Kattintson a **beállítások**.

    ![Az Azure portál HDInsight fürt használata](./media/hdinsight-administer-use-management-portal/hdinsight.portal.cluster.settings.png)

   * **Tulajdonságok**: a fürt tulajdonságainak megtekintése.
   * **A fürt AAD-identitása**:
   * **Az Azure Storage-kulcsok**: az alapértelmezett tárfiók és a kulcsok megtekintéséhez. A beállítás a tárfiók a fürt létrehozása során.
   * **A fürt bejelentkezési**: módosítsa a fürt HTTP felhasználónevet és jelszót.
   * **Külső Metaadattárakat**: a Hive és az Oozie metastores megtekintése. A metaadattárakat csak konfigurálható úgy, hogy a fürt létrehozása során.
   * **Fürt méretezése**: növelése és a fürt feldolgozó csomópontok száma csökken.
   * **A távoli asztal**: engedélyezze és tiltsa le a távoli asztal (RDP) eléréséhez, és konfigurálja az RDP-felhasználónév.  A távoli asztalhoz tartozó felhasználónév a HTTP felhasználónévben különbözőnek kell lennie.
   * **Bejegyzett partner**:

     > [!NOTE]
     > Ez az egy általános rendelkezésre álló beállítások listája; nem minden tárolódjanak minden fürt esetében.
     >
     >
6. Kattintson a **tulajdonságok**:

    A Tulajdonságok szakaszának sorolja fel a következő:

   * **Állomásnév**: fürt nevét.
   * **A fürt URL-cím**.
   * **Állapot**: tartalmaznak megszakadt, fogadja el, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, működési, fut, a hiba, törlése, törlése, időtúllépésbe került, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued, ClusterCustomization
   * **A régióban**: Azure-beli hely. Támogatott Azure helyek listáját lásd: a **régió** lévő legördülő lista [HDInsight árképzési](https://azure.microsoft.com/pricing/details/hdinsight/).
   * **Létrehozott adatokat**.
   * **Operációs rendszer**: vagy **Windows** vagy **Linux**.
   * **Típus**: Hadoop, HBase, Storm, Spark.
   * **Verzió**. Lásd: [HDInsight-verziókról](hdinsight-component-versioning.md)
   * **Előfizetés**: előfizetés nevét.
   * **Előfizetés-azonosító**.
   * **Elsődleges adatforrás**. Az Azure Blob storage-fiók alapértelmezés szerint használt Hadoop-fájlrendszer.
   * **IP-címek munkavégző csomópontokhoz**.
   * **Átjárócsomópont tarifacsomag**.

## <a name="delete-clusters"></a>Fürtök törlése
Törli a fürt nem törli az alapértelmezett tárfiók vagy az összes kapcsolt tárfiókot. Újra létrehozhatja a fürtöt azonos tárfiókok és ugyanazon a metaadattárakat használatával.

1. Jelentkezzen be a [Portal][azure-portal].
2. Kattintson a **összes tallózása** a bal oldali menüben kattintson a **a HDInsight-fürtök**, kattintson a fürt nevére.
3. Kattintson a **törlése** a felső menüben, majd kövesse az utasításokat.

Lásd még: [fürtök szünet/Leállítás](#pauseshut-down-clusters).

## <a name="scale-clusters"></a>Fürtök méretezése
A fürt skálázás funkciót lehetővé teszi, hogy anélkül, hogy újra létre kell hoznia a fürt fut az Azure HDInsight fürt által használt feldolgozó csomópontok számának módosítása.

> [!NOTE]
> Csak verzió 3.1.3 hdinsight clusters vagy annál magasabb támogatottak. Ha biztos benne, hogy a fürt verzióját, a Tulajdonságok lapján ellenőrizheti.  Lásd: [listája és megjelenítése fürtök](#list-and-show-clusters).
>
>

A fürt a HDInsight által támogatott különböző típusú adatok csomópontok számának módosítása következményei:

* Hadoop

    Zökkenőmentesen növelheti adhatja meg, hogy minden folyamatban lévő vagy a futó feladatok befolyásolása nélkül fut egy Hadoop-fürt feldolgozó csomópontjainak számát. Új feladatokat is küldheti el, amíg a művelet folyamatban van. A méretezési művelet sikertelen szabályosan kezeli, hogy a fürt mindig működőképes állapotban marad.

    A Hadoop fürtök adatok csomópontok számának csökkentésével átméretezi, ha néhány, a fürt a szolgáltatások újraindításáig. Ennek hatására a összes futó és függőben lévő feladatok meghiúsulhatnak, a méretezési művelet befejezését. Akkor is, azonban küldje el újra a feladatok a művelet végrehajtása után.
* HBase

    Akkor is zökkenőmentesen csomópontok hozzáadásához és eltávolításához a HBase-fürtöt a futtatása. Területi kiszolgálók automatikus elosztását a méretezési művelet befejezését néhány percen belül. Azonban Ön kézzel is eloszthatja a regionális kiszolgálók fürt headnode való bejelentkezés, és futtatja a következő parancsokat egy parancssori ablakot:

        >pushd %HBASE_HOME%\bin
        >hbase shell
        >balancer

    A HBase rendszerhéjjal további információkért lásd:]
* Storm

    Akkor is zökkenőmentesen csomópontok hozzáadásához és eltávolításához adatok Storm fürthöz való futtatása során. De a méretezési művelet sikeres befejezését követően szüksége lesz a topológia egyensúlyba.

    Kétféle módon valósítható meg újraelosztás:

  * A Storm webes felhasználói felület
  * Parancssori felület (CLI) eszköz

    Tekintse meg a [alatt futó Apache Storm-dokumentáció](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) további részleteket.

    A Storm webes felhasználói felület érhető el a HDInsight-fürt:

    ![A HDInsight alatt futó storm méretezési egyensúlyozza ki újra](./media/hdinsight-administer-use-management-portal/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Íme egy példa a CLI parancs használata a Storm-topológia egyensúlyba:

        ## Reconfigure the topology "mytopology" to use 5 worker processes,
        ## the spout "blue-spout" to use 3 executors, and
        ## the bolt "yellow-bolt" to use 10 executors
        $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

**Fürtök méretezése**

1. Jelentkezzen be a [Portal][azure-portal].
2. Kattintson a **összes tallózása** a bal oldali menüben kattintson a **a HDInsight-fürtök**, kattintson a fürt nevére.
3. Kattintson a **beállítások** a felső menüben, majd kattintson a **méretezési fürt**.
4. Adja meg **számát a feldolgozó csomópontok**. A csomópont számára vonatkozó korlátozást Azure-előfizetések függően változik. A korlát növeléséhez Számlázási támogatást kérhetnek.  A Költséginformációk változik meg a módosítások a csomópontok számát.

    ![HDInsight Hadoop HBase Storm Spark méretezési](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.png)

## <a name="pauseshut-down-clusters"></a>Fürtök szünet/leállítása
A legtöbb Hadoop-feladat kötegelt feladatok, amelyek csak esetenként futott. A legtöbb Hadoop-fürtök vonatkoznak-e nagy időszakokra, amely a fürt nem használják a feldolgozáshoz. A HDInsight az Azure Storage szolgáltatásban tárolja az adatokat, így biztonságosan törölhet olyan fürtöket, amelyek nincsenek használatban.
Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket.

Meg a program a folyamat számos módja van:

* Az Azure Data Factory felhasználó. Lásd: [Azure HDInsight társított szolgáltatás](../data-factory/compute-linked-services.md) és [alakít át és elemez az Azure Data Factory használatával](../data-factory/transform-data.md) igény szerinti és önálló definiált hdinsight összekapcsolt szolgáltatások.
* Azure PowerShell használatával.  Lásd: [repülési késleltetés adatok elemzése](hdinsight-analyze-flight-delay-data.md).
* Az Azure CLI használata. Lásd: [kezelése HDInsight-fürtök Azure parancssori felület használatával](hdinsight-administer-use-command-line.md).
* A HDInsight .NET SDK használata. Lásd: [nyújt Hadoop-feladatokat](hdinsight-submit-hadoop-jobs-programmatically.md).

Díjszabási információkért lásd: [HDInsight árképzési](https://azure.microsoft.com/pricing/details/hdinsight/). Törölni a fürtöt a portálról, lásd: [fürtök törlése](#delete-clusters)

## <a name="change-cluster-username"></a>Változás fürt felhasználónév
HDInsight-fürtök lehet két felhasználói fiókot. A HDInsight fürt felhasználói fiók jön létre a létrehozási folyamat során. RDP-kapcsolaton keresztül a fürt eléréséhez egy RDP-felhasználói fiókot is létrehozhat. Lásd: [engedélyezése a távoli asztal](#connect-to-hdinsight-clusters-by-using-rdp).

**A HDInsight fürt felhasználónév és jelszó módosítása**

1. Jelentkezzen be a [Portal][azure-portal].
2. Kattintson a **összes tallózása** a bal oldali menüben kattintson a **a HDInsight-fürtök**, kattintson a fürt nevére.
3. Kattintson a **beállítások** a felső menüben, majd kattintson a **fürt bejelentkezési**.
4. Ha **fürt bejelentkezési** lett engedélyezve van, meg kell nyomnia **letiltása**, és kattintson a **engedélyezése** a felhasználónév és jelszó módosítása előtt...
5. Módosítsa a **fürt bejelentkezési neve** és/vagy a **fürt bejelentkezési jelszó**, és kattintson a **mentése**.

    ![A HDInsight fürt felhasználói felhasználónév jelszó http felhasználó módosítása](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)

## <a name="grantrevoke-access"></a>Hozzáférés biztosítása/visszavonása
A HDInsight-fürtök a következő HTTP webszolgáltatásokat (ezen szolgáltatások mindegyikéhez rendelkezik RESTful végpontok) rendelkezik:

* ODBC
* JDBC
* Ambari
* Oozie
* Lépni a Templeton

Alapértelmezés szerint ezek a szolgáltatások hozzáférés vonatkozóan biztosított. Akkor is a visszavonási/grant a hozzáférés az Azure portálról.

> [!NOTE]
> A hozzáférés biztosítása/visszavonása, visszaállítja, a fürt felhasználónevet és jelszót.
>
>

**Az engedélyezéshez/visszavonáshoz HTTP webes szolgáltatásokhoz férhetnek hozzá**

1. Jelentkezzen be a [Portal][azure-portal].
2. Kattintson a **összes tallózása** a bal oldali menüben kattintson a **a HDInsight-fürtök**, kattintson a fürt nevére.
3. Kattintson a **beállítások** a felső menüben, majd kattintson a **fürt bejelentkezési**.
4. Ha **fürt bejelentkezési** lett engedélyezve van, meg kell nyomnia **letiltása**, és kattintson a **engedélyezése** a felhasználónév és jelszó módosítása előtt...
5. A **fürt bejelentkezési felhasználónevének** és **fürt bejelentkezési jelszó**, adja meg az új felhasználónevet és jelszót (illetve) a fürthöz.
6. Kattintson a **SAVE** (Mentés) gombra.

    ![HDInsight teljes http webes hozzáférés eltávolítása](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)

## <a name="find-the-default-storage-account"></a>Az alapértelmezett tárfiók keresése
Minden egyes HDInsight-fürt rendelkezik egy alapértelmezett tárfiókot. Megjelenik az alapértelmezett tárfiók és a fürt kulcsait **beállítások**/**tulajdonságok**/**Azure Storage kulcsok**. Lásd: [listája és megjelenítése fürtök](#list-and-show-clusters).

## <a name="find-the-resource-group"></a>Az erőforráscsoport keresése
Az Azure Resource Manager módban mindegyik HDInsight-fürt létrehozása egy Azure-erőforrás csoporttal. A fürthöz tartozó Azure erőforráscsoport jelenik meg:

* A fürt listája tartalmaz egy **erőforráscsoport** oszlop.
* Fürt **alapvető** csempére.  

Lásd: [listája és megjelenítése fürtök](#list-and-show-clusters).

## <a name="open-hdinsight-query-console"></a>Nyissa meg a HDInsight lekérdezés konzolt
A HDInsight lekérdezés konzol az alábbi szolgáltatásokat tartalmazza:

* **Hive szerkesztő**: webes felülete A grafikus felhasználói Felülettel Hive-feladatok elküldésekor.  Lásd: [a lekérdezés konzollal Hive futtatása lekérdezések](hdinsight-hadoop-use-hive-query-console.md).

    ![HDInsight portál hive szerkesztő](./media/hdinsight-administer-use-management-portal/hdinsight-hive-editor.png)
* **Feladatelőzmények**: Monitor Hadoop-feladatokat.  

    ![HDInsight portál feladatelőzmények](./media/hdinsight-administer-use-management-portal/hdinsight-job-history.png)

    Kattintson a **lekérdezésnév** feladattulajdonság, beleértve a részletek megjelenítéséhez **feladat lekérdezés**, és ** Job Output. A lekérdezés, mind a kimeneti is letöltheti a munkaállomáson.
* **Fájl a böngésző**: tekintse át az alapértelmezett tárfiókot, és a társított tárfiókokat.

    ![HDInsight portál fájl böngésző Tallózás](./media/hdinsight-administer-use-management-portal/hdinsight-file-browser.png)

    A képernyőképen látható a  **<Account>**  típus azt jelzi, hogy a cikk az Azure storage-fiók.  Kattintson a fiók nevére, a fájlok tallózásához.
* **Hadoop-UI**.

    ![HDInsight portal Hadoop felhasználói felület](./media/hdinsight-administer-use-management-portal/hdinsight-hadoop-ui.png)

    A **Hadoop felhasználói felület*, keresse meg a fájlokat, és ellenőrizze a naplókat.
* **A yarn felhasználói felület**.

    ![HDInsight portal YARN felhasználói felületen](./media/hdinsight-administer-use-management-portal/hdinsight-yarn-ui.png)

## <a name="run-hive-queries"></a>Hive-lekérdezések futtatása
Hive-feladatok futtatása a portálról, kattintson a **Hive szerkesztő** a HDInsight lekérdezés konzolon. Lásd: [nyissa meg a HDInsight-lekérdezés konzol](#open-hdinsight-query-console).

## <a name="monitor-jobs"></a>Feladatok figyelése
A portálról feladat figyeléséhez kattintson **Feladatelőzményekben** a HDInsight lekérdezés konzolon. Lásd: [nyissa meg a HDInsight-lekérdezés konzol](#open-hdinsight-query-console).

## <a name="browse-files"></a>Fájlok tallózása
Keresse meg az alapértelmezett tárfiók és a társított tárfiókokat tárolt fájlok, kattintson a **fájl böngésző** a HDInsight lekérdezés konzolon. Lásd: [nyissa meg a HDInsight-lekérdezés konzol](#open-hdinsight-query-console).

Használhatja a **keresse meg a fájlrendszer** az eszközt a **Hadoop felhasználói felület** a HDInsight-konzolon.  Lásd: [nyissa meg a HDInsight-lekérdezés konzol](#open-hdinsight-query-console).

## <a name="monitor-cluster-usage"></a>A figyelő fürt használata
A **használati** szakasz a HDInsight-fürt panelről az elérhető és a HDInsight együttes használata az előfizetés magok száma, valamint az ehhez a fürthöz, és azok elosztását vezérli a fürtben levő csomópontok számára lefoglalt magok száma információkat jelenít meg. Lásd: [listája és megjelenítése fürtök](#list-and-show-clusters).

> [!IMPORTANT]
> A HDInsight-fürt által nyújtott szolgáltatások figyeléséhez, az Ambari Web vagy az Ambari REST API-t kell használnia. További információ az Ambari használatával, lásd: [kezelése HDInsight-fürtök Ambari használatával](hdinsight-hadoop-manage-ambari.md)
>
>

## <a name="open-hadoop-ui"></a>Nyissa meg a Hadoop felhasználói felület
A fürt figyelésére, keresse meg a fájlrendszer, és ellenőrizze a naplókat, kattintson az **Hadoop felhasználói felület** a HDInsight lekérdezés konzolon. Lásd: [nyissa meg a HDInsight-lekérdezés konzol](#open-hdinsight-query-console).

## <a name="open-yarn-ui"></a>Nyissa meg a Yarn felhasználói felületen
A Yarn felhasználói felületen használatához kattintson **Yarn felhasználói felületen** a HDInsight lekérdezés konzolon. Lásd: [nyissa meg a HDInsight-lekérdezés konzol](#open-hdinsight-query-console).

## <a name="connect-to-clusters-using-rdp"></a>Csatlakozás RDP-fürtök
A fürt a létrehozása a megadott hitelesítő adatai hozzáférést a fürtön a szolgáltatásokat, de nem maga a fürt távoli asztalon keresztül. Ha a fürt, vagy a fürt létesítése után kapcsolhatja be a távoli asztal eléréséhez. A távoli asztal engedélyezése a létrehozásakor vonatkozó utasításokért lásd: [hozzon létre HDInsight-fürt](hdinsight-hadoop-provision-linux-clusters.md).

**Távoli asztal engedélyezése**

1. Jelentkezzen be a [Portal][azure-portal].
2. Kattintson a **összes tallózása** a bal oldali menüben kattintson a **a HDInsight-fürtök**, kattintson a fürt nevére.
3. Kattintson a **beállítások** a felső menüben, majd kattintson a **távoli asztal**.
4. Adja meg **lejárati dátuma**, **távoli asztali felhasználónévnek** és **távoli asztal jelszó**, és kattintson a **engedélyezése**.

    ![HDInsight engedélyezi letiltja a távoli asztal konfigurálása](./media/hdinsight-administer-use-management-portal/hdinsight.portal.remote.desktop.png)

    Az alapértelmezett értékeit lejárati dátuma a hét.

   > [!NOTE]
   > A HDInsight .NET SDK használatával a távoli asztal engedélyezése a fürtön. Használja a **EnableRdp** módszer a HDInsight ügyfél objektum a következő módon: **ügyfél. EnableRdp (fürtnév, hely, "rdpuser", "rdppassword" DateTime.Now.AddDays(6))**. Hasonlóképpen, a távoli asztal letiltása a fürtön, segítségével **ügyfél. (A fürtnév, a hely) DisableRdp**. E módszerekkel kapcsolatos további információkért lásd: [HDInsight .NET SDK-dokumentáció](http://go.microsoft.com/fwlink/?LinkId=529017). Ez az csak a HDInsight-fürtökön futó Windows alkalmazható.
   >
   >

**RDP használatával csatlakozni egy fürthöz**

1. Jelentkezzen be a [Portal][azure-portal].
2. Kattintson a **összes tallózása** a bal oldali menüben kattintson a **a HDInsight-fürtök**, kattintson a fürt nevére.
3. Kattintson a **beállítások** a felső menüben, majd kattintson a **távoli asztal**.
4. Kattintson a **Connect** és kövesse az utasításokat. Ha Connect tiltsa le, engedélyeznie kell azt először. Győződjön meg arról, hogy a távoli asztali felhasználói felhasználónévvel és jelszóval.  A fürt felhasználói hitelesítő adatok nem használhatók.

## <a name="open-hadoop-command-line"></a>Nyissa meg a Hadoop parancssor
Távoli asztal használatával csatlakozzon a fürthöz, és a Hadoop parancsot használja, kell először engedélyezte a fürt távoli asztal eléréséhez az előző szakaszban leírtak szerint.

**A Hadoop parancssor megnyitása**

1. Csatlakozzon a fürthöz, a távoli asztal használatával.
2. Kattintson duplán az asztalon **Hadoop parancssori**.

    ![HDI. HadoopCommandLine][image-hadoopcommandline]

    További információk a Hadoop-parancsok: [Hadoop-parancsok hivatkozás](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html).

Előző képernyőképen látható a mappa neve verziószáma a Hadoop beágyazott. A verziószám módosítható a fürtön telepíteni a Hadoop-összetevők verziója alapján. Hadoop környezeti változók segítségével tekintse meg a mappákra. Példa:

    cd %hadoop_home%
    cd %hive_home%
    cd %hbase_home%
    cd %pig_home%
    cd %sqoop_home%
    cd %hcatalog_home%

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben megtanulta rendelkezik a HDInsight-fürtök létrehozása a portál használatával, és nyissa meg a parancssori eszköz a Hadoop. További tudnivalókért tekintse meg a következő cikkeket:

* [Felügyelheti a HDInsight az Azure PowerShell használatával](hdinsight-administer-use-powershell.md)
* [Felügyelheti a HDInsight az Azure parancssori felület használatával](hdinsight-administer-use-command-line.md)
* [A HDInsight-fürtök létrehozása](hdinsight-hadoop-provision-linux-clusters.md)
* [Hadoop-feladatokat programozott módon küldhet](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Ismerkedés az Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
* [Azure HDInsight Hadoop verziójának van?](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-management-portal/hdinsight-hadoop-command-line.png "Hadoop parancssor"
