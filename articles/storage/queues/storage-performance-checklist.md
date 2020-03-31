---
title: Teljesítmény- és méretezhetőségi ellenőrzőlista a várólista-tároláshoz – Azure Storage
description: A várólista-tárolóval a nagy teljesítményű alkalmazások fejlesztéséhez használt bevált gyakorlatok ellenőrzőlistája.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: queues
ms.openlocfilehash: eb1821537e6e25b05dfdca3107729eecf4c6e1bf
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "75750500"
---
# <a name="performance-and-scalability-checklist-for-queue-storage"></a>Teljesítmény- és méretezhetőségi ellenőrzőlista a várólista-tároláshoz

A Microsoft számos bevált gyakorlatot fejlesztett ki a nagy teljesítményű alkalmazások várólistára történő tárolással történő fejlesztéséhez. Ez az ellenőrzőlista azonosítja azokat a kulcsfontosságú gyakorlatokat, amelyeket a fejlesztők követhetnek a teljesítmény optimalizálása érdekében. Tartsa szem előtt ezeket a gyakorlatokat, miközben az alkalmazás tervezése és a folyamat során.

Az Azure Storage méretezhetőségi és teljesítménycélokat biztosít a kapacitás, a tranzakciós díj és a sávszélesség számára. Az Azure Storage méretezhetőségi céljairól további információt a [méretezhetőségi és teljesítménycélok a normál tárfiókokhoz,](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) valamint [a méretezhetőségi és teljesítménycélok a várólista-tároláshoz](scalability-targets.md)című témakörben talál.

## <a name="checklist"></a>Ellenőrzőlista

Ez a cikk a teljesítményre vonatkozó bevált eljárásokat egy ellenőrzőlistába rendezi, amelyet a várólista-tárolási alkalmazás fejlesztése során követhet.

