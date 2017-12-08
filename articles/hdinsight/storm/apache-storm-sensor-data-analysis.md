---
title: "Az Apache Storm és HBase érzékelőadatok elemzése |} Microsoft Docs"
description: "Ismerje meg, hogyan csatlakozhat az Apache Storm egy virtuális hálózattal. Használjon Storm HBase használatával dolgozza az eseményközpontban lévő és jelenítheti meg azt a D3.js."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: a9a1ac8e-5708-4833-b965-e453815e671f
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/19/2017
ms.author: larryfr
ms.openlocfilehash: 87c2aece68c5de06d683abf971b6c7ccf7f67a54
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="analyze-sensor-data-with-apache-storm-event-hub-and-hbase-in-hdinsight-hadoop"></a>Apache Storm, az Event Hubs és a HBase a HDInsight (Hadoop) érzékelőadatok elemzése

Útmutató Apache Storm on HDInsight használatával dolgozza az Azure Event Hubs. Az adatok az Apache HBase a HDInsight majd tárolja, és ábrázolt D3.js használatával.

Ebben a dokumentumban használt Azure Resource Manager sablon bemutatja, hogyan hozzon létre több Azure-erőforrások erőforráscsoportban. A sablon egy Azure virtuális hálózatra, két HDInsight-fürtök (a Storm és HBase) és az Azure Web Apps hoz létre. A valós idejű webes irányítópult node.js végrehajtásának automatikusan telepítve van a webes alkalmazás.

> [!NOTE]
> A dokumentum és példa ebben a dokumentumban szereplő információk a HDInsight 3.6 verzió szükséges.
>
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="architecture"></a>Architektúra

![architektúra ábrája](./media/apache-storm-sensor-data-analysis/devicesarchitecture.png)

Ebben a példában a következő összetevőkből áll:

* **Az Azure Event Hubs**: az érzékelők összegyűjtött adatokat tartalmazza.
* **A HDInsight alatt futó Storm**: az Eseményközpont kiválasztásával valós idejű adatok feldolgozása biztosít.
* **A HDInsight HBase**: biztosítja a állandó NoSQL-adattár adatok feldolgozása a Storm után.
* **Az Azure virtuális hálózat szolgáltatás**: lehetővé teszi, hogy a HDInsight alatt futó Storm és HBase a HDInsight-fürtök közötti biztonságos kommunikációhoz.
  
  > [!NOTE]
  > A virtuális hálózatra azért szükség, a Java HBase ügyfél API használata esetén. Nincs felfedve, a HBase fürtök nyilvános átjárón keresztül. A HBase és a Storm-fürtök telepítését az azonos virtuális hálózatban lehetővé teszi a Storm-fürt (vagy a virtuális hálózaton lévő más rendszereit) közvetlen hozzáférést a HBase ügyfél API-val.

