---
title: Összetevők & előfeltételek
description: A IoT szolgáltatás előfeltételeinek megkezdéséhez szükséges Azure Security Center részletei.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 790cbcb7-1340-4cc1-9509-7b262e7c3181
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2019
ms.author: mlottner
ms.custom: references_regions
ms.openlocfilehash: 19e4ea37aa10c90d15a2b7dcdf962c131c8e473d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84193215"
---
# <a name="azure-security-center-for-iot-prerequisites"></a>A IoT előfeltételei Azure Security Center

Ez a cikk a IoT szolgáltatás Azure Security Center különböző összetevőit ismerteti, amelyeket el kell kezdenie, és ismerteti az alapvető fogalmakat, amelyek segítenek a szolgáltatás megismerésében.

## <a name="minimum-requirements"></a>Minimális követelmények

- Standard szintű IoT Hub
  - RBAC szerepkör- **tulajdonosi** szintű jogosultságok
- [Log Analytics munkaterület](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)
- Azure Security Center (ajánlott)
  - A Azure Security Center használata javaslat, nem követelmény. Azure Security Center nélkül nem tudja megtekinteni a többi Azure-erőforrást IoT Hubon belül.

## <a name="working-with-azure-security-center-for-iot-service"></a>Azure Security Center használata a IoT szolgáltatáshoz

Az IoT-és jelentéskészítési Azure Security Center az Azure IoT Hub és a Azure Security Center használatával érhetők el. Ha engedélyezni szeretné az Azure-IoT Hub IoT Azure Security Center, a **tulajdonosi** szintű jogosultságokkal rendelkező fiókra van szükség. Miután engedélyezte az ASC szolgáltatást a IoT Hub IoT, a IoT-megállapítások Azure Security Center az Azure IoT Hub és a Azure Security Center **IoT** **biztonsági** funkciójaként jelenik meg.

## <a name="supported-service-regions"></a>Támogatott szolgáltatási régiók

A IoT Azure Security Center jelenleg a következő Azure-régiókban támogatott az IoT-hubok esetében:

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

A IoT Azure Security Center az összes európai régióból a Nyugat-európai regionális adatközpontba és a többi régióba irányuló összes forgalmat az USA középső régiójában lévő adatközpontba irányítja.

## <a name="wheres-my-iot-hub"></a>Hol van a IoT Hub?

A szolgáltatás elérhetőségének megkezdése előtt ellenőrizze a IoT Hub helyét.

1. Nyissa meg a IoT Hub.
1. Kattintson az **Áttekintés** elemre.
1. Ellenőrizze, hogy a felsorolt hely megfelel-e a [támogatott szolgáltatási régiók](#supported-service-regions)egyikének.

## <a name="supported-platforms-for-agents"></a>Az ügynökök által támogatott platformok

A IoT-ügynökök Azure Security Centera támogatja az eszközök és platformok egyre bővülő listáját. Tekintse meg a [támogatott platformok listáját](how-to-deploy-agent.md) , hogy ellenőrizze a meglévő vagy tervezett eszköz könyvtárát.

## <a name="next-steps"></a>További lépések

- Az Azure IoT biztonsági [áttekintése](overview.md)
- Útmutató [a szolgáltatás engedélyezéséhez](quickstart-onboard-iot-hub.md)
- Olvassa el a [IoT kapcsolatos gyakori kérdéseket Azure Security Center](resources-frequently-asked-questions.md)
- A IoT- [riasztások Azure Security Center megismerése](concept-security-alerts.md)
