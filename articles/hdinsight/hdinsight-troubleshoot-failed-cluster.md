---
title: "A lassú vagy meghibásodott HDInsight-fürt - Azure HDInsight hibaelhárítása |} Microsoft Docs"
description: "Diagnosztizálásához és hibaelhárításához lassú vagy meghibásodott HDInsight-fürt."
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
ms.openlocfilehash: 59382931d4b5478888238760b268af7f962a10b5
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2018
---
# <a name="troubleshoot-a-slow-or-failing-hdinsight-cluster"></a>A lassú vagy meghibásodott HDInsight-fürt hibaelhárítása

Ha a HDInsight-fürtök lassan fut vagy hibakód miatt sikertelenül működő, több lehetőség közül választhat hibaelhárítási. Ha a feladat futtatásához a vártnál hosszabb ideig tart, vagy Ön válaszidejű általában, hibák előtt a fürtről, például amikor a fürt fut. szolgáltatások lehet. Az ilyen lassulás leggyakoribb okait azonban megfelelő méretezés. Amikor létrehoz egy új HDInsight-fürtöt, válassza ki a megfelelő [virtuálisgép-méretek](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters)

A lassú vagy meghibásodott fürt diagnosztizálásához, gyűjtse össze a környezetben, például a társított Azure-szolgáltatásokat, a fürt konfigurálása és a feladat-végrehajtási információt szempontjaival kapcsolatos adatokat. A hasznos diagnosztika arra, hogy a fürt egy másik hibaállapot Reprodukálja próbáljon.

* 1. lépés: A problémával kapcsolatos adatok összegyűjtése
* 2. lépés: A HDInsight-fürt környezetében ellenőrzése 
* 3. lépés: A fürt állapotának megtekintéséhez.
* 4. lépés:, Tekintse át a környezeti verem és -verziók
* 5. lépés:, Vizsgálja meg a fürt naplófájlok
* 6. lépés: Ellenőrizze a konfigurációs beállítások
* 7. lépés: Reprodukálja a hibát egy másik fürtön 

## <a name="step-1-gather-data-about-the-issue"></a>1. lépés: A problémával kapcsolatos adatok összegyűjtése

HDInsight, azonosítása és elhárítása fürtök segítségével számos eszközt biztosít. Az alábbi lépéseket, amelyek ezen eszközök, és adja meg a probléma felügyelő javaslatokat.

### <a name="identify-the-problem"></a>A probléma meghatározásához

Probléma azonosítása érdekében ellenőrizze a következőket:

* Mi volt várható megtörténjen-e? Mi történt helyette?
* Mennyi a folyamat vette futtatásához? Mennyi ideig kell kell futtatni?
* Vannak-e a feladatok mindig lassan fut a fürtön? Nem azok futtatni gyorsabban egy másik fürthöz?
* Ha a probléma először szerepelt? Milyen gyakran az keletkezését óta?
* Változott, a fürt konfigurációjába?

### <a name="cluster-details"></a>A fürt részleteinek

Fürt fontos információk a következők:

