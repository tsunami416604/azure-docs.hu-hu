---
title: Az Event Hubsból az Azure HDInsight a Storm - események feldolgozása
description: Ismerje meg, a Visual Studio létrehozta a HDInsight tools for Visual Studio használatával C# Storm-topológia az Azure Event Hubsból származó adatok feldolgozása.
services: hdinsight,notification hubs
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: jasonh
ROBOTS: NOINDEX
ms.openlocfilehash: 7c37abe709559fc0628b94ba50634673c08af375
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43051567"
---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-c"></a>Események feldolgozása az Azure Event Hubsból a Storm on HDInsight (C#)

Ismerje meg, hogyan használható az Apache Storm on HDInsight az Azure Event hubs szolgáltatással. Ez a dokumentum olvasása és írása az adatokat az Event Hubs C# Storm-topológia használja

> [!NOTE]
> Ez a projekt Java verziója: [dolgozza fel az Azure Event Hubsból futó stormmal (Java) HDInsight](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/).

## <a name="scpnet"></a>SCP.NET

A jelen dokumentumban leírt lépések SCP.NET, a NuGet-csomagot, amellyel egyszerűen hozhat létre a C#-topológiák és összetevők használatra a HDInsight alatt futó Stormmal használható.

> [!IMPORTANT]
> A jelen dokumentumban leírt lépések egy Windows fejlesztési környezetet a Visual Studióval támaszkodnak, amíg a lefordított projekt küldheti el a Storm on HDInsight-fürt által használt Linux. Csak a Linux-alapú fürtök 2016. október 28. után létrehozott SCP.NET topológiákat támogatja.

HDInsight 3.4-es és nagyobb mértékben használják Mono C#-topológiák futtatásához. Az itt bemutatott példában a HDInsight 3.6-os együttműködik. Ha azt tervezi, hogy a saját .NET – megoldások létrehozását a HDInsight, ellenőrizze a [monó kompatibilitási](http://www.mono-project.com/docs/about-mono/compatibility/) lehetséges inkompatibilitásokat a dokumentum.

### <a name="cluster-versioning"></a>Fürt-verziókezelés

A Microsoft.SCP.Net.SDK NuGet-csomagot a projekthez használt telepítve a HDInsight Storm főverziója egyeznie kell. HDInsight-verziók 3.5-ös és 3.6-os alatt futó Storm használható 1.x, így ezek a fürtök SCP.NET verzió 1.0.x.x kell használnia.

> [!IMPORTANT]
> A példában a jelen dokumentum vár egy HDInsight 3.5-ös és 3.6-fürt.
>
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

C#-topológiák is kell céloznia a .NET 4.5-ös verzióját.

## <a name="how-to-work-with-event-hubs"></a>Az Event Hubs használata

A Microsoft biztosít egy Java-összetevők az Event Hubs egy Storm-topológiából származó folytatott kommunikációhoz használható. A Java-archívumfájl (JAR), amely ezeket az összetevőket, kompatibilis a HDInsight 3.6-os verzióját tartalmazza annak [ https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar ](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar).

> [!IMPORTANT]
> Amíg az összetevők Java nyelven íródtak, könnyen használhatja őket a C#-topológiák.

A következő összetevőket ebben a példában használt:

* __EventHubSpout__: beolvassa az adatokat az Event hubs Eseményközpontokból.
* __EventHubBolt__: írja az adatokat az Event hubs szolgáltatásba.
* __EventHubSpoutConfig__: EventHubSpout konfigurálásához használt.
* __EventHubBoltConfig__: EventHubBolt konfigurálásához használt.

### <a name="example-spout-usage"></a>Spout gyakorlati példa

SCP.NET-EventHubSpout ad hozzá a topológia módszert biztosít. Ezek a metódusok egyszerűbben, mint az általános metódusok használata hozzáadásához egy Java-összetevők a spout hozzáadása. Az alábbi példa bemutatja, hogyan hozhat létre egy spout használatával a __SetEventHubSpout__ és **EventHubSpoutConfig** SCP.NET által biztosított módszerek:

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

Az előző példában létrehoz egy új spout nevű összetevő __EventHubSpout__, és konfigurálja, hogy és az eseményközpont közötti kommunikációhoz. Az összetevő a párhuzamosság mutató értéke a partíciók számát, az eseményközpont. Ez a beállítás lehetővé teszi, hogy a Storm, hozzon létre egy példányt az egyes partíciók-összetevőt.

### <a name="example-bolt-usage"></a>Példa a bolt használatra

Használja a **JavaComponmentConstructor** metódussal hoz létre a bolt egy példányát. Az alábbi példa bemutatja, hogyan kell létrehozni és konfigurálni egy új példányát a **EventHubBolt**:

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
> Ebben a példában egy átadott karakterlánc használata helyett Clojure kifejezés **JavaComponentConstructor** hozhat létre egy **EventHubBoltConfig**, mint a spout-példában. Mindkét módszer használható. Úgy érzi, a legjobb módszert használja.

## <a name="download-the-completed-project"></a>Töltse le a befejezett projekt

A projekt ebben az oktatóanyagban létrehozott egy teljes körű verziója letölthető [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub). Azonban továbbra is szeretné az oktatóanyag lépéseit követve adja meg a konfigurációs beállításokat.

### <a name="prerequisites"></a>Előfeltételek

* Egy [Apache Storm on HDInsight 3.5-ös és 3.6-os fürtverzió](apache-storm-tutorial-get-started-linux.md).

    > [!WARNING]
    > Az itt bemutatott példa a Storm on HDInsight 3.5-ös és 3.6-os verziója szükséges. Ez nem működik a HDInsight, régebbi verzióival használhatatlanná tévő osztály nevének módosítása miatt. Ebben a példában, amely együttműködik a régi fürtök verzióját, lásd: [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub/releases).

* Egy [az Azure event hub](../../event-hubs/event-hubs-create.md).

