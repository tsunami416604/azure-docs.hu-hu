---
title: Az Azure Storage teljesítmény és méretezhetőség – ellenőrzőlista |} A Microsoft Docs
description: Ellenőrzőlista használható az Azure Storage nagy teljesítményű alkalmazások fejlesztése a bevált eljárásokat.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 12/08/2016
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: b8451a1195ab64d3cd7afda074d786a3209ce785
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58793968"
---
# <a name="microsoft-azure-storage-performance-and-scalability-checklist"></a>A Microsoft Azure Storage teljesítmény- és méretezhetőségi ellenőrzőlistája
## <a name="overview"></a>Áttekintés
A Microsoft Azure Storage szolgáltatások kiadása óta a Microsoft bevált módszereket, ezek a szolgáltatások olyan nagy teljesítményű módon számos fejlesztett ki, és ez a cikk ezek közül a legfontosabb kialakíthattunk ellenőrzőlista stílusú listáját szolgál. Ez a cikk a szándéka érdekében ellenőrizze a bevált gyakorlatokat használják az Azure Storage segítségével az alkalmazásfejlesztők és és egyéb bevált gyakorlatokat, akkor érdemes bevezetése jelzi. Ez a cikk nem kísérli meg, hogy biztosítsák a minden lehetséges teljesítményének és méretezhetőségének optimalizálás –, kivéve azokat, amelyek a hatásuk a kis- és üzemelő megoldásain alkalmazható. Az az alkalmazás viselkedésében előrejelezhető a tervezés során, hasznos már a legelején a terveket, amelyeket fog futni, a teljesítménybeli problémák elkerülése érdekében szem előtt kell tartani.  

Minden alkalmazás fejlesztői Azure Storage használatával Ez a cikk ismerteti, és ellenőrizze, hogy saját alkalmazást követi az alábbi bevált gyakorlatokat mindegyike időt vesz igénybe.  

## <a name="checklist"></a>Ellenőrzőlista
Ez a cikk a bevált eljárásokat az alábbi csoportokba rendezik. Bevált gyakorlatok a alkalmazni:  

* Minden Azure Storage szolgáltatás (blobok, táblák, üzenetsorok és fájlok)
* Blobok
* Táblák
* Üzenetsorok  

| Kész | Terület | Kategória | Kérdés |
| --- | --- | --- | --- |
| &nbsp; | Az összes szolgáltatás |Skálázási célértékei |[Az alkalmazás célja elkerülése érdekében hamarosan eléri a skálázási célértékei?](#subheading1) |
| &nbsp; | Az összes szolgáltatás |Skálázási célértékei |[Az elnevezési konvenciókba lehetővé teszi, hogy jobban terheléselosztás?](#subheading47) |
| &nbsp; | Az összes szolgáltatás |Hálózat |[Rendelkeznek-e a kiszolgálóoldali ügyféleszközök, elég nagy sávszélességű és kis késése, a szükséges teljesítmény eléréséhez?](#subheading2) |
| &nbsp; | Az összes szolgáltatás |Hálózat |[Ügyfél ügyféloldali eszközök rendelkeznek elég magas minőségű hivatkozást?](#subheading3) |
| &nbsp; | Az összes szolgáltatás |Hálózat |["Közelében" a tárfiók található az ügyfélalkalmazás?](#subheading4) |
| &nbsp; | Az összes szolgáltatás |Tartalomterjesztés |[Használ a tartalom terjesztése egy CDN-t?](#subheading5) |
| &nbsp; | Az összes szolgáltatás |A közvetlen ügyfelek hozzáférése |[Használatával és a CORS engedélyezése helyett proxy tárhely eléréséhez közvetlen hozzáférést?](#subheading6) |
| &nbsp; | Az összes szolgáltatás |Gyorsítótárazás |[Az alkalmazás ismételten használt gyorsítótárazási adatokat és módosítások ritkán van?](#subheading7) |
| &nbsp; | Az összes szolgáltatás |Gyorsítótárazás |[Van az alkalmazás (ügyféloldali gyorsítótárazás őket, és ezután töltse fel a nagyobb készleteket) kötegelésére?](#subheading8) |
| &nbsp; | Az összes szolgáltatás |.NET Configuration |[Konfigurálta az ügyfél számára elegendő az egyidejű kapcsolatok használatát?](#subheading9) |
| &nbsp; | Az összes szolgáltatás |.NET Configuration |[Konfigurálta már a .NET használata szálak elegendő számú?](#subheading10) |
| &nbsp; | Az összes szolgáltatás |.NET Configuration |[Használ .NET 4.5-ös vagy újabb, amely javult a szemétgyűjtés?](#subheading11) |
| &nbsp; | Az összes szolgáltatás |Párhuzamosság |[Ön gondoskodott róla, hogy párhuzamosságot korlátozódik megfelelően, hogy ne túlterhelni, vagy a képességei, vagy a skálázási célértékei?](#subheading12) |
| &nbsp; | Az összes szolgáltatás |Eszközök |[Vannak a Microsoft legújabb verzióját használja a megadott ügyfél-könyvtárak és eszközök?](#subheading13) |
| &nbsp; | Az összes szolgáltatás |Újrapróbálkozások |[Azok az Ön egy exponenciális visszatartással ismételje meg a szabályzat szabályozási hibákat és időtúllépéseket?](#subheading14) |
| &nbsp; | Az összes szolgáltatás |Újrapróbálkozások |[Az a alkalmazás legpontosabb újrapróbálkozások – Újrapróbálkozást lehetővé nem tevő hibák esetén?](#subheading15) |
| &nbsp; | Blobok |Skálázási célértékei |[Egyetlen objektum egyszerre hozzáférő ügyfelek nagy számú van?](#subheading46) |
| &nbsp; | Blobok |Skálázási célértékei |[Az alkalmazás egy blob sávszélesség vagy a műveletek méretezhetőségi célértékét belül tartózkodik?](#subheading16) |
| &nbsp; | Blobok |Blobok másolása |[Másolási blobok azok hatékony módon?](#subheading17) |
| &nbsp; | Blobok |Blobok másolása |[Használja az AzCopy tömeges másolási a blobok?](#subheading18) |
| &nbsp; | Blobok |Blobok másolása |[Használ nagy mennyiségű adat átvitele Azure Import/Export?](#subheading19) |
| &nbsp; | Blobok |Metaadatok |[Tárolja a gyakran használt metaadatait a BLOB metaadatainak?](#subheading20) |
| &nbsp; | Blobok |Gyors feltöltése |[Gyorsan egy blob feltöltése közben, a feltöltendő párhuzamosan blokkok?](#subheading21) |
| &nbsp; | Blobok |Gyors feltöltése |[Feltöltése közben számos blobok gyorsan, akkor tölt fel párhuzamosan a blobok?](#subheading22) |
| &nbsp; | Blobok |Javítsa ki a Blob típusa |[Használja a lapblobok vagy adott esetben a blokkblobok használatát támogatják?](#subheading23) |
| &nbsp; | Táblák |Skálázási célértékei |[Közeledik másodpercenként entitások skálázási célértékei?](#subheading24) |
| &nbsp; | Táblák |Konfiguráció |[Használ-e a tábla kérelmek JSON?](#subheading25) |
| &nbsp; | Táblák |Konfiguráció |[Hogy ki van kapcsolva a Nagle kis méretű kérések teljesítményét?](#subheading26) |
| &nbsp; | Táblák |Táblák és partíciók |[Rendelkezik, megfelelően particionálni az adatokat?](#subheading27) |
| &nbsp; | Táblák |Forró partíciók |[Minták fűzi, és csak illesztenie elkerülésére?](#subheading28) |
| &nbsp; | Táblák |Forró partíciók |[Vannak a beszúrások és frissítések elosztva sok partíció?](#subheading29) |
| &nbsp; | Táblák |Lekérdezés hatókörébe |[Kell megtervezni a sémát, úgy, hogy a legtöbb esetben használható pont lekérdezések, és a táblázat a lekérdezések takarékosan?](#subheading30) |
| &nbsp; | Táblák |Lekérdezés sűrűség |[Hajtsa végre a lekérdezéseket általában csak vizsgálat, és adja vissza, amely az alkalmazás fogja használni sorok?](#subheading31) |
| &nbsp; | Táblák |Korlátozó visszaadott adatok |[Használ szűrése nem szükséges entitások visszaadó elkerülése érdekében?](#subheading32) |
| &nbsp; | Táblák |Korlátozó visszaadott adatok |[Használ leképezése nem szükséges tulajdonságok visszaadó elkerülése érdekében?](#subheading33) |
| &nbsp; | Táblák |Denormalizáció |[Rendelkezik denormalizált adatait úgy, hogy ne hatékony lekérdezések vagy több olvasási kérelmek adatainak beolvasásakor?](#subheading34) |
| &nbsp; | Táblák |INSERT/frissítése/törlése |[Azok az Ön kötegelés adott kell lennie a tranzakciós kérelmek, vagy csökkenteni az üzenetváltások egyidejűleg teheti meg?](#subheading35) |
| &nbsp; | Táblák |INSERT/frissítése/törlése |[Vannak, elkerülve a csak az entitások határozza meg, hogy a hívás, insert nebo update beolvasása?](#subheading36) |
| &nbsp; | Táblák |INSERT/frissítése/törlése |[Tekintette tárolása gyakran letöltött adatok sorozatát együtt egyetlen entitás tulajdonságokként több entitás helyett?](#subheading37) |
| &nbsp; | Táblák |INSERT/frissítése/törlése |[Mindig lekéri együtt, és kötegekben (például idősorozat-adatok) írható entitások rendelkezik meg minősül blobs használata helyett a táblák?](#subheading38) |
| &nbsp; | Üzenetsorok |Skálázási célértékei |[Közeledik üzenetek / másodperc skálázási célértékei?](#subheading39) |
| &nbsp; | Üzenetsorok |Konfiguráció |[Hogy ki van kapcsolva a Nagle kis méretű kérések teljesítményét?](#subheading40) |
| &nbsp; | Üzenetsorok |Üzenet mérete |[Azok az üzenetek tömörítése a várólista teljesítményének javítása érdekében?](#subheading41) |
| &nbsp; | Üzenetsorok |Tömeges beolvasása |[Több üzenetet egy "Get" művelettel beolvasása?](#subheading42) |
| &nbsp; | Üzenetsorok |Lekérdezési gyakorisága |[Vannak, lekérdezési elég gyakran az alkalmazás észlelhető késleltetés csökkentése érdekében?](#subheading43) |
| &nbsp; | Üzenetsorok |Üzenet frissítése |[Használ UpdateMessage tárolja a folyamat az üzenetek feldolgozásának elkerülése, nem kell újból feldolgozza a teljes üzenetet, ha hiba történik?](#subheading44) |
| &nbsp; | Üzenetsorok |Architektúra |[Használ a teljes alkalmazás jobban skálázható hosszú ideig futó számítási feladatokat a kritikus útvonalat kívül tartja, és független méretezését, majd üzenetsorok?](#subheading45) |

## <a name="allservices"></a>All Services
Ez a szakasz ismerteti az ajánlott eljárások az Azure Storage szolgáltatás (blobok, táblák, üzenetsorok vagy fájlok) bármelyikének a használatára érvényesek.  

### <a name="subheading1"></a>Skálázási célértékei
Maga az Azure Storage régiónként és előfizetésenként legfeljebb 250 tárfiókot tartalmaz. Ha eléri a korlátot, nem hozható létre minden olyan további tárfiókok, hogy előfizetés-régió kombináció fogjuk.

Az Azure Storage szolgáltatás mindegyike rendelkezik kapacitás (GB), a tranzakciós sebesség és a sávszélesség skálázhatósági célokat. Ha az alkalmazás mérete megközelíti vagy meghaladja a skálázási célértékei bármelyikét, tapasztalhat tranzakciók késése vagy szabályozás. Amikor a Storage szolgáltatás szabályozza az alkalmazását, a szolgáltatás elkezdi ad vissza "503-as kiszolgáló foglalt" vagy "500 művelet időtúllépése" hibakódok bizonyos tárolási tranzakciók. Ez a szakasz ismerteti mind az általános megközelítés a skálázhatósági célokat és a sávszélesség teljesítménycélokat különösen foglalkoznak. Amelyek az egyes tárolási szolgáltatások későbbi részekben bemutatjuk a skálázhatósági célokat, hogy adott szolgáltatási összefüggésében:  

* [BLOB sávszélesség és a kérések másodpercenként](#subheading16)
* [Táblaentitások száma másodpercenként](#subheading24)
* [Üzenetsorbeli üzenetek / másodperc](#subheading39)  

#### <a name="sub1bandwidth"></a>A sávszélesség méretezhetőségi Célértékét szolgáltatásokhoz
Írása idején a sávszélesség célokat, az Egyesült Államokban, a georedundáns tárolás (GRS) fiók 10 Gigabit / másodperc (Gbps) a bejövő (a storage-fiókjába küldött adatok) és a 20 GB/s kimenő (a storage-fiók által küldött adatokat). Helyileg redundáns tárolás (LRS) fiók esetén a korlátok a következők magasabb – 20 GB/s a bejövő és kimenő 30 GB/s.  Nemzetközi sávszélességkorlátok lehet alacsonyabb, és találhatók a [méretezhetőségi célok lap](https://msdn.microsoft.com/library/azure/dn249410.aspx).  A storage redundanciabeállításai további információkért tekintse meg a hivatkozásokat az alábbi hasznos forrásokat.  

#### <a name="what-to-do-when-approaching-a-scalability-target"></a>Mi a teendő, ha hamarosan eléri a skálázhatósági célok
Ha Ön hamarosan eléri a storage-fiókokat használhat egy adott előfizetés/régió kombinációja túllépi, az alkalmazás- és tárfiókjai használatának értékelése, és határozza meg, ha bármely feltétel a alkalmazni.

* Storage-fiókot használni a nem felügyelt lemezként, és ezeket a lemezeket ad hozzá a virtuális gépek. Ebben a forgatókönyvben javasoljuk [felügyelt lemezek](../../virtual-machines/windows/managed-disks-overview.md), ahogy a tárolási lemezskálázhatóság, anélkül, hogy egyes storage-fiókok létrehozásához és kezeléséhez kellene adni.
* Egy tárfiók használatával egy ügyfél alapon, az adatok elkülönítését céljából. Ebben a forgatókönyvben javasoljuk egy teljes tárfiókot, hanem minden egyes ügyfél storage-tárolók használatával. Az Azure Storage most már lehetővé teszi a szerepköralapú hozzáférés-vezérlés egy kiszolgálónként [tároló alapjául](storage-auth-aad-rbac-portal.md).
* Több tárfiók használata szegmensre bejövő/kimenő forgalom/iops/kapacitás nagyobb fokú méretezhetőségét. Ebben a forgatókönyvben, ha lehetséges, javasoljuk, hogy előnyeit a [növelni a korlátozások](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/) a standard szintű tárfiókok a storage-fiókok szükséges a számítási feladatok számának csökkentése érdekében.

Ha az alkalmazás már majdnem elérte az egy tárfiókban skálázási célértékei, vegye figyelembe, vezetnek be a következő módszerek egyikét:  

* Gondolja át az alkalmazás megközelítik vagy meghaladják a skálázhatósági célok számítási feladatokra. Megtervezheti, hogy másképp kisebb sávszélességet vagy kapacitását, vagy kevesebb tranzakciók?
* Ha egy alkalmazás túl kell lépnie az egyik a skálázhatósági célokat, létre kell hoznia storage-fiókok és a partíció az alkalmazásadatok ezeket több tárfiókon keresztül. Ha ezt a mintát használja, majd mindenképpen tervezze alkalmazását úgy, hogy további tárfiókok a jövőben terheléselosztásra is hozzáadhat. Írásának időpontjában a minden Azure-előfizetés storage-fiókok legfeljebb 100 lehet.  Storage-fiókok is kívül tárolt adatokat, a végrehajtott tranzakciók és az átvitt adatok tekintetében a használat ingyenes.
* Ha az alkalmazás eléri a sávszélesség-célokat, fontolja meg az ügyfél elküldi az adatokat a storage szolgáltatás szükséges sávszélesség csökkentése érdekében az adatok tömörítése.  Bár ez sávszélességet takaríthat, és hálózati teljesítmény javításához, azt is lehet néhány negatív hatással van.  Ki kell értékelni a teljesítményre gyakorolt hatás erre a további feldolgozás tömörítése és kibontása adatokat az ügyfél követelményei miatt. Emellett a tömörített adatok tárolására több nehézkessé teheti a hibák elhárításához, mivel lehet bonyolultabb standard eszközökkel tárolt adatok megtekintéséhez.
* Ha az alkalmazás eléri a skálázhatósági célokat, majd győződjön meg arról, hogy újrapróbálkozások használ egy exponenciális visszatartással (lásd: [újrapróbálkozások](#subheading14)).  Győződjön meg arról, soha nem módszerrel érdemes a skálázhatósági célértékét (amelyet a fenti módszerek egyikének használatával), de ez biztosítja az alkalmazás nem csupán továbbra is újrapróbálkozik gyorsan, ami a szabályozás rosszabb, akkor célszerűbb.  

#### <a name="useful-resources"></a>Hasznos segédanyagok
Az alábbi hivatkozások további részleteket a skálázhatósági célokat adjon meg:

* Lásd: [Azure Storage méretezhetőségi és Teljesítménycéljai](storage-scalability-targets.md) teljesítménycélokat kapcsolatos információkat.
* Lásd: [Azure Storage replikáció](storage-redundancy.md) és a blogbejegyzést [Azure Storage Redundanciabeállításainál és olvasási-Access Geo Redundant Storage](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx) storage redundanciabeállításai kapcsolatos információkat.
* Az Azure-szolgáltatásokhoz díjszabással kapcsolatos aktuális információkért lásd: [az Azure díjszabása](https://azure.microsoft.com/pricing/overview/).  

### <a name="subheading47"></a>Partícióelnevezési konvenciót
Az Azure Storage egy tartományalapú particionálási sémát, és méretezés és a terhelés elosztása a rendszer használ. A partíciókulcs partíció tartományok és a tartományok adatok betöltése-között oszlanak el a rendszer segítségével. Ez azt jelenti, hogy elnevezési konvenciók például lexikai rendezés (például msftpayroll, msftperformance, msftemployees, stb.), vagy időbélyegző (log20160101, log20160102, log20160102, stb.) használatával lesz a verziókövetésre folyamatban potenciálisan védelmicsoport-készletek a partíciók a azonos partíciókiszolgáló, amíg egy terheléselosztási művelet felosztja a őket ki kisebb tartományt. Például tárolóban lévő összes BLOB kiszolgálható egyetlen kiszolgáló, mindaddig, amíg a terhelés, az ilyen blobok igényel, további újraegyensúlyozása a partíció-tartományokat. Ehhez hasonlóan kevésbé terhelt fiókokat a nevük lexikai sorrendbe rendezve csoportja kiszolgálása az internetszolgáltatójuk egyetlen kiszolgáló csak az egyik terhelését, vagy ezek a fiókok mindegyikét hitelesíteni feloszthatók több partíció-kiszolgáló. Minden terheléselosztási művelet közben a művelet hatással lehet a storage hívások késése. Mindaddig, amíg a terheléselosztási művelet megrúgni a és a partíciókulcs-tartományok rebalances korlátozza egy egypartíciós kiszolgáló méretezhetőségét partíciók adatforgalom hirtelen kezeli a rendszer riasztásészlelési képességére.  

Az ilyen műveletek gyakoriságának csökkentése érdekében néhány ajánlott eljárást is követheti.  

* Vizsgálja meg az elnevezési szabályok tartozó fiókokat, tárolók, blobok, táblák és üzenetsorok, szorosan. Vegye figyelembe, hogy egy kivonatoló függvénnyel az igényeinek leginkább megfelelő 3 számjegyből kivonattal fiókneveket előtag.  
* Ha, ahogy az időbélyegek vagy numerikus azonosítók használata esetén az adatok, akkor győződjön meg arról, nem használ egy csak hozzáfűzéssel bővíthető (vagy csak illesztenie) forgalmi minták. Ezek a minták nem alkalmasak a tartomány-alapú particionálási rendszert, és sikerült érdeklődőt fogja egy partíció, és hatékonyan korlátozza a rendszer az összes forgalom a terheléselosztás. Például ha napi műveletek, például ÉÉÉÉHHNN időbélyegzővel ellátott blob objektumot használó, majd az a napi művelet összes forgalom irányul, egyetlen objektumot, amely egy partíció-kiszolgáló által kiszolgált. Keresse meg, hogy a blob korlátokat partíciónként korlátok az igényeknek, és érdemes feldarabolni Ez a művelet több blobokba, ha szükséges. Ehhez hasonlóan ha idősorozat-adatok tárolása a táblák, mind a forgalom irányítja az utolsó kulcs névtér a része. Időbélyegeket vagy numerikus azonosítót kell használnia, ha előtag azonosítója 3 számjegyből kivonattal, illetve időbélyegeket előtag ssyyyymmdd például az idő a másodperc részét. Ha listázás és lekérdezés műveleteket rendszeresen hajtja végre, válassza ki a kivonatoló függvényt, amely korlátozza a lekérdezések száma. Egyéb esetben elegendő lehet egy véletlenszerű előtagot.  
* A további információk a particionálási sémát, használja az Azure Storage, olvassa el a SOSP tanulmány [Itt](https://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf).

### <a name="networking"></a>Hálózat
Függetlenül attól, hogy az API-t hív meg, amíg gyakran a fizikai hálózati korlátok az alkalmazás egy jelentős hatással lehet a teljesítmény. Az alábbiakban ismertetett egyes felhasználók You may encounter korlátozások.  

#### <a name="client-network-capability"></a>Ügyfél hálózati képesség
##### <a name="subheading2"></a>Átviteli sebesség
A sávszélességgel, a probléma, gyakran az ügyfél képességeit. Például az egy tárfiókban képes kezelni, 10 GB/s, vagy több bejövő közben (lásd: [sávszélesség teljesítménycélokat](#sub1bandwidth)), a hálózat sebességétől, a "Kis" Azure feldolgozói szerepkörpéldányok csak akkor képes a körülbelül 100 MB/s. Nagyobb az Azure-példányok a hálózati adapterek nagyobb kapacitással rendelkeznek, így érdemes használni egy nagyobb példány, vagy további virtuális gépeket, ha nagyobb hálózati korlátok egyetlen gépről van szüksége. A Storage szolgáltatás a helyi alkalmazásból való hozzáférés, akkor ugyanaz a szabály vonatkozik: a hálózati képességekről az ügyféleszköz- és a hálózati kapcsolatot az Azure Storage-helyre, és akár-javítása őket igény szerint vagy tervezési a az alkalmazás működéséhez képességeikkel belül.  

##### <a name="subheading3"></a>Hivatkozás minősége
Csakúgy, mint bármely hálózati használatot, vegye figyelembe, hogy hálózati körülmények hibák és a csomagveszteség lassú-e a tényleges átviteli sebesség.  A WireShark vagy NetMon segíthet a probléma diagnosztizálásakor.  

##### <a name="useful-resources"></a>Hasznos segédanyagok
További információ a virtuálisgép-méretek és a hozzárendelt sávszélesség: [Windows Virtuálisgép-méretek](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) vagy [Linux Virtuálisgép-méretek](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

#### <a name="subheading4"></a>hely
Minden elosztott környezetben az ügyfél a kiszolgáló közel helyezi tesz lehetővé az a legjobb teljesítmény érdekében. A legkisebb késéssel rendelkező Azure Storage eléréséhez, a legjobb hely az ügyfél azonos Azure-régióban van. Például ha rendelkezik Azure-webhelyek által használt Azure Storage, meg kell keresse meg azokat is (például az USA nyugati régiója vagy és délkeleti régiója) egy adott régión belül. Ez csökkenti a késést és a költségek – írása idején ingyenes a sávszélesség-használat egy adott régión belül.  

Ha az alkalmazások nem üzemeltetett Azure-ban (például a mobileszköz-alkalmazások vagy a helyi vállalati szolgáltatásokhoz), majd újra ügyfél függvényalkalmazáshoz az eszközöket, akik hozzáférhetnek, egy régióban helyezi el a tárfiók általánosan csökkenti a késés. Ha az ügyfelek széles körben oszlanak meg (a példa néhány Észak-Amerikában és Európában néhány), akkor érdemes használni, több tárfiókot: egyet az Észak-amerikai régió és a egy Európai régióban található. Ez segít a mindkét régióban lévő felhasználók számára a késés csökkentése érdekében. Ez a megközelítés könnyebben akkor megvalósításához, ha az alkalmazás adattárak jellemző egyéni felhasználók számára, és nem igényli a storage-fiókok között replikálja az adatokat.  A széles körű tartalomterjesztés CDN ajánlott – további részleteket a következő szakaszban talál.  

### <a name="subheading5"></a>Tartalom terjesztése
Egyes esetekben egy alkalmazásnak kell azonos, vagy több régióban található (például egy terméket bemutató videója webhely kezdőlapján használt), a több felhasználó ugyanazt a tartalmat. Ebben a forgatókönyvben egy Content Delivery Network (CDN) például az Azure CDN-t kell használnia, és a CDN-t használja az Azure storage, az adatok forrása. Egy Azure Storage-fiókot, amely egy adott régióban létezik, és az alacsony késésű, más régiókban, amely nem lehet kézbesíteni tartalmat, eltérően az Azure CDN-kiszolgálót használ szerte a világon több adatközpontban. Emellett CDN általában sokkal nagyobb kimenő rendelkeznek, mint az egy tárfiókban támogat.  

Az Azure CDN kapcsolatos további információkért lásd: [Azure CDN](https://azure.microsoft.com/services/cdn/).  

### <a name="subheading6"></a>SAS és a CORS használatával
Engedélyezze a felhasználó webböngészőt vagy az Azure Storage-adatok elérését a mobiltelefonján lévő alkalmazás például a JavaScript kódja van szüksége, amikor egyik módszere az alkalmazás használatát a webes szerepkör-proxyként: a felhasználó eszközén végzi a hitelesítést a webes szerepkör , amely ezután engedélyezi a hozzáférést a tárolási erőforrások. Így elkerülheti a is közzéteheti a tárfiókkulcsok nem biztonságos eszközökön. Azonban ez helyez el egy jelentős többletterhelést a webes szerepkör, mert a felhasználó-eszköz és a storage szolgáltatás között továbbított összes adat kell haladnia a webes szerepkör. Elkerülheti a webes szerepkör-proxyként a storage szolgáltatás közös hozzáférésű Jogosultságkódok (SAS), egyes esetekben a fejlécek eltérő eredetű erőforrások megosztása (CORS) együtt. Az SAS segítségével lehetővé teheti a felhasználó eszközét, hogy kéréseket közvetlenül a storage szolgáltatás egy korlátozott hozzáférési jogkivonat segítségével. Például ha a felhasználó használni szeretne az alkalmazáshoz egy fénykép feltöltése, a webes szerepkör is készítése és küldeni a felhasználó eszközén, amely engedélyt ad egy adott blobon vagy tárolón írni a következő 30 percig (amely után a SAS-jogkivonat lejár) SAS-jogkivonatát.

Normál esetben egy böngészőben nem engedélyezi a JavaScript által egy webhely, például egy "PUT" egy másik tartományba meghatározott műveletek elvégzéséhez egy tartományban lévő üzemeltetett lapon. Ha tartalmaz: "contosomarketing.cloudapp.net" webes szerepkört, és feltölt egy blobot a storage-fiók "contosoproducts.blob.core.windows.net", az ügyféloldali JavaScript használatával, például a böngészők "azonos szabályzat" megtiltsa fogja ezt a műveletet. A CORS böngésző funkciója lehetővé teszi, hogy a céltartományban (ebben az esetben a storage-fiók) való kommunikációhoz a böngészőnek, hogy a forrástartomány (ebben az esetben a webes szerepkör) feladatokból származó kérelmek megbízik.  

Mindkét technológiát segítségével elkerülhető a felesleges terhelést (és a szűk keresztmetszeteket) a webalkalmazásában.  

#### <a name="useful-resources"></a>Hasznos segédanyagok
További információ a SAS: [közös hozzáférési aláírások, 1. rész: A SAS-modell ismertetése](../storage-dotnet-shared-access-signature-part-1.md).  

A CORS kapcsolatos további információkért lásd: [eltérő eredetű erőforrások megosztása (CORS) támogatása az Azure Storage szolgáltatásainak](https://msdn.microsoft.com/library/azure/dn535601.aspx).  

### <a name="caching"></a>Gyorsítótárazás
#### <a name="subheading7"></a>Adatok beolvasása
Adatok beolvasása a szolgáltatástól egyszer általában jobb, mint az első, kétszer. Vegyünk például egy 50 MB-os blobot már beolvasta a storage szolgáltatásból szolgálhat a tartalmat egy felhasználó egy webes szerepkörben futó MVC webalkalmazás. Az alkalmazás ezután beolvasása ugyanennek a blobnak minden alkalommal, amikor egy felhasználó azt kéri, illetve akkor gyorsítótárazhatja helyileg, lemezre, és újra felhasználhatja a gyorsítótárazott verziót, a további felhasználói kérések. Továbbá minden alkalommal, amikor egy felhasználó kéri az adatokat, az alkalmazás sikerült probléma BEOLVASNI egy feltételes fejléccel módosításának időpontja, amely lenne elkerülése érdekében a teljes blob lekérése, ha még nem lett módosítva. Ez a minta, de az táblaentitások alkalmazhat.  

Bizonyos esetekben előfordulhat, hogy úgy dönt, hogy az alkalmazás feltételezheti, hogy a blob beolvasása, után rövid ideig érvényes marad, és, hogy ebben az időszakban az alkalmazást nem kell ellenőrizni, ha a blob módosítva lett.

Konfigurációs, keresési és egyéb adatok mindig az alkalmazás által használt leginkább alkalmasak a gyorsítótárazáshoz.  

A blob tulajdonságai felderíteni a .NET használatával utolsó módosítási dátumának lekérése egy példa: [beállítása és beolvasni a tulajdonságok és metaadatok](../blobs/storage-properties-metadata.md). Feltételes letöltések kapcsolatos további információkért lásd: [feltételesen frissítése egy Blob egy helyi példányát](https://msdn.microsoft.com/library/azure/dd179371.aspx).  

#### <a name="subheading8"></a>Adatok kötegelt feltöltése
Bizonyos esetekben az összesített adatok helyben, és majd rendszeres időközönként töltse fel egy kötegben minden adat feltöltése azonnal helyett. Például egy webalkalmazás előfordulhat, hogy tartsa a tevékenységek naplófájlt: az alkalmazás vagy feltölthet minden tevékenység részleteinek (amelyhez szükséges számos tárolási műveletek) tábla egységként történik, vagy egy helyi naplófájlba tevékenység részletei menthető, majd rendszeres időközönként tagolt fájl egy blobba, töltse fel minden tevékenység részletei. Ha minden naplóbejegyzés 1 KB méretű, feltöltheti a több ezer egyetlen "Put Blob" tranzakció (akkor is feltölt egy blobot egy tranzakción belül mérete legfeljebb 64 MB). A helyi gépen a feltöltés előtt összeomlik, ha elveszti néhány naplóadatok potenciálisan: az alkalmazás fejlesztőjének kell az ügyféleszközön lehetőségét tervezése vagy feltöltési hibák.  Ha az a tevékenység adatokat le kell tölteni a mérföldkövei (nem csak egyetlen tevékenység), majd blobok használata akkor javasolt táblák keresztül.

### <a name="net-configuration"></a>.NET Configuration
A .NET-keretrendszer használata esetén ez a szakasz felsorolja a több gyors konfigurációs beállítások segítségével győződjön meg arról, teljesítményének jelentős növelése.  Ha más nyelven használja, ellenőrizze, hogy ha hasonló fogalmak érvényesek-e a választott nyelven.  

#### <a name="subheading9"></a>Alapértelmezett korlát növeléséhez
A .NET a következő kódot az alapértelmezett kapcsolathoz megadott korlátot (amely általában 2-es ügyfél környezetben vagy 10 kiszolgálói környezetben) és 100 nő. Általában körülbelül az alkalmazása által használt szálak számát, az értéket kell beállítania.  

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

A kapcsolathoz megadott korlátot, minden olyan kapcsolat megnyitása előtt be kell állítani.  

Más programozási nyelven dokumentációjában adott nyelvhez, hogyan lehet beállítani a kapcsolathoz megadott korlátot.  

További információkért tekintse meg a következő blogbejegyzésben: [webszolgáltatások: Egyidejű kapcsolatok](https://blogs.msdn.com/b/darrenj/archive/2005/03/07/386655.aspx).  

#### <a name="subheading10"></a>Növelje a szálkészlet minimális szálak, ha az aszinkron feladatokat szinkron kód használatával
Ez a kód növeli a hozzászóláslánc készlet minimális szálak:  

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

További információkért lásd: [ThreadPool.SetMinThreads metódus](https://msdn.microsoft.com/library/system.threading.threadpool.setminthreads%28v=vs.110%29.aspx).  

#### <a name="subheading11"></a>Használja ki a .NET 4.5-ös szemétgyűjtés
.NET 4.5-ös vagy újabb, az ügyfél alkalmazás segítségével kihasználhatja a kiszolgáló szemétgyűjtési teljesítménnyel kapcsolatos fejlesztések.

További információkért tekintse meg a cikket [egy áttekintés a teljesítménnyel kapcsolatos fejlesztések a .NET 4.5 rendszerben](https://msdn.microsoft.com/magazine/hh882452.aspx).  

### <a name="subheading12"></a>Korlátlan streameken működő párhuzamosság
Párhuzamosság kiváló teljesítmény lehetnek, legyen óvatos a korlátlan streameken működő párhuzamosság (nincsenek korlátozva a szálak és/vagy párhuzamos kérelmek száma) fel-vagy le adatot, feldolgozó több partíciót eléréséhez használt használatáról (tárolók, üzenetsorok, vagy táblapartíciók) ugyanazt a tárfiókot, vagy ugyanazon a partíción több elemek eléréséhez. Ha a párhuzamosság korlátlan streameken működő, az alkalmazás is lehet használni az ügyféleszköz-képességek, vagy a storage-fiók méretezhetőség eredményez a késés és sávszélesség-szabályozási célozza meg benne.  

### <a name="subheading13"></a>Tároló Ügyfélkódtárai és eszközök
A legújabb Microsoft által biztosított ügyfél-könyvtárak és eszközök mindig használja. Írása idején nincsenek klienskódtárak .NET, Windows Phone, Windows Runtime, Java és C++ érhető el, valamint más nyelven előzetes könyvtárak. Ezenkívül a Microsoft közzétette PowerShell-parancsmagok és az Azure Storage használatát az Azure CLI-parancsokat. A Microsoft aktívan alakul ki ezeket az eszközöket és teljesítményt szem előtt tartja naprakészen tartása a legújabb verziót és biztosítja belső azok kezelni bizonyított teljesítményével kapcsolatos eljárásokat.  

### <a name="retries"></a>Újrapróbálkozások
#### <a name="subheading14"></a>Throttling/ServerBusy
Bizonyos esetekben a storage szolgáltatás lehet szabályozni az alkalmazás, vagy előfordulhat, hogy egyszerűen nem tud teljesíteni a kérést néhány átmeneti állapot miatt, és egy "503-as kiszolgáló foglalt" üzenet vagy "500 időtúllépés" adnak vissza.  Ez akkor fordulhat elő, ha az alkalmazás már majdnem elérte a skálázhatósági célokat bármelyikét, vagy ha a rendszer a újraegyensúlyozása a particionált adatok lehetővé teszik a nagyobb sebesség érdekében.  Az ügyfélalkalmazás általában érdemes újrapróbálkoznia a művelettel, az ilyen hibát okozó: a kérésben később kísérlet sikeres. Ha a társzolgáltatás az alkalmazás szabályozza mert teljesítménycélokat meghaladja, vagy akkor is, ha a szolgáltatás nem tudott teljesíteni a kérést valamilyen más okból, az agresszív újrapróbálkozások általában ellenőrizze a probléma még rosszabb. Ezért az exponenciális visszatartási (az ügyfél könyvtárak alapértelmezett ezt a viselkedést) kell használnia. Például az alkalmazás lehetséges, hogy 2 másodpercig 4 másodperc, majd 10 másodpercet, majd a 30 másodperc után próbálkozzon újra, és majd teljes egészében feladja. Ezt a viselkedést az alkalmazás jelentősen csökkenti annak a szolgáltatás terhelése helyett súlyosbodott problémákat eredményez.  

Kapcsolódási hibák újra meg lehet próbálni azonnal, mivel nem szabályozás eredményét, és várhatóan átmenetiek.  

#### <a name="subheading15"></a>– Újrapróbálkozást lehetővé nem tevő hibák
A klienskódtárak, mely hibák újrapróbálkozási képes, és nem ismeri el. Ha a saját kódját a storage REST API-t, ne feledje azonban, hogy nem kell próbálkoznia hibásak: például 400 (hibás kérés) válasz azt jelzi, hogy az ügyfélalkalmazás nem sikerült feldolgozni, mert nem volt kérés érkezett egy várt formátumban. A kérelem újraküldése eredményeként ugyanazt a választ minden alkalommal, így nincs újrapróbálkozás, a pont. Ha a storage REST API-t a saját kódot kell írnia, vegye figyelembe hibakódok jelenti azt, és a megfelelő módszer újra (vagy nem) mindegyikük számára.  

#### <a name="useful-resources"></a>Hasznos segédanyagok
Tárolási hibakódokkal kapcsolatban további információkért lásd: [állapota és hibakódok](https://msdn.microsoft.com/library/azure/dd179382.aspx) a Microsoft Azure-webhelyen.  

## <a name="blobs"></a>Blobok
A bevált gyakorlatokat mellett [minden szolgáltatás](#allservices) korábban leírt, a következő bevált eljárások alkalmazni a blob szolgáltatáshoz.  

### <a name="blob-specific-scalability-targets"></a>A BLOB-specifikus skálázási célértékei
#### <a name="subheading46"></a>Egyetlen objektum eléréséhez egyidejűleg több ügyfél
Ha egyetlen objektum egyszerre hozzáférő ügyfelek nagy számú, szüksége lesz egy objektum- és tárolási skálázási célértékei érdemes figyelembe venni. A pontos szám az ügyfelek által elérhető egyetlen objektumot fogja tényezők, például az az objektum egyszerre, kérő ügyfelek az objektum mérete száma változhat, hálózati feltételek stb.

Az objektum terjeszthetők keresztül egy CDN-t, például a képek és videók kiszolgálása egy webhelyről, akkor egy CDN-t kell használnia. Lásd: [Itt](#subheading5).

Két lehetősége van más forgatókönyvek esetében, például a tudományos szimulációk esetében, ahol az adatok bizalmas adatnak minősül. Az első az a számítási feladatok hozzáférés ütemtervét, hogy az objektum válaszidővel egyidejűleg hozzáfér egy adott időszakban érhető el. Az objektum azt is megteheti, így növelve a teljes IOPS objektumonkénti és a storage-fiókok között több tárfiók ideiglenesen másolja. Korlátozott tesztjei úgy találtuk, hogy körülbelül 25 virtuális gépeket egyidejűleg sikerült letölteni egy 100 GB-os blobot, párhuzamos (minden virtuális gép volt párhuzamosan futtatni a letöltés 32 szálat használ). Ha 100 ügyféllel, hogy az objektum elérésére. először másolja egy másik tárfiókot, majd az első 50 virtuális gépek eléréséhez, az első blob és a második 50 virtuális gépek hozzáférést a második blob. Eredmények az alkalmazások működésének függően változhat, hogy tesztelje a tervezés során. 

#### <a name="subheading16"></a>Sávszélesség- és Blob műveletek
Olvassa el, vagy írni egy blobot, legfeljebb 60 MB/másodperc (Ez a körülbelül 480 MB/s, amely meghaladja a túl sok ügyféloldali hálózathoz (beleértve a fizikai hálózati Adapterre az ügyféleszközön) képességeit. Egy blob emellett másodpercenként akár 500 kérésig támogatja. Ha több ügyfelet szeretne olvasni ugyanennek a blobnak, és előfordulhat, hogy túllépi ezeket a korlátokat, érdemes a CDN használata a blob terjesztéséhez.  

Cél átviteli blobok kapcsolatos további információkért lásd: [Azure Storage méretezhetőségi és Teljesítménycéljai](storage-scalability-targets.md).  

### <a name="copying-and-moving-blobs"></a>Másolásának és áthelyezésének Blobok
#### <a name="subheading17"></a>Copy Blob
A storage REST API-JÁNAK 2012-02-12-es verzió bevezetett hasznos lehetővé teszi különböző fiókok blobok másolása: egy ügyfélalkalmazás kérje meg a storage szolgáltatás egy blob másolása (valószínűleg egy másik tárfiók) a más forrásból származó, és ezután lehetővé a szolgáltatás végrehajtása a másolási aszinkron módon történik. Ez jelentősen csökkenti a sávszélesség, az alkalmazás számára szükséges adatok telepít át más tárfiókokban, mert nem kell letölteni, és töltse fel az adatokat.  

Egyik ilyen megfontolandó, azonban nem, hogy másolása tárfiókok között, ha nincs ideje garancia arra, amikor a másolási befejezi a. Ha az alkalmazásnak kell egy blob másolása az irányítása alatt gyorsan befejeződik, érdemes lehet a blob másolása töltse le a virtuális géphez, és ezután feltölti a célhelyre.  Ebben a helyzetben teljes tervezhetőségét győződjön meg arról, hogy a másolat egy azonos Azure-régióban futó virtuális gép végzi, vagy más hálózati körülmények előfordulhat, hogy (és valószínűleg fog) befolyásolja a másolási teljesítmény.  Emellett programozott módon figyelheti egy aszinkron másolás előrehaladását.  

Az adott tárfiók magának másolatot általában gyorsan fejeződött be.  

További információkért lásd: [a Blob másolásához](https://msdn.microsoft.com/library/azure/dd894037.aspx).  

#### <a name="subheading18"></a>Az AzCopy használata
Az Azure Storage csapat kiadott "AzCopy" parancssori eszköz, amely a hivatott kiküszöbölni tömeges átvitele számú blobot, valamint a tárfiókok között.  Ezt az eszközt ebben a forgatókönyvben van optimalizálva, és magas átviteli sebességet érhet el.  Tömeges feltöltése, letöltése és másolási forgatókönyvek esetén a javasolt annak használatára. Tudjon meg többet, és töltheti le: [adatátvitel az AzCopy parancssori segédprogrammal](storage-use-azcopy.md).  

#### <a name="subheading19"></a>Az Azure Import/Export szolgáltatás
Nagy mennyiségű (több mint 1 TB) adat az Azure Storage elérhető az Import/Export szolgáltatás, ami lehetővé teszi a fel- és blob storage-ból a szállítási merevlemez-meghajtókon letöltése.  Használja ki az adatokat a merevlemezen, és azokat elküldi a Microsoftnak a feltöltéshez, vagy egy üres merevlemezt küldeni a Microsoftnak adatok letöltéséhez.  További információk a [Microsoft Azure Import/Export szolgáltatás adatok a Blob Storage-ba való átviteléhez történő használatát](../storage-import-export-service.md) ismertető cikkben olvashatóak.  Ez lehet sokkal hatékonyabb, mint feltöltése/letöltése ezen adatmennyiség a hálózaton keresztül.  

### <a name="subheading20"></a>Metaadatok
A blob szolgáltatás támogatja a fő kérések, amelyek magukban foglalhatják a blob metaadatait. Például az alkalmazás egy fénykép kívül EXIF-adatok szükség esetén, sikerült beolvasni a fényképet és csomagolja. Sávszélességet takaríthat meg, és a teljesítmény javítása, az alkalmazás tárolhatja az EXIF-adatok a blob metaadatai az alkalmazás feltöltésekor a fénykép: majd kérheti le az EXIF-adatok használata csak olyan HEAD-kérést, metaadatok mentése jelentős sávszélesség, és a minden alkalommal, amikor a blob olvasása EXIF-adatok kinyerése szükséges feldolgozási időt. Ez akkor lehet hasznos, ha forgatókönyvek, ahol csak szüksége van a metaadatok és a blob nem a teljes tartalom.  A metaadatok csak 8 KB-os (a szolgáltatás nem fogad el store nagyobb, irányuló kérelem) blobonkénti tárolhatók, így az adatok nem fér el ezt a méretet, ha nem lehet ezzel a módszerrel használhatja.  

Hogyan kérhet egy blob metaadatait a .NET-példa: [beállítása és beolvasni a tulajdonságok és metaadatok](../blobs/storage-properties-metadata.md).  

### <a name="uploading-fast"></a>Gyors feltöltése
Az első kérdést megválaszolni a blobfeltöltéshez gyors,:, hogy feltöltése a blob egy vagy több?  Használja az alábbi útmutatás meg, hogy a megfelelő módszert a forgatókönyvtől függően.  

#### <a name="subheading21"></a>Egy nagy méretű blob gyorsan feltöltése
Gyorsan egyetlen nagy méretű blobot feltölteni, az ügyfélalkalmazás a blokkok vagy lapok (folyamatban van az egyes blobok és a tárfiók teljes skálázási célértékei szem előtt tartva) párhuzamos kell tölteni.  A hivatalos Microsoft által biztosított RTM tároló ügyfélkódtárai (.NET, Java) is képes erre.  Az egyes könyvtárak, használja az alább megadott objektum/tulajdonság a párhuzamosság szintjét beállításához:  

* .NET: Használandó BlobRequestOptions objektum ParallelOperationThreadCount állíthatók be.
* Java/Android: Use BlobRequestOptions.setConcurrentRequestCount()
* Node.js: ParallelOperationThreadCount használja, vagy a lehetőségek, illetve a blob szolgáltatás.
* C++: Blob_request_options::set_parallelism_factor módszert használja.

#### <a name="subheading22"></a>Számos blobok gyorsan feltöltése
A blobfeltöltéshez számos gyorsan, a blobfeltöltéshez párhuzamosan. Ez a gyorsabb, mint az egyetlen blobokat egyszerre-s feltöltéssel párhuzamos blokk feltöltését, mert a feltöltés, a storage szolgáltatás több partíción terjeszti. Egy blob csak egy átviteli sebességének 60 MB/másodperc (körülbelül 480 MB/s) támogat. Írása idején egy Egyesült Államokbeli székhelyű LRS-fiókra legfeljebb 20-GB/s belépő, ami sokkal több, mint az átviteli sebességet egy adott blob által támogatott.  [Az AzCopy](#subheading18) feltöltések párhuzamosan alapértelmezés szerint elvégez, és ebben a forgatókönyvben javasoljuk.  

### <a name="subheading23"></a>A megfelelő típusú blob kiválasztása
Az Azure Storage blob két típusát támogatja: *oldal* blobok és *blokk* blobok. Adott használati esethez blob típusa a kívánt teljesítményét és méretezhetőségét, hogy a megoldás hatással lesz. A blokkblobok megfelelőek, amikor nagy mennyiségű adat hatékonyan feltölteni kívánt: például egy ügyfélalkalmazás is kell fénykép vagy videó feltöltése a blob storage. A lapblobok megfelelőek, ha az alkalmazásnak kell adatmennyiségek véletlenszerű írását az adatokon végrehajtandó: például az Azure virtuális merevlemezeket lapblobként tárolt.  

További információkért lásd: [Understanding Block Blobs, hozzáfűző blobokat és Lapblobokat](https://msdn.microsoft.com/library/azure/ee691964.aspx).  

## <a name="tables"></a>Táblák
A bevált gyakorlatokat mellett [minden szolgáltatás](#allservices) korábban leírt, a következő bevált eljárások alkalmazni a kifejezetten a table service.  

### <a name="subheading24"></a>Tábla-specifikus skálázási célértékei
Egy teljes tárfiókot sávszélesség korlátozásai mellett táblák rendelkeznek a következő megadott skálázhatósági korlát.  A rendszer betölti a egyenleg, az adatforgalom növekedésével, de ha az adatforgalom hirtelen jellemzik, akkor előfordulhat, hogy nem lehet átviteli kötet webszerveren, és.  Ha a minta jellemzik, szabályozási és/vagy időtúllépések alatt a hirtelen tárolóként szolgáltatás automatikusan ki a tábla betöltése egyenlegek látja a keresett kell.  Általában lassan ramping rendelkezik, a rendszer pontos ideje szerinti megfelelően terheléselosztásához nyújt jobb eredményeket.  

#### <a name="entities-per-second-account"></a>Entitások (fiók) másodpercenként
Az eléréséhez a táblák skálázhatósági korlátja (1 KB-os egyes) legfeljebb 20 000 entitások egy második egy olyan fiók.  Általában minden entitás, amely kerül beillesztésre oda, frissítése, törlése vagy a beolvasott számát a cél felé.  A kötegelt Beszúrás, 100 entitást tartalmazó számolja tehát, 100 entitást.  Egy lekérdezést, amely megvizsgálja 1000 entitásokat, és adja vissza 5 1000 entitásokként számolja.  

#### <a name="entities-per-second-partition"></a>Entitások (partíció) másodpercenként
Belül egyetlen partícióra a skálázhatósági célok eléréséhez a táblák 2000 entitások (1 KB-os egyes) másodpercenként, használja ugyanazt a leltár az előző szakaszban leírtak szerint.  

### <a name="configuration"></a>Konfiguráció
Ez a szakasz felsorolja a több gyors konfigurációs beállítások segítségével győződjön meg arról, jelentős teljesítménybeli fejlesztései, a table service szolgáltatásban:  

#### <a name="subheading25"></a>Használjon JSON
2013-08-15 storage szolgáltatás verzióval kezdve a table service támogatja a JSON az XML-alapú AtomPub formátum helyett tábla adatátvitelre. Ez hasznos adat mérete kevesebb mint 75 %-kal csökkentheti, és jelentősen javíthatja az alkalmazás teljesítményét.

További információkért tekintse meg a bejegyzését [a Microsoft Azure-beli táblák: A JSON ismertetése](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/05/windows-azure-tables-introducing-json.aspx) és [adattartalom formátuma a Table Service műveletek](https://msdn.microsoft.com/library/azure/dn535600.aspx).

#### <a name="subheading26"></a>Nagle kikapcsolása
A Nagle algoritmus széles körben van megvalósítva TCP/IP-hálózatokon keresztül való hálózati teljesítmény javításához. Azonban akkor sem optimális minden körülmények között (például az interaktív környezetekben). Az Azure Storage esetében Nagle a algoritmus negatív hatással van a tábla és üzenetsor szolgáltatásokra irányuló kérések teljesítményét, és tiltsa le, ha lehetséges.

### <a name="schema"></a>Séma
Hogyan felel meg, és lekérdezheti az adatokat az a legnagyobb egyetlen szolgáltatás, amely befolyásolja a teljesítményt, a table Service. Bár minden alkalmazás különböző, az ebben a szakaszban néhány általános kapcsolódó bevált eljárásokat ismerteti:  

* Táblatervezés
* Hatékony lekérdezések
* Hatékony adatokat.  

#### <a name="subheading27"></a>Táblák és partíciók
Táblák partíciók oszthatók. Minden entitás, a partíción tárolja oszt meg ugyanazt a partíciókulcsot, és a egy egyedi sorral kulcs azonosításához, hogy a partíción belül. A partíciók előnyt nyújtanak, de is használ a skálázási korlátaival.  

* Előnyök: Egyetlen, atomi, kötegelt tranzakcióban, amely tartalmazza a legfeljebb 100 külön tárolási műveletek (legfeljebb 4 MB-os teljes mérete) ugyanazon a partíción entitásokat is frissítheti. A lekérdezni kívánt entitások azonos számú feltételezve, lekérdezheti, adatok egyetlen partíción belül (azonban olvassa el a további javaslatokat a táblaadatok) partíciók kiterjedő adatok hatékonyabban.
* Skálázhatósági korlátja: Ugyanazon a partíción tárolt entitások elérése nem lehet elosztott terhelésű, mert a partíció támogatja a atomi kötegelt tranzakciókat. Ebből kifolyólag a skálázhatósági célok, az egyes táblákat partíciók kisebb, mint a table service az egész.  

Táblák és partíciók ezek jellemzői miatt el kell fogadnia az alábbi tervezési elvek:  

* Az ügyfélalkalmazás gyakran frissíteni vagy munka logikai egységen belüli lekérdezett adatok ugyanazon a partíción kell elhelyezkedniük.  Ez lehet, mert írások összesíti az alkalmazás, illetve előnyeit atomi kötegműveletek szeretné.  Emellett adatok ugyanazon a partíción hatékonyabban lekérdezhetők, egyetlen lekérdezést, mint az adatok partíciók között.
* Az ügyfélalkalmazás nem beszúrási/frissítés, adatok vagy a lekérdezést a munka (egyetlen lekérdezés vagy kötegelt frissítés) ugyanazon logikai egység kell elhelyezkedniük külön partíciókon tárolják.  Fontos megjegyzés: az, hogy nincs egyetlen tábla, partíciós kulcsok száma nincs korlátozva, kellene több millió, a partíciókulcsok nem probléma, és nem lesz hatással a teljesítményre.  Például ha az alkalmazás a felhasználói bejelentkezés népszerű webhely, a felhasználói Azonosítót használja partíciókulcsként lehet jó választás.  

#### <a name="hot-partitions"></a>Forró partíciók
Gyakori elérésű partíció, amely fogadja a forgalmat egy fiókhoz aránytalanul nagy százalékának, és nem kell az elosztott terhelésű egy olyan partíciót, mert.  Általában forró partíciók jönnek létre két módszer egyikével:  

##### <a name="subheading28"></a>Csak hozzáfűzés és jogosultságokat csak minták
A "Hozzáfűzése csak" a minta akkor egyet, ahol a forgalmat egy adott PK minden (vagy szinte az összes) növeli és csökkenti az aktuális idő szerint.  Ilyen például, ha az alkalmazás használja-e az aktuális dátumot naplóadatok partíciókulcsként.  Ennek eredményeképpen az összes bevezetése Beszúrások utolsó a tábla partíciója, és a rendszer nem lehet terhelést elosztani, mert a tábla végéhez fogja az összes írást.  Ha az adott partíció adatforgalom mennyisége meghaladja a partíció-szintű méretezhetőségi célértékét, majd azt eredményez szabályozási.  Célszerűbb győződjön meg arról, hogy adatforgalom több partícióhoz terheléselosztás engedélyezéséhez a kérelmeket a tábla között.  

##### <a name="subheading29"></a>Nagy forgalmú adatok
Ha a particionálási sémát, amely csak az adatok, amelyekkel jobban, mint a többi partíción rendelkezik egy partíció eredményez, mint szabályozása, hogy a partíció megközelíti a skálázhatósági célok partícióinak is látni.  Győződjön meg arról, hogy a partícióséma eredményez nincs egyetlen partíció hamarosan eléri a skálázhatósági célokat, akkor célszerűbb.  

#### <a name="querying"></a>Lekérdezés
Ez a szakasz ismerteti a lekérdezés a table service bevált eljárásokat.  

##### <a name="subheading30"></a>Lekérdezés hatókörébe
Számos módon adja meg a lekérdezés entitások tartományát.  Az alábbiakban látható minden egyes felhasználási megbeszélésre.  

Általánosságban véve elkerülése érdekében a vizsgálatok (lekérdezések nagyobb, mint egyetlen entitást), de meg kell vizsgálnia, ha megpróbálja rendszerezi az adatokat, hogy a vizsgálatok vagy a nem szükséges entitások jelentős mennyiségű visszaadó nélkül adatok lekéréséhez.  

###### <a name="point-queries"></a>Pont lekérdezések
A pont lekérdezés pontosan egy entitás kérdezi le. Ezt a partíciókulcs és a sorkulcs beolvasni az entitás megadásával hajtja végre. Ezek a lekérdezések hatékonyak, és lehetőség szerint használjon őket.  

###### <a name="partition-queries"></a>Partíciós lekérdezések
A partíciólekérdezés egy lekérdezést, amely lekéri az adatok, amelyek egy közös partíciókulcs egy készletét. A lekérdezés általában egy sorkulcsérték-tartományon vagy egy tartományt néhány entitás tulajdonság mellett egy partíciókulcsot határoz meg. Ezek pont lekérdezések-nél kevésbé hatékonyak, és takarékosan.  

###### <a name="table-queries"></a>Tábla lekérdezések
A lekérdezés egy lekérdezést, amely beolvassa az entitások egy készletét, amely nem osztozik egy közös partíciókulcsot. Ezek a lekérdezések nem hatékony, és lehetőség szerint kerülje azokat.  

##### <a name="subheading31"></a>Lekérdezés sűrűség
Egy másik lekérdezés hatékonyságát kulcsfontosságú tényező a számú entitást adott vissza, mint a korábban megszokott vizsgálata a visszaadott set található entitások száma. Ha az alkalmazás, hogy csak 1 %-át az adatokat megosztások, a lekérdezés megvizsgálja 100 entitást ad vissza minden egy entitás egy tulajdonságértéket a szűrt tábla lekérdezést hajt végre. A tábla teljesítménycélokat tárgyalt korábban az összes beolvasott entitások száma, és kapcsolatosak nem entitások száma: alacsony lekérdezés sűrűségű egyszerűen okozhat a table szolgáltatás szabályozása az alkalmazás, mert azt kell vizsgálnia, sok entitások kérje le az entitást, Ön által keresett.  Tekintse meg az alábbi szakaszt a [denormalizáció](#subheading34) hogyan kerülheti el, ez további információt.  

##### <a name="limiting-the-amount-of-data-returned"></a>A visszaküldött adatok mennyiségét korlátozza.
###### <a name="subheading32"></a>Szűrés
Ha tudja, hogy a lekérdezés fog entitások visszaadása, amelyek nem kell az ügyfélalkalmazásban, fontolja meg egy szűrőt a visszaadott csoport méretének csökkentése érdekében. Az entitások nem küld vissza az ügyfélnek továbbra is beleszámítanak a méretezhetőségének korlátai, amíg az alkalmazás teljesítményéhez javulni fog a csökkentett hálózati hasznos adatainak mérete és fel kell dolgoznia az ügyfélalkalmazás entitások korlátozott száma miatt.  Lásd a fenti Megjegyzés a [lekérdezés sűrűségű](#subheading31), azonban – a skálázási célértékei számához kapcsolódnak az entitások vizsgálata, így egy lekérdezést, amely kiszűri a számos entitás továbbra is eredményezhet szabályozás, még akkor is, ha néhány entitások adja vissza.  

###### <a name="subheading33"></a>Projection
Az ügyfélalkalmazásnak szüksége van a tábla az entitások tulajdonságok csak korlátozott számú, ha a leképezés használatával korlátozható a visszaadott adatkészlet mérete. Csakúgy, mint a szűrés, ez segít csökkenteni a hálózati terheléselosztást és az ügyfél feldolgozása.  

##### <a name="subheading34"></a>Denormalizáció
Ellentétben a relációs adatbázisok használata, a bevált gyakorlatokat táblaadatok hatékonyan vezethet denormalizálni az adatokat. Azt jelenti, duplikálásához ugyanazokat az adatokat több entitás (egy minden kulcs használatával az adatok megkeresése) azokat az entitásokat, egy lekérdezést kell vizsgálnia az adatok megkeresése az ügyfél a lehető legkevesebb kell, ahelyett, hogy nagy számú entitások megtalálta az adatokat az alkalmazás vizsgálata Alkalmazásbeállítások van szüksége.  Ha például egy e-kereskedelmi webhelyen érdemes található egy rendelés mindkét által az ügyfél-Azonosítót (, adja meg a megrendelések) és a dátum szerint (engedi rendelések egy napon).  Table Storage, érdemes tárolni, az entitás (vagy egy hivatkozást) kétszer – egyszer tábla nevével, PK és ZATI megkönnyítése érdekében az ügyfél keresése azonosító, egyszer megkeresi a dátum szerint megkönnyítése érdekében.  

#### <a name="insertupdatedelete"></a>INSERT/frissítése/törlése
Ez a szakasz ismerteti a table service-ben tárolt entitások módosítása bevált eljárásokat.  

##### <a name="subheading35"></a>Kötegelés
Kötegelt tranzakciókat ismertek, entitás csoport tranzakciók (ETG) az Azure Storage; egy ETG belül minden művelet egy partíció egyetlen táblában kell lennie. Ahol lehetséges, a ETGs segítségével végezze el a beszúrások, a frissítések és törlések kötegekben. Ez csökkenti a kiszolgáló az ügyfélalkalmazás adatváltások számát, csökkenti a számlázható tranzakciók (egy ETG számlázás szempontjából egy egyetlen tranzakciónak számít, és tartalmazhat legfeljebb 100 tárolási műveletek) száma, és lehetővé teszi a (minden atomi frissítések műveletek sikeres, vagy egy ETG belül sikertelen). Mobil eszközök például a nagy késleltetésű rendelkező környezetek élvezheti jelentősen ETGs használatával.  

##### <a name="subheading36"></a>Upsert
Táblázatból **Upsert** műveletek, amikor lehetséges. Két típusa van **Upsert**, mindkettő hatékonyabb lehet több, mint a hagyományos **beszúrása** és **frissítés** műveletek:  

* **InsertOrMerge**: Ezzel az entitás tulajdonságok egy részének feltöltendő, de nem tudja, hogy az entitás már létezik. Az entitás létezik, ha a hívás szerepel tulajdonságainak frissítése. a **Upsert** műveletet, és hagyja az összes meglévő tulajdonság azok, ha az entitás nem létezik, az új entitás illeszt be. Ez hasonlít leképezése segítségével egy lekérdezésben, hogy csak fel kell töltenie a tulajdonságokat, amelyeket változnak.
* **InsertOrReplace**: Akkor használja, ha szeretne feltölteni egy teljesen új entitás, de nem tudja, hogy már megtalálható-e. Csak akkor ajánlott ez, ha biztos benne, hogy az újonnan feltöltött entity teljesen helyesen szerepel-e, mert a régi entity teljesen felülírja. Ha például az entitást, amely tárolja a felhasználó aktuális helye függetlenül-e az alkalmazás korábban tárolta; felhasználó számára a helyadatok frissíteni kívánt az új hely entitás elkészült, és nem kell minden előző entitás származó adatot.

##### <a name="subheading37"></a>Adatsorozat tárolja egyetlen entitás
Egyes esetekben egy alkalmazás tárolja-e egy sorozat, amelyek gyakran kell egyszerre: például egy alkalmazás előfordulhat, hogy nyomon CPU-használat idővel annak érdekében, hogy az adatok az elmúlt 24 órában a működés közbeni diagram megrajzolásához. Egyik lehetőség, hogy minden entitás egy adott óra jelölő és tárolása a CPU-használat az adott órában az óránkénti, egy tábla entitást. Ezeket az adatokat jeleníti meg, hogy az alkalmazásnak kell lekérni az adatokat a legutóbbi 24 órán belül entitásokat.  

Másik lehetőségként az alkalmazás tárolhatja a CPU-használat az egyetlen entitás külön tulajdonságaként óránként: óránként frissíti, az alkalmazás segítségével egyetlen **InsertOrMerge Upsert** meghívásához frissítse az értéket a legutóbbi az óra. Jeleníti meg az adatokat, az alkalmazás csak kell helyett 24, így egy hatékony lekérdezések egyetlen entitás lekérdezése (lásd fentebb vitafórum a [lekérdezési hatókör](#subheading30)).

##### <a name="subheading38"></a>Strukturált adatok tárolása blobokban
Néha tárolt részben strukturált adatokat úgy érzi, például táblákban, el kell, de a tartományok entitások mindig együtt lekéri és a kötegelt képes beszúrni.  Erre jó példa a naplófájl.  Ebben az esetben is batch-naplók néhány percig, szúrja be őket, és ezután mindig beolvassa a naplók, valamint egyszerre több percig.  Ebben az esetben a teljesítmény érdekében érdemes használata helyett táblák, blobok, mivel jelentősen csökkentheti a visszaadott objektumok száma írt /, és általában a kérelmek száma, amelyeket kell.  

## <a name="queues"></a>Üzenetsorok
A bevált gyakorlatokat mellett [minden szolgáltatás](#allservices) korábban leírt, a következő bevált eljárások alkalmazni az üzenetsor-szolgáltatáshoz.  

### <a name="subheading39"></a>Méretezhetőségi korlátok
Egyetlen üzenetsorhoz feldolgozhatja megközelítőleg 2000 üzenetet (1 KB-os egyes) másodpercenként (minden AddMessage GetMessage és DeleteMessage száma itt üzenetnek számít). Ha ez nem elegendő az alkalmazáshoz, több üzenetsort használja, és az üzenetek elosztva.  

Megtekintheti az aktuális skálázhatósági célokat, [Azure Storage méretezhetőségi és Teljesítménycéljai](storage-scalability-targets.md).  

### <a name="subheading40"></a>Nagle kikapcsolása
Című témakör a tábla-konfigurációt, amely ismerteti a Nagle algoritmus – a Nagle algoritmus a teljesítmény, várólista-kérelmek általában hibás, és tiltsa le.  

### <a name="subheading41"></a>Üzenet mérete
Várólista teljesítményének és méretezhetőségének csökkentheti a üzenet méretének növekedése. Az üzenet csak a fogadó kell információt helyezze.  

### <a name="subheading42"></a>A Batch lekéréséhez
Legfeljebb 32 üzenetet egy üzenetsorból egyetlen művelettel kérheti le. Ez csökkentheti életű könyvtárgyorsítótárból száma az ügyfélalkalmazás, amely különösen hasznos a környezetekben, mobileszközök, például magas késést biztosítanak.  

### <a name="subheading43"></a>Várólista lekérdezési időköz
A legtöbb alkalmazás elindítja a lekérdezést az üzeneteket az üzenetsorból, amely lehet a legnagyobb forrásokból, a tranzakciók az adott alkalmazáshoz. Válassza ki a lekérdezési időköz megérik: túl gyakori lekérdezési okozhat az alkalmazás a várólista skálázási célértékei elsajátításában. (A írása idején) $0,01 a 200 000 tranzakció, egy adott processzor követően egy hónapig másodpercenként lenne költség tehát költség kevesebb mint 15 cent lekérdezési viszont nem általában egy szolgáltatás, amely befolyásolja a kiválasztott lekérdezési időközt.  

Naprakész költség információkért lásd: [Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/).  

### <a name="subheading44"></a>UpdateMessage
Használhat **UpdateMessage** növelheti az láthatatlansági időkorlátot, vagy a állapotadatokat, egy üzenet frissítéséhez. Amíg ez a hatékony, ne feledje, hogy minden egyes **UpdateMessage** művelettel counts a skálázhatósági célok felé. Azonban ez lehet egy sokkal hatékonyabb megközelítést, mint ha egy munkafolyamatot, amely egy feladat a következő egy olyan sort adja át a feladat egyes lépéseinek befejezettként. Használatával a **UpdateMessage** művelet lehetővé teszi, hogy az alkalmazása az üzenetre a feladat állapot mentése és folytathatják a munkát, az üzenet a feladat a következő lépéshez requeuing minden alkalommal, amikor egy lépés befejezi helyett.  

További információkért tekintse meg a cikket [hogyan: Üzenetsorban található üzenet tartalmának módosítása](../queues/storage-dotnet-how-to-use-queues.md#change-the-contents-of-a-queued-message).  

### <a name="subheading45"></a>Alkalmazásarchitektúra
Üzenetsorok kell használnia, hogy az alkalmazásarchitektúra méretezhető. Az alábbi listában néhány üzenetsorok segítségével győződjön meg arról, az alkalmazás jobban skálázható módon:  

* Használhatja a várólistákat munka feldolgozási várakozási sorok létrehozásához, és a számítási feladatok simítja az alkalmazásban. Például hogy sikerült várólistára helyezését felhasználóktól érkező kéréseket, például a feltöltött képek átméretezésének a processzor nagy számításigényű munka elvégzéséhez.
* Használhatja a várólistákat az alkalmazás részei különítse el úgy, hogy egymástól függetlenül skálázhatja őket. Például az egy web front end tudta elhelyezni az felmérés eredménye a felhasználók egy későbbi elemzés céljából, valamint a tárolási üzenetsorba. Hozzáadhat további feldolgozói szerepkör példányai a sorban lévő adatok szükség szerint feldolgozásához.  

## <a name="conclusion"></a>Összegzés
Ebben a cikkben tárgyalt egyes leggyakoribb és bevált eljárások az Azure Storage használata esetén a teljesítmény optimalizálása. Arra bátorítunk minden alkalmazásfejlesztőt, hogy alkalmazásaikat a fenti gyakorlatok alapján értékeljék újra, és fontolják meg, hogy érdemes-e az ajánlások szerint eljárniuk az Azure Storage-t használó alkalmazásaik teljesítményének fokozása érdekében.
