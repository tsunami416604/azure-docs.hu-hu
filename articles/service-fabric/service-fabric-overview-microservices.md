---
title: Az Azure mikroszolgáltatások bemutatása |} A Microsoft Docs
description: Miért érdemes a mikroszolgáltatási megközelítést felhőalkalmazások létrehozásához fontos a modern alkalmazások fejlesztését, és hogyan Azure Service Fabric ennek érdekében platformot biztosít áttekintést.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: fae2be85-0ab4-4cd3-9d1f-e0d95fe1959b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/02/2017
ms.author: msfussell
ms.openlocfilehash: 39034d069c0c0feb2ac81437e34c7206d7fe6c4c
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55102816"
---
# <a name="why-a-microservices-approach-to-building-applications"></a>Miért érdemes a mikroszolgáltatás-alapú megközelítést választani alkalmazások?

Szoftver-fejlesztők számára, jelenleg nincs újdonságunk kapcsolatos egy alkalmazás összetevőit faktoring szerintünk a. Általában egy rétegzett megközelítéssel használatban van egy háttér-tároló, a középső rétegű üzleti logikát és a egy előtér-felhasználói felületének (UI). Mi *rendelkezik* megváltozott az elmúlt néhány évben, hogy tudjuk, fejlesztők számára nyújtanak, elosztott alkalmazásokat készítenek a felhőben.

A változó üzleti igények szerint a következők:

* A szolgáltatás épül, és egyszerre több elérhetők az ügyfelek az új földrajzi régióban működik.
* Gyorsabb tartalomkézbesítés a szolgáltatásokat és képességeket, hogy tudjon reagálni az ügyféligényekre percalapú díjfizetést kínál az.
* Továbbfejlesztett erőforrás-használat költségek csökkentése érdekében.

Ezek az üzleti igények, lényegében *hogyan* alkalmazások készítünk.

Azure-mikroszolgáltatás-alapú megközelítést kapcsolatos további információkért olvassa el a [mikroszolgáltatás-alapú: Egy felhőbeli alkalmazásforradalom](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="monolithic-vs-microservice-design-approach"></a>Monolitikus, és a mikroszolgáltatások tervezési megközelítés

Alkalmazások időbeli alakulásával. Sikeres alkalmazás fel hasznos személyek fejlesztheti tovább. Sikertelen kérelmek nem fejlődnek, és idővel elavulttá váltak. A kérdés van: IP-címek fenntartási ismeri a követelményekkel kapcsolatos még ma, és mi azok lesz a jövőben? Például tegyük fel, hogy a jelentéskészítő alkalmazás Intézet hoz létre. Biztos benne, hogy az alkalmazás csak a vállalat hatókörén belül vonatkozik, és hogy a jelentések rövid ideig tartó. A választott módszer eltér, tegyük fel, hogy, videótartalmak létrehozásához egy szolgáltatás, amely továbbítja az ügyfelek tízmillió.

Egyes esetekben első valamit az ajtó ki, a koncepció igazolása, a vezetői tényező, viszont ismeri az alkalmazás újabb újratervezve. Soha nem használt lekéri a fölösleges mérnöki valami kis pont van. A szokásos mérnöki kompromisszum. Másrészről vállalatok beszélni a felhő létrehozása, amikor az elvárás növekedését és a használat. A probléma oka, hogy a növekedési és a méretezési csoport előre nem látható. Szeretnénk felkereső prototípusként gyorsan közben is ismerete, hogy azt egy jövőbeli sikeres kezelésére elérési úton vannak. Ez az a lean indítási megközelítés: hozhat létre, illetve mérheti, ismerje meg, és ismételt futtatásával.

Az ügyfél és kiszolgáló era során azt növelése az egyes technológiák használatával az egyes szintek többszintű alkalmazások készítésére. Az előfizetési időszak *monolitikus* alkalmazás felmerült, ezek a módszerek esetében. A rétegek közötti kell növelése a felületek, és több szorosan összekapcsolt tervezési használták az egyes szinteken belül összetevői között. Fejlesztők számára készült és a faktorált osztályok csatolását néhány végrehajtható fájlok és DLL-EK és kódtárak lefordítva. 

Nincsenek arra, hogy az ilyen a monolitikus kialakítás megközelítést. Gyakran egyszerűbb tervezhet, és azt, gyorsabb hívások összetevői között, mert ezeket a hívásokat sokszor folyamatközi kommunikáció (IPC) keresztül. Ezenkívül egyetlen termékhez, amely általában több személyek-erőforrás hatékony mindenki teszteli. A hátránya az, hogy van egy rétegzett rétegek közötti szoros összekapcsolódást, és egyes összetevőket nem skálázhatja. Ha a javítások és frissítések végrehajtásához van szüksége, akkor várja meg, másokat, hogy a tesztelés befejezése. Legyünk nagyobb nehézséget jelent.

Mikroszolgáltatások cím ezek több szempontból sem ajánlott, és további szorosan igazítása a fenti üzleti követelményeinek, de előnyöket és forrásokat is is vannak. A mikroszolgáltatások előnyeit is, hogy mindegyikhez egyszerűbb üzleti funkció, amely, kisebbre vagy nagyobbra méretezhetők, teszteléséhez, üzembe helyezése és kezelése egymástól függetlenül általában magában foglalja. A mikroszolgáltatási megközelítést egyik fontos előnye, hogy csapatok alakítják több, mint üzleti forgatókönyvek szerint technológia--a rétegzett megközelítéssel javasolja. A gyakorlatban kisebb csapatok egy, az ügyfél forgatókönyv-alapú mikroszolgáltatás fejlesztés, és minden olyan technológiák kiválasztását.

Más szóval a szervezet műszaki épülő alkalmazások karbantartásához egységesítésére nem szükséges. Az egyes csapatok, hogy saját szolgáltatás teheti meg logikus mi számukra csapat szaktudását alapján, vagy mi a probléma megoldására legmegfelelőbb. A gyakorlatban ajánlott technológiák, például egy adott nosql-alapú csoportja tárolásához, vagy webalkalmazás-keretrendszer, célszerű a.

A hátránya mikroszolgáltatások külön entitások megnövekedett száma kezelését, és összetettebb üzembe helyezések és versioning foglalkozó érhető el. A mikroszolgáltatások közötti hálózati forgalom és a megfelelő hálózati késési nő. Nagy mennyiségű forgalmas részletes szolgáltatás esetében a teljesítmény nightmare egy Recept. Eszközök megtekintése érdekében nélkül a függőségek meglehetősen nehéz a "Lásd" a teljes rendszer.

Szabványok győződjön meg arról, a mikroszolgáltatási megközelítést szerint elfogadja a meghatározott való kommunikációra, és csak az egyikét egy szolgáltatásból kell hibatűrő munkahelyi helyett merev szerződéseket. Fontos határozhat meg ezek a szerződések meghozni a tervezési, mert a szolgáltatások egymástól frissíti. A mikroszolgáltatási megközelítést tervezéséhez alkotta egy másik leírása a "részletes szolgáltatásorientált architektúra (SOA)."

***A legegyszerűbb a mikroszolgáltatások tervezési megközelítés egy leválasztott összevonási szolgáltatások, az egyes független módosítások és a konferenciahívások lefolytatása előírásoknak kommunikációra szolgál.***

Ahogy további felhőalkalmazások előállítása, személyek felderített, hogy ez a teljes alkalmazás felbontása független, a forgatókönyv témájú szolgáltatásba-e jobban hosszú távú megközelítést.

## <a name="comparison-between-application-development-approaches"></a>Alkalmazás fejlesztése módszerek összehasonlítása

![A Service Fabric platformot alkalmazásfejlesztés][Image1]

1) Egy monolitikus alkalmazáshoz tartalmazza a tartomány-specifikus szolgáltatásokat, és általában oszlik, például a webes, az üzleti és működési rétegek szerint.

2) Több kiszolgáló/virtuális gépek klónozásával egy monolitikus alkalmazások méretezése és tárolók.

3) A mikroszolgáltatás-alkalmazások elválasztja a kisebb szolgáltatások külön funkciókat.

4) A mikroszolgáltatási megközelítést skálázását követve rugalmasan méretezhető által végzett központi telepítése minden egyes szolgáltatás egymástól függetlenül, ezen példányok létrehozása között kiszolgálók/virtuális gépek és tárolók.

A mikroszolgáltatások tervezése módszer nem az összes projekt egy panacea, de jobban igazodnak a fentebb leírt, az üzleti célkitűzéseinek megvalósulását. A monolitikus megközelítés kezdve elfogadható, ha tudja, hogy kell-e lehetőséget a kód későbbi újragyártási mikroszolgáltatásokra be lehet. Leggyakrabban egy monolitikus alkalmazáshoz kezdődik, és lassan bontsa szakaszban, a funkcionális területeket, kell lennie a méretezhető, vagy Agilis kezdve.

A mikroszolgáltatási megközelítést, az azt jelenti, hogy több kis szolgáltatásra, az alkalmazás írásakor. A szolgáltatások a gép egy fürtön üzembe helyezett tárolók futnak. Kisebb csapatok fejlesztése egy szolgáltatás, amely eset fókuszál, és egymástól függetlenül tesztelése, verzió, telepíthet és méretezhet minden egyes szolgáltatás úgy, hogy a teljes alkalmazás tovább lehessen fejleszteni.

## <a name="what-is-a-microservice"></a>Mit jelent a mikroszolgáltatások?

Nincsenek a mikroszolgáltatások külön definíciót. Azonban a következő jellemzőkkel mikroszolgáltatások a legtöbb széles körben megegyezésre:

* Egy ügyfél vagy az üzleti forgatókönyv magába foglalja. Mi az a problémamegoldó?
* Egy kis mérnöki csapata által fejlesztett.
* Bármelyik nyelven programozási nyelvet, és bármely keretrendszer használata.
* És (opcionálisan) állapot, mindkettő független verziójú telepítve, és a méretezett kód állnak.
* Együttműködhet más mikroszolgáltatások jól definiált felületek és protokollok.
* Oldja fel a hely (URL-címek) egyedi névvel rendelkezik.
* Egységes és folytonosságát hibák esetén elérhető marad.

Összegezve:

***Mikroszolgáltatás-alkalmazások kisebb, független verziójú és méretezhető ügyfélközpontú szolgáltatások jól meghatározott felületek a szabványos protokollokon keresztül kommunikálnak egymással állnak.***

### <a name="written-in-any-programming-language-and-use-any-framework"></a>Bármelyik nyelven programozási nyelvet, és bármely keretrendszer használata

Fejlesztők számára nyújtanak szeretnénk tenni, hogy válasszon egy nyelvet vagy keretrendszert, amelyet meg szeretnénk, attól függően, a képességek vagy a szolgáltatást igényei szerint. Az egyes szolgáltatások, előfordulhat, hogy érték felett más C++ előnyeinek. Más szolgáltatások a könnyű felügyelt fejlesztés C# és Java legfontosabb lehet. Bizonyos esetekben szükség lehet egy adott partner könyvtár, adattárolási módszerektől vagy azt jelenti, hogy az ügyfelek a szolgáltatást.

Miután döntött olyan technológia, a működési és életciklus-kezelési származnak, és a szolgáltatás méretezése.

### <a name="allows-code-and-state-to-be-independently-versioned-deployed-and-scaled"></a>Lehetővé teszi a kóddal és állapottal, független verziójú legyen telepítve, és a méretezett

Azonban úgy dönt, hogy a mikroszolgáltatások, írása, a kódot, és szükség esetén az állapotát, érdemes egymástól függetlenül üzembe helyezése, frissítése és méretezése. Ez az egyik a nehezebben problémákat kell megoldani, mert van, a szabadon választott technológiákkal. Méretezési, elsajátítása partíció (vagy a szegmensben) a kód és a állapot nagyobb kihívást jelent. Ha a kóddal és állapottal különálló technológia, amely közös még ma, a mikroszolgáltatások üzembe helyezési szkriptjei képeseknek kell lenniük mindkét őket. Ez akkor is kapcsolatos gyorsasága és rugalmassága biztosítja, hogy mindegyiket egyszerre frissítése nélkül frissítheti néhány a mikroszolgáltatások.

Térjen vissza a monolitikus mikroszolgáltatási megközelítést és egy kis ideig, az alábbi ábrán látható állapot tárolásához az módszer közötti különbségeket.

#### <a name="state-storage-between-application-styles"></a>Alkalmazás stílusok közötti állapot storage

![A Service Fabric platformot állapot tárolásához][Image2]

***A monolitikus megközelítés alkalmazásakor a bal oldali rendelkezik egy önálló adatbázis és az egyes technológiák szint.***

***A mikroszolgáltatási megközelítést, a jobb oldali rendelkezik egy grafikont a mikroszolgáltatások hálózatáról van szó, ahol állapota a mikroszolgáltatások általában hatókörét, és különböző technológiákkal.***

A monolitikus megközelítés a az alkalmazás általában egy adatbázist használ. Az előnye, hogy-e egyetlen helyen, amelynek segítségével könnyedén üzembe helyezéséhez. Minden egyes összetevő állapotában tárolásához egy táblát is rendelkezhet. Csapatok kell szigorúan külön állapotát, amely nehézségeket jelenthet. Nincsenek elkerülhetetlenül temptations egy olyan új oszlop hozzáadása egy meglévő ügyfél táblához, a táblák közötti csatlakozzon, és függőségek létrehozása a tárolási rétegben. Ha ez történik, az egyes összetevők nem skálázhatja.

A mikroszolgáltatás-alapú módszert használja, az egyes szolgáltatások kezeli, és tárolja a saját állapota. Minden egyes szolgáltatás méretezése a kód és az állapot együtt, hogy a videólejátszást a szolgáltatás felelős. A hátránya, hogy amikor nézetek vagy a lekérdezések, az alkalmazásadatok, létrehozásához szükség van, kell több állapot tároló lekérdezésére. Általában ez van megváltoztatásával orvosolhatók külön mikroszolgáltatások, amely képet alkot a mikroszolgáltatások gyűjteménye között kellene. Ha szüksége több rögtönzött lekérdezések végrehajtása az adatokon, mindegyik mikroszolgáltatás vegye figyelembe az adatok írása egy adattárház szolgáltatás offline elemzéshez.

Mikroszolgáltatások rendszerverzióval ellátott és lehetséges, hogy mikroszolgáltatások különböző verzióit is futnia egymás mellett. Egy újabb verziója egy mikroszolgáltatási előfordulhat, hogy sikertelen frissítés során, és állítható vissza egy korábbi verziója szükséges. Verziókezelés A/B-stílusú tesztelési, ahol a különböző felhasználói élmény a szolgáltatás különböző verzióit is hasznos lehet. Például szokás egy új funkció teszteléséhez mielőtt széles körben közbeni további ügyfelek adott halmazának mikroszolgáltatás frissítése.

### <a name="interacts-with-other-microservices-over-well-defined-interfaces-and-protocols"></a>Más mikroszolgáltatások kommunikál a jól definiált felületek és protokollok

Széles körű elsajátításához kapcsolatos szolgáltatásorientált architektúrájú közzétette-e az elmúlt 10 évben leíró kommunikációs minták. Általában a szolgáltatások közötti kommunikáció egy REST módszert használ a HTTP és a TCP protokollt, és az XML vagy JSON szerializálási formátum szerint. Felület szempontból a webes tervezési megközelítés kihasználásának szól. De semmi nem, a bináris protokollok vagy a saját adatok formátum használatával. Elő kell készíteni a felhasználók számára a mikroszolgáltatások használatával, ha ezek a protokollok és formátumok nem érhetők el némelyik nehezebb időpontot.

### <a name="has-a-unique-name-url-used-to-resolve-its-location"></a>Oldja fel a hely egyedi neve (URL)

A mikroszolgáltatások kell legyen elérhető, bárhol is fut-e. Ha gépeket tanulmánnyal, és a egy adott mikroszolgáltatás melyiket fut, dolgokat gyorsan hibás lépjen. 

Ugyanolyan módon, hogy a DNS egy adott URL feloldása egy adott géphez a mikroszolgáltatások úgy, hogy az aktuális helyén felderíthető kell egy egyedi nevet. Mikroszolgáltatás-alapú független az azokat futtató infrastruktúra címezhető nevet kell adni. Ez azt jelenti, hogy van egy belőle a szolgáltatás telepítési módját, és hogyan kiderül, mert szükség van egy szolgáltatás beállításjegyzék. Ha egy gép nem sikerül, a beállításjegyzék szolgáltatást kell mondani, ahol a szolgáltatás került.

### <a name="remains-consistent-and-available-in-the-presence-of-failures"></a>Egységes és folytonosságát hibák esetén érhető el

Többé vesződnie a sérült váratlan meghibásodások esetén az egyik nagyon nehéz problémák megoldásában, különösen az elosztott rendszerekben. Nagy része a kódot, amely a fejlesztők számára nyújtanak írunk kivételkezelést van, és ez történik akkor is, a legtöbb időt vesz igénybe a tesztelés. A probléma, mint a kódírás hibáinak a kezelése nagyobb jelentőséggel bír. Mi történik, ha a gép a mikroszolgáltatási futtató kiszolgáló nem tud? Nem csak szükség van a hiba (rögzített probléma önállóan) észlelését, de is újra kell indítania a mikroszolgáltatás.

Rendelkezésre állási okokból mikroszolgáltatások kell lennie a hatással a meghibásodások és a egy másik gépen újra. A rugalmas folyamatban futó kóddal, mellett az adatvesztés nem lehet, és az adatokat kell maradniuk.

Rugalmasság nehezen érhető el, ha hibája a legtöbb esetben egy alkalmazás frissítése során. A mikroszolgáltatás-a központi telepítés rendszer használata nem kell helyreállítania. Döntse el, hogy az továbbra is előrelépés az újabb verzióra, vagy inkább visszaállítása egy korábbi verziójának egy konzisztens állapotba fenntartása érdekében szükséges. Kérdéseket, hogy elegendő gép érhetők el, hogy előre történő áthelyezésének és korábbi verzióiban a mikroszolgáltatási helyreállítása például figyelembe kell venni. Ehhez a mikroszolgáltatást gridre bocsáthatja ki az egészségügyi információk ezen döntések.

### <a name="reports-health-and-diagnostics"></a>Jelentések állapot és diagnosztika

Ez nyilvánvalónak tűnhet, és gyakran kihagyott van, de a mikroszolgáltatások kell jelentse, az állapot és diagnosztika. Ellenkező esetben nincs kis betekintést műveletek szempontból az állapotát. Diagnostické události naplókezelője független szolgáltatások között, és kezelése összeállnak az események sorrendjének gép eltérései, is kihívást jelent. Ugyanúgy, mint protokollt konferenciahívások lefolytatása, valamint az adatformátumok a célnyelven mikroszolgáltatások dolgozhat, az egészségügyi és diagnosztikai eseményeket egy eseménytár lekérdezése és megtekintéséhez végső soron megszűnik bejelentkezéshez normalizálnia kell. A mikroszolgáltatási megközelítést, hogy rendelkezik-kulcs különböző csapat egységes naplózási formátum megegyeztünk. Szükség van egy egységes megközelítést diagnosztikai események megtekintése az az alkalmazás egésze.

Diagnosztikai állapot eltér. Állapotfigyelő jelentési a megfelelő műveletet a jelenlegi állapotában a mikroszolgáltatási tárgya. Egy jó példa dolgozik azon, frissítését és telepítését mechanizmusokkal rendelkezésre állását. Bár a szolgáltatás jelenleg nem megfelelő, egy folyamat összeomlása miatt, vagy számítógép-újraindítás, a szolgáltatás továbbra is lehet működési. A legutolsó dolog van szüksége, hogy ennél rosszabb frissít. A legjobb módszer az, hogy először tegye a vizsgálatot, vagy várja meg a mikroszolgáltatások helyreállításához. A mikroszolgáltatások hálózatállapot-események segítsen tájékozott döntéseket hozhat, és érvényben, az önjavítási szolgáltatások létrehozása érdekében.

## <a name="service-fabric-as-a-microservices-platform"></a>A Service Fabric egy mikroszolgáltatás-platform szerint

Az Azure Service Fabric kiderült, amikor a Microsoft a szolgáltatások biztosítása a becsomagolt termékek, általában monolitikus stílus, amely állítjuk át. Élmény épület és működő nagyméretű szolgáltatások, például az Azure SQL Database és az Azure Cosmos DB, a Service Fabric alakú. A platform továbbfejlődtek idővel egyre több szolgáltatás elfogadta azt. A Service Fabric kellett futtatni, nem csak az Azure-ban, hanem a különálló Windows Server-telepítéseket.

***A Service Fabric célja a rögzített kapcsolatos problémák megoldásához létrehozásának és futtatásának és infrastruktúra-erőforrások hatékony, úgy, hogy a csapatok egy mikroszolgáltatás-alapú megközelítéssel üzleti problémák megoldhatók.***

A Service Fabric nyújt azáltal, hogy a mikroszolgáltatási megközelítést használó alkalmazások:

* Egy üzembe helyezéséhez, a rendszer szolgáltatásokat biztosító platform frissítése, észleli, és indítsa újra a sikertelen szolgáltatások, szolgáltatások felderítése, üzenetek, állapot kezelése és állapotának figyelése.
* Alkalmazások üzembe helyezése vagy futó tárolók vagy folyamatok lehetőség. Service Fabric-tároló és a folyamat az orchestrator a.
* Hatékony programozási API-k, alkalmazások, mikroszolgáltatások segíteni: [Az ASP.NET Core Reliable actors – és a Reliable Services](service-fabric-choose-framework.md). Például állapot és diagnosztika információt, vagy a beépített magas rendelkezésre állás előnyeit is.

***A Service Fabric hogyan hozhat létre a szolgáltatás a független, és minden olyan technológiát is használhatja. Azonban olyan beépített programozási API-k, amelyek megkönnyítik a mikroszolgáltatásokat.***

### <a name="migrating-existing-applications-to-service-fabric"></a>Meglévő alkalmazások Service Fabric migrálása

A Service Fabric lehetővé teszi, hogy újra felhasználhatja a meglévő kódot, majd az új mikroszolgáltatások kell modernizálta. Az alkalmazások modernizálása az öt szakaszon, és, elindíthatja és leállíthatja a bármelyik a szakaszokat. Ezek a következők:

1) Indítsa el a hagyományos monolitikus alkalmazások.  
2) Átemelés és Shift - vendégtárolókon és használatával a Service Fabric a meglévő kódot futtatni.  
3) Korszerűsítése – új mikroszolgáltatások hozzá meglévő tárolóalapú kóddal együtt.  
4) Innováció – a monolitikus felosztása mikroszolgáltatások, kell pusztán alapján.  
5) Mikroszolgáltatások – az átalakítás monolitikus alkalmazások meglévő vagy új előzmények nélküli alkalmazások alakítja át.

![Mikroszolgáltatás-alapú áttelepítés][Image3]

Fontos, hogy hangsúlyozzák az, hogy újra **elindítása és leállítása bármelyik, amelyek a következő szakaszok**. Nem köteles haladnak, a következő szakaszra. Most lássuk példák az egyes szakaszokat.

**Átemelése**  
Vállalat nagy számú vannak átemeléséhez meglévő monolitikus alkalmazások tárolókba két oka:

- Költségcsökkentés vagy miatt a konszolidálási és a magasabb sűrűség meglévő hardver- vagy futó alkalmazások eltávolítását.
- Fejlesztés és a műveletek közötti szerződés egységes üzembe helyezést.

A költségcsökkenést érthető, és a Microsofton belül nagyszámú meglévő alkalmazások vannak folyamatban konténeralapú csupán be millió dollárt megtakarítani. Egységes üzembe helyezést kiértékelheti, hogy nehezebben, de ugyanolyan fontos. Azt jelenti, hogy a fejlesztők akkor is lehet, választhatja a technológia, amely a megfelel, azonban a műveleteket csak egyetlen üzembe helyezéséhez és kezeléséhez ezeket az alkalmazásokat úgy fogja fogadni. A műveleteket, hogy számos különböző technológiák összetettsége bajlódnia vagy fejlesztők számára, hogy csak válassza ki az egyes kapcsolatok kényszerítése az enyhíti azt. Lényegében minden alkalmazás tárolóba való önálló központi telepítési lemezképei van.

Számos szervezet megállhat itt. Már van a tárolók előnyeit, és a Service Fabric biztosítja a teljes körű felügyeleti szolgáltatásokat, az üzembe helyezési, frissítések, versioning, visszagörgetése, egészségügyi figyelés stb.

**Modernization**  
Hozzáadása az új szolgáltatások mellett a meglévő kód tárolóba. Ha új kódot írni, célszerű úgy dönt, hogy haladjon kis lépésekben le a mikroszolgáltatás-alapú elérési útját. Lehet, hozzáadása egy új REST API-végpont, és új üzleti logikát. Ezzel a módszerrel, indítsa el a a szállítás új mikroszolgáltatások létrehozása és a gyakorlati fejleszteni és üzembe helyezni őket.

**Az innováció**  
A mikroszolgáltatási megközelítést alkalmazkodik a változó üzleti igények szerint. Ezen a ponton a döntés, hogy kell-e start felosztása a monolitikus app services vagy az innováció. Íme egy példa akkor, ha a munkafolyamat üzenetsorokkal használt adatbázis feldolgozás szűk keresztmetszetté válik. Munkafolyamat száma növekszik kér, mivel a munkahelyi kell elérhetőnek lennie a méretezési csoport. Az alkalmazás, amely nem méretezése folyamatban van, vagy, amelyek gyakran frissíteni kell, hogy részletezhető mikroszolgáltatások ossza ki ez, és az innováció.

**Mikroszolgáltatások alakítva**  
Ez az, ahol az alkalmazás az teljes mértékben a (vagy szét) mikroszolgáltatás-alapú. Szeretné elérni az itt, a mikroszolgáltatások utazás hajtott végre. Itt is elindítható, de ehhez nélkül a mikroszolgáltatás-alapú platform, amely segít jelentős befektetéseket. 

### <a name="are-microservices-right-for-my-application"></a>Azok a mikroszolgáltatások közvetlenül az alkalmazásom?

Lehetséges. Azt tapasztalta, hogy a Microsoft több és több csapat megkezdődött a felhő üzleti okokból hozhat létre, mert ezek közül számos is felismerte, hogy egy a mikroszolgáltatás-hez hasonló módon előnyeinek volt. A Bing, például van már használatával történő fejlesztéséhez mikroszolgáltatások évig. Más csapatok számára új volt a mikroszolgáltatási megközelítést. Csapatok találta, hogy van problémákat megoldani kívül erőssége alapvető területére. Ezért a Service Fabric szerzett elősegítik a technológiai szolgáltatások tetszése szerint.

A Service Fabric célja kiküszöböli a mikroszolgáltatás-alkalmazások létrehozását, így nem kell áthaladnia annyi költséges redesigns csökkentése érdekében. Indítsa el a kis, szükség esetén skálázhatja, szolgáltatások kivezetjük, újakat vehet fel és megemelkedett ügyfelek általi használatot. Is tudjuk, hogy nincsenek-e számos más még megoldandó problémák az a legtöbb fejlesztő több programozása mikroszolgáltatások legyen. Tárolók és a actors programozási modell példák ebben az irányban kis lépésekben, és arról, hogy további innovációkat jelentkezik ez könnyebben vagyunk.
 
<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>További lépések

* [A Service Fabric a terminológia áttekintése](service-fabric-technical-overview.md)
* [Mikroszolgáltatás-alapú: Egy felhőbeli alkalmazásforradalom](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)

[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png
[Image3]: media/service-fabric-overview-microservices/microservices-migration.png
