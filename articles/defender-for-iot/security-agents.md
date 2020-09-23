---
title: A biztonsági ügynök áttekintése
description: Ismerkedjen meg az Azure Defender IoT biztonsági szolgáltatási ügynökökkel való megismerésével, konfigurálásával, üzembe helyezésével és használatával a IoT-eszközökön.
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
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 888fdbb2352c4ac972eac8b24c29d22ce973e04d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939744"
---
# <a name="get-started-with-azure-defender-for-iot-device-security-agents"></a>Ismerkedés az Azure Defender IoT-eszközök biztonsági ügynökökkel való használatába

A IoT biztonsági ügynököknek szóló Defender fokozott biztonsági képességeket kínál, például a távoli kapcsolatok figyelését, az aktív alkalmazásokat, a bejelentkezési eseményeket és az operációs rendszer konfigurálásának ajánlott eljárásait. A fenyegetések elleni védelem és a biztonsági helyzet szabályozása egyetlen szolgáltatással.

A Linux és a Windows biztonsági ügynökökre vonatkozó hivatkozási architektúra a C# és a C nyelvben is elérhető.

A Defender for IoT biztonsági ügynökök a nyers események gyűjtését kezelik az eszköz operációs rendszeréről, az események összesítését a költségeket csökkentik, és a konfigurációt egy külön eszközosztály segítségével. A biztonsági üzeneteket a IoT Hub a Defender for IoT Analytics Services szolgáltatásban küldik el.

A következő munkafolyamat használatával telepítheti és tesztelheti a Defender for IoT biztonsági ügynökeit:

1. [A Defender engedélyezése a IoT szolgáltatás számára a IoT Hub](quickstart-onboard-iot-hub.md)
1. Ha a IoT Hub nem rendelkezik regisztrált eszközökkel, [regisztráljon egy új eszközt](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy).
1. [Hozzon létre egy azureiotsecurity biztonsági modult](quickstart-create-security-twin.md) az eszközökhöz.
1. Ha az ügynököt egy Azure-beli szimulált eszközre szeretné telepíteni ahelyett, hogy tényleges eszközre telepítené, egy [új Azure-beli virtuális gépet (VM)](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) helyezzen üzembe egy elérhető zónában.
1. [Helyezzen üzembe egy Defender for IoT biztonsági ügynököt](how-to-deploy-linux-cs.md) a IoT-eszközön vagy az új virtuális gépen.
1. A támadás ártalmatlan szimulációjának futtatásához kövesse az [trigger_events](https://aka.ms/iot-security-github-trigger-events) utasításait.
1. Ellenőrizze, hogy a Defender IoT-riasztásokat adott-e meg az előző lépésben szimulált támadásra válaszul. A szkript futtatása után indítsa el az ellenőrzést öt perccel.
1. A IoT Hub használatával a [riasztások](concept-security-alerts.md), a [javaslatok](concept-recommendations.md)és a részletes [merülések](how-to-security-data-access.md) megismerése log Analytics.

## <a name="next-steps"></a>Következő lépések

- A [megoldás](quickstart-configure-your-solution.md) konfigurálása
- [Biztonsági modulok létrehozása](quickstart-create-security-twin.md)
- [Egyéni riasztások](quickstart-create-custom-alerts.md) konfigurálása
- [Biztonsági ügynök üzembe helyezése](how-to-deploy-agent.md)
