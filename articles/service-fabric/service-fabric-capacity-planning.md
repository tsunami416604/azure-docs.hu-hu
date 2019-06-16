---
title: Kapacitás megtervezése a Service Fabric-alkalmazások |} A Microsoft Docs
description: Ismerteti, hogyan lehet a Service Fabric-alkalmazás szükséges számítási csomópontok számának meghatározása
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: markfuss
editor: ''
ms.assetid: 9fa47be0-50a2-4a51-84a5-20992af94bea
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: d7ca566b86ed79aa773d7af2553223c79ed9944a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64701841"
---
# <a name="capacity-planning-for-service-fabric-applications"></a>Kapacitás megtervezése a Service Fabric-alkalmazások
Ez a dokumentum bemutatja, hogyan megbecsülni az Azure Service Fabric-alkalmazások futtatásához szükséges erőforrásokat (processzorok, memória, lemezes tárolás) mennyisége. Az az idő előrehaladtával változik az erőforrásigények szokás. Néhány információforrást, általában szükség van, a szolgáltatás fejlesztés és tesztelés, és éles be, és az alkalmazás növekszik kezdenek népszerűvé válni robotjai, majd több erőforrást igényel. Az alkalmazás tervezésekor gondolja, hogy a hosszú távú követelményekért, és döntéseket, amelyek a szolgáltatás magas ügyfél igény szerint méretezhető.

 Service Fabric-fürt létrehozásakor eldöntheti, milyen típusú virtuális gépeken (VM) a fürtöt alkotó. Minden virtuális gép csak korlátozott mennyiségű processzor (magok és sebesség), a hálózati sávszélesség, a RAM-MAL és a lemezes tárolás formájában erőforrásokat tartalmaz. A szolgáltatás növekedésével idővel frissítheti a virtuális gépeket, amelyek nagyobb erőforrásokat kínálnak, illetve további virtuális gépeket ad hozzá a fürthöz. Az utóbbi esetben meg kell tervezhet a szolgáltatás kezdetben, az új virtuális gépeket, amelyek dinamikusan hozzáadja a fürthöz előnyeit is igénybe vehet.

Egyes szolgáltatások kezelheti a nem a virtuális gépekből a kevés adattal. Ezért ezek a szolgáltatások kapacitástervezése irányul, elsősorban a teljesítményt, ami azt jelenti, hogy a megfelelő processzor (magok és sebesség) a virtuális gépek kiválasztása. Emellett érdemes lehet hálózati sávszélességet, beleértve a hálózati adatátvitel milyen gyakran fordul elő, és átvitt adatok mennyiségét. Ha a szolgáltatás kell végeznie, valamint a szolgáltatás használatának növekedése, adhat hozzá további virtuális gépeket a fürt és a terhelés egyenleget, a hálózati kérelmek, a virtuális gép között.

Olyan szolgáltatásokhoz, amelyek nagy mennyiségű adatot a virtuális gépeken kapacitástervezés kell koncentrálnak méretét. Alaposan fontolja meg a virtuális gép RAM-MAL és a lemez tárolási kapacitását. Windows virtuális memória felügyeleti rendszer lehetővé teszi a lemezterület RAM kinézni az alkalmazás kódjában. Emellett a Service Fabric-futtatókörnyezet biztosít az intelligens lapozás csak a gyakori elérésű adatok megőrzése mellett a memóriában, és a ritkán használt adatok áthelyezése a lemez. Alkalmazások ily módon használhatják a fizikailag rendelkezésre áll a virtuális gépen több memóriára. Több RAM Memóriát kellene egyszerűen növeli a teljesítményt, lehetőséget, mivel a virtuális gép RAM megmaradhat további lemezterület. A kiválasztott virtuális gép elég nagy az adatokat, amelyeket szeretne tárolja a virtuális lemezt kell rendelkeznie. Ehhez hasonlóan a virtuális gép a felhasználók által igényelt teljesítményt biztosítson elegendő RAM memóriával kell rendelkeznie. Ha a szolgáltatás az adatmennyiség az idő függvényében, adhat hozzá további virtuális gépeket a fürt és az adatok particionálására a virtuális gép között.

## <a name="determine-how-many-nodes-you-need"></a>Hány csomóponttal kell meghatározni.
Particionálás a szolgáltatás lehetővé teszi a horizontális felskálázás a szolgáltatás adatokat. A particionálás további információkért lásd: [Service Fabric particionálási](service-fabric-concepts-partitioning.md). Mindegyik partíció hozzá kell férnie egyetlen virtuális Gépet, de több (kisméretű) partíció helyezhető egyetlen virtuális géphez. Így több kis partíció nagyobb rugalmasságot biztosít, mint a néhány nagyobb partíció. Kompromisszumot kötni, hogy a partíciók sok növeli a Service Fabric-terhelést, és nem hajtható végre a tranzakciós műveletek partíciók között. Nincs több lehetséges hálózati forgalmat, ha a szolgáltatás kód gyakran van szükség, amely a különböző partíciók élő adatok eléréséhez. A szolgáltatás tervezésekor alaposan fontolja meg és a egy hatékony particionálási stratégia elérésének hátrányai.

Tegyük fel, az alkalmazás egyetlen állapotalapú szolgáltatás, amely egy tároló mérete, hogy DB_Size GB-os év a várt rendelkezik. A rendszer hozzáadhat további alkalmazásokat (és partíciók), túl az adott év növekedés tapasztalható.  A replikációs tényező (RF), amely megadja, hogy a szolgáltatás replikák száma befolyásolja a teljes DB_Size. A teljes DB_Size összes replika között a replikációs tényező DB_Size szorzata.  Node_Size a lemez lemezterület és a RAM a szolgáltatáshoz használni kívánt csomópontonként jelöli. A legjobb teljesítmény érdekében a DB_Size kell fér a memóriába a fürtön, és a egy Node_Size, amely körül a RAM a virtuális gép ki kell választani. Foglalhatja le a Node_Size, amely nagyobb, mint a RAM-MAL kapacitás, a Service Fabric-futtatókörnyezet által biztosított lapozási vannak hagyatkoznia. Ezért a teljesítmény nem feltétlenül optimális, ha az összes adatát akkor minősül forró (ezt követően az adatok a lapozható be/ki). Azonban számos olyan szolgáltatás, ahol az adatok egy részét az gyakran használt adatok, fontos helyett.

A maximális teljesítmény szükséges csomópontok számának kiszámítása a következőképpen történik:

```
Number of Nodes = (DB_Size * RF)/Node_Size

```


## <a name="account-for-growth"></a>Figyelembe venni a növekedést
Érdemes a számítási csomópontok az, hogy, mellett a DB_Size, amely már a szolgáltatás várhatóan az DB_Size alapján. Ezt követően növelje a csomópontok számát, a szolgáltatást, hogy a rendszer nem túlzott kiosztása csomópontok számának növekedésével. Azonban a partíciók számának van szükség, ha a szolgáltatás futtatja, a legnagyobb növekedés csomópontok száma alapján.

A jó néhány bármikor elérhető további gépekkel rendelkezik, így kezelheti minden olyan váratlan megnövekedésére vagy sikertelen (például, ha néhány virtuális gépet).  Kapacitás a várt adatforgalmi csúcsokhoz használatával kell meghatározni, amíg az kiindulási pontot, hogy néhány további virtuális gépek lefoglalása (5 – 10 %-os extra).

Az előző feltételezi, hogy egyetlen állapotalapú szolgáltatás. Ha egynél több állapotalapú szolgáltatásból, fel kell vennie a DB_Size rájöttünk, az az egyéb szolgáltatásokkal társított. Másik lehetőségként képes számítási csomópontok külön-külön az egyes állapotalapú szolgáltatás.  Előfordulhat, hogy a szolgáltatás replikákat és partíciókat, amely nem elosztott terhelésű. Ne feledje, hogy a partíciók is rendelkezhetnek, mint a többi több adatot. A particionálás további információkért lásd: [particionálása a gyakorlati tanácsok cikk](service-fabric-concepts-partitioning.md). Azonban az előző képlete a partíció- és a replika független, mert a Service Fabric biztosítja, hogy a replikák szétterítik mozgatja a csomópontok optimalizált módon.

## <a name="use-a-spreadsheet-for-cost-calculation"></a>Egy táblázat celláira használja a költségek kiszámításához
Most állítsuk munkába a képletben néhány valós számmá. Egy [példa számolótábla](https://servicefabricsdkstorage.blob.core.windows.net/publicrelease/SF%20VM%20Cost%20calculator-NEW.xlsx) szemlélteti a kapacitástervezés három típusú objektumok tartalmazó alkalmazást. Minden objektum esetén azt hozzávetőleges mérete és szeretné, hogy várhatóan hány objektumot. Azt is kiválaszthatja, minden egyes objektumtípus szeretnénk hány replikákat. A táblázatban tárolja a fürt teljes memóriamennyiség számítja ki.

Azt adja meg a virtuális gép mérete és a havi költségeket. A virtuális gép mérete alapján, a táblázat kiderül, hogy a partíciók kell használni az adatok fizikailag kell férnie a csomópontok minimális száma. Amennyit csak lehet, hogy szeretne nagyobb számú partíciót megfeleljen az alkalmazás adott számítási és hálózati forgalom van szüksége. A táblázatban látható, amely kezeli a felhasználói profil objektumok partíciók száma növekedett, egy hat és.

Most ez az információ alapján, a táblázat jeleníti meg, hogy fizikailag sikerült-e be a kívánt partíciót és replikákat az adatok egy 26-csomópontból álló fürtön. Azonban a fürt lenne kell osztozniuk csomagolt, ezért érdemes néhány további csomópontokat csomóponthibák és frissítéseket. A táblázat is mutatja, hogy több mint 57 csomópontok kellene biztosít további értéket nem mert üres csomópontok kell. Újra előfordulhat, hogy szeretne belépni 57 csomópont feletti ennek ellenére csomóponthibák és frissítéseket. A táblázat az alkalmazás igényeinek megfelelően módosíthat.   

![A költségek kiszámításához táblázat][Image1]

## <a name="next-steps"></a>További lépések
Tekintse meg [particionálás Service Fabric-szolgáltatások] [ 10] tudhat meg többet a szolgáltatás particionálás.

<!--Image references-->
[Image1]: ./media/SF-Cost.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-concepts-partitioning.md