| Kész | Kategória | Tervezési szempontok |
| --- | --- | --- |
| &nbsp; |Méretezhetőségi célok |[Meg tudja tervezni az alkalmazást úgy, hogy legfeljebb a tárfiókok maximális számát használja?](#maximum-number-of-storage-accounts) |
| &nbsp; |Méretezhetőségi célok |[Elkerüli a kapacitás- és tranzakciós korlátok közeledését?](#capacity-and-transaction-targets) |
| &nbsp; |Hálózat |[Az ügyféloldali eszközök elegendően nagy sávszélességgel és alacsony késleltetéssel rendelkeznek a szükséges teljesítmény eléréséhez?](#throughput) |
| &nbsp; |Hálózat |[Az ügyféloldali eszközök kiváló minőségű hálózati kapcsolattal rendelkeznek?](#link-quality) |
| &nbsp; |Hálózat |[Az ügyfélalkalmazás ugyanabban a régióban, mint a tárfiók?](#location) |
| &nbsp; |Közvetlen ügyfél-hozzáférés |[Megosztott hozzáférésű aláírásokat (SAS) és több forrásból származó erőforrás-megosztást (CORS) használ az Azure Storage közvetlen elérésének engedélyezéséhez?](#sas-and-cors) |
| &nbsp; |.NET-konfiguráció |[A .NET Core 2.1-es vagy újabb verziót használja az optimális teljesítmény érdekében?](#use-net-core) |
| &nbsp; |.NET-konfiguráció |[Úgy állította be az ügyfelet, hogy elegendő számú egyidejű kapcsolatot használjon?](#increase-default-connection-limit) |
| &nbsp; |.NET-konfiguráció |[A .NET alkalmazások esetében a .NET érték megfelelő számú szálat használt?](#increase-minimum-number-of-threads) |
| &nbsp; |Párhuzamosság |[Biztosította, hogy a párhuzamosság megfelelően legyen határolódva, hogy ne terhelje túl az ügyfél képességeit, és ne közelítse meg a méretezhetőségi célokat?](#unbounded-parallelism) |
| &nbsp; |Eszközök |[A Microsoft által biztosított ügyfélkódtárak és -eszközök legújabb verzióját használja?](#client-libraries-and-tools) |
| &nbsp; |Újrapróbálkozások |[Az újrapróbálkozási szabályzatot exponenciális visszalépéssel használja a szabályozási hibák és időmegadások esetén?](#timeout-and-server-busy-errors) |
| &nbsp; |Újrapróbálkozások |[Elkerüli az alkalmazás az újrapróbálkozásokat a nem újrapróbálható hibák miatt?](#non-retryable-errors) |
| &nbsp; |Konfiguráció |[Kikapcsolta a Nagle algoritmust, hogy javítsa a kis kérések teljesítményét?](#disable-nagle) |
| &nbsp; |Üzenet mérete |[Az üzenetek kompaktak a várólista teljesítményének javítása érdekében?](#message-size) |
| &nbsp; |Tömeges visszakeresés |[Több üzenetet keres egyetlen GET műveletben?](#batch-retrieval) |
| &nbsp; |A lekérdezés gyakorisága |[Elég gyakran van lekérdezése ahhoz, hogy csökkentse az alkalmazás észlelt késését?](#queue-polling-interval) |
| &nbsp; |Üzenet frissítése |[Az Üzenet frissítése művelettel tárolja az üzenetek feldolgozásának előrehaladását, így nem kell újra feldolgoznia a teljes üzenetet, ha hiba történik?](#use-update-message) |
| &nbsp; |Architektúra |[Várólisták használatával, hogy a teljes alkalmazás skálázható azáltal, hogy a hosszú ideig futó számítási feladatok ki a kritikus elérési út és a méretezés, majd egymástól függetlenül?](#application-architecture) |

## <a name="scalability-targets"></a>Méretezhetőségi célok

Ha az alkalmazás megközelíti vagy meghaladja a skálázhatósági célok bármelyikét, megnövekedett tranzakció-késésekkel vagy szabályozással találkozhat. Amikor az Azure Storage szabályozza az alkalmazást, a szolgáltatás 503 (kiszolgáló foglalt) vagy 500 (operation timeout) hibakódokat ad vissza. Ezek a hibák elkerülése a skálázhatósági célok határain belül való tartózkodással fontos része az alkalmazás teljesítményének növelésének.

A várólista-szolgáltatás méretezhetőségi céljairól az [Azure Storage méretezhetőségi és teljesítménycélok című](/azure/storage/queues/scalability-targets#scale-targets-for-queue-storage)témakörben talál további információt.

### <a name="maximum-number-of-storage-accounts"></a>Tárfiókok maximális száma

Ha megközelíti az adott előfizetés/régió kombináció engedélyezett tárfiókok maximális számát, több tárfiókot használ a szegmensek számára a kimenő, kimenő, I/O-műveletek másodpercenkénti (IOPS) vagy kapacitás növelése érdekében? Ebben a forgatókönyvben a Microsoft azt javasolja, hogy a tárfiókok megnövekedett korlátainak kihasználásával csökkentse a számítási feladatokhoz szükséges tárfiókok számát, ha lehetséges. Lépjen kapcsolatba [az Azure-támogatással,](https://azure.microsoft.com/support/options/) és kérjen megnövelt korlátozásokat a tárfiókhoz. További információ: [Nagyobb, nagyobb méretű tárfiókok bejelentése.](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)

### <a name="capacity-and-transaction-targets"></a>Kapacitás- és tranzakciócélok

Ha az alkalmazás megközelíti a skálázhatósági célok at egy tárfiók, fontolja meg az alábbi megközelítések:  

- Ha a várólisták méretezhetőségi céljai nem elegendőek az alkalmazás számára, akkor használjon több várólistát, és ossza el az üzeneteket közöttük.
- Gondolja át újra a számítási feladatok, amely miatt az alkalmazás megközelíti vagy meghaladja a méretezhetőségi cél. Meg tudja tervezni másképpen, hogy kevesebb sávszélességet vagy kapacitást használjon, vagy kevesebb tranzakciót?
- Ha az alkalmazás nak meg kell haladnia a méretezhetőségi célok egyikét, majd hozzon létre több tárfiókot, és particionálja az alkalmazásadatait a több tárfiókok között. Ha ezt a mintát használja, akkor ügyeljen arra, hogy tervezze meg az alkalmazást, hogy a jövőben további tárfiókokat adhat hozzá a terheléselosztáshoz. A tárfiókok maguk nak nincs más költsége, mint a használat a tárolt adatok, tranzakciók vagy átvitt adatok tekintetében.
- Ha az alkalmazás megközelíti a sávszélesség-célokat, fontolja meg az adatok tömörítése az ügyféloldalon, hogy csökkentse az adatok küldéséhez szükséges az Azure Storage-ba.
    Az adatok tömörítése sávszélességet takaríthat meg és javíthatja a hálózati teljesítményt, ezért negatív hatással lehet a teljesítményre is. Értékelje ki az adattömörítésre és a dekompresszióra vonatkozó további feldolgozási követelmények teljesítményhatását az ügyféloldalon. Ne feledje, hogy a tömörített adatok tárolása megnehezítheti a hibaelhárítást, mivel az adatok szabványos eszközökkel történő megtekintése nagyobb kihívást jelenthet.
- Ha az alkalmazás közeledik a méretezhetőségi célokat, majd győződjön meg arról, hogy exponenciális visszamaradást használ az újrapróbálkozások. A legjobb, ha megpróbálja elkerülni a méretezhetőségi célok elérését az ebben a cikkben ismertetett javaslatok végrehajtásával. Azonban egy exponenciális backoff újrapróbálkozások megakadályozza, hogy az alkalmazás gyorsan újrapróbálkozik, ami ronthatja a szabályozást. További információt az [Időelési és kiszolgálói foglaltsági hibák](#timeout-and-server-busy-errors)című szakaszban talál.

## <a name="networking"></a>Hálózat

Az alkalmazás fizikai hálózati korlátai jelentős hatással lehetnek a teljesítményre. A következő szakaszok néhány olyan korlátozást ismertetik, amelyekkel a felhasználók találkozhatnak.  

### <a name="client-network-capability"></a>Ügyfélhálózati képesség

A sávszélesség és a hálózati kapcsolat minősége fontos szerepet játszik az alkalmazások teljesítményében, akövetkező szakaszokban leírtak szerint.

#### <a name="throughput"></a>Teljesítmény

A sávszélesség esetében a probléma gyakran az ügyfél képességei. A nagyobb Azure-példányok hálózati adapterek nagyobb kapacitással rendelkeznek, ezért érdemes egy nagyobb példányt vagy több virtuális gépet használni, ha egyetlen gépről magasabb hálózati korlátokra van szüksége. Ha az Azure Storage-t egy helyszíni alkalmazásból éri el, akkor ugyanaz a szabály vonatkozik: ismerje meg az ügyféleszköz hálózati képességeit és az Azure Storage helyéhez való hálózati kapcsolatot, és szükség szerint javítsa őket, vagy tervezze meg alkalmazás a képességeiken belül.

#### <a name="link-quality"></a>Kapcsolat minősége

Mint minden hálózati használat, ne feledje, hogy a hálózati feltételeket eredményező hibák és csomagvesztés lassítja a hatékony átviteli.  A WireShark vagy a NetMon használata segíthet a probléma diagnosztizálásában.  

### <a name="location"></a>Hely

Bármely elosztott környezetben az ügyfél kiszolgáló közelében történő elhelyezése a legjobb teljesítményt nyújtja. A legalacsonyabb késleltetéssel rendelkező Azure Storage eléréséhez az ügyfél számára a legjobb hely ugyanabban az Azure-régióban található. Ha például egy Azure-webalkalmazás, amely az Azure Storage-t használja, keresse meg őket egy régióban, például az USA nyugati vagy délkeleti régióban. Az erőforrások közös helytcsoportosítása csökkenti a késést és a költségeket, mivel a sávszélesség-használat egyetlen régión belül ingyenes.  

Ha az ügyfélalkalmazások hozzáférnek az Azure Storage-hoz, de nem találhatók az Azure-ban, például mobileszköz-alkalmazásokban vagy helyszíni vállalati szolgáltatásokban, akkor a tárfiók az ügyfelek közelében lévő régióban való helytkeresése csökkentheti a késést. Ha az ügyfelek széles körben elosztott (például néhány Észak-Amerikában, és néhány Európában), akkor fontolja meg egy tárfiók régiónként. Ez a megközelítés könnyebben megvalósítható, ha az alkalmazás által tárolt adatok az egyes felhasználók számára, és nem igényel adatok replikálása a tárfiókok között.

## <a name="sas-and-cors"></a>SAS és CORS

Tegyük fel, hogy engedélyeznie kell a kódot, például a JavaScript, amely fut a felhasználó webböngészőben vagy egy mobiltelefon-alkalmazásban az Adatok eléréséhez az Azure Storage-ban. Az egyik megközelítés az, hogy hozzon létre egy szolgáltatásalkalmazást, amely proxyként működik. A felhasználó eszköze hitelesíti magát a szolgáltatással, amely viszont engedélyezi az Azure Storage-erőforrásokhoz való hozzáférést. Ily módon elkerülheti a tárfiók kulcsainak felfedését a nem biztonságos eszközökön. Ez a megközelítés azonban jelentős terhelést ró a szolgáltatásalkalmazásra, mivel a felhasználó eszköze és az Azure Storage között átvitt összes adatnak át kell haladnia a szolgáltatásalkalmazáson.

A megosztott hozzáférésű aláírások (SAS) használatával elkerülheti, hogy egy szolgáltatásalkalmazást az Azure Storage proxyjaként használjon. A SAS használatával engedélyezheti, hogy a felhasználó eszköze kéréseket küldjön közvetlenül az Azure Storage-ba egy korlátozott hozzáférési jogkivonat használatával. Ha például egy felhasználó szeretne feltölteni egy fényképet az alkalmazásba, majd a szolgáltatásalkalmazás létrehozhat egy SAS-t, és elküldheti azt a felhasználó eszközére. A SAS-jogkivonat engedélyt adhat egy Azure Storage-erőforrásnak egy megadott ideig, amely után a SAS-jogkivonat lejár. A SAS-ről további információt az [Azure Storage-erőforrásokhoz megosztott hozzáférésű aláírások (SAS) használatával való korlátozott hozzáférés megadása](../common/storage-sas-overview.md)című témakörben talál.  

A webböngészők általában nem engedélyezik a JavaScript et az egyik tartomány egyik webhelye által üzemeltetett lapon, hogy bizonyos műveleteket, például írási műveleteket hajtson végre egy másik tartományban. Az azonos eredetű házirendként ismert házirend megakadályozza, hogy egy rosszindulatú parancsfájl az egyik oldalon hozzáférjen egy másik weblapon lévő adatokhoz. Az azonos eredetű szabályzat azonban korlátozás lehet a felhőben történő megoldás létrehozásakor. A kereszteredetű erőforrás-megosztás (CORS) egy olyan böngészőszolgáltatás, amely lehetővé teszi, hogy a céltartomány kommunikáljon a böngészővel arról, hogy megbízik a forrástartományból származó kérelmekben.

Tegyük fel például, hogy egy Azure-ban futó webalkalmazás egy azure Storage-fiókhoz kér egy erőforrást. A webalkalmazás a forrástartomány, a tárfiók pedig a céltartomány. A CORS konfigurálhatja az Azure Storage-szolgáltatások bármely kommunikálni a webböngésző, amely a kérelmeket a forrástartomány megbízható az Azure Storage. A CORS szolgáltatásról további információt az [Azure Storage több forrásból származó erőforrás-megosztási (CORS) támogatása című témakörben talál.](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services)  
  
A SAS és a CORS egyaránt segít elkerülni a webes alkalmazás szükségtelen terhelését.  

## <a name="net-configuration"></a>.NET-konfiguráció

A .  Ha más nyelveket használ, ellenőrizze, hogy hasonló fogalmak vonatkoznak-e a választott nyelvre.  

### <a name="use-net-core"></a>A .NET Core használata

A teljesítménybeli fejlesztések előnyeinek kihasználásához fejlesztheti Azure Storage-alkalmazásait a .NET Core 2.1-es vagy újabb verzióival. A .NET Core 3.x használata lehetőség szerint ajánlott.

A .NET Core teljesítményjavulásáról az alábbi blogbejegyzésekben talál további információt:

- [Teljesítménybeli fejlesztések a .NET Core 3.0-ban](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)
- [Teljesítménybeli fejlesztések a .NET Core 2.1-ben](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)

### <a name="increase-default-connection-limit"></a>Az alapértelmezett kapcsolatkorlát növelése

A .NET-ben a következő kód 100-ra növeli az alapértelmezett kapcsolatkorlátot (amely ügyfélkörnyezetben általában 2, kiszolgálói környezetben 10). Általában az értéket az alkalmazás által használt szálak számának megfelelően kell beállítani.  

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

A kapcsolatok megnyitása előtt állítsa be a kapcsolatkorlátot.  

Más programozási nyelvek esetében tekintse meg a nyelv dokumentációját, hogy hogyan állítsa be a kapcsolati korlátot.  

További információt a Web [Services: Concurrent Connections](https://blogs.msdn.microsoft.com/darrenj/2005/03/07/web-services-concurrent-connections/)című blogbejegyzésben talál.  

### <a name="increase-minimum-number-of-threads"></a>A szálak minimális számának növelése

Ha szinkron hívásokat és aszinkron feladatokat használ, érdemes lehet növelni a szálak számát a szálkészletben:

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

További információt a [ThreadPool.SetMinThreads](/dotnet/api/system.threading.threadpool.setminthreads) metódusban talál.  

## <a name="unbounded-parallelism"></a>Határtalan párhuzamosság

Míg a párhuzamosság nagyszerű lehet a teljesítményhez, legyen óvatos a nem kötött párhuzamosság használatával kapcsolatban, ami azt jelenti, hogy nincs korlátozva a szálak vagy a párhuzamos kérelmek száma. Ügyeljen arra, hogy korlátozza az adatok feltöltésére vagy letöltésére, az ugyanazon tárfiók több partíciójának elérésére, vagy ugyanazon partíció több elemének elérésére vonatkozó párhuzamos kérelmeket. Ha a párhuzamosság nincs korlátozva, az alkalmazás meghaladhatja az ügyféleszköz képességeit vagy a tárfiók méretezhetőségi céljait, ami hosszabb késéseket és szabályozást eredményez.  

## <a name="client-libraries-and-tools"></a>Ügyfélkönyvtárak és -eszközök

A legjobb teljesítmény érdekében mindig a Microsoft által biztosított legújabb ügyfélkönyvtárakat és eszközöket használja. Az Azure Storage ügyfélkódtárak számos nyelven érhetők el. Az Azure Storage támogatja a PowerShellt és az Azure CLI-t is. A Microsoft aktívan fejleszti ezeket az ügyfélkönyvtárakat és -eszközöket a teljesítmény szem előtt tartásával, naprakészen tartja őket a legújabb szolgáltatásverziókkal, és biztosítja, hogy a bevált teljesítménygyakorlatok nagy részét belsőleg kezeljék. További információt az [Azure Storage referenciadokumentációjában](/azure/storage/#reference)talál.

## <a name="handle-service-errors"></a>Szolgáltatási hibák kezelése

Az Azure Storage hibaüzenetet ad vissza, ha a szolgáltatás nem tud feldolgozni egy kérelmet. Az Azure Storage által egy adott forgatókönyvben visszaadható hibák megértése hasznos a teljesítmény optimalizálásához.

### <a name="timeout-and-server-busy-errors"></a>Idő- és kiszolgálófoglalt sági hibák

Az Azure Storage szabályozhatja az alkalmazást, ha megközelíti a méretezhetőségi korlátokat. Bizonyos esetekben előfordulhat, hogy az Azure Storage nem tudja kezelni a kérelmet valamilyen átmeneti állapot miatt. Mindkét esetben a szolgáltatás 503 (kiszolgáló foglalt) vagy 500 (Időmegadás) hibát adhat vissza. Ezek a hibák akkor is előfordulhatnak, ha a szolgáltatás újraegyensúlyozza az adatpartíciókat, hogy nagyobb átviteli terhelést tegyen lehetővé. Az ügyfélalkalmazásnak általában újra meg kell próbálnia azt a műveletet, amely a hibák egyikét okozza. Ha azonban az Azure Storage szabályozása az alkalmazás, mert meghaladja a méretezhetőségi célokat, vagy akkor is, ha a szolgáltatás nem tudta kiszolgálni a kérelmet valamilyen más okból, agresszív újrapróbálkozások ronthatja a problémát. Exponenciális biztonsági másolatot aretry házirend használata ajánlott, és az ügyfélkódtárak alapértelmezés szerint ezt a viselkedést. Előfordulhat például, hogy az alkalmazás 2 másodperc, majd 4 másodperc, majd 10 másodperc, majd 30 másodperc után újrapróbálkozik, majd teljesen feladja. Ily módon az alkalmazás jelentősen csökkenti a terhelést a szolgáltatás, ahelyett, hogy súlyosbítja a viselkedést, amely oda vezethet, hogy a szabályozás.  

A kapcsolódási hibák azonnal újrapróbálkozhatók, mert nem szabályozás eredménye, és várhatóan átmenetiek lesznek.  

### <a name="non-retryable-errors"></a>Nem újrapróbálható hibák

Az ügyféltárak úgy kezelik az újrapróbálkozásokat, hogy mely hibák kísérhetők meg újra, és melyek nem. Azonban ha közvetlenül hívja meg az Azure Storage REST API-t, vannak olyan hibák, amelyeket nem szabad újra megpróbálnia. Például egy 400 (Hibás kérelem) hiba azt jelzi, hogy az ügyfélalkalmazás olyan kérelmet küldött, amelyet nem lehetett feldolgozni, mert nem volt a várt formában. A kérelem újraküldése minden alkalommal ugyanazt a választ eredményezi, így nincs értelme újrakipróbálni. Ha közvetlenül hívja meg az Azure Storage REST API-t, vegye figyelembe a lehetséges hibákat, és hogy meg kell-e próbálni őket.

Az Azure Storage hibakódjairól az [Állapot- és hibakódok című témakörben](/rest/api/storageservices/status-and-error-codes2)talál további információt.

## <a name="disable-nagle"></a>Nagle letiltása

A Nagle algoritmusa széles körben elterjedt a TCP/IP hálózatokon, mint a hálózati teljesítmény javításának eszköze. Azonban nem optimális minden körülmények között (például a rendkívül interaktív környezetben). A Nagle algoritmusa negatív hatással van az Azure Table szolgáltatáshoz érkező kérések teljesítményére, és ha lehetséges, tiltsa le.

## <a name="message-size"></a>Üzenet mérete

A várólista teljesítménye és méretezhetősége az üzenetek méretének növekedésével csökken. Csak a címzettnek szükséges információkat helyezze el egy üzenetben.  

## <a name="batch-retrieval"></a>Köteglekérések

Egy várólistából legfeljebb 32 üzenetet lehet beolvasni egyetlen műveletben. A kötegelt lekérés csökkentheti az ügyfélalkalmazásból származó odautazások számát, ami különösen hasznos a nagy késleltetéssel rendelkező környezetekben, például mobileszközökön.  

## <a name="queue-polling-interval"></a>Várólista lekérdezési időköze

A legtöbb alkalmazás leszavazása egy várólistából érkező üzenetek, amely lehet az egyik legnagyobb tranzakcióforrások az adott alkalmazáshoz. Válassza ki a lekérdezési időköz bölcsen: a lekérdezés túl gyakran okozhat az alkalmazás megközelíteni a skálázhatósági célokat a várólista. Bármennyire, -on 200,000 lebonyolítás részére $0.01 ( abban az időpont -ból írás), egy egyes folyamat szavazás egyszer egyszer mind második részére egy hónap akar ár kevésbé mint 15 cent tehát ár van általában egy tényező amit befolyásol -a válogatott -ból szavazás időköz.  

A legfrissebb költséginformációkat az [Azure Storage díjszabása](https://azure.microsoft.com/pricing/details/storage/)című témakörben talál.  

## <a name="use-update-message"></a>A Frissítési üzenet használata

Az Üzenet **frissítése** művelettel növelheti a láthatatlanságidő-túltöltést, vagy frissítheti az üzenet állapotadatait. Az **Üzenet frissítése** szolgáltatás hatékonyabb lehet, mint egy olyan munkafolyamat, amely egy feladatot egyik várólistából a másikba ad át, mivel a feladat minden egyes lépése befejeződött. Az alkalmazás mentheti a feladat állapotát az üzenetbe, majd folytathatja a munkát ahelyett, hogy minden lépésnél újrasorolná az üzenetet a feladat következő lépéséhez. Ne feledje, hogy minden **frissítési üzenet** művelet beleszámít a méretezhetőségi célba.

## <a name="application-architecture"></a>Alkalmazásarchitektúra

A várólisták segítségével méretezhetővé teheti az alkalmazásarchitektúrát. Az alábbi lista felsorolja, hogyan használhatja a várólistákat az alkalmazás méretezhetőbbé válására:  

- A várólisták segítségével munkaelmaradásokat hozhat létre az alkalmazás ban, és elsimíthatja a munkaterheléseket. Például várólistára veheti a felhasználók kéréseit, hogy processzorigényes munkát végezzenek, például átméretezheti a feltöltött képeket.
- A várólisták segítségével leválaszthatja az alkalmazás egyes részeit, így egymástól függetlenül méretezheti őket. Például egy webes előtér-felhasználók felmérési eredményeket helyezhet el egy várólistában későbbi elemzés és tárolás érdekében. Szükség szerint további feldolgozói szerepkör-példányokat adhat hozzá a várólista-adatok feldolgozásához.  

## <a name="next-steps"></a>További lépések

- [Méretezhetőségi és teljesítménycélok a várólista-tároláshoz](scalability-targets.md)
- [Méretezhetőségi és teljesítménycélok a szabványos tárfiókokhoz](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [Állapot- és hibakódok](/rest/api/storageservices/Status-and-Error-Codes2)
