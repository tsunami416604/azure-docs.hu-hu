---
title: Az Azure mikroszolgáltatások bemutatása |} A Microsoft Docs
description: Miért érdemes a mikroszolgáltatási megközelítést felhőalkalmazások létrehozásához fontos a modern alkalmazások fejlesztését, és hogyan Azure Service Fabric ennek érdekében platformot biztosít áttekintést.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: fae2be85-0ab4-4cd3-9d1f-e0d95fe1959b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/18/2019
ms.author: atsenthi
ms.openlocfilehash: 5bcb52165c7cae18b807eff03c80b51eae8e2717
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204810"
---
# <a name="why-use-a-microservices-approach-to-building-applications"></a>Miért érdemes használni a mikroszolgáltatási megközelítést alkalmazások létrehozásához?

A szoftver-fejlesztők számára egy alkalmazás összetevőit faktoring nincs újdonságunk. Általában egy rétegzett megközelítéssel használnak, egy háttér-tárolót, a középső rétegű üzleti logikát és a egy előtér-felhasználói felületének (UI). Mi *rendelkezik* megváltozott az elmúlt néhány évben, hogy a fejlesztők fejleszt elosztott alkalmazások a felhőben.

Az alábbiakban néhány változó üzleti igények szerint:

* Egy szolgáltatás, amely rendelkezik létrehozott és üzemeltetett egyszerre több földrajzi régióban új ügyfeleket érhet el.
* Gyorsabb tartalomkézbesítés a funkciók és képességek az ügyféligényekre percalapú díjfizetést kínál az válaszol.
* Továbbfejlesztett erőforrás-használat költségek csökkentése érdekében.

Ezek az üzleti igények, lényegében *hogyan* alkalmazások készítünk.

