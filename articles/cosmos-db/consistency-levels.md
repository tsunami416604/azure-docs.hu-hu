---
title: Konzisztenciaszintek az Azure Cosmos DB-ben
description: Az Azure Cosmos DB öt konzisztenciaszinttel rendelkezik a végleges konzisztencia, a rendelkezésre állás és a késés kompromisszumok kiegyensúlyozása érdekében.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 4e3d29471064616039bf946bb2762c15ce67bf8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530257"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Konzisztenciaszintek az Azure Cosmos DB-ben

Elosztott adatbázisok, amelyek a magas rendelkezésre állás, alacsony késés vagy mindkettő replikációja, hogy az alapvető kompromisszumot az olvasási konzisztencia vs rendelkezésre állás, késés és átviteli. A legtöbb kereskedelmi forgalomban kapható elosztott adatbázis arra kéri a fejlesztőket, hogy válasszanak a két szélsőséges konzisztenciamodell közül: *erős* konzisztencia és *végleges* konzisztencia. A linearizability vagy az erős konzisztencia modell az arany standard adatok programozhatóság. De hozzáteszi, az ár a magasabb késés (állandósult állapotban) és a csökkentett rendelkezésre állás (hibák során). Másrészt az esetleges konzisztencia magasabb rendelkezésre állást és jobb teljesítményt kínál, de megnehezíti az alkalmazások programozását. 

Az Azure Cosmos DB az adatok konzisztenciáját két véglet helyett választási lehetőségek spektrumaként közelíti meg. Erős konzisztencia és végleges konzisztencia vannak a végén a spektrum, de sok konzisztencia választás mentén a spektrum. A fejlesztők ezekkel a lehetőségekkel pontos döntéseket hozhatnak, és részletes kompromisszumot köthetnek a magas rendelkezésre állás és teljesítmény tekintetében. 

Az Azure Cosmos DB segítségével a fejlesztők öt jól definiált konzisztenciamodell közül választhatnak a konzisztenciaspektrumon. A legerősebbtől a lazábbig a modellek közé tartozik az *erős,* *határolt frissesség*, *munkamenet*, *konzisztens előtag*és *végleges* konzisztencia. A modellek jól meghatározott és intuitív, és fel lehet használni az egyes valós forgatókönyvek. Minden modell [rendelkezésre állási és teljesítmény-kompromisszumokat](consistency-levels-tradeoffs.md) biztosít, és az SL-ek is alátámasztják. Az alábbi képen a különböző konzisztenciaszintek spektrumként jelennek meg.

![Konzisztencia mint spektrum](./media/consistency-levels/five-consistency-levels.png)

A konzisztenciaszintek régió-agnosztikus, és garantált minden művelet, függetlenül attól, hogy a régió, ahonnan az olvasási és írási kiszolgált, az Azure Cosmos-fiókhoz társított régiók száma, vagy hogy a fiók egyetlen konfigurált vagy több írási régió.

## <a name="scope-of-the-read-consistency"></a>Az olvasási konzisztencia hatóköre

Az olvasási konzisztencia egyetlen olvasási művelethatókörre vonatkozik, amely egy partíciókulcs-tartományon vagy logikai partíción belül van. Az olvasási műveletet távoli ügyfél vagy tárolt eljárás is kibocsáthatja.

## <a name="configure-the-default-consistency-level"></a>Az alapértelmezett konzisztenciaszint beállítása

