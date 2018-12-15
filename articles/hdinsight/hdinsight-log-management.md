---
title: Egy HDInsight-fürt – Azure HDInsight naplóinak kezelése
description: A típusok, méretek és a HDInsight-tevékenység naplófájlokon adatmegőrzési házirendek meghatározása.
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/11/2018
ms.author: ashishth
ms.openlocfilehash: 9a76ad219e538874af04a72c9aa64e87a35bc53d
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53434885"
---
# <a name="manage-logs-for-an-hdinsight-cluster"></a>HDInsight-fürt naplóinak kezelése

Egy HDInsight-fürtöt hoz létre különböző naplófájlokat. Például az Apache Hadoop és a kapcsolódó szolgáltatások, például az Apache Spark, termék részletes feladatvégrehajtási naplók. Naplófájl kezelése fenntartása a kifogástalan állapotú HDInsight-fürt részét képezi. Emellett szabályozási követelmények log archiválásra lehet.  Száma és a naplófájlok méretét, mert a optimalizálása a naplók tárolásához, és segít az archiválás cost management szolgáltatás.

HDInsight-fürt naplóinak kezelése magában foglalja a fürt környezet vonatkozó biztonságáért adatainak megőrzése. Ezen információk közé tartozik minden társított Azure-szolgáltatás naplóit, fürtkonfiguráció, feladat-végrehajtási információt, bármely hibaállapotok és egyéb adatok, igény szerint.

HDInsight kezelése az általános lépések a következők:

* 1. lépés: Napló adatmegőrzési szabályzatok meghatározása
* 2. lépés: Fürt szolgáltatás verziók konfigurációs naplók kezelése
* 3. lépés: Fürt feladat-végrehajtási naplófájlok kezelése
* 4. lépés: Log storage kötetméretet, és a költségek
* 5. lépés: Napló archív szabályzatoknak és folyamatoknak meghatározása

## <a name="step-1-determine-log-retention-policies"></a>1. lépés: Napló adatmegőrzési szabályzatok meghatározása

A HDInsight cluster log management stratégia létrehozásának első lépése az üzleti helyzetek és feladat-végrehajtási előzmények tárhelykövetelmények információt gyűjteni.

### <a name="cluster-details"></a>Fürt részletes adatai

A következő fürt részletes adatai hasznosak lehetnek abban, hogy a napló-kezelési stratégia adatainak összegyűjtése. Az információk gyűjtésére az összes HDInsight-fürtről, létrehozott egy adott Azure-fiókkal.

* Fürt neve
* Fürt régió és az Azure rendelkezésre állási zónában
* Fürt állapota, beleértve az utolsó állapotváltozás részleteit
* A master, a core és a feladat csomópontok megadott HDInsight-példányok száma és típusa

A legtöbb a legfelső szintű adatokat az Azure portal használatával kérheti le.  A klasszikus Azure CLI segítségével azt is megteheti, információ jelenik meg a HDInsight-fürt:

