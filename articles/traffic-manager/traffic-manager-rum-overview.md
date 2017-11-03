---
title: "Az Azure Traffic Manager felhasználó mérések |} Microsoft Docs"
description: "Felhasználó mérések őket a Traffic Manager bemutatása"
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/19/2017
ms.author: kumud
ms.custom: 
ms.openlocfilehash: a7e8ae605b6234341d9ab8b790f4c54d8627f29f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="traffic-manager-real-user-measurements-overview"></a>Traffic Manager valós felhasználói mérések áttekintése

>[!NOTE]
>A tényleges felhasználói mérések szolgáltatást a Traffic Manager nyilvános előzetes verziójában, és nem rendelkezhet azonos szintű rendelkezésre állást és megbízhatóságot, szolgáltatások, amelyek általában a rendelkezésre állási kiadási. A szolgáltatás nem támogatott, van, korlátozott képességeket, és előfordulhat, hogy nem érhető el az összes Azure helyét. A rendelkezésre állás és a szolgáltatás állapotát a legfrissebb értesítések, tekintse meg a [Azure Traffic Manager-frissítések](https://azure.microsoft.com/updates/?product=traffic-manager) lap.

Állít be Traffic Manager-profil a teljesítmény-útválasztási módszert használja, ha a szolgáltatás keres, ha a DNS-lekérdezési kérelmek érkező, és ezeket az Azure-régió, amely a legkisebb mértékű késleltetést tartománytagi hitelesítéssel közvetlen útválasztási döntéseket. Ez úgy érhető el, a hálózati késés eszközintelligencia Traffic Manager által a különböző végfelhasználói hálózatok használata.

Valós felhasználói mérések lehetővé teszi az Azure-régiók, hálózati késés mérések mérjük az ügyfélalkalmazások, használja a végfelhasználók számára, és rendelkezik a Traffic Manager vegye figyelembe ezt az információt, valamint útválasztási döntések meghozásakor. A tényleges felhasználói mérések választ, növelheti az ezekhez a hálózatokhoz érkező, amelyen a végfelhasználók találhatók kérelmek útválasztási pontosságát. 

## <a name="how-real-user-measurements-work"></a>Valós felhasználói mérések működése

Valós felhasználói mérések azzal, hogy az ügyfél alkalmazások mérték késés Azure-régiók, a végfelhasználó hálózatokon használt vannak alapegységét működik. Tegyük fel Ha egy weblap, amelyen a felhasználók férhetnek hozzá (például az Észak-amerikai régió), a különböző helyek között kihasználhatja valós felhasználói mérések power teljesítmény útválasztási módszer használatakor a legjobb Azure-régiót való eléréséhez amely a kiszolgálóalkalmazás tárolja.

A weblapok ágyaz be egy Azure megadott JavaScript (a benne lévő egyedi kulcs) kezdődik. Ezt követően, amikor a felhasználó meglátogat weblapról, a JavaScript lekérdezi a Traffic Manager kell értékelnie, hogy az Azure-régiók kapcsolatos információkat. A szolgáltatás adja vissza, amely végpontok a parancsfájlt, amely akkor mérték e régiók egymás után úgy, hogy egy adott Azure-régiók üzemel, és a várakozási idő közötti megjegyezni egyetlen képpont kép letölti a kérelmet küldték, és az idő, amikor az első bájt érkezett . A mérési adatok majd vissza jelenti a Traffic Manager szolgáltatásba.

Idővel ez sokszor történik, és a Traffic Manager, amelyben a hálózatot késési jellemzői pontosabb információt első vezető sok hálózatokon keresztül a végfelhasználók találhatók. Ez az információ akkor kezdődik, által a Traffic Manager útválasztási döntések szereplő első. Ennek eredményeképpen vezet ezeket a döntéseket a tényleges felhasználói mérések küldött alapján a megnövekedett pontosságát.

Valós felhasználói mérések használatakor számlázása a Traffic Manager küldött mérések száma alapján. Az árakkal kapcsolatos további részletekért látogasson el a [árképzést ismertető oldalra Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="next-steps"></a>Következő lépések
- Ismerje meg, hogyan használható [valós felhasználói mérések a weblapokat](traffic-manager-create-rum-web-pages.md)
- Ismerje meg, [Traffic Manager működése](traffic-manager-overview.md)
- További információ [Mobile Center](https://docs.microsoft.com/mobile-center/)
- További információ a [forgalom-útválasztási módszert](traffic-manager-routing-methods.md) Traffic Manager által támogatott
- Megtudhatja, hogyan [Traffic Manager-profil létrehozása](traffic-manager-create-profile.md)

