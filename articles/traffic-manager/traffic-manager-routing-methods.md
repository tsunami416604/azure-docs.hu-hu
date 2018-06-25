---
title: Az Azure Traffic Manager - forgalom-útválasztási módszerei |} Microsoft Docs
description: Ez a cikk, valamit megismerheti a különböző forgalom-útválasztási módszerekkel Traffic Manager által használt
services: traffic-manager
documentationcenter: ''
author: KumudD
manager: timlt
editor: ''
ms.assetid: db1efbf6-6762-4c7a-ac99-675d4eeb54d0
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2017
ms.author: kumud
ms.openlocfilehash: c9bd9b4913e38ed5c1f7f4ec8ee7e3210fa3be8f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
ms.locfileid: "30245362"
---
# <a name="traffic-manager-routing-methods"></a>Traffic Manager útválasztási módszerek

Az Azure Traffic Manager négy forgalom-útválasztási módszer különböző szolgáltatásvégpontokra irányítható a hálózati forgalom is támogatja. A TRAFFIC Manager minden DNS-lekérdezést kap a forgalom-útválasztási módszer vonatkozik. A forgalom-útválasztási módszer határozza meg, a DNS-válaszban visszaadott melyik végponthoz.

Nincsenek elérhető a Traffic Manager négy forgalom-útválasztási módszerekkel:

* **[Prioritás](#priority):** kiválasztása **prioritás** Ha azt szeretné, egy elsődleges szolgáltatás végpontjának összes forgalom, és adja meg a biztonsági mentések esetében az elsődleges vagy a biztonsági mentési végpontok nem érhetők el.
* **[Weighted](#weighted):** válasszon **Weighted** kívánt forgalom szét a végpontok készlete, vagy egyenletesen vagy súlyok, amely alapján határozzák meg.
* **[Teljesítmény](#performance):** válasszon **teljesítmény** Ha végpontok különböző földrajzi helyeken található rendelkezik, és a végfelhasználók számára, hogy a legkisebb hálózati késést tekintetében "legközelebbi" végpontot használni kívánt.
* **[Földrajzi](#geographic):** válasszon **földrajzi** , hogy a felhasználók mely földrajzi helye alapján meghatározott végpontokhoz (Azure, külső vagy beágyazott) a rendszer átirányítja a DNS-lekérdezés származik. Ez lehetővé teszi a Traffic Manager-ügyfelek számára, ahol a felhasználó földrajzi régióban ismerete, és azokat, amelyek alapján útválasztási fontos triggerrel. Például megfelel az adatok közös joghatóság alá megbízás honosítási tartalom & felhasználói felület és a különböző régiókban érkező forgalmat méri.

Traffic Manager-profilokat tartalmazza, végpont állapotát és automatikus végpont feladatátvételi figyelését. További információkért lásd: [Traffic Manager-végpont figyelés](traffic-manager-monitoring.md). Egyetlen Traffic Manager-profil csak egyetlen forgalom-útválasztási módszert használhatja. A profil bármikor választhat egy másik forgalom-útválasztási módszert. A módosításai érvényesek lesznek egy percen belül, és az állásidő nélkül van szükség. Forgalom-útválasztási módszer kombinálható is beágyazott Traffic Manager-profilok használatával. A beágyazási lehetővé teszi, hogy a kifinomult és rugalmas forgalom-útválasztási konfigurációk, amely nagyobb, összetett alkalmazások igényeihez. További információkért lásd: [Traffic Manager-profilok beágyazott](traffic-manager-nested-profiles.md).

## <a name = "priority"></a>Prioritás forgalom-útválasztási módszer

Gyakran egy szervezet szeretne nyújtani a megbízhatóság a hozzá tartozó szolgáltatások telepítésével egy vagy több biztonsági mentési szolgáltatást, abban az esetben, ha az elsődleges szolgáltatás leáll. A "Priority" forgalom-útválasztási módszer lehetővé teszi, hogy a feladatátvétel mintát könnyen végrehajtásához Azure-ügyfél.

![Az Azure Traffic Manager "Priority" forgalom-útválasztási módszer][1]

A Traffic Manager-profil Szolgáltatásvégpontok rangsorolt listáját tartalmazza. Alapértelmezés szerint a Traffic Manager elküldi az összes forgalom az elsődleges (legnagyobb prioritás) végpont. Az elsődleges végpont nem érhető el, ha a Traffic Manager irányítja a forgalmat a második végpontnak. Az elsődleges és másodlagos végpontok nem érhetők el, ha a forgalom halad, a külső, és így tovább. A végpont rendelkezésre állását a konfigurált állapota (engedélyezve vagy letiltva), és a folyamatban lévő végpontmonitoring kijelző alapul.

### <a name="configuring-endpoints"></a>Végpontok konfigurálása

Az Azure Resource Manager, akkor a explicit módon használja a "priority" végpontok végpont prioritásának beállítása. Ez a tulajdonság értéke 1 és 1000 között. Alacsonyabb értékeket egy nagyobb prioritást jelölnek. Nem lehetnek közös prioritási értékek. A következő tulajdonság beállítása nem kötelező megadni. Ha nincs megadva, a végpont sorrendnek alapértelmezett prioritást használja.

##<a name = "weighted"></a>Súlyozott forgalom-útválasztási módszer
A "Súlyozott" forgalom-útválasztási módszerrel forgalom egyenletes elosztása vagy egy előre meghatározott értékek súlyozását.

![Az Azure Traffic Manager az "Súlyozott" forgalom-útválasztási módszer][2]

A súlyozott forgalom-útválasztási módszert rendeljen hozzá egy minden végpontra a Traffic Manager-profil konfigurációban. A súly egy 1 és 1000 egész. Ez a paraméter nem kötelező megadni. Ha nincs megadva, a forgalom kezelők "1" alapértelmezett súlyt használja. A nagyobb súlyt, annál magasabb a prioritás.

Minden egyes megadott DNS-lekérdezés érkezett Traffic Manager véletlenszerűen választ egy elérhető végpontot. A valószínűségi végpont kiválasztása a súlyok rendelt összes elérhető végpontok alapul. Az azonos súlyozással használatával egy még akkor is, a forgalom eloszlása eredményez az összes végpontok között. A meghatározott végpontokhoz magasabb vagy alacsonyabb súlyok használatával azt eredményezi, ezekre a végpontokra ritkább vagy gyakoribb visszahelyezi a DNS-válaszok.

A súlyozott mód lehetővé teszi, hogy néhány hasznos forgatókönyv:

* Az alkalmazásfrissítés fokozatos: az új végpont-hez irányítandó forgalom százalékát, és fokozatosan kívánja növelni a forgalom bizonyos idő 100 %.
* Az Azure-ba történő áttérés:-profil létrehozása az Azure és a külső végpontok száma. Állítsa be úgy a végpontokat előnyben részesítik a végpont súlyának.
* Felhőalapú Teljesítménynövelés a további kapacitás-érdekében: gyorsan bontsa ki a Traffic Manager-profil mögött helyezésével egy helyi központi kiterjeszti a felhőbe. A felhő plusz kapacitást kell, adja hozzá vagy további végpontok engedélyezése, és adja meg, milyen forgalom részét kerül minden végpontra.

Az erőforrás-kezelő Azure portal súlyozott a forgalom útválasztásához konfigurálását támogatja.  Konfigurálhatja a súlyok az Azure PowerShell parancssori felület és a REST API-k erőforrás-kezelő verzióját használja.

Fontos tudni, hogy DNS-válaszok gyorsítótárba kerüljenek-e az ügyfelek és a rekurzív DNS-kiszolgálók, az ügyfelek által használt DNS-nevek feloldására. A gyorsítótárazás súlyozott forgalom terjesztéseket hatással lehetnek. Ha az ügyfelek és a rekurzív DNS-kiszolgálók száma túl nagy, forgalomeloszlás megfelelően működik-e. Azonban ha az ügyfelek vagy a rekurzív DNS-kiszolgálók száma alacsony, gyorsítótárazás is jelentősen döntés a forgalom eloszlása.

Gyakori használati esetek a következők:

* Fejlesztési és tesztelési környezetek
* Alkalmazások kommunikáció
* Alkalmazások célzó egy keskeny felhasználói-talál, amelyek egy közös rekurzív DNS-infrastruktúra (például egy proxyn keresztül történő kapcsolódás vállalati alkalmazottak)

A DNS-gyorsítótár hatások megegyeznek az összes DNS-alapú forgalom útválasztási rendszerre, nem csak az Azure Traffic Manager. Bizonyos esetekben a DNS-gyorsítótár explicit módon törlése rendelkezhetnek megoldás. Más esetekben alternatív forgalom-útválasztási módszert lehet több.

## <a name = "performance"></a>Teljesítmény forgalom-útválasztási módszer

Két vagy több helyen végpontok telepítése világszerte a helyre, amely "" Önhöz legközelebbi adatforgalom sok alkalmazások válaszképességének növelhető. A "Teljesítmény" forgalom-útválasztási módszert biztosítja ezt a lehetőséget.

![Az Azure Traffic Manager "Teljesítmény" forgalom-útválasztási módszer][3]

A "legközelebbi" végpont nincs feltétlenül legközelebbi földrajzi távolság mérve. Ehelyett a "Teljesítmény" forgalom-útválasztási módszer hálózati késés mérésével a legközelebbi végpontot határozza meg. A TRAFFIC Manager IP-címtartományok és minden Azure-adatközpontban közötti üzenetváltások idejének nyomon követésére egy Internet késés tábla tárolja.

A TRAFFIC Manager keres a bejövő DNS-kérelem a Internet késés tábla a forrás IP-címét. A TRAFFIC Manager úgy dönt, az elérhető végpontot az Azure adatközpontjában, amely a legkisebb mértékű késleltetést adott IP-címtartomány, akkor az adott végpontra a DNS-választ ad vissza.

A [Traffic Manager működése](traffic-manager-overview.md#how-traffic-manager-works), a Traffic Manager nem kapja meg DNS-lekérdezések közvetlenül az ügyfelektől. Ahelyett, hogy DNS-lekérdezések határozza meg a rekurzív DNS-szolgáltatás, hogy az ügyfelek használatára van konfigurálva. Ezért az IP-cím segítségével meghatározhatja a "legközelebbi" végpont nem az ügyfél IP-cím, de a rekurzív DNS-szolgáltatás IP-címét. A gyakorlatban az IP-cím esetén az ügyfél a helyes proxy.


A TRAFFIC Manager rendszeresen frissíti a Internet késés tábla a globális internetes és az új Azure-régiók változásainak. Azonban az alkalmazások teljesítményének függ a valós idejű változások a terhelési az interneten keresztül. Teljesítmény forgalom-útválasztás nem figyeli egy adott szolgáltatás végpontjának terhelése. Azonban a végpont nem érhető el, ha a Traffic Manager foglalja a DNS-válaszokban.


Vegye figyelembe a következő szempontok:

* Ha a profil azonos Azure-régióban több végpontot tartalmaz, majd a Traffic Manager elosztja forgalom egyenletesen az adott régióban elérhető végpontok. Régión belül egy másik forgalomeloszlás tetszés szerint használhatja [Traffic Manager-profilok beágyazott](traffic-manager-nested-profiles.md).
* Minden engedélyezett végpontok legközelebbi Azure-régióban állapotromlást, ha a Traffic Manager forgalom áthelyezi a végpontok következő legközelebbi Azure-régióban. Ha szeretne egy előnyben részesített feladatátvételi definiálják, [Traffic Manager-profilok beágyazott](traffic-manager-nested-profiles.md).
* Ha a teljesítmény forgalom-útválasztási módszert használ külső végpontok száma vagy beágyazott végpontok, meg kell adnia az ezekre a végpontokra helyét. Válassza ki a központi telepítés legközelebbi Azure-régiót. Azokon a helyeken az értékeket a Internet késés tábla által támogatott.
* Az algoritmus, amely kiválasztja a végpont determinisztikus. Az azonos ügyféltől ismételt DNS-lekérdezések a azonos végpont irányítja. Általában a ügyfelek különböző rekurzív DNS-kiszolgálók használata utazás közben. Az ügyfél lehet, hogy egy másik végpont irányítása. Útválasztás is hatással lehet az Internet késés táblázaton által. Ezért a teljesítmény forgalom-útválasztási módszer nem garantálja, hogy egy ügyfél mindig irányítása ugyanazon végpont.
* Az Internet késés táblázat megváltozásakor Észreveheti, hogy egyes ügyfelek egy másik végpont van irányítva. Az útválasztási módosítása pontosabb aktuális késési adatok alapján. Ezek a frissítések elengedhetetlen a teljesítmény forgalom-útválasztási pontosságát folyamatos fejlődésének az interneten.

## <a name = "geographic"></a>Földrajzi forgalom-útválasztási módszer

Traffic Manager-profilok beállítható úgy, hogy a földrajzi-útválasztási módszert használja, így a felhasználók vannak átirányítva alapján meghatározott végpontokhoz (Azure, külső vagy beágyazott) mely földrajzi helyen található, a DNS-lekérdezés származik. Ez lehetővé teszi a Traffic Manager-ügyfelek számára, ahol a felhasználó földrajzi régióban ismerete, és azokat, amelyek alapján útválasztási fontos triggerrel. Például megfelel az adatok közös joghatóság alá megbízás honosítási tartalom & felhasználói felület és a különböző régiókban érkező forgalmat méri.
Ha egy profil földrajzi útválasztásra van konfigurálva, mindegyik végpont társított, hogy a profil kell rendelkeznie egy földrajzi helyhez rendelve. A földrajzi régió következő részletességi szinteken is lehet. 
- A globális – bármely régió
- Regionális csoportosítás – például Afrika Közel-Kelet, Ausztrália/csendes-óceáni stb. 
- Ország vagy régió – például Írországban Perui, Hongkong KKT stb. 
- Az állam/megye – például az USA-kaliforniai, Ausztrália-Queensland, Kanada-Alberta stb. (Megjegyzés: a részletesség szintje csak állapotok támogatott / tartományok Ausztrália, Kanada, UK és USA).

Ha egy régiót vagy egy régiók hozzá van rendelve egy végpontot, csak az adott végpontra továbbított azokban a régiókban érkező kérelmeket. A TRAFFIC Manager a forrás IP-címét a DNS-lekérdezés segítségével határozza meg a régió, ahol a felhasználó kérdezi le, a – általában ez az IP-címét a helyi DNS-feloldó, ennek során a felhasználó nevében a lekérdezés.  

![Az Azure Traffic Manager az "Földrajzi" forgalom-útválasztási módszer](./media/traffic-manager-routing-methods/geographic.png)

A TRAFFIC Manager beolvassa a forrás IP-címét a DNS-lekérdezés, és úgy dönt, mely földrajzi régióban származó van. Majd megvizsgálja, hogy nincs-e olyan végponttal, amely a földrajzi régió rendelve van. Ez a keresés legalacsonyabb szintű részletesség (az állam/megye ahol akkor támogatott, ellenkező esetben az ország vagy régió szinten) kezdődik, és egészen a legfelső szintre, amely legfeljebb kerül **globális**. Az első egyezés a átjárás használatával van kijelölve a végpont vissza a válaszban. Ha egy beágyazott típus végponttal, a gyermek-profilon belül a végpont megfelelő adja vissza, az útválasztási módszer alapján. Ez a viselkedés vonatkoznak a következő szempontokat:

- A földrajzi régió rendelhetők csak egy végpont a Traffic Manager-profil földrajzi útválasztási útválasztási típus esetén. Ez biztosítja, hogy a felhasználók útválasztási determinisztikus, és ügyfelek forgatókönyv esetén van szükség a egyértelműen földrajzi határok használatával engedélyezheti.
- Ha egy felhasználó régió két különböző végpontokhoz földrajzi leképezés, a Traffic Manager a végpont a legalacsonyabb lépésköz kiválasztása, és nem veszi figyelembe a többi végpont régió útválasztási kérelmek. Vegye figyelembe például két végpont - Endpoint1 és Endpoint2 földrajzi útválasztási típus egy profilt. Endpoint1 Írországban a forgalom fogadására van beállítva, és Endpoint2 Európából forgalom fogadására van beállítva. Ha a kérelem Írországban származik, mindig átirányítja Endpoint1.
- Egy régiót csak egy végpont rendelhetők, mivel a Traffic Manager visszaadja függetlenül attól, hogy a végpont állapota megfelelő-e.

    >[!IMPORTANT]
    >Erősen ajánlott, hogy az ügyfelek a földrajzi útválasztási módszerrel társíthatja a beágyazott típus végpontok belül legalább két végpontot tartalmazó gyermek profillal.
- Ha egy végpont egyezést talál, és adott végpontra a **leállítva** állapotba kerül, a Traffic Manager NODATA választ ad. Ebben az esetben nincs további keresések legyen feljebb a földrajzi régióban hierarchiában. Ez a viselkedés akkor is beágyazott végpont esetében alkalmazható, ha a gyermek profil van a **leállítva** vagy **letiltott** állapotát.
- Ha egy olyan végpont jelenít meg egy **letiltott** állapota, nem fog szerepelni a folyamat megfelelő régióban. Ez a viselkedés akkor is beágyazott végpont esetében alkalmazható, ha a végpont van a **letiltott** állapotát.
- Ha egy lekérdezést a földrajzi régió, amely nem rendelkezik az adott profilhoz érkezik, a Traffic Manager NODATA választ ad. Ezért erősen ajánlott, hogy az ügyfelek használja-e a földrajzi útválasztási egy végponttal, ideális típusú beágyazott legalább két végpontot a gyermek profiljában, az a régió **globális** rendelve. Ez biztosítja azt is, hogy bármilyen IP-címek, amelyek nem felelnek meg a régió kezeli.

A [Traffic Manager működése](traffic-manager-how-traffic-manager-works.md), a Traffic Manager nem kapja meg DNS-lekérdezések közvetlenül az ügyfelektől. Ahelyett, hogy DNS-lekérdezések határozza meg a rekurzív DNS-szolgáltatás, hogy az ügyfelek használatára van konfigurálva. Ezért az IP-cím segítségével meghatározhatja a régió nem az ügyfél IP-cím, de a rekurzív DNS-szolgáltatás IP-címét. A gyakorlatban az IP-cím esetén az ügyfél a helyes proxy.


## <a name="next-steps"></a>További lépések

Útmutató a magas rendelkezésre állású alkalmazások fejlesztéséhez [Traffic Manager-végpont figyelése](traffic-manager-monitoring.md)

Megtudhatja, hogyan [Traffic Manager-profil létrehozása](traffic-manager-create-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-routing-methods/priority.png
[2]: ./media/traffic-manager-routing-methods/weighted.png
[3]: ./media/traffic-manager-routing-methods/performance.png



