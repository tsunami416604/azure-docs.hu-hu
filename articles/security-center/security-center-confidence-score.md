---
title: Megbízhatósági pontszám az Azure Security Centerben | Microsoft dokumentumok
description: A Security Center megbízhatósági pontszámokat hoz létre, hogy segítsen a csapatnak meghatározni, hogy a fenyegetés jogszerű-e, és hogyan kell rangsorolni és rangsorolni a riasztásokat.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e88198f8-2e16-409d-a0b0-a62e68c2f999
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2018
ms.author: memildin
ms.openlocfilehash: 8aa17f473c550c99d91862754e6a746575aca202
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77604383"
---
# <a name="alert-confidence-score-preview"></a>Riasztási megbízhatósági pontszám (előzetes verzió)

Az Azure Security Center láthatóvá teszi az Azure-ban futtatott erőforrásokat, és figyelmezteti, ha észleli a lehetséges problémákat. A riasztások mennyisége kihívást jelenthet egy biztonsági műveleti csapat számára, hogy egyénileg foglalkozzon, és szükségessé válik a kivizsgálandó riasztások rangsorolása. A riasztások vizsgálata összetett és időigényes lehet, és ennek eredményeképpen egyes riasztások figyelmen kívül hagynak.

A Biztonsági központban található megbízhatósági pontszám (jelenleg előzetes verzióban) segíthet a csapatnak a riasztások osztályozásában és rangsorolásában. A Security Center automatikusan alkalmazza az iparági bevált gyakorlatokat, az intelligens algoritmusokat és az elemzők által használt folyamatokat annak megállapítására, hogy egy fenyegetés jogszerű-e, és megbízható tartalomra mutató pontszám formájában érdemi elemzéseket biztosít.

## <a name="how-the-confidence-score-is-triggered"></a>A megbízhatósági pontszám aktiválásának oka

Riasztások jönnek létre, ha gyanús folyamatokat észlel a virtuális gépeken futó. A Security Center áttekinti és elemzi ezeket a riasztásokat az Azure-ban futó Windows virtuális gépeken. Automatizált ellenőrzéseket és korrelációkat hajt végre speciális algoritmusok használatával a szervezet több entitása és adatforrása, valamint az összes Azure-erőforrás között, és megbízhatósági pontszámot mutat be, amely azt méri, hogy mennyire magabiztos a Security Center hogy a figyelmeztető jelzés valódi, és ki kell vizsgálni.

## <a name="understanding-the-confidence-score"></a>A megbízhatósági pontszám megértése

A megbízhatósági pontszám 1 és 100 között mozog, és azt a megbízhatóságot jelöli, amelyet a Security Center rendelkezik, hogy a riasztást ki kell vizsgálni. Minél magasabb a pontszám, annál magabiztosabb a Security Center, hogy a riasztás valódi rosszindulatú tevékenységet jelez. A megbízhatósági pontszám tartalmazza a legfontosabb okokat, amiért a riasztás megkapta a megbízhatósági pontszámot. A megbízhatósági pontszám megkönnyíti a biztonsági elemzők számára, hogy rangsorolják a riasztásokra adott válaszaikat, és először a legsürgetőbb támadásokat kezeljék, végső soron csökkentve a támadásokra és a támadásokra való reagáláshoz szükséges időt.

A megbízhatósági pontszám megtekintése:
- A Biztonsági központ portálon nyissa meg a Biztonsági riasztások panelt.
-  A riasztások és incidensek a legmagasabbtól a legalacsonyabbig vannak rendszerezve, ami azt jelenti, hogy minél magabiztosabb a Security Center, hogy a riasztás fenyegetést jelent, annál közelebb van az oldal tetejéhez. 


 ![Megbízhatósági pontszám][1]

A Security Center riasztásba vetett bizalmához hozzájáruló adatok megtekintése:
- A biztonsági riasztás **i. bizalom**csoportban tekintse meg a megbízhatósági pontszámhoz hozzájáruló megfigyeléseket, és nyerjen betekintést a riasztáshoz kapcsolódóan. Ez több betekintést nyújt a riasztást okozó tevékenységek jellegébe.

  ![Gyanús megbízhatósági pontszám][2]

A Security Center megbízhatósági pontszáma segítségével rangsorolhatja a riasztási osztályozást a környezetében. A megbízhatósági pontszám időt és energiát takarít meg a riasztások automatikus vizsgálatával, az iparág bevált gyakorlatainak és intelligens algoritmusainak alkalmazásával, valamint virtuális elemzőként való működéssel annak meghatározására, hogy mely fenyegetések valósak, és hol kell összpontosítania a figyelmét.


<!--Image references-->
[1]: ./media/security-center-confidence-score/confidence-score.png
[2]: ./media/security-center-confidence-score/suspicious-confidence-score.png
