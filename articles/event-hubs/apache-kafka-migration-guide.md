---
title: Migrálás az Azure Event Hubs for Apache Kafka
description: Ez a cikk bemutatja, hogy a különböző protokollokat (AMQP, Apache Kafka és HTTPS) használó felhasználók és gyártók Hogyan válthatnak be eseményeket az Azure Event Hubs használatakor.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2020
ms.author: shvija
ms.openlocfilehash: 32b08e565b86af8f6373c9848211646128bb346d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81677356"
---
# <a name="migrate-to-azure-event-hubs-for-apache-kafka-ecosystems"></a>Migrálás az Azure Event Hubs Apache Kafka ökoszisztémák számára
Az Azure Event Hubs egy Apache Kafka-végpontot tesz elérhetővé, amely lehetővé teszi, hogy a Kafka protokoll használatával kapcsolódjon a Event Hubshoz. Ha minimális módosításokat végez a meglévő Kafka-alkalmazásban, csatlakozhat az Azure Event Hubshoz, és kihasználhatja az Azure-ökoszisztéma előnyeit. A Kafka-támogatás Event Hubs [Apache Kafka 1,0](https://kafka.apache.org/10/documentation.html) -es és újabb verzióiban.

## <a name="pre-migration"></a>A migrálást megelőző folyamatok 

### <a name="create-an-azure-account"></a>Azure-fiók létrehozása
Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) .

### <a name="create-an-event-hubs-namespace"></a>Event Hubs-névtér létrehozása
Az Event Hubs névtér és az Event hub létrehozásához kövesse az [Event hub létrehozása](event-hubs-create.md) című cikkben ismertetett részletes útmutatót. 

### <a name="connection-string"></a>Kapcsolati sztring
Kövesse a [kapcsolatok karakterlánc beolvasása a portálról](event-hubs-get-connection-string.md#get-connection-string-from-the-portal) című cikk lépéseit. És jegyezze fel a kapcsolatok karakterláncát későbbi használatra. 

### <a name="fully-qualified-domain-name-fqdn"></a>Teljes tartománynév (FQDN)
Szükség lehet a teljes tartománynevet is, amely az Event hub-névtérre mutat. A teljes tartománynevet a következő módon lehet megtekinteni a kapcsolatok karakterláncában:

`Endpoint=sb://`**`mynamespace.servicebus.windows.net`**`/;SharedAccessKeyName=XXXXXX;SharedAccessKey=XXXXXX`

Ha a Event Hubs névtér nem nyilvános felhőre van telepítve, a tartománynév eltérhet (például \*. servicebus.chinacloudapi.cn, \*. servicebus.usgovcloudapi.net vagy \*. servicebus.cloudapi.de).

## <a name="migration"></a>Migrálás 

### <a name="update-your-kafka-client-configuration"></a>A Kafka-ügyfél konfigurációjának frissítése

A Kafka-kompatibilis Event hubhoz való kapcsolódáshoz frissítenie kell a Kafka-ügyfél konfigurációit. Ha nem találja meg a problémát, próbálja meg megkeresni `bootstrap.servers` , hogy hol van beállítva az alkalmazásban.

Szúrja be a következő konfigurációkat, bárhol is legyen értelme az alkalmazásban. Győződjön meg arról, hogy `bootstrap.servers` a `sasl.jaas.config` és az értékeket frissíti, hogy az ügyfelet a Event Hubs Kafka-végpontra irányítsa a megfelelő hitelesítéssel. 

```
bootstrap.servers={MYNAMESPACE}.servicebus.windows.net:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING TO YOUR NAMESPACE}";
``` 

Ha `sasl.jaas.config` a keretrendszer nem támogatja a konfigurációt, keresse meg a SASL felhasználónevének és jelszavának beállításához használt konfigurációkat, és használja őket. Állítsa be a felhasználónevet `$ConnectionString` és a jelszót a Event Hubs-kapcsolódási karakterláncra.

## <a name="post-migration"></a>A migrálást követő folyamatok
Futtasson egy Kafka-alkalmazást, amely eseményeket küld az Event hub-nak. Ezután ellenőrizze, hogy az Event hub fogadja-e az eseményeket a Azure Portal használatával. A Event Hubs névtér **Áttekintés** lapján váltson a **metrikák** szakasz **üzenetek** nézetére. Frissítse a lapot a diagram frissítéséhez. Eltarthat néhány másodpercig, amíg meg nem jelenik az üzenetek fogadása. 

[![Annak ellenőrzése, hogy az Event hub fogadta-e az üzeneteket](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)


## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni a Kafka Event Hubséről és Event Hubsról, tekintse meg a következő cikkeket:  

- [Event Hubs Apache Kafka hibaelhárítási útmutatója](apache-kafka-troubleshooting-guide.md)
- [Gyakori kérdések – Event Hubs Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [Apache Kafka fejlesztői útmutató az Azure-hoz Event Hubs](apache-kafka-developer-guide.md)
- [Ajánlott konfigurációk](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)