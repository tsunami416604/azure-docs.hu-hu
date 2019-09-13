---
title: Azure-HDInsight figyelése és kezelése a Ambari webes felhasználói felületén
description: Ismerje meg, hogyan használhatja a Ambari a Linux-alapú HDInsight-fürtök figyelésére és kezelésére. Ebből a dokumentumból megtudhatja, hogyan használhatja a HDInsight-fürtökhöz tartozó Ambari webes felhasználói felületet.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: hrasheed
ms.openlocfilehash: 3ca9c12caa7fa9b54cd63c2655166d95477dffa2
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70885272"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-web-ui"></a>HDInsight-fürtök kezelése az Apache Ambari webes FELÜLETének használatával

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Az Apache Ambari megkönnyíti egy Apache Hadoop-fürt felügyeletét és figyelését azáltal, hogy könnyen használható webes felhasználói felületet és REST API biztosít. A Ambari a HDInsight-fürtökben található, és a fürt figyelésére és a konfiguráció módosítására szolgál.

Ebből a dokumentumból megtudhatja, hogyan használhatja a Ambari webes felhasználói felületét egy HDInsight-fürttel.

## <a id="whatis"></a>Mi az Apache Ambari?

Az [Apache Ambari](https://ambari.apache.org) egy könnyen használható webes felhasználói felületet biztosít a Hadoop-kezeléshez. A Ambari használatával felügyelheti és figyelheti a Hadoop-fürtöket. A fejlesztők a [AMBARI REST API](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)-k használatával integrálhatja ezeket a képességeket alkalmazásaiba.

## <a name="connectivity"></a>Kapcsolat

A Ambari webes felhasználói felülete a HDInsight `https://CLUSTERNAME.azurehdinsight.net`-fürtön érhető el, ahol `CLUSTERNAME` a a fürt neve.

> [!IMPORTANT]  
> A HDInsight Ambari-hez való csatlakozáshoz HTTPS szükséges. Ha a rendszer a hitelesítésre kéri, használja a fürt létrehozásakor megadott rendszergazdai fiók nevét és jelszavát.

## <a name="ssh-tunnel-proxy"></a>SSH-alagút (proxy)

Habár a fürt Ambari közvetlenül az interneten keresztül érhető el, néhány hivatkozás a Ambari webes felhasználói felületéről (például a JobTracker) nem elérhető az interneten. A szolgáltatások eléréséhez létre kell hoznia egy SSH-alagutat. További információ: az [SSH-alagút használata a HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="ambari-web-ui"></a>Ambari Web UI

> [!WARNING]  
> A Ambari webes felhasználói felületének nem minden funkciója támogatott a HDInsight. További információkért lásd a jelen dokumentum nem [támogatott műveletek](#unsupported-operations) című szakaszát.

A Ambari webes felhasználói felületéhez való csatlakozáskor a rendszer felszólítja, hogy hitelesítse magát a lapon. A fürt létrehozásakor használt fürt rendszergazdai felhasználójának (alapértelmezett rendszergazdája) és jelszavának használata.

Amikor megnyílik az oldal, jegyezze fel a felső sávot. Ez a sáv a következő információkat és vezérlőket tartalmazza:

![ambari – NAV](./media/hdinsight-hadoop-manage-ambari/ambari-nav.png)

|Elem |Leírás |
|---|---|
|Ambari embléma|Megnyitja az irányítópultot, amely a fürt figyelésére használható.|
|Fürt neve # Ops|A folyamatban lévő Ambari-műveletek számát jeleníti meg. A fürt nevének kiválasztásakor vagy az **# Ops** megjeleníti a háttérben futó műveletek listáját.|
|riasztások száma|Riasztásokat vagy kritikus riasztásokat jelenít meg, ha vannak ilyenek a fürthöz.|
|Irányítópult|Megjeleníti az irányítópultot.|
|Szolgáltatások|A fürt szolgáltatásainak információi és konfigurációs beállításai.|
|Gazdagépek|A fürt csomópontjainak információi és konfigurációs beállításai.|
|Riasztások|Információk, figyelmeztetések és kritikus riasztások naplója.|
|rendszergazda|A fürtre, a szolgáltatásfiók-információkra és a Kerberos-biztonságra telepített szoftverek stackje vagy szolgáltatásai.|
|Rendszergazda gomb|Ambari-kezelés, felhasználói beállítások és kijelentkezés.|

## <a name="monitoring"></a>Figyelés

### <a name="alerts"></a>Riasztások

Az alábbi lista a Ambari által használt általános riasztási állapotokat tartalmazza:

* **OK**
* **Figyelmeztetés**
* **CRITICAL**
* **ISMERETLEN**

Az **októl** eltérő riasztások miatt a **# riasztások** bejegyzés jelenik meg a lap tetején a riasztások számának megjelenítéséhez. Ennek a bejegyzésnek a kiválasztásával megjelennek a riasztások és állapotuk.

A riasztások több alapértelmezett csoportba vannak rendezve, amelyek a **riasztások** lapról jeleníthetők meg.

![Riasztások lap](./media/hdinsight-hadoop-manage-ambari/hdinsight-alerts-page.png)

A csoportokat a **műveletek** menü segítségével kezelheti, és a **riasztási csoportok kezelése**lehetőségre kattintva.

![riasztási csoportok kezelése párbeszédpanel](./media/hdinsight-hadoop-manage-ambari/manage-alerts.png)

Emellett a riasztási módszereket is kezelheti, és riasztási értesítéseket hozhat létre a **műveletek** menüből a __Riasztási értesítések kezelése__lehetőség kiválasztásával. Minden aktuális értesítés megjelenik. Itt is létrehozhat értesítéseket. Értesítéseket küldhet **e-mailben** vagy **SNMP** -n, ha adott riasztási/súlyossági kombinációk történnek. Küldhet például egy e-mailt, ha a **fonal alapértelmezett** csoportjában lévő bármelyik riasztás **kritikus**értékre van állítva.

![Riasztás létrehozása párbeszédpanel](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

Végül, ha a __műveletek__ menüben a __riasztási beállítások kezelése__ lehetőséget választja, megadhatja, hogy a riasztások hányszor legyenek elküldve az értesítés elküldése előtt. Ezzel a beállítással elkerülhetők az átmeneti hibák értesítései.

### <a name="cluster"></a>Fürt

Az irányítópult **metrikák** lapja több widgetet tartalmaz, amelyek megkönnyítik a fürt állapotának figyelését egy pillantással. Több widget (például **CPU-használat**) esetén további információk is megadhatók a kattintáskor.

![irányítópult metrikákkal](./media/hdinsight-hadoop-manage-ambari/hdi-metrics-dashboard.png)

A **intenzitástérképei** lap színes intenzitástérképei jeleníti meg a metrikákat, zöldről pirosra haladva.

![irányítópult a intenzitástérképei](./media/hdinsight-hadoop-manage-ambari/hdi-heatmap-dashboard.png)

A fürtben lévő csomópontokkal kapcsolatos további információkért válassza a **gazdagépek**lehetőséget. Ezután válassza ki az Önt érdeklő csomópontot.

![Gazdagép adatai](./media/hdinsight-hadoop-manage-ambari/host-details.png)

### <a name="services"></a>Szolgáltatások

Az irányítópulton található **szolgáltatások** oldalsáv gyors betekintést nyújt a fürtön futó szolgáltatások állapotával. A különböző ikonok jelzik az állapotot vagy a végrehajtandó műveleteket. A sárga Lomtár például akkor jelenik meg, ha egy szolgáltatást újra kell indítani.

![szolgáltatások oldalsó sáv](./media/hdinsight-hadoop-manage-ambari/service-bar.png)

> [!NOTE]  
> A megjelenített szolgáltatások különböznek a HDInsight-fürtök típusai és verziói között. Az itt megjelenő szolgáltatások eltérhetnek a fürthöz megjelenített szolgáltatástól.

A szolgáltatás kiválasztása részletesebb információkat jelenít meg a szolgáltatásról.

![szolgáltatás összegző információi](./media/hdinsight-hadoop-manage-ambari/service-details.png)

#### <a name="quick-links"></a>Gyorshivatkozások

Egyes szolgáltatások egy **Gyorshivatkozások** hivatkozást jelenítenek meg az oldal tetején. Ez használható a szolgáltatás-specifikus webes felület eléréséhez, például:

* **Feladatok előzményei** – MapReduce-feladatok előzményei.
* **Resource Manager** – a fonal erőforráskezelő felhasználói felülete.
* **NameNode** -Hadoop ELOSZTOTT fájlrendszer (HDFS) NameNode felhasználói felülete.
* **Oozie webes felhasználói felület** – Oozie felhasználói felület.

A hivatkozások bármelyikének kiválasztásával megnyílik egy új lap a böngészőben, amely megjeleníti a kijelölt lapot.

> [!NOTE]  
> Ha egy szolgáltatáshoz tartozó **Gyorshivatkozások** bejegyzést választja, a "kiszolgáló nem található" hibaüzenetet adhat vissza. Ha ezt a hibát tapasztalja, egy SSH-alagutat kell használnia, ha a **gyors hivatkozások** bejegyzést használja ehhez a szolgáltatáshoz. További információ: az [SSH-alagút használata a HDInsight](hdinsight-linux-ambari-ssh-tunnel.md)

## <a name="management"></a>Kezelés

### <a name="ambari-users-groups-and-permissions"></a>Felhasználók, csoportok és engedélyek Ambari

A felhasználók, csoportok és engedélyek használata a [tartományhoz csatlakoztatott](./domain-joined/hdinsight-security-overview.md) HDInsight-fürt használata esetén támogatott. A tartományhoz csatlakoztatott fürtök Ambari-kezelési felhasználói felületének használatával kapcsolatos információkért lásd: [tartományhoz csatlakoztatott HDInsight-fürtök kezelése](./domain-joined/hdinsight-security-overview.md).

> [!WARNING]  
> Ne változtassa meg a Ambari watchdog (hdinsightwatchdog) jelszavát a Linux-alapú HDInsight-fürtön. A jelszó módosítása megszakítja a parancsfájl-műveletek használatát, vagy skálázási műveleteket hajt végre a fürtön.

### <a name="hosts"></a>Gazdagépek

A **gazdagépek** lapon a fürtben található összes gazdagép szerepel. A gazdagépek kezeléséhez kövesse az alábbi lépéseket.

![gazdagépek lapja](./media/hdinsight-hadoop-manage-ambari/hdinsight-hosts-page.png)

> [!NOTE]  
> A gazdagépek hozzáadása, leszerelése és újraüzembe helyezése nem használható HDInsight-fürtökkel.

1. Válassza ki a kezelni kívánt gazdagépet.

2. A **műveletek** menüben válassza ki a végrehajtani kívánt műveletet:

    |Elem |Leírás |
    |---|---|
    |Az összes összetevő elindítása|Indítsa el az összes összetevőt a gazdagépen.|
    |Az összes összetevő leállítása|Állítsa le az összes összetevőt a gazdagépen.|
    |Az összes összetevő újraindítása|Állítsa le és indítsa el az összes összetevőt a gazdagépen.|
    |Karbantartási mód bekapcsolása|Letiltja a gazdagép riasztásait. Ezt a módot akkor kell engedélyezni, ha riasztásokat létrehozó műveleteket hajt végre. Például leállíthatja és elindíthatja a szolgáltatást.|
    |Karbantartási mód kikapcsolása|A gazdagépet a normál riasztáshoz adja vissza.|
    |Leállítás|Leállítja a DataNode vagy a Csomópontkezelők a gazdagépen.|
    |Start|Elindítja a DataNode vagy a Csomópontkezelők a gazdagépen.|
    |Újraindítás|Leállítja és elindítja a DataNode vagy a Csomópontkezelők a gazdagépen.|
    |Leszerelése|Eltávolít egy gazdagépet a fürtből. **Ne használja ezt a műveletet a HDInsight-fürtökön.**|
    |Recommission|Egy korábban leszerelt gazdagép hozzáadását a fürthöz. **Ne használja ezt a műveletet a HDInsight-fürtökön.**|

### <a id="service"></a>Services

Az **irányítópult** vagy **szolgáltatások** lapon a szolgáltatások listájának alján található **műveletek** gomb használatával állítsa le és indítsa el az összes szolgáltatást.

![szolgáltatási műveletek](./media/hdinsight-hadoop-manage-ambari/service-actions.png)

> [!WARNING]  
> A **Hozzáadási szolgáltatás** megjelenik ebben a menüben, ezért nem használható szolgáltatások hozzáadására a HDInsight-fürthöz. Az új szolgáltatásokat a fürt üzembe helyezése során parancsfájl-művelettel kell hozzáadni. A parancsfájl-műveletek használatával kapcsolatos további információkért lásd: [HDInsight-fürtök testreszabása parancsfájl-műveletek használatával](hdinsight-hadoop-customize-cluster-linux.md).

Míg a **műveletek** gomb újraindíthatja az összes szolgáltatást, gyakran egy adott szolgáltatás indítását, leállítását vagy újraindítását is elvégezheti. Az alábbi lépések végrehajtásával hajthat végre műveleteket egy adott szolgáltatáson:

1. Az **irányítópult** vagy **szolgáltatások** lapon válasszon ki egy szolgáltatást.

2. Az **Összefoglalás** lap tetején kattintson a **szolgáltatási műveletek** gombra, és válassza ki az elvégzendő műveletet. Ezzel újraindítja a szolgáltatást az összes csomóponton.

    ![szolgáltatási művelet](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

   > [!NOTE]  
   > Bizonyos szolgáltatások újraindítása, amíg a fürt fut, riasztásokat hozhatnak. A riasztások elkerülése érdekében a **szolgáltatás műveletei** gomb használatával engedélyezheti a **karbantartási módot** a szolgáltatás számára az újraindítás előtt.

3. A művelet kiválasztását követően az oldal tetején lévő **# op** bejegyzés megnöveli a háttérben futó művelet megjelenítését. Ha a megjelenítésre van konfigurálva, megjelenik a háttérben futó műveletek listája.

   > [!NOTE]  
   > Ha engedélyezte a **karbantartási módot** a szolgáltatáshoz, ne felejtse el letiltani a **szolgáltatás műveletei** gomb használatával a művelet befejeződése után.

A szolgáltatás konfigurálásához kövesse az alábbi lépéseket:

1. Az **irányítópult** vagy **szolgáltatások** lapon válasszon ki egy szolgáltatást.

2. Válassza ki a **Configs** fülre. Megjelenik az aktuális konfiguráció. Megjelenik a korábbi konfigurációk listája is.

    ![konfigurációk](./media/hdinsight-hadoop-manage-ambari/service-configs.png)

3. A megjelenített mezők használatával módosítsa a konfigurációt, majd válassza a **Mentés**lehetőséget. Vagy válasszon egy korábbi konfigurációt, majd válassza az **aktuális** lehetőséget az előző beállításokra való visszalépéshez.

## <a name="ambari-views"></a>Ambari Views

A Ambari nézetek lehetővé teszik a fejlesztők számára a felhasználói felületi elemek csatlakoztatását a Ambari webes felhasználói felületéhez az [Apache Ambari views keretrendszer](https://cwiki.apache.org/confluence/display/AMBARI/Views)használatával. A HDInsight a következő nézeteket biztosítja a Hadoop-fürtökhöz:

* Struktúra nézet: A kaptár nézet lehetővé teszi, hogy közvetlenül a webböngészőből futtasson kaptár-lekérdezéseket. Mentheti a lekérdezéseket, megtekintheti az eredményeket, mentheti az eredményeket a fürt tárolójába, vagy letöltheti az eredményeket a helyi rendszeren. A kaptár-nézetek használatáról további információt a [Apache Hive nézetek használata a HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)című témakörben talál.

* Tez nézet: A TEZ nézet lehetővé teszi a feladatok jobb megismerését és optimalizálását. Megtekintheti a TEZ feladatok végrehajtásának és az erőforrások használatának módját.

## <a name="unsupported-operations"></a>Nem támogatott műveletek

A következő Ambari műveletek nem támogatottak a HDInsight:

* __A metrikák gyűjtő szolgáltatásának áthelyezése__. A metrika-gyűjtő szolgáltatás információinak megtekintésekor a szolgáltatási műveletek menüben elérhető műveletek egyike a __metrika-gyűjtő mozgatása__. Ez a HDInsight nem támogatott.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan használhatja az [Apache Ambari Rest APIt](hdinsight-hadoop-manage-ambari-rest-api.md) a HDInsight használatával.