* **Irányítópult webhely**: egy példa-irányítópultot, amely valós idejű adatok diagramokat.
  
  * A webhely a node.js valósul meg.
  * [Socket.IO](http://socket.io/) valós idejű, a Storm-topológia és a webhely közötti kommunikációhoz használt.
    
    > [!NOTE]
    > Egy megvalósítási részletes Socket.io segítségével kommunikációra. Bármely kommunikációs keretrendszert, például a nyers websocket elemek vagy SignalR is használhatja.

  * [D3.js](http://d3js.org/) szolgál a webhely elküldött adatblokkhoz diagramot.

> [!IMPORTANT]
> Két fürt nem szükséges, mivel nincs támogatott módszer Storm és HBase egy HDInsight-fürt létrehozásához.

A topológia olvassa be az adatokat az Event Hubs használatával a `org.apache.storm.eventhubs.spout.EventHubSpout` osztály és HBase használatával írt adatok a `org.apache.storm.hbase.bolt.HBaseBolt` osztály. A webhely kommunikál végezhető el [socket.io-client.java](https://github.com/nkzawa/socket.io-client.java).

A következő ábra illusztrálja a topológia elrendezését:

![topológia ábrája](./media/apache-storm-sensor-data-analysis/sensoranalysis.png)

> [!NOTE]
> Ez az ábra a topológia egyszerűsített nézete. Az egyes összetevők példánya létrejön az Eseményközpont minden partícióján. Ezek a példányok elosztott a fürt csomópontjaihoz, és adatok között irányított azokat az alábbiak szerint:
> 
> * Az elemző a spout adatainak terhelésű.
> * Az irányítópult és a HBase az elemző adatainak Eszközazonosító, szerint van csoportosítva, így ugyanarra az eszközre érkező üzenetek mindig azonos összetevőnek.

### <a name="topology-components"></a>Topológia összetevők

* **Event Hub Spout**: A spout rendszer alatt futó Apache Storm-verziójú 0.10.0-s megadott és magasabb.
  
  > [!NOTE]
  > Az ebben a példában használt Event Hub spout egy Storm on HDInsight-fürt verziószáma 3.5-ös vagy 3.6 igényel.

* **ParserBolt.java**: az adatok a spout által kibocsátott nyers JSON, és esetenként egynél több esemény is ki lesz adva egyszerre. Tartalmaz a bolt beolvassa a spout által kibocsátott adatokról, és elemzi a JSON-üzenet. A bolt több mezőt tartalmazó rekordot, majd az adatok bocsát ki.
* **DashboardBolt.java**: Ez az összetevő bemutatja, hogyan kell a Socket.io ügyféloldali kódtára a Java használatával valós idejű adatokat küldeni a webes irányítópult.
* **nem-hbase.yaml**: helyi módban történő futtatásakor használt topológia meghatározása. A HBase-összetevők nem használ.
* **a hbase.yaml**: használható, ha a topológia a fürtben futó topológia meghatározása. Az a HBase összetevőket használnak.
* **dev.Properties**: A konfigurációs adatokat az Event Hub spout, a HBase bolt és az Irányítópult-összetevők.

## <a name="prepare-your-environment"></a>A környezet előkészítése

Mielőtt használná az ebben a példában, elő kell készítenie a fejlesztési környezetet. Is létre kell hoznia egy Azure Eseményközponthoz, amelyet ez a példa használ.

A fejlesztési környezet telepíthető a következőkre lesz szüksége:

* [NODE.js](http://nodejs.org/): a webes irányítópult helyileg a fejlesztési környezetet az előzetes használt.
* [Java és a JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/index.html): a Storm-topológiák fejlesztése használatával.
* [Maven](http://maven.apache.org/what-is-maven.html): build és a projekt lefordítása használt.
* [Git](http://git-scm.com/): tölthető le a projektet a Githubról.
* Egy **SSH** ügyfél: a Linux-alapú HDInsight-fürtök való kapcsolódáshoz használt. További információ: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

Létrehoz egy eseményközpontot, kövesse a lépéseket a [létrehoz egy eseményközpontot](../../event-hubs/event-hubs-create.md) dokumentum.

> [!IMPORTANT]
> A RootManageSharedAccessKey menteni az eseményközpont neveként, a névtér és a kulcsot. Ez az információ a Storm-topológia konfigurálására szolgál.

Nem kell egy HDInsight-fürtre. A jelen dokumentumban leírt lépések adjon meg egy Azure Resource Manager-sablon által létrehozott ebben a példában szükséges erőforrásokat. A sablont hoz létre a következőket:
 
* Egy Azure virtuális hálózat
* A Storm on HDInsight-fürt (Linux-alapú, két munkavégző csomópontokhoz)
* HBase on HDInsight-fürt (Linux-alapú, két munkavégző csomópontokhoz)
* Az Azure Web Apps, amelyen a webhely irányítópult

## <a name="download-and-configure-the-project"></a>Töltse le és a projekt konfigurálása

A következő segítségével töltse le a projektet a Githubról.

    git clone https://github.com/Blackmist/hdinsight-eventhub-example

A parancs befejezése után a következő könyvtárstruktúrát:

    hdinsight-eventhub-example/
        TemperatureMonitor/ - this contains the topology
            resources/
                log4j2.xml - set logging to minimal.
                no-hbase.yaml - topology definition without hbase components.
                with-hbase.yaml - topology definition with hbase components.
            src/main/java/com/microsoft/examples/bolts/
                ParserBolt.java - parses JSON data into tuples
                DashboardBolt.java - sends data over Socket.IO to the web dashboard.
        dashboard/nodejs/ - this is the node.js web dashboard.
        SendEvents/ - utilities to send fake sensor data.

> [!NOTE]
> Ez a dokumentum nem lépjen részletesen az ebben a példában szereplő kódot. A kód azonban teljes mértékben megjegyzésként.

Az Event Hubs olvasni a projekt konfigurálásához nyissa meg a `hdinsight-eventhub-example/TemperatureMonitor/dev.properties` fájlt, és az Event Hubs-adatokat hozzáadni a következő sorokat:

```bash
eventhub.policy.key: the_key_for_RootManageSharedAccessKey
eventhub.namespace: your_namespace_here
eventhub.name: your_event_hub_name
eventhub.partitions: 2
```

## <a name="compile-and-test-locally"></a>Fordítsa le és helyi tesztelése

> [!IMPORTANT]
> A topológia helyileg a használatához a Storm fejlesztői környezetben működő. További információkért lásd: [egy Storm-fejlesztési környezet létrehozása](http://storm.apache.org/releases/1.1.0/Setting-up-development-environment.html) az Apache.org webhelyen.

Az irányítópult a topológia eredményének megtekintéséhez és az adatok tárolása az Event Hubs létrehozása előtt nem kell elindítani.

> [!IMPORTANT]
> A HBase ebben a topológiában összetevője nem aktív helyi tesztelése során. A Java API-t a HBase-fürtöt a fürtök tartalmazó Azure virtuális hálózaton kívülről nem érhető el.

### <a name="start-the-web-application"></a>A webalkalmazás elindítása

1. Nyisson meg egy parancssort, és módosítsa a könyvtárat a `hdinsight-eventhub-example/dashboard`. A következő paranccsal telepítse a függőségeket, a webes alkalmazás szükséges:
   
    ```bash
    npm install
    ```

2. Az alábbi parancs segítségével indítsa el a webalkalmazást:
   
    ```bash
    node server.js
    ```
   
    Az alábbihoz hasonló üzenet jelenik meg:
   
        Server listening at port 3000

3. Nyisson meg egy webböngészőt, és írja be `http://localhost:3000/` címként. Az alábbi képen hasonló lap jelenik meg:
   
    ![webes irányítópult](./media/apache-storm-sensor-data-analysis/emptydashboard.png)
   
    Hagyja megnyitva a parancssort. Tesztelés után Ctrl-C használatával állítsa le a webkiszolgálón.

### <a name="generate-data"></a>Adatok létrehozása

> [!NOTE]
> A jelen szakaszban szereplő lépéseket használja a Node.js, így bármilyen platformon is használhatók. Más nyelv, tekintse meg a `SendEvents` könyvtár.

1. Nyisson meg egy új parancssor, rendszerhéjat vagy terminált, és módosítsa a könyvtárat a `hdinsight-eventhub-example/SendEvents/nodejs`. A függőségek az alkalmazás által igényelt telepítéséhez használja a következő parancsot:

    ```bash
    npm install
    ```

2. Nyissa meg a `app.js` fájlt egy szövegszerkesztőben, és adja meg a korábban beszerzett Eseményközpont információkat:
   
    ```javascript
    // ServiceBus Namespace
    var namespace = 'Your-eventhub-namespace';
    // Event Hub Name
    var hubname ='Your-eventhub-name';
    // Shared access Policy name and key (from Event Hub configuration)
    var my_key_name = 'RootManageSharedAccessKey';
    var my_key = 'Your-Key';
    ```
   
   > [!NOTE]
   > Ez a példa feltételezi, hogy a `RootManageSharedAccessKey` az eseményközpont eléréséhez.

3. A következő paranccsal helyezze be az új bejegyzések az Event Hubs:
   
    ```bash
    node app.js
    ```
   
    Megjelenik a kimeneti több sornyi Eseményközpont küldött adatok tartalmazzák:
   
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"0","Temperature":7}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"1","Temperature":39}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"2","Temperature":86}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"3","Temperature":29}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"4","Temperature":30}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"5","Temperature":5}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"6","Temperature":24}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"7","Temperature":40}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"8","Temperature":43}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"9","Temperature":84}

