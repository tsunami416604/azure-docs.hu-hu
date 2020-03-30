---
title: Mi az Azure Service Health?
description: Személyre szabott információk arról, hogy az Azure-alkalmazásokat hogyan befolyásolják az Azure-szolgáltatások jelenlegi és jövőbeli problémái és karbantartása.
ms.topic: overview
ms.date: 05/10/2019
ms.openlocfilehash: 44b819a88baec383d2faf80ab37edb903b0a5f4d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77654086"
---
# <a name="what-is-azure-service-health"></a>Mi az Azure Service Health?

Az Azure egy olyan élménycsomagot kínál, amely folyamatosan tájékoztatja a felhőalapú erőforrások állapotáról. Ezek az információk olyan aktuális és közelgő problémákat tartalmaznak, mint például a szolgáltatásra ható események, a tervezett karbantartás és egyéb olyan változások, amelyek befolyásolhatják a rendelkezésre állást.

Az Azure Service Health három különálló kisebb szolgáltatás kombinációja.

[Az Azure-állapot](azure-status-overview.md) az Azure-állapot lapon tájékoztatja az Azure szolgáltatáskimaradásairól. **[Azure Status page](https://status.azure.com)** A lap az összes Azure-szolgáltatás állapotának globális nézete az összes Azure-régióban. Az állapotlap jó hivatkozása a széles körben elterjedt incidensek, de azt javasoljuk, hogy a jelenlegi Azure-felhasználók az Azure-szolgáltatás állapotát, hogy tájékozott maradjon az Azure-incidensek és karbantartás.

[Az Azure-szolgáltatás állapota](service-health-overview.md) személyre szabott képet nyújt az Ön által használt Azure-szolgáltatások és -régiók állapotáról. Ez a legjobb hely a szolgáltatások működését befolyásoló kommunikáció követéséhez szolgáltatáskimaradások, tervezett karbantartási tevékenységek és az egyéb, állapottal kapcsolatos tanácsadási információkhoz, mivel a hitelesített Azure Service Health szolgáltatás számon tartja az Ön által aktuálisan használt szolgáltatásokat és erőforrásokat. A Service Health használatának legjobb módja az, ha Service Health-riasztásokat állít be, amelyek a választott kommunikációs csatornákon értesítik Önt az Ön által használt Azure-szolgáltatások és -régiók működését befolyásoló hibákról, tervezett karbantartásokról és egyéb változásokról.

[Az Azure-erőforrás állapota](resource-health-overview.md) az egyes felhőbeli erőforrások, például egy adott virtuálisgép-példány állapotáról nyújt tájékoztatást. Az Azure Monitor használatával a riasztásokat úgy is konfigurálhatja, hogy az Ön által használt felhőerőforrások rendelkezésre állásával kapcsolatos változásokról küldjön értesítést. Az Azure Resource Health szolgáltatás és az Azure Monitor értesítéseinek használatával percenként frissülő, teljes körű információkkal rendelkezhet az erőforrások rendelkezésre állására vonatkozóan, és gyorsan felmérheti, hogy egy adott hibát az Ön oldalán felmerülő probléma vagy valamelyik Azure-platform eseménye okoz.

Ezek a szolgáltatások együttesen átfogó képet nyújtanak az Azure állapotáról, az Ön igényeinek legjobban megfelelő részletességgel.

**Az Azure állapotjelző oldal, az Azure Service Health és az Azure Resource Health áttekintése**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OgX6]

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]