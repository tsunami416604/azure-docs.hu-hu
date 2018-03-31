---
title: Az Azure Mobile Engagement megvalósítási játék alkalmazás
description: Azure Mobile Engagement végrehajtásához szóló forgatókönyvet játékok
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 2cafc044-4902-4058-8037-49399bf6bf7f
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 96e827b57e804cc91798859bc906e49046decede
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="implement-mobile-engagement-with-gaming-app"></a>Mobile Engagement az Játékalkalmazásról megvalósítása
## <a name="overview"></a>Áttekintés
> [!IMPORTANT]
> Az Azure Mobile Engagement kivonja a 3/31/2018. Ezen a lapon hamarosan után törlődni fog.
> 

Egy játék kezdeti elindította az új alapú halászati role-play/stratégia játék alkalmazás. A game már működik, és 6 hónapig. A game hatalmas sikeres, és több millió letöltések rendelkezik pedig a megőrzési nagyon magas kezdeti játék alkalmazások képest. A negyedéves felülvizsgálati értekezleten érdekelt felek elfogadja átlagos bevétel felhasználónként (ARPU) növelnie kell azokat. Prémium szintű a játékbeli csomagok elérhetők, különleges ajánlatokat. Ezek a csomagok játék engedélyezése a felhasználók számára a Megjelenés és a teljesítmény halászati sorok és lures vagy a játék tackles frissítése. Azonban a csomag értékesítési nem nagyon alacsony. Úgy döntenek, először a felhasználói élmény analytics eszközzel elemzése, és majd fejlesztése az engagement program értékesítési használatával növelje speciális Szegmentálás.

Alapján a [Azure Mobile Engagement – első lépések útmutató ajánlott eljárásoknak megfelelő beállításában](mobile-engagement-getting-started-best-practices.md) engagement-stratégia kialakítása.

## <a name="objectives-and-kpis"></a>Célok és a KPI-k
A game megfeleljenek a kulcsfontosságú érintettekkel. Az összes fogadja el az egyik fő cél - növeléséhez a prémium csomag értékesítési 15 %. Hoznak létre üzleti a fő teljesítménymutatók (KPI-k) mérték, és a meghajtó ebben a célkitűzésben

* A game milyen szintű a ezeket a csomagokat vásárolt?
* Mi az a bevétel felhasználónként, munkamenetenként, heti és havi?
* Mik a kedvenc beszerzési típusok?

/ 1. rész a [Getting Started Guide](mobile-engagement-getting-started-best-practices.md) ismerteti, hogyan adható meg a célok és a KPI-k. 

Az üzleti KPI-k már meg van adva a Mobile termék Manager hoz létre új felhasználói trendeket és a megőrzési meghatározásához bevonási KPI-k.

* Megőrzési figyelése és a következő időszakok használja: napi, 2 naponta, hetente, havonta, és minden harmadik hónapban
* Aktív felhasználók száma
* Az áruházbeli alkalmazás minősítése

Az informatikai csapat javaslatai alapján, a következő műszaki KPI-k lettek hozzáadva a következő kérdések megválaszolásához:

* Mi az a felhasználó elérési út (mely megnyitják, mennyi időt igénybe rajta)
* Összeomlások vagy munkamenetenként észlelt hibák száma
* Milyen operációs rendszer verzióit futtatják a felhasználók?
* Mi az, hogy a felhasználók számára képernyő átlagos mérete?
* Milyen típusú internetkapcsolattal rendelkeznek a felhasználók?

Minden KPI-hez tartozó Mobile termék megadja az elemzőnek szüksége van, és hol helyezkedik el saját alkalmazástervezési adatokat.

## <a name="engagement-program-and-integration"></a>Bevonási program és az integráció
Egy speciális bevonási program létrehozása, előtt a mobileszköz projekt igazgató feladata a projekt ismeri részletesen bemutatja, hogyan, és a felhasználók által felhasznált termékek kell rendelkeznie.

3 hónap után a mobilalkalmazás-projekt igazgató összegyűjtötte elég adat alkalmazásbeli leküldéses értesítési eladásait javítása érdekében. Ezután Tanulja meg, amelyek:

* Az első beszerzési általában akkor fordul elő, 14 szinten. Ezekben az esetekben 90 %-át a vásárlást új legendary fegyverek $3.
* Ezekben az esetekben 80 %-át, a felhasználók, akik végzett a vásárlást, a termék folytatni, és ellenőrizze a további vásárol.
* A szint 20, akik elindíthatók egy több mint 10 $vagy hét.
* Prémium szintű csomagok szinten 16, 24 és 32 megvásárlása a felhasználók általában.

Az elemzés környezetnek köszönhetően a mobilalkalmazás-projekt igazgató úgy dönt, hogy létrehozása adott leküldéses értesítési sorozatok app Sales növelése érdekében. Három leküldéses folyamatok, amelyek ezután meghívja hoz: üdvözli a program, értékesítési Program és inaktív Program. További információ a [Playbooks](https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks) ![][1]

<!--Image references-->

[1]: ./media/mobile-engagement-game-scenario/notification-scenario.png

<!--Link references-->
