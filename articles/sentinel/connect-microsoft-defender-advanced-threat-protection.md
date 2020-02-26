---
title: A Microsoft Defender ATP-adatkapcsolatok összekapcsolhatók az Azure Sentinel szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan kapcsolódhat a Microsoft Defender komplex veszélyforrások elleni védelemhez az Azure Sentinel szolgáltatáshoz.
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
ms.date: 10/13/2019
ms.author: yelevin
ms.openlocfilehash: ee2c917d02a442ebabc0e1b40bc245982af794f8
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588212"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>Riasztások összekapcsolása a Microsoft Defender komplex veszélyforrások elleni védelemmel 


> [!IMPORTANT]
> A Microsoft Defender komplex veszélyforrások elleni védelem naplófájljainak betöltése jelenleg nyilvános előzetes verzióban érhető el.
> Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 

A [Microsoft Defender komplex veszélyforrások elleni védelemről](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) érkező riasztásokat egyetlen kattintással továbbíthatja az Azure sentinelbe. Ez a kapcsolat lehetővé teszi, hogy a riasztásokat a Microsoft Defender komplex veszélyforrások elleni védelemből továbbítsa az Azure Sentinel szolgáltatásba. 

## <a name="prerequisites"></a>Előfeltételek

- Érvényes licenc a Microsoft Defender komplex veszélyforrások elleni védelemhez, amely a következő témakörben leírtak szerint engedélyezhető: a [Licencelés kiépítés ellenőrzése és a Microsoft Defender összetett veszélyforrások elleni védelem beállítása](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing). 
- Az Azure Sentinel-bérlő rendszergazdájának vagy biztonsági rendszergazdájának kell lennie.


## <a name="connect-to-microsoft-defender-advanced-threat-protection"></a>Kapcsolódás a Microsoft Defender komplex veszélyforrások elleni védelemhez

Ha a Microsoft Defender komplex veszélyforrások elleni védelem üzembe helyezését és betöltését végzi, a riasztások könnyen továbbíthatók az Azure Sentinel szolgáltatásba.


1. Az Azure Sentinelben válassza az **adatösszekötők**lehetőséget, majd kattintson a **Microsoft Defender komplex veszélyforrások elleni védelem** csempére, és válassza az **összekötő megnyitása lapot**.
1. Kattintson a **Csatlakozás** gombra. 
1. Ha a Defender ATP-riasztások esetében a Log Analytics megfelelő sémáját szeretné használni, keresse meg a **SecurityAlert** , és a **szolgáltató neve** **MDATP**.




## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtanulta, hogyan csatlakoztatható a Microsoft Defender ATP az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats.md).
