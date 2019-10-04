---
title: Mi az Azure Service Health?
description: Személyre szabott információk az Azure-alkalmazások az Azure szolgáltatás jelenlegi és jövőbeli problémák és a karbantartás által érintett módját.
author: stephbaron
ms.author: stbaron
services: service-health
ms.service: service-health
ms.topic: article
ms.date: 05/10/2019
ms.openlocfilehash: 058a171766680f09eaf4de14ddd25235020b1ba4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079573"
---
# <a name="what-is-azure-service-health"></a>Mi az Azure Service Health?

Az Azure számos Önt folyamatosan értesíteni a felhőbeli erőforrások állapotára vonatkozó élményt biztosít. Ezen információk közé tartozik például a szolgáltatást érintő események, a tervezett karbantartás és az egyéb módosítások, amelyek befolyásolhatják a rendelkezésre állási jelenlegi és jövőbeli problémák.

Azure Service Health egy három külön kisebb szolgáltatások olyan kombinációját.

[Az Azure állapota](azure-status-overview.md) tájékoztatja a szolgáltatáskimaradások az Azure-ban a  **[Azure állapotlapján](https://status.azure.com)** . Az oldal összes Azure-szolgáltatások állapotának globális nézet készítése az összes Azure-régióban. Az állapotlapon útmutatással incidensekhez széleskörű hatással van, de erősen ajánlott, hogy a jelenlegi Azure-felhasználók azonnali tájékozódás az Azure az incidensek és a karbantartás az Azure service Health irányítópultján használhatja.

[Az Azure service health](service-health-overview.md) személyre szabott Azure-szolgáltatások és régiók használ állapotát jeleníti meg. Ez a legjobb hely a szolgáltatások működését befolyásoló kommunikáció követéséhez szolgáltatáskimaradások, tervezett karbantartási tevékenységek és az egyéb, állapottal kapcsolatos tanácsadási információkhoz, mivel a hitelesített Azure Service Health szolgáltatás számon tartja az Ön által aktuálisan használt szolgáltatásokat és erőforrásokat. A Service Health használatának legjobb módja az, ha Service Health-riasztásokat állít be, amelyek a választott kommunikációs csatornákon értesítik Önt az Ön által használt Azure-szolgáltatások és -régiók működését befolyásoló hibákról, tervezett karbantartásokról és egyéb változásokról.

[Az Azure resource health](resource-health-overview.md) az egyes felhőalapú erőforrások, például egy adott virtuálisgép-példány állapotával kapcsolatos információkat biztosít. Az Azure Monitor használatával a riasztásokat úgy is konfigurálhatja, hogy az Ön által használt felhőerőforrások rendelkezésre állásával kapcsolatos változásokról küldjön értesítést. Az Azure Resource Health szolgáltatás és az Azure Monitor értesítéseinek használatával percenként frissülő, teljes körű információkkal rendelkezhet az erőforrások rendelkezésre állására vonatkozóan, és gyorsan felmérheti, hogy egy adott hibát az Ön oldalán felmerülő probléma vagy valamelyik Azure-platform eseménye okoz.

Ezek a szolgáltatások együttesen átfogó képet nyújtanak az Azure állapotáról, az Ön igényeinek legjobban megfelelő részletességgel.

**Tekintse meg az Azure állapota lap, az Azure Service Health és az Azure Resource Health áttekintése**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OgX6]
