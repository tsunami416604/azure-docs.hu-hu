---
title: Összetevők & előfeltételek
description: Részletes információk az Azure Defender IoT-szolgáltatás előfeltételeinek megkezdéséhez szükséges összes adatról.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.custom: references_regions
ms.openlocfilehash: 19263f8db58c8d20288d3ae74c24efd85667bc33
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90935326"
---
# <a name="azure-defender-for-iot-prerequisites"></a>Az Azure Defender a IoT előfeltételei

Ez a cikk magyarázatot nyújt a Defender for IoT szolgáltatás különböző összetevőiről, mire van szüksége, és ismerteti az alapvető fogalmakat, amelyek segítenek a szolgáltatás megismerésében.

## <a name="minimum-requirements"></a>Minimális követelmények

- Standard szintű IoT Hub
  - Az Azure szerepkör **tulajdonosi** szintű jogosultságai
- [Log Analytics-munkaterület](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)
- Azure Security Center (ajánlott)
  - A Azure Security Center használata javaslat, nem követelmény. Azure Security Center nélkül nem tudja megtekinteni a többi Azure-erőforrást IoT Hubon belül.

## <a name="working-with-defender-for-iot-service"></a>A Defender használata a IoT szolgáltatáshoz

A IoT-bepillantást és jelentéskészítést a Defender az Azure IoT Hub és Azure Security Center használatával érhető el. Ha engedélyezni szeretné a Defender számára az Azure IoT Hub IoT, a **tulajdonosi** szintű jogosultságokkal rendelkező fiókra van szükség. Miután engedélyezte az ASC-t a IoT Hub IoT, a Defender for IoT-megállapítások az Azure IoT Hub **biztonsági** funkciójaként jelennek meg, Azure Security Center pedig  **IoT** .

## <a name="supported-service-regions"></a>Támogatott szolgáltatási régiók

A IoT Defender a következő Azure-régiókban jelenleg támogatott a IoT hubok esetében:

- USA középső régiója
- USA keleti régiója
- USA 2. keleti régiója
- USA nyugati középső régiója
- USA nyugati régiója
- USA 2. nyugati régiója
- USA déli középső régiója
- USA északi középső régiója
- Közép-Kanada
- Kelet-Kanada
- Észak-Európa
- Dél-Brazília
- Közép-Franciaország
- Az Egyesült Királyság nyugati régiója
- Az Egyesült Királyság déli régiója
- Nyugat-Európa
- Észak-Európa
- Nyugat-Japán
- Kelet-Japán
- Délkelet-Ausztrália
- Kelet-Ausztrália
- Kelet-Ázsia
- Délkelet-Ázsia
- Dél-Korea középső régiója
- Dél-Korea déli régiója
- Közép-India
- Dél-India

A IoT Defender az összes európai régióból a Nyugat-európai regionális adatközpontba és a többi régióba irányuló összes forgalmat az USA középső régiójának adatközpontja felé irányítja.

## <a name="wheres-my-iot-hub"></a>Hol van a IoT Hub?

A szolgáltatás elérhetőségének megkezdése előtt ellenőrizze a IoT Hub helyét.

1. Nyissa meg a IoT Hub.
1. Kattintson az **Áttekintés** elemre.
1. Ellenőrizze, hogy a felsorolt hely megfelel-e a [támogatott szolgáltatási régiók](#supported-service-regions)egyikének.

## <a name="supported-platforms-for-agents"></a>Az ügynökök által támogatott platformok

A Defender for IoT-ügynökök az eszközök és platformok egyre bővülő listáját támogatják. Tekintse meg a [támogatott platformok listáját](how-to-deploy-agent.md) , hogy ellenőrizze a meglévő vagy tervezett eszköz könyvtárát.

## <a name="next-steps"></a>Következő lépések

- Az Azure IoT biztonsági [áttekintése](overview.md)
- Útmutató [a szolgáltatás engedélyezéséhez](quickstart-onboard-iot-hub.md)
- Olvassa el a [Defender for IoT – gyakori kérdések](resources-frequently-asked-questions.md)
- A IoT- [riasztások Defender megismerése](concept-security-alerts.md)
