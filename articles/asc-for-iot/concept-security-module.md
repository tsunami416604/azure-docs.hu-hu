---
title: Biztonsági modul és eszköz ikrek
description: Ismerje meg a biztonsági modul ikrek koncepcióját, és hogy hogyan használják őket az Azure Security Center for IoT-ben.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: a5c25cba-59a4-488b-abbe-c37ff9b151f9
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2019
ms.author: mlottner
ms.openlocfilehash: d598d291612c6e4f58caf77e1b213b2bc3f42820
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311447"
---
# <a name="security-module"></a>Biztonsági modul

Ez a cikk bemutatja, hogyan használja az Azure Security Center for IoT eszközök twins és modulok.

## <a name="device-twins"></a>Eszköz ikrek

Az Azure-ban készült IoT-megoldások esetében az ikereszközök kulcsfontosságú szerepet játszanak mind az eszközkezelésben, mind a folyamatautomatizálásban.

Az Azure Security Center for IoT teljes integrációt kínál a meglévő IoT-eszközfelügyeleti platformmal, lehetővé téve az eszköz biztonsági állapotának kezelését, valamint a meglévő eszközvezérlési képességek kihasználását. Az integráció az IoT Hub ikermechanizmusának használatával érhető el.

További információ az [eszközök ikeriklításának](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins) koncepciójáról az Azure IoT Hubban.

## <a name="security-module-twins"></a>Biztonsági modul ikrek

Az Azure Security Center for IoT egy ikerbiztonsági modult tart fenn a szolgáltatás minden egyes eszközéhez.
A biztonsági modul iker tartalmazza az összes fontos információt az eszköz biztonságát minden egyes eszköz a megoldásban.
Az eszközbiztonsági tulajdonságok egy dedikált ikerbiztonsági modulban maradnak meg a biztonságosabb kommunikáció, valamint a kevesebb erőforrást igénylő frissítések és karbantartás engedélyezése érdekében.

Lásd: [Biztonsági modul létrehozása iker](quickstart-create-security-twin.md) és konfigurálja a biztonsági [ügynökök,](how-to-agent-configuration.md) hogy megtanulják, hogyan lehet létrehozni, testre, és konfigurálja a két. [Tekintse meg a modul twins további](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) megismerése a modul ikrek az IoT Hubban.

## <a name="see-also"></a>Lásd még

- [Az Azure Security Center for IoT – áttekintés](overview.md)
- [Biztonsági ügynökök üzembe helyezése](how-to-deploy-agent.md)
- [Biztonsági ügynök hitelesítési módszerei](concept-security-agent-authentication-methods.md)
