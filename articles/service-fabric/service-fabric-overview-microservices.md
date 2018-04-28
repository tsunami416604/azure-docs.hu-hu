---
title: Bevezetés az Azure mikroszolgáltatások |} Microsoft Docs
description: Miért egy mikroszolgáltatások módszert használja a felhőalapú alkalmazások számára fontos a modern alkalmazások fejlesztését, és hogyan Azure Service Fabric ennek érdekében platformot biztosít áttekintése.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: fae2be85-0ab4-4cd3-9d1f-e0d95fe1959b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/02/2017
ms.author: msfussell
ms.openlocfilehash: d56b78a0274842c012e57f80f0c2fe5619a62962
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="why-a-microservices-approach-to-building-applications"></a>Miért egy mikroszolgáltatások közelítik meg az alkalmazások?
Szoftverfejlesztők, mert jelenleg nincs újdonságunk a ahogyan szerintünk kapcsolatos faktoring egy alkalmazás összetevő részre. A központi paradigma objektum tájolás, a szoftver absztrakt entitással egészült ki, és a componentization. Napjainkban a factorization általában osztályok és a megosztott szalagtárakkal és technológiai réteg kapcsolódási formájában. Általában a rétegzett megközelítés használatban van egy háttér-tároló, középszintű üzleti logika és előtér felhasználói felület (UI). Mi *rendelkezik* változások az elmúlt néhány évben, hogy azt, mint a fejlesztők számára által létrehozott elosztott alkalmazások, amelyek a felhőben és a vállalat által vezérelt.

A változó üzleti igényeinek a következők:

* A szolgáltatás épül, és működik, a méretezés új földrajzi régióban ügyfelek (például) elérésére.
* Funkciók és képességek tudni válaszolni a felhasználói igények gyors módon gyorsabb kézbesítését.
* Továbbfejlesztett erőforrás-használat költségek csökkentése érdekében.

Ezek az üzleti igények is érinthetik *hogyan* azt olyan alkalmazásokat hozhat létre.

