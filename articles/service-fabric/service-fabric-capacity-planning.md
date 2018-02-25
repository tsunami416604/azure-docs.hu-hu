---
title: "Kapacitástervezés a Service Fabric-alkalmazásokhoz |} Microsoft Docs"
description: "Ismerteti, hogyan lehet szükséges a Service Fabric-alkalmazás a számítási csomópontok számának meghatározása"
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: markfuss
editor: 
ms.assetid: 9fa47be0-50a2-4a51-84a5-20992af94bea
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: dc5e81ff7e5466d258624cee80c520c848bd60fa
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2018
---
# <a name="capacity-planning-for-service-fabric-applications"></a>Kapacitástervezés a Service Fabric-alkalmazások
Ez a dokumentum útmutatást ad az Azure Service Fabric-alkalmazások futtatásához szükséges erőforrások (CPU-k, memória, lemezes tárolás) becslésére módjáról. Gyakori, hogy az erőforrás-követelményei változnak az idők is. Túl sok erőforrás általában a szolgáltatás fejlesztés/tesztelés, és ezután ugyan több erőforrást, éles környezetben módba, és az alkalmazás növekszik időben népszerűvé vált a szükséges. Az alkalmazás tervezésekor gondolja, hogy a hosszú távú követelmények keresztül, és lehetőségeket, amelyek lehetővé teszik a szolgáltatás méretezhető, nagy felhasználói igényeknek.

 A Service Fabric-fürt létrehozásakor eldöntheti, milyen típusú virtuális gépek (VM) jött létre a fürt. Minden virtuális gép csak korlátozott mennyiségű processzor (magok és sebesség), a hálózati sávszélesség, a RAM-MAL és a lemezes tárolás formájában erőforrásokat tartalmaz. A szolgáltatás adott idő alatt növekedésével frissítheti a virtuális gépekhez, amely nagyobb erőforrásokat kínálnak, illetve további virtuális gépek felvétele a fürt. Ehhez az utóbbi, meg kell tervezővel a szolgáltatás kezdetben, kihasználhatja az új virtuális gépek, amelyek a fürt dinamikusan hozzáadják.

Egyes szolgáltatások a virtuális kevés a nem adatok kezelésében. Ezért ezek a szolgáltatások kapacitástervezését kell összpontosítania elsősorban teljesítményére, ami azt jelenti, hogy a virtuális gépek megfelelő processzor (magok és sebesség) kiválasztása. Emellett érdemes megfontolnia hálózati sávszélesség, beleértve a hálózati átvitel milyen gyakran fordul elő, és mennyi adat átvitele. Ha a szolgáltatás számára szükséges-e, és a szolgáltatás használati növekedése, adhat hozzá további virtuális gépek a fürt és a betöltés egyenleg a hálózati kérelmek, a virtuális gépek között.

A nagy mennyiségű adat a virtuális gépeken kezelő szolgáltatásokra kapacitástervezés kell összpontosítania elsősorban méretét. Ebből kifolyólag alaposan gondolja át RAM a virtuális gép és a lemezes tárolás. Windows virtuális memória felügyeleti rendszer alkalmazáskód RAM tűnjön lemezterület teszi. A Service Fabric-futtatókörnyezet emellett az intelligens lapozás csak gyakran használt adatokkal a memóriában tartja, és helyezze át a ritkán használt adatok lemezre. Alkalmazások így több memóriát a virtuális Gépen fizikailag rendelkezésre állónál használhatják. Egyszerűen több RAM-MAL rendelkező növeli teljesítmény, mivel a virtuális gép további lemezegységet RAM tárolhatja. A kiválasztott virtuális gép elég nagy legyen a kívánt adatokat tárolja a virtuális lemezt kell rendelkeznie. Hasonlóképpen a virtuális gép biztosítja, hogy felügyelni teljesítmény megfelelő mennyiségű RAM memóriával kell rendelkeznie. Ha a szolgáltatás az növekszik adott idő alatt, adhat hozzá további virtuális gépek a fürt és a partíció az adatokat a virtuális gépek között.

## <a name="determine-how-many-nodes-you-need"></a>Határozza meg, hány csomópontok van szüksége
Particionálás a szolgáltatás lehetővé teszi ki a szolgáltatás adatokat. A particionálás további információkért lásd: [particionálás Service Fabric](service-fabric-concepts-partitioning.md). Mindegyik partíció hozzá kell férnie egy virtuális, de több (kisméretű) partíció egy virtuális lehet tenni. Igen, több kis partíciót rendelkező nagyobb rugalmasságot biztosít mint rendelkezik néhány nagyobb partíciókat. A kompromisszum, hogy ha sok partíciók növeli a Service Fabric terhelés, és nem hajtható végre a tranzakciós műveletek közötti partíciók. Emellett további lehetséges a hálózati adatforgalom egy Ha a szolgáltatáskód hibáit gyakran hozzáférésre van szüksége, amely a különböző partíciók élő adatok. A szolgáltatás tervez, alaposan gondolja át ezek és egy hatékony particionálási stratégia elérésének.

