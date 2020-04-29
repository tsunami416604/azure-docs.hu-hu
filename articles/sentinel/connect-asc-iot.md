---
title: A IoT és az Azure Sentinel összeAzure Security Centerának összekötése | Microsoft Docs
description: Megtudhatja, hogyan csatlakoztathatók a IoT-hez készült Azure Security Centerek az Azure Sentinel szolgáltatáshoz.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: yelevin
ms.openlocfilehash: 3af51110a4c4604444573f62be65077c786db606
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588637"
---
# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel"></a>Az adatok összekapcsolásának Azure Security Center a IoT és az Azure Sentinel között 


> [!IMPORTANT]
> A IoT adatösszekötő Azure Security Center jelenleg nyilvános előzetes verzióban érhető el. Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A IoT-összekötő Azure Security Center segítségével továbbíthatja az összes Azure Security Center a IoT-események Azure Sentinelbe való továbbításához. 

## <a name="prerequisites"></a>Előfeltételek

- **Olvasási** és **írási** engedélyek azon a munkaterületen, amelyen az Azure Sentinel üzembe lett helyezve
- A **IoT Azure Security Center** engedélyezni kell a megfelelő IoT hub (ka) **t**
- **Olvasási** és **írási** engedélyek a csatlakozni kívánó **Azure-IoT hub**
- **Olvasási** és **írási** engedélyek az **Azure IoT hub erőforráscsoporthoz**

> [!NOTE]
> Habár az előfizetése számára engedélyezni kell az Azure Security Center **standard** szintű licencet a stream IoT-erőforrás-riasztások Azure Sentinel szolgáltatásba való bekapcsolásához, a IoT-riasztások az Azure sentinelben való Azure Security Center megtekintéséhez csak az előfizetéshez tartozó Azure Security Center **szabad** szintű licencre van szükség. 

## <a name="connect-to-azure-security-center-for-iot"></a>Kapcsolódás Azure Security Center IoT

1. Az Azure Sentinelben válassza az **adatösszekötők** lehetőséget, majd kattintson a **Azure Security Center IoT** csempére.
1. A jobb alsó ablaktáblán kattintson az **összekötő lap megnyitása**lehetőségre. 
1. Kattintson a **Kapcsolódás**lehetőségre minden olyan IoT hub-előfizetés mellett, amelynek a riasztásait és az eszközök riasztásait az Azure Sentinel szolgáltatásba kívánja továbbítani. 
    - Ha a IoT Azure Security Center nincs engedélyezve ezen a központban **, egy figyelmeztető üzenet jelenik meg** . A szolgáltatás elindításához kattintson az **enable (Engedélyezés** ) hivatkozásra. 
1. Eldöntheti, hogy szeretné-e, hogy a riasztások Azure Security Center IoT az incidensek automatikus előállítását az Azure Sentinelben. Az **incidensek létrehozása**területen válassza az **Engedélyezés** lehetőséget az alapértelmezett analitikus szabály engedélyezéséhez, hogy automatikusan hozzon létre incidenseket a csatlakoztatott biztonsági szolgáltatásban létrehozott riasztásokból. Ez a szabály az **Analytics** > **aktív** szabályok területén módosítható vagy módosítható.

> [!NOTE]
> Némi időbe telik, amíg a központi lista frissül a kapcsolatok módosítása után. 

## <a name="log-analytics-alert-display"></a>Log Analytics riasztás megjelenítése

A Log Analytics megfelelő sémájának használata a IoT-riasztások Azure Security Center megjelenítéséhez:

1. Nyissa meg a **naplók** > **SecurityInsights** > **SecurityAlert**, vagy keressen rá a **SecurityAlert**kifejezésre. 
2. A szűréssel csak a következő kql szűrővel rendelkező IoT által generált riasztások Azure Security Center látható:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
``` 

### <a name="service-notes"></a>Szolgáltatási megjegyzések

IoT Hub csatlakoztatása után a hub-adatközpont körülbelül 15 perccel később elérhető az Azure Sentinelben.


## <a name="next-steps"></a>További lépések

Ebből a dokumentumból megtudhatta, hogyan kapcsolódhat Azure Security Center a IoT-hez az Azure Sentinel szolgáltatáshoz. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.
