---
title: A HDInsight az Azure Portalon Windows-alapú Hadoop-fürtök kezelése
description: Útmutató a HDInsight szolgáltatás felügyeletéhez. Hozzon létre egy HDInsight-fürtöt, nyissa meg az interaktív JavaScript-konzolt, és nyissa meg a Hadoop parancssori konzolt.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: jasonh
ROBOTS: NOINDEX
ms.openlocfilehash: a4cbc0c14d0f6c505b391becf33c56dd95bfc251
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39592220"
---
# <a name="manage-windows-based-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>A HDInsight Windows-alapú Hadoop-fürtök kezelése az Azure portal használatával

Használatával a [az Azure portal][azure-portal], Windows-alapú Hadoop-fürtök létrehozása az Azure HDInsight, Hadoop-felhasználó jelszavának módosítására, és távoli asztal protokoll (RDP) engedélyezése, hogy hozzáférhessen a Hadoop-parancs a konzol a fürtön.

Ebben a cikkben található információk csak a Windows-alapú HDInsight-fürtök vonatkozik. Linux-alapú fürtök kezelésével kapcsolatos információkért lásd: [kezelése Hadoop-fürtök a HDInsight az Azure portal használatával](hdinsight-administer-use-portal-linux.md).

> [!IMPORTANT]
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).


## <a name="prerequisites"></a>Előfeltételek

A cikk elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Az Azure Storage-fiók** – egy HDInsight-fürt az Azure Blob storage-tárolót használja, mint az alapértelmezett fájlrendszer. Hogyan nyújt az Azure Blob storage a zökkenőmentes élményt a HDInsight-fürtökkel kapcsolatos további információkért lásd: [az Azure Blob Storage a HDInsight](hdinsight-hadoop-use-blob-storage.md). Azure Storage-fiók létrehozásával kapcsolatos részletekért lásd: [Storage-fiók létrehozása](../storage/common/storage-create-storage-account.md).

