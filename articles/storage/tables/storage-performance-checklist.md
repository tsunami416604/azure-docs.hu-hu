---
title: Teljesítmény- és méretezhetőségi ellenőrzőlista a table storage-hoz - Azure Storage
description: A táblázattárolóval a nagy teljesítményű alkalmazások fejlesztéséhez használt bevált gyakorlatok ellenőrzőlistája.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: tables
ms.openlocfilehash: 89581c8ae2fbdbb55a2abfbd527c8fdcf4b65761
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "75749555"
---
# <a name="performance-and-scalability-checklist-for-table-storage"></a>Teljesítmény- és méretezhetőségi ellenőrzőlista a táblázattároláshoz

A Microsoft számos bevált gyakorlatot fejlesztett ki a table storage-szal rendelkező nagy teljesítményű alkalmazások fejlesztésére. Ez az ellenőrzőlista azonosítja azokat a kulcsfontosságú gyakorlatokat, amelyeket a fejlesztők követhetnek a teljesítmény optimalizálása érdekében. Tartsa szem előtt ezeket a gyakorlatokat, miközben az alkalmazás tervezése és a folyamat során.

Az Azure Storage méretezhetőségi és teljesítménycélokat biztosít a kapacitás, a tranzakciós díj és a sávszélesség számára. Az Azure Storage méretezhetőségi céljairól további információt a [méretezhetőségi és teljesítménycélok a normál tárfiókokhoz,](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json) valamint [a Table storage méretezhetőségi és teljesítménycéljai című témakörben](scalability-targets.md)talál.

## <a name="checklist"></a>Ellenőrzőlista

Ez a cikk a teljesítményre vonatkozó bevált gyakorlatokat egy ellenőrzőlistába rendezi, amelyet a Table storage alkalmazás fejlesztése során követhet.

