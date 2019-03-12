---
title: Mi az Academic Knowledge API?
titlesuffix: Azure Cognitive Services
description: Az Academic Knowledge API segítségével értelmezhetők a felhasználói lekérdezések és részletes információk kérhetők le az Academic Graph-ból.
services: cognitive-services
author: darrine
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: overview
ms.date: 10/30/2018
ms.author: darrine
ms.openlocfilehash: b15ed5e2b31ed817d3f6558858e2b7285f98a70f
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57551721"
---
# <a name="academic-knowledge-api"></a>Academic Knowledge API

Üdvözli az Academic Knowledge API. Ez a szolgáltatás lehetővé teszi a tudományos jellegű felhasználói lekérdezések értelmezését, és részletes információkat kér le a Microsoft Academic Graph-ból (MAG). A MAG tudásbázisa egy webes heterogén entitásgrafikon, amely a tudományos tevékenységeket modellező entitásokból áll: tanulmányi terület, szerző, intézmény, tanulmány, helyszín és esemény. 

A MAG-adatok a Bing webes indexéből, valamint a Bing egy belső tudásbázisából származnak. A folyamatos Bing-indexelésnek köszönhetően az API friss webes információkat fog tartalmazni, miután azokat a Bing felderítette és indexelte. Ezen adatkészlet segítségével az Academic Knowledge API-k lehetővé teszik a tudásalapú, interaktív párbeszédet, amely zökkenőmentesen kombinálja a reaktív keresést a proaktív javaslati megoldásokkal, a részletes tanulmánykeresési gráfokkal, valamint a tanulmányok és a kapcsolódó entitások attribútumértékeinek hisztogramdisztribúcióival.

A Microsoft Academic Graphról itt talál további információt: [https://aka.ms/academicgraph](https://aka.ms/academicgraph).

Az Academic Knowledge API átkerült a Cognitive Services előzetes verziójából a Cognitive Services Labsbe. A projekt új kezdőlapja: [https://labs.cognitive.microsoft.com/en-us/project-academic-knowledge](https://labs.cognitive.microsoft.com/en-us/project-academic-knowledge). A meglévő API-kulcsok 2018. május 24-ig működnek. E dátumot követően hozzon létre egy új API-kulcsot. Vegye figyelembe, hogy a fizetett előzetes verzió a meglévő kulcs lejártát követően nem lesz elérhető. Lépjen kapcsolatba velünk, ha az API ingyenes szintje nem elegendő a céljai megvalósításához. 

## <a name="features"></a>Szolgáltatások
Az Academic Knowledge API négy egymáshoz kapcsolódó REST-végpontból áll:  
  1. **interpret** (értelmezés) – Értelmez egy természetes nyelven megfogalmazott felhasználói lekérdezési sztringet. Jegyzettel ellátott értelmezéseket ad vissza, amelyek lehetővé teszik a keresőmezők automatikus kiegészítését, előre jelezve, hogy a felhasználó mit fog írni.  
  2. **evaluate** (kiértékelés) – Kiértékel egy lekérdezési kifejezést és visszaadja az Academic Knowledge-entitásokkal kapcsolatos eredményeket.  
  3. **calchistogram** (hisztogramszámítás) – Kiszámítja egy keresőkifejezés által visszaadott tudományos entitások attribútumértékeinek eloszlási hisztogramját, pl. egy adott szerző idézettségének évenkénti eloszlását.  
  
Ezen API-metódusok együttes használatával részletes szemantikai keresési felületet hozhat létre. Egy megadott felhasználói lekérdezési sztring esetén az **interpret** metódus visszaadja a lekérdezés egy jegyzetekkel ellátott verzióját és egy strukturált lekérdezési kifejezést, miközben a mögöttes tudományos adatok szemantikája alapján opcionálisan kiegészíti a felhasználói lekérdezést. Például ha a felhasználó által beírt sztring a *látens s*, az **interpret** metódus képes megadni különböző értelmezések egy rangsorolt készletét, amelyek alapján a felhasználó kereshet a *látens szemantikai elemzés* területre, a *látens struktúraelemzés* tanulmányra, vagy bármely más entitáskifejezésre, amelynek kezdete a *látens s*. Ezen információk segítségével a felhasználó gyorsan eljuthat a kívánt keresési eredményekhez.

Az **evaluate** metódus segítségével egyező tanulmányentitások kérhetők le a tudományos tudásbázisból, a **calchistogram** metódussal pedig kiszámítható a tanulmányentitások attribútumértékeinek eloszlása, amely alapján tovább szűrhetők a keresési eredmények.        

## <a name="getting-started"></a>Első lépések 
Részletes dokumentációt a bal oldali altémakörökben talál.  Vegye figyelembe, hogy a példák olvashatóbbá tétele érdekében a REST API-hívások olyan karaktereket is tartalmaznak (például szóközöket), amelyek nem URL-kódolásúak.  Saját kódjának mindig a megfelelő URL-kódolásokat kell alkalmaznia.
