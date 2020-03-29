---
title: Hogyan működik a Personalizer - Personalizer
description: A Personalizer _hurok_ gépi tanulás segítségével készítse el a modellt, amely előre jelzi a tartalom legfelső műveletét. A modell kizárólag a Rang és jutalom hívásokkal küldött adatokra van betanítva.
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 836c207213ac52a60e27da6fc957418187059023
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623744"
---
# <a name="how-personalizer-works"></a>A Personalizer működése

A Personalizer erőforrás, a _tanulási ciklus,_ gépi tanulás segítségével a modell, amely előre jelzi a tartalom legfelső művelet. A modell kizárólag a **Rang** és **jutalom** hívásokkal küldött adatokra van betanítva. Minden hurok teljesen független egymástól.

## <a name="rank-and-reward-apis-impact-the-model"></a>A rangsorolási és jutalom API-k hatással vannak a modellre

A _funkciókkal_ és a _környezettel kapcsolatos funkciókat_ a Rang API-ba küldműveleteket. A **Rang** API úgy dönt, hogy használja:

* _Exploit_: A jelenlegi modell a legjobb művelet et a múltbeli adatok alapján határozza meg.
* _Felfedezés_: Válasszon másik műveletet a legfelső művelet helyett. Ezt [a százalékot konfigurálhatja](how-to-settings.md#configure-exploration-to-allow-the-learning-loop-to-adapt) a Personalizer erőforráshoz az Azure Portalon.

Ön határozza meg a jutalom pontszámot, és küldje el a pontszámot a Jutalom API-t. A **jutalom** API:

* Adatokat gyűjt, hogy betanítsa a modellt az egyes rangok funkcióinak rögzítésével és jutalompontszámainak jutalmazásával.
* Ezeket az adatokat a tanulási szabályzatban megadott konfiguráció alapján használja a modell _frissítésére._

## <a name="your-system-calling-personalizer"></a>A rendszer hívja A Personalizer

Az alábbi képen a Rang és jutalom hívások hívásának építészeti folyamata látható:

![helyettesítő szöveg](./media/how-personalizer-works/personalization-how-it-works.png "A személyre szabás működése")

1. A _funkciókkal_ és a _környezettel kapcsolatos funkciókat_ a Rang API-ba küldműveleteket.

    * A Personalizer dönti el, hogy kihasználja-e az aktuális modellt, vagy új lehetőségeket fedez fel a modellhez.
    * A rangsorolási eredmény az EventHubra kerül.
1. A felső rang visszakerül a _rendszer,_ mint jutalom akció azonosító .
    A rendszer bemutatja ezt a tartalmat, és a saját üzleti szabályai alapján határozza meg a jutalompontszámot.
1. A rendszer visszaadja a jutalom pontszámot a tanulási hurok.
    * Amikor a Personalizer megkapja a jutalmat, a jutalom az EventHubra kerül.
    * A rang és a jutalom összefügg.
    * Az AI-modell a korrelációs eredmények alapján frissül.
    * A következtetési motor frissül az új modellel.

## <a name="personalizer-retrains-your-model"></a>A personalizer átképzi a modellt

A Personalizer átvonatozhatja a modellt a **modell gyakoriságfrissítési** beállítása alapján az Azure Portalon a Personalizer erőforráson.

A Personalizer az összes jelenleg megőrzött adatot felhasználja az **Azure** Portalon a Personalizer-erőforráson megadott napok száma alapján.

## <a name="research-behind-personalizer"></a>Kutatás mögött Personalizer

Personalizer alapul élvonalbeli tudomány és a kutatás területén [megerősítése Learning](concepts-reinforcement-learning.md) beleértve a papírokat, kutatási tevékenységek, és a folyamatban lévő területeken feltárása a Microsoft Research.

## <a name="next-steps"></a>További lépések

További információ a Personalizer [legfontosabb forgatókönyveiről](where-can-you-use-personalizer.md)