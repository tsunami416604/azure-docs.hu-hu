---
title: Konzisztenciaszintek az Azure Cosmos DB-ben
description: Azure Cosmos DB öt konzisztencia-szinttel rendelkezik a végleges konzisztencia, a rendelkezésre állás és a késések kompromisszumának kiegyensúlyozásához.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 8f482c4fe6817c75079ceb98e981c846c395ad13
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2020
ms.locfileid: "91396025"
---
# <a name="what-are-consistency-levels-in-azure-cosmos-db"></a>Mik a Azure Cosmos DB konzisztenciáji szintjei?

A magas rendelkezésre állásra, kis késésre vagy mindkettőre támaszkodó elosztott adatbázisok esetében az olvasási konzisztencia és a rendelkezésre állás, a késés és az átviteli sebesség közötti alapvető kompromisszumot kell biztosítani. A legtöbb kereskedelmi forgalomban kapható elosztott adatbázis arra kéri a fejlesztőket, hogy válasszon a két szélsőséges konzisztencia-modell közül: *erős* konzisztencia és *végleges* konzisztencia. Az erős konzisztencia-modell linearizability az adatprogramozási szabvány. Ugyanakkor a magasabb írási késés (állandó állapotú) és a rendelkezésre állási idő (a hibák során) csökkentett árát adja hozzá. Másfelől a végső konzisztencia magasabb rendelkezésre állást és jobb teljesítményt nyújt, de megnehezíti az alkalmazások programozását.

A Azure Cosmos DB a két véglet helyett a választási lehetőségek spektrumát közelíti meg. A fejlesztők ezeket a lehetőségeket a magas rendelkezésre állás és a teljesítmény szempontjából pontos döntések és részletes kompromisszumok biztosítására használhatják.

A Azure Cosmos DB segítségével a fejlesztők öt jól meghatározott konzisztencia-szintet választhatnak a konzisztencia-spektrumon. Ezek a szintek *erős*, *kötött*elavulás, *munkamenet*, *konzisztens előtag*és *végleges* konzisztenciát tartalmaznak. A szintek jól definiálva és intuitív módon használhatók, és az adott valós forgatókönyvek esetében használhatók. Minden szint [rendelkezésre állási és teljesítménybeli kompromisszumokat](consistency-levels-tradeoffs.md) biztosít, és a SLA-kat támogatja. A következő képen a különböző konzisztencia-szintek láthatók spektrumként.

:::image type="content" source="./media/consistency-levels/five-consistency-levels.png" alt-text="Konzisztencia spektrumként" border="false" :::

A konzisztencia szintje régió-agnosztikus, és minden művelet számára garantált, függetlenül attól, hogy az olvasások és írások kiszolgálása melyik régióból történik, az Azure Cosmos-fiókhoz társított régiók száma, illetve hogy a fiókja egyetlen vagy több írási régióval van-e konfigurálva.

## <a name="scope-of-the-read-consistency"></a>Az olvasási konzisztencia hatóköre

Az olvasási konzisztencia a logikai partíción belüli egyetlen olvasási műveletre vonatkozik. Az olvasási műveletet távoli ügyfél vagy tárolt eljárás is kiállíthatja.

## <a name="configure-the-default-consistency-level"></a>Az alapértelmezett konzisztenciaszint beállítása