Az Azure mikroszolgáltatás-alapú megközelítést kapcsolatos további információkért lásd: [mikroszolgáltatás-alapú: Egy felhőbeli alkalmazásforradalom](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="monolithic-vs-microservices-design-approach"></a>Monolitikus, és a mikroszolgáltatások tervezési megközelítés

Alkalmazások időbeli alakulásával. Sikeres alkalmazás fel hasznos személyek fejlesztheti tovább. Sikertelen kérelmek nem fejlődnek, és idővel elavulttá váltak. Íme a kérdést: IP-címek fenntartási ismeri a követelményekkel kapcsolatos még ma, és mi legyenek a jövőben? Például tegyük fel, a jelentéskészítő alkalmazás Intézet készít a vállalatnál. Biztos benne az alkalmazás csak a vállalat hatókörén belül vonatkozik, és, hogy a jelentések nem fog tartani hosszú. A megközelítésre eltérő, tegyük fel, videótartalmak létrehozásához egy szolgáltatás, amely továbbítja az ügyfelek tízmillió.

Egyes esetekben a környezetbarát tényező első valamit az ajtó ki, a koncepció igazolása. Biztos, hogy az alkalmazás újabb újratervezve is. Soha nem használt lekéri a fölösleges mérnöki valami kis pont van. Másrészről vállalatok felhőalkalmazások fejlesztése, amikor az elvárás növekedését és a használat. Növekedési és a méretezési csoport is előre nem látható. Szeretnénk prototípus gyorsan közben is ismerete, hogy az egy elérési utat, amely képes kezelni a jövőbeli sikeres vagyunk. Ez az a lean indítási megközelítés: hozhat létre, illetve mérheti, ismerje meg, és ismételt futtatásával.

Az ügyfél-kiszolgáló era során azt növelése az egyes technológiák használatával az egyes szintek többszintű alkalmazások készítésére. Az előfizetési időszak *monolitikus* alkalmazás felmerült, ezek a módszerek leírására. A rétegek közötti kell növelése a felületek, és több szorosan összekapcsolt tervezési használták az egyes szinteken belül összetevői között. Fejlesztők számára tervezett, és a faktorált lefordítva kódtárak és néhány végrehajtható fájlok és DLL-ek egymáshoz kapcsolódó osztályok.

Nincsenek arra, hogy a monolitikus kialakítás megközelítést. Monolitikus alkalmazások gyakran egyszerűbb tervezhet, és -összetevők közötti hívások gyorsabbak, mivel ezeket a hívásokat sokszor folyamatközi kommunikáció (IPC) keresztül. Ezenkívül mindenki teszteli, egyetlen termékhez, amely általában egy emberi erőforrások hatékonyabb kihasználását. A hátránya az, hogy van egy rétegzett rétegek közötti szoros összekapcsolódást, és egyes összetevőket nem skálázhatja. Ha kell tennie a javítások és frissítések, akkor várja meg, mások azok tesztelés befejezésére. Ezért jóval nehezebb az agilisek lehetnek.

Mikroszolgáltatások cím ezek több szempontból sem ajánlott, és további szorosan igazítása a fenti üzleti követelményeinek. De előnyöket és forrásokat is is vannak. A mikroszolgáltatások előnyeit is, hogy mindegyikhez egyszerűbb üzleti funkció, amely, kisebbre vagy nagyobbra méretezhetők, teszteléséhez, üzembe helyezése és kezelése egymástól függetlenül általában magában foglalja. Egy fontos mikroszolgáltatási megközelítést előnye, hogy csapatok alakítják több, mint üzleti forgatókönyvek szerint technológia. Kisebb csapatok egy, az ügyfél forgatókönyv-alapú mikroszolgáltatás fejlesztés, és minden olyan technológiákat, amelyek igénybe szeretnék használni.

Más szóval a szervezet műszaki épülő alkalmazások karbantartásához egységesítésére nem szükséges. Az egyes csapatok, hogy saját szolgáltatás teheti meg logikus mi számukra csapat szaktudását alapján, vagy mi a probléma megoldására legmegfelelőbb. A gyakorlatban egy ajánlott technológiák, például egy adott NoSQL-tároló vagy webalkalmazás-keretrendszer, előnyösebb.

A mikroszolgáltatások hátránya, hogy több különálló-entitások kezelését, és összetettebb telepítések és verziókezelés foglalkozik. A mikroszolgáltatások közötti hálózati forgalom növekszik, mint a megfelelő hálózati késéseket. Nagy mennyiségű forgalmas részletes szolgáltatás egy teljesítmény nightmare okozhat. A függőségek megjelenítéséhez eszközök nélkül meglehetősen nehéz a teljes rendszer megtekintéséhez.

Szabványok győződjön meg arról, a mikroszolgáltatási megközelítést kommunikáció a megadásával, és csak a legfontosabb tudnivaló a szolgáltatásból tolerating munkahelyi helyett merev szerződéseket. Fontos meghatározni a kialakításban meghozni ezek a szerződések, mert a szolgáltatások frissítése egymástól. A mikroszolgáltatási megközelítést tervezéséhez alkotta egy másik leírása a "részletes szolgáltatásorientált architektúra (SOA)."

***A legegyszerűbb a mikroszolgáltatások tervezési megközelítés tárgya egy leválasztott összevonási szolgáltatások, az egyes független módosítások és a kommunikációhoz konferenciahívások lefolytatása előírásoknak.***

Ahogy további felhőalkalmazások előállítása, személyek felderített, hogy ez az alkalmazás általános független, a forgatókönyv témájú szolgáltatásba felbontása-e jobban hosszú távú megközelítést.

## <a name="comparison-between-application-development-approaches"></a>Alkalmazás fejlesztése módszerek összehasonlítása

![A Service Fabric platformot alkalmazásfejlesztés][Image1]

1) A monolitikus alkalmazásokat tartalmazza a tartomány-specifikus szolgáltatásokat, és általában oszlik meg például a webes, az üzleti és működési rétegek.

2) Több kiszolgáló/virtuális gépek klónozásával méretezése a monolitikus alkalmazásokat és tárolókat.

3) A mikroszolgáltatás-alkalmazások elválasztja a kisebb szolgáltatások külön funkciókat.

4) A mikroszolgáltatási megközelítést skálázását követve rugalmasan méretezhető által végzett központi telepítése minden egyes szolgáltatás egymástól függetlenül, ezen példányok létrehozása között kiszolgálók/virtuális gépek és tárolók.

