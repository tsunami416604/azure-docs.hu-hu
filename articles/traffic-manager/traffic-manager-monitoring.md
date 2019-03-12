---
title: Az Azure Traffic Manager végpont figyelése |} A Microsoft Docs
description: Ez a cikk azt segítenek megérteni, hogyan Traffic Manager segítségével végpont monitorozása és feladatátvétele automatikus végpont magas rendelkezésre állású alkalmazások üzembe helyezése az Azure-ügyfelek
services: traffic-manager
author: KumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2018
ms.author: kumud
ms.openlocfilehash: 50ed230993f1df07b463297605a144830476803d
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57540262"
---
# <a name="traffic-manager-endpoint-monitoring"></a>Traffic Manager végpont figyelése

Az Azure Traffic Manager beépített végpont monitorozása és feladatátvétele automatikus végpontot tartalmaz. Ez a funkció segít végponti hiba, beleértve az Azure-régió, hibák, rugalmas, magas rendelkezésre állású alkalmazások létrehozásához.

## <a name="configure-endpoint-monitoring"></a>Végpont-monitorozás konfigurálása

Konfigurálja a végpontot, figyelés, a következő beállításokat kell megadnia a Traffic Manager-profil:

* **Protokoll**. Válassza ki a HTTP, HTTPS vagy a TCP protokoll, amelyet a Traffic Manager használ a végpont-tesztelés során ellenőrizze annak állapotát. HTTPS-figyelés nem ellenőrzi a beállítása, hogy az SSL-tanúsítvány érvényes--, csak ellenőrzi, hogy a tanúsítvány jelen-e.
* **Port**. Válassza ki a kérelem által használt port.
* **Elérési út**. A konfigurációs beállítás értéke csak érvényes a HTTP és HTTPS protokollok, mely az elérési út megadását az beállítás megadása kötelező. Ezt a beállítást, a TCP protokoll hibát eredményez figyelési biztosítása. HTTP és HTTPS-protokoll esetében adja meg a következő relatív elérési és a képernyőn látható weblapon vagy a fájlt, amely hozzáfér a figyelési nevét. Perjellel (/) egy relatív elérési út érvényes bejegyzés. Ez az érték azt jelenti, hogy a fájl a gyökérmappában lévő (alapértelmezett).
* **Egyéni fejlécbeállítások** a konfigurációs beállítás segítségével adhat meghatározott HTTP-fejlécek állapotát ellenőrzi, hogy a Traffic Manager elküldi végpontokat a profilhoz. Az egyéni fejlécek egy profil szintjén is alkalmazható a profilt minden végponthoz and / or egy végpont csak az adott végpontra alkalmazandó szintjén adható meg. Egyéni fejlécek is használhatja, hogy az állapot-ellenőrzések egy több-bérlős környezetben végpontokra átirányíthatók megfelelően a rendeltetési állomásfejléc megadásával. Egyedi fejlécek, amelyek segítségével azonosíthatja a Traffic Manager adja meg a HTTP (S) kérések hozzáadásával is használhatja ezt a beállítást, és eltérően feldolgozza azokat. Nyolc fejléc: érték párok seprated megadhatja vesszővel elválasztva. Például "header1:value1, header2:value2". 
* **A várt állapotkód-tartományok állapot** Ez a beállítás lehetővé teszi, hogy több sikeres tartományok 200-299, a 301-301 formátumban adja meg. Ha ezek állapotkódok vannak érkezett válasz a végpont állapot-ellenőrzése elindításakor, a Traffic Manager ezekre a végpontokra kifogástalan állapotúként jelöli meg. Legfeljebb 8 kód tartományt is megadhat. Ez a beállítás akkor alkalmazható, csak a HTTP és HTTPS protokoll és az összes végpontra. Ezzel a beállítással a Traffic Manager-profil szintjén, és alapértelmezés szerint a 200-as értéket számít, ha a sikeres állapot kódja.
* **Mintavételi időköz**. Ez az érték határozza meg, milyen gyakran a rendszer ellenőrzi a végpont a Traffic Manager ellenőrzési ügynököket az állapotát. Két érték Itt adhatja meg: 30 másodperces (normál tesztelés) és 10 másodperc (gyors tesztelés). Ha nincs érték van megadva, a profil egy alapértelmezett érték 30 másodperc állítja be. Látogasson el a [Traffic Manager díjszabási](https://azure.microsoft.com/pricing/details/traffic-manager) gyors ellenőrzési díjszabással kapcsolatos további tudnivalókért lapra.
* **Hibák száma megengedhető**. Ezt az értéket megadja egy ellenőrzési Traffic Manager-ügynököt, hogy a végpont nem kifogástalanként való megjelölése előtt eltűr hány sikertelen. Az érték 0 és 9 közötti tartományba. Egy érték 0, egy egyetlen figyelési hiba okozhatja, hogy a végpont állapotúként lesz megjelölve. Ha nem ad meg értéket, használja az alapértelmezett érték a 3-ból.
* **Mintavétel időkorlátja**. Ez a tulajdonság határozza meg, mennyi ideig az ellenőrzési Traffic Manager-ügynök várnia kell, mielőtt a mérlegeli, hogy ellenőrizze a hiba, ellenőrizze állapotmintát a végponthoz való elküldésekor. Ha a tesztelés időköz értéke 30 másodperc, majd beállíthatja a időtúllépési értéket 5-10 másodperc között. Ha nem ad meg értéket, egy alapértelmezett érték 10 másodperces használ. Ha a tesztelés időköze 10 másodpercet, majd beállíthatja a időtúllépési értéket 5 és 9 másodperc között. Ha nincs időtúllépési érték van megadva, 9 másodperc, az alapértelmezett érték használ.

    ![Traffic Manager végpont figyelése](./media/traffic-manager-monitoring/endpoint-monitoring-settings.png)

    **Ábra:  Traffic Manager végpont figyelése**

## <a name="how-endpoint-monitoring-works"></a>Végpont-monitorozás működése

A monitorozási protokoll HTTP vagy HTTPS van beállítva, ha az ellenőrzési Traffic Manager-ügynök a végpontra a protokoll, port és a megadott relatív elérési úton a GET kérelmet küld. Ha visszakap egy 200-OK válasz, vagy a válaszok bármelyikét konfigurált a ** várt állapotkód: * címtartományok **, majd, hogy a végpont kifogástalan számít. Ha a válasz egy másik értéket, vagy ha nem kap választ adva, majd a Traffic Manager-ügynököt újra megkísérli a hibák száma megengedhető (nincs újból megkísérli történik, ha a beállítás értéke 0) megfelelően tesztelés az időkorláton belül. Ha egymást követő hibák száma nagyobb, mint a hibák száma megengedhető beállítást, majd, hogy a végpont, nem kifogástalannak van jelölve. 

Ha a monitorozási protokoll TCP, az ellenőrzési Traffic Manager-ügynök használatával a megadott port TCP-kapcsolati kérelmekre indítja el. Ha a végpont válaszol a kérésre választ ad a kapcsolat létrehozásához, adott állapot-ellenőrzése sikeres van megjelölve, és az ellenőrzési Traffic Manager-ügynök alaphelyzetbe állítja a TCP-kapcsolatot. Ha a válasz egy másik értéket, vagy ha nem érkezik válasz a határidőn belül megadva, a Traffic Manager-ügynököt újra megkísérli a hibák száma megengedhető (nincs újból megkísérli történik, ha a beállítás értéke 0) megfelelően tesztelés. Ha egymást követő hibák száma nagyobb, mint a hibák száma megengedhető beállítást, majd, hogy a végpont nem kifogástalannak van jelölve.

Minden esetben a Traffic Manager-vizsgálatok több helyről, és minden egyes régióban történik az egymást követő hiba meghatározása. Ez azt is jelenti, hogy végpontok azért küldtük Önnek, állapot-mintavételei Traffic Managerből, mint a tesztelés alatt használt értéke nagyobb gyakorisággal.

>[!NOTE]
>HTTP vagy HTTPS protokollt figyelése a végpont oldalon egy gyakori eljárás, hogy egyéni lap az alkalmazásban – például /health.aspx megvalósítása. Ezt az elérési utat használja figyeléshez, alkalmazásspecifikus ellenőrzéseket, például teljesítményszámlálók ellenőrzése vagy adatbázis-elérhetőségi ellenőrzése hajthat végre. Az Egyéni ellenőrzések alapján, az oldal a megfelelő HTTP-állapotkódot adja vissza.

Traffic Manager-profil szereplő összes végpont ossza meg a figyelési beállításokat. Ha szeretné használni a különböző figyelési beállítások különböző végpontok, létrehozhat [beágyazott Traffic Manager-profilok](traffic-manager-nested-profiles.md#example-5-per-endpoint-monitoring-settings).

## <a name="endpoint-and-profile-status"></a>Végpont és a profil állapota

Engedélyezheti és letilthatja a Traffic Manager-profilok és a végpontok. Azonban a végpont állapotának változása is fordulhat elő egy eredményt a Traffic Manager beállításainak és folyamatokat automatikus módon.

### <a name="endpoint-status"></a>Végpont állapota

Engedélyezheti vagy letilthatja egy adott végpontnak. A mögöttes szolgáltatás, amely továbbra is lehet megfelelő, akkor nem érinti. A végpont állapotának módosítása azt szabályozza, a végpont a Traffic Manager-profil rendelkezésre állását. Ha egy végpont állapota le van tiltva, a Traffic Manager nem ellenőrzi az állapotát, és a végpont nem érhető el a DNS-válasz.

### <a name="profile-status"></a>Profil állapota

A profil állapota beállítás használata esetén engedélyezheti vagy letilthatja egy adott profilhoz. Végpont állapota érint egyetlen végpontot, amíg a profil állapota a teljes profil, beleértve az összes végpontok hatással van. Ha letilt egy profilt, a végpontok állapotát nem ellenőrzi, és a végpontok szerepelnek a DNS-választ. Egy [NXDOMAIN](https://tools.ietf.org/html/rfc2308) válaszkódot adja vissza a DNS-lekérdezés.

### <a name="endpoint-monitor-status"></a>Szolgáltatásvégpont-figyelő állapota

Végpont a figyelő állapota, hogy a végpont állapotát jeleníti meg a Traffic Manager által létrehozott értéket. Ezt a beállítást manuálisan nem módosítható. A végpont a figyelő állapota végpont-monitorozás eredményei és a konfigurált végpont állapotának. A lehetséges értékek a szolgáltatásvégpont-figyelő állapota, az alábbi táblázatban láthatók:

| Profil állapota | Végpont állapota | Szolgáltatásvégpont-figyelő állapota | Megjegyzések |
| --- | --- | --- | --- |
| Letiltva |Engedélyezve |Inaktív |A profil le van tiltva. Bár a végpont állapotának engedélyezve van, akkor a profil állapota (letiltva) élvez elsőbbséget. A letiltott profilok végpontjai nem figyeli a program. A DNS-lekérdezés egy eredménye NXDOMAIN válaszkódot adja vissza. |
| &lt;Bármely&gt; |Letiltva |Letiltva |A végpont le van tiltva. Letiltott végpontok nem figyeli a program. A végpont nem szerepel a DNS-reakciókhoz, ezért azt nem forgalom fogadására. |
| Engedélyezve |Engedélyezve |Online |A végpont számítógép megfigyelés alatt áll, és kifogástalan. Ez a DNS-válaszok tartalmazza, és képes forgalom fogadására. |
| Engedélyezve |Engedélyezve |Csökkentett teljesítmény |Végpont-figyelési állapot-ellenőrzések sikertelenek. A végpont nem érhető el a DNS-válaszok, és nem érkezik forgalom. <br>Egy kivétel ebben a végpontok összes állapotromlást, ha ebben az esetben ezek mindegyike számítanak a lekérdezésekre adott válaszok a visszaadandó).</br>|
| Engedélyezve |Engedélyezve |CheckingEndpoint |A végpont figyel, de az első vizsgálat eredményeit még nem lett érkezett. CheckingEndpoint csak átmeneti állapot, amely közvetlenül a hozzáadásával vagy egy végpontot a profilt az engedélyezése után általában akkor fordul elő. Ebben az állapotban a végpont DNS-válaszok szerepel, és képes forgalom fogadására. |
| Engedélyezve |Engedélyezve |Leállítva |Nem fut a felhőalapú szolgáltatás, vagy a webes alkalmazás, amely a végpontra mutat. Ellenőrizze a felhőalapú szolgáltatás vagy a webes alkalmazás beállításait. Ez is történhet, ha a végpont típusa beágyazott végpont, és a gyermek le van tiltva vagy inaktív. <br>A leállított állapotú végpont nem áll felügyelet. Ez nem szerepel a DNS-válaszok, és nem érkezik a forgalom. Egy kivétel ebben a végpontok összes állapotromlást, ha ebben az esetben ezek mindegyike minősülnek kell visszaadni a lekérdezésekre adott válaszok.</br>|

Hogyan kerül kiszámításra az szolgáltatásvégpont-figyelő állapota a beágyazott végpontokat kapcsolatos részletekért lásd: [beágyazott Traffic Manager-profilok](traffic-manager-nested-profiles.md).

>[!NOTE]
> A végpont leállt a figyelő állapota az App Service történhet, ha a webalkalmazás nem fut, a Standard szintű vagy újabb. További információkért lásd: [App Service-ben a Traffic Manager-integráció](/azure/app-service/web-sites-traffic-manager).

### <a name="profile-monitor-status"></a>Profil a figyelő állapota

A profil a figyelő állapota konfigurált profil állapota és összes végpontok végpont a figyelő állapota értékeit. A következő táblázat ismerteti a lehetséges értékek:

| Profil állapota (a be van állítva) | Szolgáltatásvégpont-figyelő állapota | Profil a figyelő állapota | Megjegyzések |
| --- | --- | --- | --- |
| Letiltva |&lt;bármely&gt; vagy nincs definiálva végpont a profil. |Letiltva |A profil le van tiltva. |
| Engedélyezve |Csökkentett teljesítményű legalább egy végpontot az állapotát. |Csökkentett teljesítmény |Tekintse át az egyes végpont állapota értékek megadásával határozza meg, milyen végpontokat további figyelmet igényelnek. |
| Engedélyezve |Legalább egy végpontot állapota Online. Nincsenek végpontjai csökkentett teljesítményű állapot rendelkezik. |Online |A szolgáltatás fogadja a forgalmat. Nincs szükség további műveletekre. |
| Engedélyezve |Legalább egy végpontot állapota CheckingEndpoint. Végpontok az Online vagy csökkentett teljesítményű állapotban van. |CheckingEndpoints |Az átmeneti állapot következik be, amikor egy profilt, ha létrehozott vagy engedélyezett. Végpont állapota első alkalommal ellenőrzése. |
| Engedélyezve |Az állapot a profilban szereplő összes végpont le van tiltva vagy le, vagy a profil nem definiált végpontjainak rendelkezik. |Inaktív |Nincs végpont sem aktív, de a profil még mindig engedélyezve van. |

## <a name="endpoint-failover-and-recovery"></a>Végponti feladatátvétel és helyreállítás

A TRAFFIC Manager rendszeresen ellenőrzi minden végponton, beleértve a nem megfelelő állapotú végpontok állapotát. A TRAFFIC Manager észleli, ha a végpont kifogástalan állapotú lesz, és vonja vissza az elforgatás.

A végpont állapota nem megfelelő, ha a következő események bármelyike előfordul:
- Ha a monitorozási protokoll HTTP vagy HTTPS:
    - Nem 200-as értékű választ, vagy egy választ, amely nem tartalmazza a megadott állapot tartomány a **állapot állapotkód-tartományok várható** (beleértve a különböző 2xx kódot, vagy 301/302 átirányítási) beállításnál érkezik.
- Ha a monitorozási protokoll TCP: 
    - Nem sikerült Nyugtát vagy külön – Nyugtázási válasz fogadása a SZINKRONIZÁLÁSI kísérlet egy kapcsolat létrehozása a Traffic Manager által küldött irányuló kérelemre adott válasz.
- Az időkorlát. 
- Bármely más kapcsolati probléma eredményez a végpont nem érhető el.

Sikertelen ellenőrzések hibaelhárítási kapcsolatos további információkért lásd: [hibaelhárítási csökkentett teljesítményű állapotban van az Azure Traffic Manager](traffic-manager-troubleshooting-degraded.md). 

Az alábbi ábrán az ütemterv a Traffic Manager-végpontot, amely a következő beállításokkal rendelkezik, a monitorozási folyamat részletes leírása: a HTTP protokoll figyelési, ellenőrzési időköz érték 30 másodperc, eltűrt hibák száma: 3, időtúllépés értéke: 10 másodperc és a DNS-Élettartamot érték 30 másodperc.

![A TRAFFIC Manager végpont feladatátvétel és feladat-visszavétel feladatütemezési](./media/traffic-manager-monitoring/timeline.png)

**Ábra:  Traffic manager végpont feladatátvételének és helyreállításának feladatütemezési**

1. **ELSŐ**. A Traffic Manager rendszer figyelése minden végpont végez egy GET kéréssel figyelési beállítások a megadott elérési út.
2. **200 OK vagy egyéni tartomány megadott figyelési beállítások Traffic Manager-profil** . A monitorozási rendszer vár egy HTTP 200 OK vagy az egyéni tartomány megadott figyelési beállítások üzenet 10 másodpercen belül Traffic Manager-profil vagy. Ha ezt a választ kap, észleli, hogy a szolgáltatás érhető el.
3. **ellenőrzések között, 30 másodperces**. A végpont állapotának ellenőrzése meg kell ismételni minden 30 másodperc.
4. **Szolgáltatás nem érhető el**. A szolgáltatás nem érhető el. Addig nem szerez tudomást a TRAFFIC Manager, amíg a következő állapot-ellenőrzése.
5. **A figyelési elérési úthoz való hozzáférésre megpróbálja**. A monitorozási rendszer hajt végre egy GET kérelmet, de nem érkezett válasz a 10 másodperces időkorláton belül (másik lehetőségként nem 200-as értékű választ előfordulhat, hogy fogadását). 30 másodperces időközönként három még többször, majd megpróbálja. Ha az megpróbál egyik sikeres, majd hányadik alaphelyzetbe áll.
6. **Csökkentett teljesítményű állapot beállítása**. Egy negyedik egymást követő hiba után a monitorozási rendszer, csökkentett teljesítményű végpont nem érhető el állapotát jelöli meg.
7. **Egyéb-végpontokra irányuló forgalom keresztül**. A Traffic Manager DNS-névkiszolgálók frissülnek, és a Traffic Manager többé nem ad vissza a végpont DNS-lekérdezések adott válaszként. Új kapcsolatokat a rendszer átirányítja az egyéb elérhető végpontok. Azonban korábbi, amelyek tartalmazzák a végpont DNS-válaszok előfordulhat, hogy továbbra is gyorsítótárazható rekurzív DNS-kiszolgálók és a DNS-ügyfeleket. Az ügyfelek továbbra is a végpontot használja, amíg a DNS-gyorsítótár le nem jár. A DNS-gyorsítótár lejár, mert az ügyfelek új DNS-lekérdezéseket, és különböző végpontok a rendszer átirányítja. A gyorsítótárazás időtartama vezérli a TTL-értéket a Traffic Manager-profilt, például 30 másodperc.
8. **Állapot-ellenőrzések folytatása**. A TRAFFIC Manager csökkentett teljesítményű állapot má a végpont állapotának ellenőrzéséhez továbbra is. A TRAFFIC Manager észleli, ha a végpont állapotának tér vissza.
9. **Ismét online elérhető szolgáltatás**. A szolgáltatás elérhetővé válik. A végpont a Traffic Manager csökkentett teljesítményű állapotát megőrzi, mindaddig, amíg a monitorozási rendszer hajtja végre a következő állapot-ellenőrzése.
10. **Szolgáltatás folytatja a forgalom**. A TRAFFIC Manager egy GET kérelmet küld, és 200 OK állapot választ kap. A szolgáltatás visszatért Kifogástalan állapotba. A Traffic Manager névkiszolgálóit frissülnek, és ki a szolgáltatás DNS-név a DNS-válaszok kézi megkezdése. Forgalom értéket ad vissza a végponthoz való gyorsítótárazott DNS-válaszok adott vissza más végpontok jár le, és más végpontok meglévő kapcsolatok megszűnik.

    > [!NOTE]
    > Traffic Manager a DNS szintjén működik, mert azt bármely végpont meglévő kapcsolatokat nem befolyásolja. (Akár által módosított profil beállításait, vagy a feladatátvétel és feladat-visszavétel) végpontok közötti forgalmat irányítja, irányítja a Traffic Manager és az új kapcsolatok számára elérhető végpontok. Más végpontok azonban továbbra is a meglévő kapcsolatok keresztül forgalom fogadására, amíg ezek a munkamenetek megszűnik. A meglévő kapcsolatok kiürítési-forgalom engedélyezéséhez alkalmazások korlátozza az egyes végponthoz használt munkamenet időtartama.

## <a name="traffic-routing-methods"></a>Forgalom-útválasztási módszerek

A végpont csökkentett teljesítményű állapotba került, amikor már nem visszaadott válasz a DNS-lekérdezésekre. Ehelyett egy másik végpont van kiválasztva, és adja vissza. A forgalom-útválasztási módszer a profilban konfigurált határozza meg, hogyan van kiválasztva a másodlagos végpontot.

* **Prioritás**. Végpontok űrlap rangsorolt listáját. A listán az első rendelkezésre álló végpont mindig adja vissza. Ha egy végpont állapota csökkentett teljesítményű, majd a következő elérhető végpontok adja vissza.
* **Súlyozott**. Véletlenszerűen hozzárendelt súlyukat és az egyéb elérhető végpontok súlyozását alapján van kiválasztani bármely elérhető végpontot.
* **Teljesítmény**. A felhasználó legközelebb eső végpont adja vissza. Hogy a végpont nem érhető el, ha a Traffic Manager forgalom áthelyezi a végpontok a következő legközelebbi Azure-régióban. Alternatív feladatátvételi tervek teljesítmény forgalom-útválasztási használatával konfigurálható [beágyazott Traffic Manager-profilok](traffic-manager-nested-profiles.md#example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region).
* **Földrajzi**. A végpont a földrajzi hely alapján a lekérdezési kérelem kiszolgálására leképezett IP adja vissza. Hogy a végpont nem érhető el, ha egy másik végpont nem lesz kiválasztva a feladatátvétel, mivel egy földrajzi helyet is le lehet képezni csak egy végpont-profilban (további részleteket a [– gyakori kérdések](traffic-manager-FAQs.md#traffic-manager-geographic-traffic-routing-method)). Ajánlott eljárásként, földrajzi útválasztásának használata esetén javasoljuk, hogy az ügyfelek számára több végpont a profil végpontjaiként beágyazott Traffic Manager-profilok használata.
* **Típushoz** IPv4/IPv6-címek hozzárendelve több végpontot adja vissza. Ehhez a profilhoz fogadásakor egy lekérdezést, kifogástalan állapotú végpontok visszaadott alapján a **válasz maximális rekordszám** Ön által megadott értéket. A válaszok száma alapértelmezett két végpontja.
* **Alhálózat** a végpont leképezett IP-címtartományok készletét adja vissza. Egy kérelem érkezett az IP-címet, ha a végpont egy hozzárendelt IP-címet az adott vissza. 

További információkért lásd: [a Traffic Manager forgalom-útválasztási módszerei](traffic-manager-routing-methods.md).

> [!NOTE]
> A normál forgalom-útválasztási viselkedés egy kivétel lép fel, ha az összes jogosult végpontjai csökkentett teljesítményű állapot. A TRAFFIC Manager révén az "ajánlott beavatkozást" kísérlet és *tűnik, mintha a csökkentett teljesítményű állapot végpontok ténylegesen online állapotban vannak*. Ezt a viselkedést célszerű a tulajdonos alternatív, amelyek nem a DNS-válaszban visszaadandó bármely végpont. Letiltott vagy leállított végpont nem figyeli, ezért nem tekinthetők jogosult-e a forgalmat.
>
> Ez a feltétel gyakran okozza a szolgáltatás nem megfelelő konfigurációja például:
>
> * Hozzáférés-vezérlési lista [ACL] blokkolja a Traffic Manager állapot-ellenőrzések.
> * A figyelési port vagy protokoll a Traffic manager-profilt a helytelen konfiguráció.
>
> Ez a viselkedés következménye, hogy ha a Traffic Manager állapot-ellenőrzések nem megfelelően vannak konfigurálva, az jelenhet meg a forgalom-útválasztási is, ha a Traffic Manager a *van* megfelelően működik. Azonban ebben az esetben végponti feladatátvétel nem fordulhat elő, ami hatással van az általános rendelkezésre állását. Fontos ellenőrizni, hogy a profil egy Online állapota, nem egy csökkentett teljesítményű állapot látható-e. Az Online állapot azt jelzi, hogy a Traffic Manager állapot-ellenőrzések várt módon működnek-e.

Nem sikerült állapot-ellenőrzések hibaelhárítással kapcsolatos további információkat lásd: [hibaelhárítási csökkentett teljesítményű állapotban van az Azure Traffic Manager](traffic-manager-troubleshooting-degraded.md).



## <a name="next-steps"></a>További lépések

Ismerje meg, [Traffic Manager működése](traffic-manager-how-it-works.md)

Tudjon meg többet a [forgalom-útválasztási módszerek](traffic-manager-routing-methods.md) a Traffic Manager által támogatott

Ismerje meg, hogyan [Traffic Manager-profil létrehozása](traffic-manager-manage-profiles.md)

[Csökkentett teljesítményű állapot hibaelhárítása](traffic-manager-troubleshooting-degraded.md) a Traffic Manager végpont
