---
title: Az Azure Storage teljesítményére és méretezhetőségére ellenőrzőlista |} Microsoft Docs
description: Egy ellenőrzőlista használható az Azure Storage performant-alkalmazások fejlesztésével bevált gyakorlatát.
services: storage
documentationcenter: ''
author: roygara
manager: jeconnoc
editor: tysonn
ms.assetid: 959d831b-a4fd-4634-a646-0d2c0c462ef8
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2016
ms.author: rogarana
ms.openlocfilehash: 945289a172270eea56625287baf437fd4b70c7f3
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
ms.locfileid: "30246219"
---
# <a name="microsoft-azure-storage-performance-and-scalability-checklist"></a>A Microsoft Azure Storage teljesítmény- és méretezhetőségi ellenőrzőlistája
## <a name="overview"></a>Áttekintés
A Microsoft Azure Storage szolgáltatás kiadása óta Microsoft performant módon ezen szolgáltatások használatára vonatkozó bevált gyakorlatok számos fejlesztett ki, és ez a cikk egy ellenőrzőlista stílusú lista egyik legfontosabb konszolidálhatják szolgál. Ez a cikk célja annak ellenőrzése, hogy azok bevált gyakorlat az Azure Storage alkalmazásfejlesztők és azokat más érdemes megfontolni a bevezetése bevált gyakorlatok azonosítása érdekében. Ez a cikk nem kísérli meg, amelyek minden lehetséges teljesítményének és méretezhetőségének optimalizálás – a művelet hatása a kis- és tágabb értelemben véve nem alkalmazható nem tartalmazza. Az az alkalmazás viselkedésében kialakítása során előre jelezhető, célszerű a korai terveket, fog futni a teljesítménybeli problémák elkerülése érdekében szem előtt kell tartani.  

Minden Azure Storage használatával alkalmazásfejlesztő gyorsabban ebben a cikkben, és ellenőrizze, hogy a saját alkalmazás követi, minden egyes, az alább felsorolt bevált ideje.  

## <a name="checklist"></a>Feladatlista
Ez a cikk a bevált gyakorlat a következő csoportokba rendezi. Bevált gyakorlatok alkalmazható:  

* Minden Azure Storage szolgáltatás (BLOB, táblák, üzenetsorok és fájlok)
* Blobok
* Táblák
* Üzenetsorok  

