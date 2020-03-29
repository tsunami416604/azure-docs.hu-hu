---
title: Az Azure Relay .NET standard API-k áttekintése | Microsoft dokumentumok
description: Ez a cikk összefoglalja az Azure Relay hybrid connections .NET Standard API néhány kulcsfontosságú áttekintését.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: b1da9ac1-811b-4df7-a22c-ccd013405c40
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: spelluru
ms.openlocfilehash: 18eaf2d2daae817107be6cdb0da9359bb5f9b4e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514535"
---
# <a name="azure-relay-hybrid-connections-net-standard-api-overview"></a>Az Azure Relay hibrid kapcsolatok –NET standard API – áttekintés

Ez a cikk összefoglalja az Azure Relay hybrid connections .NET Standard [ügyfél API-k néhány kulcsát.](/dotnet/api/microsoft.azure.relay)
  
## <a name="relay-connection-string-builder-class"></a>Kapcsolatszerkesztő továbbítása osztály

A [RelayConnectionStringBuilder][RelayConnectionStringBuilder] osztály a hibrid kapcsolatok továbbítására jellemző kapcsolati karakterláncokat formázza. Segítségével ellenőrizheti a kapcsolati karakterlánc formátumát, vagy teljesen új kapcsolati karakterláncot hozhat létre. Lásd a következő kódot egy példa:

```csharp
var endpoint = "[Relay namespace]";
var entityPath = "[Name of the Hybrid Connection]";
var sharedAccessKeyName = "[SAS key name]";
var sharedAccessKey = "[SAS key value]";

var connectionStringBuilder = new RelayConnectionStringBuilder()
{
    Endpoint = endpoint,
    EntityPath = entityPath,
    SharedAccessKeyName = sasKeyName,
    SharedAccessKey = sasKeyValue
};
```

A kapcsolati karakterláncot közvetlenül `RelayConnectionStringBuilder` is átadhatja a metódusnak. Ez a művelet lehetővé teszi annak ellenőrzését, hogy a kapcsolati karakterlánc érvényes formátumú-e. Ha a paraméterek bármelyike érvénytelen, a konstruktor létrehoz egy `ArgumentException`.

```csharp
var myConnectionString = "[RelayConnectionString]";
// Declare the connectionStringBuilder so that it can be used outside of the loop if needed
RelayConnectionStringBuilder connectionStringBuilder;
try
{
    // Create the connectionStringBuilder using the supplied connection string
    connectionStringBuilder = new RelayConnectionStringBuilder(myConnectionString);
}
catch (ArgumentException ae)
{
    // Perform some error handling
}
```

## <a name="hybrid-connection-stream"></a>Hibrid kapcsolatfolyam

A [HybridConnectionStream][HCStream] osztály az elsődleges objektum, amely et az Azure Relay végpontról történő adatküldésre és -fogadásra használják, függetlenül attól, hogy [Egy HybridConnectionClient][HCClient]vagy egy [HybridConnectionListener][HCListener]használatával dolgozik.

### <a name="getting-a-hybrid-connection-stream"></a>Hibrid kapcsolatfolyam beszerzése

#### <a name="listener"></a>Figyelő

A [HybridConnectionListener][HCListener] objektum használatával az `HybridConnectionStream` alábbi módon szerezhet be egy objektumot:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var listener = new HybridConnectionListener(csb.ToString());
// Open a connection to the Relay endpoint
await listener.OpenAsync();
// Get a `HybridConnectionStream`
var hybridConnectionStream = await listener.AcceptConnectionAsync();
```

#### <a name="client"></a>Ügyfél

A [HybridConnectionClient][HCClient] objektum használatával az `HybridConnectionStream` alábbi módon szerezhet be egy objektumot:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var client = new HybridConnectionClient(csb.ToString());
// Open a connection to the Relay endpoint and get a `HybridConnectionStream`
var hybridConnectionStream = await client.CreateConnectionAsync();
```

### <a name="receiving-data"></a>Adatok fogadása

A [HybridConnectionStream][HCStream] osztály kétirányú kommunikációt tesz lehetővé. A legtöbb esetben folyamatosan érkezik az adatfolyamból. Ha az adatfolyamból olvas szöveget, akkor egy [StreamReader-objektumot](https://msdn.microsoft.com/library/system.io.streamreader(v=vs.110).aspx) is érdemes használnia, amely megkönnyíti az adatok elemzését. Az adatokat például szövegként is `byte[]`felolvashatja, nem pedig a .

A következő kód az adatfolyam egyes sorait olvassa be a törlés kéréséig:

```csharp
// Create a CancellationToken, so that we can cancel the while loop
var cancellationToken = new CancellationToken();
// Create a StreamReader from the 'hybridConnectionStream`
var streamReader = new StreamReader(hybridConnectionStream);

while (!cancellationToken.IsCancellationRequested)
{
    // Read a line of input until a newline is encountered
    var line = await streamReader.ReadLineAsync();
    if (string.IsNullOrEmpty(line))
    {
        // If there's no input data, we will signal that 
        // we will no longer send data on this connection
        // and then break out of the processing loop.
        await hybridConnectionStream.ShutdownAsync(cancellationToken);
        break;
    }
}
```

### <a name="sending-data"></a>Adatok küldése

Miután létrejött a kapcsolat, üzenetet küldhet a Továbbítás végpontnak. Mivel a kapcsolatobjektum örökli [a](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx)Stream `byte[]`objektumot, az adatokat a rendszerre küldi. A következő példa bemutatja, hogyan kell ezt megtenni:

```csharp
var data = Encoding.UTF8.GetBytes("hello");
await clientConnection.WriteAsync(data, 0, data.Length);
```

Ha azonban közvetlenül szeretne szöveget küldeni anélkül, hogy minden alkalommal kódolnia kellene a karakterláncot, az `hybridConnectionStream` objektumot [streamwriter](https://msdn.microsoft.com/library/system.io.streamwriter(v=vs.110).aspx) objektummal csomagolhatja.

```csharp
// The StreamWriter object only needs to be created once
var textWriter = new StreamWriter(hybridConnectionStream);
await textWriter.WriteLineAsync("hello");
```

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az Azure Relayről, látogasson el az alábbi hivatkozásokra:

* [Microsoft.Azure.Relay – referencia](/dotnet/api/microsoft.azure.relay)
* [Mi az az Azure Relay?](relay-what-is-it.md)
* [Elérhető relé API-k](relay-api-overview.md)

[RelayConnectionStringBuilder]: /dotnet/api/microsoft.azure.relay.relayconnectionstringbuilder
[HCStream]: /dotnet/api/microsoft.azure.relay.hybridconnectionstream
[HCClient]: /dotnet/api/microsoft.azure.relay.hybridconnectionclient
[HCListener]: /dotnet/api/microsoft.azure.relay.hybridconnectionlistener