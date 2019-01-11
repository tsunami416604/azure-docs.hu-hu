---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: scottwhi-msft
ms.service: cognitive-services
ms.topic: include
ms.date: 04/09/2018
ms.author: scottwhi
ms.custom: include file
ms.openlocfilehash: 6016b13fe7d3e1f3b673bd2446d2f68b04878cd6
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54193397"
---
Statisztikai Bing analytics Bing keresési API-kat biztosít. Analytics biztosít a hívások mennyisége, felső lekérdezési karakterláncokat, földrajzi eloszlása és egyéb. A Bing Search-előfizetéssel a statisztikai Bing engedélyezéséhez lépjen a [Azure irányítópultján](https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7), válassza ki a fizetett előfizetését, majd kattintson a statisztikai Bing engedélyezése. Statisztikai Bing engedélyezése kissé növeli a feliratkozási arány (lásd: [díjszabás](https://aka.ms/bingstatisticspricing)).

> [!NOTE]
> Csak a fizetett előfizetések érhető el a statisztikai Bing - együtt ingyenes próba-előfizetések nem érhető el.

> [!NOTE]
> A statisztikai Bing-irányítópulton keresztül elérhető adatok nem használhatók harmadik felek terjesztés alkalmazásokat hozhat létre.

A Bing analytics-adatok 24 óránként frissíti, és 13 hónap alatt az előzmények másolatot tart fenn.

## <a name="accessing-your-analytics"></a>Analytics-elérése

Az analytics-irányítópult eléréséhez, https://bingapistatistics.com. Ellenőrizze, hogy be van jelentkezve a Microsoft-fiók (MSA) az előfizetés beszerzéséhez használt használatával.

## <a name="filtering-the-data"></a>Az adatok szűrése

Alapértelmezés szerint a diagramokat és ábrákat tükrözik, amely hozzáfér az összes mérőszámadatokat. Szűrheti az erőforrásokat, a piacon, a végpontok kiválasztásával a diagramokat és ábrákat megjelenő adatokat, és a jelentési időszak érdekli. A diagramokat és ábrákat módosítsa megfelelően a szűrőket, akkor a alkalmazni. Az alábbiakban ismertetett, előfordulhat, hogy módosítja a szűrőket.

- **Erőforrás-azonosító**: Az egyedi erőforrás-azonosító, amely azonosítja az Azure-előfizetésében. A lista tartalmazza a több ID, ha egynél több keresési Bing-API-csomagra fizetett elő. Alapértelmezés szerint az összes erőforrás ki van jelölve.  
  
- **Piacok**: A piacon, honnan származnak az eredményeket. Ha például en-us (angol nyelven, Egyesült Államok). Alapértelmezés szerint minden olyan piacon ki van jelölve. Vegye figyelembe, hogy az en-WW piaci a piacon, amely a Bing használja, ha a hívás nem adja meg a piacon, és a Bing nem tudja meghatározni a felhasználó piacon.  
  
- **Végpontok**: A Bing keresési API-végpontokat. A lista tartalmazza az összes végpontot, amelyhez fizetős előfizetéssel rendelkezik. Alapértelmezés szerint minden végpontok ki van jelölve.  

- **Időkeret**: A jelentési időszak. Adhatja meg:
  - Az összes&mdash;szolgáltatás való 13 hónap adatmintát  
  - Elmúlt 24 óra&mdash;tartalmazza az elmúlt 24 órában végzett elemzések  
  - Elmúlt hét&mdash;tartalmazza az elmúlt hét napban végzett elemzések  
  - Előző hónap&mdash;tartalmazza az előző 30 nap analytics  
  - Egy egyéni dátumtartományt&mdash;magában foglalja a megadott dátumtartományban végzett elemzések, ha elérhető  

  > [!NOTE]  
  > Metrikákért a irányítópultján illesztésének akár 24 óráig is eltarthat. Az irányítópult megjeleníti a dátum és idő, az adatok utolsó frissítése.  

  > [!NOTE]  
  > Metrikák érhetők el a alkalommal, amikor engedélyezi a statisztikai Bing-bővítményben.

## <a name="charts-and-graphs"></a>Diagramok és grafikonok

Az irányítópult megjeleníti, diagramokat és ábrákat a kiválasztott végpont elérhető metrikák. Minden végpontok nem minden metrikák érhetők el. A diagramokat és ábrákat végpontok statikusak (nem választhat a diagramokat és ábrákat megjelenítéséhez). Az irányítópult megjeleníti, csak a diagramok és grafikonok, amelynek nincs adat.

<!--
For example, if you don't include the User-Agent header in your calls, the dashboard will not include device-related graphs.
-->

Az alábbi táblázat a lehetséges metrikákat. Minden egyes metrika megjegyzések végpont korlátozások.

- **Hívja a kötet**: A jelentési időszak során indított hívások számát jeleníti meg. Ha a jelentési időszak egy napnál, a diagramot óránkénti indított hívások számát jeleníti meg. Ellenkező esetben a diagram mutatja a jelentési időszak naponta végzett hívások száma.  
  
  > [!NOTE]
  > A hívások mennyisége eltérhet a számlázási jelentéseket, amelyek általában csak a sikeres hívások tartalmazza.

- **Leggyakoribb lekérdezések**: A leggyakoribb lekérdezések, és minden egyes lekérdezés előfordulási számát mutatja a jelentési időszak során. Konfigurálhatja a látható lekérdezések száma. Például megjelenítheti a 25-ös, 50 vagy 75 felső lekérdezéseket. Leggyakoribb lekérdezések nem érhető el a következő végpontoknál:  

  - /Images/trending
  - / képek/részletei
  - / képek/visualsearch
  - /Videos/trending
  - / videók vagy azonos adatokkal
  - /News
  - / news/trendingtopics
  - /Suggestions  
  
  > [!NOTE]  
  > Bizonyos lekérdezési kifejezések letiltja, előfordulhat, hogy távolítsa el az e-mailt, telefonszámok, SSN, például bizalmas információk stb.

- **Földrajzi elosztás**: A piacon, honnan származnak az eredményeket. Ha például en-us (angol nyelven, Egyesült Államok). A Bing a `mkt` lekérdezési paraméter meghatározásához a piacon, ha meg van adva. Ellenkező esetben a Bing jelek, például a hívó IP-cím alapján határozza meg a piacon.

- **Válaszkódok eloszlása**: A HTTP-állapotkódok minden hívás a jelentési időszak során.

- **Forrás terjesztési hívás**: A felhasználók által használt böngésző típusú. Például a Microsoft Edge, Chrome, a Safari és a FireFox. Egy böngészőbe, például a robotok, a Postman és a egy konzolalkalmazást a curl használatával kívülre indított hívások kódtárak szerint vannak csoportosítva. A forrás használatával a kérelem felhasználói ügynök fejléc értéke határozza meg. Ha a kérelem nem tartalmazza a felhasználói ügynök fejlécet, a Bing próbálja meg más jelekkel származtassa. a forrás.  

- **Biztonságos keresés terjesztési**: Biztonságos keresés értékek eloszlását. Például azt kikapcsolva, mérsékelt, vagy a szigorú. A `safeSearch` lekérdezési paraméter értéke, ha meg van adva. Ellenkező esetben a Bing alapértelmezés szerint a közepes érték.  

- **Válaszok a kért terjesztési**: A Web Search API, a kért választ ad a `responseFilter` lekérdezési paraméter.  

- **Választ adott vissza terjesztési**: A Web Search API a válaszban visszaadott válaszokat.

- **Válasz kiszolgáló terjesztési**: A kiszolgáló, amely az API-kérelmek szolgálja ki. A lehetséges értékek: Bing.com (az asztali és hordozható eszközök által szolgáltatott forgalom) és a Bing.com mobil (a mobileszközök által szolgáltatott forgalom). A kiszolgáló a kérelem felhasználói ügynök fejléc értéke használatával határozza meg. Ha a kérelem nem tartalmazza a felhasználói ügynök fejlécet, a Bing próbálja meg más jelekkel származtassa. a kiszolgáló.

Az alábbiakban látható a elemzési minden végpont elérhető.

![Terjesztési végpont támogatási mátrix](./media/cognitive-services-bing-statistics/bing-statistics-matrix.PNG)
