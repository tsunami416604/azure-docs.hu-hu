---
title: Azure Traffic Manager végpontfigyelés | Microsoft dokumentumok
description: Ez a cikk segítséget nyújt annak megértésében, hogy a Traffic Manager hogyan használja a végpontfigyelést és az automatikus végpontfeladat-átvételt, hogy segítse az Azure-ügyfeleket a magas rendelkezésre állású alkalmazások üzembe helyezésében.
services: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2018
ms.author: rohink
ms.openlocfilehash: fcc9c5333b37c041342c2d20a53cf5d3908d1a26
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938562"
---
# <a name="traffic-manager-endpoint-monitoring"></a>Traffic Manager-végpontmonitorozás

Az Azure Traffic Manager beépített végpontfigyelést és automatikus végpontfeladat-átvételt tartalmaz. Ez a funkció segít a végponthibákkal szemben rugalmas, magas rendelkezésre állású alkalmazások biztosításában, beleértve az Azure-régió hibáit is.

## <a name="configure-endpoint-monitoring"></a>Végpontfigyelés konfigurálása

A végpontfigyelés konfigurálásához a következő beállításokat kell megadnia a Traffic Manager-profilon:

* **protokollt**. Válassza a HTTP, HTTPS vagy TCP protokollt, amelyet a Traffic Manager használ a végpont vizsgálatakor az állapotának ellenőrzéséhez. A HTTPS-figyelés nem ellenőrzi, hogy az SSL-tanúsítvány érvényes-e, csak azt ellenőrzi, hogy a tanúsítvány jelen van-e.
* **Port .** Válassza ki a kérelemhez használt portot.
* **Elérési út**. Ez a konfigurációs beállítás csak a HTTP és HTTPS protokollokra érvényes, amelyekhez meg kell adni az elérési út beállítását. Ha ezt a beállítást adja meg a TCP figyelési protokollhoz, az hibát eredményez. HTTP és HTTPS protokoll esetén adja meg a relatív elérési utat és annak a weblapnak vagy fájlnak a nevét, amelyhez a figyelés hozzáfér. A perjel (/) a relatív elérési út érvényes bejegyzése. Ez az érték azt jelenti, hogy a fájl a gyökérkönyvtárban van (alapértelmezett).
* **Egyéni fejlécbeállítások** Ezzel a konfigurációs beállítással adott HTTP-fejléceket adhat hozzá a Traffic Manager által a profil alatti végpontok számára küldött állapotellenőrzésekhez. Az egyéni fejlécek profilszinten adhatók meg, hogy az adott profil összes végpontjára és / vagy csak az adott végpontra vonatkozó végpontokra alkalmazhatók legyenek. Az egyéni fejlécek használatával állapot-ellenőrzések végpontok egy több-bérlős környezetben megfelelően irányítható a cél egy állomás fejlécét. Ezt a beállítást egyedi fejlécek hozzáadásával is használhatja, amelyek a Traffic Manager által származó HTTP(S) kérések azonosítására használhatók, és eltérő módon dolgozza fel azokat. Legfeljebb nyolc fejléc:értékpárt adhat meg vesszővel seprated. Például a "header1:value1,header2:value2". 
* **Várt állapotkód-tartományok** Ezzel a beállítással több sikerkódtartományt adhat meg 200-299, 301-301 formátumban. Ha ezek az állapotkódok válaszként érkeznek egy végpontról az állapot-ellenőrzés indításakor, a Traffic Manager ezeket a végpontokat kifogástalanként jelöli meg. Legfeljebb 8 állapotkód-tartományt adhat meg. Ez a beállítás csak a HTTP és HTTPS protokollra és az összes végpontra vonatkozik. Ez a beállítás a Traffic Manager profil szintjén van, és alapértelmezés szerint a 200 érték a sikerességi állapotkód.
* **A szondázási időköz**. Ez az érték azt határozza meg, hogy milyen gyakran ellenőrzi a rendszer a végpont állapotát egy Traffic Manager-vizsgáló ügynöktől. Itt két értéket adhat meg: 30 másodperc (normál szondázás) és 10 másodperc (gyors vizsgálat). Ha nincs megadva érték, a profil értéke 30 másodperc. Látogasson el a [Traffic Manager díjszabási oldalára,](https://azure.microsoft.com/pricing/details/traffic-manager) ahol többet tudhat meg a gyors prognosztikai díjakról.
* **A hibák tolerálható száma**. Ez az érték azt adja meg, hogy a Traffic Manager-probing ügynök hány hibát tűr, mielőtt a végpontot nem kifogástalanként jelölné meg. Értéke 0 és 9 között lehet. A 0 érték azt jelenti, hogy egyetlen figyelési hiba azt eredményezheti, hogy a végpont nem megfelelő állapotúként van megjelölve. Ha nincs megadva érték, akkor a 3-as alapértelmezett értéket használja.
* **A mintavétel időmeghosszabbítása**. Ez a tulajdonság azt adja meg, hogy a Traffic Manager-ellenőrző ügynöknek mennyi ideig kell várnia, mielőtt figyelembe venné, hogy az állapot-ellenőrző mintavétel a végpontra küldésekor ellenőrizze a hibát. Ha a Probing Interval értéke 30 másodperc, akkor az időtúltöltés értékét 5 és 10 másodperc között állíthatja be. Ha nincs megadva érték, akkor 10 másodperces alapértelmezett értéket használ. Ha a Probing Interval értéke 10 másodperc, akkor az időtúltöltés értékét 5 és 9 másodperc között állíthatja be. Ha nincs megadva időtúlesély érték, akkor az alapértelmezett érték 9 másodperc.

    ![Traffic Manager-végpontmonitorozás](./media/traffic-manager-monitoring/endpoint-monitoring-settings.png)

    **Ábra: Traffic Manager végpontfigyelés**

## <a name="how-endpoint-monitoring-works"></a>A végpontfigyelés működése

Ha a figyelési protokoll HTTP vagy HTTPS protokollként van beállítva, a Traffic Manager probing ügynök get-kérelmet küld a végpontnak a megadott protokoll, port és relatív elérési út használatával. Ha egy 200 OK választ kap vissza, vagy a **Várt állapotkód-tartományokban \*** konfigurált válaszok bármelyikét, akkor ez a végpont kifogástalannak minősül. Ha a válasz egy másik érték, vagy ha nem érkezik válasz a megadott időbeli határidőn belül, akkor a Traffic Manager probing ügynök újra megkísérli a Tolerálható hibák száma beállítás szerint (nincs újrapróbálkozás, ha ez a beállítás 0). Ha az egymást követő hibák száma magasabb, mint a Hibák tolerálható száma beállítás, akkor a végpont nem kifogástalanként van megjelölve. 

Ha a figyelési protokoll TCP, a Traffic Manager ellenőrző ügynök tcp-kapcsolati kérelmet kezdeményez a megadott port használatával. Ha a végpont válaszol a kérésre a kapcsolat létrehozásához adott válaszsal, az állapot-ellenőrzés sikeresként van megjelölve, és a Traffic Manager ellenőrző ügynök alaphelyzetbe állítja a TCP-kapcsolatot. Ha a válasz egy másik érték, vagy ha nem érkezik válasz a megadott időbeli megadott időn belül, a Traffic Manager probing ügynök újra kísérletek szerint a Tolerálható hibák száma beállítás (nem történik újra kísérletek, ha ez a beállítás 0). Ha az egymást követő hibák száma magasabb, mint a Hibák tolerálható száma beállítás, akkor a végpont nem kifogástalannak van jelölve.

A Traffic Manager minden esetben több helyről vizsgálja meg a mintavételeket, és az egymást követő hibameghatározás az egyes régiókon belül történik. Ez azt is jelenti, hogy a végpontok a Traffic Manager től nagyobb gyakorisággal kapnak állapotminta, mint a probing interval használt beállítás.

>[!NOTE]
>HTTP vagy HTTPS figyelési protokoll esetén a végponti oldalon általános gyakorlat egy egyéni lap megvalósítása az alkalmazáson belül – például /health.aspx. Ezzel az elérési úttal figyelheti meg, alkalmazásspecifikus ellenőrzéseket hajthat végre, például teljesítményszámlálók ellenőrzését vagy az adatbázis elérhetőségének ellenőrzését. Ezen egyéni ellenőrzések alapján a lap megfelelő HTTP-állapotkódot ad vissza.

A Traffic Manager-profil összes végpontja megosztja a figyelési beállításokat. Ha különböző figyelési beállításokat kell használnia a különböző végpontokhoz, [egymásba ágyazott Traffic Manager-profilokat](traffic-manager-nested-profiles.md#example-5-per-endpoint-monitoring-settings)hozhat létre.

## <a name="endpoint-and-profile-status"></a>Végpont és profil állapota

A Traffic Manager-profilokat és végpontokat engedélyezheti és letilthatja. A végpont állapotának változása azonban a Traffic Manager automatikus beállításai nak és folyamatainak eredményeként is előfordulhat.

### <a name="endpoint-status"></a>Végpont állapota

Egy adott végpontot engedélyezhet vagy letilthatjon. Az alapul szolgáló szolgáltatás, amely még mindig kifogástalan állapotú, nem érinti. A végpont állapotának módosítása szabályozza a végpont elérhetőségét a Traffic Manager-profilban. Ha egy végpont állapota le van tiltva, a Traffic Manager nem ellenőrzi annak állapotát, és a végpont nem szerepel a DNS-válaszban.

### <a name="profile-status"></a>Profil állapota

A profil állapotbeállításával engedélyezheti vagy letilthatja az adott profilt. Míg a végpont állapota egyetlen végpontot érint, a profil állapota a teljes profilra hatással van, beleértve az összes végpontot is. Ha letilt egy profilt, a rendszer nem ellenőrzi a végpontokat az állapot szempontjából, és a DNS-válasz nem tartalmaz végpontokat. A [DNS-lekérdezéshez NXDOMAIN](https://tools.ietf.org/html/rfc2308) válaszkódot ad vissza.

### <a name="endpoint-monitor-status"></a>Végpontfigyelő állapota

A végpontfigyelő állapota egy Traffic Manager által létrehozott érték, amely a végpont állapotát mutatja. Ez a beállítás nem módosítható manuálisan. A végpontfigyelő állapota a végpontfigyelés és a beállított végpontállapot eredményeinek kombinációja. A végpontfigyelő állapotának lehetséges értékei az alábbi táblázatban láthatók:

| Profil állapota | Végpont állapota | Végpontfigyelő állapota | Megjegyzések |
| --- | --- | --- | --- |
| Letiltva |Engedélyezve |Inaktív |A profil le van tiltva. Bár a végpont állapota engedélyezve van, a profil állapota (Letiltva) elsőbbséget élvez. A letiltott profilok végpontjait a rendszer nem figyeli. A DNS-lekérdezéshez NXDOMAIN válaszkódot ad vissza. |
| &lt;bármelyik&gt; |Letiltva |Letiltva |A végpont le van tiltva. A letiltott végpontok nem figyelhetők. A végpont nem szerepel a DNS-válaszokban, ezért nem kap forgalmat. |
| Engedélyezve |Engedélyezve |Online |A végpont figyelt és kifogástalan állapotú. Ez szerepel a DNS-válaszok, és képes fogadni a forgalmat. |
| Engedélyezve |Engedélyezve |Csökkentett teljesítményű |Végpont figyelése állapotellenőrzések sikertelenek. A végpont nem szerepel a DNS-válaszokban, és nem kap forgalmat. <br>Ez alól kivételt képez, ha az összes végpont leromlott, amely esetben mindegyik a lekérdezési válaszban visszaadottnak minősül).</br>|
| Engedélyezve |Engedélyezve |Végpont ellenőrzése |A végpont ot figyeli a rendszer, de az első mintavétel eredményei még nem érkeztek meg. CheckingEndpoint egy ideiglenes állapot, amely általában akkor jelentkezik, közvetlenül hozzáadása után, vagy engedélyezve egy végpontot a profilban. Egy végpont ebben az állapotban szerepel a DNS-válaszok, és forgalmat fogadhat. |
| Engedélyezve |Engedélyezve |Leállítva |A végpont által mutatott webalkalmazás nem fut. Ellenőrizze a webalkalmazás beállításait. Ez akkor is előfordulhat, ha a végpont beágyazott végpont, és a gyermekprofil le van tiltva vagy inaktív. <br>Egy Leállított állapotú végpont ot nem figyel a rendszer. Nem szerepel a DNS-válaszokban, és nem kap forgalmat. Ez alól kivételt képez, ha az összes végpont leromlott, amely esetben mindegyik a lekérdezési válaszban visszaadottnak minősül.</br>|

A végpontfigyelő állapotának a beágyazott végpontok kiszámításáról a [beágyazott Traffic Manager-profilok](traffic-manager-nested-profiles.md)című témakörben talál részleteket.

>[!NOTE]
> A Leállított végpont figyelő állapota akkor fordulhat elő az App Service-ben, ha a webalkalmazás nem fut a standard szinten vagy annál magasabb. További információ: [Traffic Manager integration with App Service.](/azure/app-service/web-sites-traffic-manager)

### <a name="profile-monitor-status"></a>Profilfigyelő állapota

A profilfigyelő állapota a beállított profil állapotának és a végpontfigyelő összes végpontállapot-értékeinek kombinációja. A lehetséges értékeket a következő táblázat ismerteti:

| Profil állapota (konfigurálva) | Végpontfigyelő állapota | Profilfigyelő állapota | Megjegyzések |
| --- | --- | --- | --- |
| Letiltva |&lt;bármely&gt; vagy meghatározott végpont nélküli profil. |Letiltva |A profil le van tiltva. |
| Engedélyezve |Legalább egy végpont állapota Leromlott. |Csökkentett teljesítményű |Tekintse át az egyes végpontok állapotértékeit annak meghatározásához, hogy mely végpontok igényelnek további figyelmet. |
| Engedélyezve |Legalább egy végpont állapota Online. Egyetlen végpont nak sincs leromlott állapota. |Online |A szolgáltatás elfogadja a forgalmat. Nincs szükség további műveletekre. |
| Engedélyezve |Legalább egy végpont állapota CheckingEndpoint. Nincsenek végpontok online vagy degradálódott állapotban. |Végpontok ellenőrzése |Ez az átmeneti állapot akkor fordul elő, ha egy profilt létrehoznak vagy engedélyeznek. A végpont állapota első alkalommal van ellenőrizve. |
| Engedélyezve |A profil összes végpontjának állapota letiltva vagy Leállítva, vagy a profilnak nincsenek meghatározott végpontjai. |Inaktív |Nincsenek aktív végpontok, de a profil továbbra is engedélyezve van. |

## <a name="endpoint-failover-and-recovery"></a>Végpontfeladat-átvétel és helyreállítás

Traffic Manager rendszeresen ellenőrzi az állapotát minden végpont, beleértve a nem megfelelő állapotú végpontok. A Traffic Manager észleli, ha egy végpont kifogástalan állapotúvá válik, és visszahozza azt az elforgatásba.

A végpont nem kifogástalan, ha az alábbi események bármelyike bekövetkezik:

- Ha a figyelési protokoll HTTP vagy HTTPS:
    - Nem 200-as válasz vagy olyan válasz érkezik, amely nem tartalmazza a **Várt állapotkód-tartományok** beállításban megadott állapottartományt (beleértve egy másik 2xx kódot vagy egy 301/302-es átirányítást).
- Ha a figyelési protokoll TCP: 
    - Az ACK-tól vagy a SYN-ACK-tól eltérő válasz érkezik a Traffic Manager által a kapcsolat létesítésére küldött SYN-kérésre.
- Időtúllépés. 
- Bármely más csatlakozási probléma, amely nek a végpont nem érhető el.

A sikertelen ellenőrzések hibaelhárításáról az [Azure Traffic Manager degradált állapotának hibaelhárítása című témakörben](traffic-manager-troubleshooting-degraded.md)talál további információt. 

Az alábbi ábrán látható idővonal a Traffic Manager-végpont figyelési folyamatának részletes leírása, amely a következő beállításokkal rendelkezik: a figyelési protokoll HTTP, a szondázási időköz 30 másodperc, a tolerálható hibák száma 3, az időtúllépések értéke 10 a DNS TTL pedig 30 másodperc.

![Traffic Manager-végpont feladatátvételi és feladat-visszavételi sorrendje](./media/traffic-manager-monitoring/timeline.png)

**Ábra: Traffic Manager végpont feladatátvételi és helyreállítási sorozat**

1. **KAP**. Minden végpont, a Traffic Manager figyelési rendszer egy GET-kérelmet hajt végre a figyelési beállításokban megadott elérési úton.
2. **200 OK vagy egyéni kódtartomány ban megadott Traffic Manager profil figyelési beállításokat** . A figyelési rendszer azt várja, hogy a HTTP 200 OK vagy a Traffic Manager-profilfigyelési beállítások üzenet10 másodpercen belül visszaadja a vagy egyéni kódtartományt. Amikor megkapja ezt a választ, felismeri, hogy a szolgáltatás elérhető.
3. **30 másodperc az ellenőrzések között.** A végpont állapot-ellenőrzése 30 másodpercenként ismétlődik.
4. **A szolgáltatás nem érhető el.** A szolgáltatás elérhetetlenné válik. Traffic Manager nem fogja tudni, amíg a következő állapot-ellenőrzés.
5. **Megkísérli elérni a figyelési útvonalat.** A felügyeleti rendszer get kérést hajt végre, de nem kap választ a 10 másodperces időtúlidőszakon belül (másik lehetőségként nem 200 válasz érkezhet). Ezután még háromszor próbálkozik, 30 másodperces időközönként. Ha az egyik próbálkozás sikeres, majd a próbálkozások száma alaphelyzetbe áll.
6. **Állapota Degraded**. Negyedik egymást követő hiba után a figyelési rendszer a nem elérhető végpont állapotát degradálva jelöli.
7. **A forgalom más végpontokra kerül.** A Traffic Manager DNS-névkiszolgálói frissülnek, és a Traffic Manager dns-lekérdezésekre válaszul már nem adja vissza a végpontot. Az új kapcsolatok más, elérhető végpontokra irányulnak. A végpontot tartalmazó korábbi DNS-válaszokat azonban a rekurzív DNS-kiszolgálók és a DNS-ügyfelek továbbra is gyorsítótárathatják. Az ügyfelek továbbra is használják a végpontot, amíg a DNS-gyorsítótár le nem jár. A DNS-gyorsítótár lejártakor az ügyfelek új DNS-lekérdezéseket végeznek, és különböző végpontokra irányítják őket. A gyorsítótár időtartamát a Traffic Manager-profil TTL-beállítása szabályozza, például 30 másodperc.
8. **Az egészségügyi ellenőrzések folytatódnak.** Traffic Manager továbbra is ellenőrzi a végpont állapotát, amíg az egy degradált állapot. A Traffic Manager észleli, ha a végpont visszatér az állapot.
9. **Szolgáltatás jön vissza online**. A szolgáltatás elérhetővé válik. A végpont megmarad a degradálódott állapot traffic manager, amíg a figyelőrendszer elvégzi a következő állapot-ellenőrzés.
10. **A szolgáltatásba irányuló forgalom folytatódik.** Traffic Manager küld get kérelmet, és kap egy 200 OK állapotválaszt. A szolgáltatás kifogástalan állapotba került. A Traffic Manager névkiszolgálói frissülnek, és elkezdik kimondani a szolgáltatás DNS-nevét a DNS-válaszokban. A forgalom visszatér a végpontra, mivel a más végpontokat visszaadó gyorsítótárazott DNS-válaszok lejárnak, és a más végpontokkal létesített kapcsolatok megszakadnak.

    > [!NOTE]
    > Mivel a Traffic Manager a DNS szintjén működik, nem befolyásolhatja a meglévő kapcsolatokat egyetlen végponthoz sem. Amikor irányítja a forgalmat a végpontok között (vagy a módosított profilbeállítások, vagy feladatátvétel vagy feladat-visszavétel során), Traffic Manager irányítja az új kapcsolatok at elérhető végpontok. Előfordulhat azonban, hogy más végpontok továbbra is fogadhatják a forgalmat a meglévő kapcsolatokon keresztül, amíg ezek a munkamenetek le nem állnak. Ahhoz, hogy a forgalom lefolyjon a meglévő kapcsolatokból, az alkalmazásoknak korlátozniuk kell az egyes végpontokhoz használt munkamenet-időtartamot.

## <a name="traffic-routing-methods"></a>Forgalom-útválasztási módszerek

Ha egy végpont állapota leromlott állapot, a dns-lekérdezésekre adott válaszként már nem ad vissza. Ehelyett egy másik végpontot választ ki, és visszaad. A profilban konfigurált forgalom-útválasztási módszer határozza meg az alternatív végpont kiválasztásának módját.

* **Prioritás**. A végpontok rangsorolt listát alkotnak. A lista első elérhető végpontja mindig visszakerül. Ha egy végpont állapota degradálva, majd a következő elérhető végpont ot adja vissza.
* **Súlyozott**. Minden rendelkezésre álló végpontvéletlenszerűen kerül kiválasztásra a hozzárendelt súlyok és a többi rendelkezésre álló végpont súlya alapján.
* **teljesítmény**. A végfelhasználóhoz legközelebb eső végpontot adja vissza. Ha ez a végpont nem érhető el, a Traffic Manager áthelyezi a forgalmat a legközelebbi Azure-régió végpontjaira. A teljesítményforgalom-továbbításhoz alternatív feladatátvételi terveket is konfigurálhat [beágyazott Traffic Manager-profilok](traffic-manager-nested-profiles.md#example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region)használatával.
* **Földrajzi**. A lekérdezési kérelem IP-címén alapuló földrajzi hely kiszolgálására leképezett végpontot adja vissza. Ha ez a végpont nem érhető el, egy másik végpont nem lesz kijelölve feladatátvételre, mivel egy földrajzi hely csak egy végponthoz képezhető le egy profilban (további részletek a [GYAKRAN FELTETT kérdésekben](traffic-manager-FAQs.md#traffic-manager-geographic-traffic-routing-method)találhatók). Ajánlott eljárásként a földrajzi útválasztás használatakor azt javasoljuk az ügyfeleknek, hogy a profil végpontjaiként több végpontot használó, beágyazott Traffic Manager-profilokat használjanak.
* **Többértékű** A rendszer több, IPv4/IPv6-címekre leképezett végpontot ad vissza. Amikor egy lekérdezés érkezik ehhez a profilhoz, a rendszer kifogástalan végpontokat ad vissza a megadott **válaszérték maximális rekordszáma** alapján. A válaszok alapértelmezett száma két végpont.
* **Alhálózat** Az IP-címtartományok készletéhez leképezett végpontot adja vissza. Amikor egy kérelem érkezik, hogy az IP-cím, a végpont vissza az adott IP-címhez leképezett. 

További információ: [Traffic Manager traffic-routing methods.](traffic-manager-routing-methods.md)

> [!NOTE]
> Egy kivétel a normál forgalom-útválasztási viselkedés akkor fordul elő, ha az összes jogosult végpontok egy leromlott állapot. Traffic Manager tesz egy "legjobb erőfeszítést" kísérlet, és *válaszol, mintha az összes degradált állapot végpontok ténylegesen egy online állapotban*. Ez a viselkedés előnyösebb, mint az alternatív, amely az lenne, hogy nem ad vissza egy végpontot a DNS-válasz. A letiltott vagy a leállított végpontok nem figyelhetők, ezért nem tekinthetők forgalomra jogosultnak.
>
> Ezt a feltételt általában a szolgáltatás nem megfelelő konfigurációja okozza, például:
>
> * Hozzáférés-vezérlési lista [ACL] blokkolja a Traffic Manager állapot-ellenőrzéseket.
> * A figyelési port vagy protokoll nem megfelelő konfigurációja a Traffic manager profilban.
>
> Ennek a viselkedésnek az a következménye, hogy ha a Traffic Manager állapotellenőrzései nincsenek megfelelően konfigurálva, akkor a forgalomútválasztásból úgy jelenhet meg, mintha a Traffic Manager megfelelően *működne.* Ebben az esetben azonban a végpontfeladat-átvétel nem történhet meg, amely hatással van az alkalmazás általános rendelkezésre állására. Fontos ellenőrizni, hogy a profil online, nem pedig degradálódott állapotot jelenít-e meg. Az online állapot azt jelzi, hogy a Traffic Manager állapotellenőrzése a várt módon működik.

A sikertelen állapotellenőrzések hibaelhárításáról az [Azure Traffic Manager degradálódott állapotának hibaelhárítása című témakörben](traffic-manager-troubleshooting-degraded.md)talál további információt.

## <a name="faqs"></a>Gyakori kérdések

* [A Traffic Manager rugalmas az Azure-régió hibáival szemben?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-traffic-manager-resilient-to-azure-region-failures)

* [Milyen hatással van az erőforráscsoport helyének kiválasztása a Forgalomkezelőre?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-the-choice-of-resource-group-location-affect-traffic-manager)

* [Hogyan állapítható meg az egyes végpontok aktuális állapotát?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-do-i-determine-the-current-health-of-each-endpoint)

* [Figyelhetem a HTTPS-végpontokat?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-monitor-https-endpoints)

* [Használok IP-címet vagy DNS-nevet végpont hozzáadásakor?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-use-an-ip-address-or-a-dns-name-when-adding-an-endpoint)

* [Milyen típusú IP-címeket használhatok végpont hozzáadásakor?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-types-of-ip-addresses-can-i-use-when-adding-an-endpoint)

* [Használhatok különböző végpontcímzési típusokat egyetlen profilon belül?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-different-endpoint-addressing-types-within-a-single-profile)

* [Mi történik, ha egy bejövő lekérdezés bejegyzéstípusa eltér a végpontok címzési típusához társított bejegyzéstípustól?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-happens-when-an-incoming-querys-record-type-is-different-from-the-record-type-associated-with-the-addressing-type-of-the-endpoints)

* [Használhatok profilt iPv4 / IPv6 címzett végpontokkal beágyazott profilban?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-a-profile-with-ipv4--ipv6-addressed-endpoints-in-a-nested-profile)

* [Leállítottam egy webalkalmazás végpontját a Traffic Manager-profilomban, de nem kapok forgalmat, még az újraindítás után sem. Hogyan tudom ezt helyrehozni?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#i-stopped-an-web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this)

* [Használhatom a Traffic Managert akkor is, ha az alkalmazásom nem támogatja a HTTP-t vagy a HTTPS-t?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https)

* [Milyen konkrét válaszok szükségesek a végponttól a TCP-figyelés használatakor?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring)

* [Milyen gyorsan mozgatja a Traffic Manager a felhasználókat egy nem megfelelő állapotú végpontról?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint)

* [Hogyan adhatok meg különböző figyelési beállításokat a profil különböző végpontjaihoz?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile)

* [Hogyan rendelhetek HTTP-fejléceket a Traffic Manager állapotellenőrzéseihez a végpontjaimhoz?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-assign-http-headers-to-the-traffic-manager-health-checks-to-my-endpoints)

* [Milyen állomásfejlécet használnak a végpont állapotellenőrzései?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-host-header-do-endpoint-health-checks-use)

* [Melyek azok az IP-címek, amelyekről az állapotvizsgálatok származnak?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-the-ip-addresses-from-which-the-health-checks-originate)

* [Hány állapotellenőrzést várhatok a Forgalomkezelőtől a végpontomon?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager)

* [Hogyan kaphatok értesítést, ha az egyik végpontom leáll?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-get-notified-if-one-of-my-endpoints-goes-down)

## <a name="next-steps"></a>További lépések

A [Traffic Manager működésének megismerése](traffic-manager-how-it-works.md)

További információ a Traffic Manager által támogatott [forgalom-útválasztási módszerekről](traffic-manager-routing-methods.md)

További információ a [Traffic Manager-profil létrehozásáról](traffic-manager-manage-profiles.md)

Forgalomkezelő-végpont [degradálódott állapotának – problémamegoldás](traffic-manager-troubleshooting-degraded.md)
