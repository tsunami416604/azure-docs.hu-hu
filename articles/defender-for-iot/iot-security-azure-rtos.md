---
title: Az Azure RTOS biztonsági moduljának áttekintése
description: További információ az Azure RTOS-támogatással és-megvalósítással kapcsolatos biztonsági modulról a IoT készült Azure Defender részeként.
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
ms.openlocfilehash: 9950f3727aac365205e979d9590edacebd32f1fc
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/31/2020
ms.locfileid: "97832743"
---
# <a name="overview-defender-for-iot-security-module-for-azure-rtos-preview"></a>Áttekintés: Defender az Azure RTOS készült IoT biztonsági modulhoz (előzetes verzió)

Az Azure Defender for IoT biztonsági modul átfogó biztonsági megoldást nyújt az Azure RTOS-t használó eszközökhöz. A valós idejű operációs rendszer (RTOS) eszközein a gyakori fenyegetések és a lehetséges kártékony tevékenységek lefedettségét biztosítja. Az Azure RTOS mostantól a beépített Azure IoT biztonsági modullal rendelkezik.

:::image type="content" source="./media/architecture/azure-rtos-security-monitoring.png" alt-text="A Defender vizualizációja az Azure RTOS IoT.":::


Az Azure RTOS biztonsági modulja a következő funkciókat kínálja:

- Kártevő hálózati tevékenységek észlelése
- Egyéni riasztáson alapuló eszköz viselkedésének viszonyítási
- Továbbfejlesztett eszköz biztonsági higiénia

## <a name="detect-malicious-network-activities"></a>Kártékony hálózati tevékenységek észlelése

A rendszer figyeli az egyes eszközök bejövő és kimenő hálózati tevékenységeit. A támogatott protokollok a következők: TCP, UDP és ICMP az IPv4-ben és az IPv6-on. A IoT Defender a Microsoft Threat Intelligence-hírcsatornán vizsgálja meg az egyes hálózati tevékenységeket. A hírcsatorna valós időben frissül, és világszerte több millió egyedi veszélyforrást észlelt.

## <a name="device-behavior-baselining-based-on-custom-alerts"></a>Az eszköz viselkedésének viszonyítási egyéni riasztások alapján

A viszonyítási lehetővé teszi az eszközök fürtözését a biztonsági csoportokba, és meghatározza az egyes csoportok várható viselkedését. Mivel a IoT-eszközök általában jól meghatározott és korlátozott helyzetekben működnek, egyszerűen létrehozhat egy alapkonfigurációt, amely meghatározza a várt viselkedést paraméterek használatával. Az alaptervtől való eltérés riasztást indít el.

## <a name="improve-your-device-security-hygiene"></a>Az eszköz biztonsági higiéniájának javítása

A IoT által biztosított ajánlott infrastruktúra-Defender segítségével ismereteket és elemzéseket kaphat a környezetében felmerülő problémákról, amelyek hatással vannak az eszközök biztonsági helyzetére, és károsíthatják azokat. A gyenge IoT biztonsági helyzetek lehetővé tehetik a lehetséges támadások sikerességét, ha változatlan marad. A biztonságot mindig a leggyengébb kapcsolat méri a szervezeten belül.

## <a name="get-started-protecting-azure-rtos-devices"></a>Az Azure RTOS-eszközök védelmének megkezdése

Az Azure RTOS biztonsági modulja ingyenes letöltést biztosít az eszközei számára. A IoT Cloud Service Defender egy 30 napos próbaidőszakot biztosít az Azure-előfizetések esetében. Első lépésként töltse le az [Azure RTOS biztonsági modulját](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/defender-for-iot/iot-security-azure-rtos.md). 

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta az Azure RTOS biztonsági modulját. Ha többet szeretne megtudni a biztonsági modulról és az első lépésekről, tekintse meg a következő cikkeket:

- [Az Azure RTOS IoT biztonsági moduljának fogalmak](concept-rtos-security-module.md)
- [Gyors útmutató: Azure RTOS IoT biztonsági modul](quickstart-azure-rtos-security-module.md)
