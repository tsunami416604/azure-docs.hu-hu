---
title: Áttelepítés az Apache Kafka Azure-beli eseményközpontokba
description: Ez a cikk bemutatja, hogy a különböző protokollokat (AMQP, Apache Kafka és HTTPS) használó felhasználók és gyártók hogyan cserélhetnek eseményeket az Azure Event Hubs használatakor.
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
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677356"
---
# <a name="migrate-to-azure-event-hubs-for-apache-kafka-ecosystems"></a>Áttelepítés az Apache Kafka-ökoszisztémák Azure-eseményközpontjába
Az Azure Event Hubs egy Apache Kafka-végpontot tesz elérhetővé, amely lehetővé teszi, hogy a Kafka protokoll használatával csatlakozzon az Event Hubs-hoz. A meglévő Kafka-alkalmazás minimális módosításával csatlakozhat az Azure Event Hubs-hoz, és kiaknázhatja az Azure-ökoszisztéma előnyeit. A Kafka eseményközpontjai támogatják [az Apache Kafka 1.0-s](https://kafka.apache.org/10/documentation.html) és újabb verzióját.

## <a name="pre-migration"></a>A migrálást megelőző folyamatok 

### <a name="create-an-azure-account"></a>Azure-fiók létrehozása
Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) mielőtt elkezdené.

### <a name="create-an-event-hubs-namespace"></a>Event Hubs-névtér létrehozása
Kövesse az Eseményközpontok névtere és egy [eseményközpont](event-hubs-create.md) létrehozásácímű cikk lépésenkénti utasításait. 

### <a name="connection-string"></a>Kapcsolati sztring
Kövesse a [kapcsolati karakterlánc leválasztása a portálcikkből származó lépéseit.](event-hubs-get-connection-string.md#get-connection-string-from-the-portal) És jegyezze fel a kapcsolati karakterláncot későbbi használatra. 

### <a name="fully-qualified-domain-name-fqdn"></a>Teljesen minősített tartománynév (FQDN)
Szükség lehet az Event Hub névterére mutat fqdn-re is. Az FQDN a következő kapcsolati karakterláncban található:

`Endpoint=sb://`**`mynamespace.servicebus.windows.net`**`/;SharedAccessKeyName=XXXXXX;SharedAccessKey=XXXXXX`

Ha az Event Hubs névtere nem nyilvános felhőben van telepítve, a \*tartománynév \*eltérhet \*(például .servicebus.chinacloudapi.cn, .servicebus.usgovcloudapi.net vagy .servicebus.cloudapi.de).

## <a name="migration"></a>Migrálás 

### <a name="update-your-kafka-client-configuration"></a>A Kafka-ügyfél konfigurációjának frissítése

A Kafka-kompatibilis Event Hubhoz való csatlakozáshoz frissítenie kell a Kafka-ügyfél konfigurációit. Ha nem találja meg a tiédet, `bootstrap.servers` próbálja meg keresni, hogy hol van beállítva az alkalmazásban.

Szúrja be a következő konfigurációkat bárhol, ahol van értelme az alkalmazásban. Győződjön meg `bootstrap.servers` arról, hogy frissítse a és `sasl.jaas.config` az értékeket, hogy irányítsa az ügyfél az Event Hubs Kafka végpont a megfelelő hitelesítéssel. 

```
bootstrap.servers={MYNAMESPACE}.servicebus.windows.net:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING TO YOUR NAMESPACE}";
``` 

Ha `sasl.jaas.config` nem támogatott konfiguráció a keretrendszerben, keresse meg a SASL felhasználónév és jelszó beállításához használt konfigurációkat, és használja őket helyette. Állítsa be a `$ConnectionString` felhasználónevet és a jelszót az Event Hubs kapcsolati karakterláncához.

## <a name="post-migration"></a>A migrálást követő folyamatok
Futtassa a Kafka-alkalmazást, amely eseményeket küld az eseményközpontba. Ezután ellenőrizze, hogy az eseményközpont fogadja-e az eseményeket az Azure Portal használatával. Az **Event** Hubs névtér Áttekintés lapján váltson át a **Metrikák** szakasz **Üzenetek** nézetére. A lap frissítése a diagram frissítéséhez. Eltarthat néhány másodpercig, amíg megis szállhat meg, hogy az üzenetek érkeztek. 

[![Annak ellenőrzése, hogy az eseményközpont megkapta-e az üzeneteket](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)


## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni a Kafka eseményközpontjairól és eseményközpontjairól, olvassa el az alábbi cikkeket:  

- [Az Apache Kafka hibaelhárítási útmutatója az Event Hubs-hoz](apache-kafka-troubleshooting-guide.md)
- [Gyakori kérdések – Az Apache Kafka eseményközpontjai](apache-kafka-frequently-asked-questions.md)
- [Az Apache Kafka fejlesztői útmutatója az Azure Event Hubs-hoz](apache-kafka-developer-guide.md)
- [Ajánlott konfigurációk](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)