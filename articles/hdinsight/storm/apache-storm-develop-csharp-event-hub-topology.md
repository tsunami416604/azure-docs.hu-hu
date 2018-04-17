---
title: Az Event Hubs Storm - Azure HDInsight származó események feldolgozására |} Microsoft Docs
description: Ismerje meg, hogyan kell feldolgozni az adatokat az Azure Event Hubs egy C# Storm-topológia hozta létre a Visual Studio, a HDInsight tools for Visual Studio használatával.
services: hdinsight,notification hubs
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 67f9d08c-eea0-401b-952b-db765655dad0
ms.service: hdinsight
ms.custom: ''
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: 475a63937ed09b4ce875465c0eb3488ce92c38be
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-c"></a>Az Azure Event Hubs (C#) futó Storm eseményeinek

Megismerheti az Azure Event Hubs a HDInsight alatt futó Apache Storm. Ez a dokumentum olvasása és írása az adatokat az Event Hubs használ a C# Storm-topológia

> [!NOTE]
> Ez a projekt Java verziója: [feldolgozni az eseményeket az Azure Event Hubs (Java) futó Storm](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/).

## <a name="scpnet"></a>SCP.NET

A jelen dokumentumban leírt lépések SCP.NET, a NuGet-csomagot, amely megkönnyíti a HDInsight alatt futó Storm a C#-topológiák és használatra összetevők létrehozásához használja.

> [!IMPORTANT]
> A jelen dokumentumban leírt lépések a Visual Studio Windows fejlesztői környezetre támaszkodnak, amíg a lefordított projekt küldheti el Storm on HDInsight-fürt által használt Linux. Csak a Linux-alapú fürtök létrehozása után 28 2016 októberétől kezdve, SCP.NET topológiákat támogatja.

HDInsight 3.4 és nagyobb használata monó C#-topológiák futtatásához. Ebben a dokumentumban bemutatott példában a HDInsight 3.6 működik. Ha a HDInsight a saját .NET megoldások létrehozását tervezi, akkor ellenőrizze a [monó kompatibilitási](http://www.mono-project.com/docs/about-mono/compatibility/) lehetséges incompatibilities dokumentumában.

### <a name="cluster-versioning"></a>Fürt versioning

A Microsoft.SCP.Net.SDK NuGet-csomagot a projekthez használt telepítve a HDInsight alatt futó Storm főverziója egyeznie kell. HDInsight-verziókról 3.5-ös és 3.6 alatt futó Stormot használni 1.x, így ezeken a fürtökön SCP.NET verzió 1.0.x.x kell használnia.

> [!IMPORTANT]
> Ebben a dokumentumban a példa egy HDInsight 3.5-ös vagy 3,6 fürt vár.
>
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

C#-topológiák is kell használnia célként .NET 4.5.

## <a name="how-to-work-with-event-hubs"></a>Az Event Hubs használata

Microsoft biztosít, amelyek segítségével kommunikál az Event Hubs egy Storm-topológia a Java-összetevők. A Java-archívumfájl (JAR), amely tartalmazza ezeket az összetevőket, egy HDInsight 3.6 kompatibilis verziója található [ https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar ](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar).

> [!IMPORTANT]
> Amíg az összetevők Java nyelven íródtak, könnyen használhatja őket a C#-topológiák.

A következő összetevők szerepelnek ebben a példában:

* __EventHubSpout__: olvassa be az adatokat az Event Hubs.
* __EventHubBolt__: az Event Hubs írja az adatokat.
* __EventHubSpoutConfig__: EventHubSpout konfigurálásához.
* __EventHubBoltConfig__: EventHubBolt konfigurálásához.

### <a name="example-spout-usage"></a>Spout-használat – példa

SCP.NET egy EventHubSpout ad hozzá a topológia módszereket kínál. Ezek a módszerek könnyebben egy spout, mint az általános metódusok használata egy Java-összetevő felvételéhez adja hozzá. A következő példa bemutatja, hogyan hozhat létre egy spout használatával a __SetEventHubSpout__ és **EventHubSpoutConfig** SCP.NET által biztosított módszerek:

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

Az előző példában hoz létre egy új nevű spout összetevő __EventHubSpout__, és konfigurálja az eseményközpontok folytatott kommunikációhoz. Az összetevő a párhuzamos végrehajtás mutató értéke a partíciók számának az Eseménynapló hub. Ez a beállítás lehetővé teszi, hogy a Storm minden partíció esetében összetevő példányának létrehozása.

