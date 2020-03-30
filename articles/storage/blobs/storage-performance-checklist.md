---
title: Teljesítmény- és méretezhetőségi ellenőrzőlista a Blob storage-hoz - Azure Storage
description: A Blob storage-szal a nagy teljesítményű alkalmazások fejlesztéséhez használt bevált gyakorlatok ellenőrzőlistája.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: e4103f8360f6fa80470b0f8002a61f8ac903bd8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255430"
---
# <a name="performance-and-scalability-checklist-for-blob-storage"></a>Teljesítmény- és méretezhetőségi ellenőrzőlista a Blob storage-hoz

A Microsoft számos bevált gyakorlatot fejlesztett ki a blob-tárolóval rendelkező nagy teljesítményű alkalmazások fejlesztéséhez. Ez az ellenőrzőlista azonosítja azokat a kulcsfontosságú gyakorlatokat, amelyeket a fejlesztők követhetnek a teljesítmény optimalizálása érdekében. Tartsa szem előtt ezeket a gyakorlatokat, miközben az alkalmazás tervezése és a folyamat során.

Az Azure Storage méretezhetőségi és teljesítménycélokat biztosít a kapacitás, a tranzakciós díj és a sávszélesség számára. Az Azure Storage méretezhetőségi céljairól további információt a [méretezhetőségi és teljesítménycélok a szabványos tárfiókokhoz,](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) valamint [a Blob storage méretezhetőségi és teljesítménycéljai című témakörben](scalability-targets.md)talál.

## <a name="checklist"></a>Ellenőrzőlista

Ez a cikk a teljesítménybizonyított gyakorlatait egy ellenőrzőlistába rendezi, amelyet a Blob storage-alkalmazás fejlesztése során követhet.