Tervezés a mikroszolgáltatás-alapú a megoldás nem megfelelő összes projekt, de jobban igazodnak a fentebb leírt, az üzleti célkitűzéseinek megvalósulását. A monolitikus megközelítés kezdve előfordulhat, hogy van értelme Ha ismeri a lehetőséget, hogy a kód későbbi újragyártási mikroszolgáltatásokra, rendelkezni fog. Leggyakrabban egy monolitikus alkalmazáshoz kezdődik, és lassan bontsa szakaszban, a funkcionális területeket, kell lennie a méretezhető, vagy Agilis kezdve.

A mikroszolgáltatás-alapú módszer használata esetén az alkalmazás több kis szolgáltatásra, állítsa össze. Ezek a szolgáltatások futnak a gép egy fürtön üzembe helyezett tárolók. Kisebb csapatok fejlesztése egy szolgáltatás, amely eset fókuszál, és egymástól függetlenül tesztelése, verzió, üzembe helyezése és minden egyes szolgáltatás méretezhető, így a teljes alkalmazás tovább lehessen fejleszteni.

## <a name="what-is-a-microservice"></a>Mit jelent a mikroszolgáltatások?

Nincsenek a mikroszolgáltatások külön definíciót. De a mikroszolgáltatási jellemzői vannak körben elfogadott:

* Egy ügyfél vagy az üzleti forgatókönyv magába foglalja. Milyen probléma megoldására?
* Egy kis mérnöki csapata által fejlesztett.
* Bármely keretrendszer használatakor, bármilyen programozási nyelven írt.
* Kód áll, és igény szerint állapot, mindkettő független verziójú, telepített, és van ellátva.
* Együttműködhet más mikroszolgáltatások jól definiált felületek és protokollok.
* Oldja fel a helyet használt egyedi neve (URL-címek).
* Egységes és folytonosságát hibák esetén elérhető marad.

Összegzését, amelyek:

***Mikroszolgáltatás-alkalmazások kisebb, független verziójú és méretezhető ügyfélközpontú szolgáltatások jól meghatározott felületek a szabványos protokollokon keresztül kommunikálnak egymással állnak.***

### <a name="written-in-any-programming-language-using-any-framework"></a>Bármilyen programozási nyelven, bármely keretrendszer használatával írt

Fejlesztők számára nyújtanak szeretnénk tenni, hogy válasszon egy nyelvet vagy keretrendszert, a szolgáltatás, amely hozunk létre az ismeretek és a követelményektől függően. Egyes szolgáltatások előnyeinek előfordulhat, hogy érték C++ fent bármi más. Mások, felügyelt fejlesztés a, a könnyű C# vagy a Java, több fontos lehet. Bizonyos esetekben szükség lehet a szolgáltatást, hogy az ügyfelek által egy adott partner könyvtár, adattárolási módszerektől vagy módszer használata.

Miután kiválasztotta a technológia, fontolja meg az operatív és életciklus-felügyelet és a szolgáltatás méretezése szüksége.

### <a name="allows-code-and-state-to-be-independently-versioned-deployed-and-scaled"></a>Lehetővé teszi a kóddal és állapottal, független verziójú legyen telepítve, és a méretezett

Függetlenül attól, hogy hogyan ír a mikroszolgáltatás-alapú, a kódot, és igény szerint az állapot érdemes egymástól függetlenül üzembe helyezése, frissítése és méretezése. Ez a probléma nem megoldani, mert van, szabadon választott technológiákkal való könnyű feladat. Méretezési, elsajátítása partíció (vagy a szegmensben) a kód és állapota is nagyobb kihívást jelent. Ha a kóddal és állapottal használnak a különböző technológiák, amely közös még ma, a mikroszolgáltatások üzembe helyezési szkriptjei képeseknek kell lenniük mindkét őket. Ez a fajta elkülönítés tárgya is gyorsasága és rugalmassága biztosítja, hogy mindegyiket egyszerre frissítése nélkül frissítheti néhány a mikroszolgáltatások.

Nézzük térjen vissza a monolitikus és mikroszolgáltatás-alapú megközelítés egy pillanatra az összehasonlítást. Ez az ábra állapot tárolásához az módszerek közötti különbségeket mutatja:

#### <a name="state-storage-for-the-two-approaches"></a>A két megközelítés állapot tárolás

![A Service Fabric platformot állapot tárolásához][Image2]

***A monolitikus megközelítés alkalmazásakor, a bal oldali rendelkezik egy önálló adatbázis és az egyes technológiák szint.***

***A mikroszolgáltatási megközelítést, a jobb oldali rendelkezik egy grafikont a mikroszolgáltatások hálózatáról van szó, ahol állapota a mikroszolgáltatások általában hatókörét, és különböző technológiákkal.***

A monolitikus megközelítés a az alkalmazás általában egy adatbázist használ. Egy adatbázis használatával előnye, hogy legyen-e egyetlen helyen, amelynek segítségével könnyedén üzembe helyezéséhez. Minden egyes összetevő állapotában tárolásához egy táblát is rendelkezhet. Csapatok kell szigorúan külön állapotát, amely nehézségeket jelenthet. Teljesítményre valaki lesz gyermekeiről oszlop hozzáadása egy meglévő ügyfél táblázat, a táblák közötti csatlakozzon, és függőségek létrehozása a tárolási rétegben. Ha ez történik, az egyes összetevők nem skálázhatja.

A mikroszolgáltatás-alapú módszert használja, az egyes szolgáltatások kezeli, és tárolja a saját állapota. Minden egyes szolgáltatás méretezése a kód és az állapot együtt, hogy a videólejátszást a szolgáltatás felelős. Egy hátránya, hogy szeretne létrehozni, nézetek és a lekérdezések, az alkalmazásadatok, amikor meg kell több állapot tároló lekérdezésére. Ez a probléma általában megoldani, amely képet alkot között gyűjteménye, mikroszolgáltatások külön mikroszolgáltatások által. Ha az adatok több rögtönzött lekérdezések futtatására van szüksége, fontolja meg írja az egyes mikroszolgáltatások adatokat egy adattárház szolgáltatás offline elemzéshez.

Mikroszolgáltatások verziószámmal is. Különböző verzióinak egymás mellett futtatásához mikroszolgáltatások lehetőség. Egy újabb verziója egy mikroszolgáltatási sikerült sikertelen frissítés során, és állítható vissza egy korábbi verziója szükséges. Verziókezelés is hasznos lehet az a / B-teszteket, ahol különböző felhasználói élmény különböző verzióit a szolgáltatást. Például szokás egy új funkció teszteléséhez mielőtt széles körben közbeni további ügyfelek adott halmazának mikroszolgáltatás frissítése.

### <a name="interacts-with-other-microservices-over-well-defined-interfaces-and-protocols"></a>Más mikroszolgáltatások kommunikál a jól definiált felületek és protokollok

Az elmúlt 10 évben széleskörű információkat ismertető kommunikációs minták a szolgáltatásorientált architektúrák közzétette. Általában a szolgáltatások közötti kommunikáció egy REST módszert használ a HTTP és a TCP protokollt és XML vagy JSON szerializálási formátum szerint. Felület szempontból egy webes Tervező módon szól. Azonban semmit nem kell ne, a bináris protokoll vagy a saját adatformátumok a célnyelven. Csak, vegye figyelembe, hogy az emberek a mikroszolgáltatások használatával, ha ezek a protokollok és formátumok nem némelyik érhetők el nehezebb időt vesz igénybe.

### <a name="has-a-unique-name-url-used-to-resolve-its-location"></a>Oldja fel a hely egyedi neve (URL)

A mikroszolgáltatások kell legyen elérhető, bárhol is fut-e. Ha szeretne áttérni a gépekről, és a egy adott mikroszolgáltatás melyiket fut, dolgot meg rossz gyorsan.

Ugyanolyan módon, hogy a DNS egy adott URL feloldása egy adott géphez a mikroszolgáltatások úgy, hogy az aktuális helyén felderíthető kell egy egyedi nevet. Mikroszolgáltatás-alapú független futnak az infrastruktúra címezhető nevet kell adni. Ez azt jelenti, hogy van egy belőle a szolgáltatás telepítési módját, és hogyan kiderül, mert szükség van egy szolgáltatás beállításjegyzék. Ha egy gép nem sikerül, a beállításjegyzék szolgáltatást kell mondani, ahol a szolgáltatás került.

