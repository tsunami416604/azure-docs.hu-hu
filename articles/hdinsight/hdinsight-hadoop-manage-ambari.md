---
title: Megfigyelés és kezelés Azure HDInsight Ambari webes felhasználói felületen |} Microsoft Docs
description: Megtudhatja, hogyan figyelheti és Linux-alapú HDInsight-fürtök kezelése az Ambari használatával. Ebben a dokumentumban megismerheti, hogyan használható az Ambari webes felhasználói felületén a HDInsight-fürtök részét képező.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 4787f3cc-a650-4dc3-9d96-a19a67aad046
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/23/2018
ms.author: larryfr
ms.openlocfilehash: 922dfd475f27f182d8958887087fc2f4945cc43a
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="manage-hdinsight-clusters-by-using-the-ambari-web-ui"></a>A HDInsight-fürtök kezelése az Ambari webes felhasználói felület használatával

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari egyszerűbbé teszi a felügyeleti és a Hadoop-fürthöz, adja meg a webes felhasználói felület és a REST API használatát egy könnyen ellenőrzésére. Ambari Linux-alapú HDInsight-fürtök része, és figyelheti a fürt és a konfigurációs módosításokat.

Ez a dokumentum elsajátíthatja az Ambari webes felhasználói felület használata a HDInsight-fürtöt.

## <a id="whatis"></a>Mi az az Ambari?

[Apache Ambari](http://ambari.apache.org) egy könnyen használható webes felhasználói felület biztosításával egyszerűsíti a Hadoop kezelését. Ambari használatával kezelni és megfigyelni a Hadoop-fürtök. A fejlesztők integrálható a ezeket a képességeket a alkalmazások használatával a [Ambari REST API-k](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Az Ambari webes felhasználói felület alapértelmezés szerint a HDInsight-fürtök, a Linux operációs rendszert használó valósul meg.

> [!IMPORTANT]
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement). 

## <a name="connectivity"></a>Kapcsolatok

Az Ambari webes felhasználói felület érhető el, a HDInsight-fürt HTTPS://CLUSTERNAME.azurehdinsight.net, ahol **CLUSTERNAME** a fürt neve.

> [!IMPORTANT]
> A HDInsight Ambari való csatlakozás igényel a HTTPS PROTOKOLLT. Amikor a rendszer, használja a rendszergazda fiók nevét és a fürt létrehozásakor megadott jelszóval.

## <a name="ssh-tunnel-proxy"></a>SSH-alagutat (proxy)

