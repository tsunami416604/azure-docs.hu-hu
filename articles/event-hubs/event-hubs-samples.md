---
title: Azure Event Hubs-minták |} A Microsoft Docs
description: Azure Event Hubs-minták
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2018
ms.author: sethm
ms.openlocfilehash: 8e80587f7b3f6b0cb081fd963848c7aa3ab11e3e
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431256"
---
# <a name="event-hubs-samples"></a>Event Hubs-minták 
Az Event Hubs-minták találhat [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples). Ezek a minták a legfontosabb funkcióit mutatja be [Azure Event Hubs](/azure/event-hubs/). Ez a cikk kategorizálja, és ismerteti a rendelkezésre álló, hivatkozásokkal az egyes minták.

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

## <a name="next-steps"></a>További lépések
További információ az Event Hubs az alábbi cikkeket:

- [Event Hubs – áttekintés](event-hubs-what-is-event-hubs.md)
- [Event Hubs-szolgáltatások](event-hubs-features.md)
- [Event Hubs – gyakori kérdések](event-hubs-faq.md)