Bármikor megadhatja az alapértelmezett konzisztencia-szintet az Azure Cosmos-fiókban. A fiókban konfigurált alapértelmezett konzisztencia-szint az adott fiókhoz tartozó összes Azure Cosmos-adatbázisra és-tárolóra vonatkozik. A tárolók vagy adatbázisok által kiadott összes olvasás és lekérdezés alapértelmezés szerint a megadott konzisztencia-szintet használja. További információ: [az alapértelmezett konzisztencia-szint konfigurálása](how-to-manage-consistency.md#configure-the-default-consistency-level). Felülbírálhatja egy adott kérelem alapértelmezett konzisztencia-szintjét is, ha további információt szeretne megtudni az [alapértelmezett konzisztencia-szint felülbírálásáról](how-to-manage-consistency.md?#override-the-default-consistency-level) szóló cikkben.

## <a name="guarantees-associated-with-consistency-levels"></a>Konzisztencia-szintekhez kapcsolódó garanciák

A Azure Cosmos DB által biztosított átfogó SLA-kat garantálják, hogy az olvasási kérelmek 100 százaléka megfelel a választott konzisztencia-szint konzisztencia-garanciájának. Az olvasási kérelmek megfelelnek a konzisztencia-szolgáltatási szerződésnek, ha a konzisztencia-szinthez kapcsolódó összes konzisztencia-garancia teljesül. Az [Azure-Cosmos-tla GitHub-](https://github.com/Azure/azure-cosmos-tla) tárházban megtalálhatók a Azure Cosmos db öt konzisztencia-szintjének pontos definíciói a tla + specifikációs nyelv használatával.

Az öt konzisztencia-szint szemantikai leírása itt található:

- **Erős**: az erős konzisztencia linearizability garanciát nyújt. A Linearizability a kérelmek egyidejű kiszolgálására hivatkozik. Az olvasások garantáltan egy adott tétel legújabb véglegesített verzióját adják vissza. Az ügyfél nem látja a nem véglegesített vagy részleges írást. A felhasználók mindig garantáltan olvasni a legutóbb véglegesített írást.

  Az alábbi ábrán a hangjegyzetekkel való erős konzisztencia látható. Miután az adatok az "USA nyugati régiója 2" régiójába kerülnek, a többi régióból származó adatok beolvasása után a legfrissebb értéket kapja:

  :::image type="content" source="media/consistency-levels/strong-consistency.gif" alt-text="videó":::

- **Határos**elavulás: az olvasások garantálva vannak, hogy tiszteletben tartsák a konzisztens előtagú garanciát. Előfordulhat, hogy az olvasások lemaradnak az írások mögött az elemek legfeljebb *"K"* verziójában (azaz "frissítések"), vagy a *"T"* időintervallumban, attól függően, hogy melyik érték van elsőként. Más szóval, ha a kötött elavulás beállítást választja, a "elavulás" két módon konfigurálható:

- Az elemek verzióinak (*K*) száma
- Az az időintervallum (*T*), ameddig az olvasások lemerülhetnek az írások mögött.

A kötött elavulás az "elévülési időszakon kívüli teljes globális sorrendet kínálja." Ha egy ügyfél olvasási műveleteket hajt végre az írásokat fogadó régióban, a kötött elavulás konzisztenciája által biztosított garanciák megegyeznek az erős konzisztencia által garantált garanciákkal.

Az elavultság ablakon belül a határértékek a következő konzisztencia-garanciákat biztosítják:

- Azonos régióban lévő ügyfelek konzisztenciája egyetlen főkiszolgálós fiók esetén = erős
- A különböző régiókban lévő ügyfelek konzisztenciája egyetlen főkiszolgálós fiók esetében = konzisztens előtag
- Több főkiszolgálós fiók esetében egyetlen régióba írt ügyfelek konzisztenciája = konzisztens előtag
- Több főkiszolgálós fiók esetében a különböző régiókba írt ügyfelek konzisztenciája = végleges

  A kötött elavulás gyakran olyan globálisan elosztott alkalmazások által van kiválasztva, amelyek alacsony írási késést várnak, de teljes globális rendelési garanciát igényelnek. A kötött elavulás kiválóan használható a csoportos együttműködést és megosztást, a tőzsdei előfizetést, a közzétételt és a várakozási sort is tartalmazó alkalmazások esetében. Az alábbi ábrán látható, hogy a megkötött elavultság konzisztens legyen a zenei megjegyzésekkel. Az adatok az "USA nyugati régiója 2" régiójába való beírása után az "USA 2. keleti régiója" és a "Kelet-Ausztrália" régiók a beállított maximális késési idő vagy a maximális műveletek alapján olvassák el a megírt értéket:

  :::image type="content" source="media/consistency-levels/bounded-staleness-consistency.gif" alt-text="videó":::

- **Munkamenet**: egyetlen ügyfél-munkameneten belül a rendszer garantáltan beolvassa a konzisztens előtagot, az monoton olvasásokat, az monoton írásokat, az olvasást és írást, valamint az írási és olvasási garanciákat. Ez egyetlen "író" munkamenetet feltételez, vagy a munkamenet-tokent több író számára is megosztja.

A munkamenet-végrehajtón kívüli ügyfelek a következő garanciákat fogják látni:

- Azonos régióban lévő ügyfelek konzisztenciája egyetlen főkiszolgálós fiók esetében = konzisztens előtag
- A különböző régiókban lévő ügyfelek konzisztenciája egyetlen főkiszolgálós fiók esetében = konzisztens előtag
- Több főkiszolgálós fiók esetében egyetlen régióba írt ügyfelek konzisztenciája = konzisztens előtag
- Több főkiszolgálós fiók esetében több régióba írt ügyfelek konzisztenciája = végleges

  A munkamenet konzisztenciája a legszélesebb körben használt konzisztencia-szint mind az egyetlen régió, mind a globálisan elosztott alkalmazások esetében. Az írási késleltetést, rendelkezésre állást és olvasási sebességet biztosít a végleges konzisztencia szempontjából, de biztosítja a konzisztencia-garanciát is, amely megfelel a felhasználó környezetében való működésre írt alkalmazások igényeinek. A következő ábra a munkamenetek konzisztenciáját mutatja be hangjegyzetekkel. Az "USA nyugati régiója 2 írója" és az "USA nyugati régiója 2 olvasója" ugyanazt a munkamenetet használja (A-munkamenet), hogy egyszerre ugyanazokat az adatfájlokat olvassák. Míg a "Kelet-Ausztrália" régió "B munkamenetet" használ, a rendszer később, de az írásokkal megegyező sorrendben fogadja az adatot.

  :::image type="content" source="media/consistency-levels/session-consistency.gif" alt-text="videó":::

- **Konzisztens előtag**: a visszaadott frissítések az összes frissítés néhány előtagját tartalmazzák, és nincsenek rések. Konzisztens előtag-konzisztenciai szint biztosítja, hogy az olvasások soha ne lássák a megrendelésen kívüli írásokat.

Ha az írások sorrendben lettek elvégezve, akkor az ügyfél a következőt látja:, `A, B, C` `A` `A,B` vagy `A,B,C` `A,C` `B,A,C` Az konzisztens előtag a végleges konzisztencia miatti írási késleltetést, rendelkezésre állást és olvasási átviteli sebességet biztosít, ugyanakkor biztosítja az olyan forgatókönyvek igényeit is, amelyek a sorrend szempontjából fontosak. 

Az alábbi konzisztens előtagok konzisztencia-garanciái:

- Azonos régióban lévő ügyfelek konzisztenciája egyetlen főkiszolgálós fiók esetében = konzisztens előtag
- A különböző régiókban lévő ügyfelek konzisztenciája egyetlen főkiszolgálós fiók esetében = konzisztens előtag
- Több főkiszolgálós fiók esetében egyetlen régióba írt ügyfelek konzisztenciája = konzisztens előtag
- Több főkiszolgálós fiók esetében több régióba írt ügyfelek konzisztenciája = végleges

A következő ábra a konzisztencia-előtagot ábrázolja a zenei megjegyzésekkel. Az összes régióban az olvasások soha nem láthatók az írások sorrendjében:

  :::image type="content" source="media/consistency-levels/consistent-prefix.gif" alt-text="videó":::

- **Végleges**: nem áll rendelkezésre rendelési garancia a beolvasáshoz. Ha nincsenek további írások, a replikák végül konvergálnak.  
A végleges konzisztencia a konzisztencia leggyengébb formája, mivel előfordulhat, hogy az ügyfél elolvashatja azokat az értékeket, amelyek régebbiek, mint a korábban olvasottak. A végleges konzisztencia ideális, ha az alkalmazás nem igényel rendelési garanciát. Ilyenek például a retweets, a Like vagy a nem többszálú megjegyzések száma. Az alábbi ábrán a zenei megjegyzésekkel való végleges konzisztencia látható.

  :::image type="content" source="media/consistency-levels/eventual-consistency.gif" alt-text="videó":::

## <a name="additional-reading"></a>További információ

Ha többet szeretne megtudni a konzisztencia-fogalmakról, olvassa el a következő cikkeket:

- [Magas szintű TLA + specifikációk a Azure Cosmos DB által kínált öt konzisztencia-szinthez](https://github.com/Azure/azure-cosmos-tla)
- [A replikált adatkonzisztencia magyarázata a baseball (videó) által a Doug Terry alapján](https://www.youtube.com/watch?v=gluIh8zd26I)
- [A replikált adatkonzisztencia a baseball (tanulmány) által a Doug Terry alapján magyarázható](https://www.microsoft.com/research/publication/replicated-data-consistency-explained-through-baseball/)
- [A gyengén konzisztens replikált adatszolgáltatások munkamenet-garanciái](https://dl.acm.org/citation.cfm?id=383631)
- [Konzisztencia-kompromisszumok a modern elosztott adatbázis-rendszerek kialakításában: a CAP csak a történet részét képezi](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Valószínűségi határos elavulás (PBS) a gyakorlati részleges Kvórumokhoz](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [Végül konzisztens – felülvizsgálat](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a Azure Cosmos DB konzisztenciáji szintjeiről, olvassa el a következő cikkeket:

- [Az alkalmazás megfelelő konzisztencia-szintjének kiválasztása](consistency-levels-choosing.md)
- [Konzisztencia szintjei Azure Cosmos DB API-k között](consistency-levels-across-apis.md)
- [Rendelkezésre állási és teljesítménybeli kompromisszumok különböző konzisztencia-szintekhez](consistency-levels-tradeoffs.md)
- [Az alapértelmezett konzisztenciaszint beállítása](how-to-manage-consistency.md#configure-the-default-consistency-level)
- [Az alapértelmezett konzisztenciaszint felülírása](how-to-manage-consistency.md#override-the-default-consistency-level)
