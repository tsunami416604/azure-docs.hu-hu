---
title: Az Azure Database for postgresql-hez a Query Store használati forgatókönyvek
description: A cikk néhány olyan forgatókönyvet a Query Store az Azure Database for PostgreSQL-hez.
services: postgresql
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 5da10faca653d0eddb50568165eb9d7ad1f877e4
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46950420"
---
# <a name="usage-scenarios-for-query-store"></a>A Query Store használati forgatókönyvek

**A következőkre vonatkozik:** Azure Database for PostgreSQL 9.6 és 10

> [!IMPORTANT]
> A Query Store szolgáltatás jelenleg nyilvános előzetes verzióban.

Számos olyan forgatókönyvekben, ahol nyomkövetési és karbantartása kiszámítható számítási feladatokra teljesítmény kritikus fontosságú a Query Store is használhatja. Vegye figyelembe az alábbi példák: 
- Azonosító és a leggyakoribb drága lekérdezések hangolása 
- A / B tesztelés 
- Frissítések során gondoskodik a teljesítmény stabil 
- Azonosító és az ad-hoc számítási feladatok javítása 

## <a name="identify-and-tune-expensive-queries"></a>Azonosíthatja és drága Lekérdezések finomhangolása 

### <a name="identify-longest-running-queries"></a>Leghosszabb futó lekérdezések azonosítása 
Használja a [lekérdezési Terheléselemző](concepts-query-performance-insight.md) megtekintése az Azure Portalon gyorsan azonosíthatja a leghosszabb futó lekérdezések. Ezek a lekérdezések általában általában használnak egy jelentős erőforrásokat. Leghosszabban futó kérdéseire optimalizálása a jobb teljesítmény érdekében a rendszeren futó más lekérdezések által használható erőforrások felszabadítása. 

### <a name="target-queries-with-performance-deltas"></a>Teljesítmény eltérések cél lekérdezéseket. 
Query Store a teljesítményadatok darabolja fel a windows indításakor, így nyomon követheti a lekérdezési teljesítmény az idő függvényében. Ennek segítségével azonosíthatja pontosan amely lekérdezések járulnak töltött teljes idő növekedéséhez. Ennek eredményeképpen teheti az alkalmazások és szolgáltatások célzott hibaelhárítás.

### <a name="tuning-expensive-queries"></a>Drága lekérdezések hangolása 
Ha azonosította a lekérdezés rosszabb teljesítménnyel, a végrehajtott művelet a probléma jellegétől függ: 
- Használat [teljesítménnyel kapcsolatos javaslatok](concepts-performance-recommendations.md) a javasolt indexek határozza meg, ha vannak ilyenek. Ha igen, hozza létre az indexet, és ezután a Query Store segítségével kiértékelheti az index létrehozása után a lekérdezési teljesítmény. 
- Győződjön meg arról, hogy naprakészek-e a statisztikák a lekérdezés által használt mögöttes táblákhoz.
- Fontolja meg a drága lekérdezések újraírását. Például a lekérdezés paraméterezésének előnyeit, és dinamikus SQL-felhasználás csökkentése. Optimális a logikát alkalmazzák, például a szűrést az adatbázisoldalon, nem az alkalmazás ügyféloldali adatok alkalmazása az adatok olvasásakor. 


## <a name="ab-testing"></a>A / B tesztelés 
Használja Query Store a számítási feladat teljesítményét előtt és után az alkalmazás megváltoztatására tervezi bevezetni. Számítási feladatok teljesítményére módosítása a környezetben vagy az alkalmazás hatásainak kiértékelését a Query Store használatára vonatkozó forgatókönyvek példái: 
- Egy alkalmazás új verzióját is hatályba lépnek. 
- További erőforrások hozzáadása a kiszolgálóhoz. 
- Hiányzó indexek létrehozása a drága lekérdezések által hivatkozott táblákban. 
 
Ezek az esetek bármelyikében alkalmazza az alábbi munkafolyamat: 
1. Futtassa a Query Store a számítási feladat a tervezett változtatás teljesítmény alapterv létrehozása előtt. 
2. Alkalmazás módosítás(ok) időben ellenőrzött jelenleg érvényes. 
3. Továbbra is a számítási feladatok elég hosszú futtatása a rendszer teljesítményének lemezkép létrehozásához a váltás után. 
4. Hasonlítsa össze az eredményeket az előtt, és a váltás után. 
5. Döntse el, a módosítás vagy visszaállítási megőrzi-e. 


## <a name="identify-and-improve-ad-hoc-workloads"></a>Azonosíthatja és javíthatja az ad-hoc számítási feladatokhoz 
Bizonyos munkaterhelések nem rendelkezik, amely a teljes alkalmazás teljesítményének javítása érdekében hangolhassa domináns lekérdezések. Számítási feladatok jellemzően viszonylag nagy számú egyedi lekérdezéseket, azok egy részét a rendszer-erőforrásokat használó jellemző. Minden egyedi hajtja végre a lekérdezést ritkán, így külön-külön futásidejű fogyasztásuk nem fontos. Másrészről tekintve, hogy az alkalmazás folyamatosan generáló új lekérdezéseket, rendszer erőforrások jelentős részét van költött lekérdezés fordítása, amely nem optimális. Általában ez a helyzet akkor történik, ha az alkalmazás lekérdezések (helyett a tárolt eljárások, vagy a paraméteres lekérdezések) hoz létre vagy objektumrelációs leképezés keretrendszerek, lekérdezések alapértelmezés szerint generáló támaszkodik. 
 
Ha az alkalmazás kódja felett, akkor fontolja meg az adatelérési réteg tárolt eljárásokat és paraméteres lekérdezések újraírását. Azonban ez a helyzet is javítható alkalmazás módosítása nélkül a teljes adatbázisról (az összes lekérdezés) vagy az egyéni lekérdezések sablonok lekérdezés kivonata a lekérdezés paraméterezésének kényszerítése. 

## <a name="next-steps"></a>További lépések
- Tudjon meg többet a [ajánlott eljárások a Query Store](concepts-query-store-best-practices.md)