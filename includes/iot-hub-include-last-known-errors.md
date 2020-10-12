---
title: fájlbefoglalás
description: fájlbefoglalás
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: d03579f704879bd8d012bb0bb326659d1f778dee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "84793330"
---
A végpont [állapotának beolvasása](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) a REST API megadja a végpontok állapotát, valamint a legutóbbi ismert hibát, hogy azonosítsa a végpontok állapotának okát. Az alábbi táblázat a leggyakoribb hibákat sorolja fel.

|Utolsó ismert hiba|Leírás/Ha előfordul|Lehetséges enyhítés|
|-----|-----|-----|
|Átmeneti|Átmeneti hiba történt, és IoT Hub újrapróbálkozik a művelettel.|Figyelje meg az útvonalak [diagnosztikai naplóit](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health#routes).|
|InternalError|Hiba történt egy üzenetnek a végponthoz való kézbesítése közben.|Ez egy belső kivétel, de az útvonalak [diagnosztikai naplóit](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health#routes)is figyelembe kell venni.|
|Nem engedélyezett|IoT Hub nincs jogosultsága arra, hogy üzeneteket küldjön a megadott végpontnak.|Ellenőrizze, hogy a végpont számára a kapcsolódási karakterlánc naprakész-e. Ha módosult, vegyen fel egy frissítést a IoT Hub. Ha a végpont felügyelt identitást használ, ellenőrizze, hogy a IoT Hub rendszerbiztonsági tag rendelkezik-e a szükséges engedélyekkel a célhelyen.|
|Szabályozott|Az üzenetek a végpontba írásakor a IoT Hub szabályozása folyamatban van.|Tekintse át az érintett végpont szabályozási korlátait. Szükség esetén módosítsa a végpont konfigurációit a vertikális felskálázáshoz.|
|Időtúllépés|Művelet időtúllépése.|Próbálja meg újra a műveletet.|
|Nem található|A célként megadott erőforrás nem létezik.|Győződjön meg arról, hogy a célként megadott erőforrás létezik.|
|A tároló nem található|A tároló nem létezik.|Győződjön meg arról, hogy a tároló létezik.|
|Tároló letiltva|A Storage-tároló le van tiltva.|Győződjön meg arról, hogy a tároló engedélyezve van.|
|MaxMessageSizeExceeded|Az üzenet-útválasztáshoz az üzenet mérete 256 kb. az átirányított üzenet mérete túllépte ezt a korlátot.|Annak ellenőrzéséhez, hogy az üzenetek mérete csökkenthető-e kevesebb alkalmazás-tulajdonság vagy kevesebb üzenet-gazdagítás használatával.|
|PartitioningAndDuplicateDetectionNotSupported|Előfordulhat, hogy a Service Bus nem engedélyezte a duplikált észlelést.|Tiltsa le a Service Bus duplikált észlelését, vagy használjon ismétlődő észlelés nélküli entitást.|
|SessionfulEntityNotSupported|Előfordulhat, hogy a Service Bus-munkamenetek nem engedélyezettek.|Tiltsa le a munkamenetet a Service Busről, vagy használjon munkamenetek nélküli entitást.|
|NoMatchingSubscriptionsForMessage|Nincs olyan előfizetés, amely a Service Bus-témakörben nem ír üzenetet.|Hozzon létre egy előfizetést, amelybe a rendszer átirányítja IoT Hub üzeneteket.|
|EndpointExternallyDisabled|A végpont nem aktív állapotban van, ezért IoT Hub küldhet üzeneteket.|Engedélyezze a végpontot, hogy az aktív állapotba kerüljön vissza.|
|DeviceMaximumQueueDepthExceeded|A Service Bus méretkorlát elérte a korlátot.|Vegye fontolóra az üzenetek eltávolítását a cél Event Hubs, hogy lehetővé tegye az új üzenetek betöltését a Event Hubsba.|