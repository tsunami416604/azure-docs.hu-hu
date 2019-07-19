---
title: Az Azure Cosmos DB-ben konzisztenciaszintek
description: Az Azure Cosmos DB öt konzisztenciaszint egyenleg végleges konzisztencia, a rendelkezésre állás és a késleltetés kompromisszummal segítségével rendelkezik.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/16/2019
ms.openlocfilehash: bb1fda48119785f5173790246f8069d3e11b6dae
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297742"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Az Azure Cosmos DB-ben konzisztenciaszintek

Elosztott adatbázisok által használt, magas rendelkezésre állás, alacsony késleltetésű, illetve mindkettőt, replikációs győződjön meg az olvasás következetes és a rendelkezésre állási, teljesítmény és késés alapvető magával. A legtöbb kereskedelmi forgalomban kapható elosztott adatbázis arra kéri a fejlesztőket, hogy válasszon a két szélsőséges konzisztencia-modell közül: *erős* konzisztencia és *végleges* konzisztencia. A linearizability vagy az erős konzisztencia-modell az adatprogramozási szabvány. Azonban a magasabb késés (állandó állapotú) és a rendelkezésre állási idő (a hibák során) csökkentett árát adja hozzá. Másfelől a végső konzisztencia magasabb rendelkezésre állást és jobb teljesítményt nyújt, de megnehezíti az alkalmazások programozását. 

Az Azure Cosmos DB választási helyett két szélsőséges spektrumát, az adatkonzisztencia közelíti meg. Az erős konzisztencia és a végleges konzisztencia a spektrum végén történik, de a spektrum számos következetességi lehetőséggel rendelkezik. A fejlesztők ezeket a lehetőségeket a magas rendelkezésre állás és a teljesítmény szempontjából pontos döntések és részletes kompromisszumok biztosítására használhatják. 

Az Azure Cosmos DB öt jól definiált konzisztenciamodellekkel konzisztencia spektrum fejlesztők is választhat. A modellek a legerősebbtól a lazábbig terjednek, így *erős*, *kötött*elavulás, *munkamenet*, *konzisztens előtag*és *végleges* konzisztencia áll fenn. A modellek jól definiált és intuitív módon használhatók, és adott valós forgatókönyvek esetében alkalmazhatók. Minden modell [rendelkezésre állási és teljesítménybeli kompromisszumokat](consistency-levels-tradeoffs.md) biztosít, és a SLA-kat támogatja. A következő képen a különböző konzisztencia-szintek láthatók spektrumként.

![A spektrum, konzisztencia](./media/consistency-levels/five-consistency-levels.png)

A konzisztencia szintje régió-agnosztikus, és minden művelet számára garantált, függetlenül attól, hogy az olvasások és írások kiszolgálása melyik régióból történik, az Azure Cosmos-fiókhoz társított régiók száma, illetve hogy a fiókja egyetlen vagy több írási régió.

## <a name="scope-of-the-read-consistency"></a>Az olvasás következetes hatóköre

Partíciókulcs tartományt és logikai partíció belül hatóköre egyetlen olvasási művelet olvasásának következetessége vonatkozik. Az olvasási műveletet egy távoli ügyfélen vagy egy tárolt eljárást adhatnak ki.

## <a name="configure-the-default-consistency-level"></a>Az alapértelmezett konzisztenciaszint beállítása

