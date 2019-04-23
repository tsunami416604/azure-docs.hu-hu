---
title: A Cloud App Security-adatok csatlakozhat az Azure-on Előzetesben Sentinel-|} A Microsoft Docs
description: Ismerje meg, hogyan kell csatlakozni a Cloud App Security-adatok Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: cd9e5e27-fdd4-4717-8924-be4c1c430f23
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 5a7dfeed7b52453b38720c21c7d213679b8d2854
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59789449"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Adatok csatlakoztatása a Microsoft Cloud App Security szolgáltatásból 

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Naplóinak streamelheti [a Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) be Azure Sentinel-egyetlen kattintással. Ez a kapcsolat lehetővé teszi, hogy a Cloud App Security riasztásai streamelése az Azure-Sentinel. 

## <a name="prerequisites"></a>Előfeltételek

- Globális rendszergazdai vagy biztonsági rendszergazdai jogosultsággal rendelkező felhasználó

## <a name="connect-to-cloud-app-security"></a>Csatlakozzon a Cloud App Securityvel

Ha már rendelkezik a Cloud App Security, győződjön meg arról, hogy [engedélyezve van a hálózaton](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Ha a Cloud App Security van telepítve, és az adatok feldolgozására, a riasztási adatok könnyen továbbítható az Azure-Sentinel.


1. Az Azure-Sentinel, válassza **adatösszekötők** és kattintson a **a Cloud App Security** csempére.

2. Kattintson a **Connect** (Csatlakozás) gombra.

3. A megfelelő sémát a Log Analytics használata a Cloud App Security-riasztásokat, keresse meg **SecurityAlert**.


## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtudhatta, hogyan szeretne csatlakozni a Microsoft Cloud App Security Azure Sentinel. Azure-Sentinel kapcsolatos további információkért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan [betekintést nyerhet az adatok és a potenciális fenyegetések](quickstart-get-visibility.md).
- Első lépések [Azure Sentinel-fenyegetések észlelése](tutorial-detect-threats.md).
