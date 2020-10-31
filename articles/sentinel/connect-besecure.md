---
title: Az Azure Sentinel biztonsági biztonságossá tétele Microsoft Docs
description: Megtudhatja, hogyan használhatja az Azure Sentinel-be a biztonságos naplókat a biztonsággal szemben biztonságos adatösszekötő használatával. Megtekintheti a munkafüzetekbe való biztonságos adatvédelmet, riasztásokat hozhat létre, és javíthatja a vizsgálatot.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2020
ms.author: yelevin
ms.openlocfilehash: f6fd6920fafe4c1080cb5539e4e0222d9d6e18cd
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93102898"
---
# <a name="connect-your-beyond-security-besecure-to-azure-sentinel"></a>A biztonság védelme az Azure Sentinel szolgáltatással

> [!IMPORTANT]
> Az Azure Sentinel szolgáltatásban a biztonsággal megőrzött adatösszekötők jelenleg nyilvános előzetes verzióban érhetők el. Ezt a szolgáltatást szolgáltatói szerződés nélkül biztosítjuk. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A biztonsággal megőrzött összekötőn túl egyszerűen csatlakoztathatja az összes biztonságos biztonsági megoldás naplóját az Azure Sentinelhez, az irányítópultok megtekintéséhez, egyéni riasztások létrehozásához és a vizsgálat javításához. A biztonságos és az Azure Sentinel közötti integráció a REST API használatát teszi lehetővé.

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="configure-and-connect-besecure"></a>A Biztonság beállítása és összekapcsolása

a biztonságos integráció és a naplók exportálása közvetlenül az Azure Sentinelbe is megadható.

1. Az Azure Sentinel-portálon kattintson az **adatösszekötők** lehetőségre, és válassza a biztonság megtartása **(előzetes verzió)** lehetőséget, majd **nyissa meg az összekötő lapot** .

1. Az alábbi lépéseket követve konfigurálhatja a beérkező ellenőrzési eredményeket, ellenőrizheti az állapotot és a naplózási naplókat az Azure Sentinel szolgáltatásban.

    **Hozzáférés az integrációs menühöz:**
    1. Kattintson a "további" menüpontra

    1. Kiszolgáló kiválasztása

    1. Integráció kiválasztása

    1. Az Azure Sentinel engedélyezése

    **Gondoskodjon az Azure Sentinel-beállítások biztonságáról.**
      - Másolja a *munkaterület-azonosítót* és az *elsődleges kulcsot* az Azure Sentinel Connector oldaláról, ILLESSZE be őket a biztonságos konfigurációba, majd kattintson a **módosítás** gombra.

## <a name="find-your-data"></a>Az adatai megkeresése

A sikeres kapcsolatok létrejötte után az adat a **naplók** területen, a **CustomLogs** szakaszban jelenik meg, az alábbi táblázatok közül egyet vagy többet:
  - `beSECURE_ScanResults_CL`
  - `beSECURE_ScanEvents_CL`
  - `beSECURE_Audit_CL`

A Log Analyticsban található, a következő táblázatban szereplő nevek egyikének megadásával kérdezheti le a megjelenő tábla neveit a lekérdezési időszak tetején.

## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése
Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics.

## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtanulta, hogyan csatlakozhat az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.
