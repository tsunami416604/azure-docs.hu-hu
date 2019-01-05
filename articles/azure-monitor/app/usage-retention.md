---
title: Adatmegőrzési felhasználóelemzés webes alkalmazásokhoz az Azure Application insights segítségével |} A Microsoft docs
description: Hány felhasználó térjen vissza az alkalmazásba?
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/03/2017
ms.pm_owner: daviste;NumberByColors
ms.reviewer: mbullwin
ms.author: daviste
ms.openlocfilehash: 4b21632f936093c0a48abf2d13389f22306045cc
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54052904"
---
# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>Adatmegőrzési felhasználóelemzés webes alkalmazásokhoz az Application insights segítségével

Az adatmegőrzési funkció [Azure Application Insights](../../application-insights/app-insights-overview.md) segít, elemzése, hogy hány felhasználó térjen vissza az alkalmazást, és milyen gyakran adott feladatok elvégzéséhez, vagy célok eléréséhez. Ha egy játék helyhez futtat, például sikerült felhasználók, akik játékfejlesztőknek, amelynek a szám elvesztése után térjen vissza a hely utáni winning visszaadása, akik a számok összehasonlítása. Ennek segítségével növelheti a felhasználói élmény és az üzleti stratégia is.

## <a name="get-started"></a>Bevezetés

Ha még nem látható adatok az adatmegőrzési eszközben, az Application Insights portálon [megtudhatja, hogyan kezdheti el a használati eszközökkel](usage-overview.md).

## <a name="the-retention-tool"></a>A megőrzés eszközzel

![Megtartási eszköz](./media/usage-retention/retention.png)

1. Az eszköztár lehetővé teszi a felhasználóknak új adatmegőrzési-jelentéseket hozhat létre, nyissa meg a meglévő adatmegőrzési-jelentések, aktuális megőrzési jelentés mentése vagy mentse, mivel a mentett jelentések végrehajtott módosítások visszaállítása, a jelentésben lévő adatok frissítése, a jelentés e-mailben vagy közvetlen hivatkozás megosztása és a dokumentáció eléréséhez oldal. 
2. Alapértelmezés szerint a megőrzési minden felhasználó, aki nem majd visszatért, és minden más időszakban volt látható. Kiválaszthatja, hogy az eseményeket az adott felhasználó tevékenységéről szűkíteni eltérő kombinációja.
3. Adjon hozzá egy vagy több szűrő tulajdonságai. Például a felhasználók egy adott országban vagy régióban is koncentrálhat. Kattintson a **frissítés** a szűrők beállítását követően. 
4. A teljes megőrzési diagram felhasználói megőrzési összegzését jeleníti meg a kijelölt időszakban között. 
5. A rács őrzi meg a #2. a Lekérdezésszerkesztő megfelelően felhasználók számát jeleníti meg. Minden egyes sora egy felhasználói kohorsz a következő végrehajtó bármely esemény időtartama alatt megjelenített időpont a jelöl. A sor minden cella jeleníti meg, hány a kohorsz visszaadott legalább egyszer egy újabb időszakra. Egyes felhasználóknak egynél több idő adhatnak vissza. 
6. Az insights-kártyák felső 5 kezdeményező esemény, és biztosít a felhasználóknak a megőrzési jelentés jobban megértette felső öt visszaadott események megjelenítése. 

![Adatmegőrzési egérrel való rámutatás](./media/usage-retention/hover.png)

Felhasználók a megtartási eszköz eléréséhez, elmagyarázza, mit jelent a cella analytics gomb és eszköztippek a cellák fölé viszi. Az elemzés gombra a felhasználók számára a elemzőeszköz, az előre összeállított lekérdezés létrehozása a felhasználók a cellából vesz igénybe. 

## <a name="use-business-events-to-track-retention"></a>Üzleti eseményeket segítségével nyomon követheti a megőrzési

A leghasznosabb megőrzési elemzés lekéréséhez mérjük események, amelyek jelentős üzleti tevékenységét. 

Például sok felhasználó előfordulhat, hogy nyissa meg egy oldalt az alkalmazásban nélkül a játék jeleníti meg. A lapmegtekintés követési ezért biztosítja a pontos becslést hányan térjen vissza a játék jól tudja korábban után. A játékosok egyértelmű képet kapjon, az alkalmazás egy egyéni eseményt, amikor egy felhasználó ténylegesen fájlból játszik le kell küldenie.  

Tanácsos kódoljon egyéni eseményeket, amelyek a fő üzleti műveleteket, és ezek a megőrzési elemzés céljából. A játék biztosítandó rögzítéséhez kell írnia egy sor kódot egyéni esemény küldése az Application Insightsba. Ha Ön ír a weboldal kódját, vagy a Node.JS-ben, akkor a következőhöz hasonló:

```JavaScript
    appinsights.trackEvent("won game");
```

Vagy ASP.NET kódban:

```csharp
   telemetry.TrackEvent("won game");
```

[További információ az egyéni események írása](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).


## <a name="next-steps"></a>További lépések
- Használati bővítsék küldésének megkezdése [egyéni események](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) vagy [Lapmegtekintések](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Ha már küldhet egyéni eseményeket vagy lapmegtekintéseket, Fedezze fel az megtudhatja, hogy a felhasználók használni a szolgáltatást a Használatelemzési eszközben.
    - [Felhasználók, munkamenetek, események](usage-segmentation.md)
    - [Tölcsérek](usage-funnels.md)
    - [Felhasználói folyamatok](usage-flows.md)
    - [Munkafüzetek](../../azure-monitor/app/usage-workbooks.md)
    - [Adja hozzá a felhasználói környezet](usage-send-user-context.md)


