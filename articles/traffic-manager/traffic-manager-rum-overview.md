---
title: Az Azure Traffic Manager valós Felhasználóiélmény-mérések
description: A Traffic Manager valós Felhasználóiélmény-mérések bemutatása
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: fd37ef739522955ae8227db39a41aecf199d65c3
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54052819"
---
# <a name="traffic-manager-real-user-measurements-overview"></a>Traffic Manager valós Felhasználóiélmény-mérések áttekintése

Traffic Manager-profil beállítása, a teljesítmény-útválasztási módszert használja, a szolgáltatás jelenik meg, ha a DNS-lekérdezésekre vonatkozó kérelmek érkeznek, és ezeket az Azure-régióban, amely a legkisebb késéssel tartománytagi hitelesítéssel közvetlen útválasztási döntéseket. Ez a hálózati késés intelligenciával, amely különböző végfelhasználói hálózatok fenntartja a Traffic Manager használatával történik.

Valós felhasználói mérések lehetővé teszi, hogy a hálózati késési mérések mértéket az Azure-régióban, az ügyfélalkalmazások, a végfelhasználók használják, és rendelkezik a Traffic Manager megfontolandó legyen útválasztási döntéseket. A valós Felhasználóiélmény-mérések használatához kiválasztásával növelheti ezekhez a hálózatokhoz származik, amelyen a végfelhasználók találhatók kérelmek útválasztását pontosságát. 

## <a name="how-real-user-measurements-work"></a>Hogyan működik a valós felhasználói mérések

Valós felhasználói mérések végfelhasználói hálózatot, amelyben használt ahogyan kellene az ügyfél alkalmazások mérték késés az Azure-régiók szerint működnek. Például ha egy weblap, amelyen a felhasználók különböző helyeken (például az Észak-amerikai régió) keresztül érhető el, használhatja valós felhasználói mérések a Teljesítménycentrikus útválasztási mód használhassa őket az ajánlott Azure-régió, amelyben a kiszolgáló alkalmazás üzemel.

Elindítja a weblapok (a benne egy egyedi kulcsot) egy Azure megadott JavaScript beágyazásával. Miután ez megtörtént, amikor egy felhasználó meglátogat weblapról, a JavaScript lekérdezi a Traffic Manager kell értékelnie, hogy az Azure-régiók információt szeretne kapni. A szolgáltatás végpontok a parancsfájlt, majd ezekben a régiókban, egymás után úgy, hogy egy ezen Azure-régióban lévő üzemeltetett és a várakozási idő közötti megállapítva vastagságú lemezképet letölti a kérelmet küldte mérték és időt adja vissza a az első bájtig eltelt érkezésekor . Ezeknek a méréseknek majd vissza jelenti a Traffic Manager szolgáltatáshoz.

Idővel ez történik, hogy hányszor, és a Traffic Manager a hálózatok késési jellemzői kapcsolatos pontos információk lekérése, amelyben vezető számos hálózatokon keresztül a végfelhasználók találhatók. Ezt az információt elindítja a által a Traffic Manager útválasztási döntéseit foglalandó első. Ennek eredményeképpen vezet nagyobb pontosságát az ezeket a döntéseket küldött valós Felhasználóiélmény-mérések alapján.

Valós felhasználói mérések használatakor küldött a Traffic Manager-mérések száma alapján számlázzuk. A díjszabás a további részletekért látogasson el a [díjszabását ismertető lapon a Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="next-steps"></a>További lépések
- Ismerje meg, hogyan használható [weblapokat valós felhasználói mérések](traffic-manager-create-rum-web-pages.md)
- Ismerje meg, [Traffic Manager működése](traffic-manager-overview.md)
- Tudjon meg többet [Mobile Center](https://docs.microsoft.com/mobile-center/)
- Tudjon meg többet a [forgalom-útválasztási módszerek](traffic-manager-routing-methods.md) a Traffic Manager által támogatott
- Ismerje meg, hogyan [Traffic Manager-profil létrehozása](traffic-manager-create-profile.md)

