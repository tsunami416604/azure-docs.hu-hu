---
title: Azure RTOS-támogatás
description: Ismerje meg az Azure RTOS támogatását a IoT szolgáltatás Azure Security Centerjában.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/15/2020
ms.author: mlottner
ms.openlocfilehash: af4579d25b94fc12f67cfc91d0f6a5af4b35c810
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096695"
---
# <a name="azure-security-center-for-iot-security-solution-for-azure-rtos"></a>Azure Security Center az Azure RTOS IoT biztonsági megoldásához 

A IoT biztonsági modul Azure Security Center átfogó biztonsági megoldást nyújt az Azure RTOS-eszközökhöz. Az Azure RTOS egy beépített biztonsági modullal rendelkezik, amely a valós idejű operációsrendszer-eszközök gyakori fenyegetéseit fedi le. 

![Azure Security Center IoT Azure-RTOS](./media/architecture/azure-rtos-security-monitoring.png)


Az Azure RTOS-támogatással rendelkező IoT biztonsági modulhoz Azure Security Center a következő funkciókat kínálja: 
- Kártevő hálózati tevékenységek észlelése
- Egyéni riasztási alapú, eszköz viselkedési viszonyítási
- Az eszköz biztonsági higiéniájának javítása

### <a name="detection-of-malicious-network-activities"></a>Rosszindulatú hálózati tevékenységek észlelése

Az egyes eszközök bejövő és kimenő hálózati tevékenységeit a rendszer figyeli (támogatott protokollok: TCP, UDP, ICMP IPv4-és IPv6-on). Azure Security Center a IoT megvizsgálja az egyes hálózati tevékenységeket a Microsoft Threat Intelligence-hírcsatornán. A hírcsatorna valós időben frissül, és világszerte több millió egyedi veszélyforrást észlelt. 

### <a name="device-behavior-baselining-based-on-custom-alerts"></a>Az eszköz viselkedésének viszonyítási egyéni riasztások alapján

A viszonyítási lehetővé teszi az eszközök fürtözését a biztonsági csoportokba, és meghatározza az egyes csoportok várható viselkedését. Mivel a IoT-eszközök általában jól meghatározott és korlátozott helyzetekben működnek, könnyen létrehozhat egy alapkonfigurációt, amely meghatározza a várt viselkedést paraméterek használatával. Az alaptervtől való eltérés riasztást indít el. 

### <a name="improve-your-device-security-hygiene"></a>Az eszköz biztonsági higiéniájának javítása

A IoT ajánlott infrastruktúra-Azure Security Centerének kihasználásával olyan ismereteket és betekintést nyerhet a környezettel kapcsolatos problémákról, amelyek hatással vannak az eszközök biztonsági helyzetére, és károsíthatják azokat. A gyenge IoT-eszközök biztonsági helyzete lehetővé teheti a lehetséges támadások sikerességét, ha változatlan marad, mivel a biztonságot mindig a leggyengébb kapcsolat méri a szervezeten belül. 

## <a name="get-started-protecting-azure-rtos-devices"></a>Az Azure RTOS-eszközök védelmének megkezdése

- Az Azure RTOS IoT biztonsági moduljának Azure Security Center ingyenes letöltést biztosít az eszközei számára. A IoT Cloud Service-hez készült Azure Security Center Azure-előfizetések esetében 30 napos próbaidőszakot biztosítunk. Az első lépésekhez töltse le az [Azure RTOS IoT biztonsági moduljának Azure Security Center](https://github.com/azure-rtos/iot-security-module-preview) . 


## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogy Azure Security Center az Azure RTOS-támogatás IoT. A következő cikkekből megtudhatja, hogyan kezdheti el az első lépéseket, és hogyan engedélyezheti IoT Hub biztonsági megoldását:

- [A szolgáltatás előfeltételei](service-prerequisites.md)
- [Bevezetés](getting-started.md)
- [A megoldás konfigurálása](quickstart-configure-your-solution.md)
- [A IoT Hub biztonságának engedélyezése](quickstart-onboard-iot-hub.md)
- [Azure Security Center IoT – gyakori kérdések](resources-frequently-asked-questions.md)
- [Azure Security Center for IoT – biztonsági riasztások](concept-security-alerts.md)