### <a name="example-bolt-usage"></a>Példa a bolt használatra

Használja a **JavaComponmentConstructor** metódust tartalmaz a bolt egy példányának létrehozásakor. A következő példa bemutatja, hogyan hozza létre és konfigurálja egy új példányát a **EventHubBolt**:

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
> Ez a példa egy karakterláncként használata helyett átadott Clojure kifejezés **JavaComponentConstructor** létrehozásához egy **EventHubBoltConfig**, mint a spout példa. Mindkét módszer használható. Érzi, a legjobb módszert használja.

## <a name="download-the-completed-project"></a>A befejezett projekt letöltése

Ebben az oktatóanyagban a létrehozott projekt teljes verziója letölthető [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub). Azonban továbbra is szeretné az oktatóanyag lépéseit követve adja meg a konfigurációs beállításokat.

### <a name="prerequisites"></a>Előfeltételek

* Egy [Apache Storm on HDInsight-fürt verziószáma 3.5-ös vagy 3.6](apache-storm-tutorial-get-started-linux.md).

    > [!WARNING]
    > Ebben a dokumentumban bemutatott példában alatt futó Storm példatopológiái 3.5-ös vagy 3.6 verziója szükséges. Ez miatt nem működik a HDInsight, korábbi verzióival való megtörje osztály neve megváltozik. Ebben a példában, amely kompatibilis a régebbi fürtök verziója: [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub/releases).

* Egy [Azure event hubs](../../event-hubs/event-hubs-create.md).

