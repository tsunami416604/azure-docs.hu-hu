---
title: Az Azure RTOS biztonsági moduljának áttekintése
description: További információ az Azure RTOS-támogatással és-megvalósítással kapcsolatos biztonsági modulról a IoT Defender-szolgáltatás részeként
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
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: 0cfd1e0ce16008c6f7fd128d561ad361bcc53b87
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936256"
---
# <a name="overview-defender-for-iot-security-module-for-azure-rtos-preview"></a>Áttekintés: Defender az Azure RTOS készült IoT biztonsági modulhoz (előzetes verzió)

A RTOS Defender for IoT biztonsági modulja átfogó biztonsági megoldást nyújt az Azure RTOS-eszközökhöz. Az Azure RTOS mostantól a beépített Azure IoT biztonsági modullal rendelkezik, és a valós idejű operációsrendszer-eszközökön biztosít lefedettséget a gyakori fenyegetések és a lehetséges kártékony tevékenységek számára. 

![Defender a IoT Azure RTOS](./media/architecture/azure-rtos-security-monitoring.png)


Az Azure RTOS biztonsági modulja a következő funkciókat kínálja: 
- Kártevő hálózati tevékenységek észlelése
- Egyéni riasztási alapú, eszköz viselkedési viszonyítási
- Az eszköz biztonsági higiéniájának javítása

## <a name="detection-of-malicious-network-activities"></a>Rosszindulatú hálózati tevékenységek észlelése

Az egyes eszközök bejövő és kimenő hálózati tevékenységeit a rendszer figyeli (támogatott protokollok: TCP, UDP, ICMP IPv4-és IPv6-on). A IoT Defender a Microsoft Threat Intelligence-hírcsatornán vizsgálja meg az egyes hálózati tevékenységeket. A hírcsatorna valós időben frissül, és világszerte több millió egyedi veszélyforrást észlelt. 

## <a name="device-behavior-baselining-based-on-custom-alerts"></a>Az eszköz viselkedésének viszonyítási egyéni riasztások alapján

A viszonyítási lehetővé teszi az eszközök fürtözését a biztonsági csoportokba, és meghatározza az egyes csoportok várható viselkedését. Mivel a IoT-eszközök általában jól meghatározott és korlátozott helyzetekben működnek, könnyen létrehozhat egy alapkonfigurációt, amely meghatározza a várt viselkedést paraméterek használatával. Az alaptervtől való eltérés riasztást indít el. 

## <a name="improve-your-device-security-hygiene"></a>Az eszköz biztonsági higiéniájának javítása

A IoT ajánlott infrastruktúra-Defender általi kihasználása révén olyan ismeretekkel és elemzésekkel találkozhat a környezettel kapcsolatos problémákról, amelyek hatással vannak az eszközök biztonsági helyzetére. A gyenge IoT-eszközök biztonsági helyzete lehetővé teheti a lehetséges támadások sikerességét, ha változatlan marad, mivel a biztonságot mindig a leggyengébb kapcsolat méri a szervezeten belül. 

## <a name="get-started-protecting-azure-rtos-devices"></a>Az Azure RTOS-eszközök védelmének megkezdése

Az Azure RTOS biztonsági modulja ingyenes letöltést biztosít az eszközei számára. A IoT Cloud Service-hez készült Defender az Azure-előfizetések esetében 30 napos próbaidőszakot biztosít. Az első lépésekhez töltse le az [Azure RTOS biztonsági modulját](https://github.com/azure-rtos/iot-security-module-preview) . 


## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megismerheti az Azure RTOS szolgáltatás biztonsági modulját. Ha többet szeretne megtudni a biztonsági modulról és az első lépésekről, tekintse meg a következő cikkeket:

- [Az Azure RTOS IoT biztonsági moduljának fogalmak](concept-rtos-security-module.md)
- [Gyors útmutató: Azure RTOS IoT biztonsági modul](quickstart-azure-rtos-security-module.md)