### <a name="remains-consistent-and-available-in-the-presence-of-failures"></a>Egységes és folytonosságát hibák esetén érhető el

Többé vesződnie a sérült váratlan meghibásodások esetén az egyik nagyon nehéz problémák megoldásában, különösen az elosztott rendszerekben. A kódot, amely a fejlesztők számára nyújtanak írunk számos olyan kivételek kezelése. Tesztelés során is költségei a legtöbb időt a kivételek kezelése. A folyamat bonyolultabb, mint a kódírás hibáinak a kezelése nem. Mi történik, ha a gép, amelyen fut a mikroszolgáltatások nem sikerült? A hiba, amely saját rögzített probléma észlelése kell. De is újra kell indítania a mikroszolgáltatás.

A rendelkezésre állás érdekében mikroszolgáltatások kell lennie a hatással a meghibásodások és a egy másik gépen újra. Ezek a rugalmassági követelmények mellett adatokat nem lehet elveszett, és adatokat kell maradniuk.

Rugalmasság nehezen érhető el, ha hibája a legtöbb esetben egy alkalmazás frissítése során. A mikroszolgáltatás-a központi telepítés rendszer használata nem kell helyreállítania. Kell-e az továbbra is előrelépés az újabb verzióra, vagy visszaállítása egy korábbi verziójának egy konzisztens állapotba fenntartásához meghatározásához. Fontolja meg néhány kérdést, például hogy elég gép érhetők el, hogy előre történő áthelyezésének és korábbi verzióiban a mikroszolgáltatási helyreállítása kell. Ezen döntések van szüksége a mikroszolgáltatási kibocsátható állapotára vonatkozó.

### <a name="reports-health-and-diagnostics"></a>Jelentések állapot és diagnosztika

Nyilvánvaló, úgy tűnhet, és gyakran kihagyott van, de a mikroszolgáltatások kell jelentenie, az állapot és diagnosztika. Ellenkező esetben a health operations szempontból kis betekintést rendelkezik. Diagnostické události naplókezelője független szolgáltatások között, és kezelése összeállnak az események sorrendjének gép eltérései, is kihívást jelent. Ugyanúgy, mint protokollt konferenciahívások lefolytatása, valamint az adatformátumok a célnyelven mikroszolgáltatások dolgozhat, az egészségügyi és diagnosztikai eseményeket egy eseménytár lekérdezése és megtekintéséhez végső soron megszűnik bejelentkezéshez normalizálnia kell. Mikroszolgáltatási megközelítést, a különböző csapatok különböző kell egyetlen naplózási formátum egyeztetni. Szükség van egy egységes megközelítést diagnosztikai események megtekintése az az alkalmazás egésze.

Diagnosztikai állapot eltér. Állapotfigyelő jelentési a megfelelő műveletet a jelenlegi állapotában a mikroszolgáltatási tárgya. Egy jó példa dolgozik azon, frissítését és telepítését mechanizmusokkal rendelkezésre állását. Bár előfordulhat, hogy egy szolgáltatás egy folyamat összeomlása miatt jelenleg nem megfelelő lehet, vagy a számítógép-újraindítás, a szolgáltatás továbbra is lehet működési. A legutolsó dolog, szüksége, hogy a helyzet még rosszabb frissítés megkezdése. A legjobb módszer az, hogy először vizsgálja meg, vagy várja meg a mikroszolgáltatások helyreállításához. A mikroszolgáltatások hálózatállapot-események segítsen tájékozott döntéseket hozhat, és érvényben, az önjavítási szolgáltatások létrehozása érdekében.

