---
title: Figyelése és felügyelete az Azure HDInsight az Ambari webes felhasználói felülettel
description: Ismerje meg, hogyan figyelheti és Linux-alapú HDInsight-fürtök kezelése az Ambari használatával. Ebben a dokumentumban megismerheti, hogyan használhatja a mellékelt a HDInsight-fürtök az Ambari webes felhasználói Felületet.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 738ef5df0b2e2a7f31a7316a1d2ef4395168d41e
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57576956"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-web-ui"></a>HDInsight-fürtök kezelése az Apache Ambari webes felhasználói felület használatával

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Az Apache Ambari leegyszerűsíti a kezelése és figyelése Apache Hadoop-fürt azáltal, hogy egy könnyen használható webes felhasználói felületen és a REST API-t. Az Ambari Linux-alapú HDInsight-fürtökön része, és a fürt monitorozására és konfigurációs módosításokat.

Ebből a dokumentumból megismerheti, hogyan az Ambari webes felhasználói felület használata egy HDInsight-fürtön.

## <a id="whatis"></a>Mi az Apache Ambari?

[Az Apache Ambari](https://ambari.apache.org) egyszerűbbé teszi a Hadoop-kezelés azáltal, hogy egy könnyen használható webes felhasználói felületen. Felügyelheti és figyelheti a Hadoop-fürtök az Ambari segítségével. A fejlesztők beépíthetik ezeket a képességeket alkalmazásaikban használatával a [az Ambari REST API-k](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Az Ambari webes felhasználói Felületet biztosít a Linux operációs rendszert használó HDInsight-fürtök az alapértelmezett.

> [!IMPORTANT]  
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement). 

## <a name="connectivity"></a>Kapcsolatok

Az Ambari webes felhasználói felület érhető el, a HDInsight-fürt HTTPS://CLUSTERNAME.azurehdinsight.net, ahol **CLUSTERNAME** a fürt neve.

> [!IMPORTANT]  
> Csatlakozás a HDInsight az Ambari HTTPS van szüksége. Amikor a rendszer, használja a rendszergazdai fiók nevét és a fürt létrehozásakor megadott jelszót.

## <a name="ssh-tunnel-proxy"></a>SSH-alagutat (proxy)

