---
title: Webalkalmazás-felhasználók megőrzésének elemzése az Azure Application Insights segítségével
description: Hány felhasználó tér vissza az alkalmazáshoz?
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 05/03/2017
ms.reviewer: mbullwin
ms.openlocfilehash: e6d9be6bc6d7f90153e2fb58aa404e281568dbdd
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80892410"
---
# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>Felhasználómegőrzési elemzés webes alkalmazásokhoz az Application Insights segítségével

Az Azure [Application Insights](../../azure-monitor/app/app-insights-overview.md) megőrzési szolgáltatása segítségével elemezheti, hogy hány felhasználó tér vissza az alkalmazásba, és milyen gyakran hajtanak végre bizonyos feladatokat vagy érnek el célokat. Ha például egy játékwebhelyet futtat, összehasonlíthatja azoknak a felhasználóknak a számát, akik egy játék elvesztése után térnek vissza a webhelyre, azzal a számmal, amely a győzelem után tér vissza. Ez a tudás segíthet mind a felhasználói élmény, mind az üzleti stratégia fejlesztésében.

## <a name="get-started"></a>Bevezetés

Ha még nem látja az adatokat az Alkalmazáselemzési portál adatmegőrzési eszközében, [olvassa el, hogyan kezdheti el a használati eszközöket.](usage-overview.md)

## <a name="the-retention-tool"></a>A Retenciós eszköz

![Megtartási eszköz](./media/usage-retention/retention.png)

1. Az eszköztár lehetővé teszi a felhasználók számára, hogy új adatmegőrzési jelentéseket hozzanak létre, meglévő adatmegőrzési jelentéseket nyissanak meg, mentsenek az aktuális adatmegőrzési jelentést, mentsenek, visszaállíthatják a mentett jelentések módosításait, frissítsék a jelentés adatait, megosszák a jelentést e-mailben vagy közvetlen hivatkozással, és hozzáférjenek a dokumentációs laphoz. 
2. Alapértelmezés szerint a megőrzés imitálta az összes felhasználót, aki bármit is tett, majd visszajött, és bármi mást tett egy idő alatt. Az események különböző kombinációját választhatja ki, hogy az adott felhasználói tevékenységekre összpontosítson.
3. Adjon hozzá egy vagy több szűrőt a tulajdonságokhoz. Fókuszálhat például egy adott ország vagy régió felhasználóira. A szűrők beállítása után kattintson a **Frissítés** gombra. 
4. A teljes megőrzési diagram a felhasználó megtartásának összegzését jeleníti meg a kiválasztott időszakban. 
5. A rács a #2 lekérdezésszerkesztő szerint megőrzött felhasználók számát jeleníti meg. Minden sor olyan felhasználók kohorszát jelöli, akik a megjelenített időszakban bármilyen eseményt végrehajtottak. A sor minden cellája azt mutatja, hogy a kohorszból hányat adott vissza legalább egyszer egy későbbi időszakban. Egyes felhasználók egynél több időszakban is visszatérhetnek. 
6. Az insights-kártyák az öt legfontosabb eseménysorozatot mutatják, és az öt legfontosabb visszaadott eseményt, hogy a felhasználók jobban megértsék a megőrzési jelentést. 

![Megőrzési egér rámutatása](./media/usage-retention/hover.png)

A felhasználók a retenciós eszköz cellái fölé mutatót fértve érhetik el az elemzési gombot és az eszköztippeket, amelyek elmagyarázzák, mit jelent a cella. Az Analytics gomb egy előre kitöltött lekérdezéssel az Analytics eszközhöz viszi a felhasználókat, hogy felhasználókat hozzon létre a cellából. 

## <a name="use-business-events-to-track-retention"></a>Üzleti események használata a megőrzés nyomon követésére

A leghasznosabb megőrzési elemzés hez mérje a jelentős üzleti tevékenységeket képviselő eseményeket. 

Előfordulhat például, hogy sok felhasználó anélkül nyit meg egy oldalt az alkalmazásban, hogy ne játszaná meg az általa megjelenített játékot. Ha csak az oldalmegtekintések nyomon követését követné, az pontatlan becslést adna arról, hogy hányan térnek vissza a játékba, miután korábban élvezték. Ahhoz, hogy tiszta képet kapj a visszatérő játékosokról, az alkalmazásnak egyéni eseményt kell küldenie, amikor a felhasználó ténylegesen játszik.  

Célszerű kódolni a kulcsfontosságú üzleti műveleteket képviselő egyéni eseményeket, és ezeket használni a megőrzési elemzéshez. A játék eredményének rögzítéséhez meg kell írnia egy kódsort, hogy egyéni eseményt küldjön az Application Insightsnak. Ha írsz, hogy a weboldal kódját, vagy Node.JS, úgy néz ki, mint ez:

```JavaScript
    appinsights.trackEvent("won game");
```

Vagy ASP.NET szerver kód:

```csharp
   telemetry.TrackEvent("won game");
```

[További információ az egyéni események írásáról.](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)


## <a name="next-steps"></a>További lépések
- A használati élmény engedélyezéséhez kezdje el elküldeni [az egyéni eseményeket](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) vagy [oldalnézeteket.](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views)
- Ha már küld egyéni eseményeket vagy oldalnézeteket, fedezze fel a Használati eszközöket, hogy megtudja, hogyan használják a felhasználók a szolgáltatást.
    - [Felhasználók, munkamenetek, események](usage-segmentation.md)
    - [Tölcsérek](usage-funnels.md)
    - [Felhasználói folyamatok](usage-flows.md)
    - [Munkafüzetek](../../azure-monitor/app/usage-workbooks.md)
    - [Felhasználói környezet hozzáadása](usage-send-user-context.md)


