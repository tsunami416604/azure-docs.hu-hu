---
title: fájlbefoglalás
description: fájlbefoglalás
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 11/19/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: dac82692c76d9d36b1f25d7b93b5c3a2e2400672
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002791"
---
### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Milyen portokat kell megnyitni a tűzfalon? 
Az Azure Event Hubs a következő protokollokat használhatja az események küldéséhez és fogadásához:

- Advanced Message Queueing Protocol 1,0 (AMQP)
- 1,1 Hypertext Transfer Protocol TLS-vel (HTTPS)
- Apache Kafka

Az alábbi táblázat tartalmazza azokat a kimenő portokat, amelyeket meg kell nyitni a protokollok Azure Event Hubs-vel való kommunikációhoz való használatához. 

| Protokoll | Portok | Részletek | 
| -------- | ----- | ------- | 
| AMQP | 5671 és 5672 | Lásd: [AMQP protokoll – útmutató](../articles/service-bus-messaging/service-bus-amqp-protocol-guide.md) | 
| HTTPS | 443 | Ez a port a HTTP/REST API és a AMQP-WebSockets esetében használatos. |
| Kafka | 9093 | Lásd: [Event Hubs használata a Kafka-alkalmazásokból](../articles/event-hubs/event-hubs-for-kafka-ecosystem-overview.md)

A kimenő kommunikációhoz a HTTPS-port szükséges abban az esetben is, ha a AMQP a 5671-as porton keresztül használja, mert több, az ügyfél SDK-k által végrehajtott felügyeleti művelet, valamint a tokenek beszerzése Azure Active Directory (ha használatban van) HTTPS protokollon keresztül 

A hivatalos Azure SDK-k általában az AMQP protokollt használják az események küldésére és fogadására Event Hubsból. A AMQP-over-WebSockets protokoll az 443-as TCP-porton fut, ugyanúgy, mint a HTTP API, de egyébként az egyszerű AMQP eltérően működik. Ez a beállítás nagyobb kezdeti kapcsolati késéssel jár, mert a további kézfogási utak és valamivel nagyobb terhelés a HTTPS-port megosztásához szükséges kompromisszum. Ha ez a mód be van jelölve, a 443-es TCP-port elegendő a kommunikációhoz. A következő lehetőségek lehetővé teszik az egyszerű AMQP vagy a AMQP WebSockets mód kiválasztását:

| Nyelv | Beállítás   |
| -------- | ----- |
| .NET     | [EventHubConnectionOptions. TransportType](/dotnet/api/azure.messaging.eventhubs.eventhubconnectionoptions.transporttype?view=azure-dotnet&preserve-view=true) tulajdonság a [EventHubsTransportType. AmqpTcp](/dotnet/api/azure.messaging.eventhubs.eventhubstransporttype?view=azure-dotnet&preserve-view=true) vagy a [EventHubsTransportType. AmqpWebSockets](/dotnet/api/azure.messaging.eventhubs.eventhubstransporttype?view=azure-dotnet&preserve-view=true) |
| Java     | [com. microsoft. Azure. eventhubs. EventProcessorClientBuilder. transportType](/java/api/com.azure.messaging.eventhubs.eventprocessorclientbuilder.transporttype?view=azure-java-stable&preserve-view=true) , [AmqpTransportType. AMQP](/java/api/com.azure.core.amqp.amqptransporttype?view=azure-java-stable&preserve-view=true) vagy [AmqpTransportType.AMQP_WEB_SOCKETS](/java/api/com.azure.core.amqp.amqptransporttype?view=azure-java-stable&preserve-view=true) |
| Csomópont  | A [EventHubConsumerClientOptions](/javascript/api/@azure/event-hubs/eventhubconsumerclientoptions?view=azure-node-latest&preserve-view=true) rendelkezik egy `webSocketOptions` tulajdonsággal. |
| Python | [EventHubConsumerClient.transport_type](/python/api/azure-eventhub/azure.eventhub.eventhubconsumerclient?view=azure-python&preserve-view=true) [TransportType. Amqp](/python/api/azure-eventhub/azure.eventhub.transporttype?view=azure-python) vagy [TransportType. AmqpOverWebSocket](/python/api/azure-eventhub/azure.eventhub.transporttype?view=azure-python&preserve-view=true) |

### <a name="what-ip-addresses-do-i-need-to-allow"></a>Milyen IP-címeket kell engedélyezni?
Ha az Azure-t használja, időnként engedélyeznie kell bizonyos IP-címtartományok vagy URL-címek használatát a vállalati tűzfalon vagy proxyn a használt összes Azure-szolgáltatás eléréséhez. Ellenőrizze, hogy engedélyezett-e a forgalom a Event Hubs által használt IP-címeken. Az Azure Event Hubs által használt IP-címek esetében lásd: [Azure IP-címtartományok és szolgáltatás-címkék – nyilvános felhő](https://www.microsoft.com/download/details.aspx?id=56519).

Ellenőrizze azt is, hogy a névtér IP-címe engedélyezett-e. Az alábbi lépéseket követve megkeresheti a kapcsolatok elérését lehetővé tevő megfelelő IP-címeket:

1. Futtassa a következő parancsot egy parancssorból: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Jegyezze fel a visszaadott IP-címet `Non-authoritative answer` . 

Ha a **zóna redundanciát** használja a névtérhez, néhány további lépést is végre kell hajtania: 

1. Először futtassa az nslookupt a névtéren.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Jegyezze fel a nevet a **nem mérvadó válasz** szakaszban, amely az alábbi formátumok egyike: 

    ```
    <name>-s1.cloudapp.net
    <name>-s2.cloudapp.net
    <name>-s3.cloudapp.net
    ```
3. Futtassa az nslookupt mindegyikhez az S1, az S2 és az S3 utótaggal a három rendelkezésre állási zónában futó mindhárom példány IP-címeinek lekéréséhez. 

    > [!NOTE]
    > A parancs által visszaadott IP-cím `nslookup` nem statikus IP-cím. Azonban állandó marad, amíg a mögöttes központi telepítést nem törlik, vagy áthelyezik egy másik fürtre.

### <a name="where-can-i-find-client-ip-sending-or-receiving-messages-to-my-namespace"></a>Hol találhatom meg az ügyfél IP-küldését vagy az üzenetek fogadását a névtérhez?
Először engedélyezze az [IP-szűrést](../articles/event-hubs/event-hubs-ip-filtering.md) a névtérben. 

Ezután engedélyezze a diagnosztikai naplókat [Event Hubs virtuális hálózati kapcsolatok eseményeihez](../articles/event-hubs/event-hubs-diagnostic-logs.md#event-hubs-virtual-network-connection-event-schema) a [diagnosztikai naplók engedélyezése](../articles/event-hubs/event-hubs-diagnostic-logs.md#enable-diagnostic-logs)című témakör utasításait követve. Ekkor megtekintheti az IP-címet, amelyhez a rendszer megtagadja a kapcsolódást.

```json
{
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "IPAddress": "1.2.3.4",
    "Action": "Deny Connection",
    "Reason": "IPAddress doesn't belong to a subnet with Service Endpoint enabled.",
    "Count": "65",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "EventHubVNetConnectionEvent"
}
```

> [!IMPORTANT]
> A virtuális hálózati naplók csak akkor jönnek létre, ha a névtér engedélyezi **bizonyos IP-címek** (IP-szűrési szabályok) elérését. Ha nem szeretné korlátozni a névtér elérését ezekkel a szolgáltatásokkal, és továbbra is szeretné lekérni a virtuális hálózati naplókat a Event Hubs névtérhez csatlakozó ügyfelek IP-címeinek nyomon követéséhez, a következő áthidaló megoldást használhatja: engedélyezze az IP-szűrést, és adja hozzá a teljes címezhető IPv4-tartományt (1.0.0.0/1-255.0.0.0/1). A Event Hubs nem támogatja az IPv6-címtartományok használatát. 
