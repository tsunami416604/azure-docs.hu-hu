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
ms.openlocfilehash: 4e19c1afefdc5bcacebcb0d495193b48c7a6d724
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313710"
---
Bing statisztika szerinti elemzést biztosít a Bing keresési API-k esetében. Elemzés hívásainak mennyiségére, felső lekérdezési karakterláncok, földrajzi eloszlása és több tartalmazza. Ahhoz, hogy a Bing statisztikákat a Bing keresési-előfizetéssel, navigáljon a [Azure irányítópult](https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7), válassza ki az előfizetés, majd kattintson a Bing statisztika engedélyezése. Bing statisztika engedélyezése némileg növeli az előfizetés arány (lásd: [árképzési](https://aka.ms/bingstatisticspricing)).


> [!NOTE]
> Bing statisztikák csak előfizetéses érhető el – az ingyenes próba-előfizetések nem érhető el. 

> [!NOTE]
> A Bing statisztika irányítópulton keresztül elérhető adatok nem használhatók a terjesztéshez, harmadik felekhez alkalmazásokat hozhat létre.

Bing analitikai adatok 24 óránként frissíti, és 13 hónap alatt érkezett előzmények másolatot tart fenn.

## <a name="accessing-your-analytics"></a>Az elemzés elérése

Az elemzések irányítópultján eléréséhez lépjen https://bingapistatistics.com. Győződjön meg arról, hogy be van jelentkezve a Microsoft-fiók (msa-t) az előfizetés beszerzéséhez használt használatával.


## <a name="filtering-the-data"></a>Az adatok szűrése

Alapértelmezés szerint a diagramok és diagramokat összes metrikák gyűjtött adatokat tükrözik, amely rendelkezik hozzáféréssel. Szűrheti a diagramok és diagramokat az erőforrásokat, a piacon, a végpontok kiválasztásával látható adatok, és a jelentési időszak kíváncsiak vagyunk. A diagramok és diagramokat az alkalmazott szűrőket megfelelően változik. Az alábbiakban ismertetett szűrők módosíthatja.

- **Erőforrás-azonosító**: az egyedi erőforrás-azonosítója, amely azonosítja az Azure-előfizetéshez. A listában több azonosítót tartalmaz, ha egynél több Bing keresési API-réteghez előfizetése. Alapértelmezés szerint minden erőforrás van kiválasztva.  
  
- **Piacok**: A piacon honnan származnak az eredményeket. Például en-us (angol nyelvű, az Amerikai Egyesült Államok). Alapértelmezés szerint minden piacok ki van jelölve. Ne feledje, hogy a en-hh-piaci a Bing használja, ha a hívás nem adja meg a piaci és a Bing piacán nem tudja meghatározni a felhasználó piaci.  
  
- **Végpontok**: A Bing keresési API-végpontokon. A lista összes végponton, amelyekhez rendelkezik a szolgáltatás fizetős tartalmazza. Alapértelmezés szerint végpontjai vannak jelölve.  

- **Időkereten belül**: A jelentési időszak. Adhat meg:  
  
  - Minden&mdash;13 hónap értékelésével másolatot tartalmaz  
  - Elmúlt 24 órában&mdash;magában foglalja az elemzés az elmúlt 24 órában  
  - Elmúlt hét&mdash;tartalmazza az előző hét nap elemzés  
  - Elmúlt hónap&mdash;magában foglalja az elmúlt 30 nap elemzés  
  - Egyéni dátumtartomány&mdash;adott dátumtartományon belül a analytics tartalmazza, ha elérhető  
  
  > [!NOTE]  
  > Az irányítópult illesztésének metrikáihoz akár 24 óráig is eltarthat. Az irányítópult jeleníti meg, a dátum és idő, az adatok utolsó frissítése.  
  
  > [!NOTE]  
  > Metrikák érhetők el a Bing statisztika a bővítmény engedélyezése óta. 


## <a name="charts-and-graphs"></a>Diagramok és diagramokat

Az irányítópulton látható diagramokat és a kijelölt végpont elérhető metrikák grafikonokat. Nem minden metrikák végpontjai érhetők el. A diagramok és a végpontok diagramokat statikusak (nem választhat a diagramok és diagramok megjelenítéséhez). Az irányítópult jeleníti meg, csak a diagramok és diagramokat, amelynek nincs adat. 

<!--
For example, if you don't include the User-Agent header in your calls, the dashboard will not include device-related graphs.
-->

Az alábbiakban a lehetséges metrikákat. Minden egyes metrika megjegyzések végpont korlátozások. 

- **Kötet hívás**: a jelentési időszak során felé indított hívások számát jeleníti meg. Ha a jelentési időszak egy napon, a diagramot óránkénti felé indított hívások számát jeleníti meg. Ellenkező esetben a diagram megjeleníti a jelentési időszak naponta felé indított hívások száma.  
  
  > [!NOTE]
  > A hívások száma eltérhet a számlázási jelentések, mert általában csak a sikeres hívás tartalmazza. 
  
-  **Leggyakoribb lekérdezések**: a leggyakoribb lekérdezések és a lekérdezés minden előfordulását mutatja a jelentési időszak során. Beállíthatja, hogy látható lekérdezések száma. Például megjelenítheti a felső 25, 50 vagy 75 lekérdezések. Leggyakoribb lekérdezések a következő végpontok esetén nem érhető el:  
  
  - /Images/trending
  - / képek/részletei
  - / képek/visualsearch
  - /Videos/trending
  - / videók/részletei
  - /News
  - / hírek/trendingtopics
  - /Suggestions  
  
  > [!NOTE]  
  > Előfordulhat, hogy letiltja, néhány lekérdezési kifejezések e-mailek, telefonszámokat, SSN, például bizalmas információk eltávolításához stb.  

- **Földrajzi eloszlása**: A piacon honnan származnak az eredményeket. Például en-us (angol nyelvű, az Amerikai Egyesült Államok). Bing használja a `mkt` lekérdezésparaméter a piacon meghatározni, ha meg van adva. Ellenkező esetben a Bing jelek, például a hívó IP-cím alapján határozza meg a piacon.  
  
- **Válasz kód terjesztési**: A HTTP-állapotkódok összes hívások a jelentési időszak során.  
  
- **Eredeti terjesztési hívás**: a felhasználók által használt böngészőket típusú. Például él, Chrome, Safari és FireFox. Hívások egy böngészőbe, például botok, Postman vagy konzolalkalmazásból, curl használatával kívül a szalagtárak szerint vannak csoportosítva. A forrás használata a kérelem felhasználói ügynök fejléc értéke határozza meg. Ha a kérelem nem tartalmazza a felhasználói ügynök fejléc, a Bing megpróbálja eredeti származik egyéb jelek.  
  
- **Biztonságos keresési terjesztési**: A terjesztési biztonságos keresési értékek. Például ki, közepes vagy szigorú. A `safeSearch` lekérdezési paraméter értéke, ha meg van adva. Ellenkező esetben a Bing mérsékelt értéke alapértelmezés szerint.  
  
- **Felveszi a kért terjesztési**: A webes keresés API ad választ, akkor a kért a `responseFilter` lekérdezési paraméter.  
  
- **Visszaadott terjesztési megválaszolja**: A webes keresés API a visszatérő válaszban válaszokat.  
  
- **Válasz kiszolgáló terjesztési**: A kiszolgáló, amely az API-kérelmek és kiszolgálása között. A lehetséges értékek: Bing.com (az asztali és hordozható eszközök származó forgalmat) és Bing.com mobil (a-forgalmat a mobileszközökről és kiszolgálása között). A kiszolgáló a kérés felhasználói ügynök állomásfejléc-érték használatával határozza meg. A kérelem nem tartalmazza a felhasználói ügynök fejléc, a Bing megpróbálja a kiszolgáló származik egyéb jeleket.  
  


Az alábbiakban látható a analytics elérhető végpontok.

![Terjesztési végpont-támogatási mátrix](./media/cognitive-services-bing-statistics/bing-statistics-matrix.PNG)