| Kész | Kategória | Tervezési szempontok |
| --- | --- | --- |
| &nbsp; |Méretezhetőségi célok |[Meg tudja tervezni az alkalmazást úgy, hogy legfeljebb a tárfiókok maximális számát használja?](#maximum-number-of-storage-accounts) |
| &nbsp; |Méretezhetőségi célok |[Elkerüli a kapacitás- és tranzakciós korlátok közeledését?](#capacity-and-transaction-targets) |
| &nbsp; |Méretezhetőségi célok |[Nagyszámú ügyfél egyidejűleg fér hozzá egy blobhoz?](#multiple-clients-accessing-a-single-blob-concurrently) |
| &nbsp; |Méretezhetőségi célok |[Az alkalmazás egyetlen blob méretezhetőségi célokon belül marad?](#bandwidth-and-operations-per-blob) |
| &nbsp; |Particionálás |[Az elnevezési konvenció takara a jobb terheléselosztásérdekében?](#partitioning) |
| &nbsp; |Hálózat |[Az ügyféloldali eszközök elegendően nagy sávszélességgel és alacsony késleltetéssel rendelkeznek a szükséges teljesítmény eléréséhez?](#throughput) |
| &nbsp; |Hálózat |[Az ügyféloldali eszközök kiváló minőségű hálózati kapcsolattal rendelkeznek?](#link-quality) |
| &nbsp; |Hálózat |[Az ügyfélalkalmazás ugyanabban a régióban, mint a tárfiók?](#location) |
| &nbsp; |Közvetlen ügyfélhozzáférés |[Megosztott hozzáférésű aláírásokat (SAS) és több forrásból származó erőforrás-megosztást (CORS) használ az Azure Storage közvetlen elérésének engedélyezéséhez?](#sas-and-cors) |
| &nbsp; |Gyorsítótárazás |[Az alkalmazás gyorsítótárazási adatok, amelyek gyakran elérhető, és ritkán változott?](#reading-data) |
| &nbsp; |Gyorsítótárazás |[Az alkalmazás kötegelési frissítések gyorsítótárazásával az ügyfél, majd feltölti őket a nagyobb készletek?](#uploading-data-in-batches) |
| &nbsp; |.NET-konfiguráció |[A .NET Core 2.1-es vagy újabb verziót használja az optimális teljesítmény érdekében?](#use-net-core) |
| &nbsp; |.NET-konfiguráció |[Úgy állította be az ügyfelet, hogy elegendő számú egyidejű kapcsolatot használjon?](#increase-default-connection-limit) |
| &nbsp; |.NET-konfiguráció |[A .NET alkalmazások esetében a .NET érték megfelelő számú szálat használt?](#increase-minimum-number-of-threads) |
| &nbsp; |Párhuzamosság |[Biztosította, hogy a párhuzamosság megfelelően legyen határolódva, hogy ne terhelje túl az ügyfél képességeit, és ne közelítse meg a méretezhetőségi célokat?](#unbounded-parallelism) |
| &nbsp; |Eszközök |[A Microsoft által biztosított ügyfélkódtárak és -eszközök legújabb verzióját használja?](#client-libraries-and-tools) |
| &nbsp; |Újrapróbálkozások |[Az újrapróbálkozási szabályzatot exponenciális visszalépéssel használja a szabályozási hibák és időmegadások esetén?](#timeout-and-server-busy-errors) |
| &nbsp; |Újrapróbálkozások |[Elkerüli az alkalmazás az újrapróbálkozásokat a nem újrapróbálható hibák miatt?](#non-retryable-errors) |
| &nbsp; |Blobok másolása |[A leghatékonyabb módon másolja a blobokat?](#blob-copy-apis) |
| &nbsp; |Blobok másolása |[Az AzCopy legújabb verzióját használja a tömeges másolási műveletekhez?](#use-azcopy) |
| &nbsp; |Blobok másolása |[Az Azure Data Box család nagy mennyiségű adat importálásához használja?](#use-azure-data-box) |
| &nbsp; |Tartalom terjesztése |[CDN-t használ a tartalom terjesztéséhez?](#content-distribution) |
| &nbsp; |Metaadatok használata |[Gyakran használt metaadatokat tárol a blobokról a metaadataikban?](#use-metadata) |
| &nbsp; |Gyors feltöltés |[Amikor megpróbál feltölteni egy blobot gyorsan, párhuzamosan tölt fel blokkokat?](#upload-one-large-blob-quickly) |
| &nbsp; |Gyors feltöltés |[Amikor megpróbál feltölteni sok blobok gyorsan, ön feltöltése blobok párhuzamosan?](#upload-many-blobs-quickly) |
| &nbsp; |Blobtípus |[Oldalblobokat vagy blokkblobokat használ, ha szükséges?](#choose-the-correct-type-of-blob) |

## <a name="scalability-targets"></a>Méretezhetőségi célok

Ha az alkalmazás megközelíti vagy meghaladja a skálázhatósági célok bármelyikét, megnövekedett tranzakció-késésekkel vagy szabályozással találkozhat. Amikor az Azure Storage szabályozza az alkalmazást, a szolgáltatás 503 (kiszolgáló foglalt) vagy 500 (operation timeout) hibakódokat ad vissza. Ezek a hibák elkerülése a skálázhatósági célok határain belül való tartózkodással fontos része az alkalmazás teljesítményének növelésének.

A várólista-szolgáltatás méretezhetőségi céljairól az [Azure Storage méretezhetőségi és teljesítménycélok című](/azure/storage/queues/scalability-targets#scale-targets-for-queue-storage)témakörben talál további információt.

### <a name="maximum-number-of-storage-accounts"></a>Tárfiókok maximális száma

Ha megközelíti az adott előfizetés/régió kombinációhoz engedélyezett tárfiókok maximális számát, értékelje ki a forgatókönyvet, és állapítsa meg, hogy az alábbi feltételek bármelyike érvényes-e:

- A nem felügyelt lemezek tárolására és a lemezek virtuális gépekhez való hozzáadására használja a tárfiókokat? Ebben az esetben a Microsoft felügyelt lemezek használatát javasolja. A felügyelt lemezek automatikusan és az egyes tárfiókok létrehozása és kezelése nélkül méretezhetők. További információ: [Bevezetés az Azure által felügyelt lemezekbe](../../virtual-machines/windows/managed-disks-overview.md)
- Ügyfélenként egy tárfiókot használ az adatok elkülönítése céljából? Ebben a forgatókönyvben a Microsoft azt javasolja, hogy egy blob tároló minden ügyfél, ahelyett, hogy egy teljes tárfiók. Az Azure Storage mostantól lehetővé teszi, hogy szerepköralapú hozzáférés-vezérlési (RBAC) szerepköröket rendeljen tárolónként. További információ: [Hozzáférés megadása az Azure blobés várólista-adatok rbac az Azure portalon.](../common/storage-auth-aad-rbac-portal.md)
- Több tárfiókok at shard a kimenő forgalom növelése, kimenő forgalom, I/O-műveletek másodpercenként (IOPS) vagy kapacitás. Ebben a forgatókönyvben a Microsoft azt javasolja, hogy a tárfiókok megnövekedett korlátainak kihasználásával csökkentse a számítási feladatokhoz szükséges tárfiókok számát, ha lehetséges. Lépjen kapcsolatba [az Azure-támogatással,](https://azure.microsoft.com/support/options/) és kérjen megnövelt korlátozásokat a tárfiókhoz. További információ: [Nagyobb, nagyobb méretű tárfiókok bejelentése.](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)

### <a name="capacity-and-transaction-targets"></a>Kapacitás- és tranzakciócélok

Ha az alkalmazás megközelíti a skálázhatósági célok at egy tárfiók, fontolja meg az alábbi megközelítések:  

- Ha az alkalmazás eléri a tranzakciós célt, fontolja meg a blokkblob-tárfiókok használatát, amelyek magas tranzakciós arányokra és alacsony és konzisztens konzisztens késésre vannak optimalizálva. További információkat az [Azure Storage-fiókok áttekintésében](../common/storage-account-overview.md) találhat.
- Gondolja át újra a számítási feladatok, amely miatt az alkalmazás megközelíti vagy meghaladja a méretezhetőségi cél. Meg tudja tervezni másképpen, hogy kevesebb sávszélességet vagy kapacitást használjon, vagy kevesebb tranzakciót?
- Ha az alkalmazás nak meg kell haladnia a méretezhetőségi célok egyikét, majd hozzon létre több tárfiókot, és particionálja az alkalmazásadatait a több tárfiókok között. Ha ezt a mintát használja, akkor ügyeljen arra, hogy tervezze meg az alkalmazást, hogy a jövőben további tárfiókokat adhat hozzá a terheléselosztáshoz. A tárfiókok maguk nak nincs más költsége, mint a használat a tárolt adatok, tranzakciók vagy átvitt adatok tekintetében.
- Ha az alkalmazás megközelíti a sávszélesség-célokat, fontolja meg az adatok tömörítése az ügyféloldalon, hogy csökkentse az adatok küldéséhez szükséges az Azure Storage-ba.
    Az adatok tömörítése sávszélességet takaríthat meg és javíthatja a hálózati teljesítményt, ezért negatív hatással lehet a teljesítményre is. Értékelje ki az adattömörítésre és a dekompresszióra vonatkozó további feldolgozási követelmények teljesítményhatását az ügyféloldalon. Ne feledje, hogy a tömörített adatok tárolása megnehezítheti a hibaelhárítást, mivel az adatok szabványos eszközökkel történő megtekintése nagyobb kihívást jelenthet.
- Ha az alkalmazás közeledik a méretezhetőségi célokat, majd győződjön meg arról, hogy exponenciális visszamaradást használ az újrapróbálkozások. A legjobb, ha megpróbálja elkerülni a méretezhetőségi célok elérését az ebben a cikkben ismertetett javaslatok végrehajtásával. Azonban egy exponenciális backoff újrapróbálkozások megakadályozza, hogy az alkalmazás gyorsan újrapróbálkozik, ami ronthatja a szabályozást. További információt az [Időelési és kiszolgálói foglaltsági hibák](#timeout-and-server-busy-errors)című szakaszban talál.

### <a name="multiple-clients-accessing-a-single-blob-concurrently"></a>Több ügyfél egyidejűleg fér hozzá egy blobhoz

Ha egy blob egyidejű legkülönbözőb-ben egy blob hoz elérhető nagy számú ügyfél, akkor figyelembe kell vennie a blobonként és a tárfiók méretezhetőségi célokonként is. Az egyetlen blobhoz hozzáférő ügyfelek pontos száma olyan tényezőktől függően változik, mint például a blobot egyidejűleg kérő ügyfelek száma, a blob mérete és a hálózati feltételek.

Ha a blob cdn-en keresztül terjeszthető, például egy webhelyről kiszolgált képek vagy videók, akkor cdn-t is használhat. További információt a [Tartalomterjesztés](#content-distribution)című szakaszban talál.

Más esetekben, például tudományos szimulációk, ahol az adatok bizalmasak, két lehetőség közül választhat. Az első az, hogy a számítási feladatok hozzáférését úgy, hogy a blob érhető el egy ideig, illetve egyidejűleg érhető el. Azt is megteheti, hogy ideiglenesen másolja a blob több tárfiókok blobonként és a storage-fiókok között a teljes IOPS növelése érdekében. Az eredmények az alkalmazás viselkedésétől függően változnak, ezért győződjön meg róla, hogy a tervezés során teszteli az egyidejűségi mintákat.

### <a name="bandwidth-and-operations-per-blob"></a>Sávszélesség és műveletek blobonként

Egyetlen blob másodpercenként legfeljebb 500 kérelmet támogat. Ha több ügyfél, amelynek kell olvasni ugyanazt a blobot, és lehet, hogy túllépi ezt a korlátot, majd fontolja meg egy blokk blob storage-fiók használata. A blokkblob-tárfiók nagyobb kérési arányt biztosít, vagy i/o-műveletek másodpercenként (IOPS).

A tartalomkézbesítési hálózat (CDN) például az Azure CDN is használhatja a blobon lévő műveletek terjesztéséhez. Az Azure CDN-ről az [Azure CDN áttekintése című témakörben olvashat bővebben.](../../cdn/cdn-overview.md)  

## <a name="partitioning"></a>Particionálás

A blobadatok azure storage-partíciók ismertetése a teljesítmény növelése érdekében. Az Azure Storage gyorsabban szolgálhatja ki az adatokat egyetlen partíción, mint a több partíciót felölelő adatok. A blobok megfelelő elnevezésével javíthatja az olvasási kérelmek hatékonyságát.

A Blob Storage tartományalapú particionálási sémát használ a méretezéshez és a terheléselosztáshoz. Minden blob rendelkezik egy partíciós kulcs áll a teljes blob neve (account+container+blob). A partíciós kulcs blobadatok tartományokba való particionálására szolgál. A tartományok ezután a blob storage között a terhelés elosztása.

A tartományalapú particionálás azt jelenti, hogy a lexikai sorrendet (például *mypayroll,* *myperformance*, *myemployees*stb.) vagy időbélyegeket *(log20160101*, *log20160102*, *log20160102*stb.) használó elnevezési konvenciók nagyobb valószínűséggel eredményeznek a partíciók ugyanazon a kiszolgálópartíción való közös elhelyezkedését. , amíg a megnövekedett terhelés megköveteli, hogy kisebb tartományokra váljanak. A blobok ugyanazon a partíciókiszolgálón való közös elhelyezése növeli a teljesítményt, ezért a teljesítmény javítása fontos része a blobok elnevezése oly módon, hogy a leghatékonyabban rendszerezze őket.

Például a tárolón belüli összes blobot egyetlen kiszolgáló is kiszolgálhatja, amíg a blobok terhelése további újraegyensúlyozást igényel a partíciótartományokban. Hasonlóképpen, egy csoport enyhén betöltött fiókok nevüket lexikai sorrendben lehet kézbesíteni egy kiszolgáló, amíg a terhelés egy vagy az összes ilyen fiókok megkövetelik, hogy fel kell osztani több partíciós kiszolgálók.

Minden terheléselosztási művelet hatással lehet a tárolási hívások késésére a művelet során. A szolgáltatás képes kezelni a partíció hirtelen tört a forgalom korlátozott a méretezhetősége egyetlen partíciókiszolgáló, amíg a terheléselosztási művelet elindul, és újraegyensúlyozza a partíciókulcs-tartományban.

Néhány ajánlott eljárás követésével csökkentheti az ilyen műveletek gyakoriságát.  

- Ha lehetséges, használjon 4 Millió nál nagyobb blob- vagy blokkméreteket a normál tárfiókokhoz, és nagyobb, mint 256 KiB a prémium szintű tárfiókokhoz. A nagyobb blob- vagy blokkméretek automatikusan aktiválják a nagy átviteli sebességű blokkblobokat. A nagy átviteli sebességű blokkblobok olyan nagy teljesítményű betöltést biztosítanak, amelyet a partíciók elnevezése nem befolyásol.
- Vizsgálja meg a fiókok, tárolók, blobok, táblák és várólisták elnevezési konvencióját. Fontolja meg a fiók-, tároló- vagy blobnevek előtolását egy háromjegyű kivonatoló val, amely az igényeinek leginkább megfelelő kivonatoló függvényt használ.
- Ha időbélyegekkel vagy numerikus azonosítókkal rendezi az adatokat, győződjön meg arról, hogy nem csak hozzáfűző (vagy csak előleg) forgalmi mintát használ. Ezek a minták nem alkalmasak tartományalapú particionálási rendszerhez. Ezek a minták vezethet az összes forgalom megy egy partícióra, és korlátozza a rendszer hatékony terheléselosztás.

    Például ha a napi műveletek, amelyek egy blob ot használ egy időbélyeg, például *yyyymmdd,* majd az adott napi művelet összes forgalmat kell irányítani egy blob, amely egyetlen partíciókiszolgáló által kiszolgált. Fontolja meg, hogy a blobonkénti korlátok és partíciónkénti korlátok megfelelnek-e az igényeinek, és fontolja meg a művelet több blobra bontását, ha szükséges. Hasonlóképpen, ha az idősorozat-adatokat a táblákban tárolja, az összes forgalom a kulcsnévtér utolsó részébe irányítható. Ha numerikus azonosítókat használ, az azonosító előtagja egy háromjegyű kivonattal. Ha időbélyegeket használ, előtagolja az időbélyeget a másodpercértékkel, például *ssyyyymmdd.* Ha az alkalmazás rendszeresen végez lista- és lekérdezési műveleteket, válasszon egy kivonatoló függvényt, amely korlátozza a lekérdezések számát. Bizonyos esetekben egy véletlenszerű előtag elegendő lehet.
  
- Az Azure Storage-ban használt particionálási sémáról az [Azure Storage: A highly available Cloud Storage Service with Strong Consistency című](https://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf)témakörben talál további információt.

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

A blobtartalom széles körű terjesztéséhez használjon tartalomkézbesítési hálózatot, például az Azure CDN-t. Az Azure CDN-ről további információt az [Azure CDN](../../cdn/cdn-overview.md)című témakörben talál.  

## <a name="sas-and-cors"></a>SAS és CORS

Tegyük fel, hogy engedélyeznie kell a kódot, például a JavaScript, amely fut a felhasználó webböngészőben vagy egy mobiltelefon-alkalmazásban az Adatok eléréséhez az Azure Storage-ban. Az egyik megközelítés az, hogy hozzon létre egy szolgáltatásalkalmazást, amely proxyként működik. A felhasználó eszköze hitelesíti magát a szolgáltatással, amely viszont engedélyezi az Azure Storage-erőforrásokhoz való hozzáférést. Ily módon elkerülheti a tárfiók kulcsainak felfedését a nem biztonságos eszközökön. Ez a megközelítés azonban jelentős terhelést ró a szolgáltatásalkalmazásra, mivel a felhasználó eszköze és az Azure Storage között átvitt összes adatnak át kell haladnia a szolgáltatásalkalmazáson.

A megosztott hozzáférésű aláírások (SAS) használatával elkerülheti, hogy egy szolgáltatásalkalmazást az Azure Storage proxyjaként használjon. A SAS használatával engedélyezheti, hogy a felhasználó eszköze kéréseket küldjön közvetlenül az Azure Storage-ba egy korlátozott hozzáférési jogkivonat használatával. Ha például egy felhasználó szeretne feltölteni egy fényképet az alkalmazásba, majd a szolgáltatásalkalmazás létrehozhat egy SAS-t, és elküldheti azt a felhasználó eszközére. A SAS-jogkivonat engedélyt adhat egy Azure Storage-erőforrásnak egy megadott ideig, amely után a SAS-jogkivonat lejár. A SAS-ről további információt az [Azure Storage-erőforrásokhoz megosztott hozzáférésű aláírások (SAS) használatával való korlátozott hozzáférés megadása](../common/storage-sas-overview.md)című témakörben talál.  

A webböngészők általában nem engedélyezik a JavaScript et az egyik tartomány egyik webhelye által üzemeltetett lapon, hogy bizonyos műveleteket, például írási műveleteket hajtson végre egy másik tartományban. Az azonos eredetű házirendként ismert házirend megakadályozza, hogy egy rosszindulatú parancsfájl az egyik oldalon hozzáférjen egy másik weblapon lévő adatokhoz. Az azonos eredetű szabályzat azonban korlátozás lehet a felhőben történő megoldás létrehozásakor. A kereszteredetű erőforrás-megosztás (CORS) egy olyan böngészőszolgáltatás, amely lehetővé teszi, hogy a céltartomány kommunikáljon a böngészővel arról, hogy megbízik a forrástartományból származó kérelmekben.

Tegyük fel például, hogy egy Azure-ban futó webalkalmazás egy azure Storage-fiókhoz kér egy erőforrást. A webalkalmazás a forrástartomány, a tárfiók pedig a céltartomány. A CORS konfigurálhatja az Azure Storage-szolgáltatások bármely kommunikálni a webböngésző, amely a kérelmeket a forrástartomány megbízható az Azure Storage. A CORS szolgáltatásról további információt az [Azure Storage több forrásból származó erőforrás-megosztási (CORS) támogatása című témakörben talál.](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services)  
  
A SAS és a CORS egyaránt segít elkerülni a webes alkalmazás szükségtelen terhelését.  

## <a name="caching"></a>Gyorsítótárazás

A gyorsítótárazás fontos szerepet játszik a teljesítményben. A következő szakaszok a gyorsítótárazás sal kapcsolatos gyakorlati tanácsait ismertetik.

### <a name="reading-data"></a>Adatok olvasása

Általánosságban elmondható, hogy az adatok egyszer olvasása előnyösebb, mint kétszer olvasni. Fontolja meg egy olyan webalkalmazás példáját, amely lekért egy 50 MiB-blobot az Azure Storage-ból, hogy tartalomként szolgáljon a felhasználó számára. Ideális esetben az alkalmazás helyileg gyorsítótárazza a blobot a lemezre, majd lekéri a gyorsítótárazott verziót a későbbi felhasználói kérelmekhez.

Az egyik módja annak, hogy elkerüljék a blob beolvasása, ha nem módosították, mivel a gyorsítótárba, hogy jogosult a GET művelet egy feltételes fejléc módosítási idő. Ha az utolsó módosított idő a blob gyorsítótárazása után van, majd a blob beolvasása és újra gyorsítótárazása lesz. Ellenkező esetben a gyorsítótárazott blob lekérésre az optimális teljesítmény érdekében.

Dönthet úgy is, hogy úgy tervezze meg az alkalmazást, hogy feltételezze, hogy a blob a beolvasásután rövid ideig változatlan marad. Ebben az esetben az alkalmazásnak nem kell ellenőriznie, hogy a blob módosult-e az adott időszakban.

A konfigurációs adatok, a keresett adatok és az alkalmazás által gyakran használt egyéb adatok jó jelöltek a gyorsítótárazásra.  

A feltételes fejlécek használatáról a [Feltételes fejlécek megadása a Blob szolgáltatásműveleteihez](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations)című témakörben talál további információt.  

### <a name="uploading-data-in-batches"></a>Adatok feltöltése kötegekben

Bizonyos esetekben az adatokat helyileg összesítheti, majd rendszeresen feltöltheti kötegben ahelyett, hogy minden egyes adatdarabot azonnal feltöltene. Tegyük fel például, hogy egy webalkalmazás naplófájlt vezet a tevékenységekről. Az alkalmazás vagy feltölti a részleteket minden tevékenység, mint ez történik, hogy egy tábla (amely számos tárolási műveleteket igényel), vagy mentheti tevékenység részleteit egy helyi naplófájlt, és majd rendszeresen feltölti az összes tevékenység részleteit, mint egy blob. Ha minden naplóbejegyzés mérete 1 KB, több ezer bejegyzést tölthet fel egyetlen tranzakcióba. Egyetlen tranzakció támogatja egy legfeljebb 64 MiB méretű blob feltöltését. Az alkalmazás fejlesztőjének meg kell terveznie az ügyféleszköz vagy a feltöltési hibák lehetőségét. Ha a tevékenységadatokat kell letölteni egy ideig, nem pedig egyetlen tevékenység, majd blob storage használata ajánlott tábla tároló n.

## <a name="net-configuration"></a>.NET-konfiguráció

A .  Ha más nyelveket használ, ellenőrizze, hogy hasonló fogalmak vonatkoznak-e a választott nyelvre.  

### <a name="use-net-core"></a>A .NET Core használata

A teljesítménybeli fejlesztések előnyeinek kihasználásához fejlesztheti Azure Storage-alkalmazásait a .NET Core 2.1-es vagy újabb verzióival. A .NET Core 3.x használata lehetőség szerint ajánlott.

A .NET Core teljesítményjavulásáról az alábbi blogbejegyzésekben talál további információt:

- [Teljesítménybeli fejlesztések a .NET Core 3.0-ban](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)
- [Teljesítménybeli fejlesztések a .NET Core 2.1-ben](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)

### <a name="increase-default-connection-limit"></a>Az alapértelmezett kapcsolatkorlát növelése

A .NET rendszerben a következő kód 100-ra növeli az alapértelmezett kapcsolatkorlátot (amely általában kettő ügyfélkörnyezetben vagy tíz kiszolgálói környezetben). Általában az értéket az alkalmazás által használt szálak számának megfelelően kell beállítani. A kapcsolatok megnyitása előtt állítsa be a kapcsolatkorlátot.

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Más programozási nyelvek esetében a csatlakozási korlát beállításának meghatározásához a dokumentációban tájékozódhat.  

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

## <a name="copying-and-moving-blobs"></a>Blobok másolása és áthelyezése

Az Azure Storage számos megoldást kínál a blobok másolásához és áthelyezéséhez egy tárfiókon belül, a tárfiókok között, valamint a helyszíni rendszerek és a felhő között. Ez a szakasz néhány ilyen lehetőséget ismertet a teljesítményre gyakorolt hatásuk szempontjából. A Blob storage-ba vagy onnan történő adatok hatékony átviteléről az [Azure-megoldás kiválasztása adatátvitelhez](../common/storage-choose-data-transfer-solution.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)című témakörben talál további információt.

### <a name="blob-copy-apis"></a>Blob másolási API-k

Blobok tárfiókok közötti másolásához használja a [Blokk url-címből eladása](/rest/api/storageservices/put-block-from-url) műveletet. Ez a művelet szinkron módon másolja az adatokat bármely URL-forrásból egy blokkblobba. A `Put Block from URL` művelet használata jelentősen csökkentheti a szükséges sávszélességet, amikor adatokat telepít át a tárfiókok között. Mivel a másolási művelet a szolgáltatási oldalon történik, nem kell letöltenie és újra feltöltenie az adatokat.

Ha ugyanabban a tárfiókban szeretné másolni az adatokat, használja a [Blob másolása](/rest/api/storageservices/Copy-Blob) műveletet. Az adatok másolása ugyanazon a tárfiókon belül általában gyorsan befejeződött.  

### <a name="use-azcopy"></a>Az AzCopy használata

Az AzCopy parancssori segédprogram egy egyszerű és hatékony lehetőség a blobok tömeges átviteléhez a tárfiókokba, a tárolófiókokba és a teljes tárfiókok között. AzCopy van optimalizálva ebben a forgatókönyvben, és magas átviteli sebességet érhet el. Az1Copy 10-es verzió a művelet segítségével másolja a `Put Block From URL` blob adatokat a tárfiókok között. További információ: [Adatok másolása vagy áthelyezése az Azure Storage-ba az AzCopy v10 használatával című témakörben.](/azure/storage/common/storage-use-azcopy-v10)  

### <a name="use-azure-data-box"></a>Az Azure Data Box használata

Nagy mennyiségű adat blob storage-ba importálásához fontolja meg az Azure Data Box család offline átviteléhez való használatát. A Microsoft által biztosított Data Box-eszközök jó választást jelentenek nagy mennyiségű adat Azure-ba való áthelyezéséhez, ha az idő, a hálózat rendelkezésre állása vagy a költségek korlátozottak. További információt az [Azure DataBox dokumentációjában](/azure/databox/)talál.

## <a name="content-distribution"></a>Tartalom terjesztése

Előfordulhat, hogy egy alkalmazásnak ugyanazt a tartalmat kell kiszolgálnia sok felhasználónak (például egy webhely kezdőlapján használt termékbemutató videónak), amely ugyanabban a vagy több régióban található. Ebben a forgatókönyvben használjon tartalomkézbesítési hálózat (CDN) például az Azure CDN blobtartalom földrajzi terjesztéséhez. Az Azure Storage-fiókkal ellentétben, amely egyetlen régióban létezik, és amely nem tud alacsony késleltetéssel más régiókba kis késleltetéssel tartalommal szolgálni, az Azure CDN a világ több adatközpontjában használ kiszolgálókat. Emellett a CDN általában sokkal magasabb kimenő forgalom korlátokat, mint egy tárfiók.  

Az Azure CDN-ről további információt az [Azure CDN](../../cdn/cdn-overview.md)című témakörben talál.

## <a name="use-metadata"></a>Metaadatok használata

A Blob szolgáltatás támogatja a HEAD-kérelmeket, amelyek blobtulajdonságokat vagy metaadatokat tartalmazhatnak. Ha például az alkalmazásnak szüksége van az Exif (exchangable image format) adatokra egy fényképről, akkor lekérheti a fényképet, és kibonthatja azt. A sávszélesség megtakarítása és a teljesítmény javítása érdekében az alkalmazás tárolhatja az Exif-adatokat a blob metaadataiban, amikor az alkalmazás feltölti a fényképet. Ezután az Exif-adatokat csak HEAD-kérelemmel kérheti be a metaadatokban. Csak metaadatok beolvasása, és nem a blob teljes tartalma jelentős sávszélességet takarít meg, és csökkenti az Exif-adatok kinyeréséhez szükséges feldolgozási időt. Ne feledje, hogy 8 KiB metaadat tárolható blobonként.  

## <a name="upload-blobs-quickly"></a>Blobok gyors feltöltése

Blobok gyors feltöltéséhez először határozza meg, hogy egy blobot vagy több blobot fog-e feltölteni. Az alábbi útmutatással határozza meg a forgatókönyvtől függően a megfelelő módszert.  

### <a name="upload-one-large-blob-quickly"></a>Töltsön fel egy nagy blobot gyorsan

Egyetlen nagy blob gyors feltöltéséhez az ügyfélalkalmazás párhuzamosan töltheti fel a blokkokat vagy az oldalakat, figyelembe véve az egyes blobok méretezhetőségi céljait és a tárfiók egészét. Az Azure Storage-ügyfélkódtárak támogatják a párhuzamos feltöltést. A következő tulajdonságok kal például megadhatja a .NET vagy a Java rendszerben engedélyezett egyidejű kérelmek számát. Más támogatott nyelvek ügyféltárai hasonló beállításokat biztosítanak.

- A .NET beállításhoz állítsa be a [BlobRequestOptions.ParallelOperationThreadCount](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.paralleloperationthreadcount) tulajdonságot.
- Java/Android esetén hívja meg a [BlobRequestOptions.setConcurrentRequestCount(végső integer concurrentRequestCount)](/java/api/com.microsoft.azure.storage.blob._blob_request_options.setconcurrentrequestcount) metódust.

### <a name="upload-many-blobs-quickly"></a>Töltsön fel sok blobot gyorsan

Sok blob gyors feltöltéséhez töltsön fel blobokat párhuzamosan. A párhuzamos feltöltés gyorsabb, mint az egyes blobok feltöltése párhuzamos blokkfeltöltésekkel, mivel a feltöltés a tárolási szolgáltatás több partíciójára terjed. AzAzCopy alapértelmezés szerint párhuzamosan hajtja végre a feltöltéseket, és ebben a forgatókönyvben ajánlott. További információ: [A Copy – első lépések.](../common/storage-use-azcopy-v10.md)  

## <a name="choose-the-correct-type-of-blob"></a>Válassza ki a blob megfelelő típusát

Az Azure Storage támogatja a blokkblobokat, a hozzáfűző blobokat és a lapblobokat. Egy adott használati forgatókönyv, a választott blob típus befolyásolja a teljesítményt és a méretezhetőségét a megoldás.

A blokkblobok akkor megfelelőek, ha nagy mennyiségű adatot szeretne hatékonyan feltölteni. Például egy ügyfélalkalmazás, amely feltölti a fényképeket vagy a videót a Blob storage-ba, blokkblobokat célozmeg.

Hozzáfűző blobok hasonlóak a blokk blobok, hogy azok blokkokból állnak. Ha módosít egy hozzáfűző blobot, a blokkok csak a blob végére kerülnek. Hozzáfűző blobok olyan esetekben, mint a naplózás, amikor egy alkalmazás nak kell adatokat hozzáadni egy meglévő blobhoz.

A lapblobok akkor megfelelőek, ha az alkalmazásnak véletlenszerű írásokat kell végrehajtania az adatokon. Például az Azure virtuális gép lemezei lapblobokként tárolódnak. További információt a [Blokkblobok, hozzáfűző kajások és lapblobok ismertetése című témakörben talál.](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)  

## <a name="next-steps"></a>További lépések

- [Méretezhetőségi és teljesítménycélok a Blob storage-hoz](scalability-targets.md)
- [Méretezhetőségi és teljesítménycélok a szabványos tárfiókokhoz](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Állapot- és hibakódok](/rest/api/storageservices/Status-and-Error-Codes2)
