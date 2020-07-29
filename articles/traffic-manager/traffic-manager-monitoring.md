---
title: Azure Traffic Manager végpontok figyelése | Microsoft Docs
description: Ez a cikk segít megérteni, hogyan használja a Traffic Manager a végpontok monitorozását és az automatikus végpont-feladatátvételt, hogy az Azure-ügyfelek magas rendelkezésre állású alkalmazásokat telepítsenek
services: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2018
ms.author: rohink
ms.openlocfilehash: 61aafbe8cb12e93d72f5efd01155f06fb3ec0c28
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80757258"
---
# <a name="traffic-manager-endpoint-monitoring"></a>Traffic Manager-végpontmonitorozás

Az Azure Traffic Manager beépített végpontok figyelését és automatikus végpont-feladatátvételt tartalmaz. Ez a funkció segít olyan magas rendelkezésre állású alkalmazások továbbításában, amelyek rugalmasan használhatók a végponti hibákhoz, beleértve az Azure-régió meghibásodását.

## <a name="configure-endpoint-monitoring"></a>Végpont figyelésének konfigurálása

A végpontok figyelésének konfigurálásához a következő beállításokat kell megadnia a Traffic Manager profiljában:

* **Protokoll**. Válassza a HTTP, HTTPS vagy TCP lehetőséget a protokollként, amelyet Traffic Manager használ, amikor a végpontot használja az állapot ellenőrzéséhez. A HTTPS-figyelés nem ellenőrzi, hogy a TLS/SSL-tanúsítvány érvényes-e – csak ellenőrzi, hogy a tanúsítvány megtalálható-e.
* **Port**. Válassza ki a kérelemhez használt portot.
* **Elérési út**. Ez a konfigurációs beállítás csak a HTTP és a HTTPS protokoll esetében érvényes, amelynél meg kell adni az elérési út beállítását. Ha ezt a beállítást a TCP-figyelési protokollhoz adja, a hibát eredményez. HTTP-és HTTPS-protokoll esetén adja meg a weblap relatív elérési útját és nevét, illetve a figyeléshez hozzáférő fájlt. A továbbítási perjel (/) a relatív elérési út érvényes bejegyzése. Ez az érték azt jelenti, hogy a fájl a gyökérkönyvtárban van (alapértelmezés).
* **Egyéni fejléc beállításai** Ezzel a konfigurációs beállítással adott HTTP-fejléceket adhat hozzá azokhoz az állapot-ellenőrzésekhez, amelyeket a rendszer a profilhoz tartozó végpontokra küld Traffic Manager. Az egyéni fejlécek a profil szintjén adhatók meg, hogy a profil összes végpontján és/vagy csak az adott végpontra érvényes végponti szinten legyenek alkalmazhatók. A több-bérlős környezetben található végpontokra vonatkozó állapot-ellenőrzésekhez egyéni fejléceket is használhat, ha a gazdagép fejlécének megadásával helyesen irányítja át azt a célhelyre. Ezt a beállítást olyan egyedi fejlécek hozzáadásával is felhasználhatja, amelyekkel azonosíthatók a Traffic Manager kezdeményezett HTTP (S) kérelmek, és különböző módon dolgozható fel. Legfeljebb nyolc fejlécet adhat meg: az érték párokat vesszővel seprated. Például: "header1: érték1, header2: Érték2". 
* **Várt állapotkód-tartományok** Ez a beállítás lehetővé teszi több sikeres kód tartományának megadását a 200-299, 301-301 formátumban. Ha ezek az állapotkódok az állapot-ellenőrzést kezdeményező végponttól válaszként érkeznek, Traffic Manager a végpontokat kifogástalanként jelöli meg. Legfeljebb 8 állapotkód-tartományt adhat meg. Ez a beállítás csak a HTTP és a HTTPS protokollra, valamint az összes végpontra vonatkozik. Ez a beállítás a Traffic Manager profil szintjén van megadva, és alapértelmezés szerint a 200 érték a sikeres állapotkód.
* A **szondázás időköze**. Ez az érték határozza meg, hogy a rendszer milyen gyakran ellenőrzi a végpontok állapotát egy Traffic Manager szondázás ügynöktől. Itt két értéket is megadhat: 30 másodperc (normál szondázás) és 10 másodperc (gyors szondázás). Ha nincs megadva érték, a profil alapértelmezett értéke 30 másodperc. A gyors szondázás díjszabásáról a [Traffic Manager díjszabását](https://azure.microsoft.com/pricing/details/traffic-manager) ismertető oldalon tájékozódhat.
* **Tolerált hibák száma**. Ez az érték határozza meg, hogy a Traffic Manager-szondázás ügynöke hány hibát észlel, mielőtt a végpontot nem Kifogástalan állapotba kívánja megjelölni. Értéke 0 és 9 között lehet. A 0 érték azt jelenti, hogy egyetlen figyelési hiba miatt a végpont nem megfelelőként van megjelölve. Ha nincs megadva érték, a rendszer az alapértelmezett 3 értéket használja.
* Mintavételi **időkorlát**. Ez a tulajdonság határozza meg azt az időtartamot, ameddig a Traffic Manager szondázás ügynöknek várnia kell, mielőtt fontolóra venné a hibát, amikor egy állapot-ellenőrzési mintavételt küld a végpontnak. Ha a szondázás időköze 30 másodpercre van beállítva, akkor az időtúllépési értéket 5 és 10 másodperc között állíthatja be. Ha nincs megadva érték, a rendszer 10 másodperces alapértelmezett értéket használ. Ha a szondázás időköze 10 másodpercre van beállítva, akkor az időtúllépési értéket 5 és 9 másodperc között állíthatja be. Ha nincs megadva időtúllépési érték, a rendszer a 9 másodperces alapértelmezett értéket használja.

    ![Traffic Manager-végpontmonitorozás](./media/traffic-manager-monitoring/endpoint-monitoring-settings.png)

    **Ábra: Traffic Manager végpont figyelése**

## <a name="how-endpoint-monitoring-works"></a>A végpont-figyelés működése

Ha a figyelési protokoll HTTP-vagy HTTPS-értékre van beállítva, akkor a Traffic Manager szondázás ügynök GET kérelmet küld a végpontnak a megadott protokoll, port és relatív elérési út használatával. Ha egy 200 – OK választ kap, vagy a **várt állapotkód- \* tartományban**konfigurált válaszokat, akkor a végpont kifogástalannak minősül. Ha a válasz egy másik érték, vagy ha a megadott időtúllépési időszakon belül nem érkezik válasz, akkor a rendszer az Traffic Manager szondázás-ügynököt az észlelt hibák száma alapján újra megkísérli (ha ez a beállítás 0), akkor a rendszer nem próbálkozik újra. Ha az egymást követő hibák száma nagyobb, mint a hibák száma, akkor a végpont nem kifogástalan állapotú. 

Ha a figyelési protokoll TCP, akkor a Traffic Manager szondázás ügynök TCP-kapcsolati kérelmet kezdeményez a megadott port használatával. Ha a végpont válaszként válaszol a kapcsolat létesítésére, az állapot-ellenőrzés sikeresként van megjelölve, és a Traffic Manager szondázás ügynök visszaállítja a TCP-kapcsolatot. Ha a válasz eltérő érték, vagy ha a megadott időtúllépési időszakon belül nem érkezik válasz, akkor a Traffic Manager-szondázás ügynök újrapróbálkozik a sikertelen hibák beállításának megfelelően (ha ez a beállítás 0), akkor a rendszer nem próbálkozik újra. Ha az egymást követő hibák száma nagyobb, mint az észlelt hibák száma, akkor a végpont állapota nem kifogástalan.

Minden esetben Traffic Manager mintavétel több helyről, és az egymást követő meghibásodások meghatározása az egyes régiókon belül történik. Ez azt is jelenti, hogy a végpontok az Traffic Manager-ből származó állapot-mintavételeket kapnak, mint a szondázás intervallumához használt beállítás.

>[!NOTE]
>HTTP-vagy HTTPS-figyelési protokoll esetén a végponti oldal egy általános gyakorlata az alkalmazáson belüli egyéni lap megvalósítása – például/Health.aspx. Ha ezt az útvonalat használja a figyeléshez, alkalmazásspecifikus ellenőrzéseket hajthat végre, mint például a teljesítményszámlálók ellenőrzése vagy az adatbázis rendelkezésre állásának ellenőrzése. Ezen egyéni ellenőrzések alapján a lap egy megfelelő HTTP-állapotkódot ad vissza.

Egy Traffic Manager profil összes végpontja megosztja a figyelési beállításokat. Ha eltérő figyelési beállításokat kell használnia a különböző végpontokhoz, létrehozhat [beágyazott Traffic Manager profilokat](traffic-manager-nested-profiles.md#example-5-per-endpoint-monitoring-settings).

## <a name="endpoint-and-profile-status"></a>Végpont és profil állapota

Engedélyezheti és letilthatja Traffic Manager profilokat és végpontokat. A végpontok állapotának változása azonban Traffic Manager automatizált beállítások és folyamatok eredményeképpen is előfordulhat.

### <a name="endpoint-status"></a>Végpont állapota

Engedélyezheti vagy letilthatja az adott végpontot. A mögöttes szolgáltatás, amely továbbra is kifogástalan lehet, nem érinti. A végpont állapotának módosítása szabályozza a végpont rendelkezésre állását a Traffic Manager profilban. Ha egy végpont állapota le van tiltva, Traffic Manager nem vizsgálja meg az állapotát, és a végpont nem szerepel a DNS-válaszban.

### <a name="profile-status"></a>Profil állapota

A profil állapota beállítás használatával engedélyezheti vagy letilthatja az adott profilt. Míg a végpont állapota egyetlen végpontra is hatással van, a profil állapota hatással van a teljes profilra, beleértve az összes végpontot is. Ha letilt egy profilt, a rendszer nem ellenőrzi a végpontokat, és nem tartalmaz végpontokat a DNS-válaszban. A DNS-lekérdezéshez egy [NXDOMAIN](https://tools.ietf.org/html/rfc2308) -hibakódot ad vissza.

### <a name="endpoint-monitor-status"></a>Endpoint monitor állapota

A Endpoint monitor állapota egy Traffic Manager által generált érték, amely a végpont állapotát jeleníti meg. Ez a beállítás nem módosítható manuálisan. A végpont-figyelő állapota a végpontok figyelésének és a beállított végpont állapotának kombinációja. A Endpoint monitor állapotának lehetséges értékei a következő táblázatban láthatók:

| Profil állapota | Végpont állapota | Endpoint monitor állapota | Jegyzetek |
| --- | --- | --- | --- |
| Letiltva |Engedélyezve |Inaktív |A profil le lett tiltva. Bár a végpont állapota engedélyezve van, a profil állapota (letiltva) elsőbbséget élvez. A letiltott profilokban lévő végpontok nincsenek figyelve. A DNS-lekérdezéshez egy NXDOMAIN-hibakódot ad vissza. |
| &lt;bármelyik&gt; |Letiltva |Letiltva |A végpont le van tiltva. A letiltott végpontok figyelése nem történik meg. A végpont nem része a DNS-válaszoknak, ezért nem kap forgalmat. |
| Engedélyezve |Engedélyezve |Online |A végpont figyelése és állapota Kifogástalan. A DNS-válaszok részét képezi, és képes fogadni a forgalmat. |
| Engedélyezve |Engedélyezve |Csökkentett teljesítményű |A végpont-figyelés állapotának ellenőrzése sikertelen. A végpont nem szerepel a DNS-válaszokban, és nem kap forgalmat. <br>Ez alól kivételt képez, ha az összes végpontot csökkentik, és ebben az esetben a lekérdezési válaszban az összeset visszaadja a rendszer.</br>|
| Engedélyezve |Engedélyezve |CheckingEndpoint |A rendszer figyeli a végpontot, de az első mintavétel eredményei még nem érkeztek meg. A CheckingEndpoint olyan ideiglenes állapot, amely általában közvetlenül a profilhoz tartozó végpont hozzáadása vagy engedélyezése után azonnal megtörténik. Ebben az állapotban egy végpont szerepel a DNS-válaszokban, és képes fogadni a forgalmat. |
| Engedélyezve |Engedélyezve |Leállítva |Az a webalkalmazás, amelyre a végpont mutat, nem fut. Keresse meg a webalkalmazás beállításait. Ez akkor is előfordulhat, ha a végpont beágyazott végpont típusú, és a gyermek profil le van tiltva vagy inaktív. <br>A leállított állapotú végpontok nem figyelhetők meg. Nem szerepel a DNS-válaszokban, és nem kap forgalmat. Ez alól kivételt képez, ha az összes végpontot csökkentik, és ebben az esetben a rendszer a lekérdezési válaszban visszaadottnak tekinti őket.</br>|

További információ a végpontok figyelő állapotának a beágyazott végpontokra való kiszámításáról: [beágyazott Traffic Manager profilok](traffic-manager-nested-profiles.md).

>[!NOTE]
> Ha a webalkalmazás nem a standard vagy újabb rendszerű, akkor előfordulhat, hogy a leállított Endpoint monitor állapota App Service. További információ: [Traffic Manager Integration with app Service](/azure/app-service/web-sites-traffic-manager).

### <a name="profile-monitor-status"></a>Profil figyelő állapota

A Profil figyelő állapota a konfigurált profil állapotának és a végpontok figyelő állapotának kombinációja az összes végpontnál. A lehetséges értékeket a következő táblázat ismerteti:

| Profil állapota (konfiguráltként) | Endpoint monitor állapota | Profil figyelő állapota | Jegyzetek |
| --- | --- | --- | --- |
| Letiltva |&lt;bármely &gt; vagy egy profil, amely nem rendelkezik definiált végpontokkal. |Letiltva |A profil le lett tiltva. |
| Engedélyezve |Legalább egy végpont állapota csökken. |Csökkentett teljesítményű |Tekintse át az egyes végpontok állapotának értékeit annak meghatározásához, hogy mely végpontok igényelnek további figyelmet. |
| Engedélyezve |Legalább egy végpont állapota online állapotban van. A végpontok nem rendelkeznek csökkentett teljesítményű állapottal. |Online |A szolgáltatás fogadja a forgalmat. Nincs szükség további műveletekre. |
| Engedélyezve |Legalább egy végpont állapota CheckingEndpoint. Nem található végpont online vagy csökkentett teljesítményű állapotban. |CheckingEndpoints |Ez az átmeneti állapot akkor fordul elő, ha a profilt létrehozták vagy engedélyezik. A végpont állapota első alkalommal van bejelölve. |
| Engedélyezve |A profilban lévő összes végpont állapota letiltva vagy leállítva, vagy a profil nem rendelkezik meghatározott végpontokkal. |Inaktív |Nincsenek aktív végpontok, de a profil továbbra is engedélyezve van. |

## <a name="endpoint-failover-and-recovery"></a>Végpont feladatátvétele és helyreállítása

Traffic Manager rendszeresen ellenőrzi az összes végpont állapotát, beleértve a nem kifogástalan állapotú végpontokat is. Traffic Manager észleli, ha egy végpont Kifogástalan állapotba kerül, és visszakerül a forgásba.

A végpont állapota nem kifogástalan, ha a következő események bármelyike előfordul:

- Ha a figyelési protokoll HTTP vagy HTTPS:
    - Nem 200 válasz, vagy olyan válasz, amely nem tartalmazza a **várt állapotkód-tartományokban** megadott állapotjelző tartományt (beleértve egy másik 2xx-kódot vagy egy 301/302-átirányítást).
- Ha a figyelési protokoll TCP: 
    - A Traffic Manager által küldött SYN-kérelemre válaszul az ACK vagy a SYN-ACK típusú válasz érkezik a kapcsolódási létesítmény megkísérlése érdekében.
- Időtúllépés. 
- A végpontot eredményező egyéb kapcsolati probléma nem érhető el.

A sikertelen ellenőrzések hibaelhárításával kapcsolatos további információkért lásd: az [Azure-Traffic Manager csökkentett teljesítményű állapotának elhárítása](traffic-manager-troubleshooting-degraded.md). 

Az alábbi ábrán látható idősor részletesen leírja Traffic Manager végpont figyelési folyamatát, amely a következő beállításokkal rendelkezik: a figyelési protokoll a HTTP, a szondázás időköze 30 másodperc, a tolerált hibák száma 3, az időtúllépési érték 10 másodperc, a DNS-TTL pedig 30 másodperc.

![Traffic Manager végpont feladatátvételi és feladat-visszavételi folyamata](./media/traffic-manager-monitoring/timeline.png)

**Ábra: a Traffic Manager-végpont feladatátvételi és helyreállítási folyamata**

1. **Beolvasás**. Az egyes végpontok esetében a Traffic Manager monitorozási rendszer a figyelési beállítások között megadott elérési úton hajt végre GET kérelmet.
2. **200 OK vagy egyéni kód megadott tartomány Traffic Manager profil figyelési beállításai** . A figyelési rendszer a HTTP 200 OK vagy az vagy az egyéni kód megadott tartományát várja, Traffic Manager a profil figyelési beállításainak üzenetét 10 másodpercen belül vissza kell adni. Amikor megkapja ezt a választ, felismeri, hogy a szolgáltatás elérhető.
3. **30 másodperc az ellenőrzések között**. A végpont állapotának ellenőrzését 30 másodpercenként megismétli.
4. A **szolgáltatás nem érhető el**. A szolgáltatás elérhetetlenné válik. A Traffic Manager nem fogja tudni a következő állapot-ellenőrzését.
5. **Megpróbál hozzáférni a figyelési útvonalhoz**. A figyelési rendszer végrehajt egy GET kérelmet, de nem kap választ a 10 másodperces időkorláton belül (másik lehetőségként nem 200 választ kaphat). Ezután három alkalommal próbálkozik, 30 másodperces időközönként. Ha a próbálkozások egyike sikeres, akkor a próbálkozások száma alaphelyzetbe áll.
6. **Az állapot értéke csökkentett teljesítményű**. A negyedik egymást követő hiba után a figyelési rendszer a nem elérhető végpontok állapotát lecsökkentettként jelöli meg.
7. **A forgalom más végpontokra van átirányítva**. A Traffic Manager DNS-névkiszolgálók frissülnek, és Traffic Manager a továbbiakban nem adja vissza a végpontot a DNS-lekérdezésekre adott válaszként. Az új kapcsolatok más, elérhető végpontokra vannak irányítva. A végpontot tartalmazó korábbi DNS-válaszokat azonban továbbra is gyorsítótárazhatja a rekurzív DNS-kiszolgálók és a DNS-ügyfelek. Az ügyfelek továbbra is a végpontot használják, amíg a DNS-gyorsítótár lejár. Ahogy a DNS-gyorsítótár lejár, az ügyfelek új DNS-lekérdezéseket végeznek, és különböző végpontokra irányítják őket. A gyorsítótár időtartamát a Traffic Manager profil TTL beállítása vezérli, például 30 másodperc.
8. Az **állapot-ellenőrzések folytatódnak**. Traffic Manager továbbra is a végpont állapotát vizsgálja, miközben az állapota csökkentett. A Traffic Manager észleli, ha a végpont állapota állapotba kerül.
9. A **szolgáltatás ismét online állapotba kerül**. A szolgáltatás elérhetővé válik. A végpont megőrzi a csökkentett teljesítményű állapotát Traffic Manager amíg a figyelési rendszer elvégzi a következő állapot-ellenőrzést.
10. **A szolgáltatás felé irányuló forgalom folytatódik**. Traffic Manager elküld egy GET-kérést, és 200 OK állapotú választ kap. A szolgáltatás kifogástalan állapotba került. A Traffic Manager névkiszolgálók frissülnek, és a DNS-válaszokban elkezdik kiadni a szolgáltatás DNS-nevét. A forgalom visszaadja a végpontnak a többi végpontot visszaadó gyorsítótárazott DNS-válaszokat, és a többi végponthoz való meglévő kapcsolatokat leállítja.

    > [!NOTE]
    > Mivel Traffic Manager a DNS szintjén működik, az nem befolyásolhatja a meglévő kapcsolatokat egyetlen végpontra sem. Ha a végpontok közötti forgalmat irányítja (a Profilbeállítások vagy a feladatátvétel vagy a feladat-visszavétel során), Traffic Manager az új kapcsolatokat az elérhető végpontokhoz irányítja. Más végpontok azonban továbbra is fogadhatnak forgalmat a meglévő kapcsolatokon keresztül, amíg a munkamenetek le nem állnak. Ha engedélyezni szeretné, hogy a forgalom leálljon a meglévő kapcsolatokból, az alkalmazásoknak korlátoznia kell az egyes végpontok által használt munkamenet időtartamát.

## <a name="traffic-routing-methods"></a>Forgalom – útválasztási módszerek

Ha egy végpont csökkentett teljesítményű állapotú, a DNS-lekérdezésekre adott válaszban már nem lesz visszaadva. Ehelyett egy másik végpont lesz kiválasztva és visszaadva. A profilban konfigurált forgalom-útválasztási módszer határozza meg, hogy az alternatív végpont hogyan legyen kiválasztva.

* **Prioritás**. A végpontok rangsorolt listát alkotnak. Mindig a lista első elérhető végpontját adja vissza a rendszer. Ha egy végpont állapota csökkentett, akkor a rendszer a következő elérhető végpontot adja vissza.
* **Súlyozott**. Minden elérhető végpont véletlenszerűen van kiválasztva a hozzárendelt súlyok és a többi elérhető végpont súlyozása alapján.
* **Teljesítmény**. A rendszer a végfelhasználóhoz legközelebb eső végpontot adja vissza. Ha a végpont nem érhető el, Traffic Manager áthelyezi a forgalmat a következő legközelebbi Azure-régióban lévő végpontokra. Alternatív feladatátvételi terveket konfigurálhat a teljesítmény forgalmához – az útválasztást [beágyazott Traffic Manager profilok](traffic-manager-nested-profiles.md#example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region)használatával végezheti el.
* **Földrajzi**hely. A rendszer a lekérdezési kérelem IP-címe alapján a földrajzi hely kiszolgálására leképezett végpontot adja vissza. Ha ez a végpont nem érhető el, egy másik végpont nem lesz kiválasztva a feladatátvételhez, mert a földrajzi hely csak egy profil egy végpontján képezhető le (a további részletek a [Gyakori kérdések](traffic-manager-FAQs.md#traffic-manager-geographic-traffic-routing-method)között találhatók). Az ajánlott eljárás a földrajzi útválasztás használata esetén azt javasoljuk, hogy az ügyfelek a profil végpontjait több végponttal rendelkező beágyazott Traffic Manager-profilokat használjanak.
* **MultiValue** Többértékű A rendszer több IPv4-/IPv6-címhez hozzárendelt végpontot ad vissza. Ha egy lekérdezés érkezik ehhez a profilhoz, a rendszer visszaadja az egészséges végpontokat a megadott válasz értékének **maximális száma** alapján. A válaszok alapértelmezett száma két végpont.
* **Alhálózat** A rendszer az IP-címtartományok számára leképezett végpontot adja vissza. Ha egy kérés érkezik az adott IP-címről, a visszaadott végpont az adott IP-címhez hozzárendelt végpont. 

További információ: [Traffic Manager Traffic-Routing metódusok](traffic-manager-routing-methods.md).

> [!NOTE]
> Egy kivétel a normál forgalomhoz – az útválasztási viselkedés akkor fordul elő, ha az összes jogosult végpont csökkentett állapotú. Traffic Manager a "legjobb megoldás" kísérletet, és *úgy reagál, mintha az összes csökkentett teljesítményű állapot végpontja online állapotban van*. Ez a viselkedés olyan alternatíva, amely a DNS-válaszban nem ad vissza végpontot. A letiltott vagy leállított végpontok nincsenek figyelve, ezért nem minősülnek forgalomnak.
>
> Ezt az állapotot általában a szolgáltatás helytelen konfigurációja okozhatja, például:
>
> * Egy hozzáférés-vezérlési lista [ACL] blokkolja a Traffic Manager állapot-ellenőrzéseket.
> * A figyelési port vagy protokoll helytelen konfigurációja a Traffic Manager-profilban.
>
> Ennek a viselkedésnek az a következménye, hogy ha a Traffic Manager állapot-ellenőrzés helytelenül van konfigurálva, akkor előfordulhat, hogy a forgalom útválasztása úgy tűnik, mintha *a Traffic Manager megfelelően* működik. Ebben az esetben azonban a végpont feladatátvétele nem fordulhat elő, amely az alkalmazások teljes rendelkezésre állását befolyásolja. Fontos annak ellenőrzését, hogy a profil egy online állapotot mutat-e, nem csökkentett teljesítményű állapotot. Az online állapot azt jelzi, hogy a Traffic Manager állapot ellenőrzése a várt módon működik.

További információ a sikertelen állapot-ellenőrzésekkel kapcsolatos hibák elhárításáról: az [Azure-Traffic Manager csökkentett teljesítményű állapotának elhárítása](traffic-manager-troubleshooting-degraded.md).

## <a name="faqs"></a>Gyakori kérdések

* [Rugalmasan Traffic Manager az Azure-régiók hibáira?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-traffic-manager-resilient-to-azure-region-failures)

* [Hogyan befolyásolja az erőforráscsoport helyének megválasztása a Traffic Manager?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-the-choice-of-resource-group-location-affect-traffic-manager)

* [Hogyan határozza meg az egyes végpontok aktuális állapotát?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-do-i-determine-the-current-health-of-each-endpoint)

* [Figyelhető a HTTPS-végpontok?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-monitor-https-endpoints)

* [Egy végpont hozzáadásakor használhatok IP-címet vagy DNS-nevet?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-use-an-ip-address-or-a-dns-name-when-adding-an-endpoint)

* [Milyen típusú IP-címeket használhatok a végpontok hozzáadásakor?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-types-of-ip-addresses-can-i-use-when-adding-an-endpoint)

* [Használhatok különböző végpont-címzési típusokat egyetlen profilon belül?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-different-endpoint-addressing-types-within-a-single-profile)

* [Mi történik, ha egy bejövő lekérdezés bejegyzéstípusa eltér a végpontok címzési típusához társított bejegyzéstípustól?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-happens-when-an-incoming-querys-record-type-is-different-from-the-record-type-associated-with-the-addressing-type-of-the-endpoints)

* [Használhatok egy beágyazott profil IPv4/IPv6-beli végpontját használó profilt?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-a-profile-with-ipv4--ipv6-addressed-endpoints-in-a-nested-profile)

* [Leállítottam egy webalkalmazás-végpontot a Traffic Manager-profilban, de nem kapok forgalmat még az újraindítást követően sem. Hogyan javíthatom ezt?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#i-stopped-an-web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this)

* [Használhatom Traffic Manager még akkor is, ha az alkalmazás nem támogatja a HTTP-t vagy a HTTPS-t?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https)

* [Milyen válaszokra van szükség a végponttól a TCP-figyelés használatakor?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring)

* [Milyen gyorsan Traffic Manager áthelyezni a felhasználókat egy nem kifogástalan állapotú végpontról?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint)

* [Hogyan adhatok meg különböző figyelési beállításokat a különböző végpontokhoz egy profilban?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile)

* [Hogyan oszthatok ki HTTP-fejléceket a Traffic Manager állapot-ellenőrzésekhez a saját végpontokhoz?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-assign-http-headers-to-the-traffic-manager-health-checks-to-my-endpoints)

* [Milyen állomásfejléc-ellenőrzést használ a rendszer a végpontok állapotának ellenőrzéséhez?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-host-header-do-endpoint-health-checks-use)

* [Mik azok az IP-címek, amelyekről az állapot-ellenőrzések származnak?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-the-ip-addresses-from-which-the-health-checks-originate)

* [Hány állapot-ellenőrzést várhatok a végpontom Traffic Manager?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager)

* [Hogyan kaphatok értesítést, ha az egyik végpontom leáll?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-get-notified-if-one-of-my-endpoints-goes-down)

## <a name="next-steps"></a>További lépések

Tudnivalók a [Traffic Manager működéséről](traffic-manager-how-it-works.md)

További információ a Traffic Manager által támogatott [forgalom-útválasztási módszerekről](traffic-manager-routing-methods.md)

Megtudhatja, hogyan [hozhat létre Traffic Manager-profilt](traffic-manager-manage-profiles.md)

Traffic Manager végponton található [csökkentett teljesítményű állapot hibáinak megoldása](traffic-manager-troubleshooting-degraded.md)