## <a name="guidance-for-designing-microservices-on-azure"></a>Útmutató az Azure mikroszolgáltatások tervezése 
Keresse meg az Azure architecture Centert kapcsolatos útmutató a [tervezése és mikroszolgáltatások készítése az Azure-ban](https://docs.microsoft.com/azure/architecture/microservices/).

## <a name="service-fabric-as-a-microservices-platform"></a>A Service Fabric egy mikroszolgáltatás-platform szerint

Az Azure Service Fabric kiderült, amikor a Microsoft a szolgáltatások biztosítása a becsomagolt termékek, általában monolitikus, amely állítjuk át. Élmény épület és működő nagyméretű szolgáltatások, például az Azure SQL Database és az Azure Cosmos DB, a Service Fabric alakú. A platform továbbfejlődtek idővel további szolgáltatások elfogadta azt. A Service Fabric kellett futtatni, nem csak az Azure-ban, hanem a különálló Windows Server-telepítéseket.

***A Service Fabric célja létrehozásának és futtatásának rögzített problémákat megoldani és infrastruktúra-erőforrások hatékony használata, a csapatok a mikroszolgáltatási megközelítést az üzleti problémák megoldásában is.***

A Service Fabric nyújt azáltal, hogy a mikroszolgáltatási megközelítést használó alkalmazások:

* Egy üzembe helyezéséhez, a rendszer szolgáltatásokat biztosító platform frissítése, észleli, és indítsa újra a sikertelen szolgáltatások, szolgáltatások felderítése, üzenetek, állapot kezelése és állapotának figyelése.
* Alkalmazások üzembe helyezése vagy futó tárolók vagy folyamatok lehetővé teszi. Service Fabric-tároló és a folyamat az orchestrator a.
* Hatékony programozási API-k, mikroszolgáltatás-alapú alkalmazások létrehozásához: [Az ASP.NET Core Reliable actors – és a Reliable Services](service-fabric-choose-framework.md). Például állapot és diagnosztika információt, vagy a beépített magas rendelkezésre állás előnyeit is.

***A Service Fabric kapcsolatban, hogyan hozhat létre a szolgáltatás független, és minden olyan technológiát is használhatja. De olyan beépített programozási API-k, amelyek megkönnyítik a mikroszolgáltatásokat.***

### <a name="migrating-existing-applications-to-service-fabric"></a>Meglévő alkalmazások Service Fabric migrálása

A Service Fabric lehetővé teszi, hogy újból felhasználhatja a meglévő kódot, és korszerűsítheti a az új mikroszolgáltatások. Az alkalmazások modernizálása az öt szakaszon, és hogy elindíthatja és leállíthatja a bármely szakaszában. A szakasza a következő:

1) Indítsa el a hagyományos monolitikus alkalmazások.  
2) Telepítse át. Használja a vendégtárolókon és a Service Fabric a meglévő kód futtatására.  
3) Korszerűsítse. Adja hozzá az új mikroszolgáltatások meglévő tárolóalapú kóddal együtt.  
4) Újítás. Szünet a monolitikus alkalmazások mikroszolgáltatásokra igényei alapján.  
5) Alakítsa át a mikroszolgáltatás-alapú alkalmazások. Előzmények nélküli új alkalmazásokat hozhat létre vagy alakíthatja át a meglévő monolitikus alkalmazások.

![Mikroszolgáltatás-alapú áttelepítés][Image3]

Ne feledje, hogy Ön is *elindítása és leállítása bármelyik, amelyek a következő szakaszok*. Nem rendelkezik a folyamat a következő szakaszra. 

Lássunk erre példák az egyes szakaszokat.

**Migrate (Áttelepítés)**  
Két okból számos vállalat áttelepíteni meglévő monolitikus alkalmazások tárolókba:

* Költségek csökkentése, vagy összevonás és a meglévő hardver eltávolítása miatt, vagy magasabb sűrűség alkalmazások futtatása.
* Egységes üzembe helyezést között létrejött szerződés fejlesztésre, működésre.

A költségcsökkenést magától értetődnek. A Microsoft számos meglévő alkalmazások vannak folyamatban tárolóba, akár több millió dollár megtakarítást a vezető. Egységes üzembe helyezést kiértékelheti, hogy nehezebben, de ugyanolyan fontos. Az azt jelenti, hogy a fejlesztők választhat, amelyek megfelelnek az azokat a technológiákat, de műveletek csak egyetlen módszer központi telepítésére és felügyeletére az alkalmazásokat fogja fogadni. Nem kell foglalkozni a különböző technológiák támogatása a fejlesztők számára, hogy csak bizonyos eszközök kiválasztása kényszerítése nélkül összetettsége műveletek enyhíti azt. Lényegében minden alkalmazás tárolóba van önálló központi telepítési lemezképei be.

