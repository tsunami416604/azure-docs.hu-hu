---
title: Bevezetés a mikroszolgáltatásokba az Azure-ban
description: Annak áttekintése, hogy miért fontos a felhőalapú alkalmazások mikroszolgáltatási megközelítéssel történő létrehozása a modern alkalmazásfejlesztéshez, és hogy az Azure Service Fabric hogyan biztosít platformot ennek eléréséhez.
ms.topic: conceptual
ms.date: 01/07/2020
ms.custom: sfrev
ms.openlocfilehash: af18a6cb45808c0af5ec2782a3fd2100e3b7bf99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750616"
---
# <a name="why-use-a-microservices-approach-to-building-applications"></a>Miért érdemes mikroszolgáltatásokkal kapcsolatos megközelítést alkalmazni az alkalmazások létrehozásához?

A szoftverfejlesztők számára az alkalmazás összetevő-alkatrészekbe számítása nem újdonság. Általában egy rétegzett megközelítést használnak, egy háttér-áruház, középső szintű üzleti logika és egy előtér-felhasználói felület (UI). Ami *has* az elmúlt években megváltozott, az az, hogy a fejlesztők elosztott alkalmazásokat hoznak a felhőbe.

Íme néhány változó üzleti igény:

* Olyan szolgáltatás, amely et nagy méretekben építettek és üzemeltettek, hogy elérjék az ügyfeleket az új földrajzi régiókban.
* A funkciók és képességek gyorsabb átadása, hogy agilis módon reagáljanak az ügyfelek igényeire.
* Továbbfejlesztett erőforrás-kihasználtság a költségek csökkentése érdekében.

Ezek az üzleti igények befolyásolják az alkalmazások készítésének *módját.*

A mikroszolgáltatások Azure-megközelítéséről további információt a [Mikroszolgáltatások: A felhő által működtetett alkalmazásforradalom című témakörben talál.](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)

## <a name="monolithic-vs-microservices-design-approach"></a>Monolitikus és mikroszolgáltatások tervezési megközelítése

Az alkalmazások idővel fejlődnek. A sikeres alkalmazások úgy fejlődnek, hogy hasznosak az emberek számára. A sikertelen alkalmazások nem fejlődnek, és végül elavultak. Itt a kérdés: mennyit tudsz a követelmények ma, és mi lesz a jövőben? Tegyük fel például, hogy jelentési alkalmazást épít a vállalat egyik részlegéhez. Biztos benne, hogy az alkalmazás csak a vállalat hatókörén belül vonatkozik, és hogy a jelentések nem lesznek sokáig megőrizve. Az Ön megközelítése különbözni fog, mint például egy olyan szolgáltatás létrehozása, amely több tízmillió ügyfélnek nyújt videotartalmat.

Néha, szerzés valami kívül az ajtón, mint a koncepció bizonyítéka a hajtóerő. Tudod, hogy az alkalmazás később újratervezhető. Nincs sok értelme túltervezésnek, amit soha nem használnak fel. Másrészt, amikor a vállalatok a felhőhöz épülnek, az elvárás a növekedés és a használat. A növekedés és a méret kiszámíthatatlan. Gyorsan prototípust akarunk, miközben tudjuk, hogy olyan úton járunk, amely képes kezelni a jövőbeli sikereket. Ez a lean indítási megközelítés: build, intézkedés, tanulni, és iterálni.

Az ügyfél/kiszolgáló korszakában általában a rétegzett alkalmazások kiépítésére összpontosítottunk az egyes szintekben lévő speciális technológiák használatával. A kifejezés *monolitikus* alkalmazás merült fel, hogy leírja ezeket a megközelítéseket. A felületek általában a rétegek között helyezkedtek el, és az egyes rétegeken belüli összetevők között szorosabban összekapcsolt kialakítást használtak. A fejlesztők olyan osztályokat terveztek és faktoráltak, amelyeket könyvtárakba fordítottak, és néhány végrehajtható fájlba és DL-be kapcsoltak.

