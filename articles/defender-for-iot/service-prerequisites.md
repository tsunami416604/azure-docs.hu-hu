---
title: Összetevők & előfeltételek
description: Részletes információk az Azure Defender IoT-szolgáltatás előfeltételeinek megkezdéséhez szükséges összes adatról.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2020
ms.author: rkarlin
ms.custom: references_regions
ms.openlocfilehash: b0913dc48f807c26396a38e31d293877b4561b7d
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2020
ms.locfileid: "92089179"
---
# <a name="azure-defender-for-iot-prerequisites"></a>Az Azure Defender a IoT előfeltételei

Ez a cikk magyarázatot nyújt a Defender for IoT szolgáltatás különböző összetevőiről, mire van szüksége, és ismerteti az alapvető fogalmakat, amelyek segítenek a szolgáltatás megismerésében.

## <a name="minimum-requirements"></a>Minimális követelmények

- IoT és OT-eszközök ügynök nélküli figyelése (CyberX-technológia alapján)
    - A forgalom figyelését támogató hálózati kapcsolók a SPAN porton keresztül
    - Hardvereszközök a NTA-érzékelőhöz – további információ: [Certified Hardware](https://aka.ms/AzureDefenderforIoTBareMetalAppliance)
    - Azure-előfizetési **közreműködő** szerepkör (csak a véglegesített eszközök definiálásához szükséges)
    - IoT Hub (ingyenes vagy standard szintű) **közreműködő** szerepkör (felhőalapú csatlakoztatott felügyelethez)
- Az Azure IoT Hub által felügyelt felügyelt IoT-eszközök biztonsága
    - IoT Hub (standard szintű) **közreműködő** szerepkör
    - IoT Hub: engedélyezni kell **Az Azure Defender for IoT** funkciót.
    - Az eszközök szintjének biztonsági moduljának támogatása  
        - A Defender for IoT-ügynökök támogatják az eszközök és platformok egyre növekvő listáját, lásd a [támogatott platformok listáját](how-to-deploy-agent.md) .


## <a name="supported-service-regions"></a>Támogatott szolgáltatási régiók

További információ: [IoT hub támogatott régiók](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub) . 

A IoT Defender az összes európai régióból a Nyugat-európai regionális adatközpontba és a többi régióba irányuló összes forgalmat az USA középső régiójának adatközpontja felé irányítja.

## <a name="next-steps"></a>Következő lépések

- Az Azure IoT biztonsági [áttekintése](overview.md)
- Útmutató [a szolgáltatás engedélyezéséhez](quickstart-onboard-iot-hub.md)
- Olvassa el a [Defender for IoT – gyakori kérdések](resources-frequently-asked-questions.md)
- A IoT- [riasztások Defender megismerése](concept-security-alerts.md)
