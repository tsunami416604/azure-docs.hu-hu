---
title: fájlbefoglalás
description: fájlbefoglalás
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: a0d1de622eefad4ae5e55a427f8b0b1bf4360c0a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84792089"
---
A végpontok állapotának beolvasásához használja a REST API a [végpont állapota](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) lehetőséget. Azt javasoljuk, hogy az útválasztási üzenet késéséhez kapcsolódó [IoT hub metrikák](../articles/iot-hub/iot-hub-metrics.md) használatával azonosítsa és hibakeresési hibákat, amikor a végpont állapota halott vagy sérült, mivel a késés nagyobb lesz, amikor a végpont az egyik államban van.


|Állapotadatok|Leírás|
|---|---|
|kifogástalan|A végpont fogadja az üzeneteket a várt módon.|
|sérült|A végpont nem fogad üzeneteket, és IoT Hub megpróbálja elküldeni az üzeneteket erre a végpontra.|
|ismeretlen|IoT Hub nem próbált üzeneteket kézbesíteni erre a végpontra.|
|leromlott|A végpont fogadja az üzeneteket a vártnál lassabban, vagy sérült állapotból helyreállítja.|
|kézbesítetlen|A IoT Hub már nem kézbesít üzeneteket erre a végpontra. Az üzenetek erre a végpontra való küldésének újrapróbálása sikertelen volt.|
