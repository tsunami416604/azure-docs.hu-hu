---
title: Azure Event Grid által támogatott metrikák
description: Ez a cikk a Azure Event Grid szolgáltatás által támogatott Azure Monitor mérőszámokat tartalmazza.
ms.topic: conceptual
ms.date: 08/13/2020
ms.openlocfilehash: 06c43b1990efc977cae33ced3f66f02e2de0b9c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88225172"
---
# <a name="metrics-supported-by-azure-event-grid"></a>Azure Event Grid által támogatott metrikák
Ez a cikk a névterek által kategorizált Event Grid mérőszámok listáját tartalmazza. 

## <a name="microsofteventgriddomains"></a>Microsoft. EventGrid/tartományok

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Igen|Kézbesítetlen levelek eseményei|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, kézbesítetlen levelekből származó összes esemény|Témakör, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|
|DeliveryAttemptFailCount|Nem|Sikertelen kézbesítések eseményei|Darabszám|Összesen|Az esemény-előfizetésnek nem sikerült kézbesíteni az összes eseményt|Témakör, EventSubscriptionName, DomainEventSubscriptionName, error, ErrorType|
|DeliverySuccessCount|Igen|Kézbesítési események|Darabszám|Összesen|Az esemény-előfizetésbe küldött összes esemény|Témakör, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|Nem|Cél feldolgozási időtartama|Ezredmásodpercben|Átlag|Cél feldolgozási időtartama (ezredmásodpercben)|Témakör, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Igen|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|Témakör, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|MatchedEventCount|Igen|Egyeztetett események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes esemény|Témakör, EventSubscriptionName, DomainEventSubscriptionName|
|PublishFailCount|Igen|Sikertelen események közzététele|Darabszám|Összesen|A témakörben nem sikerült közzétenni az összes eseményt|Témakör, ErrorType, hiba|
|PublishSuccessCount|Igen|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|Témakör|
|PublishSuccessLatencyInMs|Igen|Sikeres közzétételi késés|Ezredmásodpercben|Összesen|Sikeres közzétételi késés (ezredmásodpercben)|Nincsenek méretek|


## <a name="microsofteventgrideventsubscriptions"></a>Microsoft. EventGrid/eventSubscriptions

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Igen|Kézbesítetlen levelek eseményei|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, kézbesítetlen levelekből származó összes esemény|DeadLetterReason|
|DeliveryAttemptFailCount|Nem|Sikertelen kézbesítések eseményei|Darabszám|Összesen|Az esemény-előfizetésnek nem sikerült kézbesíteni az összes eseményt|Hiba, ErrorType|
|DeliverySuccessCount|Igen|Kézbesítési események|Darabszám|Összesen|Az esemény-előfizetésbe küldött összes esemény|Nincsenek méretek|
|DestinationProcessingDurationInMs|Nem|Cél feldolgozási időtartama|Ezredmásodpercben|Átlag|Cél feldolgozási időtartama (ezredmásodpercben)|Nincsenek méretek|
|DroppedEventCount|Igen|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|DropReason|
|MatchedEventCount|Igen|Egyeztetett események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes esemény|Nincsenek méretek|


## <a name="microsofteventgridextensiontopics"></a>Microsoft. EventGrid/extensionTopics

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|PublishFailCount|Igen|Sikertelen események közzététele|Darabszám|Összesen|A témakörben nem sikerült közzétenni az összes eseményt|ErrorType, hiba|
|PublishSuccessCount|Igen|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|Nincsenek méretek|
|PublishSuccessLatencyInMs|Igen|Sikeres közzétételi késés|Ezredmásodpercben|Összesen|Sikeres közzétételi késés (ezredmásodpercben)|Nincsenek méretek|
|UnmatchedEventCount|Igen|Páratlan események|Darabszám|Összesen|A témakörben szereplő esemény-előfizetéseknek nem megfelelő események összesen|Nincsenek méretek|


## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Igen|Kézbesítetlen levelek eseményei|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, kézbesítetlen levelekből származó összes esemény|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|Nem|Sikertelen kézbesítések eseményei|Darabszám|Összesen|Az esemény-előfizetésnek nem sikerült kézbesíteni az összes eseményt|Hiba, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Igen|Kézbesítési események|Darabszám|Összesen|Az esemény-előfizetésbe küldött összes esemény|EventSubscriptionName|
|DestinationProcessingDurationInMs|Nem|Cél feldolgozási időtartama|Ezredmásodpercben|Átlag|Cél feldolgozási időtartama (ezredmásodpercben)|EventSubscriptionName|
|DroppedEventCount|Igen|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|DropReason, EventSubscriptionName|
|MatchedEventCount|Igen|Egyeztetett események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes esemény|EventSubscriptionName|
|PublishFailCount|Igen|Sikertelen események közzététele|Darabszám|Összesen|A témakörben nem sikerült közzétenni az összes eseményt|ErrorType, hiba|
|PublishSuccessCount|Igen|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|Nincsenek méretek|
|PublishSuccessLatencyInMs|Igen|Sikeres közzétételi késés|Ezredmásodpercben|Összesen|Sikeres közzétételi késés (ezredmásodpercben)|Nincsenek méretek|
|UnmatchedEventCount|Igen|Páratlan események|Darabszám|Összesen|A témakörben szereplő esemény-előfizetéseknek nem megfelelő események összesen|Nincsenek méretek|


## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/témakörök

|Metrika|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Leírás|Dimenziók|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Igen|Kézbesítetlen levelek eseményei|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, kézbesítetlen levelekből származó összes esemény|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|Nem|Sikertelen kézbesítések eseményei|Darabszám|Összesen|Az esemény-előfizetésnek nem sikerült kézbesíteni az összes eseményt|Hiba, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Igen|Kézbesítési események|Darabszám|Összesen|Az esemény-előfizetésbe küldött összes esemény|EventSubscriptionName|
|DestinationProcessingDurationInMs|Nem|Cél feldolgozási időtartama|Ezredmásodpercben|Átlag|Cél feldolgozási időtartama (ezredmásodpercben)|EventSubscriptionName|
|DroppedEventCount|Igen|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|DropReason, EventSubscriptionName|
|MatchedEventCount|Igen|Egyeztetett események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes esemény|EventSubscriptionName|
|PublishFailCount|Igen|Sikertelen események közzététele|Darabszám|Összesen|A témakörben nem sikerült közzétenni az összes eseményt|ErrorType, hiba|
|PublishSuccessCount|Igen|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|Nincsenek méretek|
|PublishSuccessLatencyInMs|Igen|Sikeres közzétételi késés|Ezredmásodpercben|Összesen|Sikeres közzétételi késés (ezredmásodpercben)|Nincsenek méretek|
|UnmatchedEventCount|Igen|Páratlan események|Darabszám|Összesen|A témakörben szereplő esemény-előfizetéseknek nem megfelelő események összesen|Nincsenek méretek|

## <a name="next-steps"></a>Következő lépések
Tekintse meg a következő cikket: [diagnosztikai naplók](diagnostic-logs.md)