* A [az Azure .NET SDK](http://azure.microsoft.com/downloads/).

* A [a HDInsight tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

* Java JDK 1.8 vagy később a fejlesztési környezetet. JDK letöltések érhetők el a [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html).

  * A **JAVA_HOME** környezeti változó Java tartalmazó könyvtárat kell mutatnia.
  * A **%JAVA_HOME%/bin** könyvtárnak az elérési úton kell lennie.

## <a name="download-the-event-hubs-components"></a>Az Event Hubs összetevők letöltése

Töltse le az Event Hubs spout, és az összetevőt erről a boltok [ https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar ](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar).

Hozzon létre egy könyvtárat nevű `eventhubspout`, és mentse a fájlt a könyvtárba.

## <a name="configure-event-hubs"></a>Az Event Hubs konfigurálása

Az Event Hubs ebben a példában az adatforrást. A "Létrehoz egy eseményközpontot" szakaszában foglaltak [Bevezetés az Event Hubs használatába](../../event-hubs/event-hubs-create.md).

1. Az eseményközpont létrehozása után megtekintheti a **EventHub** az Azure portál, majd válassza a beállítások **megosztott elérési házirendek**. Válassza ki **+ Hozzáadás** a következő házirendek hozzáadása:

   | Name (Név) | Engedélyek |
   | --- | --- |
   | szerkesztő |Küldés |
   | olvasó |Figyelés |

    ![Képernyőkép a fájlmegosztás hozzáférési házirendek ablak](./media/apache-storm-develop-csharp-event-hub-topology/sas.png)

2. Válassza ki a **olvasó** és **író** házirendek. Másolja ki és mentse a házirendet, az elsődleges kulcs értéke később használja ezeket az értékeket.

## <a name="configure-the-eventhubwriter"></a>A EventHubWriter konfigurálása

1. Ha még nem telepítette a legújabb verzióját a HDInsight tools for Visual Studio, lásd: [első lépései a HDInsight tools for Visual Studio használatával](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

2. Letöltheti a megoldást a [eventhub-storm-hibrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

3. Az a **EventHubWriter** projektben nyissa meg a **App.config** fájlt. Olvassa el az eseményközpontból korábban megadott értékektől töltse ki a következő kulcs értéke:

   | Kulcs | Érték |
   | --- | --- |
   | EventHubPolicyName |író (Ha használja-e egy másik nevet a házirend *küldése* engedéllyel, használja helyette.) |
   | EventHubPolicyKey |Az író házirend kulcsa. |
   | EventHubNamespace |A névtér, amely tartalmazza az eseményközpont. |
   | EventHubName |Az eseményközpont neveként. |
   | EventHubPartitionCount |A partíciók az eseményközpont száma. |

4. Mentse és zárja be a **App.config** fájlt.

## <a name="configure-the-eventhubreader"></a>A EventHubReader konfigurálása

1. Nyissa meg a **EventHubReader** projekt.

2. Nyissa meg a **App.config** fájlt a **EventHubReader**. Olvassa el az eseményközpontból korábban megadott értékektől töltse ki a következő kulcs értéke:

   | Kulcs | Érték |
   | --- | --- |
   | EventHubPolicyName |olvasó (Ha használja-e egy másik nevet a házirend *figyelésére* engedéllyel, használja helyette.) |
   | EventHubPolicyKey |A kulcs az olvasó házirend. |
   | EventHubNamespace |A névtér, amely tartalmazza az eseményközpont. |
   | EventHubName |Az eseményközpont neveként. |
   | EventHubPartitionCount |A partíciók az eseményközpont száma. |

3. Mentse és zárja be a **App.config** fájlt.

## <a name="deploy-the-topologies"></a>A topológiák telepítése

1. A **Megoldáskezelőben**, kattintson a jobb gombbal a **EventHubReader** projektre, és válassza a **Submit a HDInsight alatt futó Storm**.

    ![Képernyőfelvétel a Solution Explorer, a kiemelt HDInsight alatt futó Storm való küldés](./media/apache-storm-develop-csharp-event-hub-topology/submittostorm.png)

2. Az a **nyújt topológia** párbeszédpanelen jelölje ki a **Storm-fürt**. Bontsa ki a **további konfigurációs**, jelölje be **Java elérési utat**, jelölje be **...** , és válassza ki a korábban letöltött JAR-fájlt tartalmazó könyvtárba. Végezetül kattintson **Submit**.

    ![Topológia nyújt képernyőkép párbeszédpanel](./media/apache-storm-develop-csharp-event-hub-topology/submit.png)

3. A topológia elküldésekor a **Storm-topológiák Viewer** jelenik meg. Válassza ki, ha a topológiára vonatkozó adatokat a **EventHubReader** topológia a bal oldali ablaktáblán.

    ![A Storm-topológiák megjelenítő képernyőkép](./media/apache-storm-develop-csharp-event-hub-topology/topologyviewer.png)

4. A **Megoldáskezelőben**, kattintson a jobb gombbal a **EventHubWriter** projektre, és válassza a **Submit a HDInsight alatt futó Storm**.

5. Az a **nyújt topológia** párbeszédpanelen jelölje ki a **Storm-fürt**. Bontsa ki a **további konfigurációs**, jelölje be **Java elérési utat**, jelölje be **...** , és válassza ki a korábban letöltött JAR-fájlt tartalmazó könyvtárba. Végezetül kattintson **Submit**.

6. A topológia elküldésekor frissítse a topológia listáján, a **Storm-topológiák Viewer** annak ellenőrzéséhez, hogy mindkét topológia fut-e a fürtön.

7. A **Storm-topológiák Viewer**, jelölje be a **EventHubReader** topológia.

8. A bolt összegzése összetevő megnyitásához kattintson duplán a **LogBolt** összetevőt a diagramban.

9. Az a **végrehajtója** területen szereplő hivatkozások közül válassza ki a **Port** oszlop. Ez az összetevő által naplózott információk megjelenítése. A naplózott információk hasonlít a következő szöveget:

        2017-03-02 14:51:29.255 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,255 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1830978598,"deviceId":"8566ccbc-034d-45db-883d-d8a31f34068e"}
        2017-03-02 14:51:29.283 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,283 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1756413275,"deviceId":"647a5eff-823d-482f-a8b4-b95b35ae570b"}
        2017-03-02 14:51:29.313 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,312 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1108478910,"deviceId":"206a68fa-8264-4d61-9100-bfdb68ee8f0a"}

## <a name="stop-the-topologies"></a>A topológia leállítása

A topológiák leállításához válassza ki az egyes topológia a **Storm-topológia Viewer**, majd kattintson a **Kill**.

![Képernyőfelvétel a Storm topológia megjelenítő, a Kill gomb](./media/apache-storm-develop-csharp-event-hub-topology/killtopology.png)

## <a name="delete-your-cluster"></a>A fürt törlése

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>További lépések

Ebben a dokumentumban rendelkezik megtudta, hogyan használja a Java Event hub spout, és a C#-topológiák adatait az Azure Event Hubs boltok. C#-topológiák létrehozásával kapcsolatos további tudnivalókért olvassa el a következőket:

* [Visual Studio használatával HDInsight alatt futó Apache Storm a C#-topológiák fejlesztése](apache-storm-develop-csharp-visual-studio-topology.md)
* [Szolgáltatáskapcsolódási pont programozási útmutató](apache-storm-scp-programming-guide.md)
* [HDInsight alatt futó Storm példatopológiái](apache-storm-example-topology.md)
