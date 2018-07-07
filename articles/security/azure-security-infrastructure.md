---
title: Azure-infrastruktúra biztonsági |} A Microsoft Docs
description: A cikk azt ismerteti, hogyan a Microsoft biztosítja, hogy az Azure-adatközpontok biztonsága.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: 397bd1f904b676a6ba020ec78fb1cad05c460be1
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2018
ms.locfileid: "37903856"
---
# <a name="azure-infrastructure-security"></a>Azure-infrastruktúra biztonsági
A Microsoft Azure által felügyelt és a Microsoft által üzemeltetett adatközpontok futtatja. Ezek az földrajzilag elosztott adatközpontok megfelelnek a fő iparági szabványok – például az ISO/IEC 27001:2013 és a NIST SP 800-53 előírásain, a biztonság és megbízhatóság. Az adatközpontok felügyelt, figyelni, és a Microsoft üzemeltetési csapat által felügyelt. Az üzemeltetési csapat tapasztalat biztosítása a világ legnagyobb online szolgáltatásai a 24 x 7 folytonossági rendelkezik.

A cikksorozat információkat biztosít a Microsoft célja az Azure-infrastruktúra biztonságossá tételéhez. A cikk címe:

- [Fizikai biztonság](azure-physical-security.md)
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
Fontos tudni, hogy az osztálynak a között, és a Microsoft felelőssége. A helyszínen, a teljes verem a saját, de a felhőre váltáskor látnia néhány feladatkört átvitele a Microsoft. A következő feladata mátrix jeleníti meg a verem egy szoftverfrissítési szolgáltatásként (SaaS), platformszolgáltatás (PaaS) és infrastruktúra platform szolgáltatás (IaaS), amelynek Ön a felelős a központi telepítés és a Microsoft felelős.

![Közös felelősség][1]

Feladatai, amelyek mindig maradnak, függetlenül a típusú telepítés, akkor a következők:

- Adatok
- Végpontok
- Fiók
- Hozzáférés-kezelés

Győződjön meg arról, hogy megértette az osztálynak a SaaS, PaaS és IaaS-telepítésben, és a Microsoft felelőssége. Lásd: [megosztott feladatkörök a felhő-számítástechnika](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf) további részleteket talál.

## <a name="next-steps"></a>További lépések
A Microsoft használ az Azure-infrastruktúra secure kapcsolatos további információkért lásd:

- [Azure létesítményekben, a helyi és a fizikai biztonság](azure-physical-security.md)
- [Azure-infrastruktúra rendelkezésre állása](azure-infrastructure-availability.md)
- [Az Azure information rendszerösszetevők és határok](azure-infrastructure-components.md)
- [Az Azure hálózati architektúra](azure-infrastructure-network.md)
- [Az Azure éles hálózati környezetben](azure-production-network.md)
- [A Microsoft Azure SQL Database biztonsági funkciók](azure-infrastructure-sql.md)
- [Azure éles környezetben való üzemeltetés és a felügyelet](azure-infrastructure-operations.md)
- [Az Azure-infrastruktúra figyelése](azure-infrastructure-monitoring.md)
- [Az Azure infrastruktúra-integritás](azure-infrastructure-integrity.md)
- [Az Azure-ban tárolt ügyféladatok védelme](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-security-infrastructure/responsibility-zones.png
