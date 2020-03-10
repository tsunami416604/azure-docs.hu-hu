---
title: Az Azure Cosmos DB-ben konzisztenciaszintek
description: Az Azure Cosmos DB öt konzisztenciaszint egyenleg végleges konzisztencia, a rendelkezésre állás és a késleltetés kompromisszummal segítségével rendelkezik.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: b5d9df7a0afa9b4270f0eff643e083e5bccfceb8
ms.sourcegitcommit: e6bce4b30486cb19a6b415e8b8442dd688ad4f92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2020
ms.locfileid: "78933685"
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

Az alapértelmezett konzisztenciaszint bármikor konfigurálhatja a Azure Cosmos-fiókjában. A fiókban konfigurált alapértelmezett konzisztencia-szint az adott fiókhoz tartozó összes Azure Cosmos-adatbázisra és-tárolóra vonatkozik. Olvasási és a egy tároló vagy adatbázis állították lekérdezések használják alapértelmezés szerint a megadott konzisztencia szintjét. További információ: [az alapértelmezett konzisztencia-szint konfigurálása](how-to-manage-consistency.md#configure-the-default-consistency-level).

## <a name="guarantees-associated-with-consistency-levels"></a>Konzisztenciaszintek társított garanciák

Az átfogó SLA-k az Azure Cosmos DB garantálja, hogy az olvasási kérések 100 %-os teljesíti-e megfelelő konzisztenciagaranciának bármely konzisztenciaszint választja által biztosított. A konzisztenciaszint társított összes konzisztenciagaranciákat teljesülése esetén egy olvasási kérést megfelel-e a konzisztencia SLA-t. Az [Azure-Cosmos-tla GitHub-](https://github.com/Azure/azure-cosmos-tla) tárházban megtalálhatók a Azure Cosmos db öt konzisztencia-szintjének pontos definíciói a tla + specifikációs nyelv használatával.

Az öt konzisztenciaszintek szemantikáját ebben a témakörben találhatók:

- **Erős**: az erős konzisztencia linearizability garanciát nyújt. A Linearizability a kérelmek egyidejű kiszolgálására hivatkozik. Az olvasások garantáltan adja vissza egy elem legutóbbi véglegesített verzióba. Egy ügyfél soha nem lát egy nem véglegesített vagy részleges írása. Felhasználók mindig garantáltan olvassa el a legutóbbi véglegesített írása.

  Az alábbi ábrán a hangjegyzetekkel való erős konzisztencia látható. Ha az adatok az "USA keleti régiója" régiójába kerülnek, az adatok más régiókból való beolvasása után a legfrissebb értéket kapja:

  ![Videó](media/consistency-levels/strong-consistency.gif)

- **Határos**elavulás: az olvasások garantálva vannak, hogy tiszteletben tartsák a konzisztens előtagú garanciát. Az olvasások a " *K"* (azaz "frissítések") és a *"T"* ("" updates ")" Más szóval, ha a kötött elavulás beállítást választja, a "elavulás" két módon konfigurálható: 

  * Az elemek verzióinak (*K*) száma
  * Az az időintervallum (*T*), ameddig az olvasások lemerülhetnek az írások mögött. 

  Korlátozott frissesség ajánlatok teljes globális rendelési kivételével a "frissesség időszakban." A monoton olvasási garancia egy adott régión belül és a frissesség időszakon kívül is belül léteznek. Az erős konzisztencia ugyanazokkal a szemantikagal rendelkezik, mint a kötött elavulás. A frissesség időszak nem lehet negatív érték. Korlátozott frissesség is idő – késleltetett linearizálhatósági nevezik. Ha egy ügyfél olvasási műveleteket hajt végre az írásokat fogadó régióban, a kötött elavulás konzisztenciája által biztosított garanciák megegyeznek az erős konzisztencia által garantált garanciákkal.

  A kötött elavulás gyakran olyan globálisan elosztott alkalmazások által van kiválasztva, amelyek alacsony írási késést várnak, de teljes globális rendelési garanciát igényelnek. A kötött elavulás kiválóan használható a csoportos együttműködést és megosztást, a tőzsdei előfizetést, a közzétételt és a várakozási sort is tartalmazó alkalmazások esetében. Az alábbi ábrán látható, hogy a megkötött elavultság konzisztens legyen a zenei megjegyzésekkel. Miután az adatok az "East US" régióba íródnak, az "USA nyugati régiója" és a "Kelet-Ausztrália" régió a beállított maximális késési idő vagy a maximális műveletek alapján beolvasta a megírt értéket:

  ![Videó](media/consistency-levels/bounded-staleness-consistency.gif)

- **Munkamenet**: egyetlen ügyfél-munkameneten belül az olvasások garantáltan betartják a konzisztens előtagot (feltéve, hogy egyetlen "író" munkamenetet), monoton olvasást, monoton írást, olvasást és írást, valamint írási és olvasási garanciákat biztosítanak. A munkamenet-végrehajtón kívüli ügyfelek a végleges konzisztenciát fogják látni.

  A munkamenet konzisztenciája a széles körben használt konzisztenciai szint mind az egyetlen régió, mind a globálisan elosztott alkalmazások esetében. Az írási késleltetést, rendelkezésre állást és olvasási sebességet biztosít a végleges konzisztencia szempontjából, de biztosítja a konzisztencia-garanciát is, amely megfelel a felhasználó környezetében való működésre írt alkalmazások igényeinek. A következő ábra a munkamenetek konzisztenciáját mutatja be hangjegyzetekkel. Az "USA nyugati régiója" és az "USA keleti régiója" ugyanazt a munkamenetet használja (A-munkamenet), hogy egyszerre olvassák el az adatelemzést. Míg a "Kelet-Ausztrália" régió "B munkamenetet" használ, a rendszer később, de az írásokkal megegyező sorrendben fogadja az adatot.

  ![Videó](media/consistency-levels/session-consistency.gif)

- **Konzisztens előtag**: a visszaadott frissítések az összes frissítés néhány előtagját tartalmazzák, és nincsenek rések. Az konzisztens előtag-konzisztenciai szint garantálja, hogy az olvasás soha nem jelenik meg a sorrend szerinti írásokban.

  Ha az írásokat a `A, B, C`sorrendben hajtották végre, akkor az ügyfél `A`, `A,B`vagy `A,B,C`t lát el, de soha nem, például `A,C` vagy `B,A,C`. Az konzisztens előtag a végleges konzisztencia miatti írási késleltetést, rendelkezésre állást és olvasási átviteli sebességet biztosít, ugyanakkor biztosítja az olyan forgatókönyvek igényeit is, amelyek a sorrend szempontjából fontosak. A következő ábra a konzisztencia-előtagot ábrázolja a zenei megjegyzésekkel. Az összes régióban az olvasások soha nem láthatók az írások sorrendjében:

  ![Videó](media/consistency-levels/consistent-prefix.gif)

- **Végleges**: nem áll rendelkezésre rendelési garancia a beolvasáshoz. A további írási műveleteket hiányában a replikák végül szerveződik.  
A végleges konzisztencia a konzisztencia leggyengébb formája, mivel előfordulhat, hogy az ügyfél elolvashatja azokat az értékeket, amelyek régebbiek, mint a korábban olvasottak. A végleges konzisztencia ideális, ha az alkalmazás nem igényel rendelési garanciát. Ilyenek például a retweets, a Like vagy a nem többszálú megjegyzések száma. Az alábbi ábrán a zenei megjegyzésekkel való végleges konzisztencia látható.

  ![Videó](media/consistency-levels/eventual-consistency.gif)

## <a name="additional-reading"></a>További olvasnivaló

Konzisztencia fogalmak kapcsolatos további információkért olvassa el a következő cikkeket:

- [Magas szintű TLA + specifikációk a Azure Cosmos DB által kínált öt konzisztencia-szinthez](https://github.com/Azure/azure-cosmos-tla)
- [A replikált adatkonzisztencia magyarázata a baseball (videó) által a Doug Terry alapján](https://www.youtube.com/watch?v=gluIh8zd26I)
- [A replikált adatkonzisztencia a baseball (tanulmány) által a Doug Terry alapján magyarázható](https://www.microsoft.com/en-us/research/publication/replicated-data-consistency-explained-through-baseball/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F157411%2Fconsistencyandbaseballreport.pdf)
- [A gyengén konzisztens replikált adatszolgáltatások munkamenet-garanciái](https://dl.acm.org/citation.cfm?id=383631)
- [Konzisztencia-kompromisszumok a modern elosztott adatbázis-rendszerek kialakításában: a CAP csak a történet részét képezi](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Valószínűségi határos elavulás (PBS) a gyakorlati részleges Kvórumokhoz](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [Végül konzisztens – felülvizsgálat](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>További lépések

Az Azure Cosmos DB-ben konzisztenciaszintek kapcsolatos további információkért olvassa el a következő cikkeket:

* [Az alkalmazás megfelelő konzisztencia-szintjének kiválasztása](consistency-levels-choosing.md)
* [Konzisztencia szintjei Azure Cosmos DB API-k között](consistency-levels-across-apis.md)
* [Rendelkezésre állási és teljesítménybeli kompromisszumok különböző konzisztencia-szintekhez](consistency-levels-tradeoffs.md)
* [Az alapértelmezett konzisztencia-szint konfigurálása](how-to-manage-consistency.md#configure-the-default-consistency-level)
* [Az alapértelmezett konzisztencia-szint felülbírálása](how-to-manage-consistency.md#override-the-default-consistency-level)

