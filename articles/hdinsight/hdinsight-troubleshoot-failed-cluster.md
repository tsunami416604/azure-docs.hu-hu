---
title: Lassú vagy sikertelen feladat elhárítása az Azure HDInsight-fürtön
description: Lassú vagy sikertelen feladat diagnosztizálása és hibaelhárítása egy Azure HDInsight-fürtön.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: be991b63784a2c72a51bfbdc8506f3b4695ed6c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895317"
---
# <a name="troubleshoot-a-slow-or-failing-job-on-a-hdinsight-cluster"></a>Egy HDInsight-fürt lassú vagy meghibásodott feladatának hibaelhárítása

Ha egy HDInsight-fürtön lévő alkalmazás adatokat dolgoz fel, vagy lassan fut, vagy hibakóddal nem működik, számos hibaelhárítási lehetőség közül választhat. Ha a feladatok futtatása a vártnál tovább tart, vagy általában lassú válaszidőt lát, előfordulhat, hogy a fürt től ük felé hibák lépnek felfelé, például azok a szolgáltatások, amelyeken a fürt fut. Azonban a leggyakoribb oka ezeknek a lassulásoknak az elégtelen méretezés. Új HDInsight-fürt létrehozásakor válassza ki a megfelelő [virtuálisgép-méreteket.](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters)

Lassú vagy hibás fürt diagnosztizálása érdekében gyűjtsön információkat a környezet minden aspektusáról, például a kapcsolódó Azure Servicesről, a fürtkonfigurációról és a feladat-végrehajtási információkról. Egy hasznos diagnosztikai, hogy megpróbálja reprodukálni a hiba állapotát egy másik fürtön.

* 1. lépés: Adatok gyűjtése a problémáról.
* 2. lépés: A HDInsight-fürtkörnyezet ellenőrzése.
* 3. lépés: A fürt állapotának megtekintése.
* 4. lépés: Tekintse át a környezet verem és verziók.
* 5. lépés: Vizsgálja meg a fürt naplófájljait.
* 6. lépés: Ellenőrizze a konfigurációs beállításokat.
* 7. lépés: A hiba reprodukálása egy másik fürtön.

## <a name="step-1-gather-data-about-the-issue"></a>1. lépés: Adatok gyűjtése a problémáról

A HDInsight számos eszközt kínál, amelyekkel azonosíthatja és elháríthatja a fürtökkel kapcsolatos problémákat. Az alábbi lépések végigvezetik ezeken az eszközökön, és javaslatokat tesznek a probléma pontos meghatározásához.

### <a name="identify-the-problem"></a>A probléma azonosítása

A probléma azonosításához vegye figyelembe a következő kérdéseket:

* Mit vártam, mi fog történni? Mi történt helyette?
* Mennyi ideig tartott a folyamat futása? Meddig kellett volna tartania?
* A feladatok mindig lassan futnak ezen a fürtön? Gyorsabban futottak egy másik fürtön?
* Mikor jelentkezett ez a probléma először? Milyen gyakran történt azóta?
* Változott valami a fürtkonfigurációban?

### <a name="cluster-details"></a>Fürt részletei

A fontos fürtinformációk a következők:

* Fürt neve.
* Fürtrégió – ellenőrizze a [régió kimaradásait.](https://azure.microsoft.com/status/)
* HDInsight-fürt típusa és verziója.
* Írja be és a fej- és munkavégző csomópontokhoz megadott HDInsight-példányok számát.

Az Azure Portal a következő információkat tudja megadni:

![HDInsight Azure-portál adatai](./media/hdinsight-troubleshoot-failed-cluster/hdi-azure-portal-info.png)

Az Azure [CLI-t is használhatja:](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

```azurecli
az hdinsight list --resource-group <ResourceGroup>
az hdinsight show --resource-group <ResourceGroup> --name <ClusterName>
```

Egy másik lehetőség a PowerShell használata. További információ: [Apache Hadoop-fürtök kezelése a HDInsightban az Azure PowerShell használatával című témakörben.](hdinsight-administer-use-powershell.md)

## <a name="step-2-validate-the-hdinsight-cluster-environment"></a>2. lépés: A HDInsight-fürtkörnyezet ellenőrzése

Minden HDInsight-fürt különböző Azure-szolgáltatásokra, valamint nyílt forráskódú szoftverekre, például az Apache HBase-re és az Apache Sparkra támaszkodik. A HDInsight-fürtök más Azure-szolgáltatásokat is igénybe vehetnek, például az Azure virtuális hálózatokat.  A fürthibát a fürt önkiszolgáló bármely szolgáltatása vagy egy külső szolgáltatás okozhatja.  A fürtszolgáltatás konfigurációjának módosítása a fürt meghiúsulását is okozhatja.

### <a name="service-details"></a>A szolgáltatás részletei

* Ellenőrizze a nyílt forráskódú tár kiadási verzióit.
* Ellenőrizze az [Azure szolgáltatáskimaradásokat.](https://azure.microsoft.com/status/)  
* Ellenőrizze az Azure Service használati korlátait. 
* Ellenőrizze az Azure virtuális hálózat alhálózati konfigurációját.  

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Fürtkonfigurációs beállítások megtekintése az Ambari felhasználói felülettel

Az Apache Ambari egy HDInsight-fürt felügyeletét és felügyeletét biztosítja webes felhasználói felülettel és REST API-val. Az Ambari linuxos HDInsight-fürtökben található. Válassza a **Fürtirányítópult** ablaktábláját az Azure Portal HDInsight lapján.  Válassza ki a **HDInsight fürt irányítópultjának** ablaktábláját az Ambari felhasználói felület megnyitásához, és adja meg a fürt bejelentkezési hitelesítő adatait.  

![Az Apache Ambari irányítópultjának áttekintése](./media/hdinsight-troubleshoot-failed-cluster/apache-ambari-overview.png)

A szolgáltatásnézetek listájának megnyitásához válassza az **Ambari nézetek lehetőséget** az Azure Portal lapon.  Ez a lista attól függ, hogy mely tárak vannak telepítve. Például megjelenhet a YARN Queue Manager, a Hive View és a Tez View.  Válasszon ki egy szolgáltatáshivatkozást a konfigurációs és szolgáltatási információk megtekintéséhez.

#### <a name="check-for-azure-service-outages"></a>Azure-szolgáltatás kimaradásainak ellenőrzése

A HDInsight számos Azure-szolgáltatásra támaszkodik. Virtuális kiszolgálókat futtat az Azure HDInsight-on, adatokat és parancsfájlokat tárol az Azure Blob storage-ban vagy az Azure Data Lake Storage-ban, és indexeli a naplófájlokat az Azure Table storage-ban. Ezek a szolgáltatások megszakításai, bár ritka, problémákat okozhat a HDInsight. Ha váratlan lassulások vagy hibák vannak a fürtben, ellenőrizze az Azure Status Dashboard című [webhelyet.](https://azure.microsoft.com/status/) Az egyes szolgáltatások állapota régiónként van felsorolva. Ellenőrizze a fürt régióját és a régiókat a kapcsolódó szolgáltatásokért.

#### <a name="check-azure-service-usage-limits"></a>Az Azure szolgáltatáshasználati korlátainak ellenőrzése

Ha egy nagy fürtöt indít, vagy egyszerre több fürtöt indított el, a fürt sikertelen lehet, ha túllépte az Azure-szolgáltatáskorlátot. A szolgáltatási korlátok az Azure-előfizetéstől függően eltérőek lehetnek. További információk: [Az Azure-előfizetések és -szolgáltatások korlátozásai, kvótái és megkötései](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).
Kérheti, hogy a Microsoft növelje a rendelkezésre álló HDInsight-erőforrások (például a virtuálisgép-magok és a virtuálisgép-példányok) számát a [Resource Manager alapvető kvótanövelési kérelemmel.](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)

#### <a name="check-the-release-version"></a>A kiadási verzió ellenőrzése

Hasonlítsa össze a fürtverzióját a legújabb HDInsight-kiadással. Minden HDInsight-kiadás olyan fejlesztéseket tartalmaz, mint az új alkalmazások, a funkciók, a javítások és a hibajavítások. Lehet, hogy a fürtöt érintő problémát a legújabb verzió javította. Ha lehetséges, futtassa újra a fürtöt a HDInsight legújabb verziójával és a kapcsolódó könyvtárakkal, például az Apache HBase, az Apache Spark és mások használatával.

#### <a name="restart-your-cluster-services"></a>A fürtszolgáltatások újraindítása

Ha a fürt lassulását tapasztalja, fontolja meg a szolgáltatások újraindítását az Ambari felhasználói felületén vagy az Azure Classic CLI-n keresztül. Előfordulhat, hogy a fürt átmeneti hibákat tapasztal, és az újraindítás a leggyorsabb módja a környezet stabilizálásának és a teljesítmény esetleges javításának.

## <a name="step-3-view-your-clusters-health"></a>3. lépés: A fürt állapotának megtekintése

A HDInsight-fürtök különböző típusú csomópontokból állnak, amelyek virtuálisgép-példányokon futnak. Minden csomópont figyelhető az erőforrás-éhezés, a hálózati kapcsolat problémái és egyéb problémák, amelyek lelassíthatják a fürtöt. Minden fürt két főcsomópontot tartalmaz, és a legtöbb fürttípus feldolgozói és peremhálózati csomópontok kombinációját tartalmazza. 

Az egyes fürttípusok által használt különböző csomópontok leírását a [Fürtök beállítása a HDInsightban az Apache Hadoop, az Apache Spark, az Apache Kafka és egyebek](hdinsight-hadoop-provision-linux-clusters.md)segítségével című témakörben található.

A következő szakaszok ismertetik, hogyan ellenőrizheti az egyes csomópontok és a teljes fürt állapotát.

### <a name="get-a-snapshot-of-the-cluster-health-using-the-ambari-ui-dashboard"></a>Pillanatkép a fürt állapotáról az Ambari felhasználói felület irányítópultján

Az [Ambari felhasználói felület (Ambari felhasználói felülete)](#view-cluster-configuration-settings-with-the-ambari-ui) `https://<clustername>.azurehdinsight.net`áttekintést nyújt a fürt állapotáról, például az állásidőről, a memóriáról, a hálózat- és processzorhasználatról, a HDFS lemezhasználatról stb. Az Ambari Hosts szakaszával állomásszinten tekintheti meg az erőforrásokat. A szolgáltatásokat le is állíthatja és újraindíthatja.

### <a name="check-your-webhcat-service"></a>A WebHCat szolgáltatás ellenőrzése

Az Apache Hive, Apache Pig vagy Apache Sqoop feladatok sikertelensége esetén az egyik gyakori forgatókönyv a [WebHCat](hdinsight-hadoop-templeton-webhcat-debug-errors.md) (vagy *Templeton)* szolgáltatás hibája. A WebHCat egy REST-felület távoli feladatvégrehajtáshoz, például Hive, Pig, Scoop és MapReduce. A WebHCat a feladatbeküldéses kérelmeket Apache Hadoop YARN-alkalmazásokká fordítja le, és a YARN alkalmazás állapotából származó állapotot ad vissza.  A következő szakaszok a WebHCat HTTP-állapotkódokat ismertetik.

#### <a name="badgateway-502-status-code"></a>BadGateway (502-es állapotkód)

Ez a kód egy általános üzenet az átjárócsomópontoktól, és a leggyakoribb hibaállapot-kódok. Ennek egyik lehetséges oka az, hogy a WebHCat szolgáltatás nem működik az aktív főcsomóponton. Ennek a lehetőségnek a ellenőrzéséhez használja a következő CURL parancsot:

```bash
curl -u admin:{HTTP PASSWD} https://{CLUSTERNAME}.azurehdinsight.net/templeton/v1/status?user.name=admin
```

Az Ambari riasztást jelenít meg, amely azokat az állomásokat jeleníti meg, amelyeken a WebHCat szolgáltatás nem működik. Megpróbálhatja újraindítani a WebHCat szolgáltatást a szolgáltatás gazdagépen való újraindításával.

![Apache Ambari WebHCat kiszolgáló újraindítása](./media/hdinsight-troubleshoot-failed-cluster/restart-webhcat-server.png)

Ha a WebHCat kiszolgáló továbbra sem jön létre, majd ellenőrizze a műveleti naplóban a hibaüzenetek. További információkért tekintse meg `stderr` `stdout` a csomóponton hivatkozott fájlokat.

#### <a name="webhcat-times-out"></a>WebHCat időát

A HDInsight-átjáró két percnél hosszabb időt `502 BadGateway`vesz igénybe a válaszok után. A WebHCat lekérdezi a YARN-szolgáltatásokat a feladatállapotok hoz, és ha a YARN válaszadása két percnél tovább tart, a kérés időkiírást is elérhet.

Ebben az esetben tekintse át a `/var/log/webhcat` következő naplókat a címtárban:

* **webhcat.log** a log4j napló, amelyre a szerver naplót ír
* **webhcat-console.log** a stdout a szerver indításakor
* **webhcat-console-error.log** a stderr a szerver folyamat

> [!NOTE]  
> Mindegyik `webhcat.log` naponta átgördül, `webhcat.log.YYYY-MM-DD`és a nevű fájlokat generálja. Válassza ki a vizsgálva lévő időtartománynak megfelelő fájlt.

A következő szakaszok a WebHCat időmegadásának néhány lehetséges okát ismertetik.

##### <a name="webhcat-level-timeout"></a>WebHCat-szint időmegadása

Ha a WebHCat terhelés alatt van, több mint 10 nyitott szoftvercsatornával, hosszabb időt vesz igénybe az új szoftvercsatorna-kapcsolatok létrehozása, ami időtúltöltést eredményezhet. A WebHCat hálózati kapcsolatainak listázásához használja `netstat` az aktuális aktív csomópontot:

```bash
netstat | grep 30111
```

A 30111 az a port, amelyet a WebHCat figyel. A nyitott foglalatszám nak 10-nél kisebbnek kell lennie.

Ha nincsenek nyitott szoftvercsatornák, az előző parancs nem hoz eredményt. Annak ellenőrzéséhez, hogy a Templeton fent van-e és figyel-e a 30111-es porton, használja a következőket:

```bash
netstat -l | grep 30111
```

##### <a name="yarn-level-timeout"></a>YARN-szint időmeghosszabbítása

Templeton felhívja a YARN-t, hogy futtasson feladatokat, és a Templeton és a YARN közötti kommunikáció időkérést okozhat.

A YARN szintjén kétféle időtúllépés létezik:

1. A YARN-feladat beküldése elég hosszú időt vehet igénybe ahhoz, hogy időhosszabbítást okozzon.

    Ha megnyitja `/var/log/webhcat/webhcat.log` a naplófájlt, és a "várólistára helyezett feladat" kifejezésre keres, több olyan bejegyzést is láthat, ahol a végrehajtási idő túl hosszú (>2000 ms), és a bejegyzések növekvő várakozási időt mutatnak.

    A várólistára helyezett feladatok ideje tovább növekszik, mivel az új feladatok elküldésének sebessége magasabb, mint a régi feladatok befejezésének sebessége. Ha a YARN memóriát 100%-ban használja, a *feladatindító-várólista* már nem tud kapacitást felvenni az *alapértelmezett várólistából.* Ezért nem lehet több új feladatot elfogadni a feladatindító várólistába. Ez a viselkedés a várakozási idő hosszabbés hosszabb idődét okozhatja, ami időbeli elírási hibát okozhat, amelyet általában sokan mások követnek.

    Az alábbi képen a joblauncher várólista 714,4%-os túlhasznált. Ez elfogadható mindaddig, amíg van szabad kapacitás az alapértelmezett várólistában, ahonnan kölcsön lehet kérni. Ha azonban a fürt teljes mértékben ki van használva, és a YARN-memória 100%-os kapacitással rendelkezik, az új feladatoknak várniuk kell, ami végül időkihagyást okoz.

    ![HDInsight feladatindító várólista nézete](./media/hdinsight-troubleshoot-failed-cluster/hdi-job-launcher-queue.png)

    A probléma kétféleképpen oldható meg: vagy csökkentheti a beküldött új feladatok sebességét, vagy növelheti a régi feladatok felhasználási sebességét a fürt növelésével.

2. A YARN feldolgozása hosszú időt vehet igénybe, ami időhosszabbítást okozhat.

    * Az összes feladat listázása: Ez egy időigényes hívás. Ez a hívás számba veszi a YARN ResourceManager alkalmazásait, és minden egyes befejezett alkalmazás esetén a YARN JobHistoryServer állapotot kapja. A nagyobb számú munkahely, ez a hívás időtúltöltés.

    * Hét napnál régebbi feladatok listázása: A HDInsight YARN JobHistoryServer`mapreduce.jobhistory.max-age-ms` úgy van beállítva, hogy a befejezett feladatadatokat hét napig (érték) megőrizze. A megtisztított feladatok számbavétele időhosszabbítást eredményez.

A problémák diagnosztizálása:

1. A hibaelhárításhoz szükséges UTC-időtartomány meghatározása
2. A megfelelő `webhcat.log` fájl(ok) kiválasztása
3. Ez idő alatt keressen FIGYELMEZTETŐ és HIBAÜZENETüzeneteket

#### <a name="other-webhcat-failures"></a>Egyéb WebHCat hibák

1. HTTP-állapotkód 500

    A legtöbb esetben, amikor a WebHCat 500-at ad vissza, a hibaüzenet a hiba részleteit tartalmazza. Ellenkező esetben `webhcat.log` keresse meg a WARN és a ERROR üzeneteket.

2. Feladathibák

    Előfordulhatnak olyan esetek, amikor a WebHCat-tel való interakciók sikeresek, de a feladatok sikertelenek.

    Templeton gyűjti a feladat `stderr` `statusdir`konzol kimenet, mint a , ami gyakran hasznos hibaelhárításhoz. `stderr`a tényleges lekérdezés YARN alkalmazásazonosítóját tartalmazza.

## <a name="step-4-review-the-environment-stack-and-versions"></a>4. lépés: A környezeti verem és a verziók áttekintése

Az Ambari felhasználói **felület verem és verzió** lap a fürtszolgáltatások konfigurációjáról és a szolgáltatás verzióelőzményeiről nyújt tájékoztatást.  A Nem megfelelő Hadoop szolgáltatáskönyvtár-verziók a fürthiba okalehet.  Az Ambari felhasználói felületén válassza a **Rendszergazda** menüt, majd a **Halom és a Verziók parancsot.**  A szolgáltatás verzióadatainak megtekintéséhez válassza a lap Verziók lapján a **Verziók** lapot:

![Apache Ambari verem és verziók](./media/hdinsight-troubleshoot-failed-cluster/ambari-stack-versions.png)

## <a name="step-5-examine-the-log-files"></a>5. lépés: A naplófájlok vizsgálata

A HDInsight-fürtöt alkotó számos szolgáltatás ból és összetevőből számos típusú napló létezik. [A WebHCat naplófájlokleírása](#check-your-webhcat-service) korábban történt. Számos más hasznos naplófájl is található, amelyek et megvizsgálhat a fürttel kapcsolatos problémák szűkítése érdekében, a következő szakaszokban leírtak szerint.

* A HDInsight-fürtök több csomópontból állnak, amelyek többsége a beküldött feladatok futtatásával van megbízva. A feladatok egyidejűleg futnak, de a naplófájlok csak lineárisan tudják megjeleníteni az eredményeket. A HDInsight új feladatokat hajt végre, és megjelent másokat, akik nem fejeződnek be először. Mindez a tevékenység naplózza `syslog` a és a `stderr` fájlokat.

* A parancsfájlnapló-fájlok hibákat vagy váratlan konfigurációs módosításokat jelenítenek meg a fürt létrehozási folyamata során.

* A Hadoop lépésnaplók azonosítják a Hadoop-feladatokat, amelyeket egy hibákat tartalmazó lépés részeként indítottak el.

### <a name="check-the-script-action-logs"></a>A parancsfájlművelet-naplók ellenőrzése

A HDInsight [parancsfájl-műveletek](hdinsight-hadoop-customize-cluster-linux.md) parancsfájlokat futtatnak a fürtön manuálisan vagy ha meg vannak adva. Parancsfájlműveletek segítségével például további szoftvereket telepíthet a fürtre, vagy módosíthatja a konfigurációs beállításokat az alapértelmezett értékekből. A parancsfájl-műveletnaplók ellenőrzése betekintést nyújthat a fürt telepítése és konfigurálása során történt hibákba.  A parancsfájl-művelet állapotát az Ambari felhasználói **felületen** lévő műveleti gomb kiválasztásával vagy az alapértelmezett tárfiókból való hozzáféréssel tekintheti meg.

A parancsfájlművelet-naplók a `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE` címtárban találhatók.

### <a name="view-hdinsight-logs-using-ambari-quick-links"></a>HDInsight-naplók megtekintése az Ambari gyorshivatkozások használatával

A HDInsight Ambari felhasználói felülete számos **gyorshivatkozás-szakaszt** tartalmaz.  A HDInsight-fürt egy adott szolgáltatásának naplóhivatkozásai eléréséhez nyissa meg a fürt Ambari felhasználói felületét, majd válassza ki a szolgáltatáshivatkozást a bal oldali listából. Jelölje ki a **Gyorshivatkozások** legördülő menüt, majd az érdeklődésre számot tartó HDInsight-csomópontot, és válassza ki a hozzá tartozó naplóhoz tartozó hivatkozást.

Például a HDFS naplók:

![Ambari gyorshivatkozások a naplófájlokhoz](./media/hdinsight-troubleshoot-failed-cluster/apache-ambari-quick-links.png)

### <a name="view-hadoop-generated-log-files"></a>Hadoop által létrehozott naplófájlok megtekintése

A HDInsight-fürt az Azure-táblákba és az Azure Blob storage-ba írt naplókat hoz létre. A YARN saját végrehajtási naplókat hoz létre. További információt a [HDInsight-fürt naplóinak kezelése című témakörben talál.](hdinsight-log-management.md#access-the-hadoop-log-files)

### <a name="review-heap-dumps"></a>Halommemória-memóriaképek áttekintése

A halommemória-memóriaképek az alkalmazás memóriájának pillanatképét tartalmazzák, beleértve az adott időpontban lévő változók értékeit is, amelyek hasznosak a futásidőben előforduló problémák diagnosztizálásakor. További információ: [Halommemória-írások engedélyezése Az Apache Hadoop-szolgáltatásokhoz Linux alapú HDInsight-alapú.](hdinsight-hadoop-collect-debug-heap-dump-linux.md)

## <a name="step-6-check-configuration-settings"></a>6. lépés: A konfigurációs beállítások ellenőrzése

A HDInsight-fürtök előre konfigurálva vannak a kapcsolódó szolgáltatások alapértelmezett beállításaival, például a Hadoop, a Hive, a HBase és így tovább. A fürt típusától, hardverkonfigurációjától, csomópontjainak számától, a futtatott feladatok típusától és a jelenleg végzett adatoktól (és az adatok feldolgozásának módjától) függően szükség lehet a konfiguráció optimalizálására.

A teljesítménykonfigurációk optimalizálására vonatkozó részletes útmutatást a [Fürtkonfigurációk optimalizálása az Apache Ambari segítségével](hdinsight-changing-configs-via-ambari.md)című témakörben talál. A Spark használatakor olvassa el [az Apache Spark-feladatok optimalizálása a teljesítmény érdekében című témakört.](spark/apache-spark-perf.md) 

## <a name="step-7-reproduce-the-failure-on-a-different-cluster"></a>7. lépés: A hiba reprodukálása egy másik fürtön

A fürthiba forrásának diagnosztizálása érdekében indítson el egy új fürtöt ugyanazzal a konfigurációval, majd küldje el újra a sikertelen feladat lépéseit egyenként. Ellenőrizze az egyes lépéseket a következő lépés feldolgozása előtt. Ez a módszer lehetőséget ad egyetlen sikertelen lépés kijavítására és újrafuttatására. Ez a módszer is megvan az az előnye, hogy csak a bemeneti adatok betöltése egyszer.

1. Hozzon létre egy új tesztfürtet ugyanazzal a konfigurációval, mint a sikertelen fürt.
2. Küldje el az első feladatlépést a tesztfürtnek.
3. Amikor a lépés befejeződik a feldolgozás, ellenőrizze a hibákat a lépés naplófájlokban. Csatlakozzon a tesztfürt fő csomópontjához, és tekintse meg az ottani naplófájlokat. A lépésnaplófájlok csak akkor jelennek meg, ha a lépés egy ideig fut, befejeződik vagy sikertelen.
4. Ha az első lépés sikeres volt, futtassa a következő lépést. Ha hibák voltak, vizsgálja meg a hibát a naplófájlokban. Ha hiba történt a kódban, tegye a javítást, és futtassa újra a lépést.
5. Folytassa, amíg az összes lépés hiba nélkül le nem fut.
6. Ha befejezte a tesztfürt hibakeresését, törölje azt.

## <a name="next-steps"></a>További lépések

* [HDInsight-fürtök kezelése az Apache Ambari webes felületével](hdinsight-hadoop-manage-ambari.md)
* [HDInsight-naplók elemzése](hdinsight-debug-jobs.md)
* [Az Apache Hadoop YARN alkalmazásjel elérése Linux alapú HDInsightban](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Halommemória-memóriaképek engedélyezése Apache Hadoop-szolgáltatásokhoz Linux-alapú HDInsight-alapú](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [Ismert problémák az Apache Spark-fürthöz a HDInsighton](hdinsight-apache-spark-known-issues.md)