### <a name="build-and-start-the-topology"></a>Hozza létre, és indítsa el a topológia

1. Nyisson meg egy új parancssort, és módosítsa a könyvtárat a `hdinsight-eventhub-example/TemperatureMonitor`. Építsenek, és a topológia csomag, a következő paranccsal: 

    ```bash
    mvn clean package
    ```

2. A topológia a helyi módjában indul el, a következő paranccsal:

    ```bash
    storm jar target/TemperatureMonitor-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local --filter dev.properties resources/no-hbase.yaml
    ```

    * `--local`a topológia helyi módban indul.
    * `--filter`használja a `dev.properties` feltöltéséhez paraméterek topológia meghatározása a fájl.
    * `resources/no-hbase.yaml`használja a `no-hbase.yaml` topológia meghatározása.
 
   Miután elindult, a topológia bejegyzések olvassa be az Eseményközpont, és elküldi azokat az irányítópulton, a helyi számítógépen futnia. Sorok jelennek meg a webes irányítópult, az alábbi képen hasonlóan kell megjelennie:
   
    ![Irányítópult adatokkal](./media/apache-storm-sensor-data-analysis/datadashboard.png)

2. Az irányítópult futása közben, használja a `node app.js` az előző lépéseket követve új adatok küldése az Event Hubs parancsot. Hőmérséklet értékek véletlenszerűen jönnek létre, mert a diagramon megjelenítendő nagy változások hőmérséklet frissítenie kell.
   
   > [!NOTE]
   > A kell lennie a **hdinsight – az eventhub-példa/SendEvents/Nodejs** directory használata esetén a `node app.js` parancsot.

