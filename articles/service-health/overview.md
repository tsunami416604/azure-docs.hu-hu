---
title: Mi az Azure Service Health?
description: Személyre szabott információk arról, hogy az Azure-alkalmazások milyen hatással vannak a jelenlegi és jövőbeli Azure-szolgáltatásokkal kapcsolatos problémákra és karbantartásra.
ms.topic: overview
ms.date: 05/10/2019
ms.openlocfilehash: 44b819a88baec383d2faf80ab37edb903b0a5f4d
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77654086"
---
# <a name="what-is-azure-service-health"></a>Mi az Azure Service Health?

Az Azure számos lehetőséget kínál a felhőalapú erőforrások állapotának folyamatos tájékoztatására. Ezek az információk olyan aktuális és jövőbeli problémákat foglalnak magukban, mint például a szolgáltatás befolyásoló eseményei, a tervezett karbantartás és a rendelkezésre állást esetlegesen befolyásoló egyéb változások.

A Azure Service Health három külön kisebb szolgáltatás kombinációja.

Az Azure [status](azure-status-overview.md) szolgáltatás az Azure-ban az Azure Status (állapot) **[lapján](https://status.azure.com)** értesíti a szolgáltatások leállásáról. A lap az összes Azure-régióban az összes Azure-szolgáltatás állapotának globális nézetét jeleníti meg. Az állapot lap jól használható az incidensek széles körű hatásával kapcsolatban, de határozottan azt javasoljuk, hogy az Azure-beli felhasználók az Azure szolgáltatás állapotát használják az Azure-incidensek és-karbantartás tekintetében.

Az [Azure Service Health](service-health-overview.md) személyre szabott áttekintést nyújt az Ön által használt Azure-szolgáltatások és-régiók állapotáról. Ez a legjobb hely a szolgáltatások működését befolyásoló kommunikáció követéséhez szolgáltatáskimaradások, tervezett karbantartási tevékenységek és az egyéb, állapottal kapcsolatos tanácsadási információkhoz, mivel a hitelesített Azure Service Health szolgáltatás számon tartja az Ön által aktuálisan használt szolgáltatásokat és erőforrásokat. A Service Health használatának legjobb módja az, ha Service Health-riasztásokat állít be, amelyek a választott kommunikációs csatornákon értesítik Önt az Ön által használt Azure-szolgáltatások és -régiók működését befolyásoló hibákról, tervezett karbantartásokról és egyéb változásokról.

Az [Azure Resource Health](resource-health-overview.md) információt nyújt az egyes felhőalapú erőforrások, például egy adott virtuálisgép-példány állapotáról. Az Azure Monitor használatával a riasztásokat úgy is konfigurálhatja, hogy az Ön által használt felhőerőforrások rendelkezésre állásával kapcsolatos változásokról küldjön értesítést. Az Azure Resource Health szolgáltatás és az Azure Monitor értesítéseinek használatával percenként frissülő, teljes körű információkkal rendelkezhet az erőforrások rendelkezésre állására vonatkozóan, és gyorsan felmérheti, hogy egy adott hibát az Ön oldalán felmerülő probléma vagy valamelyik Azure-platform eseménye okoz.

Ezek a szolgáltatások együttesen átfogó képet nyújtanak az Azure állapotáról, az Ön igényeinek legjobban megfelelő részletességgel.

**Tekintse meg az Azure status oldal, a Azure Service Health és a Azure Resource Health áttekintését**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OgX6]

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]