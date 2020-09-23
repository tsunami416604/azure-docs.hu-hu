---
title: Az Azure Sentinel for Defender konfigurálása a IoT (előzetes verzió)
description: Ez a cikk azt ismerteti, hogyan konfigurálható az Azure Sentinel a Defender for IoT megoldás adatainak fogadására.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 71147352c5b75195ed0dff2b05acc5315f3183cc
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940056"
---
# <a name="connect-your-data-from-defender-for-iot-to-azure-sentinel-preview"></a>Az adatok összekötése a Defender for IoT és az Azure Sentinel között (előzetes verzió)

Az Azure Sentinel IoT-adatösszekötő Azure Security Center jelenleg nyilvános előzetes verzióban érhető el. Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/en-us/support/legal/preview-supplemental-terms/).

Ebből az útmutatóból megtudhatja, hogyan csatlakoztatható a Defender a IoT-hez az Azure Sentinel szolgáltatáshoz.

> [!div class="checklist"]
> * Előfeltételek
> * Kapcsolati beállítások
> * Log Analytics riasztás nézet

Riasztások összekapcsolását a Defendertől a IoT, és közvetlenül az Azure Sentinelben továbbíthatja őket.

## <a name="prerequisites"></a>Előfeltételek

- A munkaterület **olvasási** és **írási** engedélyekkel kell rendelkeznie.
- A **IoT Defender** -nek **engedélyezni** kell a megfelelő IoT hub (ka) t.
- **Olvasási** és **írási** engedéllyel kell rendelkeznie az **Azure-IoT hubhoz** , amelyhez csatlakozni szeretne.
- Az **Azure IoT hub erőforráscsoporthoz** **olvasási** és **írási** engedélyekkel is rendelkeznie kell.

> [!NOTE]
> Az általános Azure-erőforrásokra vonatkozó riasztások elküldéséhez az előfizetéshez tartozó Azure Security Center standard szintű licencelésnek kell futnia. A Defender for IoT ingyenes szintjére vonatkozó licenccel csak a IoT kapcsolatos riasztások továbbítva lesznek az Azure Sentinel szolgáltatáshoz.

## <a name="connect-to-defender-for-iot"></a>Kapcsolódás a Defenderhez a IoT-hez

1. Az Azure Sentinelben válassza az **adatösszekötők** lehetőséget, majd kattintson a **Defender for IoT** csempére.
1. A jobb oldali ablaktábla alján kattintson az **összekötő lap megnyitása**lehetőségre.
1. Kattintson a **Kapcsolódás**lehetőségre minden olyan IoT hub-előfizetés mellett, amelynek a riasztásait és az eszközök riasztásait az Azure Sentinel szolgáltatásba kívánja továbbítani.
    - Ha a IoT Defender nincs engedélyezve ezen a központban, egy figyelmeztető üzenet jelenik meg. Kattintson az **Engedélyezés** hivatkozásra a szolgáltatás elindításához és engedélyezéséhez.
1. Eldöntheti, hogy szeretné-e, hogy a Defender IoT az incidensek automatikus előállítását az Azure Sentinelben. Az **incidensek létrehozása**területen válassza az **Engedélyezés** lehetőséget, ha engedélyezni szeretné, hogy a szabály automatikusan hozzon létre incidenseket a generált riasztásokból.  Ez a szabály az **Analytics**  >  **aktív** szabályok területén módosítható vagy módosítható.

> [!NOTE]
>A kapcsolatok módosítása után akár 10 másodpercig is eltarthat a központ listájának frissítése.

## <a name="log-analytics-alert-display"></a>Log Analytics riasztás megjelenítése

A Log Analytics vonatkozó sémájának használata a IoT riasztások megjelenítéséhez:

1. Nyissa meg a **naplók**  >  **SecurityInsights**  >  **SecurityAlert**, vagy keressen rá a **SecurityAlert**kifejezésre.
1. A szűréssel csak a IoT által generált riasztások jelennek meg a következő kql szűrő használatával:

```kusto
SecurityAlert | where ProductName == "Defender for IoT"
```

### <a name="service-notes"></a>Szolgáltatási megjegyzések

IoT Hub csatlakoztatása után a hub-adatközpont körülbelül 15 perccel később elérhető az Azure Sentinelben.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtanulta, hogyan csatlakoztatható a Defender a IoT az Azure Sentinelhez. A veszélyforrások észlelésével és a biztonsági adathozzáféréssel kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan használhatja az Azure Sentinelt az [adatai és a potenciális fenyegetések](https://docs.microsoft.com/azure/sentinel/quickstart-get-visibility)megismerésére.

- Ismerje meg, hogyan [férhet hozzá a IoT biztonsági adataihoz](how-to-security-data-access.md)
