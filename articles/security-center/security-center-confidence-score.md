---
title: Azure Security Center megbízhatósági pontszáma | Microsoft Docs
description: Security Center megbízhatósági pontszámokat hoz létre, amelyek segítségével a csapat eldöntheti, hogy a fenyegetés legitim-e, és hogyan osztályozhatja és rangsorolhatja a riasztásokat.
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
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77604383"
---
# <a name="alert-confidence-score-preview"></a>Riasztási megbízhatósági pontszám (előzetes verzió)

Azure Security Center az Azure-ban futtatott erőforrások láthatóságát biztosítja, és riasztást küld, ha észleli a lehetséges problémákat. A riasztások mennyisége nagy kihívást jelenthet a biztonsági műveletekért felelős csapat számára, és szükség lesz a vizsgálandó riasztások rangsorolására. A riasztások kivizsgálása összetett és időigényes lehet, ezért bizonyos riasztások figyelmen kívül lesznek hagyva.

Security Center a megbízhatósági pontszám (jelenleg előzetes verzióban) segíthet a csapat osztályozásában és a riasztások rangsorolásában. A Security Center automatikusan alkalmazza az iparági ajánlott eljárásokat, intelligens algoritmusokat és az elemzők által használt folyamatokat annak megállapítására, hogy a fenyegetés jogos-e, és hogy a megbízhatósági pontszám formájában hasznos elemzéseket biztosít-e.

## <a name="how-the-confidence-score-is-triggered"></a>A megbízhatósági pontszám kiváltása

A riasztások akkor jönnek létre, amikor a rendszer gyanús folyamatokat észlel a virtuális gépeken. Security Center áttekinti és elemzi ezeket a riasztásokat az Azure-ban futó Windows rendszerű virtuális gépeken. A speciális algoritmusok használatával automatizált ellenőrzéseket és összefüggéseket hajt végre a szervezeten belül több entitásra és adatforrásra vonatkozóan, valamint az összes Azure-erőforrást, és egy megbízhatósági pontszámmal mutatja be, hogy milyen mértékben Security Center a riasztás valódi, és meg kell vizsgálni.

## <a name="understanding-the-confidence-score"></a>A megbízhatósági pontszám megértése

A megbízhatósági pontszám 1 és 100 közötti tartományban van, és a megbízhatósági Security Center azt jelzi, hogy a riasztást ki kell vizsgálni. Minél nagyobb a pontszám, annál magabiztosabb Security Center, hogy a riasztás valódi kártékony tevékenységet jelez. A megbízhatósági pontszám tartalmazza a legfontosabb okok listáját, hogy a riasztás miért kapta meg a megbízhatósági pontszámát. A megbízhatósági pontszám megkönnyíti a biztonsági elemzők számára a riasztásokra adott válaszok rangsorolását, és először a legtöbbször megjelenő támadásokkal foglalkoznak, ami végül csökkenti a támadásokra és szabálysértésekre való reagáláshoz szükséges időt.

A megbízhatósági pontszám megtekintése:
- A Security Center portálon nyissa meg a biztonsági riasztások panelt.
-  A riasztások és incidensek a lehető legalacsonyabbra vannak rendezve, ami azt jelenti, hogy a több magabiztos Security Center az, hogy egy riasztás fenyegetést jelent, annál közelebb van az oldal tetejéhez. 


 ![Megbízhatósági pontszám][1]

A riasztásban Security Center megbízhatóságához hozzájáruló adatmennyiségek megtekintése:
- A biztonsági riasztások panelen, a **megbízhatóság**alatt tekintse meg a megbízhatósági pontszámhoz hozzájáruló észrevételeket, valamint a riasztással kapcsolatos elemzéseket. Ez részletesebben ismerteti a riasztást kiváltó tevékenységek természetét.

  ![Gyanús megbízhatósági pontszám][2]

Használja Security Center megbízhatósági pontszámát, hogy rangsorolja a riasztások osztályozását a környezetben. A megbízhatósági pontszám a riasztások automatikus kivizsgálásával, az iparági ajánlott eljárások és az intelligens algoritmusok alkalmazásával, valamint virtuális elemzőként való működésével időt takaríthat meg, és meghatározza, hogy mely fenyegetések valósak, és hol kell összpontosítania a figyelmet.


<!--Image references-->
[1]: ./media/security-center-confidence-score/confidence-score.png
[2]: ./media/security-center-confidence-score/suspicious-confidence-score.png