Vannak előnyei a monolitikus tervezési megközelítés. A monolitikus alkalmazások tervezése gyakran egyszerűbb, és az összetevők közötti hívások gyorsabbak, mivel ezek a hívások gyakran folyamatközi kommunikáción (IPC) keresztül történnek. Is, mindenki tesztel egy termék, amely általában a hatékonyabb felhasználása az emberi erőforrások. A hátránya az, hogy szoros a többszintű rétegek közötti kapcsolat, és nem lehet az egyes összetevőket méretezni. Ha javításokat vagy frissítéseket kell végeznie, meg kell várnia, amíg mások befejezik a tesztelést. Nehezebb agilisnak lenni.

A mikroszolgáltatások megfelelnek ezeknek a hátrányoknak, és jobban igazodnak az előző üzleti követelményekhez. De vannak előnyeik és kötelezettségeik is. A mikroszolgáltatások előnye, hogy mindegyik általában egyszerűbb üzleti funkciókat foglal magában, amelyeket fel- vagy leskálázhat, tesztelhet, üzembe helyezhet és kezelhet egymástól függetlenül. A mikroszolgáltatások megközelítésének egyik fontos előnye, hogy a csapatokat inkább az üzleti forgatókönyvek vezérlik, mint a technológia. Kisebb csapatok egy ügyfél-forgatókönyv alapján fejlesztenek mikroszolgáltatást, és bármilyen technológiát használnak, amelyet használni szeretnének.

Más szóval a szervezetnek nem kell szabványosítania a technológiát a mikroszolgáltatási alkalmazások karbantartásához. A saját szolgáltatásokat nyújtó egyéni csapatok a csapatszakértelem vagy a probléma megoldásához legmegfelelőbb információk alapján azt tehetik, ami számukra értelmet nyer. A gyakorlatban az ajánlott technológiák, például egy adott NoSQL áruház vagy webalkalmazás-keretrendszer, előnyösebb.

A mikroszolgáltatások hátránya, hogy több különálló entitást kell kezelnie, és összetettebb telepítések és verziószámozás kezelésére kell foglalkoznia. A mikroszolgáltatások közötti hálózati forgalom nő, csakúgy, mint a megfelelő hálózati késések. Sok beszédes, szemcsés szolgáltatások okozhat a teljesítmény rémálom. A függőségek megtekintéséhez szükséges eszközök nélkül nehéz az egész rendszert látni.

A szabványok a mikroszolgáltatások megközelítését úgy működnek, hogy meghatározzák a kommunikáció módját, és csak azokat a dolgokat tolerálják, amelyekre egy szolgáltatásból van szüksége, nem pedig merev szerződésekre. Fontos, hogy ezeket a szerződéseket előre határozza meg a tervben, mert a szolgáltatások egymástól függetlenül frissülnek. Egy másik leírás, amelyet a mikroszolgáltatások megközelítésével történő tervezéshez alkottak meg, a "részletes szolgáltatásorientált architektúra (SOA)."

***A legegyszerűbb, a mikroszolgáltatások tervezési megközelítés körülbelül egy független összevonása szolgáltatások, független változások minden és a megállapodás szerinti kommunikációs szabványok.***

Ahogy egyre több felhőalapú alkalmazás készül, az emberek felfedezték, hogy az általános alkalmazás független, forgatókönyv-központú szolgáltatásokra történő lebontása jobb hosszú távú megközelítés.

## <a name="comparison-between-application-development-approaches"></a>Alkalmazásfejlesztési megközelítések összehasonlítása

![A Service Fabric platformalkalmazás-fejlesztés][Image1]

1) A monolitikus alkalmazások tartományspecifikus funkciókat tartalmaznak, és általában funkcionális rétegekre, például webre, üzleti és adatra oszthatók.

2) Egy monolitikus alkalmazást több kiszolgálón/virtuális gépen/tárolón klónozva méretezhet.

3) A mikroszolgáltatás-alkalmazások külön kisebb szolgáltatásokra bontják a funkciókat.

4) A mikroszolgáltatások megközelítés horizontális felskálázása az egyes szolgáltatások egymástól függetlenül üzembe helyezésével, a szolgáltatások példányainak létrehozása kiszolgálók/virtuális gépek/tárolók között.