3. Annak ellenőrzése, hogy az irányítópult frissíti, után állítsa le a Ctrl + C topológiáját. Ctrl + C használatával állítsa le a helyi webkiszolgáló is.

## <a name="create-a-storm-and-hbase-cluster"></a>A Storm és HBase-fürt létrehozása

Ezen lépések szakasz használata egy [Azure Resource Manager sablon](../../azure-resource-manager/resource-group-template-deploy.md) hozhat létre Azure-beli virtuális hálózatra és a Storm és HBase-fürtöt a virtuális hálózaton. A sablon is létrehoz az Azure Web Apps, és telepíti az irányítópult másolatának bele.

> [!NOTE]
> Egy virtuális hálózatot, hogy a topológia a Storm-fürt futó közvetlenül kommunikálhatnak a HBase Java API-val HBase-fürtöt szolgál.

A Resource Manager-sablon, itt a következő nyilvános blobtárolóban található **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hbase-storm-cluster-in-vnet-3.6.json**.

1. A következő gombra kattintva jelentkezzen be az Azure-ba, és nyissa meg a Resource Manager-sablon az Azure portálon.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-storm-cluster-in-vnet-3.6.json" target="_blank"><img src="./media/apache-storm-sensor-data-analysis/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Az a **egyéni telepítési** területen adja meg a következő értékeket:
   
    ![A HDInsight-paraméterek](./media/apache-storm-sensor-data-analysis/parameters.png)
   
   * **Fürt neve kiinduló**: Ez az érték szerepe alapnevét a Storm és HBase fürtök. Ha például **abc** hoz létre egy Storm-fürt nevű **storm-abc** és nevű HBase-fürtöt **hbase-abc**.
   * **A fürt bejelentkezési felhasználónevét**: A rendszergazda felhasználóneve a Storm és HBase fürt.
   * **A fürt bejelentkezési jelszó**: a Storm és HBase fürt rendszergazdai jelszóval.
   * **SSH-felhasználónév**: az SSH-felhasználó a Storm és HBase fürtök létrehozása.
   * **SSH-jelszónak**: a Storm és HBase fürt SSH-felhasználó jelszavát.
   * **Hely**: A régiót, amelyben a fürtök jönnek létre.
     
     Kattintson az **OK** gombra a paraméterek mentéséhez.

3. Használja a **alapjai** szakasz hozzon létre egy erőforráscsoportot, vagy válasszon egy meglévőt.
4. Az a **erőforráscsoport helye** legördülő menüben válassza ki kijelölt ugyanarra a helyre, ha Ön a **hely** paramétere a **beállítások** szakasz.
5. Olvassa el a használati feltételeket, majd válassza ki **elfogadom a feltételeket és a fenti feltételek**.
6. Végül ellenőrizze **rögzítés az irányítópulton** majd **beszerzési**. A fürt létrehozása nagyjából 20 percet vesz igénybe.

