---
title: Az Azure Cosmos DB-ben konzisztenciaszintek |} A Microsoft Docs
description: Az Azure Cosmos DB öt konzisztenciaszint egyenleg végleges konzisztencia, a rendelkezésre állás és a késleltetés kompromisszummal segítségével rendelkezik.
keywords: végleges konzisztencia, az azure cosmos db, azure, Microsoft azure
services: cosmos-db
author: aliuy
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: andrl
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5cb439f7fe6461fcef0d010535179e16e28c294a
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50239169"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Az Azure Cosmos DB-ben konzisztenciaszintek

Elosztott adatbázisok hagyatkoznia a magas rendelkezésre állás, alacsony késleltetésű, illetve mindkettőt, replikációs győződjön és a rendelkezésre állási, teljesítmény és a késés az olvasás következetes alapvető magával. A legtöbb kereskedelmi forgalomban kapható elosztott adatbázisok kérje meg a fejlesztők számára, hogy a két szélsőséges konzisztencia modell közül választhat: erős konzisztencia és a végleges konzisztencia. Bár a [linearizálhatósági](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) vagy az erős konzisztencia modellje adatok programozhatóság gold szabványa, (az egyenletes állapotú) nagyobb késés nagy ahhoz áron érhető el, és csökkenteni a rendelkezésre állás (meghibásodások). Végleges konzisztencia, másrészt magasabb rendelkezésre állás és jobb teljesítményt nyújt, de rendkívül nehéz programozni.

A cosmos DB választási lehetőségek, a két szélsőséges helyett egy átfogó, az adatkonzisztencia közelíti meg. Erős konzisztencia és a végleges konzisztencia az értéktartományon két végén, amelyek nincsenek konzisztenciaspektrumot számos konzisztenciaszint. Ezek konzisztencia beállításai lehetővé teszik a fejlesztők számára, hogy pontos lehetőségeket és kifinomultabb granularitási kompromisszumot kínál a magas rendelkezésre állás vagy a teljesítmény tekintetében. A cosmos DB segítségével a fejlesztők számára, hogy választhat a konzisztencia (legerősebb leggyengébb felé haladva) – színspektrumból öt jól definiált konzisztenciamodellekkel **erős**, **korlátozott frissesség**, **munkamenet** , **konzisztens előtag**, és **végleges**. Egyes ezek konzisztenciamodellt kínál jól definiált és könnyen kezelhető és bizonyos valós forgatókönyvek esetén is használható. Az öt konzisztenciamodell mindegyike adja meg a clear és rendelkezésre állás és teljesítmény kompromisszumot kínál, és átfogó SLA-k élvezik.

![Konzisztencia egy átfogó](./media/consistency-levels/five-consistency-levels.png)
**konzisztencia egy átfogó**

Vegye figyelembe, hogy a konzisztenciaszintek régiófüggetlen. A következő függetlenül minden olvasási műveletek garantáltan konzisztencia szintjét (és a megfelelő konzisztenciagaranciákat), a Cosmos DB-fiók:

- A régiót, amelyből az olvasási és írási kiszolgált
- A Cosmos-fiókjához társított régiók számának
- Hogy a fiók konfigurációja egy vagy több írási régiót

## <a name="scope-of-the-read-consistency"></a>Hatóköre a olvasásának következetessége

A olvasásának következetessége olvasási művelete partíciókulcs tartományba (más néven, a logikai partíció) hatóköre egyetlen vonatkozik. Az olvasási műveletet egy távoli ügyfélen vagy egy tárolt eljárást adhatnak ki.

## <a name="configuring-the-default-consistency-level"></a>Az alapértelmezett konzisztenciaszint konfigurálása

Konfigurálhatja a **alapértelmezett konzisztenciaszint** a Cosmos DB-fiókja bármikor. Az alapértelmezett konzisztenciaszint a fiókjában konfigurált összes Cosmos-adatbázis (és tárolók) vonatkozik, az adott fiók. Olvasási és a egy tároló vagy adatbázis állították lekérdezések alapértelmezés szerint fogja használni a konzisztencia szintjét. Itt találhat útmutató [konfigurálhatja az alapértelmezett konzisztenciaszint](how-to-manage-consistency.md#configure-the-default-consistency-level).

## <a name="guarantees-associated-with-consistency-levels"></a>Konzisztenciaszintek társított garanciák

Az Azure Cosmos DB által biztosított átfogó SLA-k garantálja, hogy az olvasási kérések 100 % megfelelnek-e a megfelelő konzisztenciagaranciának bármely konzisztenciaszint választja. Egy olvasási kérést minősül teljesül-e a konzisztencia SLA-t, ha teljesülnek a konzisztenciaszint társított összes konzisztenciagaranciákat. Az öt konzisztenciaszintet Cosmos DB-ben a pontos meghatározását a [TLA + specifikációs nyelv (TLA +)](http://lamport.azurewebsites.net/tla/tla.html) szerepelnek a [azure-cosmos-tla](https://github.com/Azure/azure-cosmos-tla) GitHub-adattárban. Az öt konzisztenciaszintek szemantikáját az alábbiakban tekintheti át:

- **Konzisztenciaszint = "erős"**: erős konzisztencia kínálja a [linearizálhatósági](https://aphyr.com/posts/313-strong-consistency-models) garantálva, az olvasások egy elem legutóbbi véglegesített verzióját garantált. Ügyfél soha nem látható egy nem véglegesített vagy részleges írása, és minden esetben garantáltan a legutóbbi véglegesített írási olvasása.
- **Konzisztenciaszint = "korlátozott frissesség"**: az olvasások garantáltan konzisztens előtag garanciát az irányadó. Az olvasások lag írási műveletek mögött legfeljebb K verziók vagy előtagjait (azaz a frissítések) elemet, vagy nincs "időköz. Ezért kiválasztása korlátozott frissesség, a "frissesség" konfigurálható kétféleképpen: a cikk vagy amelyekkel az olvasások előfordulhat, hogy késés jelentkezhet az írási időtartam alatt (t) verziójú (K) száma. Korlátozott frissesség ajánlatok teljes globális rendelési kivételével a "frissesség időszakban." A monoton olvasási garancia keretén belül egy régión belül és kívül is a "frissesség ablak." Erős konzisztencia az azonos szemantikákkal, korlátozott frissesség, de az a "frissesség ablakban" értéknek nullánál rendelkezik. Korlátozott frissesség más "idő – késleltetett linearizálhatósági" néven. Amikor egy ügyfél fogadja az írási műveletek egy adott régión belül olvasási műveleteket végez, korlátozott frissesség konzisztencia által nyújtott garanciák megegyeznek az erős konzisztencia.
- **Konzisztenciaszint = "munkamenet"**: olvasási-az-írások, írás-követi-olvasások garantálja az olvasások is garantálható, hogy tartsa tiszteletben a konzisztens előtag, monoton olvasások, monoton írások,. Egy ügyfél-munkamenet munkamenet-konzisztencia hatókörét.
- **Konzisztenciaszint = "konzisztens előtag"**: visszaadott frissítések állnak nincsenek szünetek és az összes frissítés néhány előtagja. Konzisztens előtag garantálja, hogy olvasások soha nem látható sorrendben írást.
- **Konzisztenciaszint = "végleges"**: nem sorbarendezésre garantált az olvasásokhoz. A további írási műveleteket hiányában a replikák végül szerveződik.

## <a name="consistency-levels-explained-through-baseball"></a>Konzisztenciaszintek a Baseball példáján

Mint a [replikált adatok konzisztencia keresztül focimeccsre szóló](https://www.microsoft.com/en-us/research/wp-content/uploads/2011/10/ConsistencyAndBaseballReport.pdf) tanulmány mutatja be, az imagine írási műveletek sorozata jelölő a pontszám a baseball játékfejlesztőknek, amelynek a inning-inning sor pontszámot. A képzeletbeli baseball játékot jelenleg éppen a hetedik inning (a proverbial hetedik – inning Nyújtás), és az otthoni csoport a winning 2 – 5.

| | **1** | **2** | **3** | **4** | **5** | **6** | **7** | **8** | **9** | **Futtatások** |
| - | - | - | - | - | - | - | - | - | - | - |
| **Látogatók** | 0 | 0 | 1 | 0 | 5 | 0 | 0 |  |  | 2 |
| **Kezdőlap** | 1 | 0 | 1 | 1 | 0 | 2 |  |  |  | 5 |

A Cosmos DB-tároló tárolja a látogatók és az otthoni csoport futtatási összegek. Amíg folyamatban van a játék, különböző olvasási garancia eredményezhet az ügyfelek különböző pontszámok olvasása. Az alábbi táblázat a látogatók és az egyes a öt konzisztenciagaranciákat otthoni pontszámok olvasásával visszaadható pontszámok teljes körét. Vegye figyelembe, hogy a látogatók pontszám első helyen szerepel, és különböző lehetséges visszatérési értékek vesszővel kell elválasztani.

| **Konzisztenciaszint** | **Pontszámok** |
| - | - |
| **Erős** | 2 – 5 |
| **Korlátozott frissesség** | eredmények, amelyek a legtöbb egy inning elavult:"2-3, 2 – 4, 2 – 5 |
| **Munkamenet** | <ul><li>Az író"a 2 – 5</li><li> a csak az író: 0-0, 0 és 1, 0-2, 0 – 3, és 4 közötti 0, 0 – 5, 1-0, 1-1, 1-2, 1 – 3, 1 – 4, 1 – 5, 2-0, 2-1, 2-2, 2-3, 2 – 4, 2 – 5</li><li>1 – 3 elolvasása után: 1-3, 1 – 4, 1 – 5, 2-3, 2 – 4, 2 – 5</li> |
| **Konzisztens előtag** | 0-0, 0 – 1, 1-1, 1-2, 1 – 3, 2-3, 2 – 4, 2 – 5 |
| **Végleges** | 0-0, 0 és 1, 0-2, 0 – 3, és 4 közötti 0, 0 – 5, 1-0, 1-1, 1-2, 1 – 3, 1 – 4, 1 – 5, 2-0, 2-1, 2-2, 2-3, 2 – 4, 2 – 5 |

## <a name="additional-reading"></a>További olvasnivaló

Konzisztencia fogalmak kapcsolatos további információkért olvassa el a következő cikkeket:

- [Az Azure Cosmos DB által kínált öt konzisztenciaszintekről magas szintű TLA + előírásai](https://github.com/Azure/azure-cosmos-tla)
- [Replikált adatok konzisztencia Baseball példáján (videó) Doug Terry által](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Replikált adatok konzisztencia Baseball példáján (tanulmány) Doug Terry által](https://www.microsoft.com/en-us/research/publication/replicated-data-consistency-explained-through-baseball/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F157411%2Fconsistencyandbaseballreport.pdf)
- [Munkamenet garanciákat, kis mértékben konzisztens replikált adatok](https://dl.acm.org/citation.cfm?id=383631)
- [Konzisztencia kompromisszumot kínál a Modern elosztott adatbázis rendszerek kialakításában: korlát a történetet csak egy részét képezi.](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html)
- [A gyakorlati részleges határozatképességére valószínűségi korlátozott frissesség (PBS)](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [Végül konzisztens – javított változat](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>További lépések

Cosmos DB-ben konzisztenciaszintek kapcsolatos további információkért olvassa el a következő cikkeket:

* [Az alkalmazás a megfelelő konzisztenciaszint kiválasztása](consistency-levels-choosing.md)
* [Konzisztenciaszintet Cosmos DB API-k között](consistency-levels-across-apis.md)
* [Kompromisszumot kínál a különböző konzisztenciaszintet rendelkezésre állás és teljesítmény](consistency-levels-tradeoffs.md)
* [Az alapértelmezett konzisztenciaszint konfigurálása](how-to-manage-consistency.md#configure-the-default-consistency-level)
* [Az alapértelmezett konzisztenciaszint felülbírálása](how-to-manage-consistency.md#override-the-default-consistency-level)