Mikroszolgáltatási megközelítéssel történő tervezés nem minden projekt számára megfelelő, de jobban igazodik a korábban ismertetett üzleti célokhoz. A monolitikus megközelítéssel való indítással lehet értelme, ha tudja, hogy később lehetősége lesz a kód mikroszolgáltatások tervezésére való átdolgozására. Gyakrabban, akkor kezdődik egy monolitikus alkalmazás, és lassan szakítani szakaszokban, kezdve a funkcionális területeken, hogy kell több skálázható vagy agilis.

Mikroszolgáltatási megközelítés használata esetén számos kisebb szolgáltatás összeállításához kell írnia az alkalmazást. Ezek a szolgáltatások olyan tárolókban futnak, amelyek egy gépfürtön vannak telepítve. Kisebb csapatok olyan szolgáltatást fejlesztenek ki, amely egy forgatókönyvre összpontosít, és egymástól függetlenül teszteli, verzió, üzembe helyezi és méretezi az egyes szolgáltatásokat, hogy a teljes alkalmazás fejlődhessen.

## <a name="what-is-a-microservice"></a>Mik azok a mikroszolgáltatások?

A mikroszolgáltatások különböző definíciói vannak. A mikroszolgáltatások legtöbb ilyen tulajdonsága azonban széles körben elfogadott:

* Ügyfél vagy üzleti forgatókönyv beágyazása. Milyen problémát oldasz meg?
* Egy kis mérnöki csapat fejlesztette ki.
* Bármilyen programozási nyelven íródott, bármilyen keretrendszer használatával.
* Kódból és opcionálisan állapotból áll, mindkettő egymástól függetlenül verziózott, üzembe helyezett és méretezett.
* Kommunikáljon más mikroszolgáltatásokkal jól definiált felületeken és protokollokon keresztül.
* A tartózkodási helyük feloldásához egyedi nevek (URL-címek) tartoznak.
* Maradjon konzisztens és elérhető a hibák jelenlétében.

Összefoglalva, hogy a következő:

***A mikroszolgáltatás-alkalmazások kisméretű, egymástól független verziójú és méretezhető ügyfélközpontú szolgáltatásokból állnak, amelyek jól definiált felületekkel rendelkező szabványos protokollokon keresztül kommunikálnak egymással.***

### <a name="written-in-any-programming-language-using-any-framework"></a>Bármilyen programozási nyelven íródott, bármilyen keretrendszer használatával

Fejlesztőkként szabadon szeretnénk választani egy nyelvet vagy keretrendszert, a ttól függően, hogy milyen készségeket és az általunk létrehozott szolgáltatás igényeit. Egyes szolgáltatások esetében a C++ teljesítményelőnyeit minden másnál jobban értékelheti. Mások számára a C# vagy a Java által kapott egyszerű felügyelt fejlesztés fontosabb lehet. Bizonyos esetekben előfordulhat, hogy egy adott partnerkönyvtárat, adattárolási technológiát vagy módszert kell használnia a szolgáltatás ügyfelek számára való kihelyezéséhez.

Miután kiválasztotta a technológiát, figyelembe kell vennie a szolgáltatás működési vagy életciklus-kezelését és méretezését.

### <a name="allows-code-and-state-to-be-independently-versioned-deployed-and-scaled"></a>Lehetővé teszi a kód és az állapot egymástól független verziókba való befuttatását, üzembe helyezését és méretezését

Nem számít, hogyan írja a mikroszolgáltatások, a kód, és adott esetben az állapot, egymástól függetlenül kell telepíteni, frissíteni és skálázni. Ezt a problémát nehéz megoldani, mert jön le, hogy a választott technológiák. A skálázás, a kód és a szegmens is kihívást jelent a kód és az állapot particionálásának (vagy shard) szerinti felosztása. Ha a kód és az állapot különböző technológiákat használ, ami ma gyakori, a mikroszolgáltatás központi telepítési parancsfájljainak mindkettőt skálázhatnak. Ez a szétválasztás is az agilitásról és a rugalmasságról szól, így a mikroszolgáltatások egy részét anélkül frissítheti, hogy egyszerre kellene frissítenie őket.

Térjünk vissza a monolitikus és mikroszolgáltatási megközelítések összehasonlításához egy pillanatra. Ez az ábra az állapotok tárolásának módszereiközötti különbségeket mutatja be:

#### <a name="state-storage-for-the-two-approaches"></a>A két megközelítés állami tárolása

![Service Fabric platformállapot-tárolása][Image2]

***A monolitikus megközelítés, a bal oldalon, egyetlen adatbázis és a szintek az egyes technológiák.***

***A mikroszolgáltatások megközelítés, a jobb oldalon, rendelkezik egy grafikont az összekapcsolt mikroszolgáltatások, ahol az állapot általában hatóköre a mikroszolgáltatás és a különböző technológiák használata.***

Monolitikus megközelítésben az alkalmazás általában egyetlen adatbázist használ. Az egyik adatbázis használatának előnye, hogy egyetlen helyen található, ami megkönnyíti a telepítést. Minden összetevő nek lehet egyetlen táblája az állapot ának tárolására. A csapatoknak szigorúan el kell különíteni az államot, ami kihívást jelent. Elkerülhetetlen, hogy valaki lesz a kísértés, hogy adjunk egy oszlopot egy meglévő vevőtábla, csinál egy illesztés táblák között, és hozzon létre függőségeket a tárolási rétegben. Miután ez megtörténik, nem méretezheti az egyes összetevőket.

A mikroszolgáltatások megközelítés, minden szolgáltatás kezeli és tárolja a saját állapotát. Minden szolgáltatás felelős a kód és az állapot együtt skálázása a szolgáltatás követelményeinek megfelelően. Hátránya, hogy ha az alkalmazás adatainak nézeteket vagy lekérdezéseket kell létrehoznia, több állapottárolóban kell lekérdezést lekérdeznie. Ezt a problémát általában egy külön mikroszolgáltatás oldja meg, amely egy nézetet hoz létre a mikroszolgáltatások gyűjteményében. Ha több rögtönzött lekérdezést kell futtatnia az adatokon, fontolja meg az egyes mikroszolgáltatások adatainak írását egy adattárház-szolgáltatásba offline elemzéshez.

A mikroszolgáltatások verziószámozottak. A mikroszolgáltatás különböző verziói egymás mellett futtathatók. A mikroszolgáltatás újabb verziója sikertelen lehet a frissítés során, és vissza kell állíthatóegy korábbi verzióra. A verziószámozás az A/B teszteléshez is hasznos, ahol a különböző felhasználók a szolgáltatás különböző verzióit tapasztalják. Gyakori például egy mikroszolgáltatás frissítése az ügyfelek egy adott készletéhez, hogy tesztelje az új funkciókat, mielőtt szélesebb körben bevezetné.

### <a name="interacts-with-other-microservices-over-well-defined-interfaces-and-protocols"></a>Más mikroszolgáltatásokkal jól definiált felületeken és protokollokon keresztül kommunikál

Az elmúlt 10 évben kiterjedt információk jelentek meg a szolgáltatásorientált architektúrák kommunikációs mintáiról. A szolgáltatáskommunikáció általában REST-megközelítést használ, amelynek http- és TCP-protokolljai, valamint XML vagy JSON a szerializálási formátum. Egy felület szempontjából, ez körülbelül vesz egy web design megközelítés. De semmi sem akadályozhatja meg abban, hogy bináris protokollokat vagy saját adatformátumokat használjon. Ne feledje, hogy az embereknek nehezebb lesz a mikroszolgáltatások használata, ha ezek a protokollok és formátumok nem érhetők el nyíltan.

### <a name="has-a-unique-name-url-used-to-resolve-its-location"></a>A hely feloldásához egyedi nevet (URL-címet) használ

A mikroszolgáltatásnak megcímezhetőnek kell lennie, bárhol is fusson. Ha a gépeken gondolkodik, és egy adott mikroszolgáltatást futtat, a dolgok gyorsan elromolhatnak.

Ugyanúgy, ahogy a DNS feloldja egy adott URL-t egy adott gépre, a mikroszolgáltatásnak egyedi névre van szüksége, hogy az aktuális helye felderíthető. A mikroszolgáltatásoknak olyan címezhető nevekre van szükségük, amelyek függetlenek attól az infrastruktúrától, amelyen futnak. Ez azt jelenti, hogy a szolgáltatás üzembe helyezése és a felderítése között interakció van, mert szükség van egy szolgáltatás-beállításjegyzékre. Ha egy gép meghibásodik, a beállításjegyzék-szolgáltatásnak meg kell mondania, hogy hová helyezték át a szolgáltatást.

