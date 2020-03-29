---
title: Elemzés hozzáadása a Bing Web Search API-hoz
titleSuffix: Azure Cognitive Services
description: A Bing Statistics elemzést nyújt a Bing Image Search API-hoz. Az Analytics tartalmazza a hívásmennyiséget, a legnépszerűbb lekérdezési karakterláncokat, a földrajzi eloszlást és egyebeket.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 5d1b0b19523eb37aa83aa59b24114be9f76ffa55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68882773"
---
# <a name="add-analytics-to-the-bing-search-apis"></a>Elemzés hozzáadása a Bing Keresési API-khoz

A Bing Statistics a Bing Keresési API-k elemzését biztosítja. Ezek az elemzések közé tartozik a hívásmennyiséget, a legfelső lekérdezési karakterláncokat, a földrajzi eloszlást és egyebeket. Az [Azure Portalon](https://ms.portal.azure.com) engedélyezheti a Bing-statisztikákat, ha az Azure-erőforrásra navigál, és a **Bing-statisztika engedélyezése**elemre kattint.

> [!IMPORTANT]
> * A Bing Statisztika nem érhető el ingyenes próba-előfizetések, vagy az ingyenes `F0` tarifacsomag erőforrásait.
> * A Bing Statisztika irányítópultján elérhető adatokat nem használhatja fel harmadik felek számára terjeszthető alkalmazások létrehozásához.
> * A Bing Statistics engedélyezése kissé megnöveli az előfizetési sebességet. A [részleteket](https://aka.ms/bingstatisticspricing) lásd az árakban.


Az alábbi képen az egyes Bing Search API-végpontok elérhető elemzései láthatók.

![Terjesztés végponttámogatási mátrix szerint](./media/bing-statistics/bing-statistics-matrix.png)

## <a name="access-your-analytics"></a>Az elemzések elérése

A Bing 24 óránként frissíti az elemzési adatokat, és akár 13 hónapos előzményeket is megőriz, amelyeket az [elemzési irányítópultról](https://bingapistatistics.com)érhet el. Győződjön meg arról, hogy ugyanazzal a Microsoft-fiókkal (MSA) jelentkezett be, mint amelyet a Bing-statisztika szolgáltatásra való regisztrációhoz használt.

> [!NOTE]  
> * A metrikák az irányítópulton való megjelenése akár 24 órát is igénybe vehet. Az irányítópult on the date and date the data was last updated.  
> * A mérőszámok attól az időponttól érhetők el, amikor engedélyezi a Bing statisztikái bővítményt.

## <a name="filter-the-data"></a>Az adatok szűrése

Alapértelmezés szerint a diagramok és grafikonok megjelenítik az összes olyan mutatót és adatot, amelyhez hozzáférése van. A diagramokon és grafikonokon látható adatok szűréséhez válassza ki az Önt érdeklő erőforrásokat, piacokat, végpontokat és jelentési időszakokat. A következő szűrőket módosíthatja:

- **Erőforrás-azonosító:** Az Azure-előfizetést azonosító egyedi erőforrás-azonosító. A lista több azonosítót tartalmaz, ha több Bing Search API-rétegre fizet elő. Alapértelmezés szerint az összes erőforrás ki van jelölve.  
  
- **Piacok**: Azok a piacok, ahonnan az eredmények származnak. Például en-us (angol, Egyesült Államok). Alapértelmezés szerint az összes piac ki van jelölve. A `en-WW` piac az a piac, amelyet a Bing akkor használ, ha a felhívás nem határoz meg piacot, és a Bing nem tudja meghatározni a felhasználó piacát.  
  
- **Végpontok**: A Bing Search API-végpontok. A lista tartalmazza az összes végpontot, amelyre fizetős előfizetéssel rendelkezik. Alapértelmezés szerint az összes végpont ki van jelölve.  

- **Időkeret**: A jelentési időszak. A következőket adhatja meg:
  - **Összes**: Legfeljebb 13 hónapnyi adatot tartalmaz  
  - **Elmúlt 24 óra**: Az elmúlt 24 óra analitikát tartalmazza  
  - **Múlt hét**: Az előző hét nap analitikát tartalmazza  
  - **Elmúlt hónap**: Az előző 30 nap analitikát tartalmazza  
  - **Egyéni dátumtartomány:** A megadott dátumtartományból származó elemzéseket tartalmazza, ha elérhető  

## <a name="charts-and-graphs"></a>Diagramok és grafikonok

Az irányítópult a kiválasztott végponthoz elérhető metrikák diagramjait és grafikonjait jeleníti meg. Nem minden metrika érhető el az összes végponthoz. Az egyes végpontok diagramjai és grafikonjai statikusak (nem jelölheti ki a megjelenítendő diagramokat és grafikonokat). Az irányítópult csak azokat a diagramokat és grafikonokat jeleníti meg, amelyekhez adatok vannak.

<!--
For example, if you don't include the User-Agent header in your calls, the dashboard will not include device-related graphs.
-->

A következőkben lehetséges metrikák és végpontkorlátozások.

- **Hívásmennyisége**: A jelentéskészítési időszak alatt kezdeményezett hívások számát jeleníti meg. Ha a jelentési időszak egy napra szól, a diagram az óránkénti hívások számát mutatja. Ellenkező esetben a diagram a jelentési időszak naponta kezdeményezett hívásainak számát mutatja.  
  
  > [!NOTE]
  > A hívás mennyisége eltérhet a számlázási jelentésektől, amelyek általában csak a sikeres hívásokat tartalmazzák.

- **Leggyakoribb lekérdezések:** A leggyakoribb lekérdezéseket és az egyes lekérdezések előfordulásainak számát jeleníti meg a jelentéskészítési időszakban. Beállíthatja a megjelenített lekérdezések számát. Megjelenítheti például a 25, 50 vagy 75 legjobb lekérdezést. A leggyakoribb lekérdezések nem érhetők el a következő végpontokhoz:  

  - /images/trending
  - /images/details
  - /images/visualsearch
  - /videók/felkapott
  - /videos/részletek
  - /hírek
  - /hírek/trendingtopics
  - /javaslatok  
  
  > [!NOTE]  
  > Egyes lekérdezési kifejezések et el lehet nyomva a bizalmas információk, például e-mailek, telefonszámok, SSN stb.

- **Földrajzi eloszlás**: Azok a piacok, ahonnan a keresési eredmények származnak. Például `en-us` (angol, Amerikai Egyesült Államok). A Bing `mkt` a lekérdezési paraméter segítségével határozza meg a piacot, ha meg van adva. Ellenkező esetben a Bing jeleket használ, például a hívó IP-címét a piac meghatározásához.

- **Válaszkód elosztása**: A jelentési időszak alatt az összes hívás HTTP-állapotkódja.

- **Hívás origin disztribúció**: A felhasználók által használt böngészőtípusok. Például microsoft edge, Chrome, Safari és FireFox. A böngészőn kívülről kezdeményezett hívások (például botok, postás vagy konzolalkalmazásból származó curl használata) a Könyvtárak csoportba vannak csoportosítva. A forrás meghatározása a kérelem User-Agent fejlécértékével történik. Ha a kérelem nem tartalmazza a User-Agent fejlécet, a Bing megpróbálja levezetni az eredetet más jelekből.  

- **Biztonságos keresési eloszlás**: A biztonságos keresési értékek eloszlása. Például, ki, mérsékelt, vagy szigorú. A `safeSearch` lekérdezési paraméter tartalmazza az értéket, ha meg van adva. Ellenkező esetben a Bing alapértelmezés szerint moderálja az értéket.  

- **Válaszok kért terjesztési:** A webes keresési API-válaszok, amit kért a `responseFilter` lekérdezési paraméter.  

- **Válaszok terjesztési:** A válaszokat, hogy a Webes keresési API-t a válaszban.

- **Válaszkiszolgáló-elosztás:** Az API-kérelmeket kiszolgáló alkalmazáskiszolgáló. A lehetséges értékek Bing.com (az asztali és laptopeszközökről kiszolgált forgalom esetében) és Bing.com mobil (mobileszközökről kiszolgált forgalom esetén). A kiszolgáló meghatározása a kérelem User-Agent fejlécértékén történik. Ha a kérelem nem tartalmazza a User-Agent fejlécet, a Bing megpróbálja levezetni a kiszolgálót más jelekből.

## <a name="next-steps"></a>További lépések

* [Mik azok a Bing Search API-k?](bing-api-comparison.md)
* [Bing Search API – követelmények használata és megjelenítése](use-display-requirements.md)
