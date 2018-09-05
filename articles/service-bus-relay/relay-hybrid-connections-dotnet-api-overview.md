---
title: .NET Standard API-k az Azure Relay áttekintése |} A Microsoft Docs
description: Az Azure Relay .NET Standard API áttekintése
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
ms.openlocfilehash: a6a1706c8d1e849fd1bb4309c46063dd3f9439c1
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43700140"
---
# <a name="azure-relay-hybrid-connections-net-standard-api-overview"></a>Az Azure Relay hibrid kapcsolatok .NET Standard API áttekintése

Ez a cikk az Azure Relay hibrid kapcsolatok .NET Standard kulcs összefoglaltuk [ügyfél API-k](/dotnet/api/microsoft.azure.relay).
  
## <a name="relay-connection-string-builder-class"></a>Relay Kapcsolatisztring-osztály

A [RelayConnectionStringBuilder] [ RelayConnectionStringBuilder] osztály formázza a kapcsolati karakterláncok, konkrétan a hibrid Relay-kapcsolatokhoz. Ellenőrizze a kapcsolati karakterlánc formátuma, vagy egy teljesen új kapcsolati karakterlánc összeállítása használhatja. Tekintse meg példaként a következő kódot:

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

Közvetlenül is átadhat egy kapcsolati karakterláncot a `RelayConnectionStringBuilder` metódust. Ez a művelet lehetővé teszi, hogy ellenőrizze, hogy a kapcsolati karakterlánc formátuma érvénytelen. Ha bármelyik paraméter érvénytelen, a konstruktor hoz létre egy `ArgumentException`.

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

## <a name="hybrid-connection-stream"></a>Hibrid kapcsolat stream

A [HybridConnectionStream] [ HCStream] osztály dolgozik-e adatokat küldeni és fogadni az Azure Relay-végpontról, a használt elsődleges objektum egy [HybridConnectionClient] [ HCClient], vagy egy [HybridConnectionListener][HCListener].

### <a name="getting-a-hybrid-connection-stream"></a>A hibrid kapcsolat stream lekérdezése

#### <a name="listener"></a>Figyelő

Használatával egy [HybridConnectionListener] [ HCListener] objektumot, beszerezhet egy `HybridConnectionStream` objektumot az alábbiak szerint:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var listener = new HybridConnectionListener(csb.ToString());
// Open a connection to the Relay endpoint
await listener.OpenAsync();
// Get a `HybridConnectionStream`
var hybridConnectionStream = await listener.AcceptConnectionAsync();
```

#### <a name="client"></a>Ügyfél

Használatával egy [HybridConnectionClient] [ HCClient] objektumot, beszerezhet egy `HybridConnectionStream` objektumot az alábbiak szerint:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var client = new HybridConnectionClient(csb.ToString());
// Open a connection to the Relay endpoint and get a `HybridConnectionStream`
var hybridConnectionStream = await client.CreateConnectionAsync();
```

### <a name="receiving-data"></a>Adatok fogadása

A [HybridConnectionStream] [ HCStream] osztály lehetővé teszi a kétirányú kommunikációt. A legtöbb esetben folyamatosan kap az adatfolyamból. Ha szöveget olvas be az adatfolyamból, akkor is érdemes használni egy [StreamReader](https://msdn.microsoft.com/library/system.io.streamreader(v=vs.110).aspx) objektum, amely lehetővé teszi, hogy az adatok könnyebben elemzése. Például olvasható adatok szövegként, nem `byte[]`.

A következő kódot mindaddig, amíg a megszakítási kérés érkezett olvassa be az adatfolyamból egyes sornyi szöveg:

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

Miután a kapcsolat létrejött, egy üzenetet küldhet a Relay-végpontot. Mivel a kapcsolat objektumot örökli [Stream](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx), küldje el az adatokat, mint egy `byte[]`. A következő példa ezt mutatja be:

```csharp
var data = Encoding.UTF8.GetBytes("hello");
await clientConnection.WriteAsync(data, 0, data.Length);
```

Azonban, ha szeretne küldeni, közvetlenül a szöveg anélkül, hogy a karakterlánc kódolása minden alkalommal, amikor, akkor futtathatja a `hybridConnectionStream` rendelkező objektum egy [StreamWriter](https://msdn.microsoft.com/library/system.io.streamwriter(v=vs.110).aspx) objektum.

```csharp
// The StreamWriter object only needs to be created once
var textWriter = new StreamWriter(hybridConnectionStream);
await textWriter.WriteLineAsync("hello");
```

## <a name="next-steps"></a>További lépések

Az Azure Relay kapcsolatos további információkért látogasson el ezeket a hivatkozásokat:

* [Microsoft.Azure.Relay referencia](/dotnet/api/microsoft.azure.relay)
* [Mi az az Azure Relay?](relay-what-is-it.md)
* [Rendelkezésre álló Relay API-k](relay-api-overview.md)

[RelayConnectionStringBuilder]: /dotnet/api/microsoft.azure.relay.relayconnectionstringbuilder
[HCStream]: /dotnet/api/microsoft.azure.relay.hybridconnectionstream
[HCClient]: /dotnet/api/microsoft.azure.relay.hybridconnectionclient
[HCListener]: /dotnet/api/microsoft.azure.relay.hybridconnectionlistener