---
title: Az Azure Storage teljesítmény-és méretezhetőségi ellenőrzőlistája | Microsoft Docs
description: Az Azure Storage-ban való használathoz szükséges bevált eljárások ellenőrzőlistája a teljesítménybeli alkalmazások fejlesztéséhez.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 06/07/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: e700cf04123bf02c1014aa418189221fbbb0b812
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2019
ms.locfileid: "71670930"
---
# <a name="microsoft-azure-storage-performance-and-scalability-checklist"></a>Teljesítmény-és méretezhetőségi ellenőrzőlista Microsoft Azure Storage

A Microsoft Azure Storage szolgáltatások megjelenése óta a Microsoft számos bevált gyakorlatot fejlesztett ki ezeknek a szolgáltatásoknak a végrehajtásához, és ez a cikk a legfontosabb feladatait ismerteti ellenőrzőlista-stílusú listára. Ennek a cikknek a célja, hogy segítse az alkalmazás-fejlesztőket az Azure Storage-ban bevált eljárások bevezetésében, és hogy könnyebben azonosítsa azokat az egyéb bevált eljárásokat, amelyeket érdemes figyelembe venni. Ez a cikk nem kísérli meg az összes lehetséges teljesítmény-és méretezhetőségi optimalizálást, és nem zárja ki azokat, amelyek kisebbek a hatásukban, vagy nem általánosan alkalmazhatóak. Ahhoz, hogy az alkalmazás viselkedése előre megjósolható legyen a tervezés során, hasznos lehet ezeket szem előtt tartani, hogy elkerülje a teljesítménnyel kapcsolatos problémákra épülő terveket.  

Minden, az Azure Storage-t használó alkalmazás-fejlesztőnek el kell végeznie a cikk elolvasásához szükséges időt, és ellenőriznie kell, hogy az alkalmazás az alábbiakban felsorolt bevált eljárások mindegyikét követi-e.  

## <a name="checklist"></a>Ellenőrzőlista

Ez a cikk a bevált eljárásokat a következő csoportokba rendezi. Bevált eljárások a következőre:  

* Az összes Azure Storage-szolgáltatás (Blobok, táblák, várólisták és fájlok)
* Blobok
* Táblák
* Üzenetsorok  

