---
title: Azure-infrastruktúra biztonsága | Microsoft Docs
description: A cikk azt ismerteti, hogyan működik a Microsoft az Azure-adatközpontok védelmére.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: a17d98d49d2c653e2498a663829d26e8a171fd74
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72433510"
---
# <a name="azure-infrastructure-security"></a>Azure-infrastruktúra biztonsága
A Microsoft Azure a Microsoft által felügyelt és üzemeltetett adatközpontokban fut. Ezek a földrajzilag elosztott adatközpontok megfelelnek a legfontosabb iparági szabványoknak, például az ISO/IEC 27001:2013 és a NIST SP 800-53 számára a biztonság és a megbízhatóság érdekében. Az adatközpontokat a Microsoft Operations munkatársai felügyelik, figyelik és felügyelik. Az operatív munkatársaknak több éve van tapasztalata a világ legnagyobb online szolgáltatásokának 24 x 7 folytonossággal való megvalósításában.

Ez a cikksorozat információt nyújt arról, hogy a Microsoft Hogyan védi az Azure-infrastruktúrát. A cikkek címe:

- [Fizikai biztonság](physical-security.md)
- [Rendelkezésre állás](infrastructure-availability.md)
- [Összetevők és határok](infrastructure-components.md)
- [Hálózati architektúra](infrastructure-network.md)
- [Üzemi hálózat](production-network.md)
- [SQL Database](infrastructure-sql.md)
- [Műveletek](infrastructure-operations.md)
- [Monitorozás](infrastructure-monitoring.md)
- [Integritását](infrastructure-integrity.md)
- [Adatvédelem](protection-customer-data.md)

## <a name="shared-responsibility-model"></a>Megosztott felelősségi modell
Fontos megérteni az Ön és a Microsoft közötti felelősség megosztását. A helyszíni, a teljes verem tulajdonosa, de a felhőbe való áttérés során bizonyos kötelezettségek a Microsoft felé irányulnak. Az alábbi ábrán a verem (szoftver mint szolgáltatás [SaaS], a szolgáltatásként nyújtott platform [Péter], az infrastruktúra-szolgáltatás [IaaS] és a helyszíni) központi telepítésének típusa látható.

![A feladatokat bemutató ábra](./media/infrastructure/responsibility-zones.png)

Mindig az alábbiakért felelős, függetlenül az üzembe helyezés típusától:

- Adatok
- Endpoints (Végpontok)
- Fiók
- Hozzáférés-kezelés

Győződjön meg róla, hogy tisztában van az Ön és a Microsoft között a SaaS-, a Pásti-és a IaaS-telepítésben. További információ: [a felhőalapú számítástechnika megosztott feladatai](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/225237/1/Shared%20Responsibilities%20for%20Cloud%20Computing%20(2017-04-03).pdf).

## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni arról, hogy mit tesz a Microsoft az Azure-infrastruktúra biztonságossá tételéhez, tekintse meg a következőt:

- [Azure-létesítmények,-telephelyek és fizikai biztonság](physical-security.md)
- [Azure-infrastruktúra rendelkezésre állása](infrastructure-availability.md)
- [Azure Information System-összetevők és-határok](infrastructure-components.md)
- [Azure hálózati architektúra](infrastructure-network.md)
- [Azure-beli üzemi hálózat](production-network.md)
- [Azure SQL Database biztonsági funkciók](infrastructure-sql.md)
- [Azure-beli üzemi műveletek és felügyelet](infrastructure-operations.md)
- [Azure-infrastruktúra figyelése](infrastructure-monitoring.md)
- [Azure-infrastruktúra integritása](infrastructure-integrity.md)
- [Azure Customer-adatvédelem](protection-customer-data.md)