* A [Azure .NET SDK-val](http://azure.microsoft.com/downloads/).

* A [HDInsight tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

* Java JDK 1.8-at vagy annál újabb verzió a fejlesztési környezetet. JDK letöltések érhetők el a [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html).

  * A **JAVA_HOME** környezeti változó Java tartalmazó könyvtárra kell mutatnia.
  * A **%JAVA_HOME%/bin** könyvtárnak az elérési úton kell lennie.

## <a name="download-the-event-hubs-components"></a>Töltse le az Event Hubs-összetevők

Töltse le az Event Hubs spout, és az összetevőt bolt [ https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar ](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar).

Hozzon létre egy könyvtárat nevű `eventhubspout`, és mentse a fájlt a könyvtárba.

## <a name="configure-event-hubs"></a>Az Event Hubs konfigurálása

Event Hubs szolgáltatás ebben a példában az adatforrás. Az "Event hub létrehozása" szakaszában foglaltak [Event Hubs használatának első lépései](../../event-hubs/event-hubs-create.md).

1. Az event hubs létrehozását követően megtekintheti a **EventHub** beállításai az Azure portálra, és kattintson a **megosztott elérési házirendek**. Válassza ki **+ Hozzáadás** hozzáadása a következő szabályzatokat:

   | Name (Név) | Engedélyek |
   | --- | --- |
   | szerkesztő |Küldés |
   | olvasó |Figyelés |

    ![Képernyőkép a fájlmegosztás hozzáférési házirendek ablak](./media/apache-storm-develop-csharp-event-hub-topology/sas.png)

2. Válassza ki a **olvasó** és **író** házirendeket. Másolja és mentse a mindkét házirend elsődleges kulcs értékét, ezeket az értékeket később használ.

## <a name="configure-the-eventhubwriter"></a>A EventHubWriter konfigurálása

1. Ha Ön még nem telepítette a legújabb verzióját a HDInsight tools for Visual Studio, [első lépései a HDInsight tools for Visual Studio használatával](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

2. A megoldás letöltése [a hibrid storm eventhub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

3. Az a **EventHubWriter** projektben nyissa meg a **App.config** fájlt. Az event hubs korábban konfigurált információk segítségével adja meg az alábbi kulcsokhoz tartozó a értékét:

   | Kulcs | Érték |
   | --- | --- |
   | EventHubPolicyName |író (Ha a házirend egy másik nevet használt *küldése* engedéllyel, használja helyette.) |
   | EventHubPolicyKey |A kulcs az író házirend. |
   | EventHubNamespace |A névtér, amely tartalmazza az eseményközpontba. |
   | EventHubName |Az eseményközpont neve. |
   | EventHubPartitionCount |Az eseményközpont száma. |

4. Mentse és zárja be a **App.config** fájlt.

## <a name="configure-the-eventhubreader"></a>A EventHubReader konfigurálása

1. Nyissa meg a **EventHubReader** projekt.

2. Nyissa meg a **App.config** fájlt a **EventHubReader**. Az event hubs korábban konfigurált információk segítségével adja meg az alábbi kulcsokhoz tartozó a értékét:

   | Kulcs | Érték |
   | --- | --- |
   | EventHubPolicyName |olvasó (Ha a házirend egy másik nevet használt *figyelésére* engedéllyel, használja helyette.) |
   | EventHubPolicyKey |A kulcs az olvasó házirend. |
   | EventHubNamespace |A névtér, amely tartalmazza az eseményközpontba. |
   | EventHubName |Az eseményközpont neve. |
   | EventHubPartitionCount |Az eseményközpont száma. |

3. Mentse és zárja be a **App.config** fájlt.

## <a name="deploy-the-topologies"></a>A topológiák üzembe helyezése

1. A **Megoldáskezelőben**, kattintson a jobb gombbal a **EventHubReader** projektre, és válassza ki **Küldés a HDInsight alatt futó Stormmal**.

    ![Képernyőkép a megoldáskezelő, Küldés a kiemelt HDInsight alatt futó Stormmal](./media/apache-storm-develop-csharp-event-hub-topology/submittostorm.png)

2. Az a **elküldése topológia** párbeszédpanelen válassza ki a **Storm-fürt**. Bontsa ki a **további konfigurációs beállítások**válassza **Java Fájlelérési utak**, jelölje be **...** , és válassza ki a korábban letöltött JAR-fájlját tartalmazó könyvtárba. Végül kattintson **küldés**.

    ![Topológia elküldése képernyőkép párbeszédpanel](./media/apache-storm-develop-csharp-event-hub-topology/submit.png)

3. Amikor a rendszer elküldte a topológiát, a **Storm-topológiák megjelenítő** jelenik meg. A topológiával kapcsolatos információk megtekintéséhez jelölje ki a **EventHubReader** topológia a bal oldali panelen.

    ![A Storm-topológiák megjelenítő képernyőkép](./media/apache-storm-develop-csharp-event-hub-topology/topologyviewer.png)

4. A **Megoldáskezelőben**, kattintson a jobb gombbal a **EventHubWriter** projektre, és válassza ki **Küldés a HDInsight alatt futó Stormmal**.

5. Az a **elküldése topológia** párbeszédpanelen válassza ki a **Storm-fürt**. Bontsa ki a **további konfigurációs beállítások**válassza **Java Fájlelérési utak**, jelölje be **...** , és válassza ki a korábban letöltött JAR-fájlját tartalmazó könyvtárba. Végül kattintson **küldés**.

6. Amikor a rendszer elküldte a topológiát, a topológia listájának frissítéséhez a **Storm-topológiák megjelenítő** , győződjön meg arról, hogy mindkét topológia a fürtön futnak.

7. A **Storm-topológiák megjelenítő**, jelölje be a **EventHubReader** topológia.

8. A bolt összefoglalását összetevő megnyitásához kattintson duplán a **LogBolt** összetevőt a diagramban.

9. Az a **végrehajtóval** szakaszban található hivatkozások közül válassza ki a **Port** oszlop. Ez az összetevő által naplózott információkat jeleníti meg. A naplózott információk az alábbi szöveghez hasonlít:

        2017-03-02 14:51:29.255 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,255 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1830978598,"deviceId":"8566ccbc-034d-45db-883d-d8a31f34068e"}
        2017-03-02 14:51:29.283 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,283 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1756413275,"deviceId":"647a5eff-823d-482f-a8b4-b95b35ae570b"}
        2017-03-02 14:51:29.313 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,312 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1108478910,"deviceId":"206a68fa-8264-4d61-9100-bfdb68ee8f0a"}

## <a name="stop-the-topologies"></a>A topológiák leállítása

A topológiák leállításához válassza ki az egyes topológia a **Storm-topológia megjelenítő**, majd kattintson a **Kill**.

![Képernyőfelvétel: a Storm topológia megjelenítő, Kill gomb kiemelésével](./media/apache-storm-develop-csharp-event-hub-topology/killtopology.png)

## <a name="delete-your-cluster"></a>A fürt törlése

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>További lépések

Ebben a dokumentumban megtanulhatta, hogyan használja a Java Event hub spout és boltok esetében a C#-topológiák az Azure Event Hubs adatokkal való munka. C#-topológiák létrehozásával kapcsolatos további tudnivalókért tekintse meg a következőket:

* [Az Apache Storm on HDInsight Visual Studio használatával C#-topológiák fejlesztése](apache-storm-develop-csharp-visual-studio-topology.md)
* [Szolgáltatáskapcsolódási pont programozási útmutató](apache-storm-scp-programming-guide.md)
* [HDInsight alatt futó Storm példatopológiái](apache-storm-example-topology.md)