A fürt Ambari közvetlenül az interneten keresztül érhető el, amíg az Ambari webes felhasználói felület (például a Hadoopból) az egyes hivatkozások nem érhetők el az interneten. Ezek a szolgáltatások eléréséhez az SSH-alagút kell létrehoznia. További információkért lásd: [használata SSH Tunneling hdinsight](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="ambari-web-ui"></a>Ambari webes felhasználói felület

> [!WARNING]
> Nem minden az Ambari webes felhasználói felület támogatja a hdinsight platformon. További információkért lásd: a [nem támogatott műveletek](#unsupported-operations) szakasz ebben a dokumentumban.

Az Ambari webes felhasználói felületén történő csatlakozáskor kéri, hogy a lap hitelesítést. A fürt rendszergazdai jogú felhasználó (alapértelmezett Admin) és a fürt létrehozása során használt jelszó használata.

Az oldal megnyitása, vegye figyelembe a sáv felső részén. A sáv a következő információ és szabályozza tartalmazza:

![ambari-nav](./media/hdinsight-hadoop-manage-ambari/ambari-nav.png)

* **Ambari embléma** -megnyitja az irányítópultot, amely a fürt figyelésére használható.

* **A fürt neve # ops** -Ambari folyamatban lévő műveletek számát jeleníti meg. A fürt nevét vagy **# ops** háttérbeli műveletek listáját jeleníti meg.

* **# riasztások** -figyelmeztetés vagy kritikus riasztás, ha vannak ilyenek, a fürt jeleníti meg.

* **Irányítópult** -jeleníti meg az irányítópulton.

* **Szolgáltatások** -információkat és a konfigurációs beállítások, a fürt számára.

* **Gazdagépek** -információkat és a konfigurációs beállítások, a fürt csomópontjaihoz.

* **Riasztások** -adatokat, figyelmeztetéseket és a kritikus riasztások naplóját.

* **Felügyeleti** -verem/szoftverszolgáltatások a fürt, a szolgáltatás fiókjának adatait, és a Kerberos biztonsági telepített.

* **Felügyeleti gomb** -Ambari felügyeleti, a felhasználói beállításokat és kijelentkezési.

## <a name="monitoring"></a>Figyelés

### <a name="alerts"></a>Riasztások

Az alábbi lista tartalmazza az Ambari által használt közös riasztási állapot:

* **OK**
* **Warning**
* **CRITICAL**
* **ISMERETLEN**

Riasztások eltérő **OK** okozhat a **# riasztások** bejegyzés a riasztások számát jeleníti meg a lap tetején. Ez a bejegyzés látható értesítések valamelyikének kiválasztásakor a riasztások és azok állapotát.

Riasztások vannak szervezve több alapértelmezett csoport, amely tekintheti meg a **riasztások** lap.

![Riasztások lap](./media/hdinsight-hadoop-manage-ambari/alerts.png)

A csoportok segítségével kezelheti a **műveletek** menüben, majd válassza **riasztási csoportok kezelése**.

![riasztási csoportok párbeszédpanel kezelése](./media/hdinsight-hadoop-manage-ambari/manage-alerts.png)

Is kezelheti a riasztási módszerek, és a riasztási értesítések létrehozása a **műveletek** menü kiválasztásával __riasztás értesítések kezelésére__. Aktuális értesítések jelennek meg. Itt értesítéseket is létrehozhat. Keresztül is elküldhetik az értesítéseket **E-mail** vagy **SNMP** Ha adott riasztás/súlyossági kombinációk történik. Például minden egyes a riasztásoknak az e-mailt küldhet a **YARN alapértelmezett** csoport beállítása **kritikus**.

![Hozzon létre figyelmeztető párbeszédpanel](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

Végezetül kiválasztásával __riasztást beállításainak kezelése__ a a __műveletek__ menü lehetővé teszi, hogy meg kell adnia a szám, ahányszor egy riasztás akkor fordulhat elő, egy értesítés elküldése előtt. Ez a beállítás segítségével használatának megakadályozása értesítések átmeneti hibák esetén.

### <a name="cluster"></a>Fürt

A **metrikák** az irányítópult widgetek, amely megkönnyíti a fürt egy pillantással állapotának figyelésére tartalmazza. Több widgets, például a **CPU-használat**, kattintáskor további információkkal.

![a metrikák irányítópult](./media/hdinsight-hadoop-manage-ambari/metrics.png)

A **Heatmaps** lapon színes heatmaps, piros a zöld lesz metrikákat jeleníti meg.

![heatmaps irányítópult](./media/hdinsight-hadoop-manage-ambari/heatmap.png)

További információ a fürtön belüli csomópontokon, válassza ki a **állomások**. Ezután válassza ki az adott csomópont érdekli.

![állomás részletei](./media/hdinsight-hadoop-manage-ambari/host-details.png)

### <a name="services"></a>Szolgáltatások

A **szolgáltatások** oldalsávon az irányítópulton a fürtben futó szolgáltatások állapotának gyors betekintést nyújt. Különböző ikonok használatosak állapot vagy a végrehajtandó műveleteket. Például egy sárga újrahasznosítást szimbólum jelenik meg, ha egy szolgáltatásnak kell újrahasznosításra kerül.

![szolgáltatások oldalsó sáv](./media/hdinsight-hadoop-manage-ambari/service-bar.png)

> [!NOTE]
> Megjelenik a szolgáltatások HDInsight-fürttípusok és verziója eltérő. Itt látható a szolgáltatások eltérhetnek a szolgáltatások jelenik meg a fürt számára.

A szolgáltatás látható értesítések valamelyikének kiválasztásakor további részletes információk a szolgáltatásban.

![szolgáltatás összefoglaló információk](./media/hdinsight-hadoop-manage-ambari/service-details.png)

#### <a name="quick-links"></a>Gyorshivatkozások

Egyes szolgáltatások megjelenítése egy **Gyorshivatkozások** az oldal tetején. Ez a szolgáltatásspecifikus web UI, például a eléréséhez használható:

* **Feladatelőzmények** -MapReduce-feladatok előzményeinek.
* **Erőforrás-kezelő** -YARN erőforrás-kezelő felhasználói felületén.
* **NameNode** -Hadoop elosztott fájl System (HDFS) NameNode felhasználói felület.
* **Oozie webes felhasználói felület** -Oozie felhasználói felület.

Új lap ezeket a hivatkozásokat kiválasztásával megnyílik a böngészőben, amely a kijelölt oldalát jeleníti meg.

> [!NOTE]
> Válassza a **Gyorshivatkozások** egy szolgáltatás bejegyzése térhetnek vissza egy "a kiszolgáló nem található" hiba. Ha ezt a hibát észlel, használnia kell az SSH-alagút használata esetén a **Gyorshivatkozások** bejegyzés ezt a szolgáltatást. További információ: [használata SSH Tunneling a hdinsight eszközzel](hdinsight-linux-ambari-ssh-tunnel.md)

## <a name="management"></a>Kezelés

### <a name="ambari-users-groups-and-permissions"></a>Ambari felhasználók, csoportok és engedélyek

Felhasználók, csoportok és engedélyek végzett használata esetén támogatottak. egy [tartományhoz csatlakoztatott](./domain-joined/apache-domain-joined-introduction.md) HDInsight-fürthöz. A felhasználói felületen az Ambari felügyeleti tartományhoz fürt információkért lásd: [tartományhoz HDInsight-fürtök kezelése](./domain-joined/apache-domain-joined-introduction.md).

> [!WARNING]
> Ne változtassa meg jelszavát a Linux-alapú HDInsight-fürtök az Ambari figyelő (hdinsightwatchdog). A jelszó módosítása megsérti a Parancsfájlműveletek vagy méretezési műveleteket a fürthöz.

### <a name="hosts"></a>Hosts

A **állomások** lap felsorolja a fürt összes gazdagépén. Gazdagép kezeléséhez kövesse az alábbi lépéseket.

![gazdagépek lap](./media/hdinsight-hadoop-manage-ambari/hosts.png)

> [!NOTE]
> Hozzáadása, leszerelése és recommissioning egy gazdagép nem használható a HDInsight-fürtök.

1. Válassza ki, amelyet felügyelni kíván.

2. Használja a **műveletek** menüre kattintva jelölje ki az elvégezni kívánt műveletet:

   * **Indítsa el az összes összetevő** -összetevők elindítja a gazdagépen.

   * **Állítsa le az összes összetevő** -összetevők leállításához a gazdagépen.

   * **Indítsa újra az összes összetevő** - Stop, és indítsa el az összes összetevő a gazdagépen.

   * **Kapcsolja be a karbantartási mód** -riasztások mellőzi a gazdagép. Hozzon létre riasztást műveleteket hajtja végre ezt a módot kell engedélyezni. Például a szolgáltatás indítása és leállítása.

   * **Kapcsolja ki a karbantartási mód** – a gazdagép normál riasztási adja vissza.

   * **Állítsa le** -leállítja DataNode vagy NodeManagers a gazdagépen.

   * **Start** -DataNode kezdődik vagy NodeManagers a gazdagépen.

   * **Indítsa újra a** -leáll, és elindítja DataNode vagy NodeManagers a gazdagépen.

   * **Szerelje le** -állomás eltávolítja a fürtből.

     > [!NOTE]
     > A HDInsight-fürtökön ne használja ezt a műveletet.

   * **Recommission** -ad hozzá egy korábban már leszerelt állomást a fürthöz.

     > [!NOTE]
     > A HDInsight-fürtökön ne használja ezt a műveletet.

### <a id="service"></a>Services

Az a **irányítópult** vagy **szolgáltatások** oldalon a **műveletek** szolgáltatások leállítása és elindítása az összes szolgáltatás a lista alján gombra.

![szolgáltatás műveletek](./media/hdinsight-hadoop-manage-ambari/service-actions.png)

> [!WARNING]
> Amíg **hozzáadása szolgáltatás** szerepel ebben a menüben szereplő, nem használható a szolgáltatások a HDInsight-fürthöz. Új szolgáltatások fel kell venni, hogy egy parancsfájl műveletével a fürtök kiépítése során. Parancsfájlműveletek használatával kapcsolatos további információkért lásd: [testreszabása HDInsight-fürtök Parancsfájlműveletek segítségével](hdinsight-hadoop-customize-cluster-linux.md).

Amíg a **műveletek** gomb újraindíthatja az összes szolgáltatás, gyakran szeretné indítása, leállítása és újraindítása egy adott szolgáltatáshoz. Az alábbi lépések segítségével elvégezheti a kapcsolódó műveleteket egy szolgáltatás:

1. Az a **irányítópult** vagy **szolgáltatások** lapon, válassza ki a szolgáltatást.

2. A felső részén a **összegzés** lapon a **szolgáltatás műveletek** gombra, majd válassza ki a végrehajtandó műveletet. Ezzel újraindul a szolgáltatás az összes olyan csomóponton.

    ![szolgáltatási művelet](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

   > [!NOTE]
   > Egyes szolgáltatások újraindítása, a fürt futása közben riasztásokat hozhat létre. Riasztások elkerülése érdekében használja a **szolgáltatás műveletek** gomb engedélyezése **karbantartási módba** végrehajtása az újraindítás előtt a szolgáltatás.

3. Egy műveletet a kijelölt a **# op** bejegyzés felső részén a lap lépésekkel jeleníthető meg, hogy a háttérművelet folyamatban van. Konfigurált megjelenítéséhez, ha a háttérbeli műveletek listája jelenik meg.

   > [!NOTE]
   > Ha engedélyezte a **karbantartási módba** a szolgáltatáshoz, ne felejtse el használatával tiltsa le a **szolgáltatás műveletek** gombra kattint, a művelet befejeződése után.

Szolgáltatás konfigurálásához tegye a következőket:

1. Az a **irányítópult** vagy **szolgáltatások** lapon, válassza ki a szolgáltatást.

2. Válassza ki a **Configs** fülre. A jelenlegi konfiguráció jelenik meg. A fenti konfiguráció listáját is megjelenik.

    ![configurations](./media/hdinsight-hadoop-manage-ambari/service-configs.png)

3. A megjelenített módosítsa a konfigurációt, és válassza ki a mezők **mentése**. Vagy válassza ki az előző konfigurációt, és válassza ki **ellenőrizze aktuális** szeretné visszaállítani az előző beállítások.

## <a name="ambari-views"></a>Az Ambari nézetek

Az Ambari nézetek a fejlesztők olyan felhasználói felületi elemei csatlakoztassa az Ambari webes felhasználói felület használata a [Ambari nézetek keretrendszer](https://cwiki.apache.org/confluence/display/AMBARI/Views). HDInsight Hadoop-fürt típusú a következő nézeteket biztosítja:

* Yarn várólista-kezelő: A várólista-kezelő egy egyszerű felhasználói Felületet biztosít megtekintése és módosítása a YARN várólisták.

* Hive nézete: A Hive nézet lehetővé teszi, hogy közvetlenül a böngészőből Hive-lekérdezések futtatásához. Lekérdezések mentése, eredmények megtekintése, az eredmények mentése a fürttároló vagy eredmények letöltése a helyi rendszer. A Hive-nézetek használata további információkért lásd: [Hive nézetek és a HDInsight együttes](hadoop/apache-hadoop-use-hive-ambari-view.md).

* Tez nézet: A Tez nézet lehetővé teszi, hogy jobban megérteni, és optimalizálja a feladatok. A Tez feladatok végrehajtásának módját, és milyen erőforrásokat használt információk is megtekinthetők.

## <a name="unsupported-operations"></a>Nem támogatott műveletek

A következő Ambari műveletek nem támogatottak a HDInsight:

* __Helyezze át a metrikákat gyűjtő szolgáltatásának__. A metrikák adatgyűjtő szolgáltatás adatokat megtekintésekor a műveletek a szolgáltatás műveletek menüjében egyik __áthelyezése metrikákat gyűjtő__. Ez nem támogatott a hdinsight eszközzel.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan használható a [Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md) a hdinsight eszközzel.