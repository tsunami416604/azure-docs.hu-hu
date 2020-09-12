---
title: Az Azure RTOS biztonsági moduljának áttekintése
description: További információ az Azure RTOS-támogatással és-megvalósítással kapcsolatos biztonsági modulról a IoT szolgáltatás Azure Security Center részeként.
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
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: bcf98f503b0f0c8bf76435d9884297d5f53d65e6
ms.sourcegitcommit: 7f62a228b1eeab399d5a300ddb5305f09b80ee14
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2020
ms.locfileid: "89514475"
---
# <a name="overview-security-module-for-azure-rtos-preview"></a>Áttekintés: biztonsági modul az Azure RTOS (előzetes verzió)

A IoT RTOS biztonsági moduljának Azure Security Center átfogó biztonsági megoldást nyújt az Azure RTOS-eszközökhöz. Az Azure RTOS mostantól a beépített Azure IoT biztonsági modullal rendelkezik, és a valós idejű operációsrendszer-eszközökön biztosít lefedettséget a gyakori fenyegetések és a lehetséges kártékony tevékenységek számára. 

![Azure Security Center IoT Azure-RTOS](./media/architecture/azure-rtos-security-monitoring.png)


Az Azure RTOS biztonsági modulja a következő funkciókat kínálja: 
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

Az Azure RTOS biztonsági modulja ingyenes letöltést biztosít az eszközei számára. A IoT Cloud Service-hez készült Azure Security Center Azure-előfizetések esetében 30 napos próbaidőszakot biztosítunk. Az első lépésekhez töltse le az [Azure RTOS biztonsági modulját](https://github.com/azure-rtos/iot-security-module-preview) . 


## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megismerheti az Azure RTOS szolgáltatás biztonsági modulját. Ha többet szeretne megtudni a biztonsági modulról és az első lépésekről, tekintse meg a következő cikkeket:

- [Az Azure RTOS IoT biztonsági moduljának fogalmak](concept-rtos-security-module.md)
- [Gyors útmutató: Azure RTOS IoT biztonsági modul](quickstart-azure-rtos-security-module.md)