Az erőforrások létrehozása után, az erőforráscsoport információkat jelenít meg.

![A virtuális hálózat és a fürtök tartozó erőforráscsoport](./media/apache-storm-sensor-data-analysis/groupblade.png)

> [!IMPORTANT]
> Figyelje meg, hogy a HDInsight-fürtök neve **storm-BASENAME** és **hbase-BASENAME**, ahol BASENAME a sablonhoz megadott név. Ezeket a neveket használni egy későbbi lépésben a fürtök való kapcsolódáskor. Is vegye figyelembe, hogy az irányítópult-hely nevével **basename-irányítópult**. Ez az érték használható a dokumentum későbbi szakaszában.

## <a name="configure-the-dashboard-bolt"></a>Az irányítópult bolt konfigurálása

Az irányítópult webes alkalmazás központilag telepített adatok küldését, módosítania kell a következő sort a a `dev.properties`fájlt:

```yaml
dashboard.uri: http://localhost:3000
```

Változás `http://localhost:3000` való `http://BASENAME-dashboard.azurewebsites.net` , és mentse a fájlt. Cserélje le **BASENAME** az Alap az előző lépésben megadott névvel. Válassza ki az irányítópultot, és tekintse meg az URL-címet korábban létrehozott erőforráscsoportot is használható.

## <a name="create-the-hbase-table"></a>A HBase tábla létrehozása

Adatok tárolása a HBase, azt először létre kell hozni egy tábla. Előzetes létrehozása az erőforrásokat, amelyek a Storm kell írni, és az erőforráshoz, a Storm létrehozására tett kísérlet, topológia ugyanaz az erőforrás létrehozásának megkísérlése több példánya is okozhat. A topológia kívüli erőforrások létrehozását, és olvasási/írási és az elemzések Storm használatát.

1. Az SSH használata a HBase-fürtöt az SSH-felhasználó és a fürt létrehozása során a sablonba megadott jelszóval való kapcsolódáshoz. Például, ha csatlakozik a `ssh` parancsot, akkor használja a következő szintaxist:
   
    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```
   
    Cserélje le `sshuser` az a fürt létrehozásakor megadott SSH-felhasználónév. Cserélje le `clustername` a HBase fürt nevéhez.

2. Az SSH-munkamenetből indítsa el a HBase rendszerhéjból.
   
    ```bash
    hbase shell
    ```
   
    Miután a rendszerhéj be van töltve, megjelenik egy `hbase(main):001:0>` kérdés.

3. A HBase rendszerhéjból adja meg a érzékelő adatokat tároló tábla létrehozása a következő parancsot:
   
    ```hbase
    create 'SensorData', 'cf'
    ```

4. Győződjön meg arról, hogy létrejött-e a tábla a következő paranccsal:
   
    ```hbase
    scan 'SensorData'
    ```
   
    Ez információkat ad vissza a következőhöz hasonló, amely azt jelzi, hogy nincsenek-e 0 sorokat a táblában.
   
        ROW                   COLUMN+CELL                                       0 row(s) in 0.1900 seconds

5. Adja meg `exit` való kilépéshez a HBase rendszerhéjból:

## <a name="configure-the-hbase-bolt"></a>A HBase bolt konfigurálása

A Storm-fürtök írni a HBase, a HBase bolt kell megadnia a konfigurációs adatait a HBase fürt.

1. Az alábbi példák segítségével a Zookeeper a HBase fürt kvórumát beolvasása:

    ```bash
    CLUSTERNAME='your_HDInsight_cluster_name'
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/HBASE/components/HBASE_MASTER" | jq '.metrics.hbase.master.ZookeeperQuorum'
    ```

    > [!NOTE]
    > Cserélje le a `your_HDInsight_cluster_name` kifejezést a HDInsight-fürt nevére. Telepítéséről további információt a `jq` segédprogram, lásd: [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).
    >
    > Amikor a rendszer kéri, adja meg a jelszót a HDInsight-rendszergazdai bejelentkezés.

    ```powershell
    $clusterName = 'your_HDInsight_cluster_name'
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HBASE/components/HBASE_MASTER" -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.metrics.hbase.master.ZookeeperQuorum
    ```

    > [!NOTE]
    > Cserélje le a(z) your_HDInsight_cluster_name a HDInsight-fürt nevét. Amikor a rendszer kéri, adja meg a jelszót a HDInsight-rendszergazdai bejelentkezés.
    >
    > Ebben a példában az Azure PowerShell szükséges. További információ az Azure PowerShell használatával, lásd: [Ismerkedés az Azure PowerShell](https://docs.microsoft.com/en-us/powershell/scripting/Getting-Started-with-Windows-PowerShell?view=powershell-6)

    Ezek a példák által visszaadott adatok hasonlít a következő szöveget:

    `zk2-hbase.mf0yeg255m4ubit1auvj1tutvh.ex.internal.cloudapp.net:2181,zk0-hbase.mf0yeg255m4ubit1auvj1tutvh.ex.internal.cloudapp.net:2181,zk3-hbase.mf0yeg255m4ubit1auvj1tutvh.ex.internal.cloudapp.net:2181`

    Ezt az információt használják a Storm kommunikálni a HBase-fürtöt.

2. Módosítsa a `dev.properties` fájlt, és a Zookeeper kvórum-adatokat hozzáadni a következő sort:

    ```yaml
    hbase.zookeeper.quorum: your_hbase_quorum
    ```

## <a name="build-package-and-deploy-the-solution-to-hdinsight"></a>Hozza létre, csomagot, és a HDInsight a megoldás telepítése

A fejlesztői környezetben tegye a következőket a Storm-topológia a storm-fürt központi telepítése.

1. Az a `TemperatureMonitor` directory, a következő parancsot egy új build végrehajtani, és hozzon létre egy JAR csomag a projektből használható:
   
        mvn clean package
   
    Ez a parancs létrehoz egy nevű fájlt `TemperatureMonitor-1.0-SNAPSHOT.jar in the `cél "mappában található a projekthez.

2. Szolgáltatáskapcsolódási pont használatával töltse fel a `TemperatureMonitor-1.0-SNAPSHOT.jar` és `dev.properties` a Storm-fürt fájlokat. Az alábbi példában cserélje le `sshuser` a fürt létrehozásakor megadott SSH-felhasználói és `clustername` a Storm-fürt nevét. Amikor a rendszer kéri, adja meg az SSH-felhasználó jelszavát.
   
    ```bash
    scp target/TemperatureMonitor-1.0-SNAPSHOT.jar dev.properties sshuser@clustername-ssh.azurehdinsight.net:
    ```

   > [!NOTE]
   > A fájlok feltöltéséhez több percig is eltarthat.

    További információk az a `scp` és `ssh` parancsok a hdinsight eszközzel, lásd: [az SSH a Hdinsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md)

3. A fájl feltöltése után csatlakozzon a Storm fürthöz SSH használatával.
   
    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

    Cserélje le `sshuser` az SSH-felhasználónév. Cserélje le `clustername` a Storm-fürt nevű.

4. Indítsa el a topológia, használja az SSH-munkamenetet a következő parancsot:
   
    ```bash
    storm jar TemperatureMonitor-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote --filter dev.properties -R /with-hbase.yaml
    ```

    * `--remote`a topológia a Nimbus szolgáltatás, amely elküldi a fürt felügyelő csomópontja küldi el.
    * `--filter`használja a `dev.properties` feltöltéséhez paraméterek topológia meghatározása a fájl.
    * `-R /with-hbase.yaml`használja a `with-hbase.yaml` topológia a csomagban található.

5. Miután elindult a topológia, egy böngészőben nyissa meg a webhely, az Azure-on közzétett, majd használja a `node app.js` szeretnék adatokat küldeni a Eseményközpont parancs. Meg kell jelennie a webes irányítópult frissül, és megjeleníti az adatokat.
   
    ![irányítópult](./media/apache-storm-sensor-data-analysis/datadashboard.png)

## <a name="view-hbase-data"></a>A HBase-adatok megtekintése

Az alábbi lépések segítségével HBase csatlakozhat, és győződjön meg arról, hogy az adatok a táblázatban van írva:

1. SSH használatával csatlakozhat a HBase-fürtöt.
   
    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

    Cserélje le `sshuser` az SSH-felhasználónév. Cserélje le `clustername` a HBase fürt nevéhez.

2. Az SSH-munkamenetből indítsa el a HBase rendszerhéjból.
   
    ```bash
    hbase shell
    ```
   
    Miután a rendszerhéj be van töltve, megjelenik egy `hbase(main):001:0>` kérdés.

3. Nézet sorokat a táblázatból:
   
    ```hbase
    scan 'SensorData'
    ```
   
    Ez a parancs visszaadja a információ az alábbihoz hasonló, jelezve, hogy nincs-e adatokat a táblában.
   
        hbase(main):002:0> scan 'SensorData'
        ROW                             COLUMN+CELL
        \x00\x00\x00\x00               column=cf:temperature, timestamp=1467290788277, value=\x00\x00\x00\x04
        \x00\x00\x00\x00               column=cf:timestamp, timestamp=1467290788277, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x01               column=cf:temperature, timestamp=1467290788348, value=\x00\x00\x00M
        \x00\x00\x00\x01               column=cf:timestamp, timestamp=1467290788348, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x02               column=cf:temperature, timestamp=1467290788268, value=\x00\x00\x00R
        \x00\x00\x00\x02               column=cf:timestamp, timestamp=1467290788268, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x03               column=cf:temperature, timestamp=1467290788269, value=\x00\x00\x00#
        \x00\x00\x00\x03               column=cf:timestamp, timestamp=1467290788269, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x04               column=cf:temperature, timestamp=1467290788356, value=\x00\x00\x00>
        \x00\x00\x00\x04               column=cf:timestamp, timestamp=1467290788356, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x05               column=cf:temperature, timestamp=1467290788326, value=\x00\x00\x00\x0D
        \x00\x00\x00\x05               column=cf:timestamp, timestamp=1467290788326, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x06               column=cf:temperature, timestamp=1467290788253, value=\x00\x00\x009
        \x00\x00\x00\x06               column=cf:timestamp, timestamp=1467290788253, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x07               column=cf:temperature, timestamp=1467290788229, value=\x00\x00\x00\x12
        \x00\x00\x00\x07               column=cf:timestamp, timestamp=1467290788229, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x08               column=cf:temperature, timestamp=1467290788336, value=\x00\x00\x00\x16
        \x00\x00\x00\x08               column=cf:timestamp, timestamp=1467290788336, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x09               column=cf:temperature, timestamp=1467290788246, value=\x00\x00\x001
        \x00\x00\x00\x09               column=cf:timestamp, timestamp=1467290788246, value=2015-02-10T14:43.05.00320Z
        10 row(s) in 0.1800 seconds
   
   > [!NOTE]
   > A vizsgálati művelet legfeljebb 10 sorokat a tábla adja vissza.

## <a name="delete-your-clusters"></a>A fürtök törlése

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

A fürtök, a tároló és a webalkalmazást egy időben törléséhez törölje a csoportot, amely tartalmazza azokat.

## <a name="next-steps"></a>Következő lépések

A HDInsight Storm-topológiák további példákért lásd [a HDInsight alatt futó Storm](apache-storm-example-topology.md)

Apache Storm kapcsolatos további információkért tekintse meg a [alatt futó Apache Storm](https://storm.incubator.apache.org/) hely.

A HDInsight HBase kapcsolatos további információkért tekintse meg a [HBase a HDInsight áttekintése](../hbase/apache-hbase-overview.md).

Socket.io kapcsolatos további információkért tekintse meg a [socket.io](http://socket.io/) hely.

D3.js kapcsolatos további információkért lásd: [D3.js - adatokat tartalmazó dokumentumok vezérelt](http://d3js.org/).

A Java topológiák létrehozásával kapcsolatos további információkért lásd: [HDInsight alatt futó Apache Storm topológiák fejlesztése Java](apache-storm-develop-java-topology.md).

A .NET topológiák létrehozásával kapcsolatos további információkért lásd: [fejlesztésére C#-topológiák futó Apache stormra a Visual Studio használatával HDInsight](apache-storm-develop-csharp-visual-studio-topology.md).

[azure-portal]: https://portal.azure.com