Az Azure-mikroszolgáltatások az módszerrel kapcsolatos további információkért olvassa el a [Mikroszolgáltatások: egy alkalmazás fordulat a felhő technológiával](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="monolithic-vs-microservice-design-approach"></a>Mikroszolgáltatási tervezett módszert alkalmaz és egységes
Minden alkalmazás verzióinformációk. Sikeres alkalmazásokat fejleszteni őket hasznos személyek számára. Sikertelen kérelmek nem fejlődnek, és végül elavultak. A kérdés válik: mennyi ismeri a követelményekkel kapcsolatos ma, és mi ezek lesznek a jövőben? Tételezzük fel például, hogy egy-egy részleg jelentéskészítési alkalmazást fejleszt. Biztos, hogy az alkalmazás továbbra is a vállalat hatókörén belül, és, hogy a jelentések megtalálhatók a rövid élettartamú. A választott módszer eltér, tegyük fel például, videotartalom felépítése egy szolgáltatás, amely továbbítja az ügyfelek tízezer. 

Egyes esetekben első valamit az ajtó ki, a koncepció igazolása vezetői tényező, amíg az alkalmazás újabb újratervezett tudja. Nincs kevés pontot túlzott mérnöki valami soha nem leggyakrabban használt. A szokásos mérnöki kompromisszum. Másrészről vállalatok szolgáltatással kapcsolatban a felhő létrehozása, amikor az elvárás növekedés és használat. A probléma oka, hogy a növekedési és a skála előre nem látható. Szeretnénk tudni prototípus gyorsan is, hogy azt egy jövőbeli sikeres kezelésére elérési úton vannak tudatában. Ez a megközelítés lean indítási: build, mérik, ismerje meg, és többször.

Az ügyfél-kiszolgáló era során azt arra irányult az rétegzett alkalmazások létrehozásához az egyes rétegek egyes technológiák használatával összpontosíthat. A kifejezés *egységes* alkalmazás mutatkozott az ezen módszerek. Azok a felületek arra irányult, a rétegek között kell lennie, és nagyobb mértékben összekapcsolt tervezési összetevők belül minden egyes réteg között használt. A fejlesztők és a szalagtárak össze és néhány futtatható fájljainak és DLL-ek az egymáshoz kapcsolódó osztályok beleszámítja. 

Nincsenek ilyen egy egységes kialakítási megközelítés előnyeit. Gyakran egyszerűbb tervezéséhez, és mivel az adott hívások gyakran folyamatközi kommunikáció (IPC) alatt van összetevők közötti gyorsabb hívások. Emellett egy termék, amely általában több személyek-erőforrások hatékony mindenki teszteli. A hátránya, hogy egy rétegzett rétegek közötti szoros kapcsoló van, de egyes összetevőket nem lehet méretezni. Ha kell végrehajtani a javítások és a frissítések, akkor várja meg, hogy mások is vizsgálati Befejezés. Azonban nehezebb gyors lennie.

Mikroszolgáltatások cím ezek downsides és több leginkább megfelel-e az előző üzleti követelményeknek, de előnyei és a források is rendelkeznek. A következők mikroszolgáltatások előnyei, hogy minden egyes általában foglalja az egyszerűbb üzleti működését, ami felfelé vagy lefelé teszt méretezhető, telepítéséhez és felügyeletéhez egymástól függetlenül. Egy fontos mikroszolgáltatási megközelítés előnye, hogy csoportok alakítják több mint üzleti forgatókönyvek szerint technológiát, amely a rétegzett megközelítés javasolja. A gyakorlatban kisebb egy ügyfél alapuló mikroszolgáltatási fejlesztése és bármely választ technológiák használata. 

Más szóval a szervezet műszaki mikroszolgáltatási alkalmazások karbantartásához normalizálnia nem szükséges. Az egyes csoportok, hogy saját szolgáltatások teheti meg szabálykészletében mi számukra team szakértői alapján, vagy a megfelelő módon megoldani a problémát. A gyakorlatban ajánlott technológiák, például egy adott NoSQL tárolja, vagy a webes alkalmazás-keretrendszer, érdemes.

A hátránya az mikroszolgáltatások külön entitások megnövekedett számú kezelése és az összetettebb telepítések and versioning foglalkozó származnak. A mikroszolgáltatások közötti hálózati forgalom és a megfelelő hálózati késések növeli. Chatty, részletes szolgáltatásokat nagyszámú egy receptet a teljesítmény-nightmare. Eszközök megtekintése nélkül ezeket a függőségeket is nehéz "Lásd" az egész rendszerhez. 

Szabványok által történő kommunikációhoz megállapodjanak, és csak az egyikét a szolgáltatásból kell hibatűrő munkahelyi helyett kemény győződjön meg a mikroszolgáltatási megközelítés szerződések. Fontos előre ezek a szerződések ad meg a Tervező, mert adatszolgáltatások frissítése egymástól függetlenül. Egy másik leírás szereznek a mikroszolgáltatások megközelítés való érték "minden részletre kiterjedő szolgáltatásorientált architektúra (SOA)."

***A legegyszerűbb esetben a mikroszolgáltatások létrehozására tervezett módszert alkalmaz egy leválasztott összevonási szolgáltatások, az egyes független módosításokat, és elfogadott szabványnak kommunikációhoz tárgya.***

Mivel további felhőalapú alkalmazások előállítása, személyek, hogy a szolgáltatás független, forgatókönyv-arra irányul, hogy a teljes alkalmazás felbontás ellen-e a jobban hosszú távú megközelítés deríti fel.

## <a name="comparison-between-application-development-approaches"></a>Alkalmazás-fejlesztői módszerek összehasonlítása
![A Service Fabric platform alkalmazásfejlesztés][Image1]

1) Egy egységes alkalmazást tartományspecifikus funkciót tartalmaz, és általában osztóval működési rétegek, például webes, üzleti és adat.

2) Több kiszolgáló virtuális gépek klónozásával méretezni egységes app/tárolók.

3) Egy mikroszolgáltatási alkalmazás elválasztja kisebb szolgáltatások külön funkciókat.

4) A mikroszolgáltatások megközelítés méretezik által végzett központi telepítése minden egyes szolgáltatás egymástól függetlenül, ezek a szolgáltatások példánya létre keresztül kiszolgálók vagy virtuális gépek/tárolók.

