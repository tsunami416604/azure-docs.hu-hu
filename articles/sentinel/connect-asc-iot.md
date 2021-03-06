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
ms.date: 12/16/2020
ms.author: yelevin
ms.openlocfilehash: 95e78c7557092a4d1203a8df3a107fe7b63eac9b
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2020
ms.locfileid: "97631441"
---
# <a name="connect-your-data-from-azure-defender-formerly-azure-security-center-for-iot-to-azure-sentinel"></a>Az Azure Defender (korábban Azure Security Center) adatainak összekötése az Azure Sentinel IoT 


> [!IMPORTANT]
> A IoT-adatösszekötő Defender jelenleg nyilvános előzetes verzióban érhető el. Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az IoT-összekötő Defender használatával továbbíthatja az összes Defender IoT-eseményeit az Azure Sentinelbe. 

Ez az integráció lehetővé teszi a szervezetek számára, hogy gyorsan azonosítsák a gyakran határokon átnyúló, illetve a határokon átívelő támadásokat. Emellett az Azure Sentinel biztonsági előkészítési, automatizálási és reagálási (SZÁRNYALó) képességeinek IoT való integrációja lehetővé teszi az automatikus válaszadást és a megelőzést a beépített, nem optimalizált forgatókönyvek használatával. 
## <a name="prerequisites"></a>Előfeltételek

- **Olvasási** és **írási** engedélyek azon a munkaterületen, amelyen az Azure Sentinel üzembe lett helyezve
- A **IoT Defender számára** engedélyezni kell a megfelelő IoT hub (ka) **t**
- **Közreműködői** engedélyekkel kell rendelkeznie a csatlakozni kívánt **előfizetéshez**

## <a name="connect-to-defender-for-iot"></a>Kapcsolódás a Defenderhez a IoT-hez

1. Az Azure Sentinelben válassza az **adatösszekötők** lehetőséget, majd válassza a Defender lehetőséget a **IoT** (IoT esetében továbbra is Azure Security Center meghívható) a katalógusból.

1. A jobb oldali ablaktábla alján kattintson az **összekötő lap megnyitása** lehetőségre. 

1. Kattintson a **Kapcsolódás** lehetőségre minden olyan IoT hub-előfizetés mellett, amelynek a riasztásait és az eszközök riasztásait az Azure Sentinel szolgáltatásba kívánja továbbítani. 
    - Hibaüzenet jelenik meg, ha a Defender for IoT nincs engedélyezve legalább egy IoT Hub egy előfizetésen belül. Engedélyezze a Defender számára a IoT belüli IoT Hub a hiba eltávolításához.

1. Eldöntheti, hogy szeretné-e, hogy a Defender IoT az incidensek automatikus előállítását az Azure Sentinelben. Az **incidensek létrehozása** területen válassza az **Engedélyezés** lehetőséget az alapértelmezett elemzési szabály engedélyezéséhez, hogy a generált riasztásokból automatikusan hozzon létre incidenseket. Ez a szabály az **Analytics**  >  **aktív szabályok** területén módosítható vagy módosítható.

> [!NOTE]
> A kapcsolódási módosítások végrehajtása után az **előfizetések** listájának frissítése 10 másodpercig is eltarthat. 

## <a name="log-analytics-alert-view"></a>Log Analytics riasztás nézet

A Log Analytics vonatkozó sémájának használata a IoT riasztások megjelenítéséhez:

1. Nyissa meg a **naplók**  >  **SecurityInsights**  >  **SecurityAlert**, vagy keressen rá a **SecurityAlert** kifejezésre. 

2. A szűréssel csak a IoT által generált riasztások jelennek meg a következő kql szűrő használatával:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
``` 

### <a name="service-notes"></a>Szolgáltatási megjegyzések

Az **előfizetés** csatlakoztatása után körülbelül 15 perccel később elérhetővé válik a hub-adatbázis az Azure sentinelben.


## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtanulta, hogyan csatlakoztatható a Defender a IoT az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.
