---
title: Események feldolgozása az Event Hubs-ból a Storm segítségével – Azure HDInsight
description: Ismerje meg, hogyan dolgozhat fel adatokat az Azure Event Hubs-ból a Visual Studióban létrehozott C# Storm topológiával a Visual Studio HDInsight-eszközeivel.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: 85b7093df99127b690c51e8f2f28d18e3f5f3c95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981643"
---
# <a name="process-events-from-azure-event-hubs-with-apache-storm-on-hdinsight-c"></a>Események feldolgozása az Azure Event Hubs-ból az Apache Storm segítségével a HDInsighton (C#)

Ismerje meg, hogyan dolgozhat az Azure Event Hubs használatával az [Apache Storm](https://storm.apache.org/) ból a HDInsight-on. Ez a dokumentum C# Storm topológiát használ az Event Hubs adatainak olvasásához és írásához

> [!NOTE]  
> A projekt Java-verzióját az Események feldolgozása az [Azure Event Hubs-ból az Apache Storm segítségével a HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)című témakörben található.

## <a name="scpnet"></a>SCP.NET

A jelen dokumentum lépései SCP.NET, egy NuGet csomagot használnak, amely megkönnyíti a C# topológiák és összetevők létrehozását a Storm hdinsight-on való használatra.

A HDInsight 3.4-es és nagyobb felbontásban a Monó a C# topológiák futtatásához használható. A dokumentumban használt példa a HDInsight 3.6-os sal működik. Ha saját .NET-megoldásokat szeretne létrehozni a HDInsight hoz létre, ellenőrizze a [Mono kompatibilitási](https://www.mono-project.com/docs/about-mono/compatibility/) dokumentumot a lehetséges inkompatibilitási lehetőségekért.

### <a name="cluster-versioning"></a>Fürt verziószámozása

A projekthez használt Microsoft.SCP.Net.SDK NuGet csomagnak meg kell egyeznie a HDInsight-ra telepített Storm főverziójával. A HDInsight 3.5-ös és 3.6-os verziója a Storm 1.x-et használja, ezért SCP.NET 1.0.x.x verziót kell használnia ezekkel a fürtökkel.

A C# topológiáknak a .NET 4.5-öt is meg kell célozniuk.

## <a name="how-to-work-with-event-hubs"></a>Az Event Hubs megoldása

A Microsoft java-összetevőket biztosít, amelyek segítségével kommunikálhat az Event Hubs storm-topológia. Az összetevők HDInsight 3.6-kompatibilis verzióját tartalmazó Java archív (JAR) [https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar)fájl a következő helyen található: a.

> [!IMPORTANT]  
> Míg az összetevők Java nyelven íródtak, könnyen használhatja őket a C# topológiából.

Ebben a példában a következő összetevőket használjuk:

* __EventHubSpout__: Adatokat olvas be az Event Hubs-ból.
* __EventHubBolt__: Adatokat ír az Event Hubs-ba.
* __EventHubSpoutConfig__: Az EventHubSpout konfigurálására szolgál.
* __EventHubBoltConfig__: Az EventHubBolt konfigurálására szolgál.

### <a name="example-spout-usage"></a>Példa kifolyó használata

SCP.NET az EventHubSpout topológiához való hozzáadásának módszereit biztosítja. Ezek a módszerek megkönnyítik a kifolyó hozzáadását, mint a Java-összetevők hozzáadásának általános módszerei. A következő példa bemutatja, hogyan hozhat létre egy spout segítségével __SetEventHubSpout__ és **EventHubSpoutConfig** módszerek által SCP.NET:

```csharp
 topologyBuilder.SetEventHubSpout(
    "EventHubSpout",
    new EventHubSpoutConfig(
        ConfigurationManager.AppSettings["EventHubSharedAccessKeyName"],
        ConfigurationManager.AppSettings["EventHubSharedAccessKey"],
        ConfigurationManager.AppSettings["EventHubNamespace"],
        ConfigurationManager.AppSettings["EventHubEntityPath"],
        eventHubPartitions),
    eventHubPartitions);
```

Az előző példa létrehoz egy új spout összetevő nevű __EventHubSpout__, és konfigurálja, hogy kommunikálni egy eseményközponttal. Az összetevő párhuzamossági tippje az eseményközpontban lévő partíciók számára van beállítva. Ez a beállítás lehetővé teszi, hogy a Storm minden partícióhoz hozzon létre egy példányt az összetevőből.

### <a name="example-bolt-usage"></a>Példa csavarhasználat

Használja a **JavaComponmentConstructor** metódust a csavar egy példányának létrehozásához. A következő példa bemutatja, hogyan hozhat létre és konfigurálhat egy új példányt az **EventHubBoltból:**

```csharp
// Java construcvtor for the Event Hub Bolt
JavaComponentConstructor constructor = JavaComponentConstructor.CreateFromClojureExpr(
    String.Format(@"(org.apache.storm.eventhubs.bolt.EventHubBolt. (org.apache.storm.eventhubs.bolt.EventHubBoltConfig. " +
        @"""{0}"" ""{1}"" ""{2}"" ""{3}"" ""{4}"" {5}))",
        ConfigurationManager.AppSettings["EventHubPolicyName"],
        ConfigurationManager.AppSettings["EventHubPolicyKey"],
        ConfigurationManager.AppSettings["EventHubNamespace"],
        "servicebus.windows.net",
        ConfigurationManager.AppSettings["EventHubName"],
        "true"));

// Set the bolt to subscribe to data from the spout
topologyBuilder.SetJavaBolt(
    "eventhubbolt",
    constructor,
    partitionCount)
        .shuffleGrouping("Spout");
```

> [!NOTE]  
> Ez a példa egy karakterláncként átadott Clojure-kifejezést használ ahelyett, hogy **a JavaComponentConstructor használatával** hozzon létre egy **EventHubBoltConfig-ot,** ahogy a spout példa tette. Mindkét módszer működik. Használja a módszert, hogy úgy érzi, a legjobb az Ön számára.

## <a name="download-the-completed-project"></a>A befejezett projekt letöltése

A cikkben létrehozott projekt teljes verzióját letöltheti a [GitHubról.](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub) A jelen cikkben ismertetett lépéseket követve azonban továbbra is meg kell adnia a konfigurációs beállításokat.

### <a name="prerequisites"></a>Előfeltételek

* Apache Storm-fürt a HDInsighton. Lásd: [Apache Hadoop-fürtök létrehozása az Azure Portalon,](../hdinsight-hadoop-create-linux-clusters-portal.md) és válassza a **Storm** for **Cluster típus**lehetőséget.

* Egy [Azure-eseményközpont.](../../event-hubs/event-hubs-create.md)

* Az [Azure .NET SDK](https://azure.microsoft.com/downloads/).

* A [Visual Studio HDInsight-eszközei](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

* Java JDK 1.8 vagy újabb a fejlesztői környezetben. A JDK letöltések az [Oracle-től](https://aka.ms/azure-jdks)érhetők el.

  * A **JAVA_HOME** környezeti változónak a Java-t tartalmazó könyvtárra kell mutatnia.
  * **A(z) %JAVA_HOME%/bin** könyvtárnak az elérési úton kell lennie.

## <a name="download-the-event-hubs-components"></a>Az Event Hubs összetevőinek letöltése

Töltse le az Event Hubs [https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar)kifolyót és a bolt-összetevőt a alkalmazásból.

Hozzon létre `eventhubspout`egy könyvtárat, és mentse a fájlt a könyvtárba.

## <a name="configure-event-hubs"></a>Eseményközpontok konfigurálása

Az Event Hubs a példa adatforrása. Használja az "Eseményközpont létrehozása" című, [az Event Hubs használatának első lépése című részében](../../event-hubs/event-hubs-create.md)található információkat.

1. Az eseményközpont létrehozása után tekintse meg az **EventHub** beállításait az Azure Portalon, és válassza a **Megosztott hozzáférési szabályzatok**lehetőséget. A **+Hozzáadás** lehetőséget választva hozza létre a következő házirendeket:

   | Név | Engedélyek |
   | --- | --- |
   | Író |Küldés |
   | Olvasó |Figyelés |

    ![Képernyőkép: Hozzáférési házirendek megosztása ablak](./media/apache-storm-develop-csharp-event-hub-topology/share-access-policies.png)

2. Válassza ki az **olvasó-** és **íróházirendeket.** Másolja és mentse az elsődleges kulcs értékét mindkét házirendhez, mivel ezeket az értékeket később használja.

## <a name="configure-the-eventhubwriter"></a>Az EventHubWriter konfigurálása

1. Ha még nem telepítette a Visual Studio HDInsight-eszközeinek legújabb verzióját, olvassa el a [HdInsight-eszközök használatának első lépéseit a Visual Studio számára című témakörben.](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)

2. Töltse le a megoldást [eventhub-storm-hibrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

3. Nyissa **meg az EventHubExample.sln-t.** Az **EventHubWriter** projektben nyissa meg az **App.config** fájlt. Használja a korábban konfigurált eseményközpontból származó információkat a következő kulcsok értékének kitöltésére:

   | Kulcs | Érték |
   | --- | --- |
   | EventHubPolicyName |író (Ha a *küldési* engedéllyel rendelkező házirendhez más nevet használt, használja azt.) |
   | EventHubPolicyKey |Az írói házirend kulcsa. |
   | EventHubNamespace |Az eseményközpontot tartalmazó névtér. |
   | EventHubName |Az eseményközpont neve. |
   | EventHubPartitionCount |A partíciók száma az eseményközpontban. |

4. Mentse és zárja be az **App.config** fájlt.

## <a name="configure-the-eventhubreader"></a>Az EventHubReader konfigurálása

1. Nyissa meg az **EventHubReader** projektet.

2. Nyissa meg az EventHubReader **App.config** **fájlját.** Használja a korábban konfigurált eseményközpontból származó információkat a következő kulcsok értékének kitöltésére:

   | Kulcs | Érték |
   | --- | --- |
   | EventHubPolicyName |olvasó (Ha a házirendhez más nevet *használt,* használja helyette.) |
   | EventHubPolicyKey |Az olvasóházirend kulcsa. |
   | EventHubNamespace |Az eseményközpontot tartalmazó névtér. |
   | EventHubName |Az eseményközpont neve. |
   | EventHubPartitionCount |A partíciók száma az eseményközpontban. |

3. Mentse és zárja be az **App.config** fájlt.

## <a name="deploy-the-topologies"></a>A topológiák telepítése

1. A **Megoldáskezelőben**kattintson a jobb gombbal az **EventHubReader** projektre, és válassza **a Küldés a stormba parancsot a HDInsight on**.

    ![Képernyőkép a Megoldáskezelőről, amelyen a Küldés a viharba lehetőség a HDInsight-on ki van emelve](./media/apache-storm-develop-csharp-event-hub-topology/submit-to-apache-storm.png)

2. A **Topológia küldése** párbeszédpanelen jelölje ki a **Storm Cluster**lehetőséget. Bontsa ki a **További konfigurációk**elemet, válassza a **Java fájl elérési útjait,** válassza a **...** lehetőséget, és jelölje ki a korábban letöltött JAR-fájlt tartalmazó könyvtárat. Végül kattintson a **Küldés gombra.**

    ![Képernyőkép: Topológia küldése párbeszédpanel](./media/apache-storm-develop-csharp-event-hub-topology/submit-storm-topology.png)

3. A topológia elküldése után megjelenik a **Storm Topologies Viewer.** A topológiával kapcsolatos információk megtekintéséhez válassza az **EventHubReader** topológiát a bal oldali ablaktáblában.

    ![A Storm Topologies viewer képernyőképe](./media/apache-storm-develop-csharp-event-hub-topology/storm-topology-viewer.png)

4. A **Megoldáskezelőben**kattintson a jobb gombbal az **EventHubWriter** projektre, és válassza **a Küldés a viharba parancsot a HDInsight on**.

5. A **Topológia küldése** párbeszédpanelen jelölje ki a **Storm Cluster**lehetőséget. Bontsa ki a **További konfigurációk**elemet, válassza a **Java fájl elérési útjait,** válassza a **...** lehetőséget, és jelölje ki a korábban letöltött JAR-fájlt tartalmazó könyvtárat. Végül kattintson a **Küldés gombra.**

6. A topológia elküldése után frissítse a Storm **Topologies Viewer** topológialistáját, és ellenőrizze, hogy mindkét topológia fut-e a fürtön.

7. A **Storm Topologies Viewer**alkalmazásban válassza ki az **EventHubReader** topológiát.

8. A csavar összetevő-összegzésének megnyitásához kattintson duplán a **diagram LogBolt** összetevőjére.

9. A **Végrehajtók** csoportban jelölje ki a **Port** oszlopban lévő hivatkozások egyikét. Ez az összetevő által naplózott információkat jeleníti meg. A naplózott adatok hasonlóak a következő szöveghez:

    ```output
    2017-03-02 14:51:29.255 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,255 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1830978598,"deviceId":"8566ccbc-034d-45db-883d-d8a31f34068e"}
    2017-03-02 14:51:29.283 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,283 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1756413275,"deviceId":"647a5eff-823d-482f-a8b4-b95b35ae570b"}
    2017-03-02 14:51:29.313 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,312 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1108478910,"deviceId":"206a68fa-8264-4d61-9100-bfdb68ee8f0a"}
    ```

## <a name="stop-the-topologies"></a>A topológiák leállítása

A topológiák leállításához jelölje ki az egyes topológiákat a **Storm Topology Viewer programban,** majd kattintson a **Ölés**gombra.

![Képernyőkép a Storm Topology Viewer képernyőképéről, kiemelve a Leölés gombbal](./media/apache-storm-develop-csharp-event-hub-topology/kill-storm-topology1.png)

## <a name="delete-your-cluster"></a>A fürt törlése

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>További lépések

Ebben a dokumentumban megtanulta, hogyan használhatja a Java Event Hubs kifolyóés a Bolt egy C# topológia adatok használatával az Azure Event Hubs. A C# topológiák létrehozásáról az alábbi témakörökben olvashat bővebben:

* [C# topológiák fejlesztése apache stormhoz a HDInsighton a Visual Studio használatával](apache-storm-develop-csharp-visual-studio-topology.md)
* [SCP programozási útmutató](apache-storm-scp-programming-guide.md)
* [HDInsight alatt futó Apache Storm példatopológiái](apache-storm-example-topology.md)