Az egy mikroszolgáltatási tervezése megoldás, nem az összes projektet, melyekben egy panacea, de jobban összhangba kerüljenek a korábban leírt üzleti célkitűzéseiket. Egységes módszer kezdve elfogadható, ha tudja, hogy a lehetőséget, hogy a kód későbbi átdolgozási mikroszolgáltatások kialakítást be fogja is lehet. Gyakrabban egységes alkalmazás kezdődnie, és lassan bontsa fázisból áll, a funkcionális területet kell lenniük, méretezhető, vagy a nagy kezdve.

Összefoglalva, a mikroszolgáltatási megközelítés, az alkalmazás számos kis szolgáltatás létrehozásához. A szolgáltatások a gépet fürtön belül üzembe helyezett tárolók futnak. Kisebb csoportok egy szolgáltatás, amely egy olyan forgatókönyvet fejlesztése és egymástól függetlenül, verzió tesztelése, telepítése és minden egyes szolgáltatás méretezhető, hogy a teljes alkalmazás fejlődnek is.

## <a name="what-is-a-microservice"></a>Mi az a mikroszolgáltatási?
Nincsenek mikroszolgáltatások különböző meghatározása. Ha az interneten keres, látni fogja, amelyek a saját nézőpontok és a definíciók biztosítanak számos hasznos források. Azonban a legtöbb mikroszolgáltatások jellemzői széles körben megegyezésre:

* Egy ügyfél vagy üzleti forgatókönyv foglalják magukban. Mi az a problémamegoldó?
* Egy kis mérnöki csapata által fejlesztett.
* Bármely programozási nyelv, és tetszőleges-keretrendszerével.
* (Opcionális) állapot, amelyek mindkettő egymástól függetlenül rendszerverzióval ellátott, telepített, és a kiterjesztett és kód állnak.
* Jól meghatározott felületet és protokollokon keresztül kommunikál a többi mikroszolgáltatások létrehozására.
* Oldja fel a hely egyedi névvel (URL) rendelkezik.
* Továbbra is következetes és hibák esetén érhető el.

A következő jellemzőkkel foglalhatja össze:

***Mikroszolgáltatási alkalmazások álló kis, egymástól függetlenül rendszerverzióval ellátott és méretezhető ügyfél tárgyalt szolgáltatás, amely a jól meghatározott felülethez szabványos protokollokon keresztül kommunikálnak egymással.***

Azt az első két pontot az előző szakaszban leírt jelez, és bontsa ki a most azt és elmagyarázza a többi.

### <a name="written-in-any-programming-language-and-use-any-framework"></a>Bármely programozási nyelv, és bármely keretrendszer használata
A fejlesztők azt kell megjelennie szabadon válasszon egy nyelvet vagy keretrendszer, amely azt szeretnénk, a képességek vagy a szolgáltatás igényeitől függően. Az egyes szolgáltatások által nyújtott mindenekelőtt más C++ előfordulhat, hogy érték. Más szolgáltatások a felügyelt könnyű fejlesztés jegyében készült C# vagy Java legfontosabb lehet. Egyes esetekben szükség lehet az adott partnert könyvtár, tárolási technológiát vagy azt jelenti, hogy érdekében, hogy csökkenjen az ügyfelek számára a szolgáltatás.

Miután kiválasztott egy technológia, Ön lesz a működési vagy életciklus-kezelési és a méretezésről, a szolgáltatás.

### <a name="allows-code-and-state-to-be-independently-versioned-deployed-and-scaled"></a>Lehetővé teszi a kódot és az állapot egymástól függetlenül rendszerverzióval ellátott, telepített, és a kiterjesztett
Azonban úgy dönt, hogy a mikroszolgáltatások létrehozására, a kódot és opcionálisan az állapot írási kell egymástól függetlenül telepítése, frissítése és méretezése. Ez az ténylegesen egyik nehezebben problémák megoldására, mert a megadott beállítás technológiák ismét. A méretezési, ismertetése partíció (vagy a shard) a kódot és az állapot kihívást. Használatakor a kódot és az állapot különálló technológia, amely közös ma, az üzembe helyezési parancsfájlok esetében a mikroszolgáltatási kell őket mindkét skálázás megbirkózni. Ez egyben kapcsolatos agilitást és a rugalmasságot, így nem kell egyszerre frissítse az összes frissíteni tudja a mikroszolgáltatások némelyike.

Visszatérés a egységes mikroszolgáltatási megközelítést és egy kis ideig, az alábbi ábrán látható állapot tárolásához az módszer közötti különbségeket.

