---
title: Query Store-forgatókönyvek – Azure-adatbázis a PostgreSQL-hez – Egyetlen kiszolgáló
description: Ez a cikk a PostgreSQL - Single Server Azure Database Query Store lekérdezési tárolójának néhány forgatókönyvét ismerteti.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 31e3f82b6ea1b1fc15c0832dc03edce2a59f1e1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768350"
---
# <a name="usage-scenarios-for-query-store"></a>A Query Store használati forgatókönyvei

**A következőkre vonatkozik:** Azure Database for PostgreSQL – Egykiszolgálós verzió9.6, 10, 11

A Query Store számos olyan forgatókönyvben használható, ahol a számítási feladatok előrekövetése és fenntartása kritikus fontosságú. Tekintse meg a következő példákat: 
- A legdrágább lekérdezések azonosítása és finomhangolása 
- A/B vizsgálat 
- A teljesítmény stabilan tartása a frissítések során 
- Az ad hoc munkaterhelések azonosítása és javítása 

## <a name="identify-and-tune-expensive-queries"></a>Költséges lekérdezések azonosítása és finomhangolása 

### <a name="identify-longest-running-queries"></a>A leghosszabb ideig futó lekérdezések azonosítása 
A [Lekérdezési teljesítmény insight](concepts-query-performance-insight.md) nézet az Azure Portalon gyorsan azonosítani a leghosszabb ideig futó lekérdezések. Ezek a lekérdezések általában általában jelentős mennyiségű erőforrást használnak fel. A leghosszabb ideig futó kérdések optimalizálása javíthatja a teljesítményt azáltal, hogy erőforrásokat szabadít fel a rendszeren futó más lekérdezések számára. 

### <a name="target-queries-with-performance-deltas"></a>Céllekérdezések teljesítménykülönbözetekkel 
A Query Store a teljesítményadatokat időablakokba vágja, így nyomon követheti a lekérdezés teljesítményét az idő múlásával. Ez segít azonosítani, hogy pontosan mely lekérdezések járulnak hozzá a teljes eltöltött idő növekedéséhez. Ennek eredményeképpen a számítási feladatok célzott hibaelhárítása.

### <a name="tuning-expensive-queries"></a>Költséges lekérdezések finom hangolása 
Ha a lekérdezést az optimálisnál gyengébb teljesítménnyel azonosítja, a végrehajtott művelet a probléma jellegétől függ: 
- [A Teljesítményjavaslatok segítségével](concepts-performance-recommendations.md) határozza meg, hogy vannak-e javasolt indexek. Ha igen, hozza létre az indexet, majd a Query Store segítségével értékelje ki a lekérdezés teljesítményét az index létrehozása után. 
- Győződjön meg arról, hogy a statisztikák naprakészek a lekérdezés által használt alapul szolgáló táblákhoz.
- Fontolja meg a költséges lekérdezések újraírását. Például használja ki a lekérdezés paraméterezése, és csökkenti a dinamikus SQL használatát. Optimális logikát valósíthat meg az adatok olvasásakor, például adatszűrés alkalmazása az adatbázis-oldalon, nem pedig az alkalmazás oldalon. 


## <a name="ab-testing"></a>A/B vizsgálat 
A Query Store segítségével összehasonlíthatja a számítási feladatok teljesítményét a bevezetni kívánt alkalmazásmódosítás előtt és után. Példák a Query Store használatával a környezet vagy az alkalmazások számítási feladatok teljesítményének változása hatásának felmérésére: 
- Az alkalmazás új verziójának visszaállítása. 
- További erőforrások hozzáadása a kiszolgálóhoz. 
- Hiányzó indexek létrehozása a drága lekérdezések által hivatkozott táblákon. 
 
Az alábbi esetek bármelyikében alkalmazza a következő munkafolyamatot: 
1. Futtassa a számítási feladatokat a Query Store-ban a tervezett módosítás előtt a teljesítményalap-alapkonfiguráció létrehozásához. 
2. Alkalmazásmódosítása(i) alkalmazása az ellenőrzött időpontban. 
3. Folytassa a számítási feladatok futtatását elég ideig ahhoz, hogy a változás után a rendszer teljesítménylemezképét hozza létre. 
4. Hasonlítsa össze a módosítás előtti és utáni eredményeket. 
5. Döntse el, hogy megtartja-e a módosítást vagy a visszaállítást. 


## <a name="identify-and-improve-ad-hoc-workloads"></a>Az ad hoc munkaterhelések azonosítása és javítása 
Egyes számítási feladatok nem rendelkeznek domináns lekérdezésekkel, amelyeket az alkalmazás általános teljesítményének javítása érdekében finomíthat. Ezeket a munkaterheléseket általában viszonylag nagy számú egyedi lekérdezés jellemzi, amelyek mindegyike a rendszererőforrások egy részét fogyasztja. Minden egyedi lekérdezés végrehajtása ritkán történik, így a futásidejű felhasználás uk nem kritikus. Másrészt, tekintettel arra, hogy az alkalmazás generál új lekérdezések minden alkalommal, a rendszer erőforrások jelentős részét a lekérdezés összeállítása, ami nem optimális. Ez a helyzet általában akkor fordul elő, ha az alkalmazás lekérdezéseket hoz létre (tárolt eljárások vagy paraméterezett lekérdezések használata helyett), vagy ha alapértelmezés szerint lekérdezéseket generáló objektumrelációs leképezési keretrendszerekre támaszkodik. 
 
Ha ön irányítja az alkalmazáskódot, érdemes lehet átírnia az adatelérési réteget tárolt eljárások vagy paraméterezett lekérdezések használatára. Ez a helyzet azonban az alkalmazás módosítása nélkül is javítható, ha a lekérdezés paraméterezését kényszeríti a teljes adatbázisra (az összes lekérdezésre) vagy az azonos lekérdezési kivonattal rendelkező egyes lekérdezéssablonokra. 

## <a name="next-steps"></a>További lépések
- További információ a [Query Store használatával kapcsolatos gyakorlati tanácsokról](concepts-query-store-best-practices.md)