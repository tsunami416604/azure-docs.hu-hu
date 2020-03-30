---
title: HDInsight-fürt naplóinak kezelése – Azure HDInsight
description: Határozza meg a HDInsight tevékenységnaplófájlok típusait, méreteit és adatmegőrzési szabályzatait.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/05/2020
ms.openlocfilehash: 8c3cbf4c18b32a94abfe95e77be768020b44fda6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272304"
---
# <a name="manage-logs-for-an-hdinsight-cluster"></a>HDInsight-fürt naplóinak kezelése

A HDInsight-fürt számos naplófájlt hoz létre. Például az Apache Hadoop és a kapcsolódó szolgáltatások, például az Apache Spark, részletes feladat-végrehajtási naplókat hoz létre. A naplófájl-kezelés része a kifogástalan HDInsight-fürt karbantartásának. A naplóarchiválásra is lehetnek szabályozási követelmények.  A naplófájlok száma és mérete miatt a naplótárolás és az archiválás optimalizálása segít a szolgáltatásköltség-kezelésben.

A HDInsight-fürtnaplók kezelése magában foglalja a fürtkörnyezet minden aspektusára vonatkozó információkat. Ez az információ tartalmazza az összes kapcsolódó Azure Service-naplók, fürtkonfiguráció, feladat-végrehajtási információk, hibaállapotok és egyéb adatok szükség szerint.

A HDInsight-naplókezelés tipikus lépései a következők:

* 1. lépés: A napló-megőrzési házirendek meghatározása
* 2. lépés: Fürtszolgáltatás verzióinak konfigurációs naplóinak kezelése
* 3. lépés: Fürtfeladat-végrehajtási naplófájlok kezelése
* 4. lépés: A napló kötetének tárolási méretének és költségeinek előrejelzése
* 5. lépés: Naplóarchiválási házirendek és folyamatok meghatározása

## <a name="step-1-determine-log-retention-policies"></a>1. lépés: A napló-megőrzési házirendek meghatározása

A HDInsight-fürtnapló-kezelési stratégia létrehozásának első lépése az üzleti forgatókönyvekről és a feladat-végrehajtási előzmények tárolási követelményeiről való információgyűjtés.

### <a name="cluster-details"></a>Fürt részletei

A következő fürtrészletek hasznosak a naplókezelési stratégia információinak összegyűjtésében. Gyűjtse össze ezeket az adatokat az adott Azure-fiókban létrehozott összes HDInsight-fürtről.

* Fürt neve
* Fürtrégió és az Azure rendelkezésre állási zónája
* Fürtállapot, beleértve a legutóbbi állapotváltozás részleteit
* A fő-, mag- és feladatcsomópontokhoz megadott HDInsight-példányok típusa és száma

A legfelső szintű információk nagy részét az Azure Portalhasználatával szerezheti be.  Másik lehetőségként az [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) segítségével információkat kaphat a HDInsight-fürt(ek)ről:

```azurecli
az hdinsight list --resource-group <ResourceGroup>
az hdinsight show --resource-group <ResourceGroup> --name <ClusterName>
```

A PowerShell segítségével is megtekintheti ezeket az információkat.  További információ: [Apache Manage Hadoop clusters in HDInsight using Azure PowerShell](hdinsight-administer-use-powershell.md).

### <a name="understand-the-workloads-running-on-your-clusters"></a>A fürtökön futó számítási feladatok megismerése

Fontos, hogy a HDInsight-fürt(ek)en futó számítási feladatok típusait ismerje meg, hogy minden típushoz megfelelő naplózási stratégiákat tervezhet.

* A számítási feladatok kísérleti (például fejlesztés vagy teszt) vagy termelési minőség?
* Milyen gyakran futnak a termelésminőségű számítási feladatok?
* A számítási feladatok bármelyike erőforrás-igényes és/vagy hosszú ideig futó?
* A számítási feladatok bármelyike használja a Hadoop-szolgáltatások olyan összetett készletét, amelyhez több típusú naplók készülnek?
* A számítási feladatok rendelkeznek a kapcsolódó szabályozási végrehajtási leszármazási követelmények?

### <a name="example-log-retention-patterns-and-practices"></a>Példa naplómegőrzési mintákra és eljárásokra

* Fontolja meg az adatvonal nyomon követésének fenntartását úgy, hogy azonosítót ad az egyes naplóbejegyzésekhez, vagy más technikákkal. Ez lehetővé teszi az adatok és a művelet eredeti forrásának nyomon követését, valamint az adatok követését az egyes fázisokon keresztül a konzisztencia és az érvényesség megértéséhez.

* Gondolja át, hogyan gyűjtheti össze a naplókat a fürtből vagy egynél több fürtből, és hogyan válogathatja össze őket olyan célokra, mint a naplózás, a figyelés, a tervezés és a riasztás. Előfordulhat, hogy egy egyéni megoldás sal érheti el és töltse le a naplófájlokat rendszeresen, és kombinálhatja és elemezheti őket, hogy irányítópult-megjelenítést biztosítson. További biztonsági vagy hibaészlelési riasztásokhoz is hozzáadhat további képességeket. Ezeket a segédprogramokat a PowerShell, a HDInsight SDK-k vagy az Azure klasszikus telepítési modelleléréséhez hozzáférő kód használatával hozhat létre.

* Fontolja meg, hogy egy figyelési megoldás vagy szolgáltatás hasznos előny lenne-e. A Microsoft System Center [hdinsight felügyeleti csomagot](https://www.microsoft.com/download/details.aspx?id=42521)biztosít. A naplók összegyűjtéséhez és központosításához külső eszközök, például az Apache Chukwa és a Ganglionok is használhatók. Számos vállalat kínál szolgáltatásokat a Hadoop-alapú big data-megoldások figyelésére, például: Centerity, Compuware APM, Sematext SPM és Zettaset Orchestrator.

## <a name="step-2-manage-cluster-service-versions-and-view-logs"></a>2. lépés: Fürtszolgáltatás-verziók kezelése és naplók megtekintése

Egy tipikus HDInsight-fürt számos szolgáltatást és nyílt forráskódú szoftvercsomagot használ (például Apache HBase, Apache Spark stb.). Egyes számítási feladatok, például a bioinformatika, előfordulhat, hogy a feladat végrehajtási naplók mellett meg kell őriznie a szolgáltatás konfigurációs naplóelőzményai.

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Fürtkonfigurációs beállítások megtekintése az Ambari felhasználói felülettel

Az Apache Ambari leegyszerűsíti a HDInsight-fürtök felügyeletét, konfigurálását és figyelését egy webes felhasználói felület és egy REST API biztosításával. Az Ambari linuxos HDInsight-fürtökben található. Válassza a **Fürtirányítópult** ablaktábláját az Azure Portal HDInsight lapján a **Fürtirányítópultok** hivatkozási lapjának megnyitásához.  Ezután válassza ki a **HDInsight-fürt irányítópultjának** ablaktábláját az Ambari felhasználói felület megnyitásához.  A rendszer kéri a fürt bejelentkezési hitelesítő adatait.

A szolgáltatásnézetek listájának megnyitásához válassza az **Ambari nézetek** ablaktáblát a HDInsight Azure Portal lapján.  Ez a lista a telepített könyvtáraktól függően változik.  Például megjelenhet a YARN Queue Manager, a Hive View és a Tez View.  Válassza ki bármelyik szolgáltatáshivatkozást a konfigurációs és szolgáltatásinformációk megtekintéséhez.  Az Ambari felhasználói **felület verem és verzió** lap tájékoztatást nyújt a fürtszolgáltatások konfigurációjáról és a szolgáltatás verzióelőzményeiről. Az Ambari felhasználói felület e szakaszába nívós menüben válassza a **Rendszergazda** menü **Veremek és verziók parancsot.**  A szolgáltatás verzióadatainak megtekintéséhez kattintson a **Verziók** fülre.

![Apache Ambari admin verem és verziók](./media/hdinsight-log-management/ambari-stack-versions.png)

Az Ambari felhasználói felület használatával letöltheti a fürt egy adott gazdagépen (vagy csomópontján) futó bármely (vagy az összes) szolgáltatás konfigurációját.  Válassza ki a **Házigazdák** menüt, majd az érdeklődési állomás hivatkozását. Az állomás lapján válassza a **Gazdaműveletek** gombot, majd **az Ügyfélkonfigurációk letöltése**lehetőséget.

![Apache Ambari letöltés gazdaklikiszolgáló konfigurációk](./media/hdinsight-log-management/download-client-configs.png)

### <a name="view-the-script-action-logs"></a>A parancsfájlművelet-naplók megtekintése

A HDInsight [parancsfájl-műveletek](hdinsight-hadoop-customize-cluster-linux.md) parancsfájlokat futtatnak egy fürtön, manuálisan vagy ha meg vannak adva. Parancsfájlműveletek segítségével például további szoftvereket telepíthet a fürtre, vagy módosíthatja a konfigurációs beállításokat az alapértelmezett értékekből. A parancsfájl-műveletnaplók betekintést nyújthatnak a fürt telepítése során történt hibákba, valamint a konfigurációs beállítások olyan változásaiba, amelyek hatással lehetnek a fürt teljesítményére és rendelkezésre állására.  Parancsfájl-művelet állapotának megtekintéséhez jelölje ki az Ambari felhasználói **felületén** a műveleti gombot, vagy lépjen be az alapértelmezett tárfiók állapotnaplóihoz. A tárolónaplók a `/STORAGE_ACCOUNT_NAME/DEFAULT_CONTAINER_NAME/custom-scriptaction-logs/CLUSTER_NAME/DATE`helyen érhetők el.

### <a name="view-ambari-alerts-status-logs"></a>Ambari riasztások állapotnaplóinak megtekintése

Az Apache Ambari a `ambari-alerts.log`riasztási állapot változásait írja . A teljes `/var/log/ambari-server/ambari-alerts.log`elérési út . A napló hibakeresésének engedélyezéséhez módosítsa `/etc/ambari-server/conf/log4j.properties.` a tulajdonságot `# Log alert state changes` a Módosítás, majd a következő helyen:

```
log4j.logger.alerts=INFO,alerts

to

log4j.logger.alerts=DEBUG,alerts
```

## <a name="step-3-manage-the-cluster-job-execution-log-files"></a>3. lépés: A fürtfeladat-végrehajtási naplófájlok kezelése

A következő lépés a különböző szolgáltatások feladat-végrehajtási naplófájljainak áttekintése.  A szolgáltatások közé tartozhat az Apache HBase, az Apache Spark és még sokan mások. A Hadoop-fürt számos részletes naplót hoz létre, így a hasznos (és nem) naplók meghatározása időigényes lehet.  A naplózási rendszer megértése fontos a naplófájlok célzott kezeléséhez.  Az alábbi kép egy példa naplófájl.

![HDInsight példa naplófájl minta kimenet](./media/hdinsight-log-management/hdi-log-file-example.png)

### <a name="access-the-hadoop-log-files"></a>A Hadoop naplófájljainak elérése

A HDInsight a naplófájlokat a fürtfájlrendszerben és az Azure Storage-ban tárolja. A fürtnaplófájljait megvizsgálhatja, ha megnyit egy [SSH-kapcsolatot](hdinsight-hadoop-linux-use-ssh-unix.md) a fürttel, és böngészik a fájlrendszerben, vagy a Távoli főcsomópont-kiszolgáló Hadoop YARN állapotportálját használja. Az Azure Storage naplófájljait az Azure Storage-ból adatokat elérő és letölthető eszközök bármelyikével vizsgálhatja meg. Ilyenek például az [AzCopy](../storage/common/storage-use-azcopy.md), [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer)és a Visual Studio Server Explorer. A PowerShell és az Azure Storage-ügyfélkönyvtárak, illetve az Azure .NET SDK-k is használhatják az Azure blob storage-beli adatok eléréséhez.

A Hadoop a feladatok munkáját *feladatkísérletekként* futtatja a fürt különböző csomópontjain. A HDInsight spekulatív feladatkísérleteket kezdeményezhet, és megszüntethet minden más olyan feladatkísérletet, amely nem fejeződik be először. Ez jelentős tevékenységet hoz létre, amely a vezérlő, stderr és syslog naplófájlok at-the-fly. Emellett egyszerre több feladatkísérlet is fut, de a naplófájl csak lineárisan tudja megjeleníteni az eredményeket.

#### <a name="hdinsight-logs-written-to-azure-blob-storage"></a>Az Azure Blob storage-ba írt HDInsight-naplók

A HDInsight-fürtök úgy vannak konfigurálva, hogy feladatnaplókat írjanak egy Azure Blob-tárfiókba az Azure PowerShell-parancsmagokkal vagy a .NET feladatbeküldési API-k használatával elküldött feladatokhoz.  Ha az SSH-n keresztül a fürthöz küldi a feladatokat, majd a végrehajtási naplózási adatok az Azure-táblákban tárolódnak az előző szakaszban tárgyalt módon.

A HDInsight által létrehozott alapvető naplófájlokmellett a telepített szolgáltatások, például a YARN is létrehoznak feladat-végrehajtási naplófájlokat.  A naplófájlok száma és típusa a telepített szolgáltatásoktól függ.  A gyakori szolgáltatások az Apache HBase, az Apache Spark és így tovább.  Vizsgálja meg az egyes szolgáltatások feladatnapló-végrehajtási fájljait a fürtön elérhető összes naplófájl megismeréséhez.  Minden szolgáltatás nak megvannak a saját egyedi naplózási módszerei és a naplófájlok tárolására szolgáló helyek.  Például a leggyakoribb szolgáltatásnaplófájlok (a YARN-ból) elérésének részleteit a következő szakasz tárgyalja.

### <a name="hdinsight-logs-generated-by-yarn"></a>A YARN által létrehozott HDInsight-naplók

A YARN összesíti a naplónaplókat a munkavégző csomópont összes tárolóján, és ezeket a naplókat munkavégző csomópontonként egy összesített naplófájlként tárolja. Ez a napló az alkalmazás befejezése után az alapértelmezett fájlrendszerben tárolódik. Az alkalmazás több száz vagy több ezer tárolót használhat, de az egyetlen munkavégző csomóponton futó összes tároló naplói mindig egyetlen fájlba vannak összesítve. Az alkalmazás által használt munkavégző csomópontonként csak egy napló van. A log összesítésalapértelmezés szerint engedélyezve van a HDInsight-fürtök 3.0-s vagy újabb verzióiban. Az összesített naplók a fürt alapértelmezett tárolójában találhatók.

```
/app-logs/<user>/logs/<applicationId>
```

Az összesített naplók közvetlenül nem olvashatók, mivel a tároló által indexelt TFile bináris formátumban vannak megírva. A YARN ResourceManager naplók vagy CLI-eszközök segítségével tekintse meg ezeket a naplókat egyszerű szövegként alkalmazások vagy fontos tárolók.

#### <a name="yarn-cli-tools"></a>YARN CLI eszközök

A YARN CLI eszközök használatához először az SSH használatával kell csatlakoznia a HDInsight-fürthöz. A `<applicationId>`parancsok `<user-who-started-the-application>` `<containerId>`futtatásakor adja meg a , , és `<worker-node-address>` az adatokat. A naplókat egyszerű szövegként tekintheti meg az alábbi parancsok egyikével:

```bash
yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
```

#### <a name="yarn-resourcemanager-ui"></a>YARN ResourceManager felhasználói felülete

A YARN ResourceManager felhasználói felülete a fürtfőcsomóponton fut, és az Ambari webes felhasználói felületén keresztül érhető el. A YARN naplók megtekintéséhez kövesse az alábbi lépéseket:

1. Egy webböngészőben nyissa meg a `https://CLUSTERNAME.azurehdinsight.net` címet. Cserélje le a CLUSTERNAME kifejezést a HDInsight-fürt nevére.
2. A bal oldali szolgáltatások listájában válassza a YARN lehetőséget.
3. A Gyorshivatkozások legördülő menüben jelölje ki a fürtfőcsomópontok egyikét, majd válassza a **ResourceManager-naplók lehetőséget.** Ön bemutatott egy listát a linkeket YARN naplók.

## <a name="step-4-forecast-log-volume-storage-sizes-and-costs"></a>4. lépés: A napló kötetének tárolási méretének és költségeinek előrejelzése

Az előző lépések végrehajtása után megismerheti a HDInsight-fürt(ek) által létrehozott naplófájlok típusait és köteteit.

Ezután elemezze a naplóadatok mennyiségét a kulcsnapló tárolási helyeken egy adott időszakban. Elemezheti például a mennyiséget és a növekedést 30-60-90 napos időszakokban.  Rögzítse ezeket az adatokat egy számolótáblában, vagy használjon más eszközöket, például a Visual Studio, az Azure Storage Explorer vagy az Excel hez készült Power Query. További információt a [HDInsight-naplók elemzése című témakörben talál.](hdinsight-debug-jobs.md)  

Most már elegendő információval rendelkezik a naplókezelési stratégia létrehozásához a kulcsnaplókhoz.  A számolótáblával (vagy a választott eszközzel) előre jelezheti a naplóméretének növekedését és a tárolási Azure-szolgáltatások költségeit a jövőben.  Fontolja meg a naplómegőrzési követelményeket is a vizsgált naplók készletére vonatkozóan.  Most már újraelőrejelzési jövőbeli napló tárolási költségek, miután megállapította, hogy mely naplófájlok törölhetők (ha vannak), és mely naplókat kell megőrizni, és archiválni a kevésbé költséges Azure Storage.

## <a name="step-5-determine-log-archive-policies-and-processes"></a>5. lépés: Naplóarchiválási házirendek és folyamatok meghatározása

Miután megállapította, hogy mely naplófájlok törölhetők, számos Hadoop-szolgáltatás naplózási paramétereit beállíthatja úgy, hogy egy meghatározott időszak után automatikusan törölje a naplófájlokat.

Bizonyos naplófájlok esetében alacsonyabb árú naplófájl-archiválási megközelítést használhat. Az Azure Resource Manager tevékenységnaplók, ezt a megközelítést az Azure Portalon.  Állítsa be az Erőforrás-kezelő naplók archiválását a **Tevékenységnapló** hivatkozás kiválasztásával az Azure Portalon a HDInsight-példányhoz.  A Tevékenységnapló keresési lap tetején válassza az **Exportálás** menüelemet a **Tevékenységnapló exportálása** ablaktábla megnyitásához.  Töltse ki az előfizetést, a régiót, hogy exportálja-e egy tárfiókba, és hány napig őrizze meg a naplókat. Ugyanezen az ablaktáblán azt is jelezheti, hogy exportálja-e az eseményközpontot.

![Az Azure Portal exportálási tevékenységnaplójának előzetes verziója](./media/hdinsight-log-management/hdi-export-log-files.png)

Másik lehetőségként parancsfájl-napló archiválás a PowerShell.  Például powershell-parancsfájl, [lásd: Archív Azure Automation-naplók az Azure Blob Storage.](https://gallery.technet.microsoft.com/scriptcenter/Archive-Azure-Automation-898a1aa8)

### <a name="accessing-azure-storage-metrics"></a>Az Azure Storage-metrikák elérése

Az Azure Storage konfigurálható a tárolási műveletek és a hozzáférés naplózására. Ezeket a nagyon részletes naplókat kapacitásfigyeléshez és -tervezéshez, valamint a tárolási kérelmek naplózásához használhatja. A naplózott adatok késési részleteket tartalmaznak, amelyek lehetővé teszik a megoldások teljesítményének figyelését és finomhangolását.
A Hadoop .NET SDK használatával megvizsgálhatja az Azure Storage számára létrehozott naplófájlokat, amelyek egy HDInsight-fürt adatait tárolják.

### <a name="control-the-size-and-number-of-backup-indexes-for-old-log-files"></a>A régi naplófájlok biztonsági másolatai méretének és számának szabályozása

A megőrzött naplófájlok méretének és számának szabályozásához `RollingFileAppender`állítsa be a következő tulajdonságokat:

* `maxFileSize`A fájl kritikus mérete, amely felett a fájl gördült. Az alapértelmezett érték 10 MB.
* `maxBackupIndex`megadja a létrehozandó biztonsági másolatok számát, alapértelmezett 1.

### <a name="other-log-management-techniques"></a>Egyéb naplókezelési technikák

Annak elkerülése érdekében, hogy elfogyjon a lemezterület, egyes operációs rendszer-eszközök, például [a logrotate](https://linux.die.net/man/8/logrotate) segítségével kezelheti a naplófájlok kezelését. Beállíthatja, `logrotate` hogy naponta fusson, tömörítse a naplófájlokat és távolítsa el a régieket. A megközelítés a ttól függ, hogy a követelmények, például mennyi ideig kell tartani a naplófájlokat a helyi csomópontokon.  

Azt is ellenőrizheti, hogy a DEBUG naplózás engedélyezve van-e egy vagy több szolgáltatáshoz, ami jelentősen megnöveli a kimeneti napló méretét.  

A naplók összegyűjtése az összes csomópontról egy központi helyre, létrehozhat egy adatfolyamot, például az összes naplóbejegyzés betöltése a Solr.To collect the logs from all the nodes to one central location, you can create a data flow, such as in in in a log entries into Solr.

## <a name="next-steps"></a>További lépések

* [A HDInsight figyelési és naplózási gyakorlata](https://msdn.microsoft.com/library/dn749790.aspx)
* [Az Apache Hadoop YARN alkalmazásnaplók elérése Linux alapú HDInsight ban](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [A naplófájlok méretének szabályozása különböző Apache Hadoop-összetevőkhöz](https://community.hortonworks.com/articles/8882/how-to-control-size-of-log-files-for-various-hdp-c.html)