| Kész | Kategória | Tervezési szempontok |
| --- | --- | --- |
| &nbsp; |Méretezhetőségi célok |[Meg tudja tervezni az alkalmazást úgy, hogy legfeljebb a tárfiókok maximális számát használja?](#maximum-number-of-storage-accounts) |
| &nbsp; |Méretezhetőségi célok |[Elkerüli a kapacitás- és tranzakciós korlátok közeledését?](#capacity-and-transaction-targets) |
| &nbsp; |Méretezhetőségi célok |[Megközelíti az entitások méretezhetőségi céljait másodpercenként?](#targets-for-data-operations) |
| &nbsp; |Hálózat |[Az ügyféloldali eszközök elegendően nagy sávszélességgel és alacsony késleltetéssel rendelkeznek a szükséges teljesítmény eléréséhez?](#throughput) |
| &nbsp; |Hálózat |[Az ügyféloldali eszközök kiváló minőségű hálózati kapcsolattal rendelkeznek?](#link-quality) |
| &nbsp; |Hálózat |[Az ügyfélalkalmazás ugyanabban a régióban, mint a tárfiók?](#location) |
| &nbsp; |Közvetlen ügyfél-hozzáférés |[Megosztott hozzáférésű aláírásokat (SAS) és több forrásból származó erőforrás-megosztást (CORS) használ az Azure Storage közvetlen elérésének engedélyezéséhez?](#sas-and-cors) |
| &nbsp; |Kötegelés |[Az alkalmazás kötegelési frissítései entitáscsoport-tranzakciók használatával történik?](#batch-transactions) |
| &nbsp; |.NET-konfiguráció |[A .NET Core 2.1-es vagy újabb verziót használja az optimális teljesítmény érdekében?](#use-net-core) |
| &nbsp; |.NET-konfiguráció |[Úgy állította be az ügyfelet, hogy elegendő számú egyidejű kapcsolatot használjon?](#increase-default-connection-limit) |
| &nbsp; |.NET-konfiguráció |[A .NET alkalmazások esetében a .NET érték megfelelő számú szálat használt?](#increase-minimum-number-of-threads) |
| &nbsp; |Párhuzamosság |[Biztosította, hogy a párhuzamosság megfelelően legyen határolódva, hogy ne terhelje túl az ügyfél képességeit, és ne közelítse meg a méretezhetőségi célokat?](#unbounded-parallelism) |
| &nbsp; |Eszközök |[A Microsoft által biztosított ügyfélkódtárak és -eszközök legújabb verzióját használja?](#client-libraries-and-tools) |
| &nbsp; |Újrapróbálkozások |[Az újrapróbálkozási szabályzatot exponenciális visszalépéssel használja a szabályozási hibák és időmegadások esetén?](#timeout-and-server-busy-errors) |
| &nbsp; |Újrapróbálkozások |[Elkerüli az alkalmazás az újrapróbálkozásokat a nem újrapróbálható hibák miatt?](#non-retryable-errors) |
| &nbsp; |Konfiguráció |[JSON-t használ az asztali kérelmekhez?](#use-json) |
| &nbsp; |Konfiguráció |[Kikapcsolta a Nagle algoritmust, hogy javítsa a kis kérések teljesítményét?](#disable-nagle) |
| &nbsp; |Táblák és partíciók |[Megfelelően particionált az adatok?](#schema) |
| &nbsp; |Forró partíciók |[Elkerüli a csak hozzáfűzést és a csak előlegként tartalmazó mintákat?](#append-only-and-prepend-only-patterns) |
| &nbsp; |Forró partíciók |[A lapkák/frissítések sok partícióközött vannak elosztva?](#high-traffic-data) |
| &nbsp; |Lekérdezési hatókör |[Úgy tervezte meg a sémát, hogy a legtöbb esetben a pontlekérdezések, a táblalekérdezések pedig takarékosan legyenek használva?](#query-scope) |
| &nbsp; |Lekérdezés sűrűsége |[A lekérdezések általában csak az alkalmazás által használt sorok bekésése és visszaadása?](#query-density) |
| &nbsp; |A visszaadott adatok korlátozása |[Szűrést használ a szükségtelen entitások visszaküldésének elkerülésére?](#limiting-the-amount-of-data-returned) |
| &nbsp; |A visszaadott adatok korlátozása |[Vetületet használ a szükségtelen tulajdonságok visszaküldésének elkerülésére?](#limiting-the-amount-of-data-returned) |
| &nbsp; |Denormalizáció |[Denormalizálta az adatokat úgy, hogy elkerülje a nem hatékony lekérdezéseket vagy több olvasási kérelmet az adatok beolvasásakor?](#denormalization) |
| &nbsp; |Beszúrás, frissítés és törlés |[Olyan kérelmekkötegelése, amelyeknek tranzakciósnak kell lenniük, vagy egyidejűleg elvégezhetők az oda-vissza utak csökkentése érdekében?](#batching) |
| &nbsp; |Beszúrás, frissítés és törlés |[Elkerüli az entitás beolvasását, csak azért, hogy megállapítsa, hogy a beszúrást vagy a frissítést hívja-e?](#upsert) |
| &nbsp; |Beszúrás, frissítés és törlés |[Fontolóra vette már az olyan adatsorok tárolását, amelyeket gyakran együtt olvas le egyetlen entitásban, több entitás helyett tulajdonságokként?](#storing-data-series-in-a-single-entity) |
| &nbsp; |Beszúrás, frissítés és törlés |[Az entitások, amelyek mindig együtt lesznek beolvasva, és kötegekben (például idősorozat-adatokban) írhatók, fontolja meg blobok használatát a táblák helyett?](#storing-structured-data-in-blobs) |

## <a name="scalability-targets"></a>Méretezhetőségi célok

Ha az alkalmazás megközelíti vagy meghaladja a skálázhatósági célok bármelyikét, megnövekedett tranzakció-késésekkel vagy szabályozással találkozhat. Amikor az Azure Storage szabályozza az alkalmazást, a szolgáltatás 503 (kiszolgáló foglalt) vagy 500 (operation timeout) hibakódokat ad vissza. Ezek a hibák elkerülése a skálázhatósági célok határain belül való tartózkodással fontos része az alkalmazás teljesítményének növelésének.

A Table szolgáltatás méretezhetőségi céljairól a [Táblázattárolás méretezhetőségi és teljesítménycéljai](scalability-targets.md)című témakörben talál további információt.

### <a name="maximum-number-of-storage-accounts"></a>Tárfiókok maximális száma

Ha megközelíti az adott előfizetés/régió kombináció engedélyezett tárfiókok maximális számát, több tárfiókot használ a szegmensek számára a kimenő, kimenő, I/O-műveletek másodpercenkénti (IOPS) vagy kapacitás növelése érdekében? Ebben a forgatókönyvben a Microsoft azt javasolja, hogy a tárfiókok megnövekedett korlátainak kihasználásával csökkentse a számítási feladatokhoz szükséges tárfiókok számát, ha lehetséges. Lépjen kapcsolatba [az Azure-támogatással,](https://azure.microsoft.com/support/options/) és kérjen megnövelt korlátozásokat a tárfiókhoz. További információ: [Nagyobb, nagyobb méretű tárfiókok bejelentése.](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)

### <a name="capacity-and-transaction-targets"></a>Kapacitás- és tranzakciócélok

Ha az alkalmazás megközelíti a skálázhatósági célok at egy tárfiók, fontolja meg az alábbi megközelítések:  

- Gondolja át újra a számítási feladatok, amely miatt az alkalmazás megközelíti vagy meghaladja a méretezhetőségi cél. Meg tudja tervezni másképpen, hogy kevesebb sávszélességet vagy kapacitást használjon, vagy kevesebb tranzakciót?
- Ha az alkalmazás nak meg kell haladnia a méretezhetőségi célok egyikét, majd hozzon létre több tárfiókot, és particionálja az alkalmazásadatait a több tárfiókok között. Ha ezt a mintát használja, akkor ügyeljen arra, hogy tervezze meg az alkalmazást, hogy a jövőben további tárfiókokat adhat hozzá a terheléselosztáshoz. A tárfiókok maguk nak nincs más költsége, mint a használat a tárolt adatok, tranzakciók vagy átvitt adatok tekintetében.
- Ha az alkalmazás megközelíti a sávszélesség-célokat, fontolja meg az adatok tömörítése az ügyféloldalon, hogy csökkentse az adatok küldéséhez szükséges az Azure Storage-ba.
    Az adatok tömörítése sávszélességet takaríthat meg és javíthatja a hálózati teljesítményt, ezért negatív hatással lehet a teljesítményre is. Értékelje ki az adattömörítésre és a dekompresszióra vonatkozó további feldolgozási követelmények teljesítményhatását az ügyféloldalon. Ne feledje, hogy a tömörített adatok tárolása megnehezítheti a hibaelhárítást, mivel az adatok szabványos eszközökkel történő megtekintése nagyobb kihívást jelenthet.
- Ha az alkalmazás közeledik a méretezhetőségi célokat, majd győződjön meg arról, hogy exponenciális visszamaradást használ az újrapróbálkozások. A legjobb, ha megpróbálja elkerülni a méretezhetőségi célok elérését az ebben a cikkben ismertetett javaslatok végrehajtásával. Azonban egy exponenciális backoff újrapróbálkozások megakadályozza, hogy az alkalmazás gyorsan újrapróbálkozik, ami ronthatja a szabályozást. További információt az [Időelési és kiszolgálói foglaltsági hibák](#timeout-and-server-busy-errors)című szakaszban talál.

### <a name="targets-for-data-operations"></a>Adatműveletek céljai

Az Azure Storage terhelési egyenlegek, ahogy a tárfiók forgalma növekszik, de ha a forgalom hirtelen adatlöketeket mutat, előfordulhat, hogy nem tudja azonnal leadni ezt a átviteli mennyiséget. A sávszélesség-szabályozás és/vagy időtúlterhelések várhatóak a sorozatsorozat során, mivel az Azure Storage automatikusan betölti a táblát. A lassú felfutás általában jobb eredményeket eredményez, mivel a rendszernek van ideje a megfelelő terheléselosztásra.

#### <a name="entities-per-second-storage-account"></a>Entitások másodpercenként (tárfiók)

A táblák elérésére vonatkozó méretezhetőségi korlát egy fiók esetében másodpercenként legfeljebb 20 000 entitás (egyenként 1 KB). Általában minden beszúrt, frissített, törölt vagy beolvasott entitás beleszámít ebbe a célba. Így egy köteglapka, amely 100 entitást tartalmaz, 100 entitásnak számít. Egy 1000 entitást betekintő és 5-öt visszaad tartalmazó lekérdezés 1000 entitásnak számít.

#### <a name="entities-per-second-partition"></a>Entitások másodpercenként (partíció)

Egyetlen partíción belül a táblák eléréséhez a méretezhetőségi cél 2000 entitás (1 KB/1 KB) másodpercenként, az előző szakaszban leírtaknak használt azonos számláláshasználatával.

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

## <a name="batch-transactions"></a>Kötegelt tranzakciók

A Table szolgáltatás támogatja az ugyanabban a táblában lévő és ugyanahhoz a partíciócsoporthoz tartozó entitások kötegtranzakcióit. További információ: [Az entitáscsoport-tranzakciók végrehajtása.](/rest/api/storageservices/performing-entity-group-transactions)

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

## <a name="configuration"></a>Konfiguráció

Ez a szakasz számos gyorskonfigurációs beállítást sorol fel, amelyekkel jelentős teljesítménybeli javulást érhet el a Table szolgáltatásban:

### <a name="use-json"></a>JSON használata

A storage service 2013-08-15-ös verziójától kezdve a Table szolgáltatás támogatja a JSON használatát az XML-alapú AtomPub formátum helyett a táblaadatok átviteléhez. A JSON használatával akár 75%-kal is csökkentheti a hasznos adatméretét, és jelentősen javíthatja az alkalmazás teljesítményét.

További információt a [Microsoft Azure-táblázatok: A JSON](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/05/windows-azure-tables-introducing-json.aspx) és [a Hasznos tartalom formátumának bemutatása table service operations című bejegyzésben talál.](https://msdn.microsoft.com/library/azure/dn535600.aspx)

### <a name="disable-nagle"></a>Nagle letiltása

A Nagle algoritmusa széles körben elterjedt a TCP/IP hálózatokon, mint a hálózati teljesítmény javításának eszköze. Azonban nem optimális minden körülmények között (például a rendkívül interaktív környezetben). A Nagle algoritmusa negatív hatással van az Azure Table szolgáltatáshoz érkező kérések teljesítményére, és ha lehetséges, tiltsa le.

## <a name="schema"></a>Séma

Az adatok ábrázolásának és lekérdezésének a legnagyobb tényező, amely befolyásolja a Table szolgáltatás teljesítményét. Bár minden alkalmazás más, ez a szakasz néhány általános, bevált gyakorlatot vázol fel, amelyek a következőkre vonatkoznak:

- Táblázat kialakítása
- Hatékony lekérdezések
- Hatékony adatfrissítések

### <a name="tables-and-partitions"></a>Táblák és partíciók

A táblák partíciókra vannak osztva. A partíción tárolt minden entitás ugyanazt a partíciókulcsot osztja meg, és rendelkezik egy egyedi sorkulcssal a partíción belüli azonosításához. A partíciók előnyöket biztosítanak, de méretezhetőségi korlátokat is bevezetnek.

- Előnyök: Az entitások ugyanazon a partíción egyetlen, atomi, kötegelt tranzakció, amely legfeljebb 100 különálló tárolási műveletek (legfeljebb 4 MB teljes méret) tartalmazó entitásokat frissíthet. Feltéve, hogy ugyanannyi entitást kell beolvasni, akkor is lekérdezheti az adatokat egy partíción belül hatékonyabban, mint az adatok, amelyek átfogja a partíciókat (bár olvassa el a további javaslatokat a táblaadatok lekérdezése).
- Méretezhetőségi korlát: Az egyetlen partíción tárolt entitásokhoz való hozzáférés nem lehet terheléselosztással, mert a partíciók támogatják az atomi kötegtranzakciókat. Emiatt az egyes táblapartíciók méretezhetőségi célja alacsonyabb, mint a Table szolgáltatás egésze számára.

A táblák és válaszfalak ezen jellemzői miatt a következő tervezési elveket kell alkalmazni:

- Keresse meg azokat az adatokat, amelyeket az ügyfélalkalmazás gyakran frissít vagy lekérdezést végez ugyanabban a logikai munkaegységben ugyanabban a partíción. Keresse meg például az adatokat ugyanabban a partícióban, ha az alkalmazás összesíti az írásokat, vagy atomi kötegműveleteket hajt végre. Emellett az egyetlen partícióban lévő adatok hatékonyabban lekérdezhetők egyetlen lekérdezésben, mint a partíciók közötti adatok.
- Keresse meg azokat az adatokat, amelyeket az ügyfélalkalmazás nem szúr be, frissít vagy nem frissít ugyanabban a logikai munkaegységben (azaz egyetlen lekérdezésben vagy kötegfrissítésben) külön partíciókon. Ne feledje, hogy egyetlen táblában nincs korlátozva a partíciókulcsok száma, így a több millió partíciókulcs nem jelent problémát, és nem befolyásolja a teljesítményt. Ha például az alkalmazás egy népszerű webhely, ahol a felhasználói bejelentkezés, a felhasználói azonosító használata partíciókulcsként jó választás lehet.

#### <a name="hot-partitions"></a>Forró partíciók

A rendszer olyan gyorsã mã átãorzta, amely a forgalom aránytalan százalékát kapja egy fiókhoz, és nem lehet terheléselosztással elhatárolt, mert egyetlen partíció. Általánosságban elmondható, hogy a forró partíciók kétféleképpen hozhatók létre:

#### <a name="append-only-and-prepend-only-patterns"></a>Csak hozzáfűzése és Csak az Előkészítés csak mintázata

A "Csak hozzáfűzése" minta az egyik, ahol az összes (vagy szinte az összes) a forgalom egy adott partíciókulcs növekszik és csökken az aktuális idő szerint. Tegyük fel például, hogy az alkalmazás az aktuális dátumot használja partíciókulcsként a naplóadatokhoz. Ez a kialakítás azt eredményezi, hogy az összes lapka a tábla utolsó partíciójára kerül, és a rendszer nem tudja megfelelően betölteni az egyensúlyt. Ha az adott partícióra irányuló forgalom mennyisége meghaladja a partíciószintű méretezhetőségi célt, akkor a rendszer szabályozást eredményez. Jobb, ha biztosítja, hogy a forgalom küldése több partícióra, hogy a terheléselosztás a kérelmek et a táblán keresztül.

#### <a name="high-traffic-data"></a>Nagy forgalmú adatok

Ha a particionálási séma egyetlen partíciót eredményez, amely csak olyan adatokat, amelyek sokkal több használt, mint más partíciók, akkor is láthatja a szabályozás, mint a partíció megközelíti a méretezhetőségi cél egyetlen partícióra. Jobb, ha győződjön meg arról, hogy a partícióséma nem eredményez egyetlen partíciót a méretezhetőségi célokat.

### <a name="querying"></a>Lekérdezés

Ez a szakasz a Table szolgáltatás lekérdezésének bevált gyakorlatait ismerteti.

#### <a name="query-scope"></a>Lekérdezési hatókör

A lekérdezni kívánt entitások tartományát többféleképpen is megadhatja. Az alábbi lista a lekérdezéshatókör egyes lehetőségeit ismerteti.

- **Pontlekérdezések:**- A pontlekérdezés pontosan egy entitást kér le a lekérni kívánt entitás partíciókulcsának és sorkulcsának megadásával. Ezek a lekérdezések hatékonyak, és ahol csak lehetséges, használja őket.
- **Partíciós lekérdezések:** A partíciós lekérdezés olyan lekérdezés, amely egy közös partíciókulcsot használó adathalmazt olvas be. A lekérdezés általában egy sorkulcs-értékek tartományát vagy egy entitástulajdonság értéktartományát adja meg a partíciókulcs mellett. Ezek a lekérdezések kevésbé hatékonyak, mint a pontlekérdezések, és takarékosan kell használni.
- **Táblalekérdezések:** A táblalekérdezés olyan lekérdezés, amely olyan entitások készletét olvassa be, amelyek nem osztoznak közös partíciókul. Ezek a lekérdezések nem hatékonyak, és ha lehetséges, kerülje őket.

Általában kerülje a vizsgálatok (lekérdezések nagyobb, mint egy entitás), de ha kell beolvasni, próbálja meg úgy szervezni az adatokat, hogy a vizsgálatok letölteni a szükséges adatokat anélkül, hogy beolvassa, vagy vissza jelentős mennyiségű entitások nem szükséges.

#### <a name="query-density"></a>Lekérdezés sűrűsége

A lekérdezés hatékonyságának másik kulcsfontosságú tényezője a visszaadott entitások száma a visszaadott készlet megkereséséhez beolvasott entitások számához képest. Ha az alkalmazás egy olyan tulajdonságértéket tartalmazó szűrővel rendelkező táblalekérdezést hajt végre, amely csak az adatmegosztások 1%-a, a lekérdezés 100 entitást fog bekeresni minden egyes általa visszaadott entitáshoz. A korábban tárgyalt tábla méretezhetőségi céljai a beolvasott entitások számára vonatkoznak, nem pedig a visszaadott entitások számára: az alacsony lekérdezési sűrűség könnyen okozhatja, hogy a Table szolgáltatás szabályozhatja az alkalmazást, mert olyan sok entitást kell beolvasnia a keresett entitás lekérése. A szabályozás elkerüléséről a [Denormalizáció](#denormalization)című részben talál további információt.

#### <a name="limiting-the-amount-of-data-returned"></a>A visszaadott adatok mennyiségének korlátozása

Ha tudja, hogy egy lekérdezés olyan entitásokat ad vissza, amelyekre nincs szüksége az ügyfélalkalmazásban, fontolja meg egy szűrő használatát a visszaadott készlet méretének csökkentéséhez. Bár az ügyfélnek vissza nem adott entitások továbbra is beleszámítanak a méretezhetőségi korlátokba, az alkalmazás teljesítménye javulni fog a csökkentett hálózati hasznos terhelés és az ügyfélalkalmazás által feldolgozandó entitások csökkentett száma miatt. Ne feledje, hogy a méretezhetőségi célok a beolvasott entitások számához kapcsolódnak, így egy lekérdezés, amely kiszűri a sok entitást, továbbra is szabályozást eredményezhet, még akkor is, ha kevés entitást ad vissza. A lekérdezések hatékonysá tételéről a [Lekérdezési sűrűség](#query-density)című szakaszban talál további információt.

Ha az ügyfélalkalmazásnak csak korlátozott tulajdonságkészletre van szüksége a táblában lévő entitásoktól, a vetítés segítségével korlátozhatja a visszaadott adatkészlet méretét. A szűréshez is a vetítés segít csökkenteni a hálózati terhelést és az ügyfelek feldolgozását.

#### <a name="denormalization"></a>Denormalizáció

A relációs adatbázisokkal ellentétben a táblaadatok hatékony lekérdezésének bevált gyakorlatai az adatok denormalizálásához vezetnek. Ez azt, hogy ugyanazokat az adatokat több entitásban (minden kulcshoz egyet az adatok kereséséhez) megkettőzve minimalizálja azon entitások számát, amelyeket a lekérdezésnek be kell keresnie az ügyfél számára szükséges adatok megtalálásához, ahelyett, hogy nagy számú entitást kellene beszkatennie a alkalmazási igényeket. Előfordulhat például, hogy egy e-kereskedelmi webhelyen meg szeretne találni egy rendelést mind a vevőazonosító (add nekem ennek a vevőnek a rendelései) és a dátum szerint (adj nekem rendeléseket egy dátumon). A Table Storage-ban a legjobb, ha az entitást (vagy egy hivatkozást) kétszer tárolja – egyszer a Táblanév, a PK és az RK segítségével, hogy megkönnyítse az ügyfélazonosító alapján történő keresés, hogy megkönnyítse a keresés dátumig.  

### <a name="insert-update-and-delete"></a>Beszúrás, frissítés és törlés

Ez a szakasz a Table szolgáltatásban tárolt entitások módosításának bevált gyakorlatait ismerteti.  

#### <a name="batching"></a>Kötegelés

A kötegelt tranzakciók at entitáscsoport-tranzakcióknak nevezzük az Azure Storage-ban. Az entitáscsoport-tranzakción belüli összes műveletnek egyetlen partíción kell lennie egyetlen táblában. Ahol lehetséges, használjon entitáscsoport-tranzakciókat beszúrások, frissítések és törlések kötegekben történő végrehajtásához. Az entitáscsoport-tranzakciók használata csökkenti az ügyfélalkalmazásból a kiszolgálóra irányuló adatváltások számát, csökkenti a számlázható tranzakciók számát (az entitáscsoport tranzakciója számlázási célból egyetlen tranzakciónak számít, és legfeljebb 100-at tartalmazhat tárolási műveletek), és lehetővé teszi a tomifrissítések (minden művelet sikeres, vagy az összes sikertelen egy entitáscsoport tranzakció). A nagy késleltetésű környezetek, például a mobileszközök nagy mértékben profitálnak az entitáscsoport-tranzakciók használatából.  

#### <a name="upsert"></a>Beszúrás és frissítés

Ahol csak lehetséges, használja **az Upsert** asztali műveleteket. Két típusú **Upsert**, amelyek mindegyike hatékonyabb lehet, mint a hagyományos **Beszúrás** és **Frissítés** műveletek:  

- **InsertOrMerge**: Akkor használja ezt a műveletet, ha az entitás tulajdonságainak egy részét szeretné feltölteni, de nem biztos abban, hogy az entitás már létezik-e. Ha az entitás létezik, ez a hívás frissíti a **Upsert** műveletben szereplő tulajdonságokat, és az összes meglévő tulajdonságot úgy hagyja, ahogy vannak, ha az entitás nem létezik, beszúrja az új entitást. Ez hasonló a lekérdezésben való vetület hez, mivel csak a változó tulajdonságokat kell feltöltenie.
- **InsertOrReplace**: Akkor használja ezt a műveletet, ha egy teljesen új entitást szeretne feltölteni, de nem biztos abban, hogy létezik-e már. Akkor használja ezt a műveletet, ha tudja, hogy az újonnan feltöltött entitás teljesen helyes, mert teljesen felülírja a régi entitást. Például frissíteni szeretné azt az entitást, amely a felhasználó aktuális helyét tárolja, függetlenül attól, hogy az alkalmazás korábban tárolta-e a felhasználó helyadatait; az új hely entitás teljes, és nincs szüksége semmilyen korábbi entitástól származó információra.

#### <a name="storing-data-series-in-a-single-entity"></a>Adatsorok tárolása egyetlen entitásban

Előfordulhat, hogy egy alkalmazás olyan adatsorokat tárol, amelyeket gyakran egyszerre kell beolvasnia: például egy alkalmazás nyomon követheti a PROCESSZOR-használatot az idő múlásával, hogy az elmúlt 24 óra adatainak gördülődiagramját ábrázolhassa. Az egyik megközelítés az, hogy óránként egy tábla entitás, minden entitás egy adott órát képvisel, és tárolja a CPU-használat az adott órában. Az adatok nyomtatásához az alkalmazásnak le kell kérnie a 24 legutóbbi órából származó adatokat tároló entitásokat.  

Másik lehetőségként az alkalmazás tárolhatja a CPU-használat minden órában egy külön tulajdonsága egyetlen entitás: minden órában frissítéséhez az alkalmazás egyetlen **InsertOrMerge Upsert** hívás segítségével frissítheti az értéket a legutóbbi óra. Az adatok nyomtatásához az alkalmazásnak csak egyetlen entitást kell beolvasnia 24 helyett, így hatékony lekérdezést kell készítenie. A lekérdezések hatékonyságáról a [Lekérdezéshatókör](#query-scope)című szakaszban talál további információt.

#### <a name="storing-structured-data-in-blobs"></a>Strukturált adatok tárolása blobokban

Ha kötegelt beszúrásokat hajt végre, majd együtt kéri az entitások tartományainak lekérését, fontolja meg a blobok használatát a táblák helyett. Jó példa erre a naplófájl. Több percnyi naplót kötegelhet, és beszúrhatja őket, majd egyszerre több percnyi naplót is lekérhet. Ebben az esetben a teljesítmény jobb, ha táblák helyett blobokat használ, mivel jelentősen csökkentheti a beírt vagy olvasott objektumok számát, és esetleg a szükséges kérelmek számát is.  

## <a name="next-steps"></a>További lépések

- [Méretezhetőségi és teljesítménycélok a táblázattároláshoz](scalability-targets.md)
- [Méretezhetőségi és teljesítménycélok a szabványos tárfiókokhoz](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)
- [Állapot- és hibakódok](/rest/api/storageservices/Status-and-Error-Codes2)