```
    azure hdinsight cluster list
    azure hdinsight cluster show <ClusterName>
```
[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

PowerShell használatával ezt az információt.  További információkért lásd: [Apache kezelése Hadoop-fürtök HDInsight az Azure PowerShell-lel](hdinsight-administer-use-powershell.md).

### <a name="understand-the-workloads-running-on-your-clusters"></a>A fürtökön futó számítási feladatok ismertetése

Fontos tudni, hogy a megfelelő naplózás az egyes stratégiák kialakíthatja a HDInsight-fürt futó számítási feladatok típusa.

* A számítási feladatok azok a kísérleti (például fejlesztési vagy tesztelési) vagy a gyártási minőségű?
* Milyen gyakran a gyártási minőségű számítási feladatok szokásos módon működnek?
* Azok a számítási feladatok bármelyikét, erőforrás-igényes és/vagy hosszú ideig futó?
* Használnak, a számítási feladatok bármelyikét álló összetett készlettel, amelynek többféle naplókat hoz létre Hadoop-szolgáltatásokhoz?
* Hajtsa végre a számítási feladatok bármelyikét társított szabályozási végrehajtási leszármaztatási követelmények?

### <a name="example-log-retention-patterns-and-practices"></a>Példa log megőrzési minták és gyakorlatok

* Fontolja meg a követési azonosító hozzáadásával, minden naplóbejegyzés vagy egyéb technikák keresztül adatok történetének karbantartása. Ez lehetővé teszi, hogy nyomon követését a művelet és az adatok az eredeti forrásra, és hajtsa végre a konzisztencia és érvényességi minden egyes szakaszhoz az adatokat.

* Fontolja meg, hogyan naplók összegyűjtése a fürtről, vagy egynél több fürtből, és összeveti őket, például a naplózás, figyelés, tervezési és riasztások. Használhat egyéni megoldásra való eléréséhez és töltse le a naplófájlok rendszeres időközönként, és egyesítése és elemzése egy irányítópulton megjelenített szerződtünk velük. Biztonság és a hibaészlelés riasztási további képességeket is hozzáadhat. Ezek a segédprogramok, PowerShell, a HDInsight SDK-k vagy a kódot, amely hozzáfér a klasszikus Azure üzemi modell használatával hozhat létre.

* Érdemes lehet-e egy figyelési megoldást vagy szolgáltatás egyik hasznos előnyt lenne. A Microsoft System Center biztosít egy [HDInsight felügyeleti csomag](https://www.microsoft.com/download/details.aspx?id=42521). Például az Apache Chukwa és Ganglia külső eszközök használatával gyűjtése, centralizálható a naplók. Számos vállalat ajánlat szolgáltatásokat és a Hadoop-alapú big data-megoldások, például figyelés: Centerity, Compuware APM, Sematext SPM-hez és az Orchestrator Zettaset.

## <a name="step-2-manage-cluster-service-versions-and-view-script-action-logs"></a>2. lépés: Fürtverziók szolgáltatás kezelése, és a Script Action naplók megtekintése

Egy tipikus HDInsight-fürtöt használ, több szolgáltatást és a nyílt forráskódú szoftvercsomagokat (például az Apache HBase, Apache Spark és így tovább). Bizonyos számítási feladatokhoz, például a Bioinformatika a szükséges megőrizni a szolgáltatás a korábbi naplók konfigurálása a feladatvégrehajtási naplók mellett.

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Az Ambari felhasználói felületén, a nézet fürtbeállítások

Az Apache Ambari leegyszerűsíti a felügyeleti, beállítást és megfigyelést a HDInsight-fürt azáltal, hogy egy webes felhasználói felület és a egy REST API-t. A Linux-alapú HDInsight-fürtök az Ambari tartalmazza. Válassza ki a **fürt irányítópultja** panel az Azure Portalon HDInsight lap megnyitásához a **"fürt irányítópultjai** hivatkozási lapja.  Ezután válassza ki a **HDInsight-fürt irányítópultja** ablaktáblán nyissa meg az Ambari felhasználói felületén.  A fürt bejelentkezési hitelesítő adatok megadását kéri.

Szolgáltatás nézetek listájának megnyitásához válassza a **Ambari-nézetek** for HDInsight az Azure portal oldalán található panelen.  Ez a lista függ attól függően, hogy mely könyvtárak telepítette.  Láthatja például, az üzenetsor-kezelő YARN, Hive-nézet és a Tez nézet.  Kattintson bármely szolgáltatás konfigurációs és szolgáltatási adatokat.  Az Ambari felhasználói felületén **verem és verzió** oldal nyújt információkat a fürtszolgáltatások konfigurációs és szolgáltatási korábbi verziók. Az ebben a szakaszban az Ambari felhasználói felületén, válassza a **rendszergazdai** menüben, majd **platformok és verziók**.  Válassza ki a **verziók** lapján megtekintheti a fájlverzió-információkat.

![Verem és -verziók](./media/hdinsight-log-management/stack-versions.png)

Az Ambari felhasználói felületén, a konfiguráció egy adott gazdagép (vagy csomópont) a fürtben futó bármely (vagy az összes) szolgáltatások töltheti le.  Válassza ki a **gazdagépek** menüre, majd a gazdagép a lényeges mutató hivatkozás. Az adott gazdagép oldalán válassza ki a **gazdagép műveletek** gombra, majd **letöltése ügyfél Configs**. 

![Gazdagép ügyfél configs](./media/hdinsight-log-management/client-configs.png)

### <a name="view-the-script-action-logs"></a>A parancsfájl műveleti naplók megtekintése

HDInsight [szkriptműveletek](hdinsight-hadoop-customize-cluster-linux.md) egy fürtön, vagy manuálisan, vagy ha a megadott szkriptek futtatása. Használható például az parancsfájlműveletekkel további szoftverek telepíthetők a fürtön, vagy megváltoztathatja a konfigurációs beállítások alapértelmezett értékét. Parancsfájl műveleti naplók segítségével betekintést a fürt beállítása során fellépő hibák, valamint a konfigurációs beállítások módosításait, amelyek hatással lehetnek a fürt teljesítmény és rendelkezésre állás.  Egy parancsprogram-művelet állapotának megtekintéséhez válassza ki a **ops** gomb az Ambari felhasználói felületén vagy a hozzáférési állapota az alapértelmezett tárfiók bejelentkezik. A storage-naplók esetén érhető el `/STORAGE_ACCOUNT_NAME/DEFAULT_CONTAINER_NAME/custom-scriptaction-logs/CLUSTER_NAME/DATE`.

## <a name="step-3-manage-the-cluster-job-execution-log-files"></a>3. lépés: A fürt feladat végrehajtási naplófájlok kezelése

A következő lépés a feladat végrehajtási naplófájlokat a különböző szolgáltatásoknál van áttekintése.  Sikerült szolgáltatások közé tartoznak az Apache HBase, Apache Spark és sok más. Egy Hadoop-fürtöt hoz létre részletes naplók, nagy számú, amely meghatározza, hogy mely naplók hasznosak (és nem) időigényes lehet.  A naplózás rendszer megértése fontos a megcélzott felügyeleti naplófájlok.  Az alábbiakban látható egy példa naplófájl.

![HDInsight log fájl például](./media/hdinsight-troubleshoot-failed-cluster/logs.png)

### <a name="access-the-hadoop-log-files"></a>Hadoop naplófájljait

HDInsight tárolja a naplófájlokat, a fürt fájlrendszerben és az Azure storage-ban is. Nyissa meg a fürthöz az SSH-kapcsolatot, és a fájlrendszer-böngészés, vagy a fő csomópontot távoli kiszolgálón a Hadoop YARN állapotát a portál használatával ellenőrizheti a naplófájlokat a fürtben. A naplófájlok, az Azure BLOB storage, az eszközöket, amelyek hozzáférhetnek, és töltse le az adatokat az Azure storage-ból bármelyikével ellenőrizheti. Példák az AZCopy CloudXplorer és a Visual Studio Server Explorerben. A PowerShell és az Azure Storage ügyfélkódtáraival vagy az Azure .NET SDK-k használatával is az Azure blob storage-adatok eléréséhez.

Hadoop fut, a feladatok munkájának *kísérletek feladat* a különböző csomópontokhoz a fürtben. HDInsight kezdeményezhet spekulatív feladat kísérletek, bármely más feladat, kísérletek nem fejeződnek be először lezárja. Ez létrehoz, a rendszer naplózza a vezérlőket, stderr és syslog log fájl a működés közbeni jelentős tevékenység. Emellett tett kísérletet feladat fut egyidejűleg, de egy naplófájlt is csak eredmények megjelenítéséhez lineárisan.

#### <a name="hdinsight-logs-written-to-azure-blob-storage"></a>Az Azure Blob storage-írt HDInsight-naplók

HDInsight-fürtök úgy vannak konfigurálva, feladat naplók írni egy Azure Blob storage-fiókot az összes feladat elküldésekor az Azure PowerShell-parancsmagok vagy a .NET-feladat küldése API-k használatával.  Ha elküldött feladatokat a fürt ssh-n keresztül, majd a végrehajtási adatok naplózása tárolva van az Azure-beli táblák az előző szakaszban leírt módon.

Mellett a core-naplófájlok HDInsight által előállított telepített szolgáltatásokat, mint például a YARN a feladat-végrehajtási naplófájlokat is létrehozhat.  A telepített szolgáltatások száma és típusa, a naplófájlok függ.  Közös szolgáltatások olyan Apache HBase, Apache Spark és így tovább.  Vizsgálja meg a feladat végrehajtási naplófájlokat az egyes szolgáltatások megértéséhez, az általános naplózási fájlokat a fürtön.  Mindegyiknek megvannak a naplófájlok tárolására szolgáló naplózási és helyét, valamint a saját egyedi módszereket.  Tegyük fel részletei a leggyakrabban használt szolgáltatás naplófájlokat (YARN) eléréséhez a következő szakasz tárgyalja.

### <a name="hdinsight-logs-generated-by-yarn"></a>YARN által létrehozott HDInsight-naplók

YARN naplók összesíti az összes tárolót a munkavégző csomópont között, és egy összesített naplófájl száma feldolgozó csomópontonként ezeket a naplókat tárolja. Egy alkalmazás befejezése után el az alapértelmezett fájlrendszer tárolják. Az alkalmazás felhasználhatja, több száz vagy akár több ezer olyan tárolók, de egy egyetlen munkavégző csomóponton futó összes tároló naplóinak mindig egyetlen fájlban vannak összesítve. Az alkalmazás által használt feldolgozó csomópontonkénti csak egyetlen naplófájl van. A HDInsight fürtök 3.0-s verzió vagy újabb alapértelmezés szerint engedélyezve van a naplózási összesítési. Összesített naplókat a fürt alapértelmezett tárolója mappában találhatók.

```
    /app-logs/<user>/logs/<applicationId>
```

Az összesített naplókat, az oktatóprogram egy tároló által indexelt TFile bináris formátum nem állnak közvetlenül olvasható. A YARN ResourceManager naplók vagy a parancssori eszközök használatával az alkalmazások vagy a lényeges tárolók egyszerű szövegként ezek a naplók megtekintéséhez.

#### <a name="yarn-cli-tools"></a>YARN parancssori felületi eszközök

YARN parancssori felületi eszközök használata, először csatlakoznia kell a HDInsight-fürthöz SSH használatával. Adja meg a `<applicationId>`, `<user-who-started-the-application>`, `<containerId>`, és `<worker-node-address>` információt a parancsok futtatása során. Az alábbi parancsok egyikével egyszerű szövegként a naplókban tekintheti meg:

```bash
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
```

#### <a name="yarn-resourcemanager-ui"></a>YARN ResourceManager felhasználói felülete

A YARN ResourceManager felhasználói felülete a fürt fő csomópontjának fut, és az Ambari webes felhasználói felületen keresztül érhető el. Használja az alábbi lépéseket a YARN-naplók megtekintéséhez:

1. A böngészőben navigáljon a `https://CLUSTERNAME.azurehdinsight.net`. CLUSTERNAME cserélje le a HDInsight-fürt nevére.
2. A bal oldali szolgáltatások listájából válassza ki a YARN.
3. A Gyorshivatkozások legördülő listából válassza ki a fürt fő csomópontjának egyikét, és válassza ki **ResourceManager naplók**. YARN-naplókat mutató hivatkozások listája jelenik meg.

## <a name="step-4-forecast-log-volume-storage-sizes-and-costs"></a>4. lépés: Log storage kötetméretet, és a költségek

Az előző lépések végrehajtását követően rendelkezik a típusok megismerése és a naplófájlok, amely a HDInsight-fürt olyan köteteket.

Ezután elemezze a naplóadatok a kulcs a naplók tárolási helye egy időszakon belül. Például elemezheti a kötet és a növekedés több mint 30 – 60 – 90 nap.  Jegyezze fel ezeket az adatokat egy táblázat celláira, vagy használjon más eszközökkel, például a Visual Studio, az Azure Storage Explorer vagy a Power Query az Excel programhoz. További információkért lásd: [elemzése HDInsight-naplók](hdinsight-debug-jobs.md).  

Most már rendelkezik egy log management stratégia a fő naplók elegendő információt.  Használja a táblázatban (vagy tetszőleges eszköz) előrejelzése mindkét naplófájl méretének növekedése, és a naplózási tároló az Azure szolgáltatási költségei teszik a jövőben.  Fontolja meg is napló megőrzési vonatkozó követelmények naplóival, amelyeket a rendszer vizsgálata készletét.  Most már Ön is reforecast későbbi napló a tárolási költségek, meghatározása után (ha vannak) törölheti mely naplófájlokat, és mely naplók kell megőrzött és archivált kevesebb költséggel jár az Azure Storage.

## <a name="step-5-determine-log-archive-policies-and-processes"></a>5. lépés: Napló archív szabályzatoknak és folyamatoknak meghatározása

Után azt állapítja meg, hogy mely naplófájlokat törlése, módosíthatja a naplófájlok automatikusan törlése után a megadott időtartam során számos Hadoop szolgáltatás a naplózási paraméterek.

Az egyes naplófájlok megközelítés archiválás alacsonyabb árú naplófájl is használhatja. Azure Resource Manager-tevékenységi naplóit megismerheti ezt a megközelítést az Azure portal használatával.  Állítsa be az ARM-naplói archiválása kiválasztásával a **tevékenységnapló**"hivatkozást a HDInsight-példány az Azure Portalon.  A tevékenységnapló-keresési lap felső részén válassza ki a **exportálása** menüelem megnyitásához a **tevékenységnapló exportálása** ablaktáblán.  Töltse ki az előfizetést, régió, e tárfiókba exportálja és hány napig megőrzi a naplókat. A azonos panelen is jelezheti, hogy egy eseményközpontba való exportálása. 

![Naplófájlok exportálása](./media/hdinsight-log-management/archive.png)

Másik megoldásként is parancsfájl, log archiválása a PowerShell használatával.  PowerShell-szkript egy példa: [az Azure Blob Storage-naplók archiválása az Azure Automation](https://gallery.technet.microsoft.com/scriptcenter/Archive-Azure-Automation-898a1aa8).

### <a name="accessing-azure-storage-metrics"></a>Az Azure storage-mérőszámok elérése

Az Azure storage beállítható úgy, hogy a naplófájl tárolási műveletek és a hozzáférés. Használhatja a nagyon részletes naplók kapacitás figyelési és tervezési, valamint a naplózási storage felé. A naplózott információk késés részletes, lehetővé téve a figyelése és finomhangolása a megoldások teljesítményét is tartalmazza.
A .NET SDK a Hadoophoz használatával vizsgálja meg az Azure storage, amely tárolja az adatokat egy HDInsight-fürt számára létrehozott naplófájlokat.

### <a name="control-the-size-and-number-of-backup-indexes-for-old-log-files"></a>A régi naplófájlokat biztonsági mentési indexek száma és mérete

Megőrzött fájlok száma és mérete szabályozására, a következő tulajdonságainak megadása a `RollingFileAppender`:

* `maxFileSize` a kritikus mérete a fájl, amely fölött a fájl lesz állítva. Az alapértelmezett értéke 10 MB-ot.
* `maxBackupIndex` a biztonságimásolat-fájlok létrehozása, 1 alapértelmezett számát adja meg.

### <a name="other-log-management-techniques"></a>Egyéb log management technikák

Nincs elég szabad lemezterület futó elkerülése érdekében bizonyos operációs rendszer eszközöket használhat például `logrotate` kezelését a naplófájlok kezelésére. Konfigurálható `logrotate` futtatni naponta, tömörítés naplófájl fájlokat és eltávolítani a régieket. A módszer a követelményeitől függ, például mennyi ideig tartani a logfiles helyi csomóponton. 

A hibakeresési naplózás engedélyezve van-e az egy vagy több szolgáltatást, ami jelentősen növeli a kimeneti napló mérete is ellenőrizheti. 

A naplók gyűjtését összes csomópontja egyetlen központi helyen, létrehozhat egy adatfolyamot, például az összes naplóbejegyzés, Solr fürtjét.

## <a name="next-steps"></a>További lépések

* [Figyelés és naplózás HDInsight eljárás](https://msdn.microsoft.com/library/dn749790.aspx)
* [Hozzáférés az Apache Hadoop YARN napló a Linux-alapú HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Különböző Apache Hadoop-összetevők a naplófájlok méretét, vezérlése](https://community.hortonworks.com/articles/8882/how-to-control-size-of-log-files-for-various-hdp-c.html)
