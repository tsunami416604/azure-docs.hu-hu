---
title: "Az Azure-továbbító .NET-szabvány API-k áttekintése |} Microsoft Docs"
description: "Azure továbbítási .NET-szabvány API – áttekintés"
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: b1da9ac1-811b-4df7-a22c-ccd013405c40
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: sethm
ms.openlocfilehash: aa1863a44f00ae17f63b02c7c247b2c9fd9925f6
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2018
---
# <a name="azure-relay-hybrid-connections-net-standard-api-overview"></a>Azure Relay a hibrid kapcsolatok .NET Standard API – áttekintés

Ez a cikk foglal össze néhányat az Azure Relay a hibrid kapcsolatok .NET szabványos kulcs [ügyfél API-k](/dotnet/api/microsoft.azure.relay).
  
## <a name="relay-connection-string-builder-class"></a>Továbbítási kapcsolat-karakterlánc szerkesztő osztály

A [RelayConnectionStringBuilder] [ RelayConnectionStringBuilder] osztály formázza a hibrid kapcsolatok adott kapcsolati karakterláncokat. Ellenőrizze a kapcsolati karakterlánc formátuma, vagy teljesen új kapcsolati karakterlánc létrehozásához használhatja. A következő kód egy vonatkozó példáért lásd:

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

Egy kapcsolati karakterláncot is közvetlenül továbbítani a `RelayConnectionStringBuilder` metódust. Ez a művelet lehetővé teszi annak ellenőrzéséhez, hogy a kapcsolati karakterlánc formátuma érvénytelen. Ha bármelyik paraméter érvénytelen, a konstruktor hoz létre egy `ArgumentException`.

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

## <a name="hybrid-connection-stream"></a>A hibrid kapcsolat adatfolyam

A [HybridConnectionStream] [ HCStream] osztály egy olyan objektum, hogy dolgozik-e adatokat küldeni és fogadni az Azure-továbbítási végpontok használt elsődleges egy [HybridConnectionClient][HCClient], vagy egy [HybridConnectionListener][HCListener].

### <a name="getting-a-hybrid-connection-stream"></a>A hibrid kapcsolat adatfolyam beolvasása

#### <a name="listener"></a>Figyelő

Használatával egy [HybridConnectionListener] [ HCListener] objektum, beszerezhet egy `HybridConnectionStream` objektumot az alábbiak szerint:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var listener = new HybridConnectionListener(csb.ToString());
// Open a connection to the Relay endpoint
await listener.OpenAsync();
// Get a `HybridConnectionStream`
var hybridConnectionStream = await listener.AcceptConnectionAsync();
```

#### <a name="client"></a>Ügyfél

Használatával egy [HybridConnectionClient] [ HCClient] objektum, beszerezhet egy `HybridConnectionStream` objektumot az alábbiak szerint:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var client = new HybridConnectionClient(csb.ToString());
// Open a connection to the Relay endpoint and get a `HybridConnectionStream`
var hybridConnectionStream = await client.CreateConnectionAsync();
```

### <a name="receiving-data"></a>Adatok fogadása

A [HybridConnectionStream] [ HCStream] osztály lehetővé teszi, hogy a kétirányú kommunikációt. A legtöbb esetben folyamatosan kapni az adatfolyamból. Ha a szöveg olvas be az adatfolyamból, előfordulhat, hogy is használni kívánt egy [StreamReader](https://msdn.microsoft.com/library/system.io.streamreader(v=vs.110).aspx) objektum, amely lehetővé teszi az adatok egyszerűbb elemzése. Például olvasható adatok szövegként, nem `byte[]`.

A következő kódot csak egy megszakítását kérték olvassa be az adatfolyamból sorokat:

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

Ha már létrehozott kapcsolat, egy üzenetet küldhet a továbbítási végpont. Mert a kapcsolat objektum örökli [adatfolyam](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx), mint az adatok küldése egy `byte[]`. A következő példa bemutatja, hogyan ehhez:

```csharp
var data = Encoding.UTF8.GetBytes("hello");
await clientConnection.WriteAsync(data, 0, data.Length);
```

Azonban, ha szeretne küldeni szöveges közvetlenül, anélkül, hogy a karakterlánc kódolása minden alkalommal, amikor, akkor futtathatja a `hybridConnectionStream` rendelkező objektum egy [StreamWriter](https://msdn.microsoft.com/library/system.io.streamwriter(v=vs.110).aspx) objektum.

```csharp
// The StreamWriter object only needs to be created once
var textWriter = new StreamWriter(hybridConnectionStream);
await textWriter.WriteLineAsync("hello");
```

## <a name="next-steps"></a>További lépések

Azure-továbbítási kapcsolatos további információkért látogasson el ezeket a hivatkozásokat:

* [Microsoft.Azure.Relay hivatkozás](/dotnet/api/microsoft.azure.relay)
* [Mi az az Azure Relay?](relay-what-is-it.md)
* [Rendelkezésre álló továbbítási API-k](relay-api-overview.md)

[RelayConnectionStringBuilder]: /dotnet/api/microsoft.azure.relay.relayconnectionstringbuilder
[HCStream]: /dotnet/api/microsoft.azure.relay.hybridconnectionstream
[HCClient]: /dotnet/api/microsoft.azure.relay.hybridconnectionclient
[HCListener]: /dotnet/api/microsoft.azure.relay.hybridconnectionlistener