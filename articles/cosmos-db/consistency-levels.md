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
ms.openlocfilehash: 2611c25764503551c4da918d06bcaabe315cbf7c
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50963081"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Az Azure Cosmos DB-ben konzisztenciaszintek

Elosztott adatbázisok hagyatkoznia a magas rendelkezésre állás, alacsony késleltetésű, illetve mindkettőt, replikációs győződjön és a rendelkezésre állási, teljesítmény és a késés az olvasás következetes alapvető magával. A legtöbb kereskedelmi forgalomban kapható elosztott adatbázisok kérje meg a fejlesztők számára, hogy a két szélsőséges konzisztencia modell közül választhat: erős konzisztencia és a végleges konzisztencia. Bár a [linearizálhatósági](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) vagy az erős konzisztencia modellje az adatok programozhatóság a gold standard, nagy ahhoz áron (a stabil állapotot) nagyobb késést hozzáadja, és csökkenteni a rendelkezésre állás (meghibásodások). Végleges konzisztencia, másrészt magasabb rendelkezésre állás és jobb teljesítményt nyújt, de nehéz alkalmazások.

A cosmos DB választási lehetőségek, a két szélsőséges helyett egy átfogó, az adatkonzisztencia közelíti meg. Erős konzisztencia és a végleges konzisztencia az értéktartományon két végén, amelyek nincsenek számos konzisztenciaszint mentén az értéktartományon. Ezek konzisztencia beállításai lehetővé teszik a fejlesztők számára, hogy pontos lehetőségeket és a részletes és magas rendelkezésre állás vagy a teljesítmény tekintetében kompromisszumot kínál. A cosmos DB engedélyezve van a fejlesztők számára, hogy az öt jól definiált konzisztenciamodellekkel közül választhat a konzisztencia (legerősebb leggyengébb felé haladva) – színspektrumból **erős**, **korlátozott frissesség**, **munkamenet** , **konzisztens előtag**, és **végleges**. Egyes ezek konzisztenciamodellt kínál jól definiált és könnyen kezelhető és bizonyos valós forgatókönyvek esetén is használható. Az öt konzisztenciamodell mindegyike biztosítanak [rendelkezésre állás és teljesítmény kompromisszumot kínál a](consistency-levels-tradeoffs.md) és átfogó SLA-k élvezik.

![A spektrum, konzisztencia](./media/consistency-levels/five-consistency-levels.png)

A konzisztenciaszintek régiófüggetlen. A Cosmos DB-fiókot, a konzisztencia szintjét garantáltan minden olvasási műveletek, függetlenül a következő tulajdonságokkal:

- A régiót, amelyből az olvasási és írási kiszolgált
- A Cosmos-fiókjához társított régiók számának
- Hogy a fiók konfigurációja egy vagy több írási régiót

## <a name="scope-of-the-read-consistency"></a>Hatóköre a olvasásának következetessége

A olvasásának következetessége olvasási művelete (az egyes logikai partíciók) a partíciókulcs tartományon belül hatóköre egyetlen vonatkozik. Az olvasási műveletet egy távoli ügyfélen vagy egy tárolt eljárást adhatnak ki.

## <a name="configuring-the-default-consistency-level"></a>Az alapértelmezett konzisztenciaszint konfigurálása

Konfigurálhatja a **alapértelmezett konzisztenciaszint** a Cosmos DB-fiókja bármikor. Az alapértelmezett konzisztenciaszint a fiókjában konfigurált összes Cosmos-adatbázis (és tárolók) vonatkozik, az adott fiók. Olvasási és a egy tároló vagy adatbázis állították lekérdezések alapértelmezés szerint a megadott konzisztenciaszint alkalmaznak. További tudnivalókért lásd: hogyan [konfigurálhatja az alapértelmezett konzisztenciaszint](how-to-manage-consistency.md#configure-the-default-consistency-level) cikk.

## <a name="guarantees-associated-with-consistency-levels"></a>Konzisztenciaszintek társított garanciák

Az átfogó SLA-k az Azure Cosmos DB garantálja, hogy az olvasási kérések 100 % megfelelnek-e a megfelelő konzisztenciagaranciának bármely konzisztenciaszint választja által biztosított. Egy olvasási kérést tekinthető, az SLA-t, konzisztencia a konzisztencia szintjét társított összes konzisztenciagaranciákat teljesülése esetén. Az öt konzisztenciaszintet Cosmos DB-ben a pontos meghatározását a [TLA + specifikációs nyelv](http://lamport.azurewebsites.net/tla/tla.html) szerepelnek a [azure-cosmos-tla](https://github.com/Azure/azure-cosmos-tla) GitHub-adattárban. Az öt konzisztenciaszintek szemantikáját az alábbiakban tekintheti át:

- **Konzisztenciaszint = "erős"**: erős konzisztencia kínálja a [linearizálhatósági](https://aphyr.com/posts/313-strong-consistency-models) garantálva, az olvasások egy elem legutóbbi véglegesített verzióját garantált. Egy ügyfél soha nem jelenik meg egy nem véglegesített vagy részleges írása. Felhasználók mindig garantáltan olvassa el a legutóbbi véglegesített írása.

- **Konzisztenciaszint = "korlátozott frissesség"**: az olvasások garantáltan konzisztens előtag garanciát az irányadó. Az olvasások előfordulhat, hogy késés jelentkezhet írások legfeljebb (az "updates"-elem) K verziók, vagy nincs "időköz. Kiválasztása korlátozott frissesség, ha a "frissesség" két módon konfigurálhatók: 

  * Az elem (K) verzióinak száma vagy
  * Az időintervallum (t) amely szerint az olvasások előfordulhat, hogy késés jelentkezhet az írást. 

  Korlátozott frissesség ajánlatok teljes globális rendelési kivételével a "frissesség időszakban." A monoton olvasási garancia keretén belül egy régión belül és kívül is a "frissesség ablak." Erős konzisztencia rendelkezik azonos által kínált azokat korlátozott frissesség, és a egy "frissesség ablak" nulla egyenlő. Korlátozott frissesség is nevezik **idő – késleltetett linearizálhatósági**. Amikor egy ügyfél ugyanabban a régióban, amely írási olvasási műveleteket végez, korlátozott frissesség konzisztencia által nyújtott garanciák megegyeznek az erős konzisztencia.

- **Konzisztenciaszint = "munkamenet"**: olvasási-az-írások, írás-követi-olvasások garantálja az olvasások is garantálható, hogy tartsa tiszteletben a konzisztens előtag, monoton olvasások, monoton írások,. Egy ügyfél-munkamenet munkamenet-konzisztencia hatókörét.

- **Konzisztenciaszint = "konzisztens előtag"**: visszaadott frissítések nincsenek szünetek és az összes frissítés néhány előtagja tartalmaznak. Konzisztens előtag garantálja, hogy olvasások soha nem látható sorrendben írást.

- **Konzisztenciaszint = "végleges"**: nem sorbarendezésre garantált az olvasásokhoz. A további írási műveleteket hiányában a replikák végül szerveződik.

## <a name="consistency-levels-explained-through-baseball"></a>Konzisztenciaszintek a Baseball példáján

Most a baseball játék forgatókönyv például igénybe vehet, imagine írási műveletek sorozata a pontszám jelölő baseball játékfejlesztőknek, amelynek az inning-inning sor pontszám leírtak szerint a [replikált adatok konzisztencia baseball](https://www.microsoft.com/en-us/research/wp-content/uploads/2011/10/ConsistencyAndBaseballReport.pdf) a tanulmány. A képzeletbeli baseball játékot jelenleg éppen a hetedik inning (a proverbial hetedik – inning Nyújtás), és az otthoni csoport a winning 2 – 5.

| | **1** | **2** | **3** | **4** | **5** | **6** | **7** | **8** | **9** | **Futtatások** |
| - | - | - | - | - | - | - | - | - | - | - |
| **Látogatók** | 0 | 0 | 1 | 0 | 1 | 0 | 0 |  |  | 2 |
| **Kezdőlap** | 1 | 0 | 1 | 1 | 0 | 2 |  |  |  | 5 |

A Cosmos DB-tároló tárolja a látogatók és az otthoni csoport futtatási összegek. Amíg folyamatban van a játék, különböző olvasási garancia eredményezhet az ügyfelek különböző pontszámok olvasása. Az alábbi táblázat a látogatók és az egyes a öt konzisztenciagaranciákat otthoni pontszámok olvasásával visszaadható pontszámok teljes körét. A látogatói pontszám első helyen szerepel, és különböző lehetséges visszatérési értékek vesszővel kell elválasztani.

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

