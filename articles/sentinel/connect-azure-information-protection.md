---
title: Azure Information Protection-adatok csatlakoztatása Azure-on Előzetesben Sentinel-|} A Microsoft Docs
description: Ismerje meg, hogyan csatlakozhat az Azure Information Protection Azure Sentinel-adatok.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 2f970910e19b3c1ed9d262d356c49848f4248b09
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59790841"
---
# <a name="connect-data-from-azure-information-protection"></a>Adatok csatlakoztatása az Azure Information Protection

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Naplóinak streamelheti [Azure Information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) be Azure Sentinel-egyetlen kattintással. Azure Information Protection segít az adatok védelme a felhőben vagy a helyszíni infrastruktúra és a vezérlő és a Súgó biztonságos e-mail, dokumentumok és bizalmas adatok vállalaton kívüli megosztott tárolja-e. Könnyű besorolásával, valamint beágyazott címkéivel és engedélyek az Azure Information Protection-mindig adatvédelmi teljesítmény javításához. Ha csatlakozik az Azure Information Protection Azure Sentinel, akkor stream riasztásait az Azure Information Protection az Azure-Sentinel.


## <a name="prerequisites"></a>Előfeltételek

- Globális rendszergazdai, biztonsági rendszergazdának vagy information protection engedélyekkel rendelkező felhasználó


## <a name="connect-to-azure-information-protection"></a>Csatlakozás az Azure Information Protection

Ha már rendelkezik Azure Information Protection, győződjön meg arról, hogy [engedélyezve van a hálózaton](https://docs.microsoft.com/azure/information-protection/activate-service).
Ha Azure Information Protection üzembe van helyezve és fogad adatokat, a riasztási adatok egyszerűen továbbítható Azure Sentinel-be.


1. Az Azure-Sentinel, válassza **adatösszekötők** és kattintson a **Azure Information Protection** csempére.

2. Nyissa meg a [Azure Information Protection-portál](https://portal.azure.com/?ScannerConfiguration=true&EndpointDiscovery=true#blade/Microsoft_Azure_InformationProtection/DataClassGroupEditBlade/quickstartBlade) 

3. A **kapcsolat**, állítsa be a folyamatos átviteli naplók az Azure Information Protection az Azure-Sentinel kattintva [az analytics konfigurálását](https://portal.azure.com/#blade/Microsoft_Azure_InformationProtection/DataClassGroupEditBlade/analyticsOnboardBlade)

4. Válassza ki a munkaterületet, amelybe telepítette az Azure-Sentinel. 

5. Kattintson az **OK** gombra.

6. A megfelelő sémát használ a Log Analytics az Azure Information Protection-riasztások, keresse meg **InformationProtectionLogs_CL**.




## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtudhatta, hogyan Azure Sentinel-Azure Information Protection csatlakozni. Azure-Sentinel kapcsolatos további információkért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan [betekintést nyerhet az adatok és a potenciális fenyegetések](quickstart-get-visibility.md).
- Első lépések [Azure Sentinel-fenyegetések észlelése](tutorial-detect-threats.md).
