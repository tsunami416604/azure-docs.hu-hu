---
title: Squadra-technológiák összekapcsolásának integrálása az Azure Sentinel használatával | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztathatók a Squadra Technologies integrálása az Azure Sentinel szolgáltatáshoz.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: d904e51321870fb4b61a237c23e425034b76dc0b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588110"
---
# <a name="connect-your-squadra-technologies-secrmm-data-to-azure-sentinel"></a>A Squadra Technologies integrálása-adatai összekapcsolhatók az Azure Sentinel szolgáltatással 

> [!IMPORTANT]
> A Squadra Technologies Security cserélhető adathordozó-kezelő (integrálása) adatösszekötő az Azure Sentinel szolgáltatásban jelenleg nyilvános előzetes verzióban érhető el.
> Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


A Squadra Technologies integrálása-összekötővel könnyedén csatlakoztathatja Squadra-technológiáit integrálása biztonsági megoldási naplóit az Azure Sentinel használatával. Lehetővé teszi az irányítópultok megtekintését, az egyéni riasztások létrehozását és a vizsgálat tökéletesítését. Ez az összekötő az USB cserélhető tároló eseményeinek betekintését teszi lehetővé. A Squadra Technologies integrálása és az Azure Sentinel közötti integráció a REST API használatát teszi lehetővé.


> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="configure-and-connect-squadra-technologies-secrmm"></a>A Squadra Technologies integrálása konfigurálása és összekapcsolása 

A Squadra Technologies integrálása közvetlenül az Azure Sentinelbe integrálhatja és exportálhatja a naplókat.
1. Az Azure Sentinel-portálon kattintson az adatösszekötők elemre, és válassza a Squadra Technologies integrálása, majd az összekötő lap megnyitása lehetőséget.

2. Kövesse az [Azure Sentinel Squadra Technologies](http://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf) bevezetési útmutatójában ismertetett lépéseket, hogy Squadra integrálása-adatbázisokat kapjon az Azure sentinelben.   


## <a name="find-your-data"></a>Az adatai megkeresése

A sikeres kapcsolatok létrejötte után az adat a Log Analytics CustomLogs secRMM_CL alatt jelenik meg.
Ha a Squadra Technologies integrálása vonatkozó sémát szeretné használni a Log Analyticsban, keressen rá a secRMM_CL kifejezésre.

## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése
Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics. 


## <a name="next-steps"></a>További lépések
Ebből a dokumentumból megtudhatta, hogyan csatlakoztathatók a Squadra Technologies integrálása az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.