| Kész | Terület | Category | Kérdés |
| --- | --- | --- | --- |
| &nbsp; | Az összes szolgáltatás |Méretezhetőségi célok |[Az alkalmazás úgy lett kialakítva, hogy elkerülje a méretezhetőségi célok elérését?](#subheading1) |
| &nbsp; | Az összes szolgáltatás |Méretezhetőségi célok |[Az elnevezési konvenció úgy lett kialakítva, hogy jobb terheléselosztást lehessen kialakítani?](#subheading47) |
| &nbsp; | Az összes szolgáltatás |Hálózat |[A szükséges teljesítmény elérése érdekében az ügyféloldali eszközök megfelelően nagy sávszélességgel és kis késéssel rendelkeznek?](#subheading2) |
| &nbsp; | Az összes szolgáltatás |Hálózat |[Az ügyféloldali eszközökön elég magas a minőségi kapcsolat?](#subheading3) |
| &nbsp; | Az összes szolgáltatás |Hálózat |[Az ügyfélalkalmazás a Storage-fiók "közelében" található?](#subheading4) |
| &nbsp; | Az összes szolgáltatás |Tartalomterjesztés |[CDN-t használ a tartalom terjesztéséhez?](#subheading5) |
| &nbsp; | Az összes szolgáltatás |Közvetlen ügyfél-hozzáférés |[SAS és CORS használatával teszi lehetővé a közvetlen hozzáférést a tárhelyhez a proxy helyett?](#subheading6) |
| &nbsp; | Az összes szolgáltatás |Gyorsítótárazás |[Az alkalmazás gyorsítótárazza az ismétlődően használt és ritkán módosított adatait?](#subheading7) |
| &nbsp; | Az összes szolgáltatás |Gyorsítótárazás |[Az alkalmazás batch-frissítése (az ügyféloldali gyorsítótárazás, majd nagyobb készletek feltöltése)?](#subheading8) |
| &nbsp; | Az összes szolgáltatás |.NET-konfiguráció |[Konfigurálta az ügyfelet az egyidejű kapcsolatok megfelelő számának használatára?](#subheading9) |
| &nbsp; | Az összes szolgáltatás |.NET-konfiguráció |[Megfelelő számú szál használatára konfigurálta a .NET-et?](#subheading10) |
| &nbsp; | Az összes szolgáltatás |.NET-konfiguráció |[A .NET 4,5-es vagy újabb verzióját használja, amely továbbfejlesztette a Garbage Collectiont?](#subheading11) |
| &nbsp; | Az összes szolgáltatás |Párhuzamosság |[Gondoskodott arról, hogy a párhuzamosságok megfelelően legyenek kötve, hogy ne legyenek túlterhelve az ügyfelek képességei vagy a méretezhetőségi célok?](#subheading12) |
| &nbsp; | Az összes szolgáltatás |Eszközök |[A Microsoft által biztosított ügyféloldali kódtárak és eszközök legújabb verzióját használja?](#subheading13) |
| &nbsp; | Az összes szolgáltatás |Újrapróbálkozások |[Exponenciális leállítási újrapróbálkozási házirendet használ a hibák és időtúllépések szabályozására?](#subheading14) |
| &nbsp; | Az összes szolgáltatás |Újrapróbálkozások |[Az alkalmazás elkerüli a nem újrapróbálkozást okozó hibák újrapróbálkozását?](#subheading15) |
| &nbsp; | Blobok |Méretezhetőségi célok |[Egyszerre több ügyfél fér hozzá egyetlen objektumhoz?](#subheading46) |
| &nbsp; | Blobok |Méretezhetőségi célok |[Az alkalmazása a sávszélesség vagy az Operations skálázhatósági célpontban marad egyetlen blob esetében?](#subheading16) |
| &nbsp; | Blobok |Blobok másolása |[Hatékony módon másolja a blobokat?](#subheading17) |
| &nbsp; | Blobok |Blobok másolása |[AzCopy használ a Blobok tömeges példányaihoz?](#subheading18) |
| &nbsp; | Blobok |Blobok másolása |[Az Azure import/export szolgáltatást használja nagy mennyiségű adatok átvitelére?](#subheading19) |
| &nbsp; | Blobok |Metaadatok használata |[Tárolja a Blobok gyakran használt metaadatait a metaadatokban?](#subheading20) |
| &nbsp; | Blobok |Gyors feltöltés |[Amikor egy blob gyors feltöltését kísérli meg, párhuzamosan kell feltölteni a blokkokat?](#subheading21) |
| &nbsp; | Blobok |Gyors feltöltés |[Sok blob gyors feltöltésének megkísérlésekor a blobokat párhuzamosan kell feltölteni?](#subheading22) |
| &nbsp; | Blobok |Helyes blob-típus |[Az oldal blobokat használja, vagy ha szükséges, a Blobok blokkolása?](#subheading23) |
| &nbsp; | Táblák |Méretezhetőségi célok |[Közeledik az entitások skálázhatósági céljaihoz másodpercenként?](#subheading24) |
| &nbsp; | Táblák |Konfiguráció |[JSON-t használ a táblázatos kérelmekhez?](#subheading25) |
| &nbsp; | Táblák |Konfiguráció |[Kikapcsolta a kis-és nagyvállalati kérelmek teljesítményének növelését?](#subheading26) |
| &nbsp; | Táblák |Táblák és partíciók |[Megfelelően particionálta az adatait?](#subheading27) |
| &nbsp; | Táblák |Gyakori partíciók |[Elkerüli a csak Hozzáfűzés és a csak előtag mintázatot?](#subheading28) |
| &nbsp; | Táblák |Gyakori partíciók |[A lapkák/frissítések több partíció között oszlanak meg?](#subheading29) |
| &nbsp; | Táblák |Lekérdezés hatóköre |[Úgy tervezte a sémát, hogy lehetővé tegye a pontok lekérdezéseit a legtöbb esetben használni, és a táblázatos lekérdezéseket takarékosan kell használni?](#subheading30) |
| &nbsp; | Táblák |Lekérdezési sűrűség |[A lekérdezések általában csak az alkalmazás által használt sorok vizsgálatát és visszaküldését végzik?](#subheading31) |
| &nbsp; | Táblák |Visszaadott adatértékek korlátozása |[A szűrést használja a nem szükséges entitások visszaküldésének elkerüléséhez?](#subheading32) |
| &nbsp; | Táblák |Visszaadott adatértékek korlátozása |[Kivetítést használ a nem szükséges tulajdonságok visszaadásának elkerülésére?](#subheading33) |
| &nbsp; | Táblák |Denormalizáció |[Leválasztotta az adatait úgy, hogy elkerülje a nem hatékony lekérdezéseket vagy több olvasási kérést az adatlekérdezés során?](#subheading34) |
| &nbsp; | Táblák |Beszúrás/frissítés/törlés |[Olyan kérelmeket dolgoz fel, amelyeknek tranzakciós igényeknek kell lenniük, vagy egyidejűleg is elvégezhető a ciklikus utak csökkentése?](#subheading35) |
| &nbsp; | Táblák |Beszúrás/frissítés/törlés |[Elkerüli az entitások beolvasását, hogy megtörténjen a Beszúrás vagy a frissítés meghívása?](#subheading36) |
| &nbsp; | Táblák |Beszúrás/frissítés/törlés |[Vannak olyan adatsorozatok, amelyek gyakran egy entitásban lesznek beolvasva több entitás helyett tulajdonságokként?](#subheading37) |
| &nbsp; | Táblák |Beszúrás/frissítés/törlés |[Azokat az entitásokat, amelyek mindig együtt lesznek lekérdezve, és kötegekben (például idősorozat-adatsorokban) is írhatók, a táblázatok helyett blobokat használtak?](#subheading38) |
| &nbsp; | Üzenetsorok |Méretezhetőségi célok |[Közeledik a másodpercenkénti üzenetek skálázhatósági céljaihoz?](#subheading39) |
| &nbsp; | Üzenetsorok |Konfiguráció |[Kikapcsolta a kis-és nagyvállalati kérelmek teljesítményének növelését?](#subheading40) |
| &nbsp; | Üzenetsorok |Üzenet mérete |[Az üzenetek tömörítve vannak az üzenetsor teljesítményének növelése érdekében?](#subheading41) |
| &nbsp; | Üzenetsorok |Tömeges beolvasás |[Több üzenet beolvasása egyetlen "Get" művelettel történik?](#subheading42) |
| &nbsp; | Üzenetsorok |Lekérdezés gyakorisága |[Elég gyakran lekérdezni az alkalmazást, hogy csökkentse az alkalmazás észlelt késését?](#subheading43) |
| &nbsp; | Üzenetsorok |Üzenet frissítése |[A UpdateMessage használatával tárolja az üzenetek feldolgozási folyamatát, így nem kell újradolgoznia a teljes üzenetet, ha hiba történik?](#subheading44) |
| &nbsp; | Üzenetsorok |Architektúra |[Olyan várólistákat használ, amelyekkel a teljes alkalmazás jobban méretezhető a hosszú távú munkaterhelések kiszámításával a kritikus elérési útról és a méretezéstől függetlenül?](#subheading45) |

## <a name="allservices"></a>Minden szolgáltatás

Ez a szakasz felsorolja az Azure Storage-szolgáltatások (Blobok, táblák, várólisták vagy fájlok) használatára vonatkozó bevált eljárásokat.  

### <a name="subheading1"></a>Méretezhetőségi célok

Az Azure Storage-ban az előfizetés régiónként legfeljebb 250 Storage-fiók van. Ha eléri ezt a korlátot, nem hozhat létre további tárfiókokat ebben az előfizetésben ebben a régióban.

Az egyes Azure Storage-szolgáltatások skálázhatósági célokat biztosítanak a kapacitás (GB), a tranzakciós sebesség és a sávszélesség tekintetében. Ha az alkalmazás megközelíti vagy túllépi a méretezhetőségi célokat, akkor a tranzakciós késések vagy a szabályozás nagyobb mértékben merülhet fel. Amikor egy tárolási szolgáltatás szabályozza az alkalmazást, a szolgáltatás megkezdi a "503-kiszolgáló foglalt" vagy "500 művelet időtúllépése" hibakódok megadását néhány tárolási tranzakció esetében. Ez a szakasz a méretezhetőségi célok és a sávszélesség-méretezhetőségi célok kezelésének általános megközelítését tárgyalja. Az egyes tárolási szolgáltatásokkal foglalkozó későbbi szakaszai a méretezhetőségi célokat tárgyalják az adott szolgáltatás kontextusában:  

* [A blob sávszélessége és a kérelmek másodpercenkénti száma](#subheading16)
* [Tábla entitások másodpercenként](#subheading24)
* [Üzenetsor-üzenetek másodpercenként](#subheading39)  

#### <a name="sub1bandwidth"></a>Sávszélesség-méretezhetőségi cél az összes szolgáltatáshoz

A megírásakor az USA-beli GRS-fiókhoz tartozó sávszélesség-célkitűzések 10 Gigabit másodpercenként (GB/s) (a Storage-fiókba elküldett adatok) és 20 GB/s (a Storage-fiókból elküldett adatok) esetében a bejövő forgalomhoz szükségesek. Helyileg redundáns tárolási (LRS-) fiók esetén a határértékek magasabbak – 20 GB/s a bejövő forgalomhoz és 30 GB/s a kimenő forgalomhoz.  A nemzetközi sávszélesség korlátozásai alacsonyabbak lehetnek, és a [skálázhatósági célok oldalon](https://msdn.microsoft.com/library/azure/dn249410.aspx)találhatók.  A tárolási redundancia lehetőségeivel kapcsolatos további információkért tekintse meg az alábbi, hasznos erőforrásokra mutató hivatkozásokat.  

#### <a name="what-to-do-when-approaching-a-scalability-target"></a>Mi a teendő, ha közeledik a skálázhatósági célponthoz

Ha közeledik az adott előfizetéshez/régióhoz tartozó Storage-fiókok száma, értékelje ki az alkalmazását és a Storage-fiókok használatát, és állapítsa meg, hogy a feltételek bármelyike érvényes-e.

* A Storage-fiókok nem felügyelt lemezként való használata, és a lemezek hozzáadása a virtuális gépekhez. Ebben a forgatókönyvben javasoljuk, hogy [felügyelt lemezeket](../../virtual-machines/windows/managed-disks-overview.md)használjon, mivel a tárterület méretezhetőségét az egyéni Storage-fiókok létrehozása és kezelése nélkül kezeli.
* Egy Storage-fiók használata felhasználónkénti alapon, adatelkülönítés céljából. Ebben a forgatókönyvben azt javasoljuk, hogy a tárolókat a teljes Storage-fiók helyett az egyes ügyfelek számára is használja. Az Azure Storage mostantól lehetővé teszi a szerepköralapú hozzáférés-vezérlés megadását a [tárolók alapján](storage-auth-aad-rbac-portal.md).
* Több Storage-fiók használata a szilánkok számára a bejövő/kimenő/IOPS/kapacitás nagyobb méretezhetősége érdekében. Ebben a forgatókönyvben, ha lehetséges, javasoljuk, hogy használja ki a standard szintű Storage-fiókok [megnövekedett korlátait](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/) , hogy csökkentse a munkaterhelés számára szükséges tárolási fiókok számát.

Ha az alkalmazása közeledik egyetlen Storage-fiók skálázhatósági céljaihoz, vegye figyelembe az alábbi módszerek egyikét:  

* Gondolja át a számítási feladatokat, amelyek hatására az alkalmazás megközelíti vagy túllépi a méretezhetőségi célt. Másképpen is megtervezhető, hogy kevesebb sávszélességet vagy kapacitást vagy kevesebb tranzakciót használjon?
* Ha egy alkalmazásnak meg kell haladnia a méretezhetőségi célok valamelyikét, hozzon létre több Storage-fiókot, és particionálja az alkalmazásadatok körét a több Storage-fiók között. Ha ezt a mintát használja, ügyeljen arra, hogy tervezze meg az alkalmazását, hogy a jövőben további Storage-fiókokat lehessen hozzáadni a terheléselosztáshoz. Az egyes Azure-előfizetések az írás időpontjában régiónként akár 250 Storage-fiókkal is rendelkezhetnek (ha a Azure Resource Manager modellel telepítik).  A Storage-fiókok a tárolt adatok, a lebonyolított tranzakciók vagy az átvitt adatok alapján nem számítanak bele a használati díjakba.
* Ha az alkalmazás eléri a sávszélesség-célokat, érdemes lehet az ügyfélen lévő adatok tömörítését csökkenteni az adatok tárolási szolgáltatásba való küldéséhez szükséges sávszélesség csökkentése érdekében.  Habár ez csökkentheti a sávszélességet, és javíthatja a hálózati teljesítményt, de negatív hatással is lehet rá.  Ennek hatására ki kell értékelnie ennek a teljesítményre gyakorolt hatását az ügyfélen lévő adatok tömörítéséhez és kibontásához szükséges további feldolgozási követelmények miatt. A tömörített adatok tárolása emellett nehezebbé teheti a problémák megoldását, mivel ez megnehezítheti a tárolt adatok megtekintését a szabványos eszközök használatával.
* Ha az alkalmazás eléri a méretezhetőségi célokat, akkor győződjön meg arról, hogy exponenciális leállítási használ az újrapróbálkozásokhoz ( [](#subheading14)lásd: újrapróbálkozások).  Érdemes meggyőződni arról, hogy soha nem közelíti meg a skálázhatósági célokat (a fenti módszerek egyikének használatával), de ez biztosítja, hogy az alkalmazás ne csak a gyors próbálkozást folytassa, így a szabályozás még rosszabb lesz.  

#### <a name="useful-resources"></a>Hasznos források

Az alábbi hivatkozások további részleteket biztosítanak a méretezhetőségi célokról:

* A méretezhetőségi célokkal kapcsolatos információkért tekintse meg az [Azure Storage skálázhatósági és teljesítményi célkitűzéseit](storage-scalability-targets.md) ismertető témakört.
* Lásd: az [Azure Storage replikációja](storage-redundancy.md) és az [Azure Storage redundancia lehetőségeinek beolvasása, valamint](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx) a tárterület-redundancia beállításával kapcsolatos információk
* Az Azure-szolgáltatások díjszabásával kapcsolatos aktuális információkért lásd: az [Azure díjszabása](https://azure.microsoft.com/pricing/overview/).  

### <a name="subheading47"></a>Partíció elnevezési konvenciója

Az Azure Storage egy tartományon alapuló particionálási sémát használ a rendszer skálázásához és terheléselosztásához. A partíciós kulcs (fiók + tároló + blob) az adattartományba való particionálásra szolgál, és ezek a tartományok terheléselosztásra kerülnek a rendszeren belül. Ez azt jelenti, hogy elnevezési konvenciók, például lexikális sorrend (például *mypayroll*, *myperformance*, *myemployees*stb.) vagy időbélyegek használata (*log20160101*, *log20160102*, *log20160102*stb.) magát a partíciókat, amelyek potenciálisan ugyanazon a partíción helyezkednek el, míg a terheléselosztási művelet kisebb tartományokra bontja őket. Egy tárolóban lévő összes blobot például egyetlen kiszolgáló is kiszolgálhat, amíg a Blobok terhelése a partíció-tartományok további kiegyensúlyozását igényli. Hasonlóképpen, a lexikális sorrendben elrendezett fiókokat tartalmazó csoportokat egyetlen kiszolgáló is kiszolgálhatja, amíg az egyik vagy az összes fiók terhelését meg kell osztani több partíciós kiszolgáló között. Minden terheléselosztási művelet hatással lehet a tárolási hívások késésére a művelet során. A rendszernek képesnek kell lennie arra, hogy a partíciók hirtelen feltört forgalmát egy partíciós kiszolgáló skálázhatósága korlátozza, amíg a terheléselosztási művelet befejeződik, és a partíciós kulcs tartományát újra kiegyenlíti.

Az ilyen műveletek gyakoriságának csökkentéséhez kövesse az ajánlott eljárásokat.  

* Ha lehetséges, használjon nagyobb mennyiségű Put blobot, vagy helyezzen el blokk méretet (4 MiB-nél nagyobb standard szintű fiókok esetében, és a prémium fiókoknál nagyobb, mint 256 KiB) a nagy átviteli sebességű blokk blob (HTBB) aktiválásához. A HTBB nagy teljesítményű betöltést biztosít, amelyet a partíciók elnevezése nem érint.
* Vizsgálja meg a fiókok, tárolók, blobok, táblák és várólisták esetében használt elnevezési konvenciót. Vegye fontolóra a fiók, a tároló vagy a blob nevének egy 3 számjegyű kivonattal történő előjavítását, amely az igényeinek legmegfelelőbb kivonatoló függvényt használja.  
* Ha az adatokat timestamp vagy numerikus azonosítók használatával rendezi, gondoskodnia kell arról, hogy ne használjon csak hozzáfűzési (vagy csak a csak a) adatforgalmi mintákat. Ezek a minták nem alkalmasak egy tartományon alapuló particionálási rendszerre, és az egyetlen partícióra irányuló összes forgalomhoz vezethetnek, és a rendszernek a hatékony terheléselosztással való korlátozása. Ha például olyan napi művelettel rendelkezik, amely egy olyan blob objektumot használ, amely egy időbélyeggel (például *ééééhhnn*) rendelkezik, akkor a napi művelet összes forgalma egyetlen, egyetlen partíciós kiszolgáló által kiszolgált objektumra lesz irányítva. Ellenőrizze, hogy a blob-korlátok és a partíciók korlátai megfelelnek-e az igényeinek, és szükség esetén vegye figyelembe a művelet több blobba való megtörését. Hasonlóképpen, ha a táblázatokban tárolja az idősoros adatokat, a rendszer az összes forgalmat átirányíthatja a kulcs névterének utolsó részére. Ha időbélyegeket vagy numerikus azonosítókat kell használnia, akkor az azonosítót 3 számjegyű kivonattal kell előadnia, vagy az időbélyegek esetében az idő másodperc részét (például *ssyyyymmdd*). Ha a lista és a lekérdezési műveletek végrehajtása rendszeresen történik, válasszon egy kivonatoló függvényt, amely korlátozza a lekérdezések számát. Más esetekben elegendő lehet egy véletlenszerű előtag.  
* Az Azure Storage-ban használt particionálási sémával kapcsolatos további információkért lásd [: Azure Storage: Magas rendelkezésre állású felhőalapú tárolási szolgáltatás erős konzisztencia](https://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf)-ellenőrzéssel.

### <a name="networking"></a>Hálózat

Az API meghívása során a rendszer gyakran az alkalmazás fizikai hálózati korlátozásai jelentős hatással vannak a teljesítményre. A következőkben néhány korlátozást láthat a felhasználók.  

#### <a name="client-network-capability"></a>Ügyfél hálózati funkciója

##### <a name="subheading2"></a>Átviteli sebesség

A sávszélesség miatt a probléma gyakran az ügyfél képességei. Ha például egy Storage-fiók 10 GB/s vagy több bejövő forgalmat tud kezelni (lásd a [sávszélesség-méretezhetőségi célokat](#sub1bandwidth)), a "kis" Azure feldolgozói szerepkörű példány hálózati sebessége csak körülbelül 100 Mbps-ra képes. A nagyobb méretű Azure-példányok nagyobb kapacitású hálózati adapterekkel rendelkeznek, ezért érdemes nagyobb méretű virtuális gépeket használni, ha egy gépről nagyobb hálózati korlátokra van szüksége. Ha egy helyszíni alkalmazásból fér hozzá a Storage szolgáltatáshoz, akkor ugyanaz a szabály érvényes: Ismerje meg az ügyféleszközök hálózati képességeit és a hálózati kapcsolatot az Azure Storage-beli helyével, vagy javítsa a szükséges vagy megtervezheti a az alkalmazás a képességein belül dolgozhat.  

##### <a name="subheading3"></a>Kapcsolat minősége

A hálózati használathoz hasonlóan vegye figyelembe, hogy a hibákkal és a csomagok elvesztésével kapcsolatos hálózati feltételek lassú működést eredményeznek.  A WireShark vagy a NetMon használata segíthet a probléma diagnosztizálásában.  

##### <a name="useful-resources"></a>Hasznos források

A virtuálisgép-méretekkel és a lefoglalt sávszélességgel kapcsolatos további információkért lásd: [Windows rendszerű](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) virtuálisgép-méretek vagy [linuxos virtuális gépek mérete](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

#### <a name="subheading4"></a>Helyen

Minden elosztott környezetben, a-ügyfél közel a kiszolgálóhoz, a legjobb teljesítményt nyújtja. Ha az Azure Storage-t a legalacsonyabb késéssel szeretné elérni, az ügyfél számára a legjobb hely ugyanazon az Azure-régión belül van. Ha például az Azure Storage szolgáltatást használó Azure-webhellyel rendelkezik, akkor mindkettőt egyetlen régión belül kell megkeresnie (például az USA nyugati régiója vagy Délkelet-Ázsia). Ez csökkenti a késést és a költségeket – az írás időpontjában az egyetlen régión belüli sávszélesség-használat ingyenes.  

Ha az ügyfélalkalmazások nem az Azure-ban (például a mobileszköz-alkalmazásokban vagy a helyszíni nagyvállalati szolgáltatásokban) vannak tárolva, akkor ismét a Storage-fiókot egy olyan régióban helyezi el, amely az ahhoz hozzáférő eszközök közelében van, általában csökkenti a késést. Ha az ügyfeleket széles körben terjesztik (például néhány Észak-Amerika és néhány Európában), akkor érdemes több Storage-fiókot használnia: az egyik az észak-amerikai régióban, egy pedig egy európai régióban található. Ez segít csökkenteni a mindkét régióban lévő felhasználók késését. Ez a megközelítés könnyebben megvalósítható, ha az alkalmazás által tárolt adattárolók egyediek az egyes felhasználók számára, és nem igénylik az adatreplikálást a Storage-fiókok között.  A széles körű tartalom terjesztéséhez a CDN használata javasolt – további részletekért lásd a következő szakaszt.  

### <a name="subheading5"></a>Tartalom terjesztése

Előfordulhat, hogy egy alkalmazásnak ugyanazokat a tartalmakat kell kiszolgálnia sok felhasználó számára (például egy, a webhely kezdőlapján használt termék-bemutató videó), amely a vagy a több régióban található. Ebben a forgatókönyvben olyan Content Delivery Network (CDN) használatát kell használni, mint például a Azure CDN, a CDN pedig az Azure Storage-t használja az adatforrásként. Az egyetlen régióban található Azure Storage-fiókkal ellentétben, amely nem tud a tartalmat alacsony késéssel kézbesíteni más régiók számára, Azure CDN több adatközpontban lévő kiszolgálókat használ a világ különböző pontjain. Emellett a CDN általában sokkal magasabb kimenő forgalomra vonatkozó korlátozásokat is támogat, mint egyetlen Storage-fiók.  

További információ a Azure CDNről: [Azure CDN](https://azure.microsoft.com/services/cdn/).  

### <a name="subheading6"></a>SAS és CORS használata

Ha olyan kódot kell engedélyeznie, mint például a JavaScript egy felhasználó webböngészőjében vagy egy mobiltelefon-alkalmazásban az Azure Storage-ban tárolt adat eléréséhez, az egyik módszer a webes szerepkörben lévő alkalmazás használata proxyként: a felhasználó eszköze hitelesíti magát a webes szerepkörrel. , amely viszont engedélyezi a hozzáférést a tárolási erőforrásokhoz. Így elkerülhető, hogy a Storage-fiók kulcsa nem biztonságos eszközökön legyen kitéve. Ez azonban jelentős terhelést ró a webes szerepkörre, mert a felhasználó eszköze és a tárolási szolgáltatás között továbbított összes adatnak át kell haladnia a webes szerepkörön. A tárolási szolgáltatáshoz a megosztott hozzáférési aláírások (SAS) használatával elkerülhetők a webes szerepkörök proxyként való használata, esetenként az több eredetű erőforrás-megosztási fejlécekkel (CORS) együtt. A SAS használatával lehetővé teheti, hogy a felhasználó eszközére a kérések közvetlenül a Storage szolgáltatáshoz legyenek biztosítva korlátozott hozzáférési jogkivonat segítségével. Ha például egy felhasználó egy fényképet szeretne feltölteni az alkalmazásba, a webes szerepkör egy SAS-tokent tud létrehozni és elküldeni a felhasználó eszközének, amely engedélyt ad egy adott blobba vagy tárolóba írni a következő 30 percre (amely után az SAS-token lejár).

Általában egy böngésző nem engedélyezi a JavaScript használatát egy olyan oldalon, amelyet egy adott tartomány webhelye futtat egy adott művelet, például egy másik tartományhoz való "PUT" esetén. Ha például egy webes szerepkört üzemeltet a "contosomarketing.cloudapp.net" címen, és az ügyféloldali JavaScript használatával szeretné feltölteni a blobot a Storage-fiókjába a következő helyen: "contosoproducts.blob.core.windows.net", a böngészők "azonos eredetű szabályzata" is tiltja ezt a műveletet. A CORS olyan böngésző-szolgáltatás, amely lehetővé teszi a célként megadott tartomány (ebben az esetben a Storage-fiók) számára, hogy kommunikáljon a böngészővel, amely megbízhatónak tekinti a forrástartományban (ebben az esetben a webes szerepkör) származó kérelmeket.  

Mindkét technológia segíthet elkerülni a webalkalmazás szükségtelen terhelését (és szűk keresztmetszeteit).  

#### <a name="useful-resources"></a>Hasznos források

Az SAS-vel kapcsolatos további információkért lásd: [korlátozott hozzáférés engedélyezése az Azure Storage-erőforrásokhoz közös hozzáférési aláírások (SAS) használatával](storage-sas-overview.md).  

A CORS kapcsolatos további információkért lásd: [Az Azure Storage szolgáltatásainak az adatforrások közötti megosztása (CORS) támogatása](https://msdn.microsoft.com/library/azure/dn535601.aspx).  

### <a name="caching"></a>Gyorsítótárazás

#### <a name="subheading7"></a>Adatgyűjtés

Általánosságban elmondható, hogy az adatok egy szolgáltatásból való beszerzése jobb, mint kétszer. Vegyünk egy olyan MVC-webalkalmazás példáját, amely egy olyan webes szerepkörben fut, amely már beolvasott egy 50 MB-os blobot a Storage szolgáltatásból, hogy tartalmat szolgáltasson a felhasználónak. Az alkalmazás lekérheti ugyanezt a blobot minden alkalommal, amikor egy felhasználó megkéri, vagy helyileg gyorsítótárazza a lemezre, és újból felhasználja a gyorsítótárazott verziót a későbbi felhasználói kérésekhez. Továbbá, ha egy felhasználó az adatkérést kéri, az alkalmazás a módosítási idő feltételes fejlécét is kiállíthatja, így elkerülhető a teljes blob beolvasása, ha még nem módosították. Ugyanezt a mintát is alkalmazhatja a Table entitásokkal való munkavégzéshez.  

Bizonyos esetekben dönthet úgy, hogy az alkalmazás feltételezi, hogy a blob a beolvasás után rövid ideig érvényes marad, és az adott időszakban az alkalmazásnak nem kell ellenőriznie, hogy a blob módosult-e.

A konfiguráció, a keresés és az alkalmazás által mindig használt egyéb adatértékek a gyorsítótárazásra kiválóan alkalmasak.  

További információ a feltételes letöltésekről: [feltételes fejlécek megadása blob Service műveletekhez](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations).  

#### <a name="subheading8"></a>Adatfeltöltés kötegekben

Egyes alkalmazási forgatókönyvekben helyileg összesítheti az adatokat, majd rendszeresen feltöltheti őket egy kötegbe ahelyett, hogy az egyes adatokat azonnal fel kellene töltenie. Egy webalkalmazás például megtarthatja a tevékenységek naplófájlját: az alkalmazás feltöltheti az összes tevékenység részleteit úgy, ahogy egy tábla entitásként (amely sok tárolási műveletet igényel), vagy mentheti a tevékenység részleteit egy helyi naplófájlba, majd rendszeresen töltse fel a tevékenység részleteit tagolt fájlként egy blobba. Ha minden naplóbejegyzés 1 KB méretű, akkor akár több ezret is feltölthet egyetlen "Put blob" tranzakcióba (akár 64 MB méretű blobot is feltölthet egyetlen tranzakcióban). Ha a helyi gép összeomlik a feltöltés előtt, akkor előfordulhat, hogy elvesznek néhány naplózási adat: az alkalmazás fejlesztőinek meg kell tervezniük az ügyféleszközök vagy a feltöltési hibák lehetőségét.  Ha a időtávok (nem csak egyetlen tevékenység) szeretné letölteni a tevékenységeket, a Blobok használata ajánlott a táblák között.

### <a name="net-configuration"></a>.NET-konfiguráció

A .NET-keretrendszer használata esetén ez a szakasz több gyors konfigurációs beállítást tartalmaz, amelyek segítségével jelentős teljesítmény-növelést végezhet.  Ha más nyelveket használ, ellenőrizze, hogy a választott nyelven a hasonló fogalmak érvényesek-e.  

#### <a name="subheading9"></a>Az alapértelmezett kapcsolatok korlátjának megemelése

A .NET-ben a következő kód növeli az alapértelmezett kapcsolódási korlátot (amely általában 2 egy ügyfél-környezetben, vagy 10 a kiszolgálói környezetben) 100-re. Az értéket általában az alkalmazás által használt szálak számának körülbelül értékre kell állítani.  

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

A kapcsolatok megnyitása előtt be kell állítania a kapcsolati korlátot.  

Más programozási nyelvek esetében az adott nyelv dokumentációjában megtudhatja, hogyan állíthatja be a kapcsolódási korlátot.  

További információkért lásd: webszolgáltatások blogbejegyzése [: Egyidejű kapcsolatok](https://blogs.msdn.com/b/darrenj/archive/2005/03/07/386655.aspx).  

#### <a name="subheading10"></a>A szálak minimális számának megemelése, ha szinkron kódot használ aszinkron feladatokkal

Ez a kód növeli a szálak minimális számát a szál készletében:  

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

További információ: [szálkészlet munkaszála belépett. SetMinThreads metódus](https://msdn.microsoft.com/library/system.threading.threadpool.setminthreads%28v=vs.110%29.aspx).  

#### <a name="subheading11"></a>Használja ki a .NET 4,5-es és magasabb szintű Garbage-gyűjtemény előnyeit

Használja a .NET 4,5-es vagy újabb verzióját az ügyfélalkalmazás számára, hogy kihasználja a kiszolgálói Garbage-gyűjtemények teljesítményének növelését.

További információkért tekintse meg a [.net 4,5 teljesítménybeli fejlesztéseinek áttekintését](https://msdn.microsoft.com/magazine/hh882452.aspx)ismertető cikket.  

### <a name="subheading12"></a>Nem kötött párhuzamosság

Habár a párhuzamosság kiválóan alkalmas a teljesítményre, ügyeljen arra, hogy az adatok feltöltéséhez és letöltéséhez ne korlátozza a nem korlátozott párhuzamosságot (a szálak és/vagy a párhuzamos kérelmek számának korlátozása nélkül), több feldolgozót használva több partíció eléréséhez (tárolók, várólisták vagy a tábla partíciói) ugyanabban a Storage-fiókban, vagy több elemhez is hozzáférnek ugyanabban a partícióban. Ha a párhuzamosság fel van oldva, az alkalmazás túllépheti az ügyféleszközök képességeit vagy a Storage-fiók skálázhatósági céljait, ami hosszabb késést és szabályozást eredményez.  

### <a name="subheading13"></a>Storage ügyféloldali kódtárak és eszközök

Mindig a Microsoft által biztosított legújabb ügyféloldali kódtárak és eszközök használata. Az írás időpontjában a .NET, a Windows Phone-telefon, a Windows-futtatókörnyezet, a Java és C++a más nyelvekhez elérhető ügyféloldali kódtárak érhetők el. A Microsoft emellett a PowerShell-parancsmagokat és az Azure CLI-parancsokat is kiadta az Azure Storage használatához. A Microsoft aktívan fejleszti ezeket az eszközöket a teljesítmény szem előtt tartásával, naprakészen tartja a legújabb szolgáltatási verziókat, és gondoskodik arról, hogy a bevált teljesítményekkel kapcsolatos gyakorlatokat belsőleg kezeljék.  

### <a name="retries"></a>Újrapróbálkozások

#### <a name="subheading14"></a>Sávszélesség-szabályozás és a kiszolgáló foglalt hibái

Bizonyos esetekben előfordulhat, hogy a tárolási szolgáltatás szabályozhatja az alkalmazást, vagy egyszerűen nem tudja kiszolgálni a kérést valamilyen átmeneti feltétel miatt, és egy "503 kiszolgáló által foglalt" üzenetet vagy "500 timeout" értéket ad vissza.  Ez akkor fordulhat elő, ha az alkalmazás a méretezhetőségi célok bármelyikére közeledik, vagy ha a rendszer a particionált adatok kiegyensúlyozását is lehetővé teszi a nagyobb átviteli sebesség érdekében.  Az ügyfélalkalmazás általában újra kell próbálkoznia a művelettel, amely egy ilyen hibát okoz: Ha később próbálkozik ezzel a kéréssel, a későbbiekben is sikeres lehet. Ha azonban a tárolási szolgáltatás szabályozza az alkalmazást, mert az meghaladja a méretezhetőségi célokat, vagy akkor is, ha a szolgáltatás nem tudta kiszolgálni a kérelmet valamilyen más okból, az agresszív újrapróbálkozások általában rosszabbul teszik a problémát. Emiatt az exponenciális visszalépést kell használnia (az ügyféloldali kódtárak ezt a viselkedést használják). Előfordulhat például, hogy az alkalmazás 2 másodperc, 4 másodperc, 10 másodperc, majd 30 másodperc múlva újra próbálkozik. Ez a viselkedés azt eredményezi, hogy az alkalmazás jelentősen csökkenti a szolgáltatás terhelését, és nem súlyosbítja a problémákat.  

A kapcsolódási hibákat azonnal újra lehet próbálni, mert nem a szabályozás eredménye, és a várt átmeneti állapotú.  

#### <a name="subheading15"></a>Nem újrapróbálkozást lehetővé tevő hibák

Az ügyféloldali kódtárak tisztában vannak azzal, hogy mely hibák állnak újra, és melyek nem. Ha azonban saját kódot ír a tároló REST API, ne feledje, hogy vannak olyan hibák, amelyeket nem kell újrapróbálkoznia: például egy 400 (hibás kérés) válasz azt jelzi, hogy az ügyfélalkalmazás olyan kérést küldött, amely nem dolgozható fel, mert nem volt egy várt formában. A kérelem újraküldése minden alkalommal ugyanazt a választ fogja eredményezni, így a rendszer nem próbálkozik újra. Ha saját kódot ír a tároló REST API, vegye figyelembe, hogy a hibakódok mit jelentenek, és hogy milyen módon próbálkozzon újra (vagy sem).  

#### <a name="useful-resources"></a>Hasznos források

A tárolási hibakódokról a Microsoft Azure webhelyén található [állapot-és hibakódok](https://msdn.microsoft.com/library/azure/dd179382.aspx) című témakörben olvashat bővebben.  

## <a name="blobs"></a>Blobok

Az összes korábban ismertetett [szolgáltatás](#allservices) bevált eljárásai mellett a következő bevált eljárások kifejezetten a blob servicera vonatkoznak.  

### <a name="blob-specific-scalability-targets"></a>BLOB-specifikus skálázhatósági célok

#### <a name="subheading46"></a>Egyszerre több ügyfél fér hozzá egyetlen objektumhoz

Ha nagy számú ügyfél fér hozzá egyszerre egyetlen objektumhoz, meg kell fontolnia egy objektum-és Storage-fiók skálázhatósági célkitűzéseit. Az egyetlen objektumhoz hozzáférő ügyfelek pontos száma attól függően változhat, hogy milyen tényezőktől függ az objektum, az objektum mérete, a hálózati feltételek stb.

Ha az objektum olyan CDN-en keresztül terjeszthető, mint például a webhelyek által szolgáltatott képek vagy videók, akkor a CDN-t kell használnia. Lásd [itt](#subheading5).

Más forgatókönyvekben, például olyan tudományos szimulációkban, ahol az adatok bizalmasak, két lehetőség közül választhat. Az első a munkaterhelések hozzáférésének felosztása úgy, hogy az objektum egy adott időtartamon keresztül legyen elérhető, és egyidejűleg legyen elérhető. Azt is megteheti, hogy ideiglenesen átmásolja az objektumot több Storage-fiókba, így növeli a teljes IOPS és a különböző tárolási fiókok számát. A korlátozott tesztelés során azt találtuk, hogy körülbelül 25 virtuális gép egyidejűleg letöltheti a 100 GB-os blobot párhuzamosan (az egyes virtuális gépek 32 szál használatával tetszés a letöltést). Ha 100 ügyfélnek kell hozzáférnie az objektumhoz, először másolja át egy második Storage-fiókba, majd az első 50 virtuális gép hozzáférhessen az első blobhoz, a második 50-as virtuális gép pedig a második blobhoz fér hozzá. Az eredmények attól függően változnak, hogy milyen viselkedést tapasztalnak, ezért a tervezés során érdemes ezt tesztelni. 

#### <a name="subheading16"></a>Sávszélesség és műveletek blobon

Egyetlen blobot legfeljebb 60 MB/másodperc (körülbelül 480 Mbps) lehet olvasni vagy írni, ami meghaladja a számos ügyféloldali hálózat képességeit (beleértve az ügyféleszközök fizikai hálózati adapterét is). Emellett egy blob másodpercenként legfeljebb 500 kérelmet támogat. Ha több ügyféllel is el kell olvasnia ugyanazt a blobot, és előfordulhat, hogy túllépi ezeket a korlátokat, érdemes lehet egy CDN-t használni a blob terjesztéséhez.  

A Blobok célzott átviteli sebességével kapcsolatos további információkért lásd: az [Azure Storage skálázhatósági és teljesítménybeli céljai](storage-scalability-targets.md).  

### <a name="copying-and-moving-blobs"></a>Blobok másolása és áthelyezése

#### <a name="subheading17"></a>BLOB másolása

A Storage REST API 2012-02-12-es verziója bevezette a Blobok különböző fiókokba való másolásának hasznos lehetőségeit: egy ügyfélalkalmazás utasíthatja a Storage szolgáltatást, hogy a blobot egy másik forrásból másolja (esetleg egy másik Storage-fiókban), majd hagyja a szolgáltatást a másolás aszinkron módon történik. Ez jelentősen csökkentheti az alkalmazáshoz szükséges sávszélességet, amikor más Storage-fiókokból végez áttelepítést, mert nem kell letöltenie és feltöltenie az adatait.  

Az egyik szempont azonban az, hogy a Storage-fiókok közötti másoláskor a másolás befejezése után nincs időkorlát. Ha az alkalmazásnak gyorsan kell végrehajtania egy blob-példányt a vezérlő alatt, akkor érdemes lehet a blobot egy virtuális gépre letölteni, majd feltölteni a célhelyre.  Ebben az esetben a teljes kiszámíthatóság érdekében győződjön meg arról, hogy a másolást egy ugyanazon az Azure-régióban futó virtuális gép végzi, vagy más hálózati feltételek (és valószínűleg a) hatással vannak a másolási teljesítményre.  Emellett az aszinkron másolások állapotát programozott módon is figyelheti.  

Az azonos Storage-fiókon belüli másolatok általában gyorsan befejeződtek.  

További információ: [blob másolása](https://msdn.microsoft.com/library/azure/dd894037.aspx).  

#### <a name="subheading18"></a>AzCopy használata

Az Azure Storage csapata kiadta a "AzCopy" nevű parancssori eszközt, amelynek célja, hogy segítsen a nagy méretű Blobok tömeges átvitelében a-ból, a-ból és a különböző tárolási fiókokba.  Ez az eszköz erre a forgatókönyvre van optimalizálva, és magas adatátviteli sebességet érhet el.  A használata a tömeges feltöltés, a letöltés és a másolási forgatókönyvek esetében ajánlott. Ha többet szeretne megtudni róla, és le szeretné tölteni, tekintse meg [az adatok átvitele a AzCopy parancssori segédprogrammal](storage-use-azcopy.md)című témakört.  

#### <a name="subheading19"></a>Azure import/export szolgáltatás

Nagy mennyiségű adat (több mint 1 TB) esetén az Azure Storage az importálási/exportálási szolgáltatást kínálja, amely lehetővé teszi a blob Storage-ból történő feltöltését és letöltését a merevlemezek szállításával.  Elhelyezheti az adatait egy merevlemezen, és elküldheti a Microsoftnak a feltöltéshez, vagy üres merevlemezt küldhet a Microsoftnak az adatok letöltéséhez.  További információk a [Microsoft Azure Import/Export szolgáltatás adatok a Blob Storage-ba való átviteléhez történő használatát](../storage-import-export-service.md) ismertető cikkben olvashatóak.  Ez sokkal hatékonyabb lehet, mint a hálózaton keresztüli adatmennyiség feltöltése/letöltése.  

### <a name="subheading20"></a>Metaadatok használata

A Blob service támogatja a fő kérelmeket, amelyek tartalmazhatják a Blobokkal kapcsolatos metaadatokat is. Ha például az alkalmazásnak egy fényképből származó EXIF-adatokra van szüksége, akkor lekérheti a fényképet, és kinyerheti azt. A sávszélesség megtakarítása és a teljesítmény javítása érdekében az alkalmazás a blob metaadataiban tárolhatja az EXIF-adatokat, amikor az alkalmazás feltöltötte a fényképet: az EXIF-adatokat a metaadatokban csak egy HEAD kérelem, a jelentős sávszélesség mentése és a feldolgozási idő szükséges az EXIF-adatok kinyeréséhez, amikor a blob beolvasása megtörténik. Ez olyan esetekben hasznos, amikor csak a metaadatokra van szükség, nem a blob teljes tartalmára.  Blobban csak 8 KB-os metaadat tárolható (a szolgáltatás nem fogad el több, mint annál többet), így ha az adatok nem férnek hozzá a mérethez, előfordulhat, hogy ezt a módszert nem fogja tudni használni.  

### <a name="rapid-uploading"></a>Gyors feltöltés

A Blobok gyors feltöltéséhez az első kérdésre adandó válasz a következő: egy blob feltöltése vagy sok más?  Az alábbi útmutatás segítségével határozza meg a forgatókönyvtől függően a helyes módszert.  

#### <a name="subheading21"></a>Egy nagyméretű blob gyors feltöltése

Ha gyorsan fel szeretne tölteni egy nagyméretű blobot, az ügyfélalkalmazás párhuzamosan fel kell töltenie a blokkokat vagy lapokat (szem előtt tartva az egyes Blobok méretezhetőségi célkitűzéseit és a Storage-fiók egészét).  A Microsoft által biztosított RTM Storage ügyféloldali kódtárak (.NET, Java) lehetővé teszi ezt.  Az egyes könyvtárak esetében a következő megadott objektum/tulajdonság használatával állítsa be a párhuzamosság szintjét:  

* .NET: ParallelOperationThreadCount beállítása a használni kívánt BlobRequestOptions objektumhoz.
* Java/Android: Use BlobRequestOptions.setConcurrentRequestCount()
* Node.js: Használja a parallelOperationThreadCount a kérelmek vagy a Blob service.
* C++: Használja a blob_request_options:: set_parallelism_factor metódust.

#### <a name="subheading22"></a>Sok blob feltöltése gyorsan

Sok blob gyors feltöltéséhez töltse fel a blobokat párhuzamosan. Ez gyorsabb, mint az egyszeres Blobok feltöltésének egyszerre történő feltöltése, mert a a Storage szolgáltatás több partícióján keresztül terjeszti a feltöltést. Egyetlen blob csak 60 MB/másodperc (körülbelül 480 Mbps) átviteli sebességet támogat. Az írás időpontjában az USA-alapú LRS-fiók akár 20 GB/s-os bejövő forgalmat is támogat, ami sokkal több, mint az egyes Blobok által támogatott átviteli sebesség.  A [AzCopy](#subheading18) alapértelmezés szerint párhuzamosan hajtja végre a feltöltéseket, és ezt a forgatókönyvet javasolja.  

### <a name="subheading23"></a>A blob helyes típusának kiválasztása

Az Azure Storage két típusú blobot támogat: az *oldal* -blobokat és a *blokk* -blobokat. Egy adott használati forgatókönyv esetén a kiválasztott blob-típus hatással lesz a megoldás teljesítményére és méretezhetőségére. A Blobok blokkolása akkor megfelelő, ha nagy mennyiségű adatok hatékony feltöltését szeretné végezni: például előfordulhat, hogy egy ügyfélalkalmazás fényképeket vagy videót kell feltöltenie a blob Storage-ba. Az oldal Blobok akkor megfelelőek, ha az alkalmazásnak véletlenszerű írást kell végeznie az adatokon: például az Azure VHD-ket blobként tárolja a rendszer.  

További információkért lásd: a [Blobok, a blobok és a](https://msdn.microsoft.com/library/azure/ee691964.aspx)Blobok hozzáfűzésének ismertetése.  

## <a name="tables"></a>Táblák

Az összes korábban ismertetett [szolgáltatás](#allservices) bevált eljárásai mellett a következő bevált gyakorlatok kifejezetten a Table szolgáltatásra vonatkoznak.  

### <a name="subheading24"></a>Táblázatra jellemző skálázhatósági célok

A teljes Storage-fiók sávszélesség-korlátozásai mellett a táblázatok a következő speciális skálázhatósági korláttal rendelkeznek.  A rendszer terheléselosztást végez a forgalom növekedésével, de ha a forgalom hirtelen feltört, előfordulhat, hogy nem tudja azonnal lekérni ezt a kötetet.  Ha a minta feltört, érdemes megtekinteni a szabályozást és/vagy időtúllépést a burst alatt, mivel a Storage szolgáltatás automatikusan kiosztja a táblázatot.  A lassú indítás általában jobb eredményeket eredményez, mivel a rendszeridőt biztosít a megfelelő terheléselosztáshoz.  

#### <a name="entities-per-second-storage-account"></a>Entitások másodpercenként (Storage-fiók)

A táblák elérésének skálázhatósági korlátja legfeljebb 20 000 entitás (egyenként 1 KB), egy fiók esetében másodpercenként.  Általánosságban elmondható, hogy minden beszúrt, frissített, törölt vagy beolvasott entitás beleszámít a cél felé.  Így az 100 entitásokat tartalmazó batch-Beszúrás 100 entitásnak számít.  Az 1000 entitások vizsgálatára és az 5 értéket visszaadó lekérdezések 1000 entitásnak számítanak.  

#### <a name="entities-per-second-partition"></a>Entitások másodpercenként (partíció)

Egyetlen partíción belül a táblák elérésének méretezhetőségi célja 2 000 entitás (1 KB másodpercenként), az előző szakaszban leírtak szerint.  

### <a name="configuration"></a>Konfiguráció

Ez a szakasz több olyan gyors konfigurációs beállítást sorol fel, amelyek segítségével jelentős teljesítménybeli tökéletesítéseket végezhet a Table Service szolgáltatásban:  

#### <a name="subheading25"></a>JSON használata

A Storage Service 2013-08-15-es verziójától kezdve a Table Service támogatja a JSON használatát az XML-alapú AtomPub formátum helyett a tábla adatai átviteléhez. Ez csökkentheti a hasznos adatok méretét a 75%-kal, és jelentősen növelheti az alkalmazás teljesítményét.

További információ: Post [Microsoft Azure Tables: ](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/05/windows-azure-tables-introducing-json.aspx) [A táblázatos szolgáltatási műveletekhez tartozó](https://msdn.microsoft.com/library/azure/dn535600.aspx)JSON-és hasznos adatok formátumának bemutatása.

#### <a name="subheading26"></a>A nyár letiltása

A Nyéki algoritmusa széles körben implementálva van a TCP/IP-hálózatokon a hálózati teljesítmény javítása érdekében. Ez azonban nem minden esetben optimális (például a nagyon interaktív környezetekben). Az Azure Storage esetében a Nyéki algoritmusa negatív hatással van a tábla-és üzenetsor-szolgáltatásokra irányuló kérések teljesítményére, és ha lehetséges, tiltsa le.

### <a name="schema"></a>Séma

Az adat a legfontosabb egyetlen tényező, amely hatással van a Table Service teljesítményére. Habár minden alkalmazás eltérő, ez a szakasz néhány, a következőhöz kapcsolódó általános bevált gyakorlatot ismertet:  

* Tábla kialakítása
* Hatékony lekérdezések
* Hatékony adatfrissítések  

#### <a name="subheading27"></a>Táblák és partíciók

A táblák felosztva vannak osztva. A partíciókban tárolt összes entitás ugyanazzal a partíciós kulccsal osztozik, és egyedi sorral rendelkezik az adott partíción belüli azonosításához. A partíciók előnyöket biztosítanak, de a méretezhetőségi korlátokat is bevezetik.  

* Előnyök: Ugyanazon a partíción lévő entitásokat egyetlen, atomi, kötegelt tranzakcióban frissítheti, amely akár 100 különálló tárolási műveletet is tartalmaz (legfeljebb 4 MB teljes méret). Ha ugyanazokat a számú entitást szeretné lekérdezni, az egyes partíciókban lévő adatlekérdezések hatékonyabbak lesznek, mint a partíciókat átölelő adatmennyiségek (bár a táblázatos adatlekérdezéssel kapcsolatos további javaslatokról olvashat bővebben).
* Skálázhatósági korlát: Az egyetlen partíción tárolt entitásokhoz való hozzáférés nem lehet elosztott terhelésű, mert a partíciók támogatják az Atomic batch-tranzakciókat. Emiatt a különálló tábla partíciójának skálázhatósági célja alacsonyabb, mint a Table szolgáltatás egésze számára.  

A táblák és partíciók jellemzői miatt a következő tervezési alapelveket kell elfogadnia:  

* Ugyanazon a partíción kell lennie, amelyet az ügyfélalkalmazás gyakran frissít vagy lekérdez az azonos logikai egységben.  Ennek az lehet az oka, hogy az alkalmazás az írásokat összesíti, vagy az Atomic batch-műveletek előnyeit szeretné kihasználni.  Emellett az egyetlen partíción lévő adatlekérdezések hatékonyabban kérhetők le egyetlen lekérdezésben, mint az adatpartíciók között.
* Azok az adatmennyiségek, amelyeket az ügyfélalkalmazás nem szúr be/nem frissít, vagy lekérdezéseket ugyanabban a logikai egységben (egyetlen lekérdezés vagy batch-frissítés) kell elhelyezni, külön partíciókban kell lennie.  Fontos megjegyezni, hogy az egyetlen táblában nincs korlátozva a partíciós kulcsok száma, így több millió partíciós kulcs nem jelent problémát, és nem befolyásolja a teljesítményt.  Ha például az alkalmazás egy olyan népszerű webhely, amely felhasználói bejelentkezést használ, a felhasználói azonosító használatával a partíciós kulcs jó választás lehet.  

#### <a name="hot-partitions"></a>Gyakori partíciók

Egy olyan gyakori partíció, amely egy fiók felé irányuló forgalom aránytalan százalékát kapja, és nem lehet terheléselosztást végezni, mert egyetlen partíció.  Általánosságban elmondható, hogy a gyakori partíciók kétféleképpen jönnek létre:  

##### <a name="subheading28"></a>Csak hozzáfűzési és csak hozzáfűzési minták

A "csak Hozzáfűzés" minta egy adott PK-re irányuló forgalom egy adott (vagy majdnem teljes) értéke, és az aktuális idő függvényében csökken.  Ilyen például, ha az alkalmazás az aktuális dátumot használta a naplózási adatok partíciós kulcsaként.  Ez azt eredményezi, hogy az összes Beszúrás a tábla utolsó partícióján történik, és a rendszer nem tudja betölteni az egyensúlyt, mert az összes írás a tábla végére kerül.  Ha az adott partícióra irányuló forgalom mennyisége meghaladja a partíciós skálázhatósági célt, akkor a rendszer a szabályozást eredményezi.  Jobb, ha biztosítani szeretné, hogy a rendszer több partícióra küldje a forgalmat, így lehetővé téve a terhelés elosztását a táblán lévő kérelmek között.  

##### <a name="subheading29"></a>Nagy forgalmú adatok

Ha a particionálási séma egy olyan partíciót eredményez, amely csak a többi partíciónál sokkal nagyobb mennyiségű adattal rendelkezik, akkor is megtekintheti a szabályozást, mivel a partíció megközelíti a méretezhetőségi célt egy adott partícióhoz.  Érdemes meggyőződni arról, hogy a partíciós séma egyetlen partíción sem közelíti meg a méretezhetőségi célokat.  

#### <a name="querying"></a>Lekérdezés

Ez a szakasz a Table szolgáltatás lekérdezésének bevált gyakorlatait ismerteti.  

##### <a name="subheading30"></a>Lekérdezés hatóköre

A lekérdezni kívánt entitások köre több módon is meghatározható.  Az alábbiakban megtárgyaljuk az egyes alkalmazások használatát.  

Általánosságban elkerülheti, hogy a vizsgálatok ne legyenek (egyetlen entitásnál nagyobb lekérdezések), de ha vizsgálatra van szüksége, próbálja meg megszervezni az adatait, hogy a vizsgálatok beolvassák a szükséges adatait anélkül, hogy jelentős mennyiségű entitást kellene beolvasni vagy visszaadni.  

###### <a name="point-queries"></a>Pontok lekérdezései

A pont lekérdezése pontosan egy entitást kérdez le. Ezt a lekérdezni kívánt entitás partíciós kulcsának és a sor kulcsának megadásával végezheti el. Ezek a lekérdezések hatékonyak, és lehetőleg ezeket kell használniuk.  

###### <a name="partition-queries"></a>Partíciós lekérdezések

A partíciós lekérdezés egy olyan lekérdezés, amely közös partíciós kulcsot használó adathalmazt kérdez le. A lekérdezés általában megadja a sor kulcsának értékét vagy az egyes entitások tulajdonságának tartományát a partíciós kulcs mellett. Ezek kevésbé hatékonyak, mint a pontszerű lekérdezések, és érdemes takarékosan használni őket.  

###### <a name="table-queries"></a>Táblák lekérdezései

A tábla lekérdezése egy olyan lekérdezés, amely nem közös partíciós kulccsal rendelkező entitásokat kérdez le. Ezek a lekérdezések nem hatékonyak, és ha lehetséges, el kell kerülnie őket.  

##### <a name="subheading31"></a>Lekérdezési sűrűség

A lekérdezés hatékonyságának egy másik kulcsfontosságú tényezője a visszaküldött entitások száma a visszaadott készlet kereséséhez képest. Ha az alkalmazás egy tábla-lekérdezést hajt végre egy olyan tulajdonságérték alapján, amely csak az adatmegosztások 1%-át tartalmazza, akkor a lekérdezés minden visszaadott entitás 100 entitását vizsgálja. A korábban tárgyalt táblázat-méretezhetőségi célok a beolvasott entitások számával és nem a visszaadott entitások számával kapcsolatosak: az alacsony lekérdezési sűrűség egyszerűen okozhatja a Table szolgáltatás számára az alkalmazás szabályozását, mivel az eszköznek sok entitást kell beolvasnia kérje le a keresett entitást.  Ennek elkerüléséhez tekintse [](#subheading34) meg a denormalizálás alábbi szakaszát.  

##### <a name="limiting-the-amount-of-data-returned"></a>A visszaadott adatmennyiség korlátozása

###### <a name="subheading32"></a>Szűrés

Ha tudja, hogy egy lekérdezés olyan entitásokat ad vissza, amelyekre nincs szüksége az ügyfélalkalmazás számára, érdemes lehet szűrőt használni a visszaadott készlet méretének csökkentéséhez. Amíg az ügyfélnek nem visszaadott entitások még a méretezhetőségi korlátok felé is beleszámítanak, az alkalmazás teljesítménye a hálózati hasznos adatok méretének csökkentése és az ügyfélalkalmazás által feldolgozható entitások számának csökkenése miatt javulni fog.  Tekintse meg a [](#subheading31)fenti megjegyzést a lekérdezési sűrűségről, azonban – a méretezhetőségi célok a beolvasott entitások számához kapcsolódnak, így a sok entitást kiszűrő lekérdezés továbbra is szabályozást eredményezhet, még akkor is, ha kevés entitást ad vissza.  

###### <a name="subheading33"></a>Projection

Ha az ügyfélalkalmazás csak korlátozott tulajdonságokat igényel a tábla entitásai közül, a kivetítés használatával korlátozhatja a visszaadott adathalmaz méretét. A szűréshez hasonlóan ez segít csökkenteni a hálózati terhelést és az ügyfelek feldolgozását.  

##### <a name="subheading34"></a>Denormalizáció

A kapcsolati adatbázisokkal való együttműködéstől eltérően bevált eljárások a Table-adatforrások hatékony lekérdezéséhez az adataik denormalizálása érdekében. Ez azt eredményezi, hogy ugyanazokat az adatokkal duplikálja több entitásban (egyet az egyes kulcsokhoz, amelyeket az adatok kereséséhez használhat), hogy csökkentse azon entitások számát, amelyeket a lekérdezésnek meg kell vizsgálnia az ügyfél által igényelt adatok megkereséséhez, és nem kell nagy számú entitást beolvasnia az alkalmazás által használt adatok megtalálásához mazáskészlet szükséges.  Egy e-kereskedelmi webhelyen például érdemes lehet megrendelést keresni az ügyfél-azonosító alapján (adja meg az ügyfél megrendeléseit) és a dátumot (dátummal megadhatja a rendeléseket).  A Table Storageban az entitást (vagy az arra mutató hivatkozást) kétszer kell tárolnia – egyszer a Table Name, a PK és a RK használatával, hogy megkönnyítse az ügyfél-azonosító alapján történő keresését  

#### <a name="insertupdatedelete"></a>Beszúrás/frissítés/törlés

Ez a szakasz a Table Service-ben tárolt entitások módosítására vonatkozó bevált eljárásokat ismerteti.  

##### <a name="subheading35"></a>Adagoló

A Batch-tranzakciók az Azure Storage-ban az Entity Group Transactions (ETG SZOLGÁLATBA) néven ismertek. egy ETG SZOLGÁLATBA belüli összes műveletnek egyetlen partíción kell lennie egyetlen táblában. Ahol lehetséges, a ETGs használatával szúrhat be, frissítéseket és törölheti a kötegeket. Ez csökkenti az ügyfélalkalmazás és a kiszolgáló közötti átutazások számát, csökkentve a számlázható tranzakciók számát (az ETG SZOLGÁLATBA egyetlen tranzakciónak számít a számlázási célokra, és akár 100 tárolási műveletet is tartalmazhat), és lehetővé teszi az Atomic Updates (mind a műveletek sikeresek vagy az összes meghiúsulása egy ETG SZOLGÁLATBA belül). A nagy késéssel rendelkező környezetek, például a mobileszközök nagy mértékben kihasználják a ETGs-t.  

##### <a name="subheading36"></a>Upsert

Ha csak lehet, használja a Table **Upsert** -műveleteket. Kétféle **Upsert**létezik, amelyek közül mindkettő hatékonyabb lehet, mint egy hagyományos beszúrási és **frissítési** művelet:  

* **InsertOrMerge**: Akkor használja, ha az entitás tulajdonságainak egy részhalmazát szeretné feltölteni, de nem biztos benne, hogy az entitás már létezik-e. Ha az entitás létezik, a hívás frissíti a **Upsert** műveletben foglalt tulajdonságokat, és az összes meglévő tulajdonságot elhagyja, ha az entitás nem létezik, beszúrja az új entitást. Ez hasonló a lekérdezésekben a leképezések használatához, hogy csak a változó tulajdonságokat kell feltöltenie.
* **InsertOrReplace**: Akkor használja ezt a lehetőséget, ha egy teljesen új entitást szeretne feltölteni, de nem biztos benne, hogy már létezik-e. Ezt csak akkor kell használnia, ha tudja, hogy az újonnan feltöltött entitás teljesen helyes, mert teljesen felülírja a régi entitást. Például szeretné frissíteni a felhasználó aktuális helyét tároló entitást, függetlenül attól, hogy az alkalmazás korábban tárolta-e a felhasználó tartózkodási helyét. az új hely entitás elkészült, és semmilyen információt nem kell megadnia az előző entitásokról.

##### <a name="subheading37"></a>Adatsorozatok tárolása egyetlen entitásban

Előfordulhat, hogy egy alkalmazás egy adatsorozatot tárol, amelyet gyakran kell egyszerre lekérnie: például egy alkalmazás nyomon követheti a CPU-használatot az idő múlásával, hogy az elmúlt 24 órában ábrázolja az adatok diagramját. Az egyik módszer az, hogy egy tábla entitás/óra legyen, és minden entitás egy adott órát jelképez, és tárolja a CPU-használatot az adott órában. Az adatok ábrázolásához az alkalmazásnak le kell kérnie az adatait tároló entitásokat a 24 legutóbbi órában.  

Azt is megteheti, hogy az alkalmazás minden órában a CPU-használatot különálló tulajdonságként tárolja: az óránkénti frissítéshez az alkalmazás egyetlen **InsertOrMerge Upsert** hívást használhat a legutóbbi óra értékének frissítéséhez. Az adatábrázoláshoz az alkalmazásnak csak egyetlen entitást kell lekérnie 24 helyett, amely hatékony lekérdezést tesz elérhetővé (lásd a [lekérdezés hatókörének](#subheading30)fenti megvitatását).

##### <a name="subheading38"></a>Strukturált adattárolók tárolása a blobokban

Időnként úgy érzi, hogy a strukturált adatmennyiség táblákba kerül, de az entitások tartománya mindig együtt lesz lekérdezve, és kötegelt beilleszthető lehet.  Jó példa erre a naplófájlra.  Ebben az esetben több perces naplót is beállíthat, beszúrhatja őket, majd egyszerre több perces naplót is beolvashat.  Ebben az esetben a teljesítmény érdekében jobb, ha táblázatok helyett blobokat használ, mivel jelentősen csökkentheti a megírt/visszaadott objektumok számát, valamint általában a szükséges kérelmek számát.  

## <a name="queues"></a>Üzenetsorok

Az összes korábban ismertetett [szolgáltatás](#allservices) bevált eljárásai mellett a következő bevált eljárások kifejezetten az üzenetsor-szolgáltatásra vonatkoznak.  

### <a name="subheading39"></a>Méretezhetőségi korlátok

Egy üzenetsor körülbelül 2 000 üzenetet képes feldolgozni (másodpercenként 1 KB) (minden AddMessage, GetMessage és DeleteMessage). Ha ez nem elegendő az alkalmazáshoz, több várólistát kell használnia, és el kell osztania őket az üzenetek között.  

Az aktuális skálázhatósági célok megtekintése az [Azure Storage skálázhatósági és teljesítményi céljainál](storage-scalability-targets.md).  

### <a name="subheading40"></a>A nyár letiltása

Tekintse meg a következő témakört: a a nyár-algoritmust bemutató táblázatos konfiguráció – a Nyéki algoritmus általában rossz a várólista-kérelmek teljesítményéhez, és le kell tiltania.  

### <a name="subheading41"></a>Üzenet mérete

A várólista teljesítményének és méretezhetőségének csökkentése az üzenet méretének növekedésével. Csak azokat az adatokat kell elhelyeznie, amelyekre a fogadónak szüksége van egy üzenetben.  

### <a name="subheading42"></a>Kötegelt lekérés

Egy várólistából legfeljebb 32 üzenetet kérhet le egyetlen művelettel. Ezzel csökkentheti az ügyfélalkalmazások számát, ami különösen a nagy késéssel rendelkező környezetek, például a mobileszközök esetében hasznos.  

### <a name="subheading43"></a>Várólista lekérdezési időköze

A legtöbb alkalmazás egy üzenetsor által küldött üzenetek lekérdezése, amely az adott alkalmazáshoz tartozó tranzakciók egyik legnagyobb forrása lehet. Válassza ki a lekérdezési időközt bölcsen: a lekérdezés túl gyakran okozhatja az alkalmazásnak, hogy megközelítse a várólista skálázhatósági céljait. Azonban a $0,01-es (az írás időpontjában) 200 000-tranzakciókban a processzorok másodpercenkénti lekérdezése kevesebb, mint 15 cent, így a költségek nem jellemzően a lekérdezési időközt befolyásoló tényező.  

A naprakész költséggel kapcsolatos információkért lásd: az [Azure Storage díjszabása](https://azure.microsoft.com/pricing/details/storage/).  

### <a name="subheading44"></a>Üzenet frissítése

Az **üzenet frissítése** művelettel növelheti a nem láthatósági időtúllépést, vagy frissítheti egy üzenet állapotadatok állapotát. Habár ez nagy teljesítményű, ne feledje, hogy az egyes **frissítési üzenetek** minden művelete beleszámít a méretezhetőségi célra. Ez azonban sokkal hatékonyabb megoldás lehet, mint egy olyan munkafolyamatnál, amely egy adott várólistából a következőre küld egy feladatot, mivel a feladatok mindegyik lépése befejeződött. Az **üzenet frissítése** művelettel engedélyezheti az alkalmazásnak, hogy mentse a feladatot az üzenetbe, majd folytassa a munkát, ahelyett, hogy az üzenetet a művelet minden egyes lépésekor a következő lépéshez átadja.  

További információt a következő cikkben [talál: Egy várólistán lévő üzenet](../queues/storage-dotnet-how-to-use-queues.md#change-the-contents-of-a-queued-message)tartalmának módosítása  

### <a name="subheading45"></a>Alkalmazás architektúrája

Az alkalmazás architektúrájának méretezhetőségéhez várólistákat kell használnia. Az alábbi lista néhány módszert biztosít a várólisták használatára az alkalmazás méretezhetőségének megtételéhez:  

* A várólisták használatával várakozó munkaterhelések hozhatók létre az alkalmazásban felhasználható számítási és kiegyenlítő feladatok elvégzéséhez. Előfordulhat például, hogy a felhasználóktól érkező kéréseket a processzorok intenzív működéséhez, például a feltöltött képek átméretezéséhez szeretné elvégezni.
* A várólisták segítségével elválaszthatja az alkalmazás egyes részeit, így egymástól függetlenül méretezheti azokat. A webes előtér például a felhasználók által egy várólistába helyezheti a későbbi elemzést és tárolást. További feldolgozói szerepkör-példányok hozzáadásával feldolgozhatja a várólista-adatkészletek igény szerinti feldolgozását.  

## <a name="conclusion"></a>Összegzés

Ez a cikk a leggyakoribb és bevált eljárásokat ismerteti a teljesítmény optimalizálásához az Azure Storage használatakor. Arra bátorítunk minden alkalmazásfejlesztőt, hogy alkalmazásaikat a fenti gyakorlatok alapján értékeljék újra, és fontolják meg, hogy érdemes-e az ajánlások szerint eljárniuk az Azure Storage-t használó alkalmazásaik teljesítményének fokozása érdekében.