Számos szervezet megállhat itt. Már van a tárolók előnyeit, és a Service Fabric a teljes körű felügyeleti kezelőfelület, többek között a központi telepítési, frissítési, versioning, visszagörgetése és szolgáltatásállapot-figyelést biztosít.

**Korszerűsítse**  
Modernizálása az új szolgáltatások meglévő tárolóalapú kóddal együtt is. Ha új kódot írni, célszerű haladjon kis lépésekben le a mikroszolgáltatás-alapú elérési útját. Ez azt is jelentheti hozzáadása egy új REST API-végpont vagy az új üzleti logikát. Ezzel a módszerrel indítsa el a folyamat új mikroszolgáltatások létrehozása és a gyakorlati fejleszteni és üzembe helyezni őket.

**Az innováció**  
A mikroszolgáltatási megközelítést alkalmazkodik a változó üzleti igények szerint. Ezen a ponton kell elindítani a monolitikus alkalmazásokat felbonthatja szolgáltatások felosztás vagy innováció kell-e. Itt egy klasszikus például, amikor egy adatbázis, amely egy munkafolyamat-várólista használata feldolgozás szűk keresztmetszetté válik. Munkafolyamat száma növekszik kér, mivel a munkahelyi kell elérhetőnek lennie a méretezési csoport. Az alkalmazás, amely nem méretezése folyamatban van, illetve, hogy több gyakran frissül, és ossza ki, mikroszolgáltatások és az innováció kell, hogy adott szoftvertermék igénybe vehet.

**Mikroszolgáltatás-alapú alkalmazások átalakítása**  
Ezen a ponton az alkalmazás a teljes mértékben a (vagy az felosztott) mikroszolgáltatás-alapú. A mikroszolgáltatások utazás végrehajtott ezen a ponton eléréséhez. Itt is elindítható, de ehhez nélkül a mikroszolgáltatás-alapú platform, amely segít jelentős befektetéseket igényel.

### <a name="are-microservices-right-for-my-application"></a>Azok a mikroszolgáltatások közvetlenül az alkalmazásom?

Lehetséges. A Microsoft több csapat hozhat létre a felhő üzleti okokból kezdődött, ezek közül számos is felismerte, hogy egy a mikroszolgáltatás-hez hasonló módon előnyeit. A Bing, például használja a mikroszolgáltatások évig. Más csapatok számára új volt a mikroszolgáltatási megközelítést. Csapatok találta, hogy van problémákat megoldani kívül erőssége alapvető területére. Ezért a Service Fabric szerzett elősegítik a szolgáltatások technológia.

A Service Fabric célja kiküszöböli a mikroszolgáltatás-alkalmazások létrehozását, így nem kell áthaladnia annyi költséges redesigns csökkentése érdekében. Indítsa el a kis, szükség esetén skálázhatja, szolgáltatások kivezetjük, újakat vehet fel és megemelkedett ügyfelek általi használatot. Is tudjuk, hogy nincsenek-e számos más még megoldandó problémák az a legtöbb fejlesztő több programozása mikroszolgáltatások legyen. Tárolók és a actors programozási modell ebben az irányban kis lépésekben példák. Sajnáljuk, hogy további innovációkat mikroszolgáltatási megközelítést könnyebben jelentkezik.


## <a name="next-steps"></a>További lépések

* [Mikroszolgáltatás-alapú: Egy felhőbeli alkalmazásforradalom](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)
* [Az Azure Architecture Centert: Mikroszolgáltatások létrehozása az Azure-ban](https://docs.microsoft.com/azure/architecture/microservices/)
* [Az Azure Service Fabric-alkalmazás és fürt kapcsolatos ajánlott eljárások](service-fabric-best-practices-overview.md)
* [A Service Fabric a terminológia áttekintése](service-fabric-technical-overview.md)

[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png
[Image3]: media/service-fabric-overview-microservices/microservices-migration.png
