---
title: Gyakori kérdések az Azure Application Gateway
description: Ez az oldal nyújt az Azure Application Gatewayjel kapcsolatos gyakori kérdésekre adott válaszok
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 6/20/2018
ms.author: victorh
ms.openlocfilehash: b8b5b1da902a854dacea7e3019e3fa1116f04212
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399143"
---
# <a name="frequently-asked-questions-for-application-gateway"></a>Az Application Gateway gyakori kérdések

## <a name="general"></a>Általános kérdések

**Q. Mi az Application Gateway?**

Az Azure Application Gateway egy alkalmazás Vezérlőszolgáltatást (ADC) szolgáltatás, amely számos 7. rétegbeli terheléselosztási lehetőséget az alkalmazások. Magas rendelkezésre állású és skálázható szolgáltatás, amely az Azure teljes körűen felügyelt kínál.

**Q. Milyen funkciókat támogatja az Application Gateway?**

Az Application Gateway támogatja az SSL-kiürítés és teljes körű SSL, a webalkalmazási tűzfal, cookie-alapú munkamenet-affinitást, URL-cím-alapú útválasztást, többhelyes üzemeltetés és mások. Támogatott szolgáltatások teljes listájáért látogasson el [Application Gateway bemutatása](application-gateway-introduction.md)

**Q. Mi a különbség az Application Gateway és az Azure Load Balancer?**

Az Application Gateway egy 7. rétegbeli terheléselosztó, ami azt jelenti, és együttműködik a csak internetes forgalmat (HTTP/HTTPS/WebSocket). Támogatja a képességek, például SSL lezárást, cookie-alapú munkamenet-affinitást és Ciklikus időszeleteléses terheléselosztás a adatforgalom. Load Balancer, a jóváírásokat összefoglaló balances forgalom rétegben 4 (TCP/UDP).

**Q. Milyen protokollokat támogat az Application Gateway?**

Az Application Gateway támogatja a HTTP, HTTPS, HTTP/2 és WebSocket.

**Q. Hogyan támogatja az Application Gateway a HTTP/2?**

HTTP/2 protokoll támogatása csak a figyelők az Application Gateway-ügyfelekhez érhető el. A háttér-kiszolgálókészlethez kommunikációs HTTP/1.1 felett van. 

Alapértelmezés szerint a HTTP/2-támogatás le van tiltva. A következő Azure PowerShell-lel kódrészlet példakód bemutatja, hogyan tehető lehetővé, hogy:

```
$gw = Get-AzureRmApplicationGateway -Name test -ResourceGroupName hm
$gw.EnableHttp2 = $true
Set-AzureRmApplicationGateway -ApplicationGateway $gw
```

**Q. Milyen erőforrások jelenleg háttérkészlet részeként támogatottak?**

Háttérkészletek összeállítható a hálózati adapterek, a virtual machine scale sets, nyilvános IP-címek, belső IP-címek, teljesen minősített neve (FQDN), és több-bérlős háttéralkalmazások hasonlóan az Azure Web Apps. Application Gateway háttérkészlet érintett tagjai nem egy rendelkezésre állási csoporthoz vannak társítva. Háttérkészletek tagjai lehetnek, fürtök és adatközpontok között, vagy Azure-on kívül mindaddig, amíg az IP-kapcsolattal rendelkeznek.

**Q. Mely régiókban érhető el a szolgáltatást?**

