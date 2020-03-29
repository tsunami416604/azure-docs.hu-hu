---
title: Adatok betöltése a Kafkából az Azure Data Explorerbe
description: Ebben a cikkben megtudhatja, hogyan töltheti be (töltheti be) az adatokat a Kafka Azure Data Explorerbe.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 03b46ff50683149a22c71ccb155480a0f08455bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "66497280"
---
# <a name="ingest-data-from-kafka-into-azure-data-explorer"></a>Adatok betöltése a Kafkából az Azure Data Explorerbe
 
Az Azure Adatkezelő egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. Az Azure Data Explorer a Kafka betöltését (adatbetöltését) kínálja. A Kafka egy elosztott streamelési platform, amely lehetővé teszi a valós idejű streamelési adatfolyamatok kiépítését, amelyek megbízhatóan mozgatják az adatokat a rendszerek vagy alkalmazások között.
 
## <a name="prerequisites"></a>Előfeltételek
 
* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt. 
 
* [Tesztfürt és adatbázis](create-cluster-database-portal.md).
 
* [Egy mintaalkalmazás,](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/kafka) amely adatokat hoz létre, és elküldi azokat a Kafkának.

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) a mintaalkalmazás futtatásához.
 
## <a name="kafka-connector-setup"></a>Kafka csatlakozó beállítása

A Kafka Connect egy olyan eszköz, amely az Apache Kafka és más rendszerek közötti skálázható és megbízható adatfolyam-továbbítást szolgál. Ez leegyszerűsíti a kafka-ba és a Kafkába nagy adatgyűjteményeket áthelyező összekötők gyors definiálását. Az ADX Kafka Sink a Kafka összekötőjeként szolgál.
 
### <a name="bundle"></a>Csomag

Kafka lehet `.jar` betölteni egy plugin, amely működni fog, mint egy egyéni csatlakozó. Ahhoz, `.jar`hogy egy ilyen , akkor klón a kódot helyben és épít segítségével Maven. 

#### <a name="clone"></a>Klónozás

```bash
git clone git://github.com:Azure/kafka-sink-azure-kusto.git
cd ./kafka-sink-azure-kusto/kafka/
```

#### <a name="build"></a>Felépítés

Helyileg a Maven-nel `.jar` hozhat létre függőségekkel rendelkező teljes körű buildelést.

* JDK >= 1,8 [letöltés](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Maven [letöltés](https://maven.apache.org/install.html)
 

A *gyökérkönyvtárban kafka-sink-azure-kusto*, fuss:

```bash
mvn clean compile assembly:single
```

### <a name="deploy"></a>Üzembe helyezés 

Load plugin a Kafka. A docker használatával egy üzembe helyezési példa megtalálható a [kafka-sink-azure-kusto](https://github.com/Azure/kafka-sink-azure-kusto#deploy)
 

Részletes dokumentáció a Kafka csatlakozókról és azok üzembe helyezéséről a [Kafka Connect](https://kafka.apache.org/documentation/#connect) 

### <a name="example-configuration"></a>Konfigurációs példa 
 
```config
name=KustoSinkConnector 
connector.class=com.microsoft.azure.kusto.kafka.connect.sink.KustoSinkConnector 
kusto.sink.flush_interval_ms=300000 
key.converter=org.apache.kafka.connect.storage.StringConverter 
value.converter=org.apache.kafka.connect.storage.StringConverter 
tasks.max=1 
topics=testing1 
kusto.tables.topics_mapping=[{'topic': 'testing1','db': 'daniel', 'table': 'TestTable','format': 'json', 'mapping':'TestMapping'}] 
kusto.auth.authority=XXX 
kusto.url=https://ingest-{mycluster}.kusto.windows.net/ 
kusto.auth.appid=XXX 
kusto.auth.appkey=XXX 
kusto.sink.tempdir=/var/tmp/ 
kusto.sink.flush_size=1000
```
 
## <a name="create-a-target-table-in-adx"></a>Céltábla létrehozása az ADX-ben
 
Hozzon létre egy táblát az ADX-ben, amelyre a Kafka adatokat küldhet. Hozza létre a táblát a fürtben és az **Előfeltételek**területen kiépített adatbázisban.
 
1. Az Azure Portalon keresse meg a fürtöt, és válassza a **Lekérdezés**lehetőséget.
 
    ![Alkalmazáshivatkozás lekérdezése](media/ingest-data-event-hub/query-explorer-link.png)
 
1. Másolja be a következő parancsot az ablakba, és válassza a **Futtatás** lehetőséget.
 
    ```Kusto
    .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
    ```
 
    ![Létrehozási lekérdezés futtatása](media/ingest-data-event-hub/run-create-query.png)
 
1. Másolja be a következő parancsot az ablakba, és válassza a **Futtatás** lehetőséget.
 
    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.timeStamp","datatype":"datetime"},{"column":"Name","path":"$.name","datatype":"string"},{"column":"Metric","path":"$.metric","datatype":"int"},{"column":"Source","path":"$.source","datatype":"string"}]'
    ```

    A parancs a bejövő JSON-adatokat leképezi a táblában (TestTable) szereplő oszlopnevekre és adattípusokra.


## <a name="generate-sample-data"></a>Mintaadatok létrehozása

Most, hogy a Kafka-fürt csatlakozik az ADX-hez, használja a letöltött [mintaalkalmazást](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) az adatok létrehozásához.

### <a name="clone"></a>Klónozás

Klónozza a mintaalkalmazást helyileg:

```cmd
git clone git://github.com:Azure/azure-kusto-samples-dotnet.git
cd ./azure-kusto-samples-dotnet/kafka/
```

### <a name="run-the-app"></a>Az alkalmazás futtatása

1. Nyissa meg a mintaalkalmazást a Visual Studióban.

1. A `Program.cs` fájlban frissítse `connectionString` az állandót a Kafka kapcsolati karakterláncra.

    ```csharp    
    const string connectionString = @"<YourConnectionString>";
    ```

1. Hozza létre és futtassa az alkalmazást. Az alkalmazás üzeneteket küld a Kafka-fürtnek, és 10 másodpercenként kinyomtatja az állapotát.

1. Miután az alkalmazás elküldött néhány üzenetet, lépjen tovább a következő lépésre.
 
## <a name="query-and-review-the-data"></a>Adatok lekérdezése és áttekintése

1. Annak érdekében, hogy a betöltés során ne történt hiba:

    ```Kusto
    .show ingestion failures
    ```

1. Az újonnan bevitt adatok megtekintése:

    ```Kusto
    TestTable 
    | count
    ```

1. Az üzenetek tartalmának megtekintése:
 
    ```Kusto
    TestTable
    ```
 
    Az eredményhalmaznak a következőkre kell hasonlítania:
 
    ![Üzenetek eredményhalmaza](media/ingest-data-event-hub/message-result-set.png)
 
## <a name="next-steps"></a>További lépések
 
* [Adatok lekérdezése az Azure Data Explorerben](web-query-data.md)