#### <a name="state-storage-between-application-styles"></a>Állapot tárolási alkalmazás stílusok között
![A Service Fabric platform állapot tárolásához][Image2]

***Az egységes módszer a bal oldali egy önálló adatbázis és az egyes technológiák álló rendelkezik.***

***A jobb oldali mikroszolgáltatások megközelítés egy grafikonon összekapcsolt mikroszolgáltatások, ahol állapot általában a mikroszolgáltatási hatókörét, és különböző technológiákkal, rendelkezik.***

Egységes megközelítéssel, általában az alkalmazás egyetlen adatbázist használ. Ennek előnye, hogy a rendszer egyetlen helyen, amely megkönnyíti a telepítéséhez. Minden egyes összetevő rendelkezhet egy táblát az állapot tárolásához. Csoportok kell feltétlenül állapotát, amely kihívást jelent. Mindenképpen nincsenek temptations egy olyan új oszlop hozzáadása a meglévő felhasználói tábla, illesztés táblák között, és hozzon létre függőségek a tárolási rétegben. Miután ez akkor fordul elő, az egyes összetevők nem lehet méretezni. 

A mikroszolgáltatások módszert használja minden egyes szolgáltatás kezeli, és eltárolja a saját állapotot. Minden szolgáltatás feladata skálázás kód és a segítségével igényeinek megfelelően a szolgáltatás állapotát. A hátránya az, hogy amikor nézeteket, vagy az alkalmazásadatok, a lekérdezések létrehozásához szükség van szüksége különböző állapotot tárolók átfogó lekérdezése. Általában ez megoldódott azzal, hogy egy külön mikroszolgáltatási, amelyek között mikroszolgáltatások gyűjteménye képet alkot. Az adatok több rögtönzött lekérdezések végrehajtásához, minden egyes mikroszolgáltatási kell javasolt írása adataik egy vámraktározási szolgáltatás offline elemzéséhez.

Versioning csak egy mikroszolgáltatási telepített verzióját az úgy, hogy több, különböző verziót telepíteni, és futtassa egymás mellett. Versioning megoldást az esetek, amikor egy mikroszolgáltatási egy újabb verziója sikertelen frissítés során, így visszaállíthatja egy korábbi kell. A verziókövetés más forgatókönyv működik-e A/B-stílusú tesztelése, ahol különböző felhasználói élmény különböző verzióit a szolgáltatás. Például esetében gyakori, egy új funkció tesztelése előtt széles körben kiosztását további ügyfelek adott számú mikroszolgáltatási frissítéséhez. Után mikroszolgáltatások életciklusának kezelését Ez most számos lehetőséget kínál, a köztük folyó kommunikációt.

### <a name="interacts-with-other-microservices-over-well-defined-interfaces-and-protocols"></a>Jól meghatározott felületet és protokollokon keresztül más mikroszolgáltatások kommunikációja
Ez a témakör figyelmet kevés itt, mert kapcsolatos szolgáltatásorientált architektúra, amely az elmúlt 10 évben közzé lett téve kiterjedt irodalom szokások ismerteti. Általában szolgáltatások közötti kommunikáció használ a többi megközelítés HTTP és a TCP protokollt és XML- vagy JSON szerializálási formátum szerint. Felület szempontból akkor kapcsolatos alkalmaznak a webes tervezett módszert alkalmaz. De semmi nem leáll, a bináris protokollok vagy a saját adatok formátum használatával. A mikroszolgáltatások használata esetén ezek a protokollok és formátumok nem nyilvánosan elérhetővé nehezebb egyszerre személyekhez készíteni.

### <a name="has-a-unique-name-url-used-to-resolve-its-location"></a>Oldja fel a hely egyedi neve (URL)
Ne feledje, hogy hogyan azt tartsa arról, hogy a mikroszolgáltatási megközelítés olyan, mint a webes? A webes, például a mikroszolgáltatási kell lennie megcímezhető, bárhol fut-e. Ha vannak a számítógép gépek gondolat, és melyik fut egy adott mikroszolgáltatási, dolgot gyorsan rossz lépjen. 

Azonos módon, hogy a DNS egy adott URL feloldása egy adott számítógép számára a mikroszolgáltatási kell úgy, hogy az aktuális helyén felderíthető egyedi nevet adni. Mikroszolgáltatások létrehozására, amelyek miatt a infrastruktúráról a futó független megcímezhető nevet kell adni. Ez azt jelenti, hogy van egy közötti interakció hogyan a szolgáltatás telepítve van és hogyan felderítésekor, mert kell adni a szolgáltatás beállításjegyzékbeli. Egyaránt Ha a gép nem sikerül, a beállításjegyzék szolgáltatást kell közli, ahol most már fut. a szolgáltatás. 

Ekkor velünk a következő témakörre: rugalmasságának megvalósítása és konzisztenciáját.

### <a name="remains-consistent-and-available-in-the-presence-of-failures"></a>Továbbra is a következetes és hibák esetén érhető el
Váratlan hibák foglalkozik a nagyon nehéz problémák megoldására, különösen az elosztott rendszerek egyike. Nagy részét a kódot, amely azt a fejlesztők írható kezeli a következő kivételeket, és ez is ahol a legtöbb idő telhet el a tesztelése. A probléma az bonyolultabb, mint a hibák kezelésének kód írása. Mi történik, amikor a számítógép, amelyen fut a mikroszolgáltatási sikertelen? Nem csak szüksége van a mikroszolgáltatási hiba (rögzített probléma önállóan) észlelését, de szükség valamit a mikroszolgáltatási újraindítására. 

Egy mikroszolgáltatási hibák rugalmasak lehetnek, és gyakran indítsa újra a rendelkezésre állási okokból egy másik gépen kell. Ez is előre a nevében a mikroszolgáltatási, ahol a mikroszolgáltatási helyre tudja állítani az ilyen állapotú, és a mikroszolgáltatási újraindításának sikeres volt-e mentett állapotba. Ez azt jelenti szükség van a számítási (a folyamat újraindítja) a rugalmasság, valamint az adatok (adatvesztés nélküli, és az adatok konzisztensek maradnak) vagy a rugalmasság.

A hibatűrés problémák vannak jóváírása során más helyzetekben, például, ha hiba fordulhat elő, egy alkalmazás frissítése során. A mikroszolgáltatási, működik-e a központi telepítés rendszerrel helyreállítása nem szükséges. Azt is meg majd döntse el, hogy az továbbra is Ugrás az újabb verzióra, vagy inkább visszaállíthatja egy korábbi verziója konzisztens állapotú legyen. Például elegendő gép elérhetők-e tartani áthelyezése előre és korábbi verzióiban a mikroszolgáltatási helyreállítása kérdéseket kell figyelembe kell venni. Ehhez a mikroszolgáltatási állapottal kapcsolatos adatok kell tenni, hogy ezek a döntések hozható létre.

### <a name="reports-health-and-diagnostics"></a>Jelentések állapot és diagnosztika
Tűnhet nyilvánvaló, és gyakran kihagyott van, de egy mikroszolgáltatási jelenteniük kell az állapot és diagnosztika. Ellenkező esetben nincs műveletek szempontból kevés betekintést. A diagnosztikai adatok független szolgáltatások között, és a gép óra dönt a esemény rendelés célszerű a foglalkozó nem kihívást. Ugyanúgy működik együtt, egy mikroszolgáltatási elfogadott protokollok és adatok formátumok keresztül, a jelenik meg a bejelentkezési állapot- és végső soron kérdez le, és tekintse meg az esemény tárolóban végül diagnosztikai eseményei hogyan szabványügyi szükségük van. Egy mikroszolgáltatások módszert használja, a kulcs esetében, hogy a különböző csapatok megállapodni egy egyetlen naplózási formátum. Szükség van egy diagnosztikai események megtekintése az alkalmazás egészének az egységes megközelítése.

Állapot abban különbözik a diagnosztika. A megfelelő műveletet a jelenlegi állapotában reporting mikroszolgáltatási lényege, hogy mely állapotát. Egy jó példa frissítését és telepítését mechanizmusokat rendelkezésre álljon, dolgoznak. Bár a szolgáltatás esetleg jelenleg nem megfelelő, egy folyamat összeomlása miatt, vagy számítógép-újraindítás, a szolgáltatás továbbra is előfordulhat, hogy működésbe. A legutolsó dolog van szüksége, hogy legyen ez rosszabb frissít. A legjobb megoldás vizsgálat kezdeti lépések, vagy várja meg a mikroszolgáltatási helyreállításához. Az egy mikroszolgáltatási állapotával kapcsolatos események segítsen tájékozott döntést hozni, és öngyógyító szolgáltatások érvényben, segítséget.

## <a name="service-fabric-as-a-microservices-platform"></a>A Service Fabric mikroszolgáltatások platformként
Azure Service Fabric a Microsoft által átmenet kiderült, a szolgáltatások továbbítása mezőben termékek, általában egységes stílus, amely kézbesíti. A felhasználói élmény épület és nagy szolgáltatások, például az Azure SQL Database és Azure Cosmos DB, a Service Fabric alakú. A platform továbbfejlődtek idővel egyre több szolgáltatások elfogadott azt. Fontos a Service Fabric futtatni kellett nem csak az Azure-ban, hanem a Windows Server-telepítések önálló.

***A Service Fabric célja kialakításához és a szolgáltatás futtatásához a rögzített problémák megoldásához, és hatékonyan, infrastruktúra erőforrásainak használatát, hogy a csapatok egy mikroszolgáltatások megközelítéssel üzleti problémák megoldhatók.***

A Service Fabric egy mikroszolgáltatások megközelítés használó alkalmazások készíthetők három területek biztosítja:

* Egy platform, amely biztosítja a rendszerszolgáltatások telepítéséhez frissítése, észleli, és indítsa újra a sikertelen szolgáltatásokat, szolgáltatások felderítése, üzenetek, állapot kezelése és állapotának figyeléséhez. A rendszer szolgáltatások engedélyezése korábban leírt mikroszolgáltatások jellemzői számos érvényben.
* Lehetővé teszi alkalmazások telepítését vagy futó tárolók vagy folyamatokat. A Service Fabric, a tároló és a folyamat az orchestrator.
* Hatékony programozási API-k, alkalmazások, például mikroszolgáltatások létrehozásához: [ASP.NET Core Reliable Actors és Reliable Services](service-fabric-choose-framework.md). Kiválaszthatja, hogy bármely kód a mikroszolgáltatási létrehozásához. Azonban ezen API-k ellenőrizze a feladat egyszerűbb, és azok integrálása a platform mélyebb ismeretére. Így például állapot és diagnosztika információkat kaphat, vagy beépített magas rendelkezésre állású előnyeit élvezheti.

***A Service Fabric a hogyan hoz létre a szolgáltatás független, és bármely technológiával. Azonban biztosítja a beépített programozási API-k, amelyek révén könnyebben mikroszolgáltatások létrehozására.***

### <a name="migrating-existing-applications-to-service-fabric"></a>A Service Fabric meglévő alkalmazások áttelepítése
A kulcs megoldás a Service Fabric, meglévő kódot, mely tudja majd utat korszerűsítenek az új mikroszolgáltatások újból. Alkalmazás korszerűsítését öt szakaszból áll, és indítsa el, és állítsa le szakaszok bármikor. Ezek a;

1) A hagyományos egységes alkalmazás igénybe  
2) Növekedési és az eltolás mértékét megadó - tárolók és a Vendég végrehajtható fájlok használatával a Service Fabric a meglévő kódot futtatni.  
3) Korszerűsítése - mellett a meglévő indexelése kód hozzáadott új mikroszolgáltatások létrehozására.  
4) Innovációját – a egységes felosztása mikroszolgáltatások tisztán igények alapján.  
5) Átalakítja az mikroszolgáltatások - átalakítását az egységes alkalmazások meglévő vagy új greenfield alkalmazások létrehozásához.

![Áttelepítés Mikroszolgáltatások][Image3]

Fontos, hogy hangsúlyozzák az, hogy újra **indítása és leállítása, ezek a szakaszok**. A következő szakaszára előrehaladás nem kényszerül vannak. Most nézzük példák az alábbi szakaszok.

**Emelje fel, és az eltolás mértékét megadó** -vállalat nagy számú feloldása, és a meglévő egységes alkalmazások időeltolású szállító két okból;

- Költség csökkentése vagy konszolidáció és magasabb sűrűség meglévő hardver- vagy futó alkalmazásokat eltávolítása miatt. 
- Egységes telepítési szerződés fejlesztésre, működésre között.

Költség csökkentésének érthető, és a Microsofton belül nagyszámú meglévő alkalmazások vannak folyamatban indexelése egyszerűen több millió dolláros mentésére. Egységes telepítési nehezebb értékelje ki, de egyaránt fontos. Azt jelzi, hogy a fejlesztők is továbbra is szabadon választhat a technológia, hogy a csomagok őket, azonban a műveleteket csak fogad egy egyetlen konkrét módszert meghatározni telepítéséhez és ezeket az alkalmazásokat kezeléséhez. Kiküszöböli az kellene számos különböző technológiák összetettsége foglalkozik, illetve a felhasználók a fejlesztők számára, hogy csak az egyes néhányat a meglévők közül válassza a műveletek. Gyakorlatilag minden egyes alkalmazás van indexelése az önálló telepítési lemezképeket.

Számos szervezet itt leállítása. A tárolók előnyeit már rendelkeznek, és a Service Fabric a teljes kezelést biztosít központi telepítés, frissítés, versioning, visszagörgetése, egészségügyi figyelési stb.

**Korszerűsítése** -hozzáadása az új szolgáltatások meglévő indexelése kóddal együtt. Ha azt tervezi, új kód írását, célszerű úgy dönt, hogy a mikroszolgáltatások útvonalon kis lépésekkel. Lehet, hozzáadása egy új REST API-végpont vagy egy új üzleti logikát. Ezzel a módszerrel, indítsa el a az út épület új mikroszolgáltatások létrehozására és gyakorlat fejlesztéséhez és telepítéséhez őket.

**Innovációját** -e eredeti változó üzleti igényeinek Ez a cikk elején mikroszolgáltatások megközelítés eredményező megjegyzése? Ebben a szakaszban a döntés ezeket hogy a jelenlegi alkalmazás, és ha igen, indítsa el a monolit felosztásával, vagy innovating kell. Itt például akkor, ha egy adatbázist a feldolgozás szűk keresztmetszetek válik, mivel használatban van egy munkafolyamat-várólista. A munkafolyamat számának kérések a munka növelése kell bővített terjeszthetők. Így az, hogy az alkalmazást, amely nem adott adathordozónevek skálázás, vagy frissíteni kell gyakrabban, ossza meg ezt egy mikroszolgáltatási és innovációját annak. 

**Átalakítja az mikroszolgáltatások** – Ez a ahol az alkalmazás teljesen álló (vagy bontja fel a rendszer a) mikroszolgáltatások létrehozására. Itt elérni, az mikroszolgáltatások út hajtott végre. Itt is elindítható, de egy mikroszolgáltatások nélkül ehhez segítséget platform jelentős befektetési. 

### <a name="are-microservices-right-for-my-application"></a>Azok a mikroszolgáltatások közvetlenül az alkalmazáshoz?
Lehetséges. Azt tapasztalta, hogy a Microsoft több és több csapat megkezdte üzleti okokból felhőalkalmazások, mert ezek is tudjuk mikroszolgáltatási hasonló alkalmazni előnyeit volt. A Bing, például rendelkezik lett fejlesztése a keresési évig mikroszolgáltatások létrehozására. A más csapatokkal az mikroszolgáltatások megközelítés az volt új. Csoportok találta, hogy a nem rögzített hibák léptek fel kívül erőssége core területére megoldására. Ezért a Service Fabric szerzett vontatási szolgáltatások számára választott technológia.

A Service Fabric célja csökkenteni a bonyolultságára alkalmazások egy mikroszolgáltatási módszert használja, így azt nem kell végighaladnia számos költséges redesigns. Kezdje kis lépésekkel, ha szükséges, szolgáltatások érvényteleníthető, újakat vehet fel és fejleszteni az ügyfél használati megközelítés. Is tudjuk, hogy vannak-e sok más még megoldandó problémák az legtöbb fejlesztő több könnyű mikroszolgáltatások legyen. Tárolók és a szereplő programozási modell példák ebben az irányban kis lépés, és azt meg arról, hogy további innovációinak jelentkezik ez megkönnyítése.
 
<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>További lépések
* [A Service Fabric-terminológia áttekintése](service-fabric-technical-overview.md)
* [Mikroszolgáltatások: Egy alkalmazás fordulat a felhő technológiával](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)

[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png
[Image3]: media/service-fabric-overview-microservices/microservices-migration.png
