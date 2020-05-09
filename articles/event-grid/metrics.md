---
title: Azure Event Grid által támogatott metrikák
description: Ez a cikk a Azure Event Grid szolgáltatás által támogatott Azure Monitor mérőszámokat tartalmazza.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: spelluru
ms.openlocfilehash: 643df2f4cc6347e0fd56f9124b68f1888ab85e26
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82630134"
---
# <a name="metrics-supported-by-azure-event-grid"></a>Azure Event Grid által támogatott metrikák
Ez a cikk a névterek által kategorizált Event Grid mérőszámok listáját tartalmazza. 

## <a name="microsofteventgriddomains"></a>Microsoft. EventGrid/tartományok

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PublishSuccessCount|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|Témakör|
|PublishFailCount|Sikertelen események közzététele|Darabszám|Összesen|A témakörben nem sikerült közzétenni az összes eseményt|Témakör, ErrorType, hiba|
|PublishSuccessLatencyInMs|Sikeres közzétételi késés|Ezredmásodpercben|Összesen|Sikeres közzétételi késés (ezredmásodpercben)|Nincs|
|MatchedEventCount|Egyeztetett események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes esemény|Témakör, EventSubscriptionName, DomainEventSubscriptionName|
|DeliveryAttemptFailCount|Sikertelen kézbesítések eseményei|Darabszám|Összesen|Az esemény-előfizetésnek nem sikerült kézbesíteni az összes eseményt|Témakör, EventSubscriptionName, DomainEventSubscriptionName, error, ErrorType|
|DeliverySuccessCount|Kézbesítési események|Darabszám|Összesen|Az esemény-előfizetésbe küldött összes esemény|Témakör, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|Cél feldolgozási időtartama|Ezredmásodpercben|Átlag|Cél feldolgozási időtartama (ezredmásodpercben)|Témakör, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|Témakör, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|DeadLetteredCount|Kézbesítetlen levelek eseményei|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, kézbesítetlen levelekből származó összes esemény|Témakör, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|

## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/témakörök

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PublishSuccessCount|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|Nincs|
|PublishFailCount|Sikertelen események közzététele|Darabszám|Összesen|A témakörben nem sikerült közzétenni az összes eseményt|ErrorType, hiba|
|UnmatchedEventCount|Páratlan események|Darabszám|Összesen|A témakörben szereplő esemény-előfizetéseknek nem megfelelő események összesen|Nincs|
|PublishSuccessLatencyInMs|Sikeres közzétételi késés|Ezredmásodpercben|Összesen|Sikeres közzétételi késés (ezredmásodpercben)|Nincs|
|MatchedEventCount|Egyeztetett események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes esemény|EventSubscriptionName|
|DeliveryAttemptFailCount|Sikertelen kézbesítések eseményei|Darabszám|Összesen|Az esemény-előfizetésnek nem sikerült kézbesíteni az összes eseményt|Hiba, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Kézbesítési események|Darabszám|Összesen|Az esemény-előfizetésbe küldött összes esemény|EventSubscriptionName|
|DestinationProcessingDurationInMs|Cél feldolgozási időtartama|Ezredmásodpercben|Átlag|Cél feldolgozási időtartama (ezredmásodpercben)|EventSubscriptionName|
|DroppedEventCount|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|DropReason, EventSubscriptionName|
|DeadLetteredCount|Kézbesítetlen levelek eseményei|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, kézbesítetlen levelekből származó összes esemény|DeadLetterReason, EventSubscriptionName|

## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PublishSuccessCount|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|Nincs|
|PublishFailCount|Sikertelen események közzététele|Darabszám|Összesen|A témakörben nem sikerült közzétenni az összes eseményt|ErrorType, hiba|
|UnmatchedEventCount|Páratlan események|Darabszám|Összesen|A témakörben szereplő esemény-előfizetéseknek nem megfelelő események összesen|Nincs|
|PublishSuccessLatencyInMs|Sikeres közzétételi késés|Ezredmásodpercben|Összesen|Sikeres közzétételi késés (ezredmásodpercben)|Nincs|
|MatchedEventCount|Egyeztetett események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes esemény|EventSubscriptionName|
|DeliveryAttemptFailCount|Sikertelen kézbesítések eseményei|Darabszám|Összesen|Az esemény-előfizetésnek nem sikerült kézbesíteni az összes eseményt|Hiba, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Kézbesítési események|Darabszám|Összesen|Az esemény-előfizetésbe küldött összes esemény|EventSubscriptionName|
|DestinationProcessingDurationInMs|Cél feldolgozási időtartama|Ezredmásodpercben|Átlag|Cél feldolgozási időtartama (ezredmásodpercben)|EventSubscriptionName|
|DroppedEventCount|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|DropReason, EventSubscriptionName|
|DeadLetteredCount|Kézbesítetlen levelek eseményei|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, kézbesítetlen levelekből származó összes esemény|DeadLetterReason, EventSubscriptionName|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft. EventGrid/eventSubscriptions

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|MatchedEventCount|Egyeztetett események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes esemény|Nincs|
|DeliveryAttemptFailCount|Sikertelen kézbesítések eseményei|Darabszám|Összesen|Az esemény-előfizetésnek nem sikerült kézbesíteni az összes eseményt|Hiba, ErrorType|
|DeliverySuccessCount|Kézbesítési események|Darabszám|Összesen|Az esemény-előfizetésbe küldött összes esemény|Nincs|
|DestinationProcessingDurationInMs|Cél feldolgozási időtartama|Ezredmásodpercben|Átlag|Cél feldolgozási időtartama (ezredmásodpercben)|Nincs|
|DroppedEventCount|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|DropReason|
|DeadLetteredCount|Kézbesítetlen levelek eseményei|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, kézbesítetlen levelekből származó összes esemény|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft. EventGrid/extensionTopics

|Metrika|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|
|PublishSuccessCount|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|Nincs|
|PublishFailCount|Sikertelen események közzététele|Darabszám|Összesen|A témakörben nem sikerült közzétenni az összes eseményt|ErrorType, hiba|
|UnmatchedEventCount|Páratlan események|Darabszám|Összesen|A témakörben szereplő esemény-előfizetéseknek nem megfelelő események összesen|Nincs|
|PublishSuccessLatencyInMs|Sikeres közzétételi késés|Ezredmásodpercben|Összesen|Sikeres közzétételi késés (ezredmásodpercben)|Nincs|

## <a name="next-steps"></a>További lépések
Tekintse meg a következő cikket: [diagnosztikai naplók](diagnostic-logs.md)
