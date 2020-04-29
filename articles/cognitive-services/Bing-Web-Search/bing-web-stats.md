---
title: Elemzés hozzáadása a Bing Web Search APIhoz
titleSuffix: Azure Cognitive Services
description: A Bing Statistics a Bing Image Search API elemzését teszi lehetővé. Az elemzésbe beletartozik a hívás mennyisége, a legnépszerűbb lekérdezési karakterláncok, a földrajzi eloszlás és sok más.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "68882773"
---
# <a name="add-analytics-to-the-bing-search-apis"></a>Elemzés hozzáadása a Bing Search API-khoz

A Bing Statistics a Bing Search API-k elemzését biztosítja. Ezek az elemzések közé tartozik a hívás mennyisége, a legnépszerűbb lekérdezési karakterláncok, a földrajzi eloszlás és sok más. A Bing-statisztikákat a [Azure Portalban](https://ms.portal.azure.com) engedélyezheti az Azure-erőforráshoz való navigálás és a **Bing-statisztika engedélyezése**lehetőségre kattintva.

> [!IMPORTANT]
> * A Bing-statisztika nem érhető el ingyenes próbaverziós előfizetéssel vagy az `F0` ingyenes díjszabási szinten található erőforrásokkal.
> * A Bing statisztikai irányítópulton keresztül nem használhat olyan adatokat, amelyek harmadik felek számára történő terjesztésre szolgáló alkalmazásokat hoznak létre.
> * A Bing-statisztika engedélyezése némileg növeli az előfizetések arányát. A részletekért tekintse meg a [díjszabást](https://aka.ms/bingstatisticspricing) .


Az alábbi képen az egyes Bing Search API-végpontok elérhető elemzései láthatók.

![Eloszlás végpont-támogatási mátrix alapján](./media/bing-statistics/bing-statistics-matrix.png)

## <a name="access-your-analytics"></a>Az elemzés elérése

A Bing 24 óránként frissíti az elemzési adatok mennyiségét, és akár 13 hónapig is megőrzi az [elemzések irányítópultján](https://bingapistatistics.com)elérhető előzményeket. Győződjön meg arról, hogy ugyanazokkal a Microsoft-fiókokkal (MSA) jelentkezett be, mint a Bing-statisztikák regisztrálásához.

> [!NOTE]  
> * Akár 24 óráig is eltarthat, amíg a metrikák felszínre kerülnek az irányítópulton. Az irányítópult az adatok utolsó frissítésének dátumát és időpontját jeleníti meg.  
> * A metrikák a Bing statisztikai bővítmény engedélyezésének időpontjában érhetők el.

## <a name="filter-the-data"></a>Az Adatszűrés

Alapértelmezés szerint a diagramok és a diagramok a hozzájuk tartozó összes mérőszámot és adatokat megjelenítik. A diagramokban és diagramokban megjelenő információk szűréséhez válassza ki azokat az erőforrásokat, piacokat, végpontokat és jelentési időszakot, amelyekre kíváncsi. A következő szűrők módosíthatók:

- **Erőforrás-azonosító**: az Azure-előfizetést azonosító egyedi erőforrás-azonosító. Ha egynél több Bing Search API-rétegre fizet elő, a lista több azonosítót is tartalmaz. Alapértelmezés szerint az összes erőforrás ki van választva.  
  
- **Piacok**: azok a piacok, amelyeken az eredmények származnak. Például: en-us (angol, Egyesült Államok). Alapértelmezés szerint minden piac ki van választva. A `en-WW` piac az a piac, amelyet a Bing akkor használ, ha a hívás nem ad meg piacot, és a Bing nem tudja meghatározni a felhasználó piacát.  
  
- **Végpontok**: a Bing Search API-végpontok. A lista tartalmazza az összes olyan végpontot, amelyhez fizetős előfizetéssel rendelkezik. Alapértelmezés szerint az összes végpont ki van választva.  

- **Időkeret**: a jelentési időszak. A következőket adhatja meg:
  - **Összes**: legfeljebb 13 hónapig hasznos adatot tartalmaz  
  - Az **elmúlt 24 óra**: az elmúlt 24 órában elemzést tartalmaz  
  - **Múlt hét**: az elmúlt hét nap elemzését tartalmazza  
  - Az **elmúlt hónapban**: az elmúlt 30 nap elemzését tartalmazza  
  - **Egyéni dátumtartomány**: a megadott dátumtartomány elemzését tartalmazza, ha van ilyen.  

## <a name="charts-and-graphs"></a>Diagramok és diagramok

Az irányítópulton a kiválasztott végponthoz elérhető metrikák diagramjai és diagramjai láthatók. Nem minden metrika érhető el az összes végponthoz. Az egyes végpontok diagramjai és diagramjai statikusak (nem lehet kijelölni a megjelenítendő diagramokat és diagramokat). Az irányítópult csak azokat a diagramokat és diagramokat jeleníti meg, amelyekhez az adathalmazok vannak.

<!--
For example, if you don't include the User-Agent header in your calls, the dashboard will not include device-related graphs.
-->

A következő lehetséges mérőszámok és végponti korlátozások.

- **Hívási kötet**: a jelentési időszak során végrehajtott hívások számát jeleníti meg. Ha a jelentési időszak egy napra esik, a diagramon az óránkénti hívások száma látható. Ellenkező esetben a diagram a jelentési időszakon belül naponta végrehajtott hívások számát jeleníti meg.  
  
  > [!NOTE]
  > A hívási kötet eltérhet a számlázási jelentéstől, amely általában csak a sikeres hívásokat tartalmazza.

- **Leggyakoribb lekérdezések**: a jelentéskészítési időszakban a leggyakoribb lekérdezéseket és az egyes lekérdezések előfordulásainak számát jeleníti meg. Beállíthatja a megjelenített lekérdezések számát. Megjelenítheti például az első 25, 50 vagy 75 lekérdezéseket. A leggyakoribb lekérdezések a következő végpontokhoz nem érhetők el:  

  - /images/trending
  - /images/details
  - /images/visualsearch
  - /videos/trending
  - /videos/details
  - /news
  - /news/trendingtopics
  - /suggestions  
  
  > [!NOTE]  
  > Bizonyos lekérdezési feltételek elhagyhatók a bizalmas információk, például az e-mailek, a telefonszámok, az SSN stb. eltávolításához.

- **Földrajzi eloszlás**: azok a piacok, ahol a keresési eredmények származnak. Például: `en-us` (angol, Egyesült Államok). A Bing a `mkt` lekérdezési paraméter használatával határozza meg a piacot, ha meg van adva. Ellenkező esetben a Bing olyan jeleket használ, mint például a hívó IP-címe a piac meghatározásához.

- **Válasz kód eloszlása**: az összes hívás HTTP-állapotkódok a jelentési időszak alatt.

- **Forrás eloszlásának hívása**: a felhasználók által használt böngészők típusai. Például a Microsoft Edge, a Chrome, a Safari és a FireFox. A böngészőn kívüli hívások (például a robotok, a Poster vagy a curl használata a konzolból) a könyvtárak alatt vannak csoportosítva. A forrás meghatározása a kérelem felhasználói ügynök fejlécének értéke alapján történik. Ha a kérelem nem tartalmazza a felhasználói ügynök fejlécét, a Bing megpróbálja származtatni a forrást más jelekből.  

- **Biztonságos Keresés eloszlása**: a biztonságos keresési értékek eloszlása. Például: off, mérsékelt vagy szigorú. Ha `safeSearch` meg van adva, a lekérdezési paraméter tartalmazza az értéket. Ellenkező esetben a Bing a mérsékelt értékre írja az értéket.  

- **Válasz kért eloszlás**: a `responseFilter` lekérdezési paraméterben kért Web Search API-válaszok.  

- **Válaszok visszaadása**: a válaszban Web Search API által visszaadott válaszok.

- **Válasz-kiszolgáló eloszlása**: az API-kérelmeket kézbesítő alkalmazáskiszolgáló. A lehetséges értékek a Bing.com (az asztali és hordozható eszközökről kiszolgált forgalom) és a Bing.com (a mobileszközök által kiszolgált forgalom esetében). A kiszolgáló a kérelem felhasználói ügynök fejlécének értéke alapján van meghatározva. Ha a kérelem nem tartalmazza a felhasználói ügynök fejlécét, a Bing megpróbálja a kiszolgálót más jelekből származtatni.

## <a name="next-steps"></a>További lépések

* [Mi a Bing Search API-k?](bing-api-comparison.md)
* [Bing Search API – követelmények használata és megjelenítése](use-display-requirements.md)