A fürt Ambari közvetlenül az interneten keresztül érhető el, amíg a Ambari webes felhasználói felületen (például a JobTracker) egyes hivatkozások nem lesznek közzétéve az interneten. Ezek a szolgáltatások eléréséhez, létre kell hoznia egy SSH-alagutat. További információkért lásd: [SSH-bújtatással való HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="ambari-web-ui"></a>Ambari Web UI

> [!WARNING]  
> HDInsight az Ambari webes felhasználói felület nem minden funkcióját támogatottak. További információkért lásd: a [nem támogatott műveletek](#unsupported-operations) szakasz ebben a dokumentumban.

Az Ambari webes Kezelőfelületen való csatlakozáskor az oldal hitelesítésre kéri. Használja a fürt rendszergazdai felhasználói (alapértelmezett rendszergazdai) és a fürt létrehozásakor használt jelszó.

Amikor megnyílik az oldal, vegye figyelembe a felső sávon. A sáv tartalmazza a következő információkat és vezérlők:

![ambari-nav](./media/hdinsight-hadoop-manage-ambari/ambari-nav.png)

* **Az Ambari embléma** – megnyitja az irányítópultot, amely a fürt monitorozására használható.

* **A fürt neve # ops** -Ambari folyamatban lévő műveletek számát jeleníti meg. A fürt nevének kijelölésekor vagy **# ops** háttérbeli műveletek listáját jeleníti meg.

* **# riasztás** -jelenít meg figyelmeztetést vagy kritikus riasztás, ha bármely, a fürt számára.

* **Irányítópult** -jeleníti meg az irányítópultot.

* **Szolgáltatások** – információ és a konfigurációs beállítások a szolgáltatások a fürtben.

* **Gazdagépek** – információ és a konfigurációs beállítások a csomópontok a fürtben.

* **Riasztások** -adatokat, figyelmeztetéseket és a kritikus riasztások naplóját.

* **Rendszergazdai** -verem és szolgáltatásokat a fürt szolgáltatásfiók-adatokat és a Kerberos biztonsági telepített.

* **Rendszergazdai gomb** -Ambari felügyeleti, a felhasználói beállításokat és a kijelentkezési.

## <a name="monitoring"></a>Figyelés

### <a name="alerts"></a>Riasztások

Az alábbi lista tartalmazza az Ambari által használt gyakori riasztási állapotok:

* **OK**
* **Figyelmeztetés**
* **CRITICAL**
* **ISMERETLEN**

Más, a riasztások **OK** okozhat a **# riasztás** bejegyzés riasztások számát jeleníti meg a lap tetején. Ez a bejegyzés kiválasztása a riasztások és azok állapotát jeleníti meg.

Riasztások több alapértelmezett csoport, amely tekinthetők vannak szervezve a **riasztások** lapot.

![Riasztások lap](./media/hdinsight-hadoop-manage-ambari/alerts.png)

A csoportok használatával kezelheti a **műveletek** menüben, majd válassza **riasztási csoportok kezelése**.

![értesítési csoportok párbeszédpanelen kezelése](./media/hdinsight-hadoop-manage-ambari/manage-alerts.png)

Emellett riasztási módok kezelése, és hozhat létre riasztási értesítéseit a **műveletek** menü kiválasztásával __riasztás értesítések kezelése__. Aktuális értesítések jelennek meg. Itt értesítéseket is létrehozhat. Az értesítések is küldhetők keresztül **E-mail** vagy **SNMP** amikor adott riasztás/súlyossági kombinációk fordulhat elő. Például ha bármelyik a riasztásoknak az e-mailt küldhet a **YARN alapértelmezett** tulajdonoscsoportja **kritikus**.

![Figyelmeztető párbeszédpanel létrehozása](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

Végül válassza __riasztás beállításainak kezelése__ származó a __műveletek__ menü lehetővé teszi, hogy hányszor riasztást kell történnie, egy értesítés elküldése előtt. Ezt a beállítást, hogy az értesítések átmeneti hibák esetén használható.

### <a name="cluster"></a>Fürt

A **metrikák** az irányítópult lap tartalmaz, amelyek megkönnyítik a fürt egyetlen pillantással állapotának figyelése widgetek sorozata. Több widgetet, például **CPU-használat**, ha rákattintunk olvashat további információkat.

![metrika az irányítópulton](./media/hdinsight-hadoop-manage-ambari/metrics.png)

A **Intenzitástérképek** lapon színes intenzitástérképek, vörös, zöld azzal metrikákat jeleníti meg.

![az irányítópulton intenzitástérképek](./media/hdinsight-hadoop-manage-ambari/heatmap.png)

További információ a fürtön belül a csomópontokon, válassza ki a **gazdagépek**. Ezután válassza ki az Önt érdeklő adott csomópont.

![gazdagép részletei](./media/hdinsight-hadoop-manage-ambari/host-details.png)

### <a name="services"></a>Szolgáltatások

A **szolgáltatások** az irányítópulton lévő oldalsávon a fürtben futó szolgáltatások állapotának gyors betekintést nyújt. Állapot vagy a végrehajtandó műveleteket hajtson végre a különféle ikonok használatosak. Például egy sárga újrahasznosítás szimbólum jelenik meg, ha egy szolgáltatás újraindítására van szükség.

![szolgáltatások oldalsó sáv](./media/hdinsight-hadoop-manage-ambari/service-bar.png)

> [!NOTE]  
> Megjelenik a szolgáltatások HDInsight-fürttípusok és verziója eltérő. Itt jelenik meg a szolgáltatások, mint a szolgáltatások jelenik meg a fürt eltérő lehet.

További információt a szolgáltatás a szolgáltatás kiválasztása jeleníti meg.

![Service összefoglaló információk](./media/hdinsight-hadoop-manage-ambari/service-details.png)

#### <a name="quick-links"></a>Gyorshivatkozások

Egyes szolgáltatások megjelenített egy **Gyorshivatkozások** hivatkozásra az oldal tetején. Szolgáltatásspecifikus webes, mint például eléréséhez használható:

* **Feladatelőzmények** -MapReduce-feladatok előzményeinek.
* **Erőforrás-kezelő** -YARN ResourceManager felhasználói felülete.
* **NameNode** – Hadoop Distributed File System (HDFS) NameNode felhasználói felületén.
* **Az Oozie webes felhasználói felületen** -Oozie felhasználói felületén.

Az alábbi hivatkozások kiválasztásával megnyílik egy új lap a böngészőben, amely a kijelölt oldal megjeleníti.

> [!NOTE]  
> Válassza a **Gyorshivatkozások** bejegyzés egy szolgáltatáshoz lehet, hogy "a kiszolgáló nem található" hibaüzenetet ad vissza. Ez a hiba jelentkezik, ha az SSH-alagút használata esetén kell használnia a **Gyorshivatkozások** bejegyzés ezt a szolgáltatást. További információ: [SSH-bújtatással való HDInsight](hdinsight-linux-ambari-ssh-tunnel.md)

## <a name="management"></a>Kezelés

### <a name="ambari-users-groups-and-permissions"></a>Ambari-felhasználók, csoportok és engedélyek

A felhasználók, csoportok és engedélyek működő támogatottak használata esetén egy [tartományhoz csatlakoztatott](./domain-joined/apache-domain-joined-introduction.md) HDInsight-fürt. Az Ambari Management UI használatával egy tartományhoz csatlakozó fürtön információkért lásd: [tartományhoz csatlakoztatott HDInsight-fürtök kezelése](./domain-joined/apache-domain-joined-introduction.md).

> [!WARNING]  
> Ne módosítsa a jelszavát a Linux-alapú HDInsight-fürt Ambari figyelő (hdinsightwatchdog). A jelszó módosítása működésképtelenné válik a parancsfájlműveletekkel vagy a fürt skálázási műveleteket végez.

### <a name="hosts"></a>Hosts

A **gazdagépek** lap felsorolja a fürtben lévő minden gazdagép. Gazdagép kezeléséhez kövesse az alábbi lépéseket.

![gazdagépek lap](./media/hdinsight-hadoop-manage-ambari/hosts.png)

> [!NOTE]  
> Hozzáadása, a leszerelése és a egy gazdagép recommissioning nem használható a HDInsight-fürtökkel.

1. Válassza ki a kezelni kívánt gazdagépen.

2. Használja a **műveletek** menüre, és jelölje ki a végrehajtani kívánt műveletet:

   * **Indítsa el az összes összetevő** -indítsa el az összes összetevő a gazdagépen.

   * **Az összes összetevő leállítása** – az összes összetevő leállítása a gazdagépen.

   * **Az összes összetevő újraindítása** – állítsa le és indítsa el az összes összetevő a gazdagépen.

   * **Karbantartási mód bekapcsolása** -elrejti a riasztásokat a gazdagép számára. Ebben a módban hajt végre műveleteket, amelyeket riasztást engedélyezni kell. Ha például egy szolgáltatás elindítása és leállítása.

   * **Karbantartási mód kikapcsolása** – a gazdagépet a szokásos riasztási adja vissza.

   * **Állítsa le** -DataNode leáll vagy NodeManagers a gazdagépen.

   * **Indítsa el** -DataNode elindul vagy NodeManagers a gazdagépen.

   * **Indítsa újra a** – leállítja és elindítja DataNode vagy NodeManagers a gazdagépen.

   * **Leszerelése** -állomás eltávolítja a fürtből.

     > [!NOTE]  
     > Ez a művelet ne használja a HDInsight-fürtökön.

   * **Recommission** -hozzáad egy korábban már leszerelt állomást a fürthöz.

     > [!NOTE]  
     > Ez a művelet ne használja a HDInsight-fürtökön.

### <a id="service"></a>Services

Az a **irányítópult** vagy **szolgáltatások** oldalon a **műveletek** gomb alsó részén állítsa le és indítsa el az összes szolgáltatás a szolgáltatások listájába.

![szolgáltatás műveletek](./media/hdinsight-hadoop-manage-ambari/service-actions.png)

> [!WARNING]  
> Miközben **– szolgáltatás hozzáadása** szerepel ebben a menüben, nem használható szolgáltatások hozzáadása a HDInsight-fürt. Kell hozzáadni az új szolgáltatások szkriptműveletekkel fürtök kiépítése során. Szkriptműveletek használatával kapcsolatos további információkért lásd: [testreszabása HDInsight-fürtök parancsfájlműveletekkel](hdinsight-hadoop-customize-cluster-linux.md).

Bár a **műveletek** gombra az összes szolgáltatás újraindításával, milyen gyakran szeretne indítása, leállítása vagy egy adott szolgáltatás újraindítása. Az alábbi lépések segítségével műveleteket hajthat végre egy adott szolgáltatás:

1. Az a **irányítópult** vagy **szolgáltatások** lapon, válassza ki a szolgáltatást.

2. A felső részén a **összefoglalás** lapján a **szolgáltatás műveletek** gombra, és válassza ki az elvégzendő műveletet. Ezzel újraindítja a szolgáltatás az összes csomópontra.

    ![szolgáltatási művelet](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

   > [!NOTE]  
   > Egyes szolgáltatások újraindítása a fürt futása közben is riasztást. Riasztások elkerülése érdekében használja a **szolgáltatás műveletek** engedélyezése gombra **karbantartási módba** végrehajtása az újraindítás előtt a szolgáltatás.

3. Művelet kiválasztása után a **# op** bejegyzés megjelenítése a háttérművelet történik, hogy a lap lépésekben tetején. Ha konfigurálva megjelenítéséhez, a háttérbeli műveletek listája jelenik meg.

   > [!NOTE]  
   > Ha engedélyezte a **karbantartási módba** a szolgáltatáshoz, ne felejtse el használatával tiltsa le azt a **szolgáltatás műveletek** gombra, miután a művelet befejeződött.

Egy szolgáltatás konfigurálásához használja az alábbi lépéseket:

1. Az a **irányítópult** vagy **szolgáltatások** lapon, válassza ki a szolgáltatást.

2. Válassza ki a **Configs** fülre. A jelenlegi konfiguráció jelenik meg. Az előző konfigurációk listáját is megjelenik.

    ![Konfigurációk](./media/hdinsight-hadoop-manage-ambari/service-configs.png)

3. Módosíthatja a konfigurációt, és válassza a megjelenő mezőket használja **mentése**. Vagy válassza ki az előző konfigurációt, és válassza **legyen ez az aktuális** visszaállítása az előző beállításokat.

## <a name="ambari-views"></a>Ambari Views

Ambari-nézetek lehetővé teszik a felhasználói felületi elemeket illeszkedik az Ambari webes felhasználói felület használatával a fejlesztők a [Apache Ambari-nézetek keretrendszer](https://cwiki.apache.org/confluence/display/AMBARI/Views). HDInsight Hadoop-fürt típusú a következő nézeteket biztosítja:


* Hive-nézet: A Hive-nézet lehetővé teszi, hogy a Hive-lekérdezések futtatása közvetlenül a webböngészőből. Mentheti a lekérdezéseket, eredmények megtekintése, az eredmények mentése a fürttároló vagy eredmények letöltése a helyi rendszerről. További információ a Hive-nézetek használata: [Apache Hive-nézetek a HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md).

* Tez megtekintése: A Tez nézet lehetővé teszi, hogy jobb megismerésében és a feladatok optimalizálása. Megtekintheti az információ a Tez-feladatok végrehajtásának módját, és milyen erőforrásokat használnak.

## <a name="unsupported-operations"></a>Nem támogatott műveletek

A következő Ambari műveletek nem támogatottak a HDInsight:

* __A metrikák gyűjtőszolgáltatás áthelyezése__. A metrikák adatgyűjtő szolgáltatás adatokat megtekintésekor a szolgáltatás műveletek menüből elérhető műveletek egyike __áthelyezése metrikákat gyűjtő__. Ez nem támogatott a HDInsight.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan használható a [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md) a HDInsight.
