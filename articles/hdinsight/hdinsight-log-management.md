---
title: "A HDInsight-fürtök - Azure HDInsight naplóinak kezeléséhez |} Microsoft Docs"
description: "A típusokat, méretek és HDInsight tevékenység naplófájlok adatmegőrzési határozza meg."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: ashishth
ms.openlocfilehash: a161a5c639ff02e1e8a2ea987d9f913ff41c5618
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2018
---
# <a name="manage-logs-for-an-hdinsight-cluster"></a>A HDInsight-fürtök kezelése

HDInsight-fürtök különböző naplófájlokat hoz létre. Például az Apache Hadoop és a kapcsolódó szolgáltatások, például az Apache Spark on az részletes feladatvégrehajtási naplók eredményez. Naplófájl kezelése karbantartásának megfelelő HDInsight-fürt része. Is napló archiválásra szabályozási követelmények lehet.  A szám és a naplófájlok méretét, miatt optimalizálása a naplók tárolásához, és segítséget nyújt az archiválás szolgáltatásfelügyelet költség.

HDInsight-fürt naplók kezelése, megtartja a fürtözött környezet szempontjaival kapcsolatos adatokat tartalmazza. Ezen információk közé tartozik minden társított Azure Service naplóit, fürtkonfiguráció, feladat-végrehajtási információt, bármilyen hiba állapotok és egyéb adatokat szükség szerint.

HDInsight napló felügyeleti jellemzően előforduló lépéseket a következők:

* : 1. lépés adatmegőrzési naplózása
* 2. lépés: A fürt szolgáltatás verziók konfigurációs naplóinak kezeléséhez
* 3. lépés: A fürt feladat végrehajtási naplófájlok kezelése
* 4. lépés: Az előrejelzési napló tároló mérete és költségek
* 5. lépés: A napló archív szabályzatoknak és folyamatoknak meghatározása

## <a name="step-1-determine-log-retention-policies"></a>: 1. lépés adatmegőrzési naplózása

A HDInsight fürt napló felügyeleti stratégia létrehozásának első lépése, hogy üzleti forgatókönyvek és a feladat végrehajtási előzményei tárolási követelményeinek információt gyűjteni.

### <a name="cluster-details"></a>A fürt részleteinek

A következő fürt adatok hasznosak útmutatás nyújtása a összegyűjteni a napló-kezelési stratégiában. Ezek az információk összegyűjtése során létrehozott egy adott Azure-fiókot az összes HDInsight-fürtök.

* Fürt neve
* Fürt régió zónát és az Azure rendelkezésre állása
* Fürt állapota, beleértve az utolsó állapotváltozás részleteit
* A master, a core és a feladat csomópontok HDInsight-példányok száma és típusa

A legtöbb a legfelső szintű adatokat az Azure portál használatával kérheti le.  Az Azure CLI segítségével azt is megteheti, a HDInsight-fürtöt vagy fürtöket kapcsolatos információk:

```
    azure hdinsight cluster list
    azure hdinsight cluster show <ClusterName>
```

PowerShell segítségével is megtekintheti.  További információkért lásd: [Azure PowerShell használatával a HDInsight fürtök kezelése Hadoop](hdinsight-administer-use-powershell.md).

### <a name="understand-the-workloads-running-on-your-clusters"></a>A fürtökön futó számítási feladatok ismertetése

Fontos ismerni a munkaterhelés megfelelő naplózás az egyes stratégiák kialakíthatja a HDInsight-fürtöt vagy fürtöket futó.

* A munkaterhelések vannak a kísérleti (például a fejlesztési és tesztelési) vagy a kiváló minőségben?
* Milyen gyakran minőségű munkaterhelés normál módon működnek?
* Erőforrás-igényes és/vagy hosszú futású, amelyek a munkaterhelések bármelyikét?
* Használnak, a munkaterhelések bármelyikét álló összetett készlettel, amelynek a naplói többféle típusú előállított Hadoop-szolgáltatás?
* Hajtsa végre a munkaterhelések bármelyikét társított szabályozó végrehajtási Leszármaztatás követelmények?

### <a name="example-log-retention-patterns-and-practices"></a>Példa napló megőrzési minták és gyakorlatok

* Fontolja meg a követési azonosító hozzáadásával, minden naplóbejegyzés vagy egyéb technikák keresztül adatokat Leszármaztatás karbantartása. Ez lehetővé teszi, hogy vissza az adatokat, majd a művelet az eredeti forrásának követése, és hajtsa végre az adatok minden szakaszban megtudhatja, hogy a konzisztencia és érvényességi.

* Vegye figyelembe, hogyan gyűjtését a fürtből, vagy egynél több fürtből, és összeveti őket például a naplózás, a figyelés, a tervezési és a riasztás célokra. Egyéni megoldás eléréséhez és a naplófájlok rendszeresen, töltse le és kombinálhatja és elemzése azokat egy irányítópulton megjelenített használhatja. A tárhelyhiba-észlelés és a biztonsági riasztások további képességeket is hozzáadhat. Ezek a segédprogramok a PowerShell, a HDInsight SDK-k vagy az Azure klasszikus üzembe helyezési modellel hozzáférő kódot hozhat létre.

* Vegye figyelembe, hogy egy figyelési megoldást vagy szolgáltatás hasznos előnyt lenne. A Microsoft System Center biztosít egy [HDInsight a felügyeleti csomag](https://www.microsoft.com/download/details.aspx?id=42521). Külső eszközöket, például a Chukwa és Ganglia segítségével is gyűjthet, és központosíthatják a naplókat. Számos vállalat a figyelheti a Hadoop-alapú big data-megoldások, például Centerity, Compuware APM Sematext SPM és Zettaset az Orchestrator szolgáltatást kínál.

## <a name="step-2-manage-cluster-service-versions-and-view-script-action-logs"></a>2. lépés: Kezelheti a fürt verziót, és a parancsfájlművelet naplók megtekintése

Egy tipikus HDInsight-fürtöt használ, több szolgáltatások és nyílt forráskódú szoftvercsomagok (például az Apache HBase, Apache Spark, és így tovább). Egyes munkaterhelések, például a Bioinformatika a szükséges előzményeinek szolgáltatás konfigurációs napló feladatvégrehajtási naplók mellett.

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Az Ambari felhasználói felületén, a nézet fürtbeállítások

Apache Ambari egyszerűbbé, beállítást és megfigyelést a HDInsight-fürt, adja meg a webes felhasználói felület és a REST API-t. Ambari része a Linux-alapú HDInsight-fürtökön. Válassza ki a **fürt irányítópult** ablak megnyitása az Azure portál HDInsight lapján a**"fürt irányítópultok** hivatkozás lapon.  Ezután válassza ki a **HDInsight fürt irányítópult** ablaktáblán az Ambari felhasználói felületének megnyitásához.  A fürt bejelentkezési hitelesítő adatok megadását kéri.

Szolgáltatás nézetek listájának megnyitásához, jelölje be a **Ambari nézetek** ablaktábla a HDInsight az Azure portál lapján.  Ez a lista változik, attól függően, hogy mely könyvtárak telepítését.  Megjelenhet például a YARN várólista-kezelő, a nézet Hive és a Tez nézet.  Válassza ki a bármely szolgáltatás hivatkozásra kattintva megtekintheti a konfiguráció és a szolgáltatás adatait.  Az Ambari felhasználói felület **verem és verzió** lap tájékoztatást ad azokról a fürtszolgáltatások konfigurációs és a szolgáltatás verziójának előzményei. Keresse meg ebben a szakaszban az Ambari felhasználói felület, válassza ki a **Admin** menüben, majd **verem és verziók**.  Válassza ki a **verziók** lapon, láthatja a fájlverzió-információkat.

![Verem és verziói](./media/hdinsight-log-management/stack-versions.png)

Az Ambari felületen bármely (vagy az összes) szolgáltatás egy adott gazdagép (vagy csomópont) a fürt konfigurációjának töltheti le.  Válassza ki a **állomások** menüre, majd az állomás egyik fontos mutató hivatkozást. Az adott gazdagépen lapon válassza ki a **állomás műveletek** gombra, majd **letöltése ügyfél Configs**. 

![Állomás ügyfél konfigurációk](./media/hdinsight-log-management/client-configs.png)

### <a name="view-the-script-action-logs"></a>A parancsfájl műveleti naplói megtekintése

HDInsight [parancsfájl-műveletek](hdinsight-hadoop-customize-cluster-linux.md) parancsfájlok futtathatók a fürt, vagy manuálisan, vagy ha a megadott. A Parancsfájlműveletek például használható további szoftvereket telepíteni a fürt vagy az alapértelmezett értékek konfigurációs beállításainak módosításához. Parancsfájl műveleti naplói biztosíthat a fürt a telepítés során előforduló, valamint a konfigurációs beállítások módosításait tartalmazza, amelyek befolyásolhatják a fürt teljesítmény és rendelkezésre állás betekintést.  A parancsfájlművelet állapotának megtekintéséhez válassza ki a **ops** gomb a Ambari felhasználói felületén vagy a hozzáférési állapota jelentkezik be az alapértelmezett tárfiók. A tárolási naplófájljai érhetők el `/STORAGE_ACCOUNT_NAME/DEFAULT_CONTAINER_NAME/custom-scriptaction-logs/CLUSTER_NAME/DATE`.

## <a name="step-3-manage-the-cluster-job-execution-log-files"></a>3. lépés: A fürt feladat végrehajtási naplófájlok kezelése

A következő lépés a feladat végrehajtása a különböző szolgáltatásokhoz a naplófájlok van áttekintése.  Szolgáltatások tartalmazhatnak Apache HBase, Apache Spark és sok más. Hadoop-fürthöz nagyszámú részletes naplókat, hoz létre, ezért hasznosak, mely naplók (és nem) időigényes.  A naplózási rendszer megértése fontos naplófájlok célirányos felügyeleti.  A következő példában a naplófájl látható.

![HDInsight log fájl példa](./media/hdinsight-troubleshoot-failed-cluster/logs.png)

### <a name="access-the-hadoop-log-files"></a>Hozzáférés a Hadoop-naplófájlok

HDInsight tárolja a naplófájlokat, a fürt fájlrendszer, és az Azure-tárfiókba. Naplófájlok a fürt ellenőrzéséhez nyissa meg a fürthöz az SSH-kapcsolatot, és keresse meg a fájlrendszer vagy a távoli átjárócsomópont kiszolgálón a Hadoop YARN állapot portál használatával ellenőrizheti. Azure storage-ban az eszközök, amelyek férhet hozzá és töltse le az adatokat az Azure storage a naplófájlokban ellenőrizheti. Többek között az AZCopy, CloudXplorer és a Visual Studio Server Explorer. PowerShell és az Azure Storage ügyfélkódtáraival vagy az Azure .NET SDK-k, használhatja az Azure blob Storage tárolóban lévő adatok eléréséhez.

Hadoop futtatja a munkát, mint a feladatok *kísérletek feladat* a fürt különböző csomópontján. HDInsight kezdeményezhet spekulatív feladat kísérletek bármely más tevékenység kísérlet, amely nem hajtható végre, először leáll. Ezt követően a rendszer naplózza a vezérlő stderr és syslog napló fájlok az azonnali jelentős tevékenység. Ezenkívül többszöri feladat fut egyidejűleg, de egy naplófájlt is csak eredmények megjelenítéséhez lineárisan.

#### <a name="hdinsight-logs-written-to-azure-tables"></a>Azure-táblákban írni a HDInsight-naplók

A naplók írni az Azure-táblákban betekintést, mi történik egy HDInsight-fürthöz. Amikor egy Linux-alapú HDInsight-fürtöt hoz létre, hat táblák automatikusan létrejönnek az alapértelmezett Table storage-ban:

* hdinsightagentlog
* syslog
* daemonlog
* hadoopservicelog
* ambariserverlog
* ambariagentlog

#### <a name="hdinsight-logs-written-to-azure-blob-storage"></a>HDInsight naplók az Azure Blob storage írása

A HDInsight-fürtök feladat naplók írni egy Azure Blob storage-fiókot az Azure PowerShell-parancsmagok vagy a .NET-feladat elküldése API-k segítségével elküldött feladathoz szükséges vannak konfigurálva.  Ha SSH-n keresztül feladatok a fürtnek, majd a végrehajtási adatok naplózása a Azure táblákban tárolódnak az előző szakaszban leírtaknak megfelelően.

Mellett a core-naplófájlok HDInsight által előállított szolgáltatások telepítése, mint például YARN generálhat feladat végrehajtási naplófájlokat.  Számát és típusát, a naplófájlok a telepített szolgáltatás függ.  Közös szolgáltatások az Apache HBase, Apache Spark és így tovább.  Vizsgálja meg a feladat végrehajtási naplófájlok megérteni a teljes naplózási fájlok a fürt minden egyes szolgáltatás.  Minden szolgáltatás naplófájlok tárolására szolgáló naplózási és a helyek saját egyedi metódusokkal rendelkezik.  Tegyük fel elérésekor a legáltalánosabb szolgáltatás naplófájlokat (YARN) részletes ismertetése a következő szakaszban.

### <a name="hdinsight-logs-generated-by-yarn"></a>YARN által létrehozott HDInsight-naplók

YARN naplók von össze az összes tároló, a munkavégző csomópont, és egy összesített naplófájlhoz munkavégző csomópont lesznek a naplók tárolja. Egy alkalmazás befejeződése után el az alapértelmezett fájlrendszert tárolja. Az alkalmazás használhat több száz vagy ezer tárolók, de az egyetlen munkavégző csomóponton futó összes tárolókat naplók mindig összesítése egy fájlba. Egyes munkavégző csomópontok az alkalmazás által használt csak egyetlen naplófájl van. Napló összesítési HDInsight fürtök 3.0-s verzió vagy újabb rendszeren alapértelmezés szerint engedélyezve van. Összesített találhatók a fürt tárolóhelyét alapértelmezett.

```
    /app-logs/<user>/logs/<applicationId>
```

Az összesített naplók nincsenek közvetlenül is olvasható, mivel az oktatóprogram tároló indexelt TFile bináris formátumot. A YARN erőforrás-kezelő naplók vagy a parancssori eszközök segítségével az alkalmazások vagy a tárolókat érdeklő egyszerű szövegként ezek a naplók megtekintéséhez.

#### <a name="yarn-cli-tools"></a>YARN CLI-eszközei

A YARN CLI eszközök használatához először csatlakoznia kell a HDInsight-fürthöz SSH használatával. Adja meg a `<applicationId>`, `<user-who-started-the-application>`, `<containerId>`, és `<worker-node-address>` adatokat, amikor a parancsok futtatásával. A naplók megtekintéséhez egyszerű szövegként a következő parancsok egyikét:

```bash
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
```

#### <a name="yarn-resourcemanager-ui"></a>YARN erőforrás-kezelő felhasználói felületen

A YARN erőforrás-kezelő felhasználói felületén az átjárócsomóponthoz fut, és az Ambari webes felhasználói felületen keresztül érhető el. A YARN naplók megtekintéséhez tegye a következőket:

1. Egy böngészőben navigáljon `https://CLUSTERNAME.azurehdinsight.net`. CLUSTERNAME cserélje le a HDInsight-fürt nevét.
2. A bal oldali szolgáltatások közül jelölje be a YARN.
3. Gyorshivatkozások legördülő listából válassza ki a fürtcsomópontok központi majd **ResourceManager naplók**. A YARN naplóit hivatkozáslista jelenik meg.

## <a name="step-4-forecast-log-volume-storage-sizes-and-costs"></a>4. lépés: Az előrejelzési napló tároló mérete és költségek

Az előző lépések végrehajtását követően, hogy egy típusok ismeretének és kötetek a naplófájlok, amely a HDInsight-fürtöt vagy fürtöket állít elő.

Ezután elemezze a kulcs naplók tárolási helye a naplózási adatok mennyiségét egy meghatározott időtartamra vonatkozóan. Például elemezheti a kötet és a növekedési több mint 30 – 60-90 nap.  Jegyezze fel ezt az információt összegyűjtheti egy táblázatban, vagy használjon más eszközökkel, például a Visual Studio, a Azure Tártallózó vagy a Power Query az Excel programhoz. További információkért lásd: [naplók elemzése a HDInsight](hdinsight-debug-jobs.md).  

Most már rendelkezik egy napló felügyeleti stratégia kulcsfontosságú naplók elegendő információt.  Használja a táblázat (vagy tetszés szerinti fejlesztőeszközzel) segítségével mindkét napló méretnövekedés előrejelzési és storage Azure szolgáltatási költségei továbbítja.  Fontolja meg is napló megőrzési vonatkozó követelmények a naplófájlokat, amelyek vizsgálja készlete.  Most is reforecast későbbi napló tárolási költségek, meghatározása után mely naplófájlokat törlése (ha van ilyen), és melyik naplók őrződnek meg legyen, és kevésbé költséges Azure Storage archivált.

## <a name="step-5-determine-log-archive-policies-and-processes"></a>5. lépés: A napló archív szabályzatoknak és folyamatoknak meghatározása

Miután eldöntötte, hogy mely naplófájlokat törölheti, állíthatja be számos Hadoop szolgáltatás automatikusan törli a naplófájlok egy megadott időszak után a naplózás paraméterek.

Az egyes naplófájlok megközelítés archiválás olcsó naplófájlt is használhatja. Azure Resource Manager tevékenységi naplóit ismerje meg ezt a módszert használja az Azure portál használatával.  Állítsa be az ARM-naplók archiválása kiválasztásával a **tevékenységnapló**"az Azure-portál a HDInsight-példány hivatkozásra.  A tevékenységnapló keresés lap tetején jelölje ki a **exportálása** menüpont megnyitása a **exportálási tevékenységnapló** ablaktáblán.  Adja meg az előfizetés, régió, hogy exportálja a tárfiók-e, és hány napig szeretné megőrizni a naplókat. Az azonos panelén is jelezheti, hogy az eseményközpontba exportálása. 

![Naplófájlok exportálása](./media/hdinsight-log-management/archive.png)

Azt is megteheti hogy lehet parancsprogramot futtatni a napló archiválása a PowerShell használatával.  PowerShell-parancsfájl például, [archív Azure Automation naplózza az Azure Blob Storage](https://gallery.technet.microsoft.com/scriptcenter/Archive-Azure-Automation-898a1aa8).

### <a name="accessing-azure-storage-metrics"></a>Az Azure storage mérőszámainak elérése

Az Azure storage beállítható úgy, hogy a naplófájl tárolási műveletek és a hozzáférés. Nagyon részletes naplók segítségével, a kapacitás figyelési és tervezési, valamint a naplózás tárolási kérelmeket. A naplózott információk tartalmazza a késés, így figyelése és a megoldások teljesítményének finomhangolásához.
A Hadoop a .NET SDK használatával vizsgálja meg a naplófájlokat hoz létre, amely tárolja az adatokat a HDInsight-fürtök az Azure storage.

### <a name="control-the-size-and-number-of-backup-indexes-for-old-log-files"></a>A méret és a régi naplófájlokat a biztonsági mentési indexek száma

Megőrzött fájlok száma és mérete szabályozására, a következő tulajdonságainak beállítása a `RollingFileAppender`:

* `maxFileSize`a kritikus mérete a fájl, amely fölött a fájl lesz állítva. Az alapértelmezett érték 10 MB.
* `maxBackupIndex`a létrehozandó 1 alapértelmezett biztonsági másolatok számát adja meg.

### <a name="other-log-management-techniques"></a>Más napló technikák

Nincs elég szabad lemezterület futtatásának elkerüléséhez eszközeivel néhány operációs rendszer például `logrotate` kezelését a naplófájlok kezelésére. Konfigurálható `logrotate` naponta futtatásához tömörítés naplófájl fájlok és eltávolításakor régiek. A megközelítésre függ a követelmények például mennyi ideig tartsa a naplófájlok helyi csomópontján. 

Ellenőrizheti azt is, hogy hibakeresési naplózás engedélyezve van-e a egy vagy több szolgáltatás, amely jelentősen megnő a kimeneti napló mérete. 

A naplók összegyűjtésére csomópontjaihoz egyetlen központi helyen, az adatfolyam, például választásával dolgozhat fel Solr az összes naplóbejegyzések hozhat létre.

## <a name="next-steps"></a>További lépések

* [Figyelés és eljárás a HDInsight-naplózás](https://msdn.microsoft.com/library/dn749790.aspx)
* [Linux-alapú HDInsight hozzáférést YARN alkalmazásnaplóban](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Hadoop-összetevők a naplófájlok méretét vezérlése](https://community.hortonworks.com/articles/8882/how-to-control-size-of-log-files-for-various-hdp-c.html)