## <a name="open-the-portal"></a>Nyissa meg a portálon
1. Jelentkezzen be a [ https://portal.azure.com ](https://portal.azure.com).
2. Miután megnyitotta a portálon, teszi lehetővé:

   * Kattintson a **erőforrás létrehozása** hozzon létre egy új fürtöt, a bal oldali menüből:

       ![új HDInsight-fürt gomb](./media/hdinsight-administer-use-management-portal/azure-portal-new-button.png)
   * Kattintson a **HDInsight-fürtök** a bal oldali menüből.

       ![Az Azure portal HDInsight fürt gomb](./media/hdinsight-administer-use-management-portal/azure-portal-hdinsight-button.png)

     Ha **HDInsight** nem jelennek meg a bal oldali menüben, kattintson a **Tallózás**.

     ![Az Azure portal fürt tallózógomb](./media/hdinsight-administer-use-management-portal/azure-portal-browse-button.png)

## <a name="create-clusters"></a>Fürtök létrehozása
A létrehozása a portál használatával, lásd: [létre HDInsight-fürtök](hdinsight-hadoop-provision-linux-clusters.md).

HDInsight együttműködik egy széles számos Hadoop-összetevők. Az ellenőrzött és a támogatott összetevők listáját lásd: [Azure HDInsight Hadoop mely verziója van](hdinsight-component-versioning.md). Testre szabható HDInsight az alábbi lehetőségek egyikének használatával:

* Szkriptműveletek használatával egyéni parancsfájlok vagy fürt konfigurációjának módosításához, vagy egyéni összetevők, például a Giraph és a Solr telepítése egy fürt testre szabhatja. További információkért lásd: [Szkriptműveletek használatával testre szabhatja a HDInsight-fürt](hdinsight-hadoop-customize-cluster.md).
* A fürt a HDInsight .NET SDK vagy az Azure PowerShell-lel testreszabási paramétereket használja a fürt létrehozása során. Konfigurációs módosítások majd megmaradnak keresztül a fürt élettartama, és nem érinti a fürt csomópont különbséglemezt, amely az Azure platform a Microsoft rendszeresen végez karbantartás céljából. A fürt testreszabása a paraméterek használatával további információkért lásd: [létre HDInsight-fürtök](hdinsight-hadoop-provision-linux-clusters.md).
* Néhány natív Java-összetevők, például a Mahout és kaszkádolás, JAR-fájlként futtathatja a fürtön. A JAR-fájlok az Azure Blob storage-terjeszt, és Hadoop-feladat beküldése mechanizmusokon keresztül a HDInsight-fürtök elküldve. További információkért lásd: [elküldése Hadoop-feladatok programozott módon](hadoop/submit-apache-hadoop-jobs-programmatically.md).

  > [!NOTE]
  > Ha problémákba ütközik a HDInsight-fürtök üzembe helyezése a JAR-fájlok vagy meghívásakor JAR-fájlok a HDInsight-fürtökön, forduljon [Support](https://azure.microsoft.com/support/options/).
  >
  > Lépcsőzetes HDInsight nem támogatja, és nem jogosult a Microsoft Support. Támogatott összetevők listáját lásd: [a HDInsight által biztosított fürtverziók újdonságai](hdinsight-component-versioning.md).
  >
  >

A fürtön a távoli asztal kapcsolat használatával egyéni szoftverek telepítése nem támogatott. Kerülje a fő csomópont, a meghajtók lévő fájlok tárolására, ezek elvesznek, újból létre kell hozni a fürtök. Azt javasoljuk, hogy az Azure Blob storage-fájlok tárolására. A BLOB storage szolgáltatás állandó.

## <a name="list-and-show-clusters"></a>Fürtök listázása és megjelenítése
1. Jelentkezzen be a [ https://portal.azure.com ](https://portal.azure.com).
2. Kattintson a **HDInsight-fürtök** a bal oldali menüből.
3. Kattintson a fürt nevére. Ha a fürt lista hosszú, az oldal tetején a szűrő is használhatja.
4. Kattintson duplán a fürtöt, a listában a részleteinek megjelenítéséhez.

    **Menü és essentials**:

    ![Az Azure portal HDInsight fürt alapjai](./media/hdinsight-administer-use-management-portal/hdinsight-essentials.png)

   * Testre szabhatja a menüben, kattintson a jobb gombbal a menüben, és kattintson **Testreszabás**.
   * **Beállítások** és **minden beállítás**: Megjeleníti a **beállítások** panelen a fürt, amely lehetővé teszi, hogy a fürt részletes konfigurációs adatok eléréséhez.
   * **Irányítópult**, **fürt irányítópultja** és **URL-cím**: a fürt irányítópultját, amely Linux-alapú fürtök az Ambari webes összes módjai az alábbiak.
   * **Secure Shell**: jeleníti meg az utasításokat követve csatlakozhat a fürthöz Secure Shell (SSH) kapcsolaton keresztül.
   * **Fürt méretezése**: lehetővé teszi, hogy a fürt munkavégző csomópontok számának módosítását.
   * **Törlés**: törli a fürtöt.
   * **A rövid útmutató**: információit jeleníti meg, amelyekkel HDInsight használatának első lépései.
   * **Felhasználók**: lehetővé teszi, hogy a használt engedélyek beállítása *portál felügyeleti* a fürt más felhasználók számára az Azure-előfizetésében.

     > [!IMPORTANT]
     > Ez *csak* hatással van a hozzáférést és engedélyeket ehhez a fürthöz az Azure Portalon, és aki csatlakozni, vagy a HDInsight-fürt-feladatok elküldése nem érinti.
     >
     >
   * **A címkék**: a címkék lehetővé teszik, hogy állítsa be a kulcs/érték párok meghatározásához egy egyéni besorolás, a cloud services. Például előfordulhat, hogy létre nevű kulcs **projekt**, majd használja az adott projekthez tartozó összes szolgáltatás közös értéket.
   * **Ambari-nézetek**: az Ambari webes hivatkozások.

     > [!IMPORTANT]
     > A HDInsight-fürt által nyújtott szolgáltatások kezelése, Ambari Web vagy az Ambari REST API-t kell használnia. Az Ambari használatával kapcsolatos további információkért lásd: [kezelése a HDInsight-fürtök az Ambari](hdinsight-hadoop-manage-ambari.md).
     >
     >

     **Használati**:

     ![Az Azure portal HDInsight fürt használat](./media/hdinsight-administer-use-management-portal/hdinsight-portal-cluster-usage.png)
5. Kattintson a **beállítások**.

    ![Az Azure portal HDInsight fürt használat](./media/hdinsight-administer-use-management-portal/hdinsight.portal.cluster.settings.png)

   * **Tulajdonságok**: megtekintheti a tulajdonságait.
   * **AAD-identitását a fürt**:
   * **Azure-Tárkulcsok**: az alapértelmezett tárfiókot és a kulcs megtekintéséhez. A tárfiók a konfiguráció a fürt létrehozása során.
   * **A fürt bejelentkezési**: módosítsa a fürt HTTP-felhasználónevet és jelszót.
   * **Külső Metaadattárak**: az Oozie és a Hive-metaadattárak megtekintéséhez. A metaadattárakat csak a fürt létrehozása során konfigurálható.
   * **Fürt méretezése**: növekedését és csökkenését a fürt munkavégző csomópontok számát.
   * **A távoli asztal**: engedélyezze és tiltsa le a távoli asztali (RDP) hozzáférést, és konfigurálja az RPD-felhasználónév.  Az RDP-felhasználó neve eltér a HTTP-felhasználó nevét kell lennie.
   * **Bejegyzett partner**:

     > [!NOTE]
     > Ez a rendelkezésre álló beállítások; általános listája nem minden lesz megtalálható az összes fürt esetében.
     >
     >
6. Kattintson a **tulajdonságok**:

    A Tulajdonságok szakaszának a következőket tartalmazza:

   * **Állomásnév**: fürt neve.
   * **A fürt URL-cím**.
   * **Állapot**: tartalmaznak megszakítva fogadják el, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, működési, fut, a hiba, törlése, a törlés időtúllépés miatt megszakadt, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued, ClusterCustomization
   * **Régió**: az Azure-helyen. Az Azure a támogatott helyek listáját lásd: a **régió** legördülő lista [HDInsight díjszabása](https://azure.microsoft.com/pricing/details/hdinsight/).
   * **Létrehozott**.
   * **Operációs rendszer**: vagy **Windows** vagy **Linux**.
   * **Típus**: Hadoop, HBase, Storm, Spark.
   * **Verzió**. Lásd: [HDInsight-verziók](hdinsight-component-versioning.md)
   * **Előfizetés**: előfizetés nevét.
   * **Előfizetés-azonosító**.
   * **Elsődleges adatforrás**. Az Azure Blob storage-fiók alapértelmezett használni a Hadoop-fájlrendszer.
   * **Munkavégző csomópontok tarifacsomag**.
   * **Átjárócsomópont tarifacsomag**.

## <a name="delete-clusters"></a>Fürtök törlése
Fürt törlése nem törli az alapértelmezett tárfiókot, vagy az összes kapcsolt tárfiókot. Ugyanazt a tárfiókot és az azonos metaadattárakat használatával újra létrehozhatja a fürtöt.

1. Jelentkezzen be a [portál][azure-portal].
2. Kattintson a **összes tallózása** a bal oldali menüben kattintson a **HDInsight-fürtök**, a fürt nevére.
3. Kattintson a **törlése** a felső menüben, majd kövesse az utasításokat.

Lásd még: [fürtök Szüneteltetés és leállítás](#pauseshut-down-clusters).

## <a name="scale-clusters"></a>Fürtök méretezése
A fürtméretezés egy funkció lehetővé teszi, hogy a fürt újbóli létrehozása nélkül fut az Azure HDInsight-fürt által használt munkavégző csomópontok számának módosítását.

> [!NOTE]
> Csak 3.1.3 verziójú HDInsight-fürtök vagy újabb verziója támogatott. Ha biztos benne, hogy a fürt verziója, a Tulajdonságok lapon ellenőrizheti.  Lásd: [fürtök listázása és megjelenítése](#list-and-show-clusters).
>
>

A fürt a HDInsight által támogatott különböző típusú adatok csomópontok számának módosításával hatásai:

* Hadoop

    Zökkenőmentesen lehet növelni egy Hadoop-fürtöt, amely a folyamatban lévő vagy futó feladatok befolyásolása nélkül fut-e a munkavégző csomópontok számát. Új feladatok is lehet beküldeni, amíg a művelet folyamatban van. A skálázási művelet hibák szabályosan kezeli, úgy, hogy a fürt minden esetben működőképes állapotban marad.

    Ha egy Hadoop-fürtöt az adatcsomópontok száma csökkentésével vertikálisan leskálázni, a fürtben a szolgáltatások újra lesz indítva. Ennek hatására a összes futó és a függőben lévő feladatok meghiúsulhatnak, a skálázási művelet befejezése után. A feladatok újból elküldheti, azonban a művelet befejeződése után.
* HBase

    Zökkenőmentesen adja hozzá vagy távolíthat el csomópontokat a HBase-fürt futás közben. Regionális kiszolgáló automatikusan kiegyensúlyozott vannak a skálázási művelet befejezése néhány percen belül. Azonban, manuálisan is eloszthatja a regionális kiszolgálók jelentkezik be a fürt átjárócsomópontjával, és a egy parancssori ablakot a következő parancsokat futtatni:

        >pushd %HBASE_HOME%\bin
        >hbase shell
        >balancer

    A HBase-rendszerhéj használata további információkért lásd:]
* Storm

    Zökkenőmentesen adja hozzá vagy távolít el a Storm-fürt adatcsomópontok futás közben. Azonban a topológia újraegyensúlyozására kell a skálázási művelet a sikeres telepítést követően.

    Az újraegyensúlyozás két módon is elvégezhető:

  * A Storm webes felhasználói felületen
  * Parancssori felület (CLI) eszköz

    Tekintse meg a [Apache Storm-dokumentáció](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) további részletekért.

    A Storm webes felhasználói felületen a HDInsight-fürtön érhető el:

    ![HDInsight storm méretezési újraegyensúlyozási](./media/hdinsight-administer-use-management-portal/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Íme egy példa a Storm-topológia újraegyensúlyozására a CLI-parancs használatával:

        ## Reconfigure the topology "mytopology" to use 5 worker processes,
        ## the spout "blue-spout" to use 3 executors, and
        ## the bolt "yellow-bolt" to use 10 executors
        $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

**Fürtök méretezése**

1. Jelentkezzen be a [portál][azure-portal].
2. Kattintson a **összes tallózása** a bal oldali menüben kattintson a **HDInsight-fürtök**, a fürt nevére.
3. Kattintson a **beállítások** a felső menüben, és kattintson a **fürt méretezése**.
4. Adja meg **száma a munkavégző csomópontok**. A fürtcsomópont korlátot változik, az Azure-előfizetések között. Növelje a számlázási támogatással fordulnia.  A költségadatok fogja tartalmazni a módosítások a csomópontok számát.

    ![HDInsight Hadoop HBase Storm Spark skála](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.png)

## <a name="pauseshut-down-clusters"></a>Fürtök Szüneteltetés és leállítás
Hadoop-feladatokat a legtöbb olyan kötegelt feladatok, amelyek csak esetenként futott. A legtöbb Hadoop-fürtök nincsenek nagy időtartamra, amely a fürt nem használja a feldolgozáshoz. A HDInsight az Azure Storage szolgáltatásban tárolja az adatokat, így biztonságosan törölhet olyan fürtöket, amelyek nincsenek használatban.
Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket.

Számos módon meg a program a folyamatot:

* Az Azure Data Factory felhasználói. Lásd: [Azure HDInsight társított szolgáltatás](../data-factory/compute-linked-services.md) és [át és elemez az Azure Data Factory használatával](../data-factory/transform-data.md) az igény szerinti és önálló meghatározott HDInsight társított szolgáltatás.
* Az Azure PowerShell-lel.  Lásd: [repülőjáratok késési adatainak elemzése](hdinsight-analyze-flight-delay-data.md).
* Az Azure CLI használatával. Lásd: [kezelése a HDInsight-fürtök Azure CLI-vel](hdinsight-administer-use-command-line.md).
* HDInsight .NET SDK használata. Lásd: [elküldése Hadoop-feladatok](hadoop/submit-apache-hadoop-jobs-programmatically.md).

Díjszabási információkért lásd: [HDInsight díjszabása](https://azure.microsoft.com/pricing/details/hdinsight/). Tekintse meg a fürt törlésének a portálról, [fürtök törlése](#delete-clusters)

## <a name="change-cluster-username"></a>Fürt felhasználónév módosítása
Egy HDInsight-fürt két felhasználói fiókot lehet. A HDInsight-fürt felhasználói fiókjának a létrehozási folyamat során jön létre. RDP-n keresztül a fürt eléréséhez egy RDP-felhasználói fiókot is létrehozhat. Lásd: [engedélyezése a távoli asztal](#connect-to-hdinsight-clusters-by-using-rdp).

**A HDInsight-fürthöz tartozó felhasználónevet és a jelszó módosítása**

1. Jelentkezzen be a [portál][azure-portal].
2. Kattintson a **összes tallózása** a bal oldali menüben kattintson a **HDInsight-fürtök**, a fürt nevére.
3. Kattintson a **beállítások** a felső menüben, és kattintson a **fürtbe való bejelentkezésekor**.
4. Ha **fürtbe való bejelentkezésekor** lett engedélyezve van, kattintson a **letiltása**, és kattintson a **engedélyezése** a felhasználónév és jelszó módosítása előtt...
5. Módosítsa a **fürt bejelentkezési neve** és/vagy a **fürt bejelentkezési jelszavának**, és kattintson a **mentése**.

    ![HDInsight fürt felhasználói felhasználónév-jelszó http-felhasználó módosítása](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)

## <a name="grantrevoke-access"></a>GRANT/revoke-access
HDInsight-fürtök a következő HTTP webes szolgáltatások (ezen szolgáltatások mindegyikéhez kell RESTful végpontokon) rendelkezik:

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton eszközön keresztül végzett

Alapértelmezés szerint ezek a szolgáltatások hozzáférés kapnak. Akkor is visszavonása/biztosítása a hozzáférést az Azure Portalról.

> [!NOTE]
> A hozzáférés biztosítása/visszavonása, visszaállítja, a fürthöz tartozó felhasználónevet és jelszót.
>
>

**Az engedélyezéshez/visszavonáshoz HTTP web services használatához**

1. Jelentkezzen be a [portál][azure-portal].
2. Kattintson a **összes tallózása** a bal oldali menüben kattintson a **HDInsight-fürtök**, a fürt nevére.
3. Kattintson a **beállítások** a felső menüben, és kattintson a **fürtbe való bejelentkezésekor**.
4. Ha **fürtbe való bejelentkezésekor** lett engedélyezve van, kattintson a **letiltása**, és kattintson a **engedélyezése** a felhasználónév és jelszó módosítása előtt...
5. A **fürt bejelentkezési felhasználóneve** és **fürt bejelentkezési jelszavának**, adja meg az új felhasználónevet és jelszót (megfelelő) a fürt számára.
6. Kattintson a **SAVE** (Mentés) gombra.

    ![HDInsight grand http webes szolgáltatás hozzáférés eltávolítása](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)

## <a name="find-the-default-storage-account"></a>Keresse meg az alapértelmezett tárfiókot
Minden egyes HDInsight-fürt rendelkezik egy alapértelmezett tárfiókot. Az alapértelmezett tárfiókot és annak kulcsait, fürt megjelenik **beállítások**/**tulajdonságok**/**Azure-Tárkulcsok**. Lásd: [fürtök listázása és megjelenítése](#list-and-show-clusters).

## <a name="find-the-resource-group"></a>Keresse meg az erőforráscsoport
Az Azure Resource Manager módban minden HDInsight-fürt az Azure-erőforráscsoportban jön létre. Az Azure-erőforráscsoportot, amely egy fürt tagja jelenik meg:

* A fürt listája tartalmaz egy **erőforráscsoport** oszlop.
* Fürt **alapvető** csempére.  

Lásd: [fürtök listázása és megjelenítése](#list-and-show-clusters).

## <a name="open-hdinsight-query-console"></a>Lekérdezés a HDInsight-konzol megnyitása
A HDInsight lekérdezéskonzol az alábbi szolgáltatásokat tartalmazza:

* **Hive-szerkesztő**: A grafikus felhasználói felület webes felület, a Hive-feladatok elküldése.  Lásd: [futtatása Hive-lekérdezések a Lekérdezéskonzol használata](hadoop/apache-hadoop-use-hive-query-console.md).

    ![HDInsight portál hive szerkesztőben](./media/hdinsight-administer-use-management-portal/hdinsight-hive-editor.png)
* **A feladatelőzmények**: Monitor Hadoop-feladatokat.  

    ![HDInsight portál feladatelőzmények](./media/hdinsight-administer-use-management-portal/hdinsight-job-history.png)

    Kattintson a **lekérdezésnév** feladat tulajdonságai, köztük részleteinek **Job Query**, és a ** Feladatkimenet. A lekérdezés mind a kimeneti is letöltheti a munkaállomáson.
* **Böngésző fájl**: keresse meg az alapértelmezett tárfiókot és a társított storage-fiókok.

    ![HDInsight portál fájlok böngésző tallózása](./media/hdinsight-administer-use-management-portal/hdinsight-file-browser.png)

    A képernyőképen a **<Account>** típus azt jelenti, a cikk egy Azure storage-fiókot.  Kattintson a fájlok tallózása a fiók nevére.
* **Hadoop-UI**.

    ![HDInsight portal Hadoop felhasználói felületén](./media/hdinsight-administer-use-management-portal/hdinsight-hadoop-ui.png)

    A **Hadoop felhasználói felület*, keresse meg a fájlokat, és ellenőrizze a naplókat.
* **A yarn felhasználói felületén**.

    ![HDInsight portál YARN felhasználói felületén](./media/hdinsight-administer-use-management-portal/hdinsight-yarn-ui.png)

## <a name="run-hive-queries"></a>Hive-lekérdezések futtatása
Hive-feladatok futtatásához a portálon kattintson a **Hive szerkesztő** a HDInsight lekérdezési konzolja. Lásd: [nyílt HDInsight lekérdezéskonzol](#open-hdinsight-query-console).

## <a name="monitor-jobs"></a>Feladatok figyelése
A portálon a feladatok monitorozásához kattintson **feladatelőzmények** a HDInsight lekérdezési konzolja. Lásd: [nyílt HDInsight lekérdezéskonzol](#open-hdinsight-query-console).

## <a name="browse-files"></a>Fájlok tallózása
Keresse meg az alapértelmezett tárfiókot és a társított storage-fiókok tárolja a fájlokat, kattintson a **Fájltallózó** a HDInsight lekérdezési konzolja. Lásd: [nyílt HDInsight lekérdezéskonzol](#open-hdinsight-query-console).

Is használhatja a **keresse meg a fájlrendszer** a segédprogram a **Hadoop felhasználói felület** a HDInsight-konzolon.  Lásd: [nyílt HDInsight lekérdezéskonzol](#open-hdinsight-query-console).

## <a name="monitor-cluster-usage"></a>Fürt használat monitorozása
A **használati** szakasz a HDInsight-fürt panelén, a HDInsight segítségével az előfizetéséhez elérhető magok száma, valamint az ehhez a fürthöz, és hogyan vannak lefoglalva számára lefoglalt magok száma információkat jelenít meg a csomópontok a fürtön belül. Lásd: [fürtök listázása és megjelenítése](#list-and-show-clusters).

> [!IMPORTANT]
> A HDInsight-fürt által nyújtott szolgáltatások figyeléséhez, Ambari Web vagy az Ambari REST API-t kell használnia. Az Ambari használatával kapcsolatos további információkért lásd: [kezelése a HDInsight-fürtök az Ambari használatával](hdinsight-hadoop-manage-ambari.md)
>
>

## <a name="open-hadoop-ui"></a>Nyissa meg a Hadoop felhasználói felület
A fürt monitorozására, keresse meg a fájlrendszer, és ellenőrizze a naplókat, kattintson az **Hadoop felhasználói felület** a HDInsight lekérdezési konzolja. Lásd: [nyílt HDInsight lekérdezéskonzol](#open-hdinsight-query-console).

## <a name="open-yarn-ui"></a>Nyissa meg a Yarn felhasználói felületén
A Yarn felhasználói felület használatához kattintson **Yarn felhasználói felületén** a HDInsight lekérdezési konzolja. Lásd: [nyílt HDInsight lekérdezéskonzol](#open-hdinsight-query-console).

## <a name="connect-to-clusters-using-rdp"></a>Csatlakozzon RDP-vel fürtökhöz
A fürt létrehozásakor a megadott hitelesítő adatai hozzáférést a szolgáltatásokhoz a fürtön, de nem maga a fürt távoli asztalon keresztül. Távoli asztali hozzáférés bekapcsolhatja a fürt üzembe helyezésekor vagy -fürt kiépítése után. A távoli asztal engedélyezése létrehozáskor kapcsolatos utasításokért lásd: [fürt létrehozása HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

**Távoli asztal engedélyezése**

1. Jelentkezzen be a [portál][azure-portal].
2. Kattintson a **összes tallózása** a bal oldali menüben kattintson a **HDInsight-fürtök**, a fürt nevére.
3. Kattintson a **beállítások** a felső menüben, és kattintson a **a távoli asztal**.
4. Adja meg **lejárati dátuma**, **távoli asztali felhasználónév** és **távoli asztal jelszava**, és kattintson a **engedélyezése**.

    ![HDInsight engedélyezése letiltása a távoli asztal konfigurálása](./media/hdinsight-administer-use-management-portal/hdinsight.portal.remote.desktop.png)

    Az alapértelmezett értékeit lejár a hetente.

   > [!NOTE]
   > A HDInsight .NET SDK használatával a távoli asztal engedélyezése egy fürtön. Használja a **EnableRdp** metódus a HDInsight-ügyfél objektum a következő módon: **ügyfél. EnableRdp (fürtnév, hely, a "rdpuser", "rdppassword" DateTime.Now.AddDays(6))**. Hasonlóképpen, a távoli asztal letiltása a fürtön, használhatja **ügyfél. (Clustername, tartózkodási hely) DisableRdp**. E módszerekkel kapcsolatos további információkért lásd: [HDInsight .NET SDK dokumentációja](http://go.microsoft.com/fwlink/?LinkId=529017). Ez akkor csak a HDInsight-fürtök a Windows rendszerű.
   >
   >

**RDP használatával csatlakozni a fürthöz**

1. Jelentkezzen be a [portál][azure-portal].
2. Kattintson a **összes tallózása** a bal oldali menüben kattintson a **HDInsight-fürtök**, a fürt nevére.
3. Kattintson a **beállítások** a felső menüben, és kattintson a **a távoli asztal**.
4. Kattintson a **Connect** , és kövesse az utasításokat. Ha a csatlakozás le lesz tiltva, engedélyeznie kell, először. Győződjön meg arról, hogy a távoli asztali felhasználói felhasználónévvel és jelszóval.  A fürt felhasználói hitelesítő adatok nem használhatók.

## <a name="open-hadoop-command-line"></a>Nyissa meg a Hadoop parancssor
Távoli asztal használatával csatlakozhat a fürthöz, és a Hadoop parancssor használata, kell először engedélyezte a fürthöz a távoli asztali hozzáférés az előző szakaszban leírtak szerint.

**A Hadoop parancssor megnyitása**

1. Csatlakozás a fürthöz, a távoli asztal használatával.
2. Kattintson duplán az asztalon **Hadoop parancssor**.

    ![HDI. HadoopCommandLine][image-hadoopcommandline]

    További információk a Hadoop-parancsokat: [Hadoop parancsok referencia](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html).

Az előző képernyőképen a mappa nevét a Hadoop-verziószám beágyazott rendelkezik. A verziószámot a fürtön telepíteni a Hadoop-összetevők verziója alapján módosíthatja. Hadoop környezeti változók használatával tekintse meg a mappákra. Példa:

    cd %hadoop_home%
    cd %hive_home%
    cd %hbase_home%
    cd %pig_home%
    cd %sqoop_home%
    cd %hcatalog_home%

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulhatta, hogyan hozhat létre egy HDInsight-fürtöt a portálon, és hogyan nyithat meg a Hadoop parancssori eszköz. További tudnivalókért tekintse meg a következő cikkeket:

* [Az Azure PowerShell használatával HDInsight felügyelete](hdinsight-administer-use-powershell.md)
* [A HDInsight az Azure CLI használatával felügyelheti](hdinsight-administer-use-command-line.md)
* [HDInsight-fürtök létrehozása](hdinsight-hadoop-provision-linux-clusters.md)
* [Programozott módon a Hadoop-feladatok elküldése](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Az Azure HDInsight – első lépések](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Az Azure HDInsight Hadoop verziójának van?](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-management-portal/hdinsight-hadoop-command-line.png "Hadoop parancssor"
