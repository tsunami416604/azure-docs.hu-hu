---
title: A webalkalmazások felhasználói adatmegőrzési elemzése az Azure Application Insights | Microsoft docs
description: Hány felhasználó tért vissza az alkalmazásba?
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 05/03/2017
ms.reviewer: mbullwin
ms.openlocfilehash: 5f5f6235354adc565815ac2eab0a1c774267102d
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899424"
---
# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>A webalkalmazások felhasználói adatmegőrzési elemzése Application Insights

Az [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) megőrzési funkciója segítségével elemezheti, hogy hány felhasználó tér vissza az alkalmazásba, és hogy milyen gyakran hajtanak végre bizonyos feladatokat, vagy hogyan érik el a célokat. Ha például egy játék helyét futtat, összehasonlíthatja azokat a felhasználókat, akik a nyeremény után visszatérnek a webhelyhez. Ez a tudás segíthet a felhasználói élmény és az üzleti stratégia tökéletesítésében.

## <a name="get-started"></a>Az első lépések

Ha még nem látja az adatokat a Application Insights-portál megőrzési eszközében, [Ismerkedjen meg a használati eszközök használatába](usage-overview.md).

## <a name="the-retention-tool"></a>Az adatmegőrzési eszköz

![Megtartási eszköz](./media/usage-retention/retention.png)

1. Az eszköztár lehetővé teszi a felhasználók számára új adatmegőrzési jelentések létrehozását, a meglévő adatmegőrzési jelentések megnyitását, a jelenlegi adatmegőrzési jelentés mentését vagy mentését, a mentett jelentésekben tárolt adatok módosítását, a jelentés adatfrissítését, a jelentés megosztását e-mailben vagy közvetlen hivatkozással, valamint a dokumentáció oldalala. 
2. Alapértelmezés szerint az adatmegőrzés megjeleníti az összes olyan felhasználót, aki bármit is visszakapott, és egy adott időszakon belül bármi mást tett. Az események különböző kombinációjának kiválasztásával szűkítheti a fókuszt adott felhasználói tevékenységekre.
3. Adjon hozzá egy vagy több szűrőt a tulajdonságokhoz. Például egy adott országban vagy régióban lévő felhasználókra lehet összpontosítani. A szűrők beállítása után kattintson a **frissítés** gombra. 
4. A teljes adatmegőrzési diagram a felhasználó adatmegőrzésének összegzését jeleníti meg a kiválasztott időszakban. 
5. A rács a #2 lekérdezés-szerkesztője szerint megőrzött felhasználók számát jeleníti meg. Minden sor olyan felhasználók kohorszát jelöli, akik a megjelenített időszakban bármilyen eseményt hajtottak végre. A sor minden cellája azt mutatja, hogy egy adott kohorsz közül hányan tért vissza legalább egyszer egy későbbi időszakban. Egyes felhasználók több időszakot is visszatérhetnek. 
6. Az adatelemzési kártyák az első öt kezdeményező eseményt és öt visszatérő eseményt mutatják, hogy a felhasználók könnyebben megértsék az adatmegőrzési jelentéseket. 

![Megőrzött egér hover](./media/usage-retention/hover.png)

A felhasználók az adatmegőrzési eszközön lévő cellák fölé helyezhetik az elemzés gombot, és az eszközhöz tartozó tippek elmagyarázzák, hogy mit jelent a cella. Az elemzés gomb a felhasználókat az elemzési eszközre egy előre feltöltött lekérdezéssel hozza a felhasználók számára a cellából. 

## <a name="use-business-events-to-track-retention"></a>Üzleti események használata a megőrzés nyomon követéséhez

A leghasznosabb adatmegőrzési elemzés érdekében mérje fel a jelentős üzleti tevékenységeket jelölő eseményeket. 

Előfordulhat például, hogy számos felhasználó megnyithat egy oldalt az alkalmazásban, és nem játssza le a megjelenített játékot. Az oldal nézeteinek nyomon követése ezért pontatlan becslést ad arról, hogy hányan térnek vissza a játékra, miután a szolgáltatás már korábban is elvégezte a lejátszást. Ha tiszta képet szeretne kapni a játékosok visszaküldéséről, az alkalmazásnak egyéni eseményt kell küldenie, amikor egy felhasználó ténylegesen játszik.  

Célszerű a kulcsfontosságú üzleti műveleteket jelképező egyéni eseményeket felvenni, és ezeket az adatmegőrzési elemzéshez használni. A játék eredményének rögzítéséhez meg kell írnia egy kódrészletet, hogy egy egyéni eseményt küldjön Application Insights. Ha a weblap kódjában vagy a Node. JS-ben írja, a következőképpen néz ki:

```JavaScript
    appinsights.trackEvent("won game");
```

Vagy a ASP.NET-kiszolgáló kódjában:

```csharp
   telemetry.TrackEvent("won game");
```

[További információ az egyéni események írásához](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).


## <a name="next-steps"></a>Következő lépések
- A használati tapasztalatok engedélyezéséhez kezdjen el [Egyéni eseményeket](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) vagy [oldalletöltések](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views)küldését.
- Ha már elküldte az egyéni eseményeket vagy a lapok nézeteit, tekintse meg a használati eszközöket, amelyekkel megismerheti, hogy a felhasználók miként használják a szolgáltatást.
    - [Felhasználók, munkamenetek, események](usage-segmentation.md)
    - [Tölcsérek](usage-funnels.md)
    - [Felhasználói folyamatok](usage-flows.md)
    - [Munkafüzetek](../../azure-monitor/app/usage-workbooks.md)
    - [Felhasználói környezet hozzáadása](usage-send-user-context.md)


