---
title: Az Azure HDInsight figyelése és kezelése az Ambari webfelhasználói felülethasználatával
description: Ismerje meg, hogyan használhatja az Ambari linuxos HDInsight-fürtöket és azok kezeléséhez. Ebben a dokumentumban megtudhatja, hogyan használhatja a HDInsight-fürtökben található Ambari webes felhasználói felületet.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/05/2020
ms.openlocfilehash: bf780897317d41c7da85140f64313546cf5c31d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064698"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-web-ui"></a>HDInsight-fürtök kezelése az Apache Ambari webes felületével

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Az Apache Ambari leegyszerűsíti az Apache Hadoop-fürt felügyeletét és felügyeletét azáltal, hogy egy könnyen használható webes felhasználói felületet és REST API-t biztosít. Az Ambari a HDInsight-fürtökben található, és a fürt figyelésére és a konfiguráció módosítására szolgál.

Ebben a dokumentumban megtudhatja, hogyan használhatja az Ambari web felhasználói felületét HDInsight-fürttel.

## <a name="what-is-apache-ambari"></a><a id="whatis"></a>Mi az Apache Ambari?

[Az Apache Ambari](https://ambari.apache.org) leegyszerűsíti a Hadoop kezelését azáltal, hogy könnyen használható webes felhasználói felületet biztosít. Az Ambari segítségével kezelheti és figyelheti a Hadoop-fürtöket. A fejlesztők ezeket a képességeket az [Ambari REST API-k](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)használatával integrálhatják az alkalmazásaikba.

## <a name="connectivity"></a>Kapcsolatok

Az Ambari web felhasználói felülete a `https://CLUSTERNAME.azurehdinsight.net`HDInsight-fürtön érhető el, ahol `CLUSTERNAME` a fürt neve található.

> [!IMPORTANT]  
> A HDInsight-on az Ambarihoz való csatlakozáshttps-t igényel. Amikor a rendszer hitelesítést kér, használja a fürt létrehozásakor megadott rendszergazdai fiók nevet és jelszót. Ha a rendszer nem kéri a hitelesítő adatokat, ellenőrizze a hálózati beállításokat, hogy nincs-e kapcsolati probléma az ügyfél és az Azure HDInsight fürtök között.

## <a name="ssh-tunnel-proxy"></a>SSH-alagút (proxy)

Bár a fürt Höz készült Ambari közvetlenül az interneten keresztül érhető el, az Ambari webfelhasználói felületről (például a JobTracker-hez) származó egyes hivatkozások nincsenek elérhetők az interneten. Ezeknek a szolgáltatásoknak a eléréséhez létre kell hoznia egy SSH-alagutat. További információ: [SSH tunneling használata a HDInsight segítségével.](hdinsight-linux-ambari-ssh-tunnel.md)

## <a name="ambari-web-ui"></a>Ambari webes felhasználói felület

> [!WARNING]  
> Az Ambari webfelhasználói felület nem minden szolgáltatása támogatott a HDInsight ban. További információt a dokumentum [Nem támogatott műveletek](#unsupported-operations) szakaszában talál.

Amikor az Ambari Web felhasználói felületéhez csatlakozik, a rendszer kéri, hogy hitelesítse magát a lapon. Használja a fürt létrehozása során használt fürtfelügyeleti felhasználót (alapértelmezett rendszergazda) és jelszavát.

Amikor megnyílik a lap, jegyezze fel a felső sávot. Ez a sáv a következő információkat és vezérlőket tartalmazza:

![Az Apache Ambari irányítópultjának áttekintése](./media/hdinsight-hadoop-manage-ambari/apache-ambari-dashboard.png)

|Elem |Leírás |
|---|---|
|Ambari embléma|Megnyitja az irányítópultot, amely a fürt figyelésére használható.|
|Fürt neve # ops|A folyamatban lévő Ambari-műveletek számát jeleníti meg. A fürt nevének vagy a **# ops-nak** a kiválasztása megjeleníti a háttérműveletek listáját.|
|# figyelmeztetések|Figyelmeztetéseket vagy kritikus riasztásokat jelenít meg a fürthöz, ha vannak ilyenek.|
|Irányítópult|Megjeleníti az irányítópultot.|
|Szolgáltatások|A fürt szolgáltatásainak információ- és konfigurációs beállításai.|
|Hosts|A fürt csomópontjainak információ- és konfigurációs beállításai.|
|Riasztások|Információk, figyelmeztetések és kritikus riasztások naplója.|
|Rendszergazda|A fürtre telepített szoftververem/szolgáltatások, a szolgáltatásfiók adatai és a Kerberos-biztonság.|
|Rendszergazda gomb|Ambari kezelése, felhasználói beállítások, és jelentkezzen ki.|

## <a name="monitoring"></a>Figyelés

### <a name="alerts"></a>Riasztások

Az alábbi lista az Ambari által használt gyakori riasztási állapotokat tartalmazza:

* **OK**
* **Figyelmeztetés**
* **Kritikus**
* **Ismeretlen**

Az **OK-tól** eltérő riasztások hatására a **# riasztások** bejegyzés az oldal tetején, hogy megjelenítse a riasztások számát. A bejegyzés kiválasztása a riasztásokat és azok állapotát jeleníti meg.

A riasztások több alapértelmezett csoportba vannak rendezve, amelyek a Riasztások lapon **tekinthetők** meg.

![Apache Ambari riasztások oldal összegzése](./media/hdinsight-hadoop-manage-ambari/hdinsight-alerts-page.png)

A csoportokat a **Műveletek** menüBen kezelheti, és a Riasztáscsoportok kezelése parancsra kattintva választhatja a **Csoportcsoportok kezelése parancsot.**

![Az Apache Ambari riasztási csoportokat kezel](./media/hdinsight-hadoop-manage-ambari/ambari-manage-alerts.png)

Az __Értesítések kezelése__parancsra kattintva kezelheti a riasztási módszereket, és riasztási értesítéseket hozhat létre a **Műveletek** menüből. Az aktuális értesítések megjelennek. Itt is létrehozhat értesítéseket. Az értesítések **et e-mailben** vagy **SNMP-en** keresztül lehet küldeni, ha adott riasztási/súlyossági kombinációk fordulnak elő. Például küldhet egy e-mailt, ha a **YARN alapértelmezett** csoportban lévő riasztások bármelyike **Kritikus**értékre van állítva.

![Az Apache Ambari riasztási értesítést hoz létre](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

Végül a __Műveletek__ menü __Riasztásbeállítások kezelése parancsával beállíthatja,__ hogy az értesítés küldése előtt hányszor kell egy riasztásnak bekövetkeznie. Ezzel a beállítással megakadályozhatja az átmeneti hibákra vonatkozó értesítéseket.

Az ingyenes [SendGrid-fiókkal](https://docs.microsoft.com/azure/sendgrid-dotnet-how-to-send-email)kapcsolatos riasztási értesítések oktatóanyagát az [Apache Ambari e-mail értesítések konfigurálása az Azure HDInsight ban című témakörben található.](./apache-ambari-email.md)

### <a name="cluster"></a>Fürt

Az irányítópult **Metrikák** lapja egy sor widgetet tartalmaz, amelyek segítségével egy pillantással áttekintheti a fürt állapotát. Számos widget, például a **CPU-használat**további információkat nyújt, ha rákattintanak.

![Apache Ambari irányítópult mérőszámokkal](./media/hdinsight-hadoop-manage-ambari/hdi-metrics-dashboard.png)

A **Heatmaps** lapon a mérőszámok színes hőtérképként jelennek meg, zöldről pirosra váltva.

![Apache Ambari műszerfal hőtérképekkel](./media/hdinsight-hadoop-manage-ambari/hdi-heatmap-dashboard.png)

A fürtön belüli csomópontokról további információt a **Hosts (Állomások) elemre**talál. Ezután válassza ki az önt érdeklő adott csomópontot.

![Apache Ambari host összefoglaló részletek](./media/hdinsight-hadoop-manage-ambari/ambari-host-details1.png)

### <a name="services"></a>Szolgáltatások

Az **irányítópulton** található Szolgáltatások oldalsávja gyors betekintést nyújt a fürtön futó szolgáltatások állapotába. Különböző ikonok jelzik az állapotot vagy a végrehajtandó műveleteket. Ha például egy szolgáltatást újra kell hasznosítani, sárga újrahasznosítási szimbólum jelenik meg.

![Apache Ambari szolgáltatások oldalsávja](./media/hdinsight-hadoop-manage-ambari/apache-ambari-service-bar.png)

> [!NOTE]  
> A megjelenített szolgáltatások különböznek a HDInsight-fürttípusok és -verziók között. Az itt megjelenített szolgáltatások eltérhetnek a fürthöz megjelenített szolgáltatásoktól.

A szolgáltatás kiválasztása részletesebb információkat jelenít meg a szolgáltatásról.

![Apache Ambari szolgáltatás összefoglaló információk](./media/hdinsight-hadoop-manage-ambari/ambari-service-details.png)

#### <a name="quick-links"></a>Gyorshivatkozások

Egyes szolgáltatások a lap tetején **gyorshivatkozásokat** tartalmazó hivatkozást jelenítmeg. Ez a szolgáltatásspecifikus webes felhasználói felület elérésére használható, például:

* **Feladatelőzmények** - MapReduce feladatelőzmények.
* **Erőforrás-kezelő** – YARN ResourceManager felhasználói felület.
* **NameNode** - Hadoop elosztott fájlrendszer (HDFS) NameNode UI.
* **Oozie Web UI** - Oozie UI.

A hivatkozások bármelyikének kijelölésével megnyílik egy új lap a böngészőben, amely a kijelölt oldalt jeleníti meg.

> [!NOTE]  
> A szolgáltatás **gyorshivatkozások** bejegyzésének kiválasztása "a kiszolgáló nem található" hibaüzenetet adhat vissza. Ha ezt a hibát észleli, sSH-alagutat kell használnia a **szolgáltatás gyorskapcsolatok** bejegyzésének használatakor. További információ: [SSH tunneling használata a HDInsight segítségével](hdinsight-linux-ambari-ssh-tunnel.md)

## <a name="management"></a>Kezelés

### <a name="ambari-users-groups-and-permissions"></a>Ambari felhasználók, csoportok és engedélyek

A felhasználókkal, csoportokkal és engedélyekkel való együttműködés támogatott [tartományhoz csatlakozott](./domain-joined/hdinsight-security-overview.md) HDInsight-fürt használata esetén. Az Ambari felügyeleti felhasználói felület tartományhoz csatlakozott fürtön való használatáról a [Tartományhoz csatlakozott HDInsight-fürtök kezelése című](./domain-joined/hdinsight-security-overview.md)témakörben talál további információt.

> [!WARNING]  
> Ne módosítsa az Ambari figyelő (hdinsightwatchdog) jelszavát a Linux-alapú HDInsight-fürtön. A jelszó módosítása megszakítja a parancsfájlműveletek használatát vagy a méretezési műveletek et a fürttel.

### <a name="hosts"></a>Hosts

A **Hosts** lap a fürt összes állomását megjeleníti. Az állomások kezeléséhez kövesse az alábbi lépéseket.

![Apache Ambari házigazdák oldal áttekintése](./media/hdinsight-hadoop-manage-ambari/hdinsight-hosts-page.png)

> [!NOTE]  
> A gazdagép hozzáadása, leszerelése és újraüzembe olása nem használható HDInsight-fürtökkel.

1. Válassza ki a kezelni kívánt állomást.

2. A **Műveletek** menüben válassza ki a végrehajtani kívánt műveletet:

    |Elem |Leírás |
    |---|---|
    |Az összes összetevő indítása|Indítsa el az összes összetevőt a gazdagépen.|
    |Az összes összetevő leállítása|Állítsa le az összes összetevőt a gazdagépen.|
    |Az összes összetevő újraindítása|Állítsa le és indítsa el az összes összetevőt a gazdagépen.|
    |A karbantartási mód bekapcsolása|Letiltja a gazdagép riasztásait. Ezt a módot engedélyezni kell, ha riasztásokat generáló műveleteket hajt végre. Például egy szolgáltatás leállítása és elindítása.|
    |Karbantartási mód kikapcsolása|Visszaállítja az állomásnormál riasztást.|
    |Leállítás|Leállítja a DataNode vagy nodemanagers-t az állomáson.|
    |Indítás|Elindítja a DataNode vagy nodemanagers-t a gazdagépen.|
    |Újraindítás|Leállítja és elindítja a DataNode vagy nodemanagers-t a gazdagépen.|
    |Leszerelése|Állomás eltávolítása a fürtből. **Ne használja ezt a műveletet HDInsight-fürtökön.**|
    |Újraüzembe helyezés|Korábban leszerelt állomást ad hozzá a fürthöz. **Ne használja ezt a műveletet HDInsight-fürtökön.**|

### <a name="services"></a><a id="service"></a>Szolgáltatások

Az **Irányítópult** vagy a **Szolgáltatások** lapon a szolgáltatások listájának alján található **Műveletek** gombbal állíthatja le és indíthatja el az összes szolgáltatást.

![Apache Ambari szolgáltatás műveletek listája](./media/hdinsight-hadoop-manage-ambari/ambari-service-actions.png)

> [!WARNING]  
> Bár a **Szolgáltatás hozzáadása** ebben a menüben szerepel, nem használható szolgáltatások hozzáadására a HDInsight-fürthöz. Új szolgáltatásokat kell hozzáadni egy parancsfájl-művelet a fürt kiépítése során. A parancsfájlműveletek használatáról a [HDInsight-fürtök testreszabása parancsfájlműveletek használatával](hdinsight-hadoop-customize-cluster-linux.md)című témakörben talál további információt.

Bár a **Műveletek** gomb minden szolgáltatást újraindíthat, gyakran el szeretne indítani, le kell állítania vagy újra kell indítania egy adott szolgáltatást. Az alábbi lépésekkel műveleteket hajthat végre egy adott szolgáltatáson:

1. Az **Irányítópult** vagy a **Szolgáltatások** lapon válasszon ki egy szolgáltatást.

2. Az **Összegzés** lap tetején használja a **Szolgáltatási műveletek** gombot, és válassza ki a végrehajtandó műveletet. Ez újraindítja a szolgáltatást az összes csomóponton.

    ![Apache Ambari egyéni szolgáltatási műveletek](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

   > [!NOTE]  
   > Egyes szolgáltatások újraindítása a fürt futása közben riasztásokat generálhat. A riasztások elkerülése érdekében a **Szolgáltatás műveletek** gombsegítségével engedélyezheti a **karbantartási módot** a szolgáltatás számára az újraindítás végrehajtása előtt.

3. Ha kiválasztott egy műveletet, az oldal tetején található **# műveleti** bejegyzés növekszik, hogy megmutassa, hogy háttérművelet történik. Ha megjelenítésre van beállítva, megjelenik a háttérműveletek listája.

   > [!NOTE]  
   > Ha engedélyezte a **karbantartási módot** a szolgáltatáshoz, a művelet befejezése után ne felejtse el letiltani azt a **Service Actions** gombbal.

Szolgáltatás konfigurálásához kövesse az alábbi lépéseket:

1. Az **Irányítópult** vagy a **Szolgáltatások** lapon válasszon ki egy szolgáltatást.

2. Válassza a **Configs (Konfigurációs lap)** lehetőséget. Megjelenik az aktuális konfiguráció. A korábbi konfigurációk listája is megjelenik.

    ![Apache Ambari szolgáltatás konfigurációja](./media/hdinsight-hadoop-manage-ambari/ambari-service-configs.png)

3. A megjelenő mezők segítségével módosítsa a konfigurációt, majd válassza a **Mentés gombot.** Vagy válasszon egy korábbi konfigurációt, majd válassza az **Aktuális váltáshoz** az előző beállításokvisszaállításához válassza az Aktuális visszaállítás lehetőséget.

## <a name="ambari-views"></a>Ambari nézetek

Az Ambari nézetek lehetővé teszik a fejlesztők számára, hogy az [Apache Ambari Views Framework](https://cwiki.apache.org/confluence/display/AMBARI/Views)segítségével csatlakoztassák a felhasználói felület elemeit az Ambari Web felhasználói felületéhez. A HDInsight a következő nézeteket biztosítja a Hadoop-fürttípusokkal:

* Hive nézet: A Hive nézet lehetővé teszi, hogy a Hive-lekérdezések közvetlenül a webböngészőből futtatható. Mentheti a lekérdezéseket, megtekintheti az eredményeket, mentheti az eredményeket a fürttárolóba, vagy letöltheti az eredményeket a helyi rendszerre. A Hive-nézetek használatáról az [Apache Hive-nézetek használata a HDInsight segítségével](hadoop/apache-hadoop-use-hive-ambari-view.md)című témakörben talál további információt.

* Tez View: A Tez nézet lehetővé teszi, hogy jobban megértsék és optimalizálják a munkahelyeket. Megtekintheti a Tez-feladatok végrehajtását és a felhasznált erőforrásokat.

## <a name="unsupported-operations"></a>Nem támogatott műveletek

A HDInsight nem támogatja a következő Ambari-műveleteket:

* __A Metrikagyűjtő szolgáltatás áthelyezése.__ A Metrikagyűjtő szolgáltatás adatainak megtekintésekor a Szolgáltatásműveletek menüben elérhető műveletek egyike a __Mérőszámok gyűjtőjének áthelyezése.__ Ezt a HDInsight nem támogatja.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan használhatja az [Apache Ambari REST API-t](hdinsight-hadoop-manage-ambari-rest-api.md) a HDInsight segítségével.
