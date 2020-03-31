---
title: A Cloud App Security adatainak összekapcsolása az Azure Sentinelhez| Microsoft dokumentumok
description: Ismerje meg, hogyan kapcsolhatja össze a Cloud App Security adatait az Azure Sentinelhez.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588365"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Adatok csatlakoztatása a Microsoft Cloud App Security alkalmazásbiztonságból 



Egyetlen kattintással streamelheti a naplókat a [Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) alkalmazásból az Azure Sentinelbe. Ez a kapcsolat lehetővé teszi, hogy a cloud app security-ből az Azure Sentinelbe továbbítsa a riasztásokat. 

## <a name="prerequisites"></a>Előfeltételek

- Globális rendszergazdai vagy biztonsági rendszergazdai engedélyekkel rendelkező felhasználó
- A Cloud Discovery-naplók Azure Sentinelbe való streameléséhez [engedélyezze az Azure Sentinelt SIEM-ként a Microsoft Cloud App Security szolgáltatásban.](https://aka.ms/AzureSentinelMCAS)

> [!IMPORTANT]
> A Cloud Discovery-naplók betöltése jelenleg nyilvános előzetes verzióban érhető el.
> Ez a szolgáltatás szolgáltatásszint-szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott.
> További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)
 
## <a name="connect-to-cloud-app-security"></a>Csatlakozás a Cloud App Security alkalmazásbiztonsághoz

Ha már rendelkezik cloud app security, győződjön meg arról, hogy engedélyezve van [a hálózaton.](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security)
Ha a Cloud App Security telepítve van, és betöltése az adatokat, a riasztási adatok könnyen streamelhető az Azure Sentinel.


1. Az Azure Sentinelben válassza **az Adatösszekötők**lehetőséget, kattintson a **Cloud App Security** csempére, és válassza az Összekötő megnyitása **lapot.**

1. Válassza ki, hogy mely naplókat szeretné streamelni az Azure Sentinelbe, **kiválaszthatja a riasztások és** **a felhőfelderítési naplók** (előzetes verzió) lehetőséget. 

1. Kattintson a **Csatlakozás** gombra.

1. Ha a Megfelelő sémát szeretné használni a Log Analytics szolgáltatásban a Cloud App Security riasztásokhoz, keresse meg a **SecurityAlert (SecurityAlert)** kifejezést.




## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtanulta, hogyan csatlakoztathatja a Microsoft Cloud App Securityt az Azure Sentinelhez. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:
- Ismerje meg, hogyan [kaphat betekintést az adatokba és a potenciális fenyegetésekbe.](quickstart-get-visibility.md)
- Az Azure Sentinel segítségével első lépések [a fenyegetések észleléséhez.](tutorial-detect-threats.md)
