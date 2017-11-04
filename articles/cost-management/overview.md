---
title: "Azure áttekintése Költségkezelésére által Cloudyn |} Microsoft Docs"
description: "Az Azure Cost Management by Cloudyn egy többfelhős költségkezelő megoldás, amely segítséget nyújt az Azure és egyéb felhőerőforrások használatához."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 10/11/2017
ms.topic: overview
ms.service: cost-management
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 969340080bfe2b04704367c2225895728773119e
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2017
---
# <a name="what-is-azure-cost-management"></a>Mi az az Azure költség Management?

Azure költség-kezelést úgy Cloudyn felhőhasználat és az Azure-erőforrások és más szolgáltatók beleértve AWS és Google kiadásainak nyomon teszi lehetővé. Irányítópult könnyen érthető költség lemezfoglalási és -showbacks/erőforrásmérést is segítséget jelent. Költség felügyeleti segítségével optimalizálhatja a felhőt, majd is kezelhet, és állítsa be a túl erőforrások azonosításával kiadásokat.

Egy bevezető videót, lásd: [bemutatása az Azure költség Management Cloudyn](https://youtu.be/NWIRny6Wpsk).

## <a name="monitor-usage-and-spending"></a>A figyelő használati és kiadások

Az használatának figyelése és költségeik oka különösen fontos az olyan felhőalapú infrastruktúrák számára szervezetek után kell fizetnie, az erőforrások használják ki adott idő alatt. Amikor kihasználtsága meghaladja a szerződés küszöbértékét, váratlan költség többletfelhasználás gyorsan fordulhat elő. Néhány fontos tényezőt ad hoc figyelési tehet nehéz feladat. Először végez kivetítést átlagos használati alapján azt feltételezi, hogy a használat egy adott számlázási időszakban konzisztensek maradnak. Másodszor amikor költségek mellett, vagy meghaladja a költségvetést, fontos proaktív módon, hogy módosítsák a kiadásait értesítéseket kap. És a felhőszolgáltatók esetleg nem nyújtanak költség leképezési és a küszöbértékek vagy időszakra összehasonlítás jelentések.

Jelentések segítenek költségeik elemzéséhez és nyomon követheti a felhőalapú használata, a költségek és a trendeket. Jelentések használata keresztül idő, észlelését rendellenességeket, amelyek eltérnek a normál trendeket. A felhőalapú környezetben hatékonyság hiánya optimalizálási jelentések láthatók. Figyelje meg a hatékonyság hiánya, a költség elemzési jelentések is.

![Költség keresztül idő jelentés](media\overview\cost-over-time-rpt.png)


## <a name="manage-costs"></a>Költségeinek kezelése

Előzményadatokat is elemezheti használati és költségek adott idő alatt, és azonosíthatja a trendeket költségek kezeléséhez. Trendek majd segítségével felmérheti a jövőbeli kiadásokat. Költség felügyeleti hasznos tervezett költség jelentést is tartalmaz.

Költség foglalási költségek a költségeket a címkézési házirend alapján elemzésével kezeli. Az egyéni fiókok, erőforrások és entitások címkék használatával költség foglalási pontosítsa. Kategória Manager rendezi a címkék további irányítás biztosítja. És visszajelzés/jóváírás költség-foglalás segítségével megjelenítése az erőforrás-használat és a kapcsolódó költségeket a fogyasztói magatartás befolyásolásához vagy bérlői ügyfelek díjat számítanak.

Hozzáférés-vezérlés segítségével győződjön meg arról, hogy felhasználók és csoportok hozzáférési csak a költség felügyeleti adatokat, akkor szükség szerint költségek kezelésére. Segítségével entitás szerkezetét, a felhasználók kezelése és az ütemezett jelentések címzett listákkal hozzáférés hozzárendelése.

Riasztási kezelhetőek költségek által automatikusan értesítés, az szokatlan költségeik vagy túlköltekezés következik be. A riasztások is értesítést egyéb érintett felek automatikusan a költségkeret rendellenességek észlelését és túlköltekezés kockázatokat. Különböző jelentéseket riasztások költségvetési alapján, és a küszöbértékek költségű támogatja. Azonban riasztások jelenleg nem támogatottak a CSP partner és előfizetések.

## <a name="improve-efficiency"></a>Hatékonyság javítása

Határozza meg az optimális virtuális gépek használata és tétlen virtuális gépek azonosításához, vagy távolítsa el a tétlen virtuális gépek és a nem csatlakoztatott lemezek költség-kezeléssel. A méretezési optimalizálása és elégtelenség jelentésekben információk alapján, létrehozhat egy tervet méretű le vagy távolítsa el a tétlen virtuális gépek. Azonban optimalizálási jelentések jelenleg nem támogatottak a CSP partner és előfizetések.

![Méretezési javaslatok](.\media\overview\sizing.png)

Ha kiépített AWS fenntartott példányok, javíthatja a a fenntartott példányok kihasználtságának optimalizálása jelentéseket, ahol vásárlásának javaslatok megtekintése, módosítása nem használt fenntartásokat és kiosztás megtervezése.

## <a name="next-steps"></a>Következő lépések

Költség-kezeléssel ismeri, a következő lépéssel fogja regisztrálni a felhőkörnyezet és Fedezze fel az adatokat.

- [Az egyes Azure-előfizetés regisztrálása](quick-register-azure-sub.md)