| Kész | Terület | Kategória | Kérdés |
| --- | --- | --- | --- |
| &nbsp; | All Services (Minden szolgáltatás) |Méretezhetőségi célok |[Az alkalmazás arra tervezték, hogy kerülje a majdnem elérte a méretezhetőségi célok?](#subheading1) |
| &nbsp; | All Services (Minden szolgáltatás) |Méretezhetőségi célok |[Az elnevezési lehetővé teszi, hogy jobban terheléselosztás?](#subheading47) |
| &nbsp; | All Services (Minden szolgáltatás) |Hálózat |[Rendelkeznek-e ügyféloldali ügyféleszközök elég nagy sávszélességet és az alacsony késleltetés a szükséges teljesítmény eléréséhez?](#subheading2) |
| &nbsp; | All Services (Minden szolgáltatás) |Hálózat |[Ügyféloldali ügyféleszközök rendelkeznek elég kiváló minőségű hivatkozást?](#subheading3) |
| &nbsp; | All Services (Minden szolgáltatás) |Hálózat |[Az ügyfélalkalmazás található "" a tárfiókot?](#subheading4) |
| &nbsp; | All Services (Minden szolgáltatás) |Tartalomterjesztés |[Használja a CDN a tartalmak terjesztéséhez?](#subheading5) |
| &nbsp; | All Services (Minden szolgáltatás) |Közvetlen ügyfélhozzáférés |[Használnak, a SAS, valamint a CORS engedélyezése helyett proxy-tárhely eléréséhez közvetlen hozzáférést?](#subheading6) |
| &nbsp; | All Services (Minden szolgáltatás) |Gyorsítótárazás |[A gyorsítótárazási alkalmazásadatok ismételten használt és módosítások ritkán van?](#subheading7) |
| &nbsp; | All Services (Minden szolgáltatás) |Gyorsítótárazás |[Az alkalmazás kötegelés van a frissítések (ügyféloldali gyorsítótárazás őket, és majd töltse fel a nagyobb készletek)?](#subheading8) |
| &nbsp; | All Services (Minden szolgáltatás) |.NET Configuration |[Rendelkezik-e konfigurálva az ügyfél számára elegendő az egyidejű kapcsolatok használatát?](#subheading9) |
| &nbsp; | All Services (Minden szolgáltatás) |.NET Configuration |[Állított .NET szálak elegendő számú használni?](#subheading10) |
| &nbsp; | All Services (Minden szolgáltatás) |.NET Configuration |[.NET 4.5-ös verziójának használatával, vagy később, amely javult a szemétgyűjtés?](#subheading11) |
| &nbsp; | All Services (Minden szolgáltatás) |Párhuzamos végrehajtás |[Biztosították, hogy párhuzamossági korlátozódik megfelelően, hogy nem túlterhelés, vagy az ügyfél lehetőségek körét, vagy a méretezhetőségi célok?](#subheading12) |
| &nbsp; | All Services (Minden szolgáltatás) |Eszközök |[Vannak a legújabb verzió Microsoft megadott ügyfél-kódtárak és eszközök?](#subheading13) |
| &nbsp; | All Services (Minden szolgáltatás) |Újrapróbálkozások |[Azok az exponenciális leállítási használatával újra hibák és időtúllépéseket okoz szabályozás házirend?](#subheading14) |
| &nbsp; | All Services (Minden szolgáltatás) |Újrapróbálkozások |[Az alkalmazás elkerülő újrapróbálkozások van – Újrapróbálkozást lehetővé nem tevő hibákat?](#subheading15) |
| &nbsp; | Blobok |Méretezhetőségi célok |[Rendelkezik egy adott objektum egyidejűleg hozzáférő ügyfelek nagy számú?](#subheading46) |
| &nbsp; | Blobok |Méretezhetőségi célok |[A sávszélesség vagy a műveletek méretezhetőség a cél egyetlen BLOB marad az alkalmazást?](#subheading16) |
| &nbsp; | Blobok |Blobok másolása |[Másolási blobok vannak hatékony módon?](#subheading17) |
| &nbsp; | Blobok |Blobok másolása |[AzCopy használunk a blobok tömeges másolatot?](#subheading18) |
| &nbsp; | Blobok |Blobok másolása |[Használja Azure Import/Export nagyon nagy mennyiségű adatot továbbít?](#subheading19) |
| &nbsp; | Blobok |Metaadatok |[Akkor tárolja a gyakran használt BLOB metaadatait a metaadatok?](#subheading20) |
| &nbsp; | Blobok |Gyors feltöltése |[Gyorsan egy blob feltöltése közben meg feltölteni, párhuzamos blokkok?](#subheading21) |
| &nbsp; | Blobok |Gyors feltöltése |[Feltöltése közben számos blobok gyorsan, akkor feltölteni párhuzamosan blobok?](#subheading22) |
| &nbsp; | Blobok |Correct Blob Type |[Használ lapblobokat vagy adott esetben a blokkblobok?](#subheading23) |
| &nbsp; | Táblák |Méretezhetőségi célok |[A másodpercenként entitásokra vonatkozó méretezhetőségi célok megközelítő?](#subheading24) |
| &nbsp; | Táblák |Konfiguráció |[Használ JSON-ban a tábla kérelmek?](#subheading25) |
| &nbsp; | Táblák |Konfiguráció |[Ön kikapcsolta Nagle kis kérelmek a teljesítmény javítása érdekében?](#subheading26) |
| &nbsp; | Táblák |Táblák és -partíciók |[Rendelkezik meg megfelelően particionálva az adatokat?](#subheading27) |
| &nbsp; | Táblák |Működés közbeni partíciók |[Fűzi, és csak illesztenie minták elkerülésére?](#subheading28) |
| &nbsp; | Táblák |Működés közbeni partíciók |[A Beszúrás/frissítés sok partíciót között van elosztva?](#subheading29) |
| &nbsp; | Táblák |Lekérdezés hatóköre |[Kell megtervezni a sémát úgy, hogy a legtöbb esetben használandó pont lekérdezések, és a tábla lekérdezések takarékosan?](#subheading30) |
| &nbsp; | Táblák |Lekérdezés sűrűség |[A lekérdezések általában csak vizsgálat, és térjen vissza a sorok, amelyekre az alkalmazás fogja használni?](#subheading31) |
| &nbsp; | Táblák |Korlátozza az adatokat adott vissza |[Használ szűrés ad vissza, nem szükséges entitások elkerülésére?](#subheading32) |
| &nbsp; | Táblák |Korlátozza az adatokat adott vissza |[Használunk leképezés nem szükséges tulajdonságok visszaadó elkerülésére?](#subheading33) |
| &nbsp; | Táblák |Denormalization |[Rendelkezik denormalizált az adatok úgy, hogy ne hatékony lekérdezések és több olvasási kéréseket adatok beolvasására tett kísérlet során?](#subheading34) |
| &nbsp; | Táblák |Insert/Update/Delete |[Ön kötegelés kér, amelyen a kell lennie, vagy üzenetváltások csökkentése érdekében egy időben végezhető?](#subheading35) |
| &nbsp; | Táblák |Insert/Update/Delete |[Vannak, szükségtelenné téve egy entitás csak szükség van-e Beszúrás vagy frissítés hívása beolvasása?](#subheading36) |
| &nbsp; | Táblák |Insert/Update/Delete |[Tekintette tárolja azokat a gyakran letöltött adatok együtt egyetlen entitás több entitás helyett tulajdonságként?](#subheading37) |
| &nbsp; | Táblák |Insert/Update/Delete |[Mindig veszi együtt, és kötegekben (pl. idő adatsor) csak írható entitások akkor tekintette a blobs használata helyett táblák?](#subheading38) |
| &nbsp; | Üzenetsorok |Méretezhetőségi célok |[Eléri az üzenetek / másodperc vonatkozó méretezhetőségi célok?](#subheading39) |
| &nbsp; | Üzenetsorok |Konfiguráció |[Ön kikapcsolta Nagle kis kérelmek a teljesítmény javítása érdekében?](#subheading40) |
| &nbsp; | Üzenetsorok |Üzenet mérete |[Az üzenetek CD vannak a várólista a teljesítmény javítása érdekében?](#subheading41) |
| &nbsp; | Üzenetsorok |Tömeges beolvasása |[Egy "Get" művelettel több üzenetek fogadása?](#subheading42) |
| &nbsp; | Üzenetsorok |Lekérdezési gyakorisága |[Vannak, lekérdezési elég gyakran az alkalmazás érzékelt működési késés csökkentése érdekében?](#subheading43) |
| &nbsp; | Üzenetsorok |Üzenet frissítése |[Használ UpdateMessage kerülése újból feldolgozza az egész üzenetet, ha hiba lép fel, az üzenetek feldolgozása folyamatban tárolására?](#subheading44) |
| &nbsp; | Üzenetsorok |Architektúra |[Használja a várólisták a teljes alkalmazás több méretezhető hosszan futó munkaterhelések kívül a kritikus út tartja és méretezését majd?](#subheading45) |

## <a name="allservices"></a>All Services
Ez a rész felsorolja a bevált gyakorlat, amelyek az Azure Storage szolgáltatás (BLOB, táblák, üzenetsorok vagy fájlok) használatával alkalmazhatók.  

### <a name="subheading1"></a>Méretezhetőségi célok
Az Azure Storage szolgáltatás mindegyikén méretezhetőségi célok kapacitás (GB), a tranzakciók sebességét és a sávszélesség. Ha az alkalmazás megközelíti vagy meghaladja a méretezhetőségi célok, észlelhetnek megnövekedett tranzakció késések vagy a sávszélesség-szabályozás. Amikor egy társzolgáltatás azelőtt gyorsítja fel az alkalmazást, a szolgáltatás elkezdi "503-as kiszolgáló elfoglalt" vagy "500 művelet időtúllépése" hibakódok néhány storage-tranzakció vissza. Ez a szakasz ismerteti mind az általános megközelítése méretezhetőségi célok sávszélesség méretezhetőségi és teljesítménycélokat különösen foglalkoznak. A későbbi fejezetek, amely az egyéni tárolószolgáltatások kezelésére, hogy adott szolgáltatás kontextusában vonatkozó méretezhetőségi célok ismertetik:  

* [A BLOB sávszélesség és a kérelmek / másodperc](#subheading16)
* [Táblaentitásokat / másodperc](#subheading24)
* [Várólista-üzenetek / másodperc](#subheading39)  

#### <a name="sub1bandwidth"></a>Sávszélesség méretezhetőség cél minden szolgáltatáshoz
Írásának időpontjában a sávszélesség georedundáns tárolás (GRS) fiók az Amerikai Egyesült Államokban célpontjai 10 Gigabit / másodperc (Gbps) érkező (a tárfiók küldött adatok) és a kimenő forgalom (a tárfiók által küldött adatok) 20 GB/s. Helyileg redundáns tárolás (LRS) fiók, használati korlátait magasabbak – 20 GB/s a bemenő és kimenő forgalom esetében 30 GB/s.  Nemzetközi sávszélességkorlátok alacsonyabb lehet, és megtalálható a [méretezhetőségi célok lap](http://msdn.microsoft.com/library/azure/dn249410.aspx).  A redundancia tárolásáról további információkért lásd: a hivatkozások [hasznos források](#sub1useful) alatt.  

#### <a name="what-to-do-when-approaching-a-scalability-target"></a>Mi a teendő, ha eléri a méretezhetőség cél
Ha az alkalmazás majdnem elérte a méretezhetőségi célok egyetlen tárfiók, vegye figyelembe a vezetnek be a következő módszerek egyikét:  

* Vizsgálja felül a terhelést, az alkalmazás megközelítik vagy meghaladják a méretezhetőség cél. Megtervezheti, hogy másképp kisebb sávszélességet vagy a kapacitás, illetve kevesebb tranzakciók használata?
* Ha egy alkalmazás haladhatja meg a vonatkozó méretezhetőségi célok egyikét, érdemes létrehozni storage-fiókok és a partíció az alkalmazásadatok ezeket több tárfiókok között. Ha ezt a mintát használja, akkor ne feledje, az alkalmazás kialakítani, hogy a további tárfiókok a jövőben terheléselosztásra is hozzáadhat. Írásának időpontjában minden Azure-előfizetés lehet akár 100 storage-fiókok.  Storage-fiókok is a használati tárolt adatokat, a végrehajtott tranzakciók vagy az átvitt adatok tekintetében eltérő költség nélkül.
* Ha az alkalmazás a sávszélesség célokat találatok, fontolja meg az ügyfél elküldi az adatokat a társzolgáltatás szükséges sávszélesség csökkentése érdekében az adatok tömörítése.  Vegye figyelembe, hogy a sávszélességet, és hálózati teljesítmény javításához, azt is beállíthatja, hogy néhány negatív hatások.  A teljesítményre gyakorolt hatását, ez a tömörítés és az ügyfél az adatok kibontása további feldolgozás követelményei miatt kell kiértékelni. Emellett a tömörített adatok tárolása több nehézkessé teheti a probléma megoldásához, mivel elképzelhető, hogy a nehezebb szabványos eszközökkel tárolt adatok megtekintéséhez.
* Ha az alkalmazás a méretezhetőségi célok találatok, majd győződjön meg arról, hogy egy exponenciális leállítási újrapróbálkozások használ (lásd: [újrapróbálkozások](#subheading14)).  Győződjön meg arról, hogy meg soha nem készíthető elő a méretezhetőségi célok (által a fenti módszerek egyikének használatával), de ezzel biztosíthatja, hogy az alkalmazás nem csupán kísérleteket gyorsan, így a sávszélesség-szabályozás rosszabb célszerűbb.  

#### <a name="useful-resources"></a>Hasznos segédanyagok
Az alábbi hivatkozások méretezhetőségi célok részletesebben is biztosítanak:

* Lásd: [Azure Storage méretezhetőségi és teljesítménycéloknak](storage-scalability-targets.md) méretezhetőségi célok kapcsolatos információkat.
* Lásd: [Azure Storage replikációs](storage-redundancy.md) és blogbejegyzést [Azure tárolási redundancia lehetőségek és az írásvédett Georedundáns redundáns tárolás](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx) tárolási redundancia beállításokkal kapcsolatos információkért.
* Az Azure szolgáltatások árazással kapcsolatos aktuális információkért lásd: [Azure-beli árakról](https://azure.microsoft.com/pricing/overview/).  

### <a name="subheading47"></a>Partíció elnevezési egyezmény
Az Azure Storage egy tartomány-alapú particionálási sémát használja a méretezés és a terhelés elosztása a rendszer. A partíciós kulcs tartományokkal és a tartományok adatimportáláshoz vannak terhelésű a rendszer partíció szolgál. Ez azt jelenti, például lexikális rendezés (pl. msftpayroll, msftperformance, msftemployees stb) elnevezési konvenciókat időbélyegeket (log20160101, log20160102, log20160102, stb.) használatával lesz alkalmasnak a partíciókra alatt esetlegesen közös elhelyezésű partíció ugyanarra a kiszolgálóra, vagy csak egy művelet terheléselosztás felosztja a őket kimenő kisebb tartományt. Például összes BLOB a tárolóban lévő szolgáltatható egyetlen kiszolgáló mindaddig, amíg a blobok terhelése szükséges további újraelosztás a partíció tartományait. Ehhez hasonlóan enyhén betöltött fiókokat a nevük lexikális sorrendbe rendezve csoportja csak egy terhelése kezdeményezheti a egyetlen kiszolgáló, vagy az összes ezeket a fiókokat szükségesek, hogy kell-e osztani több partíció-kiszolgálót. Minden terheléselosztási művelet hatással lehet a tárolási hívások késleltetése a művelet során. A rendszer egy partícióhoz forgalom hirtelen kapacitásnövelés kezelésének képessége mindaddig, amíg a terheléselosztási művelet megrúgni és a újra egyensúlyba hozza a partíciós kulcs tartomány korlátozza az egypartíciós kiszolgáló méretezhetőségét.  

Követheti, hogy csökkentse az ilyen műveletek vonatkozó ajánlott eljárásokat.  

* Vizsgálja meg az elnevezési konvenciót kell használni a fiókok, tárolók, blobok, táblák és várólisták, szorosan. Fontolja meg a kivonatoló függvénnyel az igényeinek leginkább megfelelő 3 számjegyű kivonatát a fióknevek előtag.  
* Ha időbélyegeket vagy numerikus azonosítók használata esetén az adatok rendezéséhez kell arról, hogy nem használja csak a hozzáfűző (vagy csak illesztenie) forgalmat. Ezek a minták nincsenek megfelelő-e a tartomány-alapú rendszer particionálás, és sikertelen lesz egy olyan partíciót, és hatékonyan korlátozza a rendszer az összes forgalom vezethet terheléselosztás. Például ha az időbélyegzőnek ÉÉÉÉHHNN például egy blob objektumot használó napi műveleteket, majd az adott napi műveletre vonatkozó összes forgalom irányul, egy adott objektum, amely egyetlen partíció-kiszolgáló által kiszolgált. Keresse meg, hogy a blob korlátokat partíciónként korlátok az igényeinek, és fontolja meg, ez a művelet ossza több blobokat, ha szükséges. Hasonlóképpen ha idő adatsorozat adat tárolása a táblák, összes lehet a forgalom irányítja utolsó a kulcs névtér része. Időbélyegeket vagy numerikus azonosítót kell használnia, ha az azonosítója a 3 számjegyű kivonatát, illetve időbélyegeket előtag előtag ssyyyymmdd például idő másodpercben részét. Ha listázása, és lekérdezi-műveleteket rendszeresen hajtja végre, válassza ki a kivonatoló függvényt, amely korlátozza a lekérdezések száma. Egyéb esetben véletlenszerű előtag elegendő lehet.  
* Az Azure Storage használt particionálási sémát az további információk olvashatók a SOSP paper [Itt](http://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf).

### <a name="networking"></a>Hálózat
Amíg az API-t hívja, függetlenül attól, hogy, gyakran az alkalmazás a fizikai hálózat korlátozásait jelentős hatást a teljesítményre. Az alábbiakban ismertetett felhasználók szembesülhetnek korlátozások némelyike.  

#### <a name="client-network-capability"></a>Ügyfél hálózati funkció
##### <a name="subheading2"></a>Átviteli sebesség
A sávszélesség a probléma gyakran az ügyfél képességeit. Például egy tárfiók kezelni tud a 10 GB/s, vagy több érkező közben (lásd: [sávszélesség méretezhetőségi célok](#sub1bandwidth)), a hálózati sebesség "Kicsi" Azure feldolgozói szerepkör példányt csak nem képes a körülbelül 100 MB/s. Nagyobb méretű Azure-példányokon a hálózati adapterek nagyobb kapacitással rendelkező rendelkezik, ezért érdemes egy nagyobb példányt, vagy több virtuális gép használ, ha nagyobb hálózati korlátok egyetlen számítógépről van szüksége. Ha egy társzolgáltatás on-premises alkalmazásból fér hozzá, akkor ugyanaz a szabály vonatkozik: az ügyféleszköz- és a hálózati kapcsolat az Azure tárolási helyre hálózati funkcióinak megismerése és javítása vagy azok regisztrációja, mivel azok képességeinek belül működjön az alkalmazás vagy szükséges.  

##### <a name="subheading3"></a>Hivatkozás minősége
Csakúgy, mint bármely hálózathasználatot, vegye figyelembe, hogy hálózati körülmények hibák és a csomagveszteség lassú-e a hatékony átviteli sebességet.  WireShark vagy NetMon segítséget nyújthat a probléma diagnosztizálásához.  

##### <a name="useful-resources"></a>Hasznos segédanyagok
További információ a virtuálisgép-méretek és a hozzárendelt sávszélesség: [Windows Virtuálisgép-méretek](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) vagy [Linux Virtuálisgép-méretek](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

#### <a name="subheading4"></a>Hely
Minden elosztott környezetben helyezi el a kiszolgáló közel az ügyfél biztosítja a legjobb teljesítmény érdekében az. Az Azure Storage elérése a legkisebb mértékű késleltetést, a legjobb az ügyfél helye belül azonos Azure-régiót. Például ha egy Azure Storage használó Azure-webhelyet, akkor kell helyezni őket mindkét (például US nyugati vagy Ázsia délkeleti) egyetlen régión belül. Ez csökkenti a késést és a költségek – írásának időpontjában egyetlen régión belül sávszélesség ingyenes.  

Ha az alkalmazások nem található Azure-ban (például a mobileszköz-alkalmazások vagy a helyi vállalati szolgáltatások), majd újra ügyfél helyezi el a tárfiók egy régiót közel az eszközöket, amelyek hozzáférhet azokhoz, általában csökkenti késés. Ha az ügyfelek széles körű terjesztése (a példában, néhány Észak-Amerikában, és néhány, az Európai), akkor érdemes több tárfiókot használ: egy Észak-amerikai régió, a másik egy Európai régióban található. Ez segít mindkét régióban lévő felhasználók számára a késés csökkentése érdekében. Erre akkor általában könnyebben megvalósítása, ha az adatok az alkalmazás tárolja egyéni felhasználók számára, és nem igényli a storage-fiókok közötti adatreplikálás.  A széles körű tartalmak terjesztéséhez CDN ajánlott – a következő szakaszban talál további részleteket.  

### <a name="subheading5"></a>Tartalom terjesztése
Bizonyos esetekben egy alkalmazást kell (pl. termék bemutató videó webhely a kezdőlap adatgyűjtéséhez használt), a több felhasználó található azonos, vagy több régióba ugyanazt a tartalmat. Ebben a forgatókönyvben egy Content Delivery Network (CDN) például az Azure CDN-t kell használnia, és a CDN volna az Azure storage használata az adatok forrása. Egy Azure Storage-fiókot, amely egy régió létezik, és más régiókba kis késéssel, amely nem továbbítanak tartalmat, eltérően Azure CDN világszerte több adatközpontokban kiszolgálót használ. Emellett a CDN általában támogathat egyetlen tárfiók sokkal nagyobb kimenő korlátokat.  

Azure CDN kapcsolatos további információkért lásd: [Azure CDN](https://azure.microsoft.com/services/cdn/).  

### <a name="subheading6"></a>SAS és a CORS használatával
Egy webes böngésző vagy a hozzáférési adatok az Azure Storage a mobiltelefonján lévő alkalmazás például a JavaScriptek kód engedélyezni kell, egyik módszer esetén a proxy-ként webes szerepkör egy alkalmazás használatát: a felhasználó eszközének a webes szerepkör, amely viszont végzi a hitelesítést a társzolgáltatás végzi a hitelesítést. Ezzel a módszerrel elkerülhetők a teszi ki a tárfiók kulcsait a nem biztonságos eszközökön. Azonban ez elhelyez egy nagy terhelést a webes szerepkör, mert a felhasználó-eszköz és a tárolási szolgáltatás között továbbított összes adat kell haladnia a webes szerepkör. Elkerülheti a webes szerepkör alapján proxyként a társzolgáltatás megosztott hozzáférési aláírásokkal (SAS), egyes esetekben a fejlécek eltérő eredetű erőforrások megosztása (CORS) együtt. SAS segítségével engedélyezheti a felhasználó eszközén legyen kérelmek közvetlenül egy társzolgáltatás egy korlátozott hozzáférési jogkivonat segítségével. Például ha egy felhasználó egy fénykép feltöltése az alkalmazáshoz, a webes szerepkör is létrehozó és küldő a felhasználók egy SAS-jogkivonatot, amely engedélyt ad egy adott blob vagy a tároló (amely után a SAS-jogkivonat lejár) 30 percig írni.

Általában a böngésző nem engedélyezi a JavaScript tartománya például a "PUT" egy másik tartományra meghatározott műveleteket hajt végre egy webhelyet üzemelteti lapon. Ha például egy webes szerepkört: "contosomarketing.cloudapp.net", és az ügyfél ügyféloldali JavaScript feltölteni a blob storage fiókját a "contosoproducts.blob.core.windows.net," a böngésző által használni kívánt "azonos származási policy" fog megtiltják ezt a műveletet. A CORS egy funkciója, amely lehetővé teszi a céltartományban (ebben az esetben a tárfiók) való kommunikációhoz a böngészőnek, hogy megbízik a forrástartomány (ebben az esetben a webes szerepkör) származó kérelmek.  

Mindkét technológiát segítségével elkerülhető szükségtelen terhelés (és a szűk keresztmetszeteket) a webalkalmazáshoz.  

#### <a name="useful-resources"></a>Hasznos segédanyagok
További információ a SAS: [megosztott hozzáférési aláírásokkal, 1. rész: az SAS-modell ismertetése](../storage-dotnet-shared-access-signature-part-1.md).  

További információ a CORS: [Cross-Origin Resource Sharing (CORS) támogatása az Azure Storage szolgáltatásainak](http://msdn.microsoft.com/library/azure/dn535601.aspx).  

### <a name="caching"></a>Gyorsítótárazás
#### <a name="subheading7"></a>Adatok beolvasása
Adatok lekérése egy szolgáltatás egyszer általában jobb, mint az első azt kétszer. Vegyünk például egy webes szerepkör, amely rendelkezik már lekért 50MB blob egy felhasználó tartalmat szolgál a társzolgáltatás futó MVC webalkalmazás. Az alkalmazás akkor majd lekérheti a, hogy ugyanazt a blob, minden alkalommal, amikor a felhasználó azt kéri, vagy azt sikerült gyorsítótárazása azt helyileg a lemezre, és újra felhasználhatja a gyorsítótárazott verzió a következő felhasználói kéréseket. Továbbá, amikor egy felhasználó kéri az adatokat, az alkalmazás problémát sikerült feltételes fejlécet beállítani a módosítás időpontja, amely volna elkerülése érdekében a teljes blob lekérése, ha még nem lett módosítva. Ebben a mintában alkalmazása táblaentitásokat használata.  

Bizonyos esetekben dönthet úgy, hogy az alkalmazás feltételezheti, hogy a blob beolvasása az után rövid ideig érvényes marad, és hogy ebben az időszakban az alkalmazás nem kell bejelölni módosították-e a blob.

Konfiguráció, a keresési és az egyéb adatok mindig az alkalmazás által használt rendszer deduplikációra kiválóan alkalmas jelöltek a gyorsítótárazáshoz.  

Hogyan kérhet egy blob tulajdonságai .NET használatával utolsó módosítási dátumának felderítéséhez példáért lásd: [Set, a Tulajdonságok beolvasása és a metaadatok](../blobs/storage-properties-metadata.md). Feltételes letöltéseire vonatkozó további információkért lásd: [feltételesen frissítése a helyi másolat készítése a Blob](http://msdn.microsoft.com/library/azure/dd179371.aspx).  

#### <a name="subheading8"></a>Adatok kötegelt feltöltése
Néhány alkalmazás esetben összesíteni az adatokat helyileg, és majd rendszeres időközönként töltse fel egy kötegben minden adat azonnal feltöltése helyett. Például egy webes alkalmazás előfordulhat, hogy tartsa a tevékenységek naplófájl: az alkalmazás vagy sikerült feltölteni minden tevékenység részleteinek, akkor fordul elő, a tábla egységként (amelyhez szükséges számos tárolási műveletek), vagy azt sikerült tevékenység részletei a helyi fájlba mentése, majd rendszeresen töltse fel az összes tevékenység részletei tagolt blob-fájlként. Ha minden naplóbejegyzés 1KB-nál, feltöltheti a több ezer (legfeljebb 64 MB-nál egy tranzakción belül blob is feltölthet) egy "Put Blob" tranzakción belül. Természetesen a helyi gép a feltöltés előtt omlik össze, potenciálisan elvész néhány naplóadatok: az alkalmazás fejlesztőjének kell ügyféleszköz lehetőségét tervezi vagy annak feltöltése sikertelen.  Ha a tevékenységek adatai a timespans (csak egyetlen tevékenység) le kell tölteni, majd blobok javasoltak táblák keresztül.

### <a name="net-configuration"></a>.NET Configuration
Ha a .NET keretrendszert használja, ez a szakasz számos gyors konfigurációs beállítások segítségével ellenőrizze az jelentős teljesítményjavulást eredményezhet.  Ha használ egyéb nyelvek, ellenőrizze, hogy ha hasonló fogalmak alkalmazza a kiválasztott nyelven.  

#### <a name="subheading9"></a>Alapértelmezett korlát növelése
A .NET a következő kódot az alapértelmezett kapcsolathoz megadott korlátot (amely általában 2 ügyfél környezetben vagy 10 kiszolgálói környezetben) és 100 növeli. Általában akkor kell beállítania a érték körülbelül az alkalmazás által használt szálak száma.  

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Meg kell adni a kapcsolatokat megnyitása előtt a kapcsolathoz megadott korlátot.  

Más programozási nyelvek dokumentációjában adott nyelven, hogyan lehet beállítani a kapcsolathoz megadott korlátot.  

További információkért lásd a következő blogbejegyzésben [webszolgáltatások: létesített egyidejű kapcsolatok](http://blogs.msdn.com/b/darrenj/archive/2005/03/07/386655.aspx).  

#### <a name="subheading10"></a>Növelje a szálkészlet Min szálak, ha aszinkron és szinkron kód használatával
Ez a kód növeli a szál készlet min szálak:  

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

További információkért lásd: [ThreadPool.SetMinThreads metódus](http://msdn.microsoft.com/library/system.threading.threadpool.setminthreads%28v=vs.110%29.aspx).  

#### <a name="subheading11"></a>.NET 4.5 szemétgyűjtés előnyeinek kihasználása
.NET 4.5-ös vagy újabb, az ügyfél alkalmazás segítségével server szemétgyűjtés teljesítménnyel kapcsolatos fejlesztések előnyeit.

További információkért lásd: a cikk [az Áttekintés a teljesítménnyel kapcsolatos fejlesztések a .NET 4.5](http://msdn.microsoft.com/magazine/hh882452.aspx).  

### <a name="subheading12"></a>Unbounded párhuzamossági
Párhuzamossági kiváló teljesítmény lehetnek, ügyeljen arra, unbounded párhuzamossági (a szálak és/vagy a párhuzamos kérések száma korlátozva) segítségével feltölteni, vagy töltse le az adatok több feldolgozókkal több partíciót (tárolók, üzenetsorokat vagy tábla partíciók) a tárfiókon eléréséhez, vagy több elem ugyanazon partíció elérésére. Ha a párhuzamos végrehajtás unbounded, az alkalmazás lépheti túl a ügyféleszköz-képességek, vagy a tárfiók méretezhetőség célozza, ami hosszabb késések fordulnak elő, és a szabályozás.  

### <a name="subheading13"></a>Storage Ügyfélkódtáraival és eszközök
Mindig legyen a legújabb Microsoft által biztosított ügyfél kódtárak és eszközök. Írásának időpontjában nincsenek .NET, Windows Phone, Windows-Futtatókörnyezetű, Java és C++ elérhető klienskódtárait, valamint más nyelvekre preview szalagtárak. Ezenkívül a Microsoft közzétette PowerShell-parancsmagok és az Azure Storage használatához Azure parancssori felület parancsait. Microsoft aktívan házon belül fejlesztett alkalmazásokra teljesítménnyel szem előtt ezeket az eszközöket, a legújabb verziót a naprakészen tartása, és biztosítja a belsőleg azok kezelni a teljesítmény bevált gyakorlatát.  

### <a name="retries"></a>Újrapróbálkozások
#### <a name="subheading14"></a>Throttling/ServerBusy
Bizonyos esetekben a társzolgáltatás előfordulhat, hogy szabályozni az alkalmazáshoz, vagy egyszerűen esetleg nem tudja teljesíteni a kérést néhány átmeneti miatt, és térjen vissza a "503-as kiszolgáló elfoglalt" üzenetet, vagy az "500 időtúllépése".  Ez akkor fordulhat elő, ha az alkalmazás közeledik, a méretezhetőségi célok bármelyikét, vagy ha a rendszer az újraelosztás particionált adatait, hogy nagyobb átviteli sebesség eléréséhez.  Az ügyfélalkalmazás általában újra kell próbálkoznia ilyen hibát okoz, művelet: a kérésben újabb kísérlet sikeres lehet. Ha a társzolgáltatás van az alkalmazás szabályozását, mert a méretezhetőségi célok túllépte, vagy akkor is, ha a szolgáltatás nem tudta teljesíteni a kérést valamilyen más okból, az agresszív újrapróbálkozások általában teszik a probléma ami még rosszabb. Emiatt az exponenciális vissza (az ügyfél szalagtárak alapértelmezett Ez a viselkedés) ki kell használnia. Például az alkalmazás előfordulhat, hogy 2 másodperc, majd 4 másodperc 10 másodpercet, majd a 30 másodperc után próbálkozzon újra, és majd teljesen feladták. Ezt a viselkedést eredményezi, az alkalmazás jelentősen csökkenti annak terhelésétől, a szolgáltatás helyett súlyosbodott problémákat.  

Vegye figyelembe, hogy kapcsolódási hibák követően újra megkísérelhető azonnal, mivel nincsenek-e a sávszélesség-szabályozás eredményét, illetve átmeneti várhatók.  

#### <a name="subheading15"></a>– Újrapróbálkozást lehetővé nem tevő hibák
Az ügyfél függvénytárainak mely hibák újrapróbálkozási-, és nem ismerik. Ha saját kódot a storage REST API-t, ne feledje azonban, hogy nem kell újra hibák vannak: például 400 válasz azt jelzi, hogy az ügyfélalkalmazás nem lehetett feldolgozni, mert nem várt formában volt kérés érkezett (hibás kérés). Küldje el újra a kéréssel jár ugyanaz a válasz minden alkalommal, így nincs újrapróbálkozás azt a pont. Ha a storage REST API-t saját kódot ír, vegye figyelembe a hibakódok jelentését és a megfelelő módon, majd ismételje meg a (vagy nem) esetében.  

#### <a name="useful-resources"></a>Hasznos segédanyagok
Tárolási hibakódokkal kapcsolatban további információkért lásd: [állapotát és hibakódok](http://msdn.microsoft.com/library/azure/dd179382.aspx) a Microsoft Azure webhelyen.  

## <a name="blobs"></a>Blobok
A bevált gyakorlat a mellett [minden szolgáltatás](#allservices) korábban leírt, az alábbi eljárások bizonyítása alkalmazás a kifejezetten a blob.  

### <a name="blob-specific-scalability-targets"></a>A BLOB-specifikus méretezhetőségi célok
#### <a name="subheading46"></a>Egy adott objektum eléréséhez egyidejűleg több ügyfél
Ha egy adott objektum egyidejűleg hozzáférő ügyfelek nagy számú szüksége lesz egy objektum és a storage méretezhetőségi célok figyelembe kell venni. Egy adott objektum hozzáférő ügyfelek pontos számát fogja például az az objektum egy időben, kérő ügyfelek az objektum mérete száma tényezőktől függően változnak, hálózati feltételek stb.

Ha az objektum keresztül terjeszthető például képek és videók CDN kiszolgálása egy webhelyről, majd a CDN-t kell használnia. Lásd: [Itt](#subheading5).

Más esetekben, például a bizalmas adatok esetén tudományos szimulációja két választási lehetősége van. Az egyik szakaszosan a számítási hozzáférés úgy, hogy az objektum egy meghatározott időtartam során elért egyidejűleg válaszidővel érhető el. Azt is megteheti ideiglenesen másolhatja az objektum több tárfiókot és növelhetik a teljes IOPS objektumonként és tárfiókok között. A korlátozott tesztelése észleltünk, hogy körülbelül 25 virtuális gépek egyidejűleg tölthető le (a virtuális gépek lett parallelizing 32 szálat használ a letöltési) párhuzamosan 100GB blob. Ha 100 ügyféllel fér hozzá az objektumhoz, először másolja azt egy másik tárolási fiókot, és már rendelkezik az első 50 igénylő virtuális gépek hozzáférési az első blob és a második 50 virtuális gépek hozzáférési a második blob. Eredmények függ az alkalmazások viselkedését, tesztelje a tervezés során. 

#### <a name="subheading16"></a>Sávszélesség és a műveletek másodpercenkénti Blob
Olvassa el, vagy írhat egy blob, legfeljebb 60 MB/s (Ez a körülbelül 480 MB/s, amely meghaladja a kapacitását, számos ügyfél oldalán hálózatok (beleértve az ügyféleszközön a fizikai hálózati adapter). Emellett egyetlen blob támogatja legfeljebb 500 kérések száma másodpercenként. Ha több ügyfelet, olvassa el a azonos blob kell, és előfordulhat, hogy meghaladja ezt a korlátot, érdemes lehet használni a CDN a blob terjesztéséhez.  

A blobok cél átviteli kapcsolatos további információkért lásd: [Azure Storage méretezhetőségi és teljesítménycéloknak](storage-scalability-targets.md).  

### <a name="copying-and-moving-blobs"></a>Másolásának és áthelyezésének Blobok
#### <a name="subheading17"></a>Copy Blob
A storage REST API 2012-02-12-es verzió bevezetett hasznos azon fiókok blobot másolni: ügyfélalkalmazás kérje meg a tároló szolgáltatást a blob másolása egy másik adatforrás (esetleg egy másik tárolási fiókot), és engedélyezze a szolgáltatást a árnyékmásolata aszinkron módon. Ez jelentősen csökkenti a sávszélesség alkalmazásához szükséges, ha telepít adatok más tárfiókok mert letöltéséhez, és töltse fel az adatokat nem szükséges.  

Egy figyelembe azonban, hogy másolás storage-fiókok között, ha nincs idő garancia a példány befejeződik, ha a. Ha az alkalmazásnak egy blob másolási saját felügyeletű gyorsan befejeződik, jobb megoldás lehet a blob másolása letöltése a egy virtuális géphez, majd ismét feltölteni a cél.  A teljes kiszámíthatóságot ebben a helyzetben győződjön meg arról, hogy a másolat végzi el a virtuális gépek azonos Azure-régióban fut, vagy pedig hálózati körülmények lehet (és valószínűleg fog) hatással a másolási teljesítményére.  Ezenkívül programozott módon figyelheti egy aszinkron másolatot előrehaladását.  

Vegye figyelembe, hogy maga tárfiókon belül másolja általában befejeződött gyorsan.  

További információkért lásd: [másolási Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx).  

#### <a name="subheading18"></a>AzCopy használata
Az Azure Storage csapat kiadott egy parancssori eszközt "AzCopy", amely arra szolgál, hogy sok blobokat, valamint a tárfiókok között átvitele tömeges számára.  Ezt az eszközt ebben a forgatókönyvben van optimalizálva, és nagy átviteli sebesség érhető el.  A tömeges feltöltés, a letöltésről és a másolással használata javasolt. További információ, és töltse le, [adatátvitel az AzCopy parancssori segédprogram a](storage-use-azcopy.md).  

#### <a name="subheading19"></a>Az Azure Import/Export szolgáltatás
A rendkívül nagy mennyiségű adatok (legfeljebb 1 TB-os) az Azure Storage kínál az Import/Export szolgáltatás, amely lehetővé teszi a feltöltés és letöltés a blob storage által szállítási merevlemez-meghajtók.  Helyezte el az adatokat a merevlemezen, és elküldi a Microsoftnak az feltöltési, vagy egy üres merevlemezen küldeni a Microsoftnak történő adatletöltéshez.  További információkért lásd: [adatok átviteléhez a Blob Storage a Microsoft Azure Import/Export szolgáltatás használata](../storage-import-export-service.md).  Ez lehet sokkal hatékonyabb, mint feltöltése/letöltése az adatok mennyiségét a hálózaton keresztül.  

### <a name="subheading20"></a>Metaadatok
A blob szolgáltatás támogatja a head kérelmek, amelyek magukban foglalhatják a blob metaadatait. Például ha az alkalmazáshoz szükséges a fénykép EXIF adatokat, akkor sikerült beolvasni a képet, és bontsa ki. Sávszélességet, és a teljesítmény javítása, az alkalmazás sikerült EXIF lévő adatok tárolásához a blob metaadatai, amikor az alkalmazás a fénykép feltöltése: mentése, jelentős sávszélesség és a feldolgozási idő szükséges a EXIF kibontásához egyes adatok idő a blob lekérése a metaadatok csak olyan HEAD EXIF adatok kérése, olvasható is. Akkor hasznos, forgatókönyvekben, ahol csak kell a metaadatokat, és nem blob teljes tartalmát.  Vegye figyelembe, hogy egy blob (a szolgáltatás nem fogad el tároló ennél nagyobb kérelem), a metaadatok csak 8 KB-os is tárolja, így ez a méret nem férnek el az adatokat, ha nem lehet tudni ezt a módszert használja.  

Példa bemutatja, hogyan lekérni egy blob metaadatait .NET használatával, lásd: [Set, a Tulajdonságok beolvasása és a metaadatok](../blobs/storage-properties-metadata.md).  

### <a name="uploading-fast"></a>Gyors feltöltése
A gyors feltöltése a BLOB, az első kérdést megválaszolásához:: Ön egy blob vagy nagy feltöltésével?  Használja az alábbi útmutatást határozza meg a megfelelő módszert a forgatókönyvtől függően.  

#### <a name="subheading21"></a>Egy nagy blob gyors feltöltése
Blobok feltöltése egyetlen nagy gyorsan, az ügyfélalkalmazás kell feltöltése, a blokkok vagy lapok párhuzamos (az egyes blobok és a tárfiók egészére vonatkozó méretezhetőségi célok szem előtt tartva alatt).  Ne feledje, hogy a hivatalos Microsoft által biztosított RTM Storage ügyfélkódtáraival (.NET, Java) ennek a lehetősége.  Az egyes könyvtárak, használja a megadott objektum/tulajdonság egyidejű szintjének beállítása az alábbi:  

* .NET: Set ParallelOperationThreadCount BlobRequestOptions-objektum használható.
* Java/Android: Use BlobRequestOptions.setConcurrentRequestCount()
* NODE.js: ParallelOperationThreadCount használja, vagy a beállításokat, vagy a blob szolgáltatás.
* C++: Blob_request_options::set_parallelism_factor módszert.

#### <a name="subheading22"></a>Sok blobok gyors feltöltése
Sok blobok gyorsan feltölteni, párhuzamos blobok feltöltése. Ez a gyorsabb, mint a feltöltés egyetlen blobok egyszerre a párhuzamos blokk feltöltések mert hogy terjeszti a feltöltés több partíciót a tárolási szolgáltatás között. Egy blob csak 60 MB/s (körülbelül 480 Mbps sebességű) átviteli támogatja. Írásának időpontjában az Egyesült államokbeli LRS-fiók jóval több, mint a támogatott által az egyes blob pedig legfeljebb 20 GB/s-érkező támogatja.  [AzCopy](#subheading18) párhuzamos feltöltések alapértelmezés szerint hajt végre, és ehhez a forgatókönyvhöz ajánlott.  

### <a name="subheading23"></a>A megfelelő típusú blob kiválasztása
Az Azure Storage támogatja a blob kétféle: *lap* blobok és *blokk* blobokat. Adott használati forgatókönyvek esetében a választott blob típushoz hatással lesz a teljesítmény és méretezhetőség a megoldás. Blokkblobok megfelelőek, amikor nagy mennyiségű adat hatékonyan feltölteni kívánt: például ügyfélalkalmazás esetleg fénykép vagy videó feltöltése a blob storage. Lapblobokat megfelelőek, ha az alkalmazás számára szükséges-e a véletlenszerű írások adatain: például Azure virtuális merevlemezek lapblobokat tárolódnak.  

További információkért lásd: [ismertetése Blokkblobokat, hozzáfűző blobokat és Lapblobokat](http://msdn.microsoft.com/library/azure/ee691964.aspx).  

## <a name="tables"></a>Táblák
A bevált gyakorlat a mellett [minden szolgáltatás](#allservices) korábban leírt, az alábbi eljárások bizonyítása alkalmazása kifejezetten a table szolgáltatás.  

### <a name="subheading24"></a>Tábla vonatkozó méretezhetőségi célok
Egy teljes tárfiókot sávszélességgel kapcsolatos korlátozásai mellett kell a következő egyedi méretezhetőségi korlátot.  Vegye figyelembe, hogy a rendszer betölti egyenleg a a forgalom növekedése, de ha a forgalom hirtelen felszakadásáig, nem lehet kérhető le a kötet átviteli azonnal.  Ha a minta felszakadásáig, tekintse meg a sávszélesség-szabályozás és/vagy a tárolóként kapacitásnövelés során időtúllépések szolgáltatás automatikusan terhelés kiegyensúlyozza a tábla el várható.  Jobb eredményeket a rendszer pontos ideje terheléselosztásához megfelelően nyújtja lassan általában ramping rendelkezik.  

#### <a name="entities-per-second-account"></a>Entitások / másodperc (fiók)
A méretezhetőség táblák elérése határa legfeljebb 20 000 entitások (1KB minden) egy második egy olyan fiók.  Általában minden entitás beszúrt, frissítése, törlése, vagy beolvasott számát, ez a cél felé.  Ezért egy 100 entitást tartalmazó kötegelt Beszúrás 100 entitást számít.  Egy lekérdezést, amely 1000 entitások vizsgálja, és visszaadja az 5 1000 entitások számít.  

#### <a name="entities-per-second-partition"></a>Entitások / másodperc (partíció:)
Belül egyetlen partícióra a méretezhetőség eléréséhez táblák célja 2000 entitások (1KB minden) másodpercenként, az azonos leltár segítségével az előző szakaszban leírtak szerint.  

### <a name="configuration"></a>Konfiguráció
Ez a szakasz számos gyors konfigurációs beállítások segítségével végezze el a table szolgáltatás teljesítményének jelentős növelése:  

#### <a name="subheading25"></a>JSON használata
Tárolási verziójú 2013-08-15 kezdve a table szolgáltatás támogatja a JSON helyett az XML-alapú AtomPub formátum tábla adatátvitelre. Ez csökkentheti a tartalom méretét szerint 75 %, és jelentősen növelheti a az alkalmazás teljesítményét.

További információkért lásd: a feladás egy vagy több [Microsoft Azure-táblákat: bevezetéséről JSON](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/05/windows-azure-tables-introducing-json.aspx) és [az adattartalom formátuma Table szolgáltatási műveletek](http://msdn.microsoft.com/library/azure/dn535600.aspx).

#### <a name="subheading26"></a>Nagle kikapcsolása
Nagle tartozó algoritmus széles körben jelentkezhessenek be a hálózati teljesítmény javításához megvalósított TCP/IP-hálózatokon keresztül. Azonban nincs optimalizálva, minden körülmények között (például magas interaktív környezetekben). Az Azure Storage Nagle tartozó algoritmus negatív hatással van a tábla és a queue szolgáltatások kérések teljesítményét, és tiltsa le, ha lehetséges.  

További információkért tekintse meg a következő blogbejegyzésben található [Nagle tartozó algoritmus nem rövid kis kérelmek felé](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx), amely ismerteti, miért Nagle tartozó algoritmus tábla- és várólista kérelmek rosszul kommunikál, és bemutatja, hogyan le kell tiltani az ügyfél-alkalmazásban.  

### <a name="schema"></a>Séma
Hogyan jelöl, és az adatok lekérdezése a legnagyobb egyetlen tényező, amely hatással van a teljesítményre, a table szolgáltatás. Míg minden más, ez a szakasz ismerteti néhány általános bevált gyakorlatokat vonatkoznak:  

* Táblatervezés
* Hatékony lekérdezések
* Hatékony adatfrissítések  

#### <a name="subheading27"></a>Táblák és -partíciók
Táblák partíciókra oszlik meg. Minden entitás partíció tárolt ugyanazzal a partíciókulccsal osztja, és való azonosításának megkönnyítése érdekében, hogy a partíción belül egyedi sorazonosítóként kulccsal rendelkezik. Partíciók előnyt nyújtanak, de méretezhetőségének korlátai is vezethet.  

* Előnyei: Entitásokat is frissítheti a partícióra egyetlen atomi, kötegelt tranzakcióban, amely legfeljebb 100 külön tárolási műveletek (4MB teljes mérete legfeljebb) tartalmazza. Entitások kérhető azonos számú feltételezve, is lekérheti adatok belül egyetlen partícióra (noha mutatunk be további ajánlások tábla adatok lekérdezése) partíciók kiterjedő adatok hatékonyabban.
* A méretezhetőségi korlátot: egyetlen partícióra tárolt entitásokat való hozzáférés nem lehet elosztott terhelésű mert partíciók támogatja a atomi kötegelt tranzakciókat. Emiatt a méretezhetőség egyedi tábla partícióhoz célja alacsonyabb, mint a table szolgáltatás a egész.  

Miatt a következő jellemzőkkel táblák és-partíciók el kell fogadnia az alábbi tervezési alapelvek:  

* Az ügyfélalkalmazás gyakran frissíteni vagy munka logikai egységen belüli lekérdezett adatok partícióra kell elhelyezni.  Erre akkor lehet, mert az alkalmazás van összesítése írási műveleteket, illetve előnyeit atomi kötegműveletek szeretné.  Is egy olyan partíciót adatok hatékonyabban lekérhetők az egyetlen lekérdezés mint adat partíciók között.
* Adatok, amelyek az ügyfélalkalmazás nem beszúrásához vagy frissítéséhez vagy munka (egyetlen lekérdezés vagy kötegelt frissítése) logikai egységen belüli lekérdezés külön partíciók kell elhelyezni.  Egy fontos: az, hogy van egy egyetlen tábla partíciós kulcsok száma nincs korlátozva, több millió partíciós kulcsok esetében nem jelent problémát, és nem lesz hatással a teljesítményre.  Például ha az alkalmazás egy népszerű webhelyen a felhasználói bejelentkezés, a felhasználói azonosítót használja, mint a partíciós kulcs lehet érdemes választani.  

#### <a name="hot-partitions"></a>Működés közbeni partíciók
A működés közbeni partíció egyik le aránytalanul nagy százalékos arányában egy fiókot a forgalmat fogadó, és nem kell az elosztott terhelésű mert egyetlen partíció.  Általában a működés közbeni partíciók létrejönnek két módszer egyikével:  

##### <a name="subheading28"></a>Csak hozzáfűzés és illesztenie csak minták
A "Hozzáfűzése csak" minta egyike ahol a forgalmat egy adott PK összes (vagy szinte minden) növeli, és csökkenti az aktuális idő szerint.  Például akkor, ha az alkalmazás a naplóadatok partíciókulcsként az aktuális dátumot használja.  Ennek eredményeként a beszúrások, melyek mindegyikét utolsó a tábla partíciója, és a rendszer nem lehet terhelést elosztani, mert az írási műveletek mindegyikét tervezi, hogy a tábla végéhez.  Ha adott partíció felé irányuló forgalom mennyisége meghaladja a partíció szintű méretezhetőség célt, majd azt eredményez szabályozási.  Érdemes győződjön meg arról, hogy adatforgalom több partícióhoz, terheléselosztás engedélyezéséhez a kérelmeket a tábla között.  

##### <a name="subheading29"></a>Nagy forgalmú adatok
Ha a particionálási sémát, amely csak az adatokat, mint a többi partíció sokkal használt tartalmaz egyetlen partícióra eredményez, sávszélesség-szabályozás szerint, hogy a partíció megközelíti a méretezhetőség célja egy olyan partíciót is megjelenik.  Érdemes győződjön meg arról, hogy a partícióséma nem majdnem elérte a méretezhetőségi célok egypartíciós eredményez-e.  

#### <a name="querying"></a>Lekérdezése
Ez a szakasz ismerteti a bevált gyakorlat a table szolgáltatás lekérdezése.  

##### <a name="subheading30"></a>Lekérdezés hatóköre
Számos módon adja meg a lekérdezendő entitásokat tartományát.  Minden egyes felhasználási döntéseken a következő:  

Általános elkerülheti a vizsgálatok (lekérdezések nagyobb, mint egyetlen entitás), de kell beolvasni, ha megpróbálja rendszerezi az adatokat, hogy a vizsgálatok beolvasni az adatokat, vagy a felesleges entitások jelentős mennyiségű visszaadó nélkül kell.  

###### <a name="point-queries"></a>Pont lekérdezések
Egy pont lekérdezés lekéri pontosan egy entitást. Ennek érdekében a partíciós kulcs és a beolvasása az entitás sorkulcsa megadása. Ezeket a lekérdezéseket, nagyon hatékony, és ajánlott őket, amikor csak lehetséges.  

###### <a name="partition-queries"></a>Partíció lekérdezések
A partíciólekérdezés osztja meg a közös partíciókulcsot adatok olyan készlete, amely. Általában a lekérdezés határoz meg, a sor kulcsértékei tartománya vagy egy partíciókulcsot mellett néhány entitástulajdonságra értékek tartományán. Ezek pont lekérdezések-nél kevésbé hatékonyak, és takarékosan.  

###### <a name="table-queries"></a>Tábla lekérdezések
A lekérdezés, amely nem ugyanazt a közös partíciókulcsot az entitások készletének, amely. Ezeket a lekérdezéseket nem hatékony, és lehetőség szerint kerülje azokat.  

##### <a name="subheading31"></a>Lekérdezés sűrűség
Egy másik lekérdezés hatékonyságát kulcsfontosságú tényező a visszaadott készletben található beolvasott entitások száma képest visszaadott entitások száma. Ha az alkalmazást, hogy csak 1 %-át az adatokat megosztások, a lekérdezés megvizsgálja összes adja vissza egy entitás 100 entitást tulajdonság értéke szűrőt tartalmazó tábla lekérdezést hajt végre. A tábla méretezhetőségi célok tárgyalt korábban összes kapcsolódnak beolvasott entitások száma, és nem adott vissza entitások száma: lekérdezés kis sűrűségű könnyen okozhat a table szolgáltatás szabályozása az alkalmazás, mert azt kell beolvasni a keresett bejegyzés lekérdezésére igen sok entitás.  A következő szakaszban talál a [denormalization](#subheading34) elkerüléséhez módjáról további információt.  

##### <a name="limiting-the-amount-of-data-returned"></a>A visszaadott adatok mennyisége korlátozása
###### <a name="subheading32"></a>Szűrés
Ha tudja, hogy a lekérdezés entitásokat, nem kell az ügyfélalkalmazás visszatér, érdemes lehet egy szűrőt a visszaadott készlet méretének csökkentése érdekében. Az entitások nem küld válaszként az ügyfél továbbra is a méretezhetőségi korlátok felé száma, amíg az alkalmazás teljesítményének javítja a kisebb hálózati terhelés méretének és entitások is fel kell dolgoznia az ügyfélalkalmazást korlátozott száma miatt.  A Megjegyzés fent látható [lekérdezés sűrűség](#subheading31), azonban – a méretezhetőségi célok számához kapcsolódnak, az entitások vizsgálja, így egy lekérdezést, amely a számos entitás továbbra is eredményezhet sávszélesség-szabályozás, még akkor is, ha néhány entitásokat ad vissza.  

###### <a name="subheading33"></a>Projection
Ha az ügyfélalkalmazást igényel, a tábla az entitások tulajdonságok csak korlátozott számú, használhatja a leképezés a visszaadott adathalmaz méretének korlátozására. Csakúgy, mint a szűrés, ez segít csökkenti a hálózati terhelést és az ügyfél feldolgozása.  

##### <a name="subheading34"></a>Denormalization
Ellentétben a relációs adatbázisok használata a bevált gyakorlat hatékonyan a tábla adatainak lekérdezése előfordulhat, hogy az adatok denormalizing. Ez azt jelenti, hogy ugyanazokat az adatokat több entitás (egy mindegyik kulcs segítségével az adatok megkeresése) duplikálásakor lekérdezés kell beolvasni az adatokat az ügyfél található entitások számának minimalizálása érdekében meg kell, nem pedig kellene nagyszámú entitások található adatok ellenőrzése az alkalmazás van szüksége.  Például az elektronikus kereskedelmi webhely, érdemes lehet egy rendezési mind az ügyfél-azonosító található (, adja meg a megrendelések) és a dátum (engedi rendelések napon).  A Table Storage a legcélszerűbb tárolja az entitás (vagy egy hivatkozást) kétszer – egyszer tábla nevével, PK és RK lehetővé teszi az ügyfél keresése azonosító, egyszer lehetővé teszi a dátum szerint találja.  

#### <a name="insertupdatedelete"></a>Insert/Update/Delete
Ez a szakasz ismerteti a bevált gyakorlat a módosítását a table szolgáltatásban tárolt entitásokat.  

##### <a name="subheading35"></a>Kötegelés
Kötegelt tranzakciókat ismert, entitás csoport tranzakciók (ETG) az Azure Storage; egy ETG belüli összes műveletet egyetlen táblázat egyetlen partícióján kell lennie. Ahol lehetséges, hajtsa végre a beszúrások, a frissítések és törlések kötegekben ETGs segítségével. Ez csökkenti az ügyfélalkalmazás számára a kiszolgáló adatváltások számát, (egy ETG más célra egyetlen tranzakció számít, és tartalmazhat legfeljebb 100 tárolási műveletek) számlázható tranzakciók száma csökkenti, és lehetővé teszi, hogy a atomi frissítések (sikeres minden műveletnél vagy az összes sikertelen egy ETG belül). Például a mobil eszközök nagy késleltetésű környezetek nagy mértékben előnyösek, ETGs használatával.  

##### <a name="subheading36"></a>Upsert
Használjon tábla **Upsert** műveletek lehetőség. Két típusa van **Upsert**, mindkettőnek hatékonyabb, mint egy hagyományos lehet **beszúrása** és **frissítés** műveletek:  

* **InsertOrMerge**: ezt használja, ha szeretne feltölteni az Entitástulajdonságok egy részét, de nem biztos benne, hogy az entitás már létezik. Az entitás létezik, a hívás frissíti a tulajdonságok a **Upsert** műveletet, és hagyja el az összes meglévő tulajdonság szerint, ha az entitás nem létezik, beilleszti az új entitás. Ez hasonlít projekció a lekérdezésben használata abban, hogy csak töltse fel a tulajdonságokat, amelyeket módosítani kell.
* **InsertOrReplace**: használja ezt a feltöltendő egy teljesen új entitás, de azt nem biztos benne, hogy létezik-e. Csak akkor ajánlott ezt, ha tudja, hogy az újonnan feltöltött entitás teljesen helyesen-e, mert a régi entitás teljesen felülírja. Például szeretné frissíteni az entitás, amely tárolja a felhasználó aktuális helye függetlenül-e az alkalmazás korábban találhatók meg a helyadatok a felhasználó; az új hely entitás befejeződött, és nincs szükség az adatok minden korábbi entitás.

##### <a name="subheading37"></a>Az Adatsorozatban tárolja egyetlen entitás
Egyes esetekben az alkalmazás tárolja a gyakran és egyszerre beolvasandó szükséges adatok több: például egy alkalmazás előfordulhat, hogy nyomon CPU-használat idővel ahhoz, hogy az adatok az elmúlt 24 órában a működés közbeni diagram megrajzolásához. Minden entitás egy adott órát jelölő és a CPU-használat tárolása, hogy egy óra, óránként egy tábla entitás egyik módszer. Ezeket az adatokat ábrázol, az alkalmazást kell beolvasni az adatokat a legutóbbi 24 órát entitásokat.  

Azt is megteheti, az alkalmazás sikerült tárolni a CPU-használat minden órában egyetlen entitás külön tulajdonságként: minden órában frissítéséhez az alkalmazás használhat egyetlen **InsertOrMerge Upsert** hívása frissítse az értéket a legutóbbi egy óra. Az adatok ábrázolására, az alkalmazást csak kell 24, így egy nagyon hatékony lekérdezés helyett egyetlen entitás lekérdezése (lásd fentebb vitafórum a [hatókör lekérdezése](#subheading30)).

##### <a name="subheading38"></a>A blobok strukturált adatok tárolására
Egyes esetekben strukturált adatok érzi, például táblázatokban, el kell, de az entitások tartományok mindig olvassa együtt, és kötegelt szúrható be.  Egy jó példa erre, egy naplófájlt.  Ebben az esetben a batch-naplók néhány percig, tegye őket, és majd mindig keres, valamint egyszerre több percnyi.  Ebben az esetben a teljesítmény érdekében érdemes használni blobok táblák, helyett, mivel jelentősen csökkentheti a visszaadott objektumok száma írt /, valamint általában a kérelmek száma, amelyeket.  

## <a name="queues"></a>Üzenetsorok
A bevált gyakorlat a mellett [minden szolgáltatás](#allservices) korábban leírt, az alábbi eljárások bizonyítása alkalmazása kifejezetten a queue szolgáltatás.  

### <a name="subheading39"></a>Méretezhetőségi korlátok
Egy adott sorba körülbelül 2000 üzenetek (1KB minden) (egyes AddMessage GetMessage és DeleteMessage száma itt üzenetként) másodpercenként feldolgozásához. Ha ez nem elegendő az alkalmazáshoz, több üzenetsorok használata, és az üzenetek elosztva.  

A jelenlegi méretezhetőségi célok megtekintése [Azure Storage méretezhetőségi és teljesítménycéloknak](storage-scalability-targets.md).  

### <a name="subheading40"></a>Nagle kikapcsolása
Című témakör ismerteti a Nagle algoritmus tábla-konfigurációban – a Nagle algoritmus általában rossz várólista kérések teljesítéséhez, és tiltsa le azt.  

### <a name="subheading41"></a>Üzenet mérete
Várólista teljesítményének és méretezhetőségének csökken a üzenet méretének növekedése. Az üzenet csak a fogadó szükséges információkat kell elhelyezni.  

### <a name="subheading42"></a>Kötegelt lekérése
A várólista egyetlen művelettel legfeljebb 32 üzenetek kérhetnek le. Ez csökkentheti adatcsere gyakoriságát az ügyfélalkalmazás, ami különösen hasznos az olyan környezetekben, például a mobil eszközök, és nagy mértékű.  

### <a name="subheading43"></a>Várólista lekérdezési időköz
A legtöbb alkalmazás kérdezze le az üzenetek várólistából való várólistából, amely a legnagyobb forrásokból tranzakciók az alkalmazás lehet. Válassza ki a lekérdezési időköz georeplikációt: lekérdezés túl gyakran okozhat az alkalmazás a méretezhetőségi célok a várólista megközelítési. Azonban 200 000 tranzakciók $0,01 (a írásának időpontjában), a lekérdezési követően egy hónapig másodpercenként volna költség költségeket, így kevesebb mint 15 cent egyprocesszoros nincs általában egy tényező, amely befolyásolja a kiválasztott lekérdezési időközt.  

Naprakész költség információkért lásd: [Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/).  

### <a name="subheading44"></a>UpdateMessage
Használhat **UpdateMessage** növelje a láthatatlansági időkorlátot, vagy módosíthatja az állapotadatokat, egy üzenet. Ez nem hatékony, ne feledje, hogy minden egyes **UpdateMessage** művelet módszert a méretezhetőség cél felé számolnak. Azonban ez nem egy sokkal hatékonyabb módszert alkalmaz, mint hogy olyan munkafolyamatot, amely egy üzenetsorból átadása egy feladat a következő befejezését követően a feladatok egyes lépéseinek. Használja a **UpdateMessage** művelet lehetővé teszi, hogy az alkalmazás számára feladatállapotot mentse az üzenethez, és folytathatják a munkát, az üzenet a következő lépés a feladat újbóli queuing, minden alkalommal, amikor egy lépés befejezése helyett.  

További információkért lásd: a cikk [hogyan: aszinkron üzenet tartalmának módosítása](../queues/storage-dotnet-how-to-use-queues.md#change-the-contents-of-a-queued-message).  

### <a name="subheading45"></a>Alkalmazás-architektúra
Várólisták segítségével ellenőrizze az alkalmazásarchitektúrát is méretezhető. A következő módokon várólisták segítségével ellenőrizze az alkalmazás több méretezhető sorolja fel:  

* Munka a feldolgozás várakozási sorok létrehozásához, és a munkaterhelések simítja az alkalmazásban várólisták segítségével. Például akkor volt sorba felhasználók által érkező kérések feltöltött képek átméretezése például a processzor intenzív feladatok végrehajtására.
* Várólisták segítségével használata leválasztja az alkalmazás részei, így függetlenül méretezheti őket. Például egy előtér-webkiszolgáló sikerült helyezzen el felméréseket, a felhasználók egy várólista újabb elemzés és tárolására. Hozzáadhatja a feldolgozni a várólista adatokat szükség szerint további munkavégző szerepkörpéldányokat.  

## <a name="conclusion"></a>Összegzés
Ez a cikk ismertet néhány leggyakoribb, bizonyítása eljárások az Azure Storage használata esetén a teljesítmény optimalizálása. Arra bátorítunk minden alkalmazásfejlesztőt, hogy alkalmazásaikat a fenti gyakorlatok alapján értékeljék újra, és fontolják meg, hogy érdemes-e az ajánlások szerint eljárniuk az Azure Storage-t használó alkalmazásaik teljesítményének fokozása érdekében.