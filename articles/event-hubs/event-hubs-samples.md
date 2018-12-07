---
title: Azure Event Hubs-minták |} A Microsoft Docs
description: Azure Event Hubs-minták
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 7a1b7cc534c68027163625d3c7be785a144f4b9e
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53013577"
---
# <a name="git-repositories-with-samples-for-azure-event-hubs"></a>Git-tárházakat, az Azure Event Hubs-minták 
Az Event Hubs-minták találhat [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples). Ezek a minták bemutatják a legfontosabb jellemzők [Azure Event Hubs](/azure/event-hubs/). Ez a cikk kategorizálja, és ismerteti a rendelkezésre álló, hivatkozásokkal az egyes minták.

## <a name="net-samples"></a>.NET-minták

| Minta neve | Leírás | 
| ----------- | ----------- | 
| [SampleSender](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender) | Ez a példa bemutatja, hogyan írható olyan .NET Core-konzolalkalmazást, amely események egy meghatározott készletének küld egy eseményközpontnak. |
| [SampleEHReceiver](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) | Ez a példa bemutatja, hogyan írható olyan .NET Core-konzolalkalmazást, amely események egy meghatározott készletének fogad egy eseményközpontból az Event Processor Host tár használatával.  | 

## <a name="java-samples"></a>Java-példák

| Minta neve | Leírás | 
| ----------- | ----------- | 
| [SendBatch](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SendBatch)  | Ez a példa bemutatja, hogyan kötegek események fogadása az event hubs-eseményközpontba. | 
| [SimpleSend](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend) | Ez a példa bemutatja, hogyan események fogadása az event hubs-eseményközpontba. |
| [AdvanceSendOptions](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/AdvancedSendOptions) | Ez a példa bemutatja az eseményeket, hogy az Event hubs szolgáltatással elérhető különböző lehetőségek. |
| [ReceiveByDateTime](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/ReceiveByDateTime) | Ez a példa bemutatja, hogyan események fogadása az event hub-partícióról használatával egy adott dátum-idő eltolása. |
| [ReceiveUsingOffset](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/ReceiveUsingOffset) | Ez a példa bemutatja, hogyan események fogadása az event hub-partícióról használatával egy adott eltolása. |  
| [ReceiveUsingSequenceNumber](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/ReceiveUsingSequenceNumber) | Ez a példa bemutatja, hogyan igényelhető az event hub partíciók használatával egy megfelelő sorszám. |   
| [EventProcessorSample](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/EventProcessorSample) |Ez a példa bemutatja, hogyan események fogadása az eseményközpontok a több egyidejű fogadóra automatikus partíció-kiválasztási és -feladatátvételt biztosító event processor host használatával. | 
| [AutoScaleOnIngress](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Benchmarks/AutoScaleOnIngress) | Ez a példa bemutatja, hogyan egy eseményközpont is automatikus vertikális felskálázás a magas terhelés. A minta gyakorisággal csupán haladhatja meg az eseményközpontok felé, ami az vertikális felskálázása az event hubs konfigurált arányát eseményeket küld. | 
| [IngressBenchmark](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Benchmarks/IngressBenchmark) | Ez a minta lehetővé teszi, hogy a bejövő forgalom mérése. | 

## <a name="python-samples"></a>Python-példák
Python-minták az Azure Event Hubs megtalálja a [azure-event-hubs-python](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) GitHub-adattárban.

## <a name="nodejs-samples"></a>Node.js-példák
Node.js-minták az Azure Event Hubs megtalálja a [azure-event-hubs-csomópont](https://github.com/Azure/azure-event-hubs-node) GitHub-adattárban.

## <a name="go-samples"></a>Go-minta
Go-minták az Azure Event Hubs megtalálja a [azure-event-hubs-go](https://github.com/Azure/azure-event-hubs-go/tree/master/_examples) GitHub-adattárban.

## <a name="azure-cli-samples"></a>Azure CLI-minták
Azure CLI-minták az Azure Event Hubs megtalálja a [azure-event-hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/Management/CLI) GitHub-adattárban.

## <a name="azure-powershell-samples"></a>Azure PowerShell-minták
Azure PowerShell-minták az Azure Event Hubs megtalálja a [azure-event-hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/Management/PowerShell) GitHub-adattárban.
 
## <a name="apache-kafka-samples"></a>Az Apache Kafka-minták
Minták az Event Hubs, az Apache Kafka szolgáltatás megtalálja a [azure-event-hubs-az-kafka](https://github.com/Azure/azure-event-hubs-for-kafka) GitHub-adattárban.

## <a name="next-steps"></a>További lépések
További információ az Event Hubs az alábbi cikkeket:

- [Event Hubs – áttekintés](event-hubs-what-is-event-hubs.md)
- [Event Hubs-szolgáltatások](event-hubs-features.md)
- [Event Hubs – gyakori kérdések](event-hubs-faq.md)