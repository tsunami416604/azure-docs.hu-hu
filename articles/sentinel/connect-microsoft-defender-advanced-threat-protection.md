---
title: A Microsoft Defender ATP-adatainak csatlakoztatása az Azure Sentinelhez| Microsoft dokumentumok
description: Ismerje meg, hogyan kapcsolhatja össze a Microsoft Defender komplex veszélyforrások elleni védelem adatait az Azure Sentinelhez.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588212"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>A Microsoft Defender komplex veszélyforrások elleni védelemriasztásai 


> [!IMPORTANT]
> A Microsoft Defender komplex veszélyforrások elleni védelem naplóinak betöltése jelenleg nyilvános előzetes verzióban érhető el.
> Ez a szolgáltatás szolgáltatásszint-szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott.
> További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)
 

Egyetlen kattintással streamelheti a [Microsoft Defender komplex veszélyforrások elleni védelemből](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) származó riasztásokat az Azure Sentinelbe. Ez a kapcsolat lehetővé teszi, hogy a Microsoft Defender komplex veszélyforrások elleni védelemből érkező riasztásokat az Azure Sentinelbe továbbítsa. 

## <a name="prerequisites"></a>Előfeltételek

- Érvényes licenc a Microsoft Defender komplex veszélyforrások elleni védelemhez, amely a [Licencelés idotartamának ellenőrzése és a Microsoft Defender komplex veszélyforrások elleni védelemhez való teljes beállítása](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing)című témakörben leírtak szerint van engedélyezve. 
- Az Azure Sentinel-bérlő rendszergazdájának vagy biztonsági rendszergazdájának kell lennie.


## <a name="connect-to-microsoft-defender-advanced-threat-protection"></a>Csatlakozás a Microsoft Defender komplex veszélyforrások elleni védelméhez

Ha a Microsoft Defender komplex veszélyforrások elleni védelem telepítve van, és az adatok betöltése, a riasztások könnyen streamelhető az Azure Sentinel.


1. Az Azure Sentinelben válassza **az Adatösszekötők**lehetőséget, kattintson a **Microsoft Defender komplex veszélyforrások elleni védelmének** csempéjére, és válassza az **Összekötő megnyitása lapot.**
1. Kattintson a **Csatlakozás** gombra. 
1. Ha a Defender ATP-riasztások naplóanalytics megfelelő sémáját szeretné használni, keresse meg a **SecurityAlert** kifejezést, és a **szolgáltató neve** **MDATP.**




## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtanulta, hogyan csatlakoztathatja a Microsoft Defender ATP-t az Azure Sentinelhez. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:
- Ismerje meg, hogyan [kaphat betekintést az adatokba és a potenciális fenyegetésekbe.](quickstart-get-visibility.md)
- Az Azure Sentinel segítségével első lépések [a fenyegetések észleléséhez.](tutorial-detect-threats.md)
