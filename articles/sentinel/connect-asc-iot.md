---
title: Az Azure Defender és a IoT összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztatható az Azure Defender (korábban Azure Security Center) a IoT-hez az Azure Sentinel szolgáltatáshoz.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2020
ms.author: yelevin
ms.openlocfilehash: 822d0c742bbd54b5bab0c69e82652743584a0696
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89659619"
---
# <a name="connect-your-data-from-azure-defender-formerly-azure-security-center-for-iot-to-azure-sentinel"></a>Az Azure Defender (korábban Azure Security Center) adatainak összekötése az Azure Sentinel IoT 


> [!IMPORTANT]
> Az Azure Defender for IoT adatösszekötő jelenleg nyilvános előzetes verzióban érhető el. Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Használja az Azure Defender for IoT-összekötőt az Azure Defender IoT-események Azure Sentinelbe való továbbításához. 

## <a name="prerequisites"></a>Előfeltételek

- **Olvasási** és **írási** engedélyek azon a munkaterületen, amelyen az Azure Sentinel üzembe lett helyezve
- A **IoT tartozó Azure Defender számára** engedélyezni kell a megfelelő IoT hub (ka) **t**
- **Olvasási** és **írási** engedélyek a csatlakozni kívánó **Azure-IoT hub**
- **Olvasási** és **írási** engedélyek az **Azure IoT hub erőforráscsoporthoz**

## <a name="connect-to-azure-defender-for-iot"></a>Kapcsolódás az Azure Defenderhez a IoT-hez

1. Az Azure Sentinelben válassza az **adatösszekötők** lehetőséget, majd válassza az **Azure Defender for IoT** (továbbra is meghívható Azure Security Center IoT) a katalógusból.
1. A jobb alsó ablaktáblán kattintson az **összekötő lap megnyitása**lehetőségre. 
1. Kattintson a **Kapcsolódás**lehetőségre minden olyan IoT hub-előfizetés mellett, amelynek a riasztásait és az eszközök riasztásait az Azure Sentinel szolgáltatásba kívánja továbbítani. 
    - Ha az Azure Defender for IoT nincs engedélyezve ezen a központban **, egy figyelmeztető üzenet jelenik meg** . A szolgáltatás elindításához kattintson az **enable (Engedélyezés** ) hivatkozásra. 
1. Eldöntheti, hogy szeretné-e, ha az Azure Defender IoT-riasztásai automatikusan előállítanak incidenseket az Azure Sentinelben. Az **incidensek létrehozása**területen válassza az **Engedélyezés** lehetőséget az alapértelmezett analitikus szabály engedélyezéséhez, hogy automatikusan hozzon létre incidenseket a csatlakoztatott biztonsági szolgáltatásban létrehozott riasztásokból. Ez a szabály az **Analytics**  >  **aktív** szabályok területén módosítható vagy módosítható.

> [!NOTE]
> Némi időbe telik, amíg a központi lista frissül a kapcsolatok módosítása után. 

## <a name="log-analytics-alert-display"></a>Log Analytics riasztás megjelenítése

A Log Analytics kapcsolódó sémájának használata az Azure Defender IoT-riasztások megjelenítéséhez:

1. Nyissa meg a **naplók**  >  **SecurityInsights**  >  **SecurityAlert**, vagy keressen rá a **SecurityAlert**kifejezésre. 
2. Szűrő: csak az Azure Defender a IoT által generált riasztásait jeleníti meg a következő kql szűrő használatával:

```kusto
SecurityAlert | where ProductName == "Azure Defender for IoT"
``` 

### <a name="service-notes"></a>Szolgáltatási megjegyzések

IoT Hub csatlakoztatása után a hub-adatközpont körülbelül 15 perccel később elérhető az Azure Sentinelben.


## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan csatlakoztatható az Azure Defender a IoT-hez az Azure Sentinel szolgáltatáshoz. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.