Tételezzük fel, hogy az alkalmazás rendelkezik, amely az év DB_Size GB nő várt tároló mérete egyetlen állapotalapú szolgáltatással. A rendszer hozzáadhat további alkalmazásokat (és partíciók), túl az adott év növekedési tapasztal.  A replikációs tényező (RF), amely megadja, hogy a szolgáltatás replikák száma a teljes DB_Size hatással van. A teljes DB_Size összes replika között a replikáció tényező DB_Size megszorozza.  Node_Size a szabad terület/RAM a szolgáltatás használni kívánt csomópontonként jelöli. A legjobb teljesítmény érdekében a DB_Size kell fér a memóriába a fürtön, és egy Node_Size, amely körül a RAM a virtuális gép kell kiválasztani. Egy Node_Size, amely nagyobb, mint a RAM kapacitás kiosztásával a lapozás a Service Fabric-futtatókörnyezet által biztosított vannak hagyatkoznia. Így a nem lehet a teljesítmény optimális, ha a teljes adat tekinthető működés közbeni (ezt követően az adatok van lapozható be- / kimeneti). Azonban számos olyan szolgáltatás, ahol csak töredéke alatt az adatok az működés közbeni, célszerű költséghatékonyabb.

A maximális teljesítmény szükséges csomópontok számának kiszámítása a következőképpen történik:

```
Number of Nodes = (DB_Size * RF)/Node_Size

```


## <a name="account-for-growth"></a>A növekedésre fiók
Érdemes lehet a DB_Size mellett a DB_Size, akkor a megkezdett, nő a szolgáltatás várhatóan alapján csomópontok számának. A csomópontok számát, majd nő, a szolgáltatás növekedésével, hogy a rendszer nem túlzott kiosztása a csomópontok számát. De a partíciók számának alapuljon van szükség, ha a maximális növekedési, amelyen a szolgáltatás csomópontok száma.

Néhány további gépek bármikor elérhető hozzá, így kezelheti bármely váratlan igényeiben jelentkező vagy sikertelen (például, ha néhány virtuális gép leáll) helyes.  A további kapacitást az elvárt teljesítményt segítségével kell meghatározni, amíg kiindulási pontot néhány további virtuális gépek lefoglalni (5 – 10 % időtartammal extra).

Az előző azt feltételezi, hogy egyetlen állapotalapú szolgáltatás. Ha egynél több állapotalapú szolgáltatásból, fel kell vennie a egyenlet be az egyéb szolgáltatásokkal társított DB_Size. Azt is megteheti külön-külön az egyes állapotalapú szolgáltatás csomópontok száma számíthatja ki.  Előfordulhat, hogy a szolgáltatás replikák és a kívánt nem elosztott terhelésű. Ne feledje, hogy partíciók több adat, mint mások is rendelkezhetnek. A particionálás további információkért lásd: [a gyakorlati tanácsok cikk particionálás](service-fabric-concepts-partitioning.md). Azonban az előző egyenlet-e a partícióazonosító és másodpéldány független, mert a Service Fabric biztosítja, hogy a replikákat helyezkednek el csomópontjai között optimalizált módon.

## <a name="use-a-spreadsheet-for-cost-calculation"></a>A táblázat használatához a költségszámítás
Most tegyünk néhány valós számok a képlet. Egy [példa számolótábla](https://servicefabricsdkstorage.blob.core.windows.net/publicrelease/SF%20VM%20Cost%20calculator-NEW.xlsx) szemlélteti a kapacitástervezés az alkalmazás három típusú adatok objektumokat tartalmazó. Az egyes objektumok azt hozzávetőleges mérete és elvárjuk, hogy hány objektumokat. Azt is választhatja azt szeretnénk, ha az egyes objektumtípusoknál hány replikákat. A táblázat a rendszer ne tárolja őket a fürt teljes memóriamennyiség számítja ki.

Azt adja meg a Virtuálisgép-méretet és havi költségét. A virtuális gép mérete alapján, a táblázat azt ismerteti, a minimális számú partíciót az adatokat, hogy a csomópontok fizikailag alkalmas leválasztására kell használnia. Előfordulhat, hogy az alkalmazás adott számítási befogadásához partíciók nagyobb számú felügyelni, és a hálózati forgalmi igényeinek kielégítése. A táblázatban látható, amely kezeli a felhasználói profil objektumok tárolására szolgáló partíciók száma növekedett egy hat.

Most alapján ezt az információt, a táblázatban látható, hogy fizikailag sikerült-e be a kívánt partíciót és replikákat az adatok 26 csomópontos fürt esetén. Azonban a fürt akkor kell sűrűn csomagolt, érdemes lehet néhány további csomópontokat csomópontok hibáit és a frissítések alkalmazásához. A táblázat is mutatja, hogy több mint 57 csomópontok összevonása nincs további értéke, mert üres csomópontok kellene lennie. Ebben az esetben érdemes lehet Ugrás fent 57 csomópontok mégis csomópontok hibáit és a frissítések. A táblázat az alkalmazás igényeinek megfelelően módosíthatja.   

![Számolótáblába, kiszámítása][Image1]

## <a name="next-steps"></a>További lépések
Tekintse meg [particionálás Service Fabric szolgáltatások] [ 10] tudhat meg többet a particionálás a szolgáltatáshoz.

<!--Image references-->
[Image1]: ./media/SF-Cost.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-concepts-partitioning.md
