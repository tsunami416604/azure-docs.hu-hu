---
title: Azure Traffic Manager – forgalomirányítási módszerek
description: Ez a cikk segít megérteni a Traffic Manager által használt különböző forgalom-útválasztási módszereket
services: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: rohink
ms.openlocfilehash: 4a035506943eeffa2c3fc4fec27c47da4136683b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250906"
---
# <a name="traffic-manager-routing-methods"></a>A Traffic Manager útválasztási módjai

Az Azure Traffic Manager hat forgalom-útválasztási módszert támogat annak meghatározásához, hogyan irányíthatja a hálózati forgalmat a különböző szolgáltatásvégpontokhoz. A Traffic Manager minden profilesetében a hozzá társított forgalom-útválasztási módszert alkalmazza minden kapott DNS-lekérdezésre. A forgalom-útválasztási módszer határozza meg, hogy melyik végpontot adja vissza a DNS-válasz.

A Traffic Manager ben a következő forgalom-útválasztási módszerek érhetők el:

* **[Prioritás:](#priority-traffic-routing-method)** Válassza **a Prioritás lehetőséget,** ha elsődleges szolgáltatásvégpontot szeretne használni az összes forgalomhoz, és biztonsági mentést szeretne biztosítani arra az esetre, ha az elsődleges vagy a biztonsági mentési végpontok nem érhetők el.
* **[Súlyozott](#weighted):** Válassza **a Súlyozott** lehetőséget, ha a forgalmat a végpontok egy készletében egyenletesen vagy súlyok szerint szeretné elosztani, amelyeket ön határoz meg.
* **[Teljesítmény:](#performance)** Válassza a **Teljesítmény** lehetőséget, ha különböző földrajzi helyeken vannak végpontjai, és azt szeretné, hogy a végfelhasználók a "legközelebbi" végpontot használják a legalacsonyabb hálózati késés szempontjából.
* **[Földrajzi](#geographic):** Válassza **ki a Földrajzi,** hogy a felhasználók irányítják az adott végpontok (Azure, külső vagy beágyazott) alapján, amely földrajzi hely a DNS-lekérdezés származik. Ez lehetővé teszi a Traffic Manager-ügyfelek számára, hogy olyan forgatókönyveket engedélyezzenek, amelyekben a felhasználó földrajzi régiójának ismerete és az e fontos alapján való útválasztás. Ilyenek például az adatszuverenitási megbízások betartása, a tartalom lokalizálása & a felhasználói élmény, valamint a különböző régiókból származó forgalom mérése.
* **[Többértékű](#multivalue):** Válassza a **Többértékű** tapra a Traffic Manager-profilokhoz, amelyek csak IPv4/IPv6-címekkel rendelkezhetnek végpontként. Amikor egy lekérdezés érkezik ehhez a profilhoz, az összes kifogástalan végpontot ad vissza.
* **[Alhálózat](#subnet):** Válassza **az Alhálózati** forgalom-útválasztási módszert a végfelhasználói IP-címtartományok készleteinek egy Traffic Manager-profilon belüli adott végponthoz való hozzárendeléséhez. Amikor egy kérelem érkezik, a végpont vissza lesz rendelve az egyik leképezve a kérelem forrás IP-címét. 


Minden Traffic Manager-profil tartalmazza a végpont állapotának figyelését és az automatikus végpontfeladat-átvételt. További információ: [Traffic Manager Endpoint Monitoring](traffic-manager-monitoring.md). Egyetlen Traffic Manager-profil csak egy forgalomútválasztási módszert használhat. A profilhoz bármikor másik forgalom-útválasztási módszert választhat. A módosítások egy percen belül érvénybe lépnek, és nem merül nek fel állásidő. A forgalom-útválasztási módszerek egymásba ágyazott Traffic Manager-profilok használatával kombinálhatók. A beágyazás kifinomult és rugalmas forgalom-útválasztási konfigurációkat tesz lehetővé, amelyek megfelelnek a nagyobb, összetett alkalmazások igényeinek. További információt a [Beágyazott Traffic Manager-profilok című témakörben talál.](traffic-manager-nested-profiles.md)

## <a name="priority-traffic-routing-method"></a>Prioritásos forgalom-útválasztási módszer

A szervezet gyakran szeretne megbízhatóságot biztosítani a szolgáltatásaiszámára egy vagy több biztonsági mentési szolgáltatás telepítésével arra az esetre, ha elsődleges szolgáltatásuk megszakadna. A "Prioritás" forgalom-útválasztási módszer lehetővé teszi az Azure-ügyfelek számára, hogy könnyen megvalósítsák ezt a feladatátvételi mintát.

![Azure Traffic Manager "Prioritás" forgalom-útválasztási módszer](media/traffic-manager-routing-methods/priority.png)

A Traffic Manager-profil szolgáltatóvégpontok prioritásos listáját tartalmazza. A Traffic Manager alapértelmezés szerint minden forgalmat az elsődleges (legmagasabb prioritású) végpontra irányít. Ha az elsődleges végpont nem elérhető, a Traffic Manager a második végpontra irányítja a forgalmat. Ha az elsődleges és a másodlagos végpont sem elérhető, akkor a forgalom a harmadikra lesz irányítva és így tovább. A végpontok elérhetősége a konfigurált állapot (engedélyezve vagy letiltva), és a végpont folyamatos figyelése alapján van meghatározva.

### <a name="configuring-endpoints"></a>Végpontok konfigurálása

Az Azure Resource Manager konfigurálja a végpont prioritás explicit módon az egyes végpontok "prioritás" tulajdonság használatával. Ez a tulajdonság 1 és 1000 közötti érték. Az alacsonyabb értékek magasabb prioritást jelentenek. A végpontok nem oszthatnak meg prioritási értékeket. A tulajdonság beállítása nem kötelező. Ha nincs megadva, a végpontrendelésen alapuló alapértelmezett prioritás t használ.

## <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Súlyozott forgalom-útválasztási módszer
A "Súlyozott" forgalom-útválasztási módszer lehetővé teszi a forgalom egyenletes elosztását vagy egy előre meghatározott súlyozás használatát.

![Azure Traffic Manager "Súlyozott" forgalom-útválasztási módszer](media/traffic-manager-routing-methods/weighted.png)

A Súlyozott forgalom-útválasztási módszerben súlyt rendel minden végponthoz a Traffic Manager profilkonfigurációjában. A súlyok 1 és 1000 közötti egész számok. Ezt a paramétert nem kötelező megadni. Ha nincs megadva, a Forgalomkezelők alapértelmezett "1" súlyt használ. Minél nagyobb a súly, annál nagyobb a prioritás.

A Traffic Manager minden kapott DNS-lekérdezés esetében véletlenszerűen választ egy elérhető végpontot. Egy adott végpont kiválasztásának valószínűsége az összes elérhető végponthoz rendelt súlyokon múlik. Azonos súly használata az összes végponton egyenletes forgalomeloszlást eredményez. Ha nagyobb vagy kisebb súlyokat használ adott végpontokon, akkor ezek a végpontok többé-kevésbé gyakran adják vissza a DNS-válaszokban.

A súlyozott módszer lehetővé tesz néhány hasznos forgatókönyvet:

* Fokozatos alkalmazásfrissítés: A forgalom bizonyos százalékát rendelje n le egy új végpontra való útvonalhoz, és fokozatosan növelje a forgalmat 100%-ra.
* Alkalmazás áttelepítése az Azure-ba: Hozzon létre egy profilt az Azure és a külső végpontok. Állítsa be a végpontok súlyát az új végpontok előnyben részesítéséhez.
* Felhő-bursting további kapacitás: Gyorsan bontsa ki a helyszíni üzembe helyezés a felhőbe azáltal, hogy egy Traffic Manager-profil mögé helyezi. Ha további kapacitásra van szüksége a felhőben, további végpontokat adhat hozzá vagy engedélyezhet, és megadhatja, hogy a forgalom melyik része kerül az egyes végpontokra.

Az Azure Portal használata mellett az Azure PowerShell, a CLI és a REST API-k használatával is konfigurálhat súlyokat.

Fontos megérteni, hogy a DNS-válaszokat az ügyfelek és a dns-nevek feloldásához használt rekurzív DNS-kiszolgálók gyorsítótárazza. Ez a gyorsítótárazás hatással lehet a súlyozott forgalomeloszlásokra. Ha az ügyfelek és a rekurzív DNS-kiszolgálók száma nagy, a forgalomelosztás a várt módon működik. Ha azonban az ügyfelek vagy a rekurzív DNS-kiszolgálók száma kicsi, a gyorsítótárazás jelentősen megdöntheti a forgalom eloszlását.

Gyakori használati esetek közé tartozik:

* Fejlesztési és tesztelési környezetek
* Alkalmazások közötti kommunikáció
* Olyan alkalmazások, amelyek egy szűk felhasználói bázisra irányulnak, amely közös rekurzív DNS-infrastruktúrával rendelkezik (például a vállalat alkalmazottai, akik proxyn keresztül csatlakoznak)

Ezek a DNS-gyorsítótárazási hatások minden DNS-alapú forgalom-útválasztási rendszerre vonatkozóan gyakoriak, nem csak az Azure Traffic Manager esetében. Bizonyos esetekben a DNS-gyorsítótár kifejezett törlése megoldást jelenthet. Más esetekben egy alternatív forgalom-útválasztási módszer megfelelőbb lehet.

## <a name="performance-traffic-routing-method"></a><a name = "performance"></a>Teljesítményforgalom-útválasztási módszer

A végpontok üzembe helyezése a világ két vagy több helyén javíthatja számos alkalmazás válaszképességét azáltal, hogy a forgalmat a "legközelebbi" helyre irányítja. A "Teljesítmény" forgalom-útválasztási módszer biztosítja ezt a képességet.

![Az Azure Traffic Manager "Teljesítmény" forgalom-útválasztási módszer](media/traffic-manager-routing-methods/performance.png)

A "legközelebbi" végpont nem feltétlenül legközelebb a földrajzi távolság alapján mért. Ehelyett a "Teljesítmény" forgalom-útválasztási módszer határozza meg a legközelebbi végpont a hálózati késés mérésével. A Traffic Manager egy internetes késési táblázatot tart fenn az IP-címtartományok és az egyes Azure-adatközpontok közötti oda-vissza út nyomon követésére.

A Traffic Manager megkeresi a bejövő DNS-kérelem forrás IP-címét az internetes késési táblában. A Traffic Manager ezután kiválaszt egy elérhető végpontot az Azure-adatközpontban, amely az adott IP-címtartománylegalacsonyabb késésével rendelkezik, és a DNS-válaszban visszaadja a végpontot.

Ahogy azt a Traffic Manager működése című részében is [ismertetik,](traffic-manager-how-it-works.md)a Traffic Manager nem fogad DNS-lekérdezéseket közvetlenül az ügyfelektől. A DNS-lekérdezések inkább arekurzív DNS-szolgáltatásból származnak, amelyet az ügyfelek használni szeretnének. Ezért a "legközelebbi" végpont meghatározásához használt IP-cím nem az ügyfél IP-címe, hanem a rekurzív DNS-szolgáltatás IP-címe. A gyakorlatban ez az IP-cím egy jó proxy az ügyfél számára.


A Traffic Manager rendszeresen frissíti az internetes késési táblázatot, hogy figyelembe vegye a globális internet és az új Azure-régiók változásait. Az alkalmazások teljesítménye azonban az interneten keresztüli betöltés valós idejű változásaitól függ. A teljesítményforgalom-útválasztás nem figyeli egy adott szolgáltatásvégpont terhelését. Ha azonban egy végpont elérhetetlenné válik, a Traffic Manager nem tartalmazza azt a DNS-lekérdezési válaszokban.


Megjegyzésre mutat:

* Ha a profil több végpontot tartalmaz ugyanabban az Azure-régióban, majd a Traffic Manager egyenletesen osztja el a forgalmat az adott régióban elérhető végpontok között. Ha egy régión belül más forgalomeloszlást szeretne előnyben részesíteni, [egymásba ágyazott Traffic Manager-profilokat](traffic-manager-nested-profiles.md)is használhat.
* Ha a legközelebbi Azure-régióban lévő összes engedélyezett végpont levan csökkentve, a Traffic Manager áthelyezi a forgalmat a legközelebbi Azure-régió végpontjaira. Ha előnyben részesített feladatátvételi sorrendet szeretne definiálni, [használjon beágyazott Traffic Manager-profilokat.](traffic-manager-nested-profiles.md)
* Ha a Teljesítmény forgalom útválasztási módszert használja külső végpontokkal vagy beágyazott végpontokkal, meg kell adnia a végpontok helyét. Válassza ki a központi telepítéshez legközelebb eső Azure-régiót. Ezek a helyek az internetes késési táblázat által támogatott értékek.
* A végpontot választó algoritmus determinisztikus. Az ugyanazon ügyfélismétlődő DNS-lekérdezések ugyanarra a végpontra irányulnak. Az ügyfelek általában különböző rekurzív DNS-kiszolgálókat használnak utazás közben. Előfordulhat, hogy az ügyfél egy másik végpontra van irányítva. Az útválasztást az internetes késleltetési tábla frissítései is befolyásolhatják. Ezért a Teljesítmény forgalom-útválasztási módszer nem garantálja, hogy az ügyfél mindig ugyanarra a végpontra van irányítva.
* Amikor az internetes késési tábla megváltozik, előfordulhat, hogy egyes ügyfelek egy másik végpontra irányulnak. Ez az útválasztási módosítás pontosabb az aktuális késési adatok alapján. Ezek a frissítések elengedhetetlenek a teljesítményforgalom-útválasztás pontosságának fenntartásához, ahogy az internet folyamatosan fejlődik.

## <a name="geographic-traffic-routing-method"></a><a name = "geographic"></a>Földrajzi forgalom-útválasztási módszer

Traffic Manager-profilok konfigurálhatók a földrajzi útválasztási módszer használatával, hogy a felhasználók adott végpontokra (Azure, Külső vagy beágyazott) irányítva legyenek, attól függően, hogy a DNS-lekérdezésük melyik földrajzi helyről származik. Ez lehetővé teszi a Traffic Manager-ügyfelek számára, hogy olyan forgatókönyveket engedélyezzenek, amelyekben a felhasználó földrajzi régiójának ismerete és az e fontos alapján való útválasztás. Ilyenek például az adatszuverenitási megbízások betartása, a tartalom lokalizálása & a felhasználói élmény, valamint a különböző régiókból származó forgalom mérése.
Ha egy profil földrajzi útválasztásra van konfigurálva, az adott profilhoz társított végpontok mindegyikéhez földrajzi régiók készletét kell hozzárendelni. A földrajzi régió a következő részletességi szinteken lehet 
- Világ – bármely régió
- Regionális csoportosulás – például Afrika, Közel-Kelet, Ausztrália/Csendes-óceán stb. 
- Ország/régió – például Írország, Peru, Hongkong KKT stb. 
- Állam/tartomány – például USA-Kalifornia, Ausztrália-Queensland, Kanada-Alberta stb.(megjegyzés: ez a részletességi szint csak Ausztrália, Kanada és az USA államai/ tartományai számára támogatott).

Ha egy régió vagy egy régiókészlet van rendelve egy végponthoz, az ezekből a régiókból érkező kérelmek csak az adott végpontra lesz irányítva. A Traffic Manager a DNS-lekérdezés forrás IP-címét használja annak a régiónak a meghatározására, amelyből a felhasználó lekérdezést használ – általában ez a felhasználó nevében lekérdezést lebonyolító helyi DNS-feloldó IP-címe.  

![Azure Traffic Manager "Földrajzi" forgalom-útválasztási módszer](./media/traffic-manager-routing-methods/geographic.png)

A Traffic Manager beolvassa a DNS-lekérdezés forrás IP-címét, és eldönti, hogy melyik földrajzi régióból származik. Ezután úgy néz ki, hogy van-e olyan végpont, amelyhez ez a földrajzi régió van leképezve. Ez a felfutás a legalacsonyabb részletességi szinten kezdődik (állam/tartomány, ahol támogatott, máshol az ország/régió szinten), és egészen a legmagasabb szintig, azaz a **Világ**. Az ezzel a bejárással talált első egyezés a lekérdezési válaszban visszaadandó végpontként van megjelölve. Beágyazott típus-végpontdal való egyeztetéskor a rendszer az útválasztási módszer alapján az adott gyermekprofilon belüli végpontot adja vissza. A következő pontok alkalmazhatók erre a viselkedésre:

- A földrajzi régió csak egy végponthoz képezhető le a Traffic Manager-profilban, ha az útválasztás típusa Földrajzi útválasztás. Ez biztosítja, hogy a felhasználók útválasztása determinisztikus, és az ügyfelek engedélyezhetik az egyértelmű földrajzi határokat igénylő forgatókönyveket.
- Ha egy felhasználó régiója két különböző végpont földrajzi leképezése alá tartozik, a Traffic Manager kiválasztja a legalacsonyabb részletességű végpontot, és nem veszi figyelembe az adott régióból a másik végpontra irányuló útválasztási kérelmeket. Vegyünk például egy földrajzi útválasztási típusprofilt két végponttal – endpoint1 és Endpoint2 . A Endpoint1 úgy van beállítva, hogy fogadja az Írországból érkező forgalmat, és az Endpoint2 úgy van beállítva, hogy fogadja az Európából érkező forgalmat. Ha egy kérelem Írországból származik, az mindig az Endpoint1-re lesz irányítva.
- Mivel egy régió csak egy végpontra képezhető le, a Traffic Manager visszaadja azt, függetlenül attól, hogy a végpont kifogástalan-e vagy sem.

    >[!IMPORTANT]
    >Erősen ajánlott, hogy a földrajzi útválasztási módszert használó ügyfelek társítsák azt a beágyazott típus-végpontokhoz, amelyek gyermekprofiljai legalább két végpontot tartalmaznak.
- Ha egy végpont egyezést talál, és hogy a végpont **leállított** állapotban van, a Traffic Manager nodata választ ad vissza. Ebben az esetben a földrajzi régióhierarchiában nem történik további további keresgélés. Ez a viselkedés beágyazott végponttípusok esetén is alkalmazható, ha a gyermekprofil **Leállított** vagy **letiltott** állapotban van.
- Ha egy végpont **letiltott** állapotot jelenít meg, az nem fog szerepelni a régióegyeztetési folyamatban. Ez a viselkedés beágyazott végponttípusok esetén is alkalmazható, ha a végpont **letiltott** állapotban van.
- Ha egy lekérdezés olyan földrajzi régióból érkezik, amelynem rendelkezik leképezéssel a profilban, a Traffic Manager NODATA-választ ad vissza. Ezért erősen ajánlott, hogy az ügyfelek földrajzi útválasztást használjanak egy végponttal, ideális esetben beágyazott típusú, legalább két végpontdal a gyermekprofilon belül, a **World** régiórendelt. Ez azt is biztosítja, hogy minden olyan IP-cím, amely nem egy régióhoz van leképezve, kezelése legyen.

Ahogy azt a Traffic Manager működése című részében is [ismertetik,](traffic-manager-how-it-works.md)a Traffic Manager nem fogad DNS-lekérdezéseket közvetlenül az ügyfelektől. A DNS-lekérdezések inkább arekurzív DNS-szolgáltatásból származnak, amelyet az ügyfelek használni szeretnének. Ezért a régió meghatározásához használt IP-cím nem az ügyfél IP-címe, hanem a rekurzív DNS-szolgáltatás IP-címe. A gyakorlatban ez az IP-cím egy jó proxy az ügyfél számára.

### <a name="faqs"></a>Gyakori kérdések

* [Milyen használati esetekben hasznos a földrajzi útválasztás?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-some-use-cases-where-geographic-routing-is-useful)

* [Hogyan dönthetem el, hogy a Teljesítmény-útválasztási módszert vagy a Földrajzi útválasztási módszert használjam?How I decide if i should use Performance routing method or Geographic routing method?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method)

* [Melyek azok a régiók, amelyeket a Traffic Manager földrajzi útválasztáshoz támogat?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing)

* [Hogyan határozza meg a forgalomkezelő, hogy a felhasználó honnan kérdez?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-manager-determine-where-a-user-is-querying-from)

* [Garantált-e, hogy a Traffic Manager minden esetben pontosan meg tudja határozni a felhasználó pontos földrajzi helyét?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case)

* [Egy végpont fizikailag ugyanabban a régióban kell lennie, mint amelyhez földrajzi útválasztáshoz van konfigurálva?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing)

* [Rendelhetek földrajzi régiókat olyan profil végpontjaihoz, amelyek nincsenek földrajzi útválasztásra konfigurálva?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing)

* [Miért jelenik meg hibaüzenet, amikor egy meglévő profil útválasztási módját földrajzira próbálom módosítani?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic)

* [Miért ajánlott, hogy az ügyfelek egymásba ágyazott profilokat hozzanak létre a végpontok helyett egy olyan profil alatt, amelynek földrajzi útválasztása engedélyezve van?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled)

* [Vannak-e olyan korlátozások az API-verzió, amely támogatja ezt az útválasztási típust?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type)

## <a name="multivalue-traffic-routing-method"></a><a name = "multivalue"></a>Többértékű forgalom-útválasztási módszer
A **többértékű** forgalom-útválasztási módszer lehetővé teszi, hogy több kifogástalan végpontot kapjon egyetlen DNS-lekérdezési válaszban. Ez lehetővé teszi, hogy a hívó ügyféloldali újrapróbálkozásokat végezs más végpontokkal abban az esetben, ha egy visszaadott végpont nem válaszol. Ez az elrendezés javíthatja a szolgáltatás rendelkezésre állását, és csökkentheti a kifogástalan állapotú végpontok DNS-beli lekérdezésével járó késést. A Többértékű útválasztási módszer csak akkor működik, ha a "Külső" típusú összes végpont IPv4- vagy IPv6-címként van megadva. Amikor egy lekérdezés érkezik ehhez a profilhoz, az összes kifogástalan állapotú végpontot ad vissza, és egy konfigurálható maximális visszatérési szám nak van kitéve.

### <a name="faqs"></a>Gyakori kérdések

* [Milyen használati esetekben hasznos a többértékű útválasztás?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-some-use-cases-where-multivalue-routing-is-useful)

* [Hány végpontot ad vissza a rendszer a többértékű útválasztás használatközben?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-endpoints-are-returned-when-multivalue-routing-is-used)

* [Ugyanazt a végpontkészletet kapom, ha többszintű útválasztást használ?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used)

## <a name="subnet-traffic-routing-method"></a><a name = "subnet"></a>Alhálózati forgalom-útválasztási módszer
Az **alhálózati** forgalom-útválasztási módszer lehetővé teszi a végfelhasználói IP-címtartományok készletének hozzárendelését a profil adott végpontjaihoz. Ezt követően, ha a Traffic Manager DNS-lekérdezést kap az adott profilhoz, megvizsgálja a kérelem forrás IP-címét (a legtöbb esetben ez lesz a hívó által használt DNS-feloldó kimenő IP-címe), határozza meg, hogy melyik végponthoz van leképezve, és melyik ad vissza végpontot a lekérdezési válaszban. 

A végponthoz leképezendő IP-cím cidr-tartományként (pl. 1.2.3.0/24) vagy címtartományként (pl. 1.2.3.4-5.6.7.8) adható meg. A végponthoz társított IP-tartományoknak egyedinek kell lenniük a profilon belül, és nem lehetnek átfedésben egy másik végpont IP-címkészletével ugyanabban a profilban.
Ha olyan végpontot határoz meg, amelynek nincs címtartománya, az tartalékként működik, és forgalmat generál a fennmaradó alhálózatokból. Ha nincs tartalék végpont, a Traffic Manager nodata választ küld bármely nem definiált tartományok. Ezért erősen ajánlott, hogy vagy definiáljon egy tartalék végpontot, vagy győződjön meg arról, hogy az összes lehetséges IP-tartomány meg van adva a végpontok között.

Az alhálózati útválasztás segítségével más élmény ben érhető el a felhasználók, akik egy adott IP-területről csatlakoznak. Alhálózatos útválasztással egy ügyfél elérheti, hogy a vállalati irodájából kezdeményezett kérések mindegyike másik végpontra legyen irányítva, ahol például az alkalmazása csak belső használatra szánt verzióját tesztelik. Egy másik használati helyzet az, amikor egy adott internetszolgáltatóval csatlakozó felhasználóknak más felületet szeretne biztosítani (például blokkolni szeretné egy adott internetszolgáltató ügyfeleit).

### <a name="faqs"></a>Gyakori kérdések

* [Milyen használati esetekben hasznos az alhálózati útválasztás?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-some-use-cases-where-subnet-routing-is-useful)

* [Honnan tudja a Traffic Manager a végfelhasználó IP-címét?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-manager-know-the-ip-address-of-the-end-user)

* [Hogyan adhatom meg az IP-címeket az alhálózati útválasztás használatakor?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-specify-ip-addresses-when-using-subnet-routing)

* [Hogyan adhatok meg tartalékvégpontot az alhálózati útválasztás használatakor?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing)

* [Mi történik, ha egy végpont le van tiltva egy alhálózati útválasztási típusú profilban?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile)


## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan fejleszthet magas rendelkezésre állású alkalmazásokat a [Traffic Manager végpontfigyelés használatával](traffic-manager-monitoring.md)




