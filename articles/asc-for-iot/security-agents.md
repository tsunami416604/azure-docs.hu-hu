---
title: Biztonsági ügynök – áttekintés
description: Ismerkedés az Azure Security Center ioT-biztonsági szolgáltatási ügynökökhez való megértésével, konfigurálásával, üzembe helyezésével és használatával ioT-eszközökön.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 0d4d21db5185e4564666e526b3edb5ca6d3451e0
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310652"
---
# <a name="get-started-with-azure-security-center-for-iot-device-security-agents"></a>Ismerkedés az Azure Security Center for IoT-eszközbiztonsági ügynökökkel

Az Azure Security Center for IoT biztonsági ügynökök továbbfejlesztett biztonsági lehetőségeket kínálnak, például a távoli kapcsolatok, az aktív alkalmazások, a bejelentkezési események és az operációs rendszer konfigurációs gyakorlati tanácsait. Egyetlen szolgáltatással átveheti az irányítást az eszköz helyszíni veszélyforrások elleni védelme és biztonsági helyzete felett.

A Linux és a Windows biztonsági ügynökök referenciaarchitektúrája c# és c nyelven is rendelkezésre áll.

Az Azure Security Center for IoT biztonsági ügynökök kezeli a nyers eseménygyűjtés az eszköz operációs rendszer, eseményösszesítés a költségek csökkentése érdekében, és a konfiguráció egy eszköz modul iker. A biztonsági üzeneteket az IoT Hubon keresztül küldi el az Azure Security Center for IoT analytics services.

Az alábbi munkafolyamat segítségével telepítheti és tesztelheti az Azure Security Center t IoT biztonsági ügynökökszámára:

1. [Az Azure Security Center engedélyezése az IoT-szolgáltatáshoz az IoT Hubon](quickstart-onboard-iot-hub.md)
1. Ha az IoT Hub nem rendelkezik regisztrált eszközökkel, [regisztráljon egy új eszközt.](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy)
1. [Hozzon létre egy azureiotsecurity biztonsági modult](quickstart-create-security-twin.md) az eszközökszámára.
1. Ha az ügynököt egy Azure-szimulált eszközre szeretné telepíteni egy tényleges eszközre való telepítés helyett, [helyezzen létre egy új Azure virtuális gépet (VM)](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) egy elérhető zónában.
1. [Telepítsen egy Azure Security Center ioT-biztonsági ügynök az](how-to-deploy-linux-cs.md) IoT-eszközön, vagy az új virtuális gép.
1. Kövesse az utasításokat [trigger_events](https://aka.ms/iot-security-github-trigger-events) futtatni egy ártalmatlan szimuláció a támadás.
1. Ellenőrizze az Azure Security Center IoT-riasztások válaszul a szimulált támadás az előző lépésben. A parancsfájl futtatása után öt perccel kezdje meg az ellenőrzést.
1. Fedezze fel [a riasztásokat](concept-security-alerts.md), [javaslatokat](concept-recommendations.md)és részletes merülést az IoT Hub [használatával a Log Analytics használatával.](how-to-security-data-access.md)

## <a name="next-steps"></a>További lépések

- A [megoldás](quickstart-configure-your-solution.md) konfigurálása
- [Biztonsági modulok létrehozása](quickstart-create-security-twin.md)
- [Egyéni riasztások konfigurálása](quickstart-create-custom-alerts.md)
- [Biztonsági ügynök telepítése](how-to-deploy-agent.md)
