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
ms.openlocfilehash: c5c0f74ed8a5688b20eea4e74f747d3ff6dd0e63
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96339999"
---
# <a name="connect-your-data-from-defender-for-iot-to-azure-sentinel-preview"></a>Az adatok összekötése a Defender for IoT és az Azure Sentinel között (előzetes verzió)

Az Azure Sentinel for IoT adatösszekötője jelenleg nyilvános előzetes verzióban érhető el. Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/en-us/support/legal/preview-supplemental-terms/).

Ebből az útmutatóból megtudhatja, hogyan csatlakoztatható a Defender a IoT-hez az Azure Sentinel szolgáltatáshoz.

> [!div class="checklist"]
> * Előfeltételek
> * Kapcsolati beállítások
> * Log Analytics riasztás nézet

Riasztások összekapcsolását a Defendertől a IoT, és közvetlenül az Azure Sentinelben továbbíthatja őket.

Ha szorosabban integrálja az Azure Defendert a IoT az Azure Sentineltel, az első Felhőbeli natív SIEM-vel és az első SIEM-szel, a natív IoT és az OT biztonsággal, a Microsoft egyszerűbb megoldást kínál az informatikai és ipari hálózatok egységes biztonságára. Az Azure Sentinel gépi tanulásával kombinálva ez az integráció lehetővé teszi a szervezetek számára, hogy gyorsan azonosítsák a sokszor határokon átnyúló, illetve a határokon átívelő többfokozatú támadásokat. Emellett az Azure Defender a IoT integrációja az Azure Sentinel biztonsági előkészítési, automatizálási és reagálási (SZÁRNYALó) képességeivel lehetővé teszi az automatikus válaszadást és a megelőzést a beépített, nem optimalizált forgatókönyvek használatával. 

## <a name="prerequisites"></a>Előfeltételek

- A munkaterület **olvasási** és **írási** engedélyekkel kell rendelkeznie.
- A **IoT Defender** -nek **engedélyezni** kell a megfelelő IoT hub (ka) t.
- **Olvasási** és **írási** engedéllyel kell rendelkeznie az **Azure-IoT hubhoz** , amelyhez csatlakozni szeretne.
- Az **Azure IoT hub erőforráscsoporthoz** **olvasási** és **írási** engedélyekkel is rendelkeznie kell.


## <a name="connect-to-defender-for-iot"></a>Kapcsolódás a Defenderhez a IoT-hez

1. Az Azure Sentinelben válassza az **adatösszekötők** lehetőséget, majd kattintson a **Defender for IoT** csempére.
1. A jobb oldali ablaktábla alján kattintson az **összekötő lap megnyitása** lehetőségre.
1. Kattintson a **Kapcsolódás** lehetőségre minden olyan IoT hub-előfizetés mellett, amelynek a riasztásait és az eszközök riasztásait az Azure Sentinel szolgáltatásba kívánja továbbítani.
    - Ha a IoT Defender nincs engedélyezve ezen a központban, egy figyelmeztető üzenet jelenik meg. Kattintson az **Engedélyezés** hivatkozásra a szolgáltatás elindításához és engedélyezéséhez.
1. Eldöntheti, hogy szeretné-e, hogy a Defender IoT az incidensek automatikus előállítását az Azure Sentinelben. Az **incidensek létrehozása** területen válassza az **Engedélyezés** lehetőséget, ha engedélyezni szeretné, hogy a szabály automatikusan hozzon létre incidenseket a generált riasztásokból.  Ez a szabály az **Analytics**  >  **aktív** szabályok területén módosítható vagy módosítható.

> [!NOTE]
>A kapcsolatok módosítása után akár 10 másodpercig is eltarthat a központ listájának frissítése.

## <a name="using-log-analytics-for-alert-display"></a>Log Analytics használata a riasztások megjelenítéséhez

A Log Analytics vonatkozó sémájának használata a IoT riasztások megjelenítéséhez:

1. Nyissa meg a **naplók**  >  **SecurityInsights**  >  **SecurityAlert**, vagy keressen rá a **SecurityAlert** kifejezésre.
1. A szűréssel csak a IoT által generált riasztások jelennek meg a következő kql szűrő használatával:

```kusto
SecurityAlert | where ProductName == "Defender for IoT"
```

### <a name="service-notes"></a>Szolgáltatási megjegyzések

IoT Hub csatlakoztatása után a hub-adatközpont körülbelül 15 perccel később elérhető az Azure Sentinelben.

## <a name="next-steps"></a>További lépések

Ebből a dokumentumból megtanulta, hogyan csatlakoztatható a Defender a IoT az Azure Sentinelhez. A veszélyforrások észlelésével és a biztonsági adathozzáféréssel kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan használhatja az Azure Sentinelt az [adatai és a potenciális fenyegetések](../sentinel/quickstart-get-visibility.md)megismerésére.

- Ismerje meg, hogyan [férhet hozzá a IoT biztonsági adataihoz](how-to-security-data-access.md)