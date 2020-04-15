---
title: Összetevők & előfeltételek
description: Az Azure Security Center ioT-szolgáltatás előfeltételeinek megkezdéséhez szükséges részletei.
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
ms.openlocfilehash: fb5c42ad490ed04f14ff150093a44b552434ed9e
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310581"
---
# <a name="azure-security-center-for-iot-prerequisites"></a>Az Azure Security Center az IoT előfeltételeihez

Ez a cikk ismerteti az Azure Security Center for IoT szolgáltatás különböző összetevőit, hogy mit kell kezdenie, és ismerteti a szolgáltatás megértéséhez szükséges alapvető fogalmakat.

## <a name="minimum-requirements"></a>Minimális követelmények

- IoT Hub standard szint
  - RBAC szerepkör **tulajdonosi** szintű jogosultságai
- [Log Analytics-munkaterület](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)
- Azure Security Center (ajánlott)
  - Az Azure Security Center használata egy javaslat, és nem követelmény. Az Azure Security Center nélkül nem tekintheti meg a többi Azure-erőforrást az IoT Hubon belül.

## <a name="working-with-azure-security-center-for-iot-service"></a>Az Azure Security Center for IoT szolgáltatás sal való együttműködése

Az Azure Security Center az IoT-elemzésekhez és a jelentésekhez az Azure IoT Hub és az Azure Security Center használatával érhető el. Az Azure Security Center for IoT engedélyezéséhez az Azure IoT Hubon **tulajdonosi** szintű jogosultságokkal rendelkező fiókszükséges. Miután engedélyezte az ASC-t az IoT-hez az IoT Hubban, az Azure Security Center for IoT insights az Azure IoT Hub **biztonsági szolgáltatásaként,** az Azure Security Centerben **pedig IoT-ként** jelenik meg.

## <a name="supported-service-regions"></a>Támogatott szolgáltatási régiók

Az Azure Security Center for IoT jelenleg támogatott IoT Hubs a következő Azure-régiókban:

- USA középső régiója
- USA keleti régiója
- USA 2. keleti régiója
- USA nyugati középső régiója
- USA nyugati régiója
- USA 2. nyugati régiója
- Usa középső déli
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

Az Azure Security Center for IoT az összes európai régióból a nyugat-európai regionális adatközpontba, a többi régiópedig az USA középső régiós adatközpontba irányítja az összes forgalmat.

## <a name="wheres-my-iot-hub"></a>Hol van az IoT Hubom?

Ellenőrizze az IoT Hub helyét a szolgáltatás elérhetőségének ellenőrzéséhez, mielőtt elkezdené.

1. Nyissa meg az IoT Hubot.
1. Kattintson az **Áttekintés** elemre.
1. Ellenőrizze, hogy a felsorolt hely megfelel-e a [támogatott szolgáltatási területek egyikének.](#supported-service-regions)

## <a name="supported-platforms-for-agents"></a>Támogatott platformok ügynökök számára

Az Azure Security Center for IoT-ügynökök támogatja az eszközök és platformok egyre növekvő listáját. Tekintse meg a [támogatott platformlistát](how-to-deploy-agent.md) a meglévő vagy tervezett eszköztár ellenőrzéséhez.

## <a name="next-steps"></a>További lépések

- Olvassa el az Azure IoT biztonsági [áttekintését](overview.md)
- További információ [a szolgáltatás engedélyezéséről](quickstart-onboard-iot-hub.md)
- Olvassa el az [Azure Security Center for IoT – gyakori kérdéseket](resources-frequently-asked-questions.md)
- Ismerje meg, hogyan [értheti meg az Azure Security Center for IoT-riasztásokat](concept-security-alerts.md)