Az Application Gateway az Azure globális minden régióban érhető el. Érhető el is [Azure China](https://www.azure.cn/) és [Azure Government](https://azure.microsoft.com/overview/clouds/government/)

**Q. Ez egy dedikált üzemelő példánya az előfizetésem vagy azt közösen használja ügyfelek?**

Application Gateway-példány dedikált központi telepítés a virtuális hálózaton.

**Q. A HTTP -> támogatott HTTPS-átirányítás?**

Átirányítás használata támogatott. Látogasson el [Application Gateway átirányítás áttekintése](application-gateway-redirect-overview.md) további.

**Q. Milyen sorrendben dolgozzák fel figyelői?**

Figyelők láthatók a rendelés feldolgozása történik. Ezért ha egy alapszintű figyelő megfelel egy bejövő kérésnek feldolgozza a először.  Többhelyes figyelőket konfigurálni kell egy alapszintű figyelő annak biztosítása érdekében az adatforgalmat a megfelelő háttér előtt.

**Q. Hol találom meg az Application Gateway IP- és DNS?**

Nyilvános IP-cím-végpontként használatakor ez az információ található a nyilvános IP-cím erőforrás, illetve az Áttekintés oldal az Application Gateway számára a portálon. A belső IP-címek ez található az Áttekintés oldalon.

**Q. Az IP- vagy DNS-változik az Application Gateway élettartama során?**

A VIP-címet módosíthatja, ha az átjáró leáll, majd indítja el az ügyfél. Az Application Gateway társított DNS nem módosítja az átjáró életciklusa. Ezért ajánlott egy CNAME-aliast használni, és az Application Gateway DNS-címére mutasson.

**Q. Az Application Gateway támogatja a statikus IP-cím?**

Nem, az Application Gateway nem támogatja a statikus nyilvános IP-címeket, de támogatja a statikus belső IP-címek.

**Q. Az Application Gateway támogatja a több nyilvános IP-címek az átjárón?**

Csak egy nyilvános IP-cím egy Application Gateway esetében támogatott.

**Q. Mekkora kell még saját alhálózatot az Application Gateway?**

Az Application Gateway egy példány egy magánhálózati IP-címet, valamint egy másik magánhálózati IP-címet használ fel, ha magánhálózati előtérbeli IP-konfiguráció van konfigurálva. Emellett az Azure lefoglalja az első négy és utolsó IP-cím mindegyik olyan alhálózatban, belső használatra.
Például, ha az Application Gateway három példányban, és nincs magánhálózati előtérbeli IP-címet, majd egy/29 méretű vagy nagyobb alhálózat van szükség. Ebben az esetben az Application Gateway három IP-címet használ. Ha rendelkezik három példányban és a egy IP-címet a magánhálózati előtérbeli IP-konfigurációhoz, majd egy/28-as méretet, vagy nagyobb alhálózat van szükség, mert négy IP-címeket kell megadni.

**Q. Az Application Gateway támogatja az x-továbbított – a fejlécek?**

Igen, az Application Gateway x – továbbított – a x továbbított protokollverzió és x továbbított port fejlécek szúr be a háttérrendszere továbbítja a kérelmet. Az x-továbbított – a fejléc formátuma IP:Port vesszővel tagolt listája. Az x továbbított proto érvényes értékei a következők: http vagy HTTPS protokollt. X továbbított port portot határozza meg, amelyen a kérelem eléri az Application gatewayben.

Az Application Gateway is tartalmaz az eredeti állomásfejlécet, amelyhez a kérés érkezett X-eredeti-Host-fejlécet szúr be. Ezt a fejlécet hasznos olyan szituációkra, mint az Azure-webhely-integráció, ahol a bejövő állomásfejléc módosította-e előtt az adatforgalmat, a háttérkiszolgáló.

**Q. Mennyi ideig tart egy Application Gateway üzembe helyezéséhez? Az Application Gateway továbbra is működik, ha a frissítés alatt?**

Új Application Gateway-telepítések esetén is igénybe vehet akár 20 percig kiépítése. Példány mérete és száma módosításai nem zavaró, és ez idő alatt az átjáró aktív marad.

## <a name="configuration"></a>Konfiguráció

**Q. Az Application Gateway mindig telepítve van a virtuális hálózaton?**

Igen, az Application Gateway mindig helyezünk üzembe a virtuális hálózat alhálózatán. Ez az alhálózat csak az Application Gateway átjárók tartalmazhat.

**Q. Az Application Gateway közötti kommunikáció a virtuális hálózatán kívüli példányok?**

Az Application Gateway, amely mindaddig, amíg nincs IP-kapcsolat van a virtuális hálózaton kívüli példányok közötti kommunikáció. Háttérkészlet-tagként belső IP-címek használatát tervezi, akkor a szükséges [virtuális hálózatok közötti társviszony-létesítés](../virtual-network/virtual-network-peering-overview.md) vagy [VPN-átjáró](../vpn-gateway/vpn-gateway-about-vpngateways.md).

**Q. Üzembe helyezhetem bármi más, az Application Gateway-alhálózat?**

Nem, de telepítheti az alhálózat más alkalmazásátjárók.

**Q. Hálózati biztonsági csoportok az Application Gateway-alhálózat támogatottak?**

Hálózati biztonsági csoportok az Application Gateway-alhálózat a következő korlátozásokkal támogatottak:

* Kivételek kell elhelyezni, a bejövő forgalom a portokon 65503 – 65534. Ezen a porttartományon szükség Azure-infrastruktúra kommunikációjához. A portokat Azure-tanúsítványok védik (zárják le). Megfelelő tanúsítványok nélkül a külső entitások – például az ügyfelek átjárók nem lesz a végpontokra módosításokat kezdeményezhetnek.

* Kimenő internetkapcsolattal nem lehet blokkolni.

* Az AzureLoadBalancer címkét a forgalmat engedélyezni kell.

**Q. Mik azok a korlátok Application Gateway-en? Növelheti ezeket a korlátokat?**

Látogasson el [Application Gateway korlátainak](../azure-subscription-service-limits.md#application-gateway-limits) korlátait megtekintéséhez.

**Q. Használható az Application Gateway belső és külső forgalom egyszerre?**

Igen, az Application Gateway támogatja az egy belső IP-cím és a egy külső IP-Címek száma az Application Gateway kellene.

**Q. Támogatott virtuális hálózatok közötti társviszony?**

Igen, virtuális hálózatok közötti társviszony támogatja, és más virtuális hálózatokhoz a forgalom terheléselosztásához előnyös.

**Q. Tudhatok a helyszíni kiszolgálók ExpressRoute vagy VPN-alagúton csatlakozáskor?**

Igen, mindaddig, amíg forgalom engedélyezve van.

**Q. Számos alkalmazás különböző portokat kiszolgálása egy háttérkészlet is van?**

Micro service-architektúra használata támogatott. Több http-beállítások különböző portokat mintavétel konfigurálva kell lennie.

**Q. Az egyéni minták támogatják a helyettesítő karakterekkel vagy reguláris kifejezést válasz adatokon?**

Az egyéni minták támogatja helyettesítő vagy reguláris kifejezést érkezett válasz adatait. 

**Q. Szabályok feldolgozásának módja?**

Szabályok feldolgozása a sorrendben vannak konfigurálva. Javasoljuk, hogy a többhelyes szabályok előtt alapvető szabályok csökkenti annak esélyét, hogy a forgalom lesz irányítva a nem megfelelő háttérrendszer és az alapszintű szabályt szeretne megegyeznek a forgalmat a port előtt a kiértékelt többhelyes szabály alapján vannak konfigurálva.

**Q. Mi a gazdagép mezőt az egyéni mintavételek jelölésére?**

A gazdagép mező a neve a mintavétel való küldéséhez. Alkalmazható csak akkor, ha a többhelyes konfigurálva van az Application Gatewayen, ellenkező esetben használja a "127.0.0.1". Ez az érték eltér a virtuális gép állomásnevét, és formátumú \<protokoll\>://\<gazdagép\>:\<port\>\<elérési út\>.

**Q. Application Gateway hozzáférés néhány forrás IP-címek engedélyezési lehetőségeket?**

Ebben a forgatókönyvben teheti meg az Application Gateway-alhálózat NSG-k használatával. Az alábbi korlátozásokat kell elhelyezni az alhálózat prioritás a listában szereplő sorrendben:

* Engedélyezik a bejövő forgalmat a forrás IP-/ IP-címtartományt.

* Lehetővé teszi minden forrás 65503 – 65534 portokat a beérkező kérések [háttérrendszer állapotának kommunikációs](application-gateway-diagnostics.md). Ezen a porttartományon szükség Azure-infrastruktúra kommunikációjához. A portokat Azure-tanúsítványok védik (zárják le). Megfelelő tanúsítványok nélkül a külső entitások – például az ügyfelek átjárók nem lesz a végpontokra módosításokat kezdeményezhetnek.

* Lehetővé teszi a bejövő Azure Load Balancer vizsgálatok (AzureLoadBalancer címke) és a bejövő virtuális hálózati forgalom (VirtualNetwork címke) a a [NSG](../virtual-network/security-overview.md).

* Minden egyéb bejövő forgalmat egy megtagadási az összes szabály letiltása.

* Az összes célhelyre az internetre irányuló kimenő forgalom engedélyezése.

**Q. Használhatja ugyanazt a portot a nyilvános és privát irányuló figyelői?**

Nem, ez nem lehetséges.

## <a name="performance"></a>Teljesítmény

**Q. Hogyan támogatja a Application Gateway a magas rendelkezésre állás és méretezhetőség?**

Az Application Gateway támogatja a magas rendelkezésre állás elérésére, ha két vagy több példánya. Azure ezek a példányok győződjön meg arról, hogy minden példány nem egy időben, frissítési és a tartalék tartomány között osztja el. Az Application Gateway támogatja a méretezhetőség átjáróhoz megosztani a terhelés több példánya hozzáadásával.

**Q. Hogyan válhatok Vészhelyreállítási forgatókönyvhöz rendelkező Application Gateway adatközpontokon?**

Ügyfeleink a Traffic Manager használatával elosztani a forgalmat több Application Gateway átjárók különböző adatközpontokban.

**Q. Automatikus skálázás támogatott?**

Nem, de Application Gateway átviteli metrikát, amely riasztást küld, ha a küszöbérték elérése használható. Manuálisan további példányok hozzáadása vagy méretének módosítása nem indítja újra az átjárót, és nem befolyásolja a meglévő forgalmat.

**Q. Manuális skálázás történik, felfelé és lefelé OK állásidő?**

Nem jár, a példányok frissítési és tartalék tartományok között oszlanak meg.

**Q. Támogatja az application gateway támogatása a kapcsolat kiürítése?**

Igen. -Kapcsolatának kiürítéséhez módosítása tagok találhatóak a háttérkészlet megszakítása nélkül is beállíthatja. Ez lehetővé teszi a meglévő kapcsolatok továbbra is az előző cél kell küldeni, amíg a kapcsolat le van zárva, vagy egy konfigurálható időkorlát lejár. Vegye figyelembe, hogy-kapcsolatának kiürítéséhez, csak megvárja a jelenlegi szükségszerű kapcsolatok végrehajtásához. Az Application Gateway még nem ismeri az alkalmazás munkamenet-állapot.

**Q. Mik azok az application gateway méretét?**

Az Application Gateway jelenleg három méretben érhető el: **Kicsi**, **Közepes** és **Nagy**. A Kicsi méret ideális fejlesztési és tesztelési célokra.

Előfizetésenként 50 alkalmazásátjárót hozhat létre, egyenként 10 példánnyal. Egy alkalmazásátjáró 20 HTTP-figyelőből állhat. Az Application Gateway korlátainak teljes listáját lásd: [Az Application Gateway szolgáltatási korlátozásai](../azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits).

Az alábbi táblázatban az egyes SSL-alapú kiszervezéshez engedélyezett alkalmazásátjárókhoz tartozó átlagos átviteli sebességek szerepelnek:

| Átlagos háttér-válasz mérete | Kicsi | Közepes | Nagy |
| --- | --- | --- | --- |
| 6KB |7,5 Mbps |13 Mbps |50 Mbps |
| 100KB |35 Mbps |100 Mbps |200 Mbps |

> [!NOTE]
> Ezek az értékek az alkalmazásátjáró hozzávetőleges átviteli sebességét jelzik. A tényleges átvitel számos környezeti tényezőtől függ, például az átlagos lapmérettől, a háttérpéldányok helyétől és a lapkiszolgálás feldolgozási időtartamától. A pontos teljesítményszámokhoz saját teszteket kell futtatnia. Ezek az értékek csupán útmutatóul szolgálnak a kapacitástervezéshez.

**Q. Módosítható példány mérete a közepes és nagy megszakítása nélkül?**

Igen, az Azure elosztja példányok, győződjön meg arról, hogy minden példány nem egy időben a frissítés és a tartalék tartományok között. Az Application Gateway támogatja a méretezés több példányát megosztani a terhelés átjáróhoz hozzáadásával.

## <a name="ssl-configuration"></a>SSL konfigurálása

**Q. Milyen tanúsítványok az Application Gatewayen támogatottak?**

Önaláírt tanúsítványok, CA-tanúsítványok és a helyettesítő tanúsítványok támogatottak. Bővített tanúsítványok nem támogatottak.

**Q. Mik azok az aktuális titkosító csomagok, az Application Gateway által támogatott?**

Az aktuális, az application gateway által támogatott titkosító csomagok a következők. Látogatás: [konfigurálása SSL házirend verziója és az Application Gatewayen titkosító csomagok](application-gateway-configure-ssl-policy-powershell.md) megtudhatja, hogyan szabhatja testre az SSL-beállításokat.

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

**Q. Az Application Gateway is támogatja a forgalmat a háttérbeli újbóli titkosítása?**

Igen, az Application Gateway támogatja az SSL-alapú kiszervezéshez és teljes körű SSL-t, amely újra titkosítja a forgalmat a háttérbeli.

**Q. Konfigurálhatja az SSL-szabályzat az SSL-protokollverziók vezérlésére?**

Igen, az Application Gateway számára megtagadja a TLS1.0 TLS1.1 és TLS1.2 is beállíthatja. Az SSL 2.0 és 3.0 alapértelmezés szerint már letiltott és nem konfigurálhatók.

**Q. Konfigurálhatok titkosító csomagok és a házirend sorrendjét?**

Igen, [titkosító csomag használatát konfigurációs](application-gateway-ssl-policy-overview.md) használata támogatott. Egyéni házirend meghatározása, legalább egy, a következő titkosító csomag használatát engedélyezni kell. Az Application gateway az SHA256 titkosítást használ a háttérbeli felügyeleti.

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

**Q. Hány SSL-tanúsítványok támogatottak?**

Legfeljebb 20 SSL tanúsítványok használata támogatott.

**Q. Hány hitelesítési tanúsítványokat a háttérrendszer újbóli titkosítására támogatottak?**

Legfeljebb 10 hitelesítési tanúsítványokat a rendszer az alapértelmezett 5 használata támogatott.

**Q. Nem az Application Gateway integrálása az Azure Key Vault natív módon?**

Nem, nincs integrálva van az Azure Key Vault.

## <a name="web-application-firewall-waf-configuration"></a>Webes alkalmazás tűzfal (WAF) konfigurálása

**Q. A WAF Termékváltozat kínál a standard szintű Termékváltozat érhető el minden funkció?**

WAF Igen, a Standard termékváltozat támogatja az összes funkciót.

**Q. Mi az Application Gateway CRS-verzió támogatja?**

Az Application Gateway támogatja a CRS [2.2.9-es](application-gateway-crs-rulegroups-rules.md#owasp229) és CRS [3.0](application-gateway-crs-rulegroups-rules.md#owasp30).

**Q. Hogyan figyelhetem a WAF?**

Diagnosztikai naplózás keresztül figyelt WAF, további információ a diagnosztikai naplózást található [diagnosztikai naplózás és mérőszámok az Application Gateway számára](application-gateway-diagnostics.md)

**Q. Észlelés üzemmód blokkolja a forgalmat?**

Nem, a észlelési mód csak naplózza a forgalmat, amely a WAF-szabály aktiválva.

**Q. Hogyan szabható testre a WAF-szabályok?**

Igen, a WAF-szabályok is testre szabható, hogyan szabhatja testre őket látogasson el a további információt [testreszabása WAF-szabálycsoportjainak és szabályok](application-gateway-customize-waf-rules-portal.md)

**Q. Milyen szabályok jelenleg érhetők el?**

WAF jelenleg támogatja a CRS [2.2.9-es](application-gateway-crs-rulegroups-rules.md#owasp229) és [3.0](application-gateway-crs-rulegroups-rules.md#owasp30), itt alapvetőbiztonságirésénektöbbségévelazelső10biztonságiréseitáltalanyitottWebApplicationSecurityProject(OWASP)biztosítótalálható[ OWASP top 10 biztonsági rések](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013)

* SQL-injektálás elleni védelem

* Webhelyek közötti, parancsprogramot alkalmazó támadások elleni védelem

* Gyakori webes támadások (például parancsinjektálás, HTTP-kéréscsempészet, HTTP-válaszfelosztás és távolifájl-beszúrásos támadás) elleni védelem

* HTTP protokoll megsértése elleni védelem

* HTTP protokollanomáliák (például hiányzó gazdagép-felhasználói ügynök és Accept (Elfogadás) fejlécek) elleni védelem

* Robotprogramok, webbejárók és képolvasók elleni védelem

 * Gyakori alkalmazások konfigurációs hibáinak észlelése (vagyis Apache, IIS stb.)

**Q. WAF is támogatja a DDoS-megelőzési?**

WAF nem, nem biztosítja a DDoS-megelőzési.

## <a name="diagnostics-and-logging"></a>Diagnosztikai és naplózási

**Q. Milyen típusú naplók érhetők el az Application Gateway szolgáltatással?**

Nincsenek elérhető, az Application Gateway három naplókat. Ezek a naplók és egyéb diagnosztikai képességek a további információért látogasson el [háttérrendszer állapota, diagnosztikai naplók és mérőszámok az Application Gateway](application-gateway-diagnostics.md).

- **ApplicationGatewayAccessLog** – a hozzáférési napló tartalmazza minden egyes kérelem, az Application Gateway frontend elküldve. A hívó IP-Címek használatához, kért URL-cím válasz késése szerepel, és visszatérési kód, bájt. Hozzáférési napló 300 másodpercenként gyűjti. Ez a napló az Application Gateway-példányonként több rekordot tartalmaz.
- **ApplicationGatewayPerformanceLog** – a teljesítménynaplóban rögzíti a teljesítményadatok alapon példány kérelmek száma összesen szolgálja ki, beleértve a átviteli sebesség (bájt), a kérelmek teljes száma szolgálja ki, a sikertelen kérések száma, kifogástalan állapotú, és nem megfelelő állapotú háttér- példányok száma.
- **ApplicationGatewayFirewallLog** – a tűzfal naplója tartalmaz, amelynek része a webalkalmazási tűzfallal rendelkező application gateway az észlelésük vagy a megelőzési módban naplózott kérések.

**Q. Honnan tudhatom meg, ha saját háttérkészlet-tagokra kifogástalan állapotban-e?**

A PowerShell-parancsmagot is használhatja `Get-AzureRmApplicationGatewayBackendHealth` vagy állapota a portálon keresztül ellenőrizni funkcionáló [Application Gateway-diagnosztika](application-gateway-diagnostics.md)

**Q. Mi a megőrzési házirend a diagnosztikai naplók?**

Diagnosztikai naplók folyamat ügyfelek storage-fiókba, és ügyfelek állíthatja be a megtartási házirend alapján választaniuk. Diagnosztikai naplók is lehet küldeni egy Eseményközpontba és a Log Analytics. Látogasson el [Application Gateway-diagnosztika](application-gateway-diagnostics.md) további részletekért.

**Q. Hogyan kaphatok auditnaplók az Application Gateway?**

Auditnaplók az Application Gateway számára érhetők el. Kattintson a portál **tevékenységnapló** menü paneljén látható az Application Gateway a napló eléréséhez. 

**Q. Beállíthatom a riasztásokat az Application Gateway?**

Igen, az Application Gateway támogatja a riasztások, értesítések metrikák ki vannak konfigurálva. Az Application Gateway jelenleg rendelkezik egy "átviteli", amely konfigurálható a metrika riasztásra. További információ a riasztásokkal kapcsolatban, látogasson el [riasztási értesítések fogadása](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

**Q. Háttérkiszolgáló állapotadatainak adja vissza a állapota ismeretlen, Mi ez az állapot okozza?**

A leggyakoribb oka a háttérrendszer a hozzáférést egy NSG-t vagy az egyéni DNS-megjelenítését blokkolják. Látogasson el [háttérrendszer állapota, diagnosztikai naplózás és mérőszámok az Application Gateway](application-gateway-diagnostics.md) további.

## <a name="next-steps"></a>További lépések

További információ az Application Gateway látogatás [Mi az Azure Application Gateway?](overview.md)
