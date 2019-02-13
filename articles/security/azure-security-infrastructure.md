---
title: Azure-infrastruktúra biztonsági |} A Microsoft Docs
description: A cikk a Microsoft biztonságossá tétele az Azure-adatközpontok működését ismerteti.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: dc9b4db37e811d8bac6df2d532fd3629d98c9650
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56104766"
---
# <a name="azure-infrastructure-security"></a>Azure-infrastruktúra biztonsági
A Microsoft Azure által felügyelt és a Microsoft által üzemeltetett adatközpontok futtatja. Ezek az földrajzilag elosztott adatközpontok megfelelnek a fő iparági szabványok – például az ISO/IEC 27001:2013 és a NIST SP 800-53 előírásain, a biztonság és megbízhatóság. Az adatközpontok felügyelt, figyelni, és a Microsoft üzemeltetési csapat által felügyelt. Az üzemeltetési csapat tapasztalat biztosítása a világ legnagyobb online szolgáltatásai a 24 x 7 folytonossági rendelkezik.

A cikksorozat Microsoft használ az Azure-infrastruktúra secure információkat biztosít. A cikk címe:

- [fizikai biztonság](azure-physical-security.md)
- [Rendelkezésre állás](azure-infrastructure-availability.md)
- [Összetevők és a határok](azure-infrastructure-components.md)
- [Hálózati architektúra](azure-infrastructure-network.md)
- [Éles hálózati környezetben](azure-production-network.md)
- [SQL Database](azure-infrastructure-sql.md)
- [Műveletek](azure-infrastructure-operations.md)
- [Monitorozás](azure-infrastructure-monitoring.md)
- [Integritás](azure-infrastructure-integrity.md)
- [Adatvédelem](azure-protection-of-customer-data.md)

## <a name="shared-responsibility-model"></a>Közös felelősség modell
Fontos tudni, hogy az osztálynak a között, és a Microsoft felelőssége. A helyszínen, a teljes verem a saját, de a felhőre váltáskor látnia néhány feladatkört átvitele a Microsoft. A következő ábra mutatja be a hatáskörüket, központi telepítését a Jegyzettömböt (szolgáltatott szoftver [SaaS], szolgáltatásként nyújtott platformon [PaaS], [IaaS] szolgáltatás és a helyszíni infrastruktúra) típusától függően.

![Ábrája feladatkörei][1]

Ön felelős minden esetben a következő, függetlenül a központi telepítési típus:

- Adatok
- Végpontok
- Fiók
- Hozzáférés-kezelés

Győződjön meg arról, hogy megértette az osztálynak a SaaS, PaaS és IaaS-telepítésben, és a Microsoft felelőssége. További információkért lásd: [megosztott feladatkörök a felhő-számítástechnika](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf).

## <a name="next-steps"></a>További lépések
A Microsoft nem biztonságossá tétele az Azure-infrastruktúra kapcsolatos további információkért lásd:

- [Azure létesítményekben, a helyi és a fizikai biztonság](azure-physical-security.md)
- [Azure-infrastruktúra rendelkezésre állása](azure-infrastructure-availability.md)
- [Az Azure information rendszerösszetevők és határok](azure-infrastructure-components.md)
- [Az Azure hálózati architektúra](azure-infrastructure-network.md)
- [Az Azure éles hálózati környezetben](azure-production-network.md)
- [Az Azure SQL Database biztonsági funkciók](azure-infrastructure-sql.md)
- [Azure éles környezetben való üzemeltetés és a felügyelet](azure-infrastructure-operations.md)
- [Azure-infrastruktúra figyelése](azure-infrastructure-monitoring.md)
- [Az Azure infrastruktúra-integritás](azure-infrastructure-integrity.md)
- [Az Azure vásárlói adatok védelmére](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-security-infrastructure/responsibility-zones.png
