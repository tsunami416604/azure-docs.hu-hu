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
ms.openlocfilehash: 831baabcc26130d9b2c730afd85f2ed4b3842690
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80756357"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>Riasztások összekapcsolása a Microsoft Defender komplex veszélyforrások elleni védelemmel 


> [!IMPORTANT]
> A Microsoft Defender komplex veszélyforrások elleni védelmi riasztások betöltése jelenleg nyilvános előzetes verzióban érhető el.
> Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott.
> További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 

A [Microsoft Defender komplex veszélyforrások elleni védelem](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) összekötője lehetővé teszi a riasztások továbbítását a Microsoft Defender komplex veszélyforrások elleni védelemből az Azure Sentinel szolgáltatásba. Ezzel a művelettel átfogóbban elemezheti a szervezet biztonsági eseményeit, és a hatékony és azonnali reagálás érdekében forgatókönyveket hozhat létre.

## <a name="prerequisites"></a>Előfeltételek

- Érvényes licenccel kell rendelkeznie a Microsoft Defender komplex veszélyforrások elleni védelemhez, a [Microsoft DEFENDER ATP üzembe helyezésének beállítása](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing)című témakörben leírtak szerint. 
- Az Azure Sentinel-bérlő rendszergazdájának vagy biztonsági rendszergazdájának kell lennie.


## <a name="connect-to-microsoft-defender-advanced-threat-protection"></a>Kapcsolódás a Microsoft Defender komplex veszélyforrások elleni védelemhez

Ha a Microsoft Defender komplex veszélyforrások elleni védelem üzembe helyezését és betöltését végzi, a riasztások könnyen továbbíthatók az Azure Sentinel szolgáltatásba.


1. Az Azure Sentinelben válassza az **adatösszekötők**lehetőséget, majd kattintson a **Microsoft Defender komplex veszélyforrások elleni védelem** csempére, és válassza az **összekötő megnyitása lapot**.
1. Kattintson a **Csatlakozás** gombra. 
1. Ha a Defender ATP-riasztások esetében a Log Analytics megfelelő sémáját szeretné használni, keresse meg a **SecurityAlert** , és a **szolgáltató neve** **MDATP**.




## <a name="next-steps"></a>További lépések
Ebből a dokumentumból megtanulta, hogyan csatlakoztatható a Microsoft Defender ATP az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats.md).
