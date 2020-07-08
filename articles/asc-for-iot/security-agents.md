---
title: A biztonsági ügynök áttekintése
description: Ismerkedjen meg a IoT-eszközökön a IoT biztonsági szolgáltatási ügynökök megismerésével, konfigurálásával, üzembe helyezésével és Azure Security Center használatával.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "81310652"
---
# <a name="get-started-with-azure-security-center-for-iot-device-security-agents"></a>Ismerkedés a Azure Security Center IoT-eszközök biztonsági ügynökökkel való használatába

A IoT biztonsági ügynökök Azure Security Center nagyobb biztonsági képességeket kínálnak, mint például a távoli kapcsolatok figyelése, az aktív alkalmazások, a bejelentkezési események és az operációs rendszer konfigurálásának ajánlott eljárásai. A fenyegetések elleni védelem és a biztonsági helyzet szabályozása egyetlen szolgáltatással.

A Linux és a Windows biztonsági ügynökökre vonatkozó hivatkozási architektúra a C# és a C nyelvben is elérhető.

A IoT biztonsági ügynökök Azure Security Center a nyers események gyűjtését kezeli az eszköz operációs rendszeréről, az események összesítését a költségeket és a konfigurációt egy külön eszközosztály használatával. A biztonsági üzeneteket a rendszer a IoT Hubon keresztül továbbítja a IoT Analytics-szolgáltatások Azure Security Center.

A következő munkafolyamattal telepítheti és tesztelheti Azure Security Center a IoT biztonsági ügynökökhöz:

1. [A IoT szolgáltatás Azure Security Center engedélyezése a IoT Hub](quickstart-onboard-iot-hub.md)
1. Ha a IoT Hub nem rendelkezik regisztrált eszközökkel, [regisztráljon egy új eszközt](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy).
1. [Hozzon létre egy azureiotsecurity biztonsági modult](quickstart-create-security-twin.md) az eszközökhöz.
1. Ha az ügynököt egy Azure-beli szimulált eszközre szeretné telepíteni ahelyett, hogy tényleges eszközre telepítené, egy [új Azure-beli virtuális gépet (VM)](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) helyezzen üzembe egy elérhető zónában.
1. [Helyezzen üzembe egy Azure Security Center a IoT biztonsági ügynökhöz](how-to-deploy-linux-cs.md) a IoT-eszközön vagy az új virtuális gépen.
1. A támadás ártalmatlan szimulációjának futtatásához kövesse az [trigger_events](https://aka.ms/iot-security-github-trigger-events) utasításait.
1. Ellenőrizze, Azure Security Center-e a IoT-riasztások az előző lépésben szimulált támadásra válaszolva. A szkript futtatása után indítsa el az ellenőrzést öt perccel.
1. A IoT Hub használatával a [riasztások](concept-security-alerts.md), a [javaslatok](concept-recommendations.md)és a részletes [merülések](how-to-security-data-access.md) megismerése log Analytics.

## <a name="next-steps"></a>További lépések

- A [megoldás](quickstart-configure-your-solution.md) konfigurálása
- [Biztonsági modulok létrehozása](quickstart-create-security-twin.md)
- [Egyéni riasztások](quickstart-create-custom-alerts.md) konfigurálása
- [Biztonsági ügynök üzembe helyezése](how-to-deploy-agent.md)
