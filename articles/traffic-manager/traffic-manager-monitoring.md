---
title: "Az Azure Traffic Manager végpontmonitoring kijelző |} Microsoft Docs"
description: "Ez a cikk ismerteti, hogyan Traffic Manager használ a végpontmonitoring kijelző és automatikus végpont feladatátvételi Azure ügyfelek magas rendelkezésre állású alkalmazások központi telepítése"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: fff25ac3-d13a-4af9-8916-7c72e3d64bc7
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/22/2017
ms.author: kumud
ms.openlocfilehash: 3b30aa04854b779c25582abafc0f9ebba65b71ba
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2017
---
# <a name="traffic-manager-endpoint-monitoring"></a>A TRAFFIC Manager-végpont figyelése

Az Azure Traffic Manager beépített végpontmonitoring kijelző és automatikus végpont feladatátvételi tartalmazza. Ez a szolgáltatás segítségével magas rendelkezésre állású alkalmazások, amelyek rugalmasan végpont hibákhoz, akár az Azure-régió.

## <a name="configure-endpoint-monitoring"></a>Konfigurálja a végpontmonitoring kijelző

Figyelési végpont konfigurálásához meg kell adnia a következő beállításokat a Traffic Manager-profil:

* **Protokoll**. Válassza a HTTP, HTTPS vagy TCP protokollt, hogy a Traffic Manager használja, ha a végponthoz probing ellenőrizze annak állapotát. HTTPS-figyelő nem ellenőrzi, hogy az SSL-tanúsítvány érvényes--csak ellenőrzi, hogy a tanúsítvány megtalálható.
* **Port**. Válassza ki a kéréshez használt portot.
* **Elérési út**. A konfigurációs beállítás csak a HTTP és HTTPS protokollok, mely elérési útjának megadásához beállításra akkor szükség esetén érvényes. Ezt a beállítást a TCP protokoll hibát eredményez figyelési biztosítása. A TCP protokollra adja meg a relatív elérési út és a képernyőn látható weblapon vagy a fájlt, amely hozzáfér a figyelő nevét. Perjellel (/) egy érvényes bejegyzés a relatív elérési útja. Ez az érték azt jelenti, hogy a fájl a gyökérmappában lévő (alapértelmezett).
* **Probing időköz**. Ez az érték határozza meg, milyen gyakran a végpont a vizsgálathoz használt Traffic Manager-ügynök állapotának ellenőrzése. Itt két értékeket adhat meg: 30 másodperc (szokásos vizsgálattal) és (gyors számlálása) 10 másodperc. Ha értékek találhatók, a profil alapértelmezett értéke 30 másodperc állítja be. Látogasson el a [Traffic Manager árazás](https://azure.microsoft.com/pricing/details/traffic-manager) tudhat meg többet a vizsgálathoz használt árképzési gyors lap.
* **Hibák száma a megengedett**. Ez az érték határozza meg a vizsgálathoz használt Traffic Manager-ügynök eltűr sérültként végpontra való megjelölése előtt hány sikertelen. Az érték 0 és 9 közötti tartományba. A 0 érték azt jelenti egy egyetlen figyelési hiba okozhatja, hogy a végpont megfelelő állapotúként kell megjelölni. Ha nincs érték megadva, akkor használja az alapértelmezett érték 3.
* **Figyelési időkorlát**. Ez a tulajdonság meghatározza a a vizsgálathoz használt Traffic Manager-ügynök várakozási figyelembe véve, hogy mennyi ideig ellenőrizze a hiba, amikor egy állapotmintáihoz ellenőrizze a rendszer elküldi a végpont. Ha az ellenőrzés időköz értéke 30 másodperc, majd beállíthatja az időtúllépés értéke 5 – 10 másodperc között. Ha nincs érték megadva, akkor az alapértelmezett érték 10 másodperc. Ha a Probing időköz értéke 10 másodperc, majd beállíthatja az időtúllépési értéket 5 és 9 másodperc között. Ha nincs időtúllépés értéke meg van adva, használja a 9-es másodperc, az alapértelmezett érték.

![A TRAFFIC Manager-végpont figyelése](./media/traffic-manager-monitoring/endpoint-monitoring-settings.png)

**1. ábra: A Traffic Manager-végpont figyelése**

## <a name="how-endpoint-monitoring-works"></a>Végpontmonitoring kijelző működése

Ha a figyelési protokoll HTTP vagy HTTPS van beállítva, a vizsgálathoz használt Traffic Manager-ügynök GET kérést küld a végpont a protokoll, port és a megadott relatív elérési útja. Ha azt lekérése vissza 200-OK választ, akkor kifogástalan tekinti a végpontot. Ha a válasz egy másik értéket, vagy ha nem érkezik válasz, majd a Traffic Manager ügynök újrapróbálkozik a hibák száma megengedett beállításai (nincs újrapróbálkozik történik, ha a beállítás értéke 0) probing megadott időkorláton belül. Ha egymást követő hibák száma nagyobb, mint a hibák száma megengedett beállítást, majd, hogy a végpont megfelelő jelölése. 

Ha a figyelési protokoll TCP, a vizsgálathoz használt Traffic Manager-ügynök kezdeményez egy TCP-kapcsolódási kérelem, a megadott porton. A végpont válaszol a kérelemre a csatlakozást, ha van megjelölve, hogy az állapot-ellenőrzés sikeres, és a vizsgálathoz használt Traffic Manager-ügynök alaphelyzetbe állítja a TCP-kapcsolatot. Ha a válasz egy másik értéket, vagy ha nem érkezik válasz a határidőn belül megadva, a Traffic Manager-ellenőrzés ügynök újrapróbálkozik a hibák száma megengedett beállításai (nincs újrapróbálkozik történik, ha a beállítás értéke 0). Ha egymást követő hibák száma nagyobb, mint a hibák száma megengedett beállítást, majd, hogy a végpont van jelölve.

Minden esetben a Traffic Manager-vizsgálat több helyről, és egymást követő sikertelen meghatározása minden régión belül történik. Ez azt is jelenti, hogy végpontok kapnak állapotteljesítmény Traffic Manager Probing időköz használt beállítás-nál nagyobb gyakorisággal.

>[!NOTE]
>HTTP vagy HTTPS protokoll figyelését a végpont oldalon általános gyakorlat, hogy alkalmazza a alkalmazásban – például /health.aspx egyéni lap. Az elérési út használatával figyelésre, alkalmazásspecifikus ellenőrzéseket, például a teljesítményszámlálók keresését, vagy az adatbázis rendelkezésre állásának ellenőrzése hajthatók végre. Az Egyéni ellenőrzések alapján, az oldalon a megfelelő HTTP-állapotkódot adja vissza.

Traffic Manager-profil található összes végpontok ossza meg a figyelési beállításokat. Ha másik figyelési beállításait használni a különböző végpontok van szüksége, létrehozhat [Traffic Manager-profilok beágyazott](traffic-manager-nested-profiles.md#example-5-per-endpoint-monitoring-settings).

## <a name="endpoint-and-profile-status"></a>Végpont és a profil állapota

Engedélyezi, és tiltsa le a Traffic Manager-profilokat és a végpontok. Azonban a végpont állapota módosítják is előfordulhat, hogy elő egy eredményt a Traffic Manager automatikus beállítások és a folyamatok.

### <a name="endpoint-status"></a>Végpont állapota

Engedélyezheti vagy letilthatja az adott végponti. A mögöttes szolgáltatás, és lehet, hogy kifogástalan, nem. A végpont állapota határozza meg a Traffic Manager-profilt a végpont rendelkezésre állását. Ha egy végpont állapota le van tiltva, a Traffic Manager nem ellenőrzi az állapotát, és a végpont nem szerepel a DNS-választ.

### <a name="profile-status"></a>Profil állapota

A profil állapota beállítással engedélyezheti vagy egy adott profil letiltása. Végpont állapota hatással van egy végpontot, amíg a profil állapota a teljes profil, beleértve az összes végpontok hatással van. Ha letilt egy profilt, a végpontok állapotát nem ellenőrzi, és máshol funkció végpontjai nem szerepelnek a DNS-választ. Egy [NXDOMAIN](https://tools.ietf.org/html/rfc2308) a DNS-lekérdezés vissza.

### <a name="endpoint-monitor-status"></a>Végpont-figyelő állapota

Végpont a figyelő állapota egy Traffic Manager által generált érték, amely a végpont állapotát jeleníti meg. Ez a beállítás manuálisan nem módosítható. A végpont a figyelő állapota a végpontmonitoring kijelző eredményeit, és a beállított végpont állapota. A lehetséges értékek a végpont-figyelő állapota a következő táblázatban láthatók:

| Profil állapota | Végpont állapota | Végpont-figyelő állapota | Megjegyzések |
| --- | --- | --- | --- |
| Letiltva |Engedélyezve |Inaktív |A profil le van tiltva. Bár a végpont állapota engedélyezve van, a profil állapota (letiltva) élvez elsőbbséget. A letiltott profilok végpontok nem figyeli. A DNS-lekérdezés egy NXDOMAIN válaszkódot adja vissza. |
| &lt;bármely&gt; |Letiltva |Letiltva |A végpont le van tiltva. Letiltott végpontok nem figyeli. A végpont nem szerepel a DNS-válaszok, ezért az nem forgalom fogadására. |
| Engedélyezve |Engedélyezve |Online |A végpont megfigyelés alatt áll, és megfelelő állapotban. DNS-válaszok szerepel, és képes forgalom fogadására. |
| Engedélyezve |Engedélyezve |Csökkentett teljesítményű |A végpont állapotfigyelése ellenőrzése sikertelen. A végpont nem része a DNS-válaszok, és nem kap a forgalmat. <br>Kivétel ez alól az összes végpontok állapotromlást, ha ebben az esetben az összes minősülnek válaszban visszaadott).</br>|
| Engedélyezve |Engedélyezve |CheckingEndpoint |A végpont megfigyelés alatt áll, de az első vizsgálat eredményeinek még nem érkeztek. CheckingEndpoint: ideiglenes állapot, amely közvetlenül a hozzáadása vagy a végpont a profil engedélyezése után általában akkor következik be. Ebben az állapotban a végpont DNS-válaszok tartalmazza, és képes forgalom fogadására. |
| Engedélyezve |Engedélyezve |Leállítva |A felhőalapú szolgáltatás, vagy a webes alkalmazás mutat, a végpont nem fut. Ellenőrizze a felhőalapú szolgáltatás, vagy a webes alkalmazás beállításai. Ez is előfordulhat, ha a végpont nem beágyazott végpont, és a gyermek le van tiltva vagy nem aktív. <br>A rendszer nem figyeli egy végpontot Leállítva állapotú. DNS-válaszok nem szerepel, és nem kap a forgalmat. Kivétel ez alól az, ha végpontjai állapotromlást, ebben az esetben az összes akkor veszi figyelembe a válaszban visszaadott.</br>|

Hogyan végpont figyelő állapotának kiszámítása beágyazott végpontok kapcsolatos részletekért lásd: [Traffic Manager-profilok beágyazott](traffic-manager-nested-profiles.md).

### <a name="profile-monitor-status"></a>Profil a figyelő állapota

A profil a figyelő állapota a konfigurált profil állapota és a végpont a figyelő állapota értékek összes végpontok. A következő táblázat ismerteti a lehetséges értékek:

| Profil állapota (a be van állítva) | Végpont-figyelő állapota | Profil a figyelő állapota | Megjegyzések |
| --- | --- | --- | --- |
| Letiltva |&lt;bármely&gt; vagy egy nem meghatározott végpont-profilt. |Letiltva |A profil le van tiltva. |
| Engedélyezve |Legalább egy végpont állapota jelenleg csökkentett. |Csökkentett teljesítményű |Tekintse át a egyedi végpont állapota értékek megadásával határozza meg, milyen végpontokat további figyelmet igényelnek. |
| Engedélyezve |Legalább egy végpont állapota Online. Nincsenek végpontjai csökkentett teljesítményű állapotú. |Online |A szolgáltatás fogadja a forgalmat. Nincs szükség semmilyen további műveletre. |
| Engedélyezve |Legalább egy végpont állapota CheckingEndpoint. Nincsenek végpontok Online vagy a csökkentett teljesítményű állapotban vannak. |CheckingEndpoints |A közbenső műveletfázisa következik be, amikor egy profil, ha a létrehozott vagy engedélyezett. A végpont állapota első alkalommal ellenőrzés. |
| Engedélyezve |A profil minden végpontok állapotok vagy le van tiltva, vagy leállt, vagy a profil nincs meghatározott végpont rendelkezik. |Inaktív |Máshol funkció végpontjai nem aktív, de a profil még mindig engedélyezve van. |

## <a name="endpoint-failover-and-recovery"></a>Végpont feladatátvétel és helyreállítás

A TRAFFIC Manager rendszeres időközönként minden végpontot, beleértve a nem megfelelő állapotú végpontok állapotát ellenőrzi. A TRAFFIC Manager észleli, ha a végpont lesz kifogástalan, és vissza során azt az elforgatás kerülnek.

A végpont nem kifogástalan, ha az alábbi esetek bármelyike előfordul:
- Ha a figyelési protokoll HTTP vagy HTTPS:
    - A nem 200 választ (beleértve a különböző 2xx kódot, vagy 301/302 irányítja át a felhasználókat).
- Ha a figyelési protokoll TCP: 
    - A válasz nem Nyugtázási vagy SZIN-Nyugtázási Traffic Manager által küldött kísérlet a kapcsolat felépítése SZINKRONIZÁLÁSI kérelemre adott válasz érkezett.
- Időtúllépés. 
- Bármely más csatlakozási probléma alatt nem érhető el a végpontot eredményez.

Hibaelhárítási sikertelen ellenőrzést kapcsolatos további információkért lásd: [hibaelhárítás "csökkentett teljesítményű" állapota az Azure Traffic Manager](traffic-manager-troubleshooting-degraded.md). 

A 2. ábrán a következő ütemterv részletes leírása a megfigyelési folyamat Traffic Manager-végpont a következő beállításokkal: HTTP protokoll figyelési ellenőrzési időköz érték 30 másodperc, megengedett hibák száma: 3, időtúllépés értéke 10 másodperc és DNS-élettartam érték 30 másodperc.

![A TRAFFIC Manager végpont feladatátvétel és a feladat-visszavétel feladatütemezési](./media/traffic-manager-monitoring/timeline.png)

**2. ábra: Traffic manager végpont feladatátvételének és helyreállításának feladatütemezési**

1. **ELSŐ**. Végpontok a Traffic Manager megfigyelési rendszere végez egy GET kérelmet a figyelési beállításokat a megadott elérési út.
2. **200 OK**. A felügyeleti rendszer 10 másodpercen belül vissza kell adni egy HTTP 200 OK üzenet vár. Ha ezt a választ kap, felismeri, hogy elérhető legyen-e a szolgáltatás.
3. **ellenőrzések között, 30 másodperces**. A végpont az állapot-ellenőrzéssel 30 másodpercenként ismétlődik.
4. **A szolgáltatás nem érhető el**. A szolgáltatás nem érhető el. A TRAFFIC Manager a következő állapot-ellenőrzéssel addig nem szerez tudomást.
5. **Megpróbál hozzáférni a figyelési elérési út**. A figyelési rendszer hajt végre egy GET kérelmet, de nem kapott választ a 10 másodperces időkorláton belül (másik lehetőségként nem 200 válasz lehet, hogy fogadható). Akkor megpróbálja háromszor, 30 másodperces időközönként. Ha az megpróbál egyik sikeres, majd kísérletek számát alaphelyzetbe áll.
6. **Csökkentett teljesítményű csökkentett**. Egy negyedik egymást követő hiba után a felügyeleti rendszer nem érhető el-végpont állapota csökkentett teljesítményű állapotúként jelöli meg.
7. **Forgalom keresztül más végpontokhoz**. A Traffic Manager DNS-névkiszolgálók folyamatosan frissülnek, és a Traffic Manager már nem ad vissza a végpont választ a DNS-lekérdezésekre. Új kapcsolatokat, rendelkezésre álló végpontja van irányítva. Azonban előző DNS-válaszok, amelyek tartalmazzák ezt a végpontot előfordulhat, hogy továbbra is gyorsítótárazható rekurzív DNS-kiszolgálók és DNS-ügyfeleket. Az ügyfelek továbbra is használja a végpontot, amíg a DNS-gyorsítótár le nem jár. A DNS-gyorsítótár lejár, az ügyfelek új DNS-lekérdezések, és különböző végpontok a rendszer átirányítja. A gyorsítótárazás időtartama vezérli a TTL-értéket a Traffic Manager-profilt, például 30 másodperc.
8. **Állapotfigyelő ellenőrzi, hogy továbbra is**. A TRAFFIC Manager továbbra is ellenőrizze, a végpont állapotát, amíg a csökkentett teljesítményű állapotú. A TRAFFIC Manager észleli, ha a rendszerállapot és a végpont ad vissza.
9. **Szolgáltatás ismét online elérhető**. A szolgáltatás elérhetővé válik. A végpont a csökkentett teljesítményű állapota a Traffic Manager őrzi meg addig, amíg a felügyeleti rendszer hajt végre a következő állapot-ellenőrzése.
10. **A szolgáltatás folytatása forgalom**. A TRAFFIC Manager GET kérést küld, és 200 OK állapotot választ kap. A szolgáltatás visszatért Kifogástalan állapotba. A Traffic Manager névkiszolgálók frissülnek, és ki a szolgáltatás DNS-név, a DNS-válaszok kézi megkezdése. Forgalom értéket ad vissza a végpontnak gyorsítótárazott DNS-válaszok adott vissza végpontja lejár, és más végpontok meglévő kapcsolatok vannak állítva.

    > [!NOTE]
    > Traffic Manager DNS szintjén működik, mert az nem befolyásolja a tetszőleges végpontot meglévő kapcsolatok. Arra utasítja a (vagy módosított profil beállításait, feladatátvétel vagy a feladat-visszavétel során) végpontok közötti forgalmat, ha a Traffic Manager arra utasítja a számára elérhető végpontok új kapcsolatot. Más végpontok azonban továbbra is meglévő kapcsolatokon keresztül forgalom fogadására, amíg a munkamenetek leállítása van. Ahhoz, hogy a forgalom kiürítése a meglévő kapcsolatokat, alkalmazások a munkamenet időtartama használt minden végponttal kell korlátoznia.

## <a name="traffic-routing-methods"></a>Forgalom-útválasztási módszer

A végpont állapota csökkentett teljesítményű, ha már nem eredmény abban a DNS-lekérdezésekre. Ehelyett egy másik végpont választott és adott vissza. A forgalom-útválasztási módszer a profilban konfigurált határozza meg, hogyan kell kiválasztani a másik végpont.

* **Prioritás**. Végpontok alkotják a prioritási sorrend listájáról. A listán az első elérhető végpontot mindig adja vissza. Ha egy végpont állapota jelenleg csökkentett, majd a következő elérhető végpontot ad vissza.
* **Súlyozott**. Minden elérhető végpontot véletlenszerű alapján a hozzárendelt és az egyéb elérhető végpontok van kiválasztva.
* **Teljesítmény**. A felhasználó legközelebb végpont adja vissza. A végpont nem érhető el, ha Traffic Manager forgalom áthelyezi a végpontok következő legközelebbi Azure-régióban. Konfigurálhatja az alternatív feladatátvételi tervek teljesítmény forgalom-útválasztás használatával [Traffic Manager-profilok beágyazott](traffic-manager-nested-profiles.md#example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region).
* **Földrajzi**. A végpont a földrajzi hely alapján a lekérdezés kérelem kiszolgálására leképezése az IP-címeknek adja vissza. A végpont nem érhető el, ha egy másik végpont nem lesz kiválasztva, így mivel a földrajzi helyet csak egy végpont a profil rendelhetők (további részleteket a [gyakran ismételt kérdések](traffic-manager-FAQs.md#traffic-manager-geographic-traffic-routing-method)). Ajánlott eljárásként, földrajzi útválasztási használatakor ügyfelek több végpont a profil végpontjaiként beágyazott Traffic Manager-profilokat használata ajánlott.

További információkért lásd: [Traffic Manager forgalom-útválasztási módszert](traffic-manager-routing-methods.md).

> [!NOTE]
> Normál forgalom-útválasztási viselkedés egyetlen kivétel következik be, amikor az összes jogosult végpontok állapotú csökkent. A TRAFFIC Manager elérhetővé válnak a lehető legjobb rendezését,"kísérlet és *tűnik, mintha a csökkentett teljesítményű állapot végpontok ténylegesen online állapotban vannak*. Ezt a viselkedést célszerű a tulajdonos alternatív, amely nem a DNS-válasz a tetszőleges végpontot vissza lenne. Letiltott vagy leállított végpontok nem figyeli, ezért nem tekinthetők jogosult a forgalmat.
>
> Ez a feltétel gyakran okozza a szolgáltatás helytelen konfigurációja, mint:
>
> * Hozzáférés-vezérlési listaként [ACL] blokkolja a Traffic Manager állapotát ellenőrzi.
> * A figyelési port vagy a Traffic manager-profil a protokoll nem megfelelő konfigurációja.
>
> Ez a viselkedés következménye, hogy ha Traffic Manager állapotellenőrzést nem megfelelően vannak konfigurálva, akkor előfordulhat, hogy jelennek meg a forgalom-útválasztási mint Traffic Manager *van* megfelelően működik. Azonban ebben az esetben végpont feladatátvételi nem fordulhat elő, ami hatással van az alkalmazás általános rendelkezésre állási. Fontos ellenőrizni, hogy a profil egy Online állapotát, a csökkentett teljesítményű állapota nem látható-e. Az Online állapot azt jelzi, hogy a Traffic Manager állapotellenőrzést várt módon működnek-e.

Nem sikerült a következő hibaelhárítással kapcsolatos további információkat a állapotellenőrzést, lásd: [hibaelhárítás "csökkentett teljesítményű" állapota az Azure Traffic Manager](traffic-manager-troubleshooting-degraded.md).



## <a name="next-steps"></a>Következő lépések

Ismerje meg, [Traffic Manager működése](traffic-manager-how-traffic-manager-works.md)

További információ a [forgalom-útválasztási módszert](traffic-manager-routing-methods.md) Traffic Manager által támogatott

Megtudhatja, hogyan [Traffic Manager-profil létrehozása](traffic-manager-manage-profiles.md)

[Csökkentett teljesítményű állapot hibaelhárítása](traffic-manager-troubleshooting-degraded.md) a Traffic Manager-végpontot