Az alapértelmezett konzisztenciaszint bármikor konfigurálhatja a Azure Cosmos-fiókjában. A fiókban konfigurált alapértelmezett konzisztencia-szint az adott fiókhoz tartozó összes Azure Cosmos-adatbázisra és-tárolóra vonatkozik. Olvasási és a egy tároló vagy adatbázis állították lekérdezések használják alapértelmezés szerint a megadott konzisztencia szintjét. További tudnivalókért lásd: hogyan [konfigurálhatja az alapértelmezett konzisztenciaszint](how-to-manage-consistency.md#configure-the-default-consistency-level).

## <a name="guarantees-associated-with-consistency-levels"></a>Konzisztenciaszintek társított garanciák

Az átfogó SLA-k az Azure Cosmos DB garantálja, hogy az olvasási kérések 100 %-os teljesíti-e megfelelő konzisztenciagaranciának bármely konzisztenciaszint választja által biztosított. A konzisztenciaszint társított összes konzisztenciagaranciákat teljesülése esetén egy olvasási kérést megfelel-e a konzisztencia SLA-t. Az [Azure-Cosmos-tla GitHub-](https://github.com/Azure/azure-cosmos-tla) tárházban megtalálhatók a Azure Cosmos db öt konzisztencia-szintjének pontos definíciói a tla + specifikációs nyelv használatával.

Az öt konzisztenciaszintek szemantikáját ebben a témakörben találhatók:

- **Erős**: Az erős konzisztencia linearizability garanciát biztosít. A Linearizability a kérelmek egyidejű kiszolgálására hivatkozik. Az olvasások garantáltan adja vissza egy elem legutóbbi véglegesített verzióba. Egy ügyfél soha nem lát egy nem véglegesített vagy részleges írása. Felhasználók mindig garantáltan olvassa el a legutóbbi véglegesített írása.

- **Kötött elavultság**: Az olvasások garantálják, hogy tiszteletben tartsák a konzisztens előtagú garanciát. Az olvasások a " *K"* (azaz "frissítések") vagy a *"T"* időintervalluma szerint az írások mögött maradnak. Más szóval, ha a kötött elavulás beállítást választja, a "elavulás" két módon konfigurálható: 

  * Az elemek verzióinak (*K*) száma
  * Az az időintervallum (*T*), ameddig az olvasások lemerülhetnek az írások mögött. 

  Korlátozott frissesség ajánlatok teljes globális rendelési kivételével a "frissesség időszakban." A monoton olvasási garancia egy adott régión belül és a frissesség időszakon kívül is belül léteznek. Az erős konzisztencia ugyanazokkal a szemantikagal rendelkezik, mint a kötött elavulás. A frissesség időszak nem lehet negatív érték. Korlátozott frissesség is idő – késleltetett linearizálhatósági nevezik. Ha egy ügyfél olvasási műveleteket hajt végre az írásokat fogadó régióban, a kötött elavulás konzisztenciája által biztosított garanciák megegyeznek az erős konzisztencia által garantált garanciákkal.

- **Munkamenet**:  Egyetlen ügyfél-munkamenet olvasásakor garantált a konzisztens előtag (feltételezhető, hogy egyetlen "író" munkamenet), monoton olvasás, monoton írás, olvasás és írás, valamint írási és olvasási garancia. A munkamenet-végrehajtón kívüli ügyfelek a végleges konzisztenciát fogják látni.

- **Konzisztens előtag**: A visszaadott frissítések az összes frissítés néhány előtagját tartalmazzák, és nincsenek rések. Konzisztens előtag-konzisztenciai szint biztosítja, hogy az olvasások soha ne lássák a megrendelésen kívüli írásokat.

- **Végleges**: A beolvasáshoz nem áll rendelkezésre rendelési garancia. A további írási műveleteket hiányában a replikák végül szerveződik.

## <a name="consistency-levels-explained-through-baseball"></a>Konzisztenciaszintek a Baseball példáján

Vessünk egy baseball játék forgatókönyv példaként. Tegyük fel, amelyek a pontszám a baseball játék írási műveletek sorozata. A sor a inning-inning pontszám leírt a [replikált adatok konzisztencia baseball](https://www.microsoft.com/en-us/research/wp-content/uploads/2011/10/ConsistencyAndBaseballReport.pdf) tanulmány. A képzeletbeli baseball játékot jelenleg éppen a hetedik inning. A fenti – inning stretch. A látogatók mögött egy 2 – 5 pontszám látható, amely az alábbiak szerint jelenik meg:

| | **1** | **2** | **3** | **4** | **5** | **6** | **7** | **8** | **9** | **Futtatások** |
| - | - | - | - | - | - | - | - | - | - | - |
| **Látogatók** | 0 | 0 | 1 | 0 | 1 | 0 | 0 |  |  | 2 |
| **Kezdőlap** | 1 | 0 | 1 | 1 | 0 | 2 |  |  |  | 5 |

Az Azure Cosmos-tároló a látogatók és a hazai csapatok futtatási összegét tartalmazza. Amíg folyamatban van a játék, különböző olvasási garancia eredményezheti, hogy az ügyfelek különböző pontszámok olvasása. Az alábbi táblázat felsorolja, előfordulhat, hogy a látogatók és az egyes a öt konzisztenciagaranciákat otthoni pontszámok olvasásával visszaadandó eredmények teljes készletét. A látogatói pontszám jelenik meg először. Különböző lehetséges visszatérési értékek vesszővel kell elválasztani.

| **Konzisztenciaszint** | **Pontszámok (látogatók, Kezdőlap)** |
| - | - |
| **Erős** | 2 – 5 |
| **Korlátozott frissesség** | A legfeljebb egy játékrész rendelkező pontszámok elavultak: 2-3, 2-4, 2-5 |
| **Munkamenet** | <ul><li>Az író esetében: 2 – 5</li><li> Az író kivételével mindenki számára: 0-0, 0 és 1, 0-2, 0 – 3, és 4 közötti 0, 0 – 5, 1-0, 1-1, 1-2, 1 – 3, 1 – 4, 1 – 5, 2-0, 2-1, 2-2, 2-3, 2 – 4, 2 – 5</li><li>Az 1-3-as olvasás után: 1-3, 1-4, 1-5, 2-3, 2-4, 2-5</li> |
| **Konzisztens előtag** | 0-0, 0 – 1, 1-1, 1-2, 1 – 3, 2-3, 2 – 4, 2 – 5 |
| **Végleges** | 0-0, 0 és 1, 0-2, 0 – 3, és 4 közötti 0, 0 – 5, 1-0, 1-1, 1-2, 1 – 3, 1 – 4, 1 – 5, 2-0, 2-1, 2-2, 2-3, 2 – 4, 2 – 5 |

## <a name="additional-reading"></a>További olvasnivaló

Konzisztencia fogalmak kapcsolatos további információkért olvassa el a következő cikkeket:

- [Az Azure Cosmos DB által kínált öt konzisztenciaszintekről magas szintű TLA + előírásai](https://github.com/Azure/azure-cosmos-tla)
- [A replikált adatok konzisztencia kifejtett keresztül Baseball (videó) Doug Terry által](https://www.youtube.com/watch?v=gluIh8zd26I)
- [A replikált adatok konzisztencia kifejtett keresztül Baseball (tanulmány) Doug Terry által](https://www.microsoft.com/en-us/research/publication/replicated-data-consistency-explained-through-baseball/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F157411%2Fconsistencyandbaseballreport.pdf)
- [Munkamenet garanciákat, kis mértékben konzisztens replikált adatok](https://dl.acm.org/citation.cfm?id=383631)
- [Konzisztencia-kompromisszumok a modern elosztott adatbázis-rendszerek kialakításában: A CAP csak a történet része](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [A gyakorlati részleges határozatképességére valószínűségi korlátozott frissesség (PBS)](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [Végül konzisztens – javított változat](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>További lépések

Az Azure Cosmos DB-ben konzisztenciaszintek kapcsolatos további információkért olvassa el a következő cikkeket:

* [Az alkalmazás a megfelelő konzisztenciaszint kiválasztása](consistency-levels-choosing.md)
* [Konzisztenciaszintek az Azure Cosmos DB API-k között](consistency-levels-across-apis.md)
* [Kompromisszumot kínál a különböző konzisztenciaszintet rendelkezésre állás és teljesítmény](consistency-levels-tradeoffs.md)
* [Az alapértelmezett konzisztenciaszint konfigurálása](how-to-manage-consistency.md#configure-the-default-consistency-level)
* [Bírálja felül az alapértelmezett konzisztenciaszint](how-to-manage-consistency.md#override-the-default-consistency-level)

