---
title: Az Azure infrastruktúra biztonságát |} Microsoft Docs
description: A cikk azt ismerteti, hogyan a Microsoft biztosítja az Azure adatközpontjaiban biztonságát.
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
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 313fbc0fea317e8888bf64e7f7817ab0e5c9f049
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102326"
---
# <a name="security-of-azure-infrastructure"></a>Az Azure infrastruktúra biztonságát
A Microsoft Azure adatközpontjaiban felügyelt és a Microsoft által üzemeltetett fut. A földrajzilag elosztott adatközpontokban kulcs szabványoknak megfelelő, például az ISO/IEC 27001:2013 és a NIST SP 800-53-as, a biztonsága és megbízhatósága. Az adatközpontok felügyelt, figyeli, és Microsoft műveleti személyzet felügyeli. A műveleti személyzet élmény, a postai a világ legnagyobb online szolgáltatások a 24 x 7 folytonosságának évnyi rendelkezik.

A cikkek az adatsorozat tájékoztatást nyújt a Microsoft funkciója az Azure-infrastruktúra védelméhez. A cikk címe:

- [Fizikai biztonság](azure-physical-security.md)
- [Rendelkezésre állás](azure-infrastructure-availability.md)
- [Összetevők és határok](azure-infrastructure-components.md)
- [Hálózati architektúra](azure-infrastructure-network.md)
- [Éles hálózati környezetben](azure-production-network.md)
- [SQL Database](azure-infrastructure-sql.md)
- [Műveletek](azure-infrastructure-operations.md)
- [Monitorozás](azure-infrastructure-monitoring.md)
- [Integritás](azure-infrastructure-integrity.md)
- [Adatvédelem](azure-protection-of-customer-data.md)

## <a name="shared-responsibility-model"></a>Megosztott felelősségi modell
Fontos tudni, az osztálynak a felelős, és a Microsoft között. A helyszínen, a teljes verem saját, de a felhőbe mozgatásakor látnia néhány feladatkört átvitele Microsoft. A következő feladata mátrix jeleníti meg a veremben egy szoftverfrissítési szoftverszolgáltatás (SaaS) platform (PaaS), vagy infrastruktúra (IaaS) szolgáltatás központi telepítést, amelyet Ön és a Microsoft felelős.

![Közös felelősség][1]

Feladatok, amelyek mindig maradnak, függetlenül attól, milyen típusú központi telepítését, akkor a következők:

- Adatok
- Végpontok
- Fiók
- Hozzáférés-kezelés

Győződjön meg arról, hogy tudomásul veszi a SaaS, a PaaS és IaaS-telepítésben, és a Microsoft közötti felelősségi osztás. Lásd: [megosztott feladatkörök a felhőalapú informatika](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf) további részletek.

## <a name="next-steps"></a>További lépések
Microsoft funkciója az Azure-infrastruktúra védelméhez kapcsolatos további információkért lásd:

- [Az Azure létesítményekben, a helyszíni és a fizikai biztonság](azure-physical-security.md)
- [Azure-infrastruktúra rendelkezésre állása](azure-infrastructure-availability.md)
- [Az Azure információk rendszerösszetevők és határok](azure-infrastructure-components.md)
- [Az Azure hálózati architektúra](azure-infrastructure-network.md)
- [Az Azure éles hálózati környezetben](azure-production-network.md)
- [A Microsoft Azure SQL Database biztonsági funkciói](azure-infrastructure-sql.md)
- [Az Azure éles műveletek és kezelése](azure-infrastructure-operations.md)
- [Azure-infrastruktúra megfigyelése](azure-infrastructure-monitoring.md)
- [Azure-infrastruktúra integritása](azure-infrastructure-integrity.md)
- [Az Azure-ban felhasználói adatok védelme](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-security-infrastructure/responsibility-zones.png