### <a name="remains-consistent-and-available-in-the-presence-of-failures"></a>Továbbra is következetes és rendelkezésre áll a hibák jelenlétében

A váratlan hibák kezelése az egyik legnehezebben megoldandó probléma, különösen egy elosztott rendszerben. Sok a kód, hogy írunk, mint a fejlesztők kezelésére kivételek. A tesztelés során a legtöbb időt a kivételkezeléssel is eltöltjük. A folyamat több szó, mint a kód írása a hibák kezelésére. Mi történik, ha a gép, amelyen a mikroszolgáltatás fut, sikertelen? Meg kell észlelni a hibát, ami egy nehéz probléma a saját. De azt is újra kell indítania a mikroszolgáltatás.

A rendelkezésre állás hoz a mikroszolgáltatás kell rugalmas hibák, és képes újraindítani egy másik gépen. Ezeken a rugalmassági követelményeken kívül az adatok nem veszhetnek el, és az adatoknak konzisztensnek kell maradniuk.

Rugalmasság nehéz elérni, ha hibák történnek az alkalmazás frissítése során. A mikroszolgáltatás, a központi telepítési rendszerrel együttműködve, nem kell helyreállítani. Meg kell határoznia, hogy tovább léphet-e az újabb verzióra, vagy visszaállíthatja-e egy korábbi verziót a konzisztens állapot fenntartása érdekében. Meg kell vizsgálnia néhány kérdést, például, hogy elegendő gép áll-e rendelkezésre a továbblépéshez, és hogyan állíthatja helyre a mikroszolgáltatás korábbi verzióit. Ezeka döntések meghozatalához szükség van a mikroszolgáltatás egészségügyi információk.

### <a name="reports-health-and-diagnostics"></a>Jelentések az egészség ről és a diagnosztikáról

Úgy tűnhet, nyilvánvaló, és gyakran figyelmen kívül hagyják, de a mikroszolgáltatás kell jelentenie az állapotát és a diagnosztika. Ellenkező esetben kevés betekintést az egészségre egy művelet szempontjából. A diagnosztikai események korrelálása független szolgáltatások készletében, és a gépóra-döntésekkel való kapcsolat az eseménysorrend hez kihívást jelent. Ugyanúgy, ahogy a mikroszolgáltatás a megállapodás szerinti protokollok és adatformátumok, egységesítenie kell, hogyan naplózza az állapot-és diagnosztikai események, amelyek végül a végén egy eseménytároló lekérdezése és megtekintése. A mikroszolgáltatások megközelítés, a különböző csapatok nak meg kell állapodniuk egy naplózási formátumban. Szükség van egy konzisztens megközelítés az alkalmazás egészének diagnosztikai események megtekintéséhez.

Az egészség eltér a diagnosztikától. Az állapot a mikroszolgáltatásról szól, amely a megfelelő műveletek hez a jelenlegi állapotát jelenti. Jó példa a frissítési és üzembe helyezési mechanizmusokkal a rendelkezésre állás fenntartása. Bár egy szolgáltatás jelenleg nem kifogástalan állapotú egy folyamat összeomlása vagy a számítógép újraindítása miatt, a szolgáltatás továbbra is működőképes lehet. Az utolsó dolog, amire szüksége van, hogy a helyzet rosszabb a kezdő frissítést. A legjobb megközelítés az, hogy vizsgálja meg az első, vagy időt a mikroszolgáltatás helyreállítása. A mikroszolgáltatás egészségügyi eseményei segítenek megalapozott döntéseket hozni, és valójában öngyógyító szolgáltatásokat létrehozni.

## <a name="guidance-for-designing-microservices-on-azure"></a>Útmutató a mikroszolgáltatások Azure-beli tervezéséhez

Az Azure architektúrás központjában útmutatást talál az [Azure-beli mikroszolgáltatások tervezéséhez és létrehozásához.](https://docs.microsoft.com/azure/architecture/microservices/)

## <a name="service-fabric-as-a-microservices-platform"></a>Szolgáltatás fabric, mint egy mikroszolgáltatási platform

Az Azure Service Fabric akkor jelent meg, amikor a Microsoft áttért a dobozos termékek – amelyek jellemzően monolitikus – szállításáról a szolgáltatások nyújtására. A nagy szolgáltatások, például az Azure SQL Database és az Azure Cosmos DB létrehozásának és üzemeltetésének élménye a Service Fabric alakú. A platform idővel fejlődött, ahogy egyre több szolgáltatás fogadta el. A Service Fabricnek nem csak az Azure-ban, hanem önálló Windows-kiszolgáló-telepítésekben is futnia kellett.

***A Service Fabric célja, hogy megoldja a szolgáltatás létrehozásának és futtatásának nehéz problémáit, és hatékonyan használja az infrastruktúra-erőforrásokat, így a csapatok mikroszolgáltatási megközelítéssel oldhatják meg az üzleti problémákat.***

A Service Fabric segítségével mikroszolgáltatási megközelítést használó alkalmazásokat hozhat létre a következők biztosításával:

* Olyan platform, amely rendszerszolgáltatásokat biztosít a sikertelen szolgáltatások telepítéséhez, frissítéséhez, észleléséhez és újraindításához, szolgáltatások felderítéséhez, üzenetek továbbításához, állapotkezeléshez és állapotfigyeléshez.
* A tárolókban vagy folyamatokban futó alkalmazások központi telepítése. A Service Fabric egy tároló és folyamat orchestrator.
* Hatékony programozási API-k az alkalmazások mikroszolgáltatásokként való létrehozásához: [ASP.NET Core, Reliable Actors és Reliable Services.](service-fabric-choose-framework.md) Például kaphat állapot- és diagnosztikai információkat, vagy kihasználhatja a beépített magas rendelkezésre állás előnyeit.

***A Service Fabric független aszolgáltatás felépítéséről, és bármilyen technológiát használhat. De ez biztosítja a beépített programozási API-k, amelyek megkönnyítik a mikroszolgáltatások létrehozása.***

### <a name="migrating-existing-applications-to-service-fabric"></a>Meglévő alkalmazások áttelepítése a Service Fabric-be

A Service Fabric lehetővé teszi a meglévő kód újrafelhasználását, és új mikroszolgáltatásokkal modernizálni. Az alkalmazás modernizálásának öt szakasza van, és bármely szakaszban elkezdheti és megállíthatja. A szakaszok a következők:

1) Kezdje egy hagyományos monolitikus alkalmazással.  
2) Áttelepítése. Tárolók vagy vendég végrehajtható fájlok a Service Fabric meglévő kód üzemeltetéséhez.  
3) Modernizálják. Új mikroszolgáltatások hozzáadása a meglévő tárolókód mellett.  
4) Innovációra. A monolitikus alkalmazás felosztása a mikroszolgáltatások igény alapján.  
5) Az alkalmazásokat mikroszolgáltatásokká alakíthatja. Alakítsa át a meglévő monolitikus alkalmazásokat, vagy hozzon létre új zöldmezős alkalmazásokat.

![Mikroszolgáltatásokra való áttérés][Image3]

Ne feledje, akkor *kezdődik, és megáll bármelyik szakaszában*. Nem kell továbbjutnod a következő szintre. 

Nézzünk példákat az egyes szakaszok.

**Áttelepítése**  
Két okból sok vállalat a meglévő monolitikus alkalmazásokat telepíti tárolókba:

* Költségcsökkentés, akár a meglévő hardver konszolidációja és eltávolítása, akár a nagyobb sűrűségű futó alkalmazások miatt.
* A fejlesztés és a műveletek közötti konzisztens üzembe helyezési szerződés.

A költségcsökkentés egyszerű. A Microsoftnál számos meglévő alkalmazást konténereznek, ami több millió dolláros megtakarítást eredményez. Konzisztens üzembe helyezés nehezebb kiértékelni, de ugyanolyan fontos. Ez azt jelenti, hogy a fejlesztők kiválaszthatják a nekik megfelelő technológiákat, de a műveletek csak egyetlen módszert fogadnak el az alkalmazások üzembe helyezéséhez és kezeléséhez. Enyhíti a műveleteket attól, hogy a különböző technológiák támogatásának összetettségével kelljen foglalkozniuk anélkül, hogy arra kényszerezne a fejlesztőkszámára, hogy csak bizonyos technológiákat válasszanak. Lényegében minden alkalmazás önálló központi telepítési lemezképektárolóba.