Az Azure Cosmos-fiók alapértelmezett konzisztenciaszintjét bármikor konfigurálhatja. A fiókjában konfigurált alapértelmezett konzisztenciaszint az adott fiókban lévő összes Azure Cosmos-adatbázisra és tárolóra vonatkozik. A tárolón vagy adatbázison kiadott összes olvasási és lekérdezési terület alapértelmezés szerint a megadott konzisztenciaszintet használja. További információ: Az [alapértelmezett konzisztenciaszint konfigurálása.](how-to-manage-consistency.md#configure-the-default-consistency-level)

## <a name="guarantees-associated-with-consistency-levels"></a>Konzisztenciaszintekkel kapcsolatos garanciák

Az Azure Cosmos DB által biztosított átfogó SL-ek garantálják, hogy az olvasási kérelmek 100 százaléka megfelel a konzisztencia-garanciának a választott konzisztenciaszinthez. Az olvasási kérelem megfelel a konzisztencia SLA, ha a konzisztencia szinthez kapcsolódó összes konzisztencia-garancia teljesül. Az Azure Cosmos DB öt konzisztenciaszintjének pontos definícióit a TLA+ specifikációs nyelv az [azure-cosmos-tla](https://github.com/Azure/azure-cosmos-tla) GitHub-tárházban biztosítjuk.

Az öt konzisztenciaszint szemantikája itt található:

- **Erős**: Az erős konzisztencia linearizability garanciát jelent. A linearizability a kérelmek egyidejű kiszolgálására vonatkozik. Az olvasások garantáltan egy elem legutóbbi véglegesített verzióját adják vissza. Az ügyfél soha nem lát nem véglegesített vagy részleges írást. A felhasználók mindig garantáltan elolvassák a legújabb véglegesített írást.

  A következő ábra a zenei hangokkal való erős összhangot mutatja be. Miután az adatokat a "USA nyugati régiója" régióba írta, amikor más régiókból olvassa be az adatokat, a legfrissebb értéket kapja:

  ![Videó](media/consistency-levels/strong-consistency.gif)

- **Határolt frissesség:** A beolvasások garantáltan tiszteletben tartják a konzisztens előtag garancia. Az olvasások egy elem *legvégén "K"* verzióival (azaz "frissítésekkel") vagy *"T"* időintervallummal elmaradnak az írásoktól. Más szóval, ha a kötött frissességet választja, a "frissesség" kétféleképpen konfigurálható: 

  * A cikk verzióinak száma (*K*)
  * Az az időintervallum (*T*), amellyel az olvasás oka az írások 

  A korlátozott frissesség a „frissességi ablak” kivételével teljes, globális sorrendet kínál. A monoton olvasási garanciák léteznek egy régión belül és kívül a frissesség ablakban. Az erős konzisztencia szemantikája megegyezik a határolt frissesség által kínált szemantikával. Az állsági ablak értéke nulla. A határolt frissességet késleltetett linearizability-nek is nevezik. Amikor egy ügyfél olvasási műveleteket hajt végre egy olyan régióban, amely elfogadja az írási műveleteket, a kötött frissenyness konzisztencia által nyújtott garanciák megegyeznek az erős konzisztencia által biztosított garanciákkal.

  A határolt frissességet gyakran választják olyan globálisan elosztott alkalmazások, amelyek alacsony írási késéseket várnak, de teljes globális rendelési garanciát igényelnek. Kötött frissesség nagy részére pályázatokat felvázoló csoport együttműködés és cserépdarab, készlet ketyeg, közzétesz- aláír/ sorban állás stb. Az alábbi ábra a zenei hangokkal való határos állságka konzisztenciáját mutatja be. Miután az adatokat a "Usa nyugati régiója" régióba írta, az "USA keleti régiója 2" és "Kelet-Kelet" régiók a beállított maximális időeltolódás vagy a maximális műveletek alapján olvassák az írott értéket:

  ![Videó](media/consistency-levels/bounded-staleness-consistency.gif)

- **Munkamenet:** Egyetlen ügyfélmunkameneten belül az olvasások garantáltan tiszteletben tartják a konzisztens előtagot (egyetlen "író" munkamenetet feltételezve), monoton olvasást, monoton írásokat, olvasási-a-írásokat és írás-követés-olvasási garanciákat. Az írási műveleteket végző munkameneten kívüli ügyfelek a végleges konzisztenciát fogják látni.

  A munkamenet-konzisztencia a széles körben használt konzisztenciaszint mind az egyrégiós, mind a globálisan elosztott alkalmazások esetében. Írási késéseket, rendelkezésre állást és olvasási átviteli átbocsátást biztosít a végleges konzisztenciához hasonló módon, de biztosítja a konzisztencia-garanciákat is, amelyek megfelelnek a felhasználó környezetében való működésre írt alkalmazások igényeinek. A következő ábra a zenei hangokkal való munkamenet konzisztenciáját mutatja be. A "West US 2" régió és az "USA keleti régiói 2" régiók ugyanazt a munkamenetet (A munkamenet) használják, így mindketten egyszerre olvassák be az adatokat. Míg az "Ausztrália keleti" régió "B munkamenetet" használ, így később, de ugyanabban a sorrendben kapja az adatokat, mint az írások.

  ![Videó](media/consistency-levels/session-consistency.gif)

- **Konzisztens előtag**: A visszaadott frissítések az összes frissítés néhány előtagot tartalmaznak, hézagok nélkül. Konzisztens előtag konzisztenciaszint garantálja, hogy az olvasás soha nem jelenik meg a sorrenden kívüli írások.

  Ha az írások végrehajtása `A, B, C` sorrendben történik, akkor az ügyfél `A`, `A,B` vagy `A,B,C` sorrendben látja azokat, de sosem felcserélt sorrendben (például `A,C` vagy `B,A,C`). Konzisztens előtag biztosít írási késések, rendelkezésre állási és olvasási átviteli átmenő hasonló a végleges konzisztencia, de azt is biztosítja a rendelési garanciákat, amelyek megfelelnek a forgatókönyvek, ahol a sorrend fontos. A következő ábra a konzisztencia előtag konzisztenciáját mutatja be a zenei jegyzetekkel. Az összes régióban az olvasások soha nem jelennek meg a sorrendben írja:

  ![Videó](media/consistency-levels/consistent-prefix.gif)

- **Esetleges**: Nincs rendelési garancia az olvasásra. További írások hiányában a replikák végül konvergálnak.  
A végleges konzisztencia a konzisztencia leggyengébb formája, mivel az ügyfél elolvashatja azokat az értékeket, amelyek régebbiek, mint korábban. A végleges konzisztencia ideális, ha az alkalmazás nem igényel rendelési garanciákat. Ilyenek például a Retweetek, a Kedvelések vagy a nem szálas megjegyzések száma. A következő ábra a zenei hangokkal való végleges összhangot mutatja be.

  ![Videó](media/consistency-levels/eventual-consistency.gif)

## <a name="additional-reading"></a>További olvasás

Ha többet szeretne megtudni a konzisztencia fogalmakról, olvassa el az alábbi cikkeket:

- [Magas szintű TLA+ specifikációk az Azure Cosmos DB által kínált öt konzisztenciaszinthez](https://github.com/Azure/azure-cosmos-tla)
- [Replikált adatok konzisztenciája magyarázta baseball (videó) Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Replikált adatok konzisztenciája magyarázta baseball (fehér könyv) Doug Terry](https://www.microsoft.com/en-us/research/publication/replicated-data-consistency-explained-through-baseball/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F157411%2Fconsistencyandbaseballreport.pdf)
- [Munkamenet-garanciák a gyengén konzisztens replikált adatokhoz](https://dl.acm.org/citation.cfm?id=383631)
- [Konzisztencia Tradeoffs a modern elosztott adatbázis-rendszerek tervezése: CAP csak egy része a történetnek](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Valószínűségi határvédett frissesség (PBS) a gyakorlati részleges kvórumokhoz](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [Végül konzisztens - Újra](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az Azure Cosmos DB konzisztenciaszintjeiről, olvassa el az alábbi cikkeket:

* [Válassza ki az alkalmazásnak megfelelő konzisztenciaszintet](consistency-levels-choosing.md)
* [Konzisztenciaszintek az Azure Cosmos DB API-k között](consistency-levels-across-apis.md)
* [Rendelkezésre állási és teljesítménykompromisszumok a különböző konzisztenciaszintekhez](consistency-levels-tradeoffs.md)
* [Az alapértelmezett konzisztenciaszint beállítása](how-to-manage-consistency.md#configure-the-default-consistency-level)
* [Az alapértelmezett konzisztenciaszint felülírása](how-to-manage-consistency.md#override-the-default-consistency-level)