* Fürt neve.
* Fürt régió,-keressen [régió kimaradások](https://azure.microsoft.com/status/).
* A HDInsight-fürt típusa és verziót.
* A head és munkavégző csomópontokhoz megadott HDInsight-példányok számát és típusát.

Az Azure-portálon is információkkal:

![Információk a HDInsight Azure-portálon](./media/hdinsight-troubleshoot-failed-cluster/portal.png)

Az Azure parancssori felület is használható:

```
    azure hdinsight cluster list
    azure hdinsight cluster show <ClusterName>
```

Lehetősége van a PowerShell használatával. További információkért lásd: [Hadoop kezelése az Azure PowerShell hdinsight clusters](hdinsight-administer-use-powershell.md).

## <a name="step-2-validate-the-hdinsight-cluster-environment"></a>2. lépés: A HDInsight-fürt környezetében ellenőrzése

Minden egyes HDInsight-fürt különböző Azure-szolgáltatásokat, és nyílt forráskódú szoftvereket, például az Apache HBase és Apache Spark támaszkodik. A HDInsight-fürtök is meghívhatja a más Azure-szolgáltatásokkal, például az Azure virtuális hálózatot.  Fürt hibát is okozhat, a fürtön futó szolgáltatások bármelyike, vagy egy külső szolgáltatás.  A fürt szolgáltatási konfiguráció módosítása is eredményezheti, hogy a fürt sikertelen lesz.

### <a name="service-details"></a>Szolgáltatásismertető

* Ellenőrizze a nyílt forráskódú könyvtár változatból származnak.
* Ellenőrizze a [Azure szolgáltatás-kimaradások számát](https://azure.microsoft.com/status/) 
* Azure szolgáltatás használati korlátok ellenőrzése 
* Az Azure virtuális hálózati alhálózat konfigurációjának ellenőrzése 

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Az Ambari felhasználói felületén, a nézet fürtbeállítások

Apache Ambari biztosít a HDInsight-fürt és a webes felhasználói felület és a REST API kezeléséhez és monitorozásához. Ambari része a Linux-alapú HDInsight-fürtökön. Válassza ki a **fürt irányítópult** ablaktáblán az Azure portál HDInsight lapján.  Válassza ki a **HDInsight fürt irányítópult** ablaktáblán az Ambari felhasználói felületének megnyitásához, és írja be a fürt bejelentkezési hitelesítő adatok.  

![Ambari felhasználói felület](./media/hdinsight-troubleshoot-failed-cluster/ambari-ui.png)

Szolgáltatás nézetek listájának megnyitásához, jelölje be **Ambari nézetek** az Azure portál lapján.  Ez a lista attól függ, mely függvénytárak telepítve vannak-e. Megjelenhet például a YARN várólista-kezelő, a nézet Hive és a Tez nézet.  Válassza ki a szolgáltatás hivatkozásra kattintva megtekintheti az konfiguráció és a szolgáltatás adatait.

#### <a name="check-for-azure-service-outages"></a>Az Azure szolgáltatás-kimaradások számát ellenőrzése

HDInsight több Azure-szolgáltatások támaszkodik. Az Azure HDInsight, tárolók adatok vagy parancsfájlok Azure Blob-tároló vagy Azure DataLake-tároló, virtuális kiszolgálók fut, és indexek naplófájlok Azure Table storage-ban. Ezek a szolgáltatások megszakadását Habár ritkán fordul elő, hibákat is okozhat a hdinsight eszközben. Ha még nem várt lassulást vagy hibák, a fürtön, ellenőrizze a [Azure állapota irányítópult](https://azure.microsoft.com/status/). Minden szolgáltatás állapotának régiónként szerepel. Ellenőrizze a fürt régióban, illetve is régiók kapcsolódó szolgáltatások.

#### <a name="check-azure-service-usage-limits"></a>Azure-szolgáltatások használati korlátok ellenőrzése

Ha van egy nagy méretű fürt indításának, vagy egy időben elindítani sok fürtök, a fürt meghiúsulhatnak, ha egy Azure-szolgáltatások ennél. Szolgáltatásra vonatkozó korlátozások eltérőek lehetnek attól függően, hogy az Azure-előfizetéshez. További információkért lásd: [Azure-előfizetés és szolgáltatási korlátok, kvóták és megkötések](https://docs.microsoft.com/azure/azure-subscription-service-limits).
Kérheti, hogy a Microsoft érhető el (például a virtuális gép maggal és Virtuálisgép-példányok) HDInsight-erőforrások számának növelése az egy [erőforrás-kezelő core kvóta növelését kérelem](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

#### <a name="check-the-release-version"></a>Ellenőrizze a végleges verziót

Hasonlítsa össze a legfrissebb verzióját a HDInsight fürt verziójával. Minden egyes HDInsight kiadás például az új alkalmazások, szolgáltatások, javítások és hibajavításokat tartalmaz fejlesztéseket tartalmazza. A problémát, amely befolyásolja a fürt előfordulhat, hogy javított a legújabb verzió. Ha lehetséges futtassa újra a fürt a HDInsight és a például az Apache HBase, Apache Spark és egyéb kapcsolódó könyvtárak legújabb verzióját használja.

#### <a name="restart-your-cluster-services"></a>A fürt szolgáltatások újraindítása

Ha lassulást tapasztal a fürtben, fontolja meg a szolgáltatások az Ambari felhasználói felületén vagy az Azure parancssori felület használatával. A fürt hibákat tapasztalhat, átmeneti és újraindítása a leggyorsabban stabilizálását a környezetében, és esetleg javíthatja a teljesítményt.

## <a name="step-3-view-your-clusters-health"></a>3. lépés: A fürt állapotának megtekintéséhez.

A HDInsight-fürtök különböző típusú virtuálisgép-példányok futó csomópontok állnak. Erőforrás adatforgalom leállásának, a hálózati kapcsolat hibái és egyéb problémák, ami lelassíthatja a fürt minden csomópont alatt. Minden fürt két központi csomópontokat tartalmazza, és a legtöbb fürttípusok munkavégző és a peremhálózati csomópontok tartalmazzák. 

<!-- For a description of the various nodes each cluster type uses, see [HDInsight Architecture](hdinsight-architecture.md). -->

Az alábbi szakaszok ismertetik az egyes csomópontok és a teljes fürt állapotának ellenőrzése.

### <a name="get-a-snapshot-of-the-cluster-health-using-the-ambari-ui-dashboard"></a>A fürt állapotát, az Ambari felhasználói felület irányítópulttal pillanatképe beolvasása

A [Ambari felhasználói felületének irányítópultja](#view-cluster-configuration-settings-with-the-ambari-ui) (`https://<clustername>.azurehdinsight.net`) áttekintése fürt állapotát, például a hasznos Üzemidő, memória, hálózati és CPU-használat, HDFS lemezek használata terén, és így tovább. A gazdagépek szakaszban az Ambari segítségével megtekintheti az erőforrások a gazdagép szintjén. Is állítsa le és indítsa újra a szolgáltatásokat.

### <a name="check-your-webhcat-service"></a>Ellenőrizze a WebHCat szolgáltatást

Egy általános forgatókönyv a Hive, Pig vagy Sqoop feladatok végrehajtása egy hiba a [WebHCat](hdinsight-hadoop-templeton-webhcat-debug-errors.md) (vagy *lépni a Templeton*) szolgáltatás. WebHCat távoli feladat végrehajtását, például a Hive, Pig, Scoop és MapReduce egy REST-felületet. WebHCat YARN alkalmazások fordítja le a feladat elküldése kéréseket, és a YARN alkalmazásállapot származó állapotot ad vissza.  A következő szakaszok ismertetik a közös WebHCat HTTP-állapotkódok.

#### <a name="badgateway-502-status-code"></a>BadGateway (502 állapotkód:)

Az alábbi hibaüzenet jelenik meg az átjáró csomópontnak, és a hiba leggyakoribb állapotkód. Ennek egy lehetséges ok: a WebHCat-szolgáltatás leállt az aktív központi csomóponton folyamatban. Ezzel a lehetőséggel ellenőrzéséhez használja a következő CURL-parancsot:

```bash
$ curl -u admin:{HTTP PASSWD} https://{CLUSTERNAME}.azurehdinsight.net/templeton/v1/status?user.name=admin
```

Ambari megjelenít egy riasztást jelenít meg a gazdagépek, amelyen a WebHCat szolgáltatás le van állítva. Próbálja meg elindítani a WebHCat szolgáltatás vissza indítsa újra a szolgáltatást a gazdagépen.

![Indítsa újra a WebHCat-kiszolgáló](./media/hdinsight-troubleshoot-failed-cluster/restart-webhcat.png)

Ha a Webhcaten-kiszolgáló még nem kapja meg, majd a naplóban a műveletek hibaüzenetek. Részletes információkat, ellenőrizze a `stderr` és `stdout` a csomóponton hivatkozott fájlok.

#### <a name="webhcat-times-out"></a>WebHCat időtúllépése

Egy HDInsight-átjáró időtúllépése válaszok, amelyek tovább tart, mint két perc visszaadása `502 BadGateway`. WebHCat lekérdezése feladatállapotok YARN szolgáltatásokat, és a YARN válaszolni két percnél hosszabb időt vesz igénybe, ha, amely a kérelem is túllépi az időkorlátot.

Ebben az esetben tekintse át a következő a `/var/log/webhcat` könyvtár:

* **webhcat.log** a log4j naplót, mely írási műveleteket a kiszolgáló naplóiban
* **webhcat-console.log** a kiszolgáló indítása, ha az stdout értéke
* **webhcat-konzol-error.log** a kiszolgálói folyamat stderr van

> [!NOTE]
> Minden egyes `webhcat.log` felülírás naponta, nevű fájlok létrehozása `webhcat.log.YYYY-MM-DD`. Válassza ki a megfelelő fájlt a időtartomány vizsgál.

A következő szakaszok ismertetik a WebHCat időtúllépések néhány lehetséges okait.

##### <a name="webhcat-level-timeout"></a>WebHCat szintű időtúllépés

Ha WebHCat terhelés, legfeljebb 10 szoftvercsatorna van nyitva, a hosszabb ideig tart új szoftvercsatorna-kapcsolatok létesítéséhez, ennek eredményeként időtúllépés. A hálózati kapcsolatok WebHCat érkező vagy oda irányuló kilistázhatja `netstat` meg az aktuális active headnode:

```bash
$ netstat | grep 30111
```

30111 a WebHCat-as port. Nyissa meg szoftvercsatornák száma kisebb, mint 10 kell lennie.

Ha nincs megnyitott sockets, az előző parancs nem hoz eredményt. Ellenőrizze, hogy-e lépni a Templeton fel, és figyeli a portot 30111, használata:

```bash
$ netstat -l | grep 30111
```

##### <a name="yarn-level-timeout"></a>YARN szintű időtúllépés

Lépni a Templeton meghívja a YARN feladatok futtatásához, és lépni a Templeton és YARN közötti kommunikáció okozhatnak időtúllépés.

A YARN szinten két típusa van időtúllépése:

1. A YARN feladat elküldése is igénybe vehet hosszúságúra, hogy időtúllépés miatt.

    Ha megnyitja a `/var/log/webhcat/webhcat.log` naplófájlt, és keressen a "sorban álló feladat", előfordulhat, hogy látja, több bejegyzés ahol a végrehajtás ideje a túl hosszú (> 2000 ms), növekvő megjelenítő megadásával várjon alkalommal.

    Az idő az aszinkron feladatok folyamatosan nő, mert a sebesség, amellyel küldött get az új feladatok értéke magasabb, mint a sebesség, amellyel a régi feladatok elvégzése. Miután a YARN memória 100 %-át használja, a *joblauncher várólista* kapacitása a már nem nincs szükséged a *alapértelmezett várólista*. Ezért a joblauncher várólistán több új feladatok hagyhatók jóvá. Ezt a viselkedést okozhat a várakozási idő hosszabb legyen, és már, időtúllépési hiba okozza, amely általában követi sok más.

    Az alábbi ábrán a joblauncher várólista színvonalát 714.4 %. Ez az elfogadható, mindaddig, amíg nincs az alapértelmezett várólistán szektortól továbbra is szabad kapacitás. Azonban ha a fürt teljes mértékben ki van használva, és a YARN memória 100 %-os kapacitással, új feladatokat meg kell várnia, emiatt előfordulhat időtúllépések.

    ![Joblauncher várólista](./media/hdinsight-troubleshoot-failed-cluster/joblauncher-queue.png)

    A probléma megoldásához két módja van: vagy csökkentse az új feladatok küldi, vagy növelje a fogyasztás sebesség régi feladatokat a fürt vertikális felskálázásával sebességét.

2. YARN feldolgozási, ami okozhat időtúllépések hosszú időt vehet igénybe.

    * Az összes feladat felsorolása: Ez az időigényes hívásakor. Ez a hívás az alkalmazások a YARN erőforrás-kezelő, és minden befejezett alkalmazáshoz enumerálása, állapotát olvassa be a YARN JobHistoryServer a. A feladatok nagyobb számmal a hívás túllépi az időkorlátot is.

    * Régebbi, mint hét nap feladatokat: A HDInsight YARN JobHistoryServer szeretné megőrizni a hét napja befejezett feladatinformációkat van konfigurálva (`mapreduce.jobhistory.max-age-ms` érték). Időtúllépés kiürítve feladatok eredményez enumerálása közben.

Ezek a problémák diagnosztizálásához:

    1. Határozza meg az UTC időt tartományát hibaelhárítása
    2. Válassza ki a megfelelő `webhcat.log` fájl
    3. Ebben az időszakban figyelmeztetés és HIBÁRÓL értesítő üzenetek keresi

#### <a name="other-webhcat-failures"></a>Más WebHCat hibák

1. HTTP-állapotkód: 500

    A legtöbb esetben, ha adja vissza a WebHCat az 500-as a hibaüzenet tartalmazza a hiba részletei. Ellenkező esetben nézze át `webhcat.log` üzenetek figyelmeztetés vagy hiba történt.

2. A sikertelen feladatok

    Előfordulhatnak olyan esetekben, ahol WebHCat interakció sikeres, de a feladat sikertelen.

    Lépni a Templeton gyűjti a feladat konzol kimeneti adatok `stderr` a `statusdir`, amelyeket legtöbbször a hibaelhárításhoz használható. `stderr`a tényleges lekérdezés a YARN-azonosítót tartalmaz.

## <a name="step-4-review-the-environment-stack-and-versions"></a>4. lépés:, Tekintse át a környezeti verem és -verziók

Az Ambari felhasználói felület **verem és verzió** lap fürt szolgáltatások konfigurációs és a szolgáltatás korábbi verzióinak kapcsolatos információkat biztosít.  Helytelen Hadoop könyvtár verziót lehet egy fürt hiba okát.  Az Ambari felhasználói felületén, válassza ki a **Admin** menüben, majd **verem és verziók**.  Válassza ki a **verziók** fülre az oldal fájlverzió-információkat lásd:

![Verem és verziói](./media/hdinsight-troubleshoot-failed-cluster/stack-versions.png)

## <a name="step-5-examine-the-log-files"></a>5. lépés:, Vizsgálja meg a naplófájlok

Nincsenek naplók számos összetevőből épül fel a HDInsight-fürtök és szolgáltatások alapján létrehozott sokféle. [WebHCat naplófájlok](#check-your-webhcat-service) korábban ismerteti. Nincsenek számos más hasznos naplófájlok használatával megvizsgálhatja a fürttel kapcsolatos problémák szűkítéséhez a következő szakaszokban ismertetett módon.

![HDInsight log fájl példa](./media/hdinsight-troubleshoot-failed-cluster/logs.png)

* A HDInsight-fürtök áll, több csomópontot, amely a legtöbb vannak szolgáltatásait elküldött feladatok futtatásához. Feladatok egyidejű futtatását, de a naplófájlok csak jelennek lineárisan eredmények. HDInsight végrehajtja a új feladatokat, mások esetében nem sikerül befejezni először leáll. Ez a tevékenység a rendszer naplózza a `stderr` és `syslog` fájlokat.

* A parancsfájl művelet naplófájlok megjelenítése hibák vagy nem várt konfigurációs módosításokat a fürt létrehozása során.

* A Hadoop lépés naplók azonosítása elindítja a hibákat tartalmazó lépésben Hadoop-feladatokat.

### <a name="check-the-script-action-logs"></a>Ellenőrizze a parancsfájl műveleti naplói

HDInsight [parancsfájl-műveletek](hdinsight-hadoop-customize-cluster-linux.md) parancsfájlok futtatásának megadott manuálisan, vagy ha a fürtön. A Parancsfájlműveletek például használható további szoftvereket telepíteni a fürt vagy az alapértelmezett értékek konfigurációs beállításainak módosításához. A parancsfájl műveleti naplói ellenőrzése biztosíthat a fürt telepítése és beállítása során előforduló betekintést.  Egy parancsfájl művelet állapota kiválasztásával megtekintheti a **ops** gomb az Ambari felhasználói felületén, vagy az alapértelmezett tárfiók eléréséhez a naplókat.

A parancsfájl műveleti naplói találhatók a `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE` könyvtár.

### <a name="view-hdinsight-logs-using-ambari-quick-links"></a>Ambari gyors hivatkozásokkal HDInsight naplók megtekintése

A HDInsight Ambari felhasználói felület számos olyan **Gyorshivatkozások** szakaszok.  Fér hozzá a napló hivatkozások egy adott szolgáltatáshoz, a HDInsight-fürtjéhez, nyissa meg a fürt Ambari felhasználói felülete, majd jelölje ki a szolgáltatás kapcsolatot a bal oldali listában. Válassza ki a **Gyorshivatkozások** legördülő menüből, majd a HDInsight munkaterület iránt, és válassza a hozzá tartozó naplófájlok mutató hivatkozást.

Például a HDFS-naplók:

![A naplófájlok Ambari – hivatkozások](./media/hdinsight-troubleshoot-failed-cluster/quick-links.png)

### <a name="view-hadoop-generated-log-files"></a>Hadoop által létrehozott naplófájlok megtekintéséhez

HDInsight-fürtöt állít elő, Azure-táblákban és az Azure Blob Storage tárolóban írt naplókat. YARN saját végrehajtási naplót hoz létre. További információkért lásd: [a HDInsight-fürtök kezelése](hdinsight-log-management.md#access-the-hadoop-log-files).

### <a name="review-heap-dumps"></a>Tekintse át a halommemória memóriaképek

Halommemória memóriaképek tartalmaz egy pillanatkép, az alkalmazás memória, beleértve az idő lejárta után, amelyeket futtatás közben felmerülő problémák diagnosztizálásához változók értékeit. További információkért lásd: [engedélyezése halommemória kiírja a Linux-alapú HDInsight Hadoop-szolgáltatások](hdinsight-hadoop-collect-debug-heap-dump-linux.md).

## <a name="step-6-check-configuration-settings"></a>6. lépés: Ellenőrizze a konfigurációs beállítások

A HDInsight-fürtök olyan előre konfigurált alapértelmezett beállításokkal, a kapcsolódó szolgáltatásokra, például a Hadoop, struktúra, HBase, és így tovább. Attól függően, hogy a fürt, a hardverkonfigurációja, a csomópontok száma típusú feladatok típusai futtatja, és az adatok típusától (és adatok feldolgozásának módja), szükség lehet a konfigurációs optimalizálása.

<!-- For detailed instructions on optimizing performance configurations for most scenarios, see [Changing configurations with Ambari](hdinsight-changing-configs-via-ambari.md). When using Spark, see [Optimizing Spark jobs for performance](spark/apache-spark-perf.md).  -->

## <a name="step-7-reproduce-the-failure-on-a-different-cluster"></a>7. lépés: Reprodukálja a hibát egy másik fürtön

A fürt hiba forrását diagnosztizálásának elősegítéséhez, indítsa el az új fürt azonos konfigurációjú, és küldje el a sikertelen feladatot lépéseket egyenként. Az egyes lépések eredményének ellenőrzése a következő feldolgozása előtt. Ez a módszer lehetővé teszi, és futtassa újra a sikertelen egyetlen lépésben. Ez a módszer is azzal az előnnyel jár, csak egyszer kell betölteni a bemeneti adatok.

1. A meghibásodott fürt azonos konfigurációjú hozzon létre egy új teszt fürtöt.
2. A tesztfürthöz feladat először küldje el.
3. A lépés feldolgozás befejezése után ellenőrizze a hibákat a lépés naplófájlokban. A tesztfürthöz főcsomópont csatlakozhat, és nincs naplófájljainak megtekintéséhez. A lépés naplófájlok csak a lépés néhány alkalommal befejeződik, vagy nem sikerül után jelennek meg.
4. Ha az első lépése sikeresen befejeződött, futtassa a következő lépéssel. Hibák léptek fel, ha vizsgálja meg a hiba a rendszernapló fájljaiban. Ha a kódban hiba volt, módosítást, és futtassa újra a lépés. 
5. Folytassa, amíg az összes lépés futtatásához hiba nélkül.
6. Ha végzett a tesztfürthöz hibakeresés törlése.

## <a name="next-steps"></a>További lépések

* [A HDInsight-fürtök kezelése az Ambari webes felhasználói felület használatával](hdinsight-hadoop-manage-ambari.md)
* [HDInsight naplóinak elemzése](hdinsight-debug-jobs.md)
* [Linux-alapú HDInsight hozzáférést YARN alkalmazásnaplóban](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Halommemória memóriaképek a Linux-alapú HDInsight Hadoop-szolgáltatások engedélyezése](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [Ismert problémák az Apache Spark-fürttel hdinsighton](hdinsight-apache-spark-known-issues.md)