Sok szervezet megáll itt. Már rendelkeznek a tárolók előnyeit, és a Service Fabric biztosítja a teljes felügyeleti élményt, beleértve a központi telepítés, a frissítések, verziószámozás, visszaállítások és az állapotfigyelés.

**Modernizálják**  
A modernizáció új szolgáltatások hozzáadása a meglévő tárolókód mellett. Ha új kódot szeretne írni, a legjobb, ha kis lépéseket tesz a mikroszolgáltatások elérési útján. Ez azt jelentheti, hogy új REST API-végpontot vagy új üzleti logikát ad hozzá. Ily módon új mikroszolgáltatások létrehozásának és üzembe helyezésének folyamata.

**Újítás**  
A mikroszolgáltatások megközelítése alkalmazkodik a változó üzleti igényeket. Ebben a szakaszban el kell döntenie, hogy elkezdi-e a monolitikus alkalmazás szolgáltatásokra bontását vagy az innovációt. Klasszikus példa erre, amikor egy munkafolyamat-várólistaként használt adatbázis feldolgozási szűk keresztmetszetté válik. A munkafolyamat-kérelmek számának növekedésével a munkát el kell osztani a méretezéshez. Vegye ki az alkalmazás adott darabját, amely nem méretezés, vagy amelyet gyakrabban kell frissíteni, és ossza fel mikroszolgáltatásként és innovációként.

**Alkalmazások átalakítása mikroszolgáltatásokká**  
Ebben a szakaszban az alkalmazás teljes mértékben áll (vagy felosztott) mikroszolgáltatások. Ahhoz, hogy ezt a pontot, akkor megtette a mikroszolgáltatások utazás. Itt is elkezdheti, de ehhez egy mikroszolgáltatási platform nélkül, amely segít jelentős befektetést igényel.

### <a name="are-microservices-right-for-my-application"></a>A mikroszolgáltatások megfelelőek az alkalmazásomhoz?

Talán. A Microsoftnál, ahogy egyre több csapat kezdett építeni a felhőszámára üzleti okokból, sokan felismerték a mikroszolgáltatás-szerű megközelítés előnyeit. A Bing például évek óta használ mikroszolgáltatásokat. Más csapatok számára a mikroszolgáltatások megközelítése új volt. A csapatok úgy találták, hogy nehéz problémákat kell megoldani uk fő erőn kívüli területein kívül. Ez az oka annak, hogy a Service Fabric vontatási lehetőségeket szerzett az építési szolgáltatások technológiájaként.

A Service Fabric célja, hogy csökkentse a mikroszolgáltatási alkalmazások létrehozásának bonyolultságát, így nem kell annyi költséges átalakításon keresztülmennie. Indítsa el a kisméreteket, szükség esetén méretezhető, minősítse a szolgáltatásokat, adjon hozzá újakat, és fejlődjön az ügyfélhasználattal. Azt is tudjuk, hogy sok más probléma még meg kell oldani, hogy a mikroszolgáltatások jobban megközelíthető a legtöbb fejlesztő számára. A tárolók és az aktor programozási modell példák az ebbe az irányba tett kis lépésekre. Biztosak vagyunk benne, hogy több újítás fog megjelenni, hogy megkönnyítse a mikroszolgáltatási megközelítést.

## <a name="next-steps"></a>További lépések

* [Mikroszolgáltatások: A felhő által működtetett alkalmazásforradalom](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)
* [Azure Architecture Center: Mikroszolgáltatások létrehozása az Azure-ban](https://docs.microsoft.com/azure/architecture/microservices/)
* [Az Azure Service Fabric-alkalmazás és a fürt ajánlott eljárásai](service-fabric-best-practices-overview.md)
* [A Service Fabric terminológia áttekintése](service-fabric-technical-overview.md)

[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png
[Image3]: media/service-fabric-overview-microservices/microservices-migration.png
