---
title: Egy lassú vagy meghibásodott HDInsight-fürt – Azure HDInsight hibaelhárítása
description: Diagnosztizálása és hibaelhárítása a lassú vagy meghibásodott HDInsight-fürt.
services: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/11/2018
ms.openlocfilehash: 5f85d01b20466fd72b802b4daaf001a7928717c4
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53410278"
---
# <a name="troubleshoot-a-slow-or-failing-hdinsight-cluster"></a>Lassú vagy meghibásodott HDInsight-fürt hibaelhárítása

Ha egy HDInsight-fürt vagy lassan fut, vagy hibakód miatt sikertelenül működő, több hibaelhárítási lehetőségek állnak rendelkezésére. Ha a feladatok futtatásához a vártnál hosszabb ideig tart, vagy általános jelennek meg lassú válaszidők, hibák felső a fürtről, például a szolgáltatások, amelyek a fürt fut. lehet. Azonban a leggyakoribb ilyen lassulás oka megfelelő méretezés. Amikor egy új HDInsight-fürtöt hoz létre, válassza ki a megfelelő [virtuálisgép-méretek](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters)

Egy lassú vagy meghibásodott fürt diagnosztizálhatja, a környezetet, például a kapcsolódó Azure-szolgáltatások, fürtkonfiguráció és feladat-végrehajtási információt vonatkozó biztonságáért adatainak összegyűjtése. Egy hasznos diagnosztikai, hogy próbálja meg újra előállítani a hibás állapotú, egy másik fürtön.

* 1. lépés: Gyűjtse össze a problémát
* 2. lépés: A HDInsight-fürt környezet ellenőrzése 
* 3. lépés: A fürt állapotának megtekintéséhez
* 4. lépés: Tekintse át a környezet verem és -verziók
* 5. lépés: Vizsgálja meg a fürt naplófájlok
* 6. lépés: Ellenőrizze a konfigurációs beállítások
* 7. lépés: Reprodukálja a hibát egy másik fürtön 

## <a name="step-1-gather-data-about-the-issue"></a>1. lépés: Gyűjtse össze a problémát

HDInsight számos eszközöket biztosít, amelyek használatával a fürtökkel problémák azonosítása és elhárítása. A következő lépések végigvezetik ezen eszközök, és javaslatokat tesz a felügyelő a probléma.

### <a name="identify-the-problem"></a>Probléma azonosítása

Probléma azonosítása érdekében vegye figyelembe a következő kérdéseket:

* Mi volt várható megtörténjen? Ehelyett Mi történt?
* Mennyi volt eltarthat futtatásához? Mennyi ideig kell azt futtatnia kell?
* A mindig lassan futnak a fürtön lévő feladatok van? Tudta, gyorsabban futnak egy másik fürtön?
* Ha adott probléma először fordulnak elő? Milyen gyakran ez történt óta?
* Változott, a saját fürtkonfiguráció?

### <a name="cluster-details"></a>Fürt részletes adatai

Fürt fontos információkat tartalmazza:

* Fürt neve.
* Fürt régió – keressen [régió leállások](https://azure.microsoft.com/status/).
* HDInsight-fürt típusa és verziója.
* A fő- és a feldolgozó csomópontok megadott HDInsight-példányok számát és típusát.

Az Azure Portalon ezeket az információkat biztosítja:

![HDInsight az Azure Portalon információkat](./media/hdinsight-troubleshoot-failed-cluster/portal.png)

Az Azure klasszikus parancssori felület is használható:

```
    azure hdinsight cluster list
    azure hdinsight cluster show <ClusterName>
```
[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

Egy másik lehetőség van a PowerShell használatával. További információkért lásd: [kezelése az Apache Hadoop-fürtök a HDInsight az Azure PowerShell-lel](hdinsight-administer-use-powershell.md).

## <a name="step-2-validate-the-hdinsight-cluster-environment"></a>2. lépés: A HDInsight-fürt környezet ellenőrzése

Egyes HDInsight-fürtök különböző Azure-szolgáltatások, valamint a nyílt forráskódú szoftverek, például az Apache Spark és Apache HBase támaszkodik. HDInsight-fürtök is meghívhatja a más Azure-szolgáltatásokkal, például az Azure-beli virtuális hálózatok.  Fürt hibát is okozhat, a fürtön futó szolgáltatások, vagy egy külső szolgáltatás által.  A fürt a fürt service konfiguráció módosítása is okozhatnak.

### <a name="service-details"></a>Szolgáltatásismertető

* Ellenőrizze a nyílt forráskódú kódtár változatból származnak.
* Keressen [Azure szolgáltatáskimaradások](https://azure.microsoft.com/status/) 
* Az Azure-szolgáltatás használati korlát ellenőrzés 
* Ellenőrizze az Azure virtuális hálózat alhálózati konfigurációját 

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Az Ambari felhasználói felületén, a nézet fürtbeállítások

Az Apache Ambari felügyeleti és monitorozási egy HDInsight-fürtöt egy webes felhasználói felületének és a egy REST API-t biztosít. A Linux-alapú HDInsight-fürtök az Ambari tartalmazza. Válassza ki a **fürt irányítópultja** HDInsight az Azure portal oldalán található panelen.  Válassza ki a **HDInsight-fürt irányítópultja** ablaktáblán nyissa meg az Ambari felhasználói felületén, és adja meg a fürt bejelentkezési hitelesítő adatait.  

![Az Ambari felhasználói felületén](./media/hdinsight-troubleshoot-failed-cluster/ambari-ui.png)

Szolgáltatás nézetek listájának megnyitásához válassza **Ambari-nézetek** az Azure portal oldalon.  Ez a lista attól függ, mely könyvtárak vannak telepítve. Láthatja például, az üzenetsor-kezelő YARN, Hive-nézet és a Tez nézet.  Kattintson a szolgáltatás konfigurációs és szolgáltatási adatokat.

#### <a name="check-for-azure-service-outages"></a>Az Azure szolgáltatás-kimaradások keresése

HDInsight számos Azure-szolgáltatások támaszkodik. Az Azure HDInsight, a tárolók adatainak és szkriptjeinek Azure Blob storage vagy Azure-DataLake Store, a virtuális kiszolgálók fut, és az indexek naplófájlok az Azure Table storage. Ezek a szolgáltatások megszakításmentes végrehajtása Habár ritkán fordul elő, akkor okozhat problémát a HDInsight. Ha nem várt lassulásokat vagy hibák a fürtön, ellenőrizze a [Azure állapotjelző irányítópultján](https://azure.microsoft.com/status/). Minden egyes szolgáltatás állapotának régió szerint jelenik meg. Ellenőrizze a fürt régió és is régióban a kapcsolódó szolgáltatások.

#### <a name="check-azure-service-usage-limits"></a>Azure-szolgáltatás használati felső korlát ellenőrzése

Ha egy nagy fürt indította el, vagy egyszerre indított számos fürthöz, a fürt meghiúsulhat, ha egy Azure-szolgáltatás ennél. Szolgáltatásra vonatkozó korlátozások az Azure-előfizetés függően változnak. További információkért lásd: [Azure-előfizetés és a szolgáltatások korlátozásai, kvótái és megkötései](https://docs.microsoft.com/azure/azure-subscription-service-limits).
Kérheti, hogy a Microsoft érhető el (például a VM-magokat és Virtuálisgép-példányok) HDInsight-erőforrások számának növelése az egy [Resource Manager core kvótakérelem növekedés](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

#### <a name="check-the-release-version"></a>Ellenőrizze a végleges verziót

Hasonlítsa össze a legfrissebb verzióját a HDInsight-fürt verziója. Minden egyes HDInsight kiadásban több ponton továbbfejlesztettük, például az új alkalmazások, szolgáltatások, javításokat és hibajavításokat tartalmaz. A probléma, amely befolyásolja a fürt esetleg javított legújabb verziójában. Ha lehetséges futtassa újra a fürt a HDInsight és például az Apache HBase, Apache Spark és egyéb kapcsolódó kódtárakat legújabb verzióját használja.

#### <a name="restart-your-cluster-services"></a>Indítsa újra a fürt szolgáltatások

Ha a lassulásokat tapasztal a fürtben, fontolja meg, az Ambari felhasználói felületén vagy a klasszikus Azure CLI segítségével a szolgáltatások újraindítása. Előfordulhat, hogy a fürt tapasztalt átmeneti hibák, és újraindítás a leggyorsabb mód a stabilizálódhatnak a környezetben, és esetleg javíthatja a teljesítményt.

## <a name="step-3-view-your-clusters-health"></a>3. lépés: A fürt állapotának megtekintéséhez

HDInsight-fürtök különböző típusú virtuálisgép-példányokon futó csomópontok állnak. Minden egyes csomópont erőforráshiány, hálózati problémák léptek fel és egyéb problémák, ami lelassíthatja a fürt figyelhető. Minden fürt két fő csomópontot tartalmaz, és a legtöbb fürttípus tartalmazhatja feldolgozó és élcsomópontok. 

Mindegyik fürttípus használ a különböző csomópontokhoz ismertetését lásd: [fürtök beállítása a HDInsight az Apache Hadoop, az Apache Spark, az Apache Kafka és további](hdinsight-hadoop-provision-linux-clusters.md).

Az alábbi szakaszok ismertetik az egyes csomópontok és a teljes fürt állapotának ellenőrzése.

### <a name="get-a-snapshot-of-the-cluster-health-using-the-ambari-ui-dashboard"></a>A fürt állapotának az Ambari felhasználói felületén irányítópulttal pillanatképet

A [Ambari felhasználói felületének irányítópultja](#view-cluster-configuration-settings-with-the-ambari-ui) (`https://<clustername>.azurehdinsight.net`) nyújt áttekintést, a fürt állapotát, például az üzemidő, memória, hálózati és CPU-használat, HDFS-lemezek használata terén, és így tovább. A gazdagépek szakaszban az Ambari segítségével megtekintheti az erőforrások a gazdagép szintjén. Is állítsa le és indítsa újra a szolgáltatásokat.

### <a name="check-your-webhcat-service"></a>Ellenőrizze a WebHCat szolgáltatást

Sikertelen volt az Apache Hive-, Apache Pig vagy az Apache Sqoop-feladatok az egyik gyakori esetben hiba a [WebHCat](hdinsight-hadoop-templeton-webhcat-debug-errors.md) (vagy *templeton eszközön keresztül végzett*) szolgáltatás. WebHCat egy REST-felület, a távoli feladat végrehajtása, például a Hive, Pig, Scoop és a MapReduce. WebHCat fordítja le a feladat beküldése kérelmek Apache Hadoop YARN-alkalmazások, és visszaad egy állapotot, a YARN-alkalmazás állapotának származik.  A következő szakaszok ismertetik a közös WebHCat HTTP-állapotkódok.

#### <a name="badgateway-502-status-code"></a>BadGateway (502 állapotkód)

Ez az átjárócsomópontok általános üzenetét, és a leggyakoribb sikertelen állapot kódja. Ennek egyik lehetséges oka egy folyamatban le az aktív központi csomóponton WebHCat-szolgáltatás. Ellenőrizze az ezzel a lehetőséggel, használja a következő CURL-parancsot:

```bash
$ curl -u admin:{HTTP PASSWD} https://{CLUSTERNAME}.azurehdinsight.net/templeton/v1/status?user.name=admin
```

Az Ambari megjelenítése, amelyen a WebHCat-szolgáltatás leállt a gazdagépek riasztást jelenít meg. Ahhoz, hogy a WebHCat szolgáltatás biztonsági mentése a gazdagépen a szolgáltatás újraindításával próbálkozzon.

![WebHCat-kiszolgáló újraindítása](./media/hdinsight-troubleshoot-failed-cluster/restart-webhcat.png)

A WebHCat-kiszolgáló még nem kapja meg, ha ezután ellenőrizze a műveleti napló az hibaüzenetek. Részletes információkat, ellenőrizze a `stderr` és `stdout` a csomóponton hivatkozott fájlok.

#### <a name="webhcat-times-out"></a>WebHCat túllépi az időkorlátot

Egy HDInsight-átjárón túllépi az időkorlátot két percnél hosszabb válaszokat visszaadó `502 BadGateway`. WebHCat lekérdezi a feladatok állapotai YARN-szolgáltatások és YARN válaszolni két percnél hosszabb időt vesz igénybe, ha, amelyek a kérelem is időtúllépés.

Ebben az esetben tekintse át a következő naplók kapcsolódnak a a `/var/log/webhcat` könyvtár:

* **webhcat.log** , mely írási műveletek naplóit a log4j bejelentkezés
* **webhcat-console.log** van a kiszolgáló indításakor a stdout
* **webhcat-konzol-error.log** van az stderr a kiszolgálói folyamat

> [!NOTE]  
> Minden egyes `webhcat.log` frissítése naponta, generují se soubory nevű `webhcat.log.YYYY-MM-DD`. Válassza ki a megfelelő fájlt a vizsgált időtartományban.

A következő szakaszok ismertetik a WebHCat időtúllépések lehetséges okai.

##### <a name="webhcat-level-timeout"></a>WebHCat-szintű időkorlátja

WebHCat esetén a terhelés több mint 10 nyílt szoftvercsatornák, a hosszabb ideig tart a szoftvercsatorna új kapcsolatokat, ami időtúllépés eredményezhet. A hálózati kapcsolatokat, és a webhcat használatával listájában, használja a `netstat` meg a jelenlegi active átjárócsomóponthoz:

```bash
$ netstat | grep 30111
```

30111 az a port, a WebHCat figyeli. A megnyitott szoftvercsatornák számát 10-nél kisebb kell lennie.

Ha nincs nyitva sockets, az előző parancs nem állít elő egy eredményt. Ellenőrizze, hogy templeton eszközön keresztül végzett be-e, és porton 30111, használata:

```bash
$ netstat -l | grep 30111
```

##### <a name="yarn-level-timeout"></a>YARN-szintű időkorlátja

Templeton eszközön keresztül végzett hívások YARN feladatok futtatásához, és templeton eszközön keresztül végzett és a YARN közötti kommunikáció időtúllépés okozhat.

A YARN szintjén két típusa van időtúllépése:

1. A YARN feladat elküldése is igénybe vehet elég hosszú időtúllépés miatt.

    Ha megnyitja a `/var/log/webhcat/webhcat.log` naplófájlt, és keressen a "sorban álló feladat", előfordulhat, hogy látja, több bejegyzést, a végrehajtási idő túl hosszú (> 2000 ms), és növelje a megjelenítő várjon alkalommal.

    A várakozási sorban lévő feladatok időpontját továbbra is fennáll, növelheti, mert a sebesség, amellyel küldött get új feladatok nagyobb, mint a sebesség, amellyel a régi feladatokat végezhető el. 100 %-át használja, a YARN memória után a *joblauncher várólista* már nem lehet kölcsönzött származó kapacitás a *alapértelmezett várólista*. Ezért a joblauncher várólistába új feladatot nem hagyhatók jóvá. Ezt a viselkedést okozhat a várakozási idő hosszabb lesz, és már, időtúllépési hiba okozza, amely általában követ sok más.

    Az alábbi képen látható a joblauncher várólista 714.4 % színvonalát. Ez az elfogadható, mindaddig, amíg az alapértelmezett üzenetsor a kölcsönvett van még szabad kapacitás. Azonban ha a fürt teljes kihasználását, és a YARN memória 100 %-os kapacitással, új feladatokat kell várnia, ami végül időtúllépéseket okoz.

    ![Joblauncher várólista](./media/hdinsight-troubleshoot-failed-cluster/joblauncher-queue.png)

    A probléma megoldásához két módja van: vagy csökkentse az új, az elküldött feladatok, vagy növelje a fogyasztás sebessége a régi feladatokat a fürt vertikális felskálázásával sebessége.

2. YARN-feldolgozás, ami időtúllépéseket is okozhatnak hosszú időt is igénybe vehet.

    * Az összes feladat listázása: Ez a hívás időigényes. Ez a hívás az alkalmazások, a YARN ResourceManager, és az egyes befejezett alkalmazások enumerálása, az állapot olvas be a YARN JobHistoryServer. A magasabb számok újabb feladatot az a hívás időtúllépés is.

    * Lista feladatok 7 napnál régebbi: A HDInsight YARN JobHistoryServer fenntartani befejezett feladat információkat hét napja van konfigurálva (`mapreduce.jobhistory.max-age-ms` érték). Feladatok törölve eredményez időtúllépés enumerálása közben.

Ezek a problémák diagnosztizálásához:

    1. Határozza meg az UTC idő tartományát hibaelhárítása
    2. Válassza ki a megfelelő `webhcat.log` fájl (ok)
    3. Ebben az időszakban figyelmeztetés és hibaüzenetek keresése

#### <a name="other-webhcat-failures"></a>Más WebHCat-hibák

1. HTTP-állapotkód: 500

    A legtöbb esetben, ahol adja vissza a WebHCat az 500-as a hibaüzenet tartalmazza a hiba részleteiért. Ellenkező esetben át kell `webhcat.log` üzenetek figyelmeztetés vagy hiba.

2. A sikertelen feladatok

    Előfordulhat, hogy esetekben használ a Webhcattel interakciók sikeres, de a feladat nem működik.

    Templeton eszközön keresztül végzett gyűjti, a feladat konzolkimenet `stderr` a `statusdir`, amelyek gyakran hasznos hibaelhárítási. `stderr` a YARN-alkalmazásazonosító, a tényleges lekérdezés tartalmazza.

## <a name="step-4-review-the-environment-stack-and-versions"></a>4. lépés: Tekintse át a környezet verem és -verziók

Az Ambari felhasználói felületén **verem és verzió** oldal ismerteti, fürt-szolgáltatások konfigurációs és szolgáltatási verzióelőzmények.  Helytelen könyvtár Hadoop verziót lehet egy fürt hiba okát.  Az Ambari felhasználói felületén, válassza ki a **rendszergazdai** menüben, majd **platformok és verziók**.  Válassza ki a **verziók** fülre az oldal fájlverzió-információkat lásd:

![Verem és -verziók](./media/hdinsight-troubleshoot-failed-cluster/stack-versions.png)

## <a name="step-5-examine-the-log-files"></a>5. lépés: Vizsgálja meg a naplófájlok

Nincsenek naplók, amelyek akkor jönnek létre, a számos szolgáltatásból és a egy HDInsight-fürtöt alkotó összetevők számos különböző típusú. [WebHCat-naplófájlok](#check-your-webhcat-service) korábban ismerteti. Nincsenek számos egyéb hasznos naplófájlok segítségével megvizsgálhatja a fürttel kapcsolatos problémák szűkítéséhez az alábbi szakaszokban leírtak szerint.

![HDInsight log fájl például](./media/hdinsight-troubleshoot-failed-cluster/logs.png)

* HDInsight-fürtök csomópontokból több csomópont, amelyek a legtöbb vannak kiadta a beküldött feladatok futtatásához. Feladatok egyidejű futtatását, de naplófájlokat is csak eredmények megjelenítéséhez lineárisan. HDInsight új feladatokat, végrehajtja a megszakítást okozó, mások, amelyek nem először végezze el. Ezt a tevékenységet a rendszer naplózza a `stderr` és `syslog` fájlokat.

* A parancsfájl műveleti naplófájljaihoz hibák vagy nem várt konfigurációmódosításokkal megjelenítése a fürt létrehozása során.

* A Hadoop-lépés naplók indul el a hibákat tartalmazó lépés részeként Hadoop-feladatok azonosítása.

### <a name="check-the-script-action-logs"></a>Ellenőrizze a parancsfájl műveleti naplói

HDInsight [szkriptműveletek](hdinsight-hadoop-customize-cluster-linux.md) parancsfájlok megadott manuálisan, vagy ha a fürtön futnak. Használható például az parancsfájlműveletekkel további szoftverek telepíthetők a fürtön, vagy megváltoztathatja a konfigurációs beállítások alapértelmezett értékét. A parancsfájl műveleti naplói ellenőrzésére is nyújtanak az fürt beállítása és konfigurálása során fellépő hibák.  Egy parancsprogram-művelet állapotának megtekintéséhez válassza a **ops** gomb az Ambari felhasználói felületén, vagy az alapértelmezett tárfiókot a naplók elérése.

A parancsfájl műveleti naplói található a `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE` könyvtár.

### <a name="view-hdinsight-logs-using-ambari-quick-links"></a>Az Ambari Gyorshivatkozások használatával HDInsight-naplók megtekintése

A HDInsight az Ambari felhasználói felületén számos olyan **Gyorshivatkozások** szakaszokat.  A napló hivatkozások egy adott szolgáltatáshoz, a HDInsight-fürt eléréséhez nyissa meg a fürt Ambari felhasználói felületén, majd válassza ki a szolgáltatás hivatkozásra a bal oldalon a listából. Válassza ki a **Gyorshivatkozások** legördülő menüből, majd a HDInsight csomópont, és válassza ki a hozzá tartozó társított napló hivatkozására.

Ha például a HDFS-naplókra:

![A naplófájlok Ambari Gyorshivatkozások](./media/hdinsight-troubleshoot-failed-cluster/quick-links.png)

### <a name="view-hadoop-generated-log-files"></a>Hadoop által létrehozott naplófájlok megtekintéséhez

Egy HDInsight-fürtön az Azure-táblák és az Azure Blob storage írt naplófájlokat hoz létre. YARN a saját végrehajtási naplókat hoz létre. További információkért lásd: [egy HDInsight-fürt naplóinak kezelése](hdinsight-log-management.md#access-the-hadoop-log-files).

### <a name="review-heap-dumps"></a>Felülvizsgálati készült halommemória-képek

Halomürítések az alkalmazás memória, beleértve a változók értékeit, ugyanakkor amelynek hasznosak a futási időben bekövetkező problémák diagnosztizálása pillanatképet tartalmaz. További információkért lásd: [engedélyezése halommemória-képek a Linux-alapú HDInsight az Apache Hadoop-szolgáltatásokhoz](hdinsight-hadoop-collect-debug-heap-dump-linux.md).

## <a name="step-6-check-configuration-settings"></a>6. lépés: Ellenőrizze a konfigurációs beállítások

HDInsight-fürtök olyan előre konfigurált alapértelmezett beállításokat, a kapcsolódó szolgáltatások, például a Hadoop-, Hive, HBase és így tovább. Fürt, a hardverkonfigurációja, a csomópontok számát típusától függően a típusú feladatot futtat, és az adatok dolgozik (és az adatok feldolgozásának módja) szükség lehet optimalizálni a konfigurációt.

Teljesítmény-konfigurációkat a legtöbb forgatókönyvhöz optimalizálására vonatkozó részletes utasításokért lásd: [optimalizálása az Apache Ambari fürtkonfigurációk](hdinsight-changing-configs-via-ambari.md). A Spark használata esetén lásd: [a teljesítmény optimalizálása az Apache Spark-feladatok](spark/apache-spark-perf.md). 

## <a name="step-7-reproduce-the-failure-on-a-different-cluster"></a>7. lépés: Reprodukálja a hibát egy másik fürtön

Segítségével diagnosztizálhatja a fürt hiba forrását. Indítsa el az új fürt ugyanazzal a konfigurációval, majd küldje el újra a sikertelen feladat lépéseket egyenként. Ellenőrizze az egyes lépések eredményét a következő feldolgozása előtt. Ez a módszer lehetővé teszi, hogy javítsa ki, majd futtassa újból a sikertelen egyetlen lépésben. Ez a módszer is rendelkezik az előnye, hogy csak a bemeneti adatok egyszer betöltése.

1. Hozzon létre egy új tesztfürt a meghibásodott fürt ugyanazt a konfigurációt.
2. Küldje el a vizsgálatot fürthöz feladat első lépése.
3. Ha a lépés befejezi a feldolgozást, a lépés naplófájlokban hibáinak kereséséhez. A test-fürt fő csomópontja csatlakozhat, és ott naplófájljainak megtekintéséhez. A lépés naplófájlok csak a lépés fut egy kis ideig, futtatása befejeződik, vagy nem sikerül után jelennek meg.
4. Ha az első lépése sikeresen befejeződött, futtassa a következő lépéssel. Ha a hibák történtek, vizsgálja meg a hibát a naplófájlokban. Ha a kódban történt, győződjön meg arról, a javítás, és futtassa újra a lépést. 
5. Folytassa az eljárást minden lépést hiba nélkül lefut.
6. Ha elkészült a tesztfürt hibakeresés törlése.

## <a name="next-steps"></a>További lépések

* [HDInsight-fürtök kezelése az Apache Ambari webes felhasználói felület használatával](hdinsight-hadoop-manage-ambari.md)
* [HDInsight-naplók elemzése](hdinsight-debug-jobs.md)
* [Hozzáférés az Apache Hadoop YARN napló a Linux-alapú HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [A Linux-alapú HDInsight az Apache Hadoop-szolgáltatásokhoz halomürítések engedélyezése](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [Ismert problémák a HDInsight az Apache Spark-fürt](hdinsight-apache-spark-known-issues.md)
