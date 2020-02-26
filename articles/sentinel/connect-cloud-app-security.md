---
title: Cloud App Security-adatkapcsolat összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Megtudhatja, hogyan csatlakoztatható Cloud App Security-adatkapcsolat az Azure Sentinelhez.
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
ms.date: 10/23/2019
ms.author: yelevin
ms.openlocfilehash: 348576fbbdd1037f9e2e792218b96bbbecf36668
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588365"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Adatok összekapcsolásának Microsoft Cloud App Security 



Egyetlen kattintással továbbíthatja a naplókat [Cloud app Securityból](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) az Azure sentinelbe. Ez a kapcsolat lehetővé teszi, hogy a riasztásokat a Cloud App Securityból az Azure Sentinelbe továbbítsa. 

## <a name="prerequisites"></a>Előfeltételek

- Globális rendszergazdai vagy biztonsági rendszergazdai engedélyekkel rendelkező felhasználó
- Cloud Discovery naplók Azure Sentinelbe való továbbításához [engedélyezze az Azure sentinelt Siem-ként Microsoft Cloud app Securityban](https://aka.ms/AzureSentinelMCAS).

> [!IMPORTANT]
> Cloud Discovery naplók betöltése jelenleg nyilvános előzetes verzióban érhető el.
> Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="connect-to-cloud-app-security"></a>Kapcsolódás Cloud App Securityhoz

Ha már rendelkezik Cloud App Security, győződjön meg arról, hogy az engedélyezve van a [hálózaton](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Ha Cloud App Security üzembe helyezése és az adatai betöltése történik, a riasztási adatot könnyen továbbíthatja az Azure Sentinelbe.


1. Az Azure Sentinelben válassza az **adatösszekötők**lehetőséget, majd kattintson a **Cloud app Security** csempére, és válassza az **összekötő megnyitása lapot**.

1. Válassza ki, hogy mely naplókba kívánja továbbítani az Azure Sentinel alkalmazást, és válassza a **riasztások** és a **Cloud Discovery naplók** (előzetes verzió) lehetőséget. 

1. Kattintson a **Csatlakozás** gombra.

1. Ha a Log Analytics vonatkozó sémát szeretné használni a Cloud App Security riasztásokhoz, keresse meg a **SecurityAlert**.




## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtanulta, hogyan csatlakozhat Microsoft Cloud App Security az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats.md).
