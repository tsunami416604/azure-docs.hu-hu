---
title: Gyakori kérdések az Azure Application Gateway
description: Ezen a lapon biztosít Azure Application Gateway gyakran feltett kérdésekre adott válaszok
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 5/21/2018
ms.author: victorh
ms.openlocfilehash: bf4e92636424e7d8f4a1bc2eb5ee9ba7e97667c6
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34699903"
---
# <a name="frequently-asked-questions-for-application-gateway"></a>Az Alkalmazásátjáró gyakori kérdések

## <a name="general"></a>Általános kérdések

**Q. Mi az Application Gateway?**

Azure Application Gateway egy alkalmazás kézbesítési vezérlő LÉPETT szolgáltatásként, az alkalmazások képességei terheléselosztási különböző réteg 7 kínál. Magas rendelkezésre állású és méretezhető szolgáltatást, amely teljes mértékben kezeli az Azure biztosít.

**Q. Milyen funkciókat támogatja az Alkalmazásátjáró?**

Alkalmazásátjáró támogatja SSL-feladatkiszervezést és végpontok közötti SSL, webalkalmazási tűzfal, munkamenet cookie-alapú kapcsolat, URL-cím elérési út-alapú útválasztási, több helyet üzemeltető és mások. Támogatott szolgáltatások teljes listájának megtekintéséhez keresse fel a [Alkalmazásátjáró bemutatása](application-gateway-introduction.md)

**Q. Mi az a különbség az Alkalmazásátjáró és az Azure Load Balancer?**

Alkalmazásátjáró 7 réteg terheléselosztó, amely azt jelenti, hogy működik együtt a csak internetes forgalmat (HTTP/HTTPS/WebSocket). Például az SSL-lezárást, a munkamenet cookie-alapú kapcsolat és a ciklikus multiplexelés képességek terheléselosztási forgalom támogatja. Terheléselosztó, kiegyensúlyozza forgalom rétegben 4 (TCP/UDP).

**Q. Milyen protokollokat támogatja az Alkalmazásátjáró?**

Alkalmazás-átjáró támogatja a HTTP, HTTPS, HTTP/2 és WebSocket.

**Q. Hogyan támogatja az Alkalmazásátjáró HTTP/2?**

A HTTP/2 protokoll támogatása csak az Application Gateway figyelői csatlakozó ügyfeleket érhető el. A háttérkészlet kiszolgáló kommunikációt HTTP/1.1 felett van. 

Alapértelmezés szerint a HTTP/2 támogatása le van tiltva. A következő Azure PowerShell-kód részlet példa bemutatja, hogyan engedélyezheti azt:

```
$gw = Get-AzureRmApplicationGateway -Name test -ResourceGroupName hm
$gw.EnableHttp2 = $true
Set-AzureRmApplicationGateway -ApplicationGateway $gw
```

**Q. Által támogatott ma háttérkészlet részeként?**

Háttérkészlet állhat hálózati adapter virtuálisgép-méretezési csoportok, nyilvános IP-címek, belső IP-címek, teljes tartománynév (FQDN) neve, és több-bérlős vissza-végpontok, például az Azure Web Apps. Alkalmazás átjáró háttér készlettag nem rendelkezésre állási csoportok vannak társítva. Háttér-készletek tagjai között, fürtök és adatközpontok, vagy lehet Azure-on kívüli mindaddig, amíg az IP-kapcsolattal rendelkeznek.

**Q. Milyen régiók érhető el a szolgáltatást?**

Alkalmazásátjáró globális Azure minden területen érhető el. Rendszerben is elérhető [Azure Kína](https://www.azure.cn/) és [Azure Government](https://azure.microsoft.com/overview/clouds/government/)

**Q. Ez az előfizetésem dedikált telepítésének vagy azt megoszthatja ügyfelek?**

Alkalmazásátjáró egy dedikált központi telepítés a virtuális hálózat.

**Q. Van HTTP -> támogatott HTTPS átirányítása?**

Átirányítás használata támogatott. Látogasson el [Alkalmazásátjáró átirányítási áttekintése](application-gateway-redirect-overview.md) további.

**Q. Milyen sorrendben figyelői feldolgozása?**

Figyelők dolgoznak fel a rendszer a sorrendben. Ezért ha egy alapszintű figyelő egy bejövő kérelem megfelel feldolgozza azt először.  Többhelyes figyelők egy alapszintű figyelő annak biztosítására, hogy a megfelelő háttér-forgalom érdekében előtt úgy kell konfigurálni.

**Q. Hol található Application Gateway IP- és DNS?**

A végpont egy nyilvános IP-címet használ, ha ez az információ található a nyilvános IP-cím erőforrás vagy a – áttekintés oldalra az Alkalmazásátjáró a portálon. A belső IP-címek ez található Áttekintés lap.

**Q. Az IP- vagy DNS változik az Alkalmazásátjáró életciklusa alatt?**

A VIP módosíthatja, ha az átjáró leállt, és az ügyfél által indított. Alkalmazásátjáró társított DNS nem változtatja meg az átjáró életciklusa alatt. Ezért ajánlott CNAME alias használja, és mutasson a az alkalmazás-átjáró a DNS-címét.

**Q. Alkalmazásátjáró támogatja a statikus IP-címet?**

Nem, az Alkalmazásátjáró nem támogatja a statikus nyilvános IP-címek, de statikus belső IP-címek támogatja.

**Q. Alkalmazásátjáró támogatja a több nyilvános IP-cím az átjárón?**

Csak egy nyilvános IP-cím egy Application Gateway esetén támogatott.

**Q. Hogy mekkorának kell még a alhálózati Alkalmazásátjáró?**

Alkalmazásátjáró példányonként egy magánhálózati IP-címet, valamint egy másik privát IP-címet használ fel, ha a magánhálózati front-end IP-konfiguráció úgy van beállítva. Emellett Azure fenntartja az első négy és az utolsó IP-cím az egyes alhálózatokon, belső használatra.
Például, ha az Application Gateway beállítása három alkalmazáspéldányra és nem magánhálózati front-end IP, majd egy /29 alhálózati vagy azoknál nagyobb méretűek van szükség. Ebben az esetben az Application Gateway három IP-címeket használ. Ha rendelkezik három alkalmazáspéldányra és a magánhálózati front-end IP-konfigurációhoz, majd egy /28 IP-cím alhálózati vagy azoknál nagyobb méretűek van szükség, mivel négy IP-címek szükség.

**Q. Támogatja az Alkalmazásátjáró x-továbbított-a fejlécek?**

Igen, az Alkalmazásátjáró x-továbbított – az x továbbított protokoll és x továbbított port fejlécek szúr be a kérést továbbítja a háttérkiszolgálón. Az x-továbbított-a fejléc formátuma IP:Port vesszővel tagolt listája. X továbbított protokoll érvényes értékei http vagy HTTPS protokollt. X-továbbított-port, amelyen a kérelmet az Alkalmazásátjáró címen érhető portot határozza meg.

**Q. Mennyi időt vesz igénybe egy alkalmazás-átjáró üzembe helyezéséhez? Az Alkalmazásátjáró továbbra is működik, ha frissítése során?**

Új Alkalmazásátjáró telepítések esetén is igénybe vehet akár 20 percig kiépítéséhez. Mérete/példányszám módosításai nem zavaró, és ez alatt az idő az átjáró aktív marad.

## <a name="configuration"></a>Konfiguráció

**Q. Alkalmazásátjáró mindig telepítve van a virtuális hálózaton?**

Igen, az Alkalmazásátjáró mindig a rendszer a virtuális hálózati alhálózat. Ez az alhálózat csak tartalmazhat Alkalmazásátjárót.

**Q. A virtuális hálózaton kívüli példányokhoz működik Alkalmazásátjáró?**

Alkalmazásátjáró működik, hogy a mindaddig, amíg nincs IP-kapcsolatot a virtuális hálózaton kívüli példányára. Ha a háttér címkészletet tagként belső IP-címek használatát tervezi, akkor van szükség [VNETBEN társviszony-létesítés](../virtual-network/virtual-network-peering-overview.md) vagy [VPN-átjáró](../vpn-gateway/vpn-gateway-about-vpngateways.md).

**Q. Központi telepítését az alkalmazás átjáróalhálózatot dolgozott?**

Nem, de telepítheti az alhálózat más alkalmazásátjárót.

**Q. Hálózati biztonsági csoportok az Alkalmazásátjáró alhálózat támogatottak?**

Hálózati biztonsági csoportok az Alkalmazásátjáró alhálózat, a következő korlátozásokkal támogatottak:

* Kivételek kell elhelyezni, a bejövő forgalmat portokon 65503-65534 az háttér health megfelelő működéséhez.

* Kimenő internetkapcsolat nem tiltható le.

* Engedélyezni kell az AzureLoadBalancer címke forgalmát.

**Q. Az Alkalmazásátjáró korlátai Tudom növelni a működés felső korlátjának?**

Látogasson el [alkalmazás átjáró korlátok](../azure-subscription-service-limits.md#application-gateway-limits) korlátokat megtekintéséhez.

**Q. Használhatok Alkalmazásátjáró külső és belső forgalmát egyidejűleg?**

Alkalmazásátjáró Igen, támogatja az egy belső IP-cím és egy külső IP-cím az Alkalmazásátjáró.

**Q. Támogatott Vnetben társviszony-létesítés?**

Igen, Vnetben társviszony-létesítés támogatott és hasznos a terheléselosztás forgalom más virtuális hálózatok.

**Q. I kommunikálhat a helyszíni kiszolgálók expressroute-on vagy VPN-alagutat csatlakozáskor?**

Igen, mindaddig, amíg forgalom engedélyezve van.

**Q. Rendelkezhet eltérő portokon számos alkalmazás szolgál egy háttérkészletéből?**

Micro service-architektúra esetén támogatott. Kell több különböző portokon mintavétel konfigurálva http-beállítások.

**Q. Támogatják egyéni mintavételt helyettesítő karakterekkel vagy reguláris kifejezéssel az érkezett válasz adatait?**

Egyéni mintavételt nem támogatják a helyettesítő karakteres vagy regex érkezett válasz adatait a. 

**Q. Szabályok feldolgozásának módja?**

Szabályok feldolgozása a sorrendben vannak konfigurálva. Javasoljuk, hogy többhelyes szabályok konfigurálva vannak-e, mielőtt alapvető szabályok csökkenti annak esélyét, hogy forgalom annak biztosítására, hogy a megfelelő háttér, az alapszintű szabály megfelelő forgalmat a többhelyes szabály értékelt előtt port alapján.

**Q. Mi a gazdagép mezőt az egyéni mintavételt jelölésére?**

A gazdagép mező neve a mintavétel történő küldéséhez. Alkalmazandó csak akkor, ha több hely van beállítva az alkalmazás-átjárón, ellenkező esetben használja a "127.0.0.1". Ez az érték eltér a virtuális gép állomásnevét, és formátumú \<protokoll\>://\<állomás\>:\<port\>\<elérési\>.

**Q. Telepíthetek engedélyezett néhány forrás IP-címek Alkalmazásátjáró elérésére?**

Ebben a forgatókönyvben végezhető Alkalmazásátjáró alhálózaton NSG-ket használ. A következő korlátozásokat a prioritásuk szerinti a listában szereplő sorrendben az alhálózaton kell rendezni:

* Engedélyezi a bejövő forgalom forrás IP-/ IP-címtartomány.

* Bejövő kérelmek forrásokból származó 65503-65534 portok engedélyezése [háttér állapotfigyelő kommunikációja](application-gateway-diagnostics.md).

* Bejövő Azure Load Balancer mintavételt (AzureLoadBalancer címke) és a bejövő virtuális hálózati forgalmat (VirtualNetwork címke) engedélyezése a [NSG](../virtual-network/security-overview.md).

* Megtagadási minden egyéb bejövő forgalom blokkolása minden szabály.

* Kimenő forgalom az internethez, az összes cél engedélyezése.

## <a name="performance"></a>Teljesítmény

**Q. Hogyan támogatja a Alkalmazásátjáró a magas rendelkezésre állás és méretezhetőség?**

Alkalmazásátjáró támogatja a magas rendelkezésre állás elérésére, ha két vagy több példányt. Azure ezek a példányok elosztása az update és a tartalék tartományok győződjön meg arról, hogy minden példány nem egy időben. Alkalmazásátjáró méretezhetőség támogatja több példányát ugyanahhoz az átjáróhoz a terhelés hozzáadásával.

**Q. Hogyan érhetők el vész-Helyreállítási forgatókönyv Alkalmazásátjáró adatközpontjaiban között?**

Ügyfelek használhatják a Traffic Manager forgalom szét több alkalmazás átjáró különböző adatközpontokban.

**Q. Automatikus skálázással támogatva van?**

Nem, de Alkalmazásátjáró riasztást küldjön, amikor a küszöbérték elérésekor használt átviteli sebesség metrikát. Manuális hozzáadása példányok vagy méretének módosítása nem indítja újra az átjárót, és nem befolyásolja a meglévő forgalom.

**Q. Fel/le OK állásidő, nem manuális méretezési?**

Állásidő nélkül, a frissítési tartományok és a tartalék tartományok között elosztott példányok.

**Q. Nem az alkalmazás átjáró támogatja a kapcsolat kiürítése?**

Igen. Beállíthatja, hogy a kapcsolat megszakítása nélkül háttérkészletbeli tagok módosítása kiürítését. Ez lehetővé teszi a meglévő kapcsolatok továbbra is az előző cél kell küldeni, amíg a kapcsolat le van zárva, vagy konfigurálható időkorlátja lejár. Vegye figyelembe, hogy a kapcsolat befejeződik az aktuális üzenetsoroktól kapcsolatok csak vár kiürítését. Alkalmazásátjáró nem tehát tudatában alkalmazás munkamenet-állapot.

**Q. Mik azok a alkalmazás átjáró mérete?**

Az Application Gateway jelenleg három méretben érhető el: **Kicsi**, **Közepes** és **Nagy**. A Kicsi méret ideális fejlesztési és tesztelési célokra.

Előfizetésenként 50 alkalmazásátjárót hozhat létre, egyenként 10 példánnyal. Egy alkalmazásátjáró 20 HTTP-figyelőből állhat. Az Application Gateway korlátainak teljes listáját lásd: [Az Application Gateway szolgáltatási korlátozásai](../azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits).

Az alábbi táblázatban az egyes SSL-alapú kiszervezéshez engedélyezett alkalmazásátjárókhoz tartozó átlagos átviteli sebességek szerepelnek:

| Átlagos háttér-lap válasza mérete | Kicsi | Közepes | Nagy |
| --- | --- | --- | --- |
| 6KB |7,5 Mbps |13 Mbps |50 Mbps |
| 100KB |35 Mbps |100 Mbps |200 Mbps |

> [!NOTE]
> Ezek az értékek az alkalmazásátjáró hozzávetőleges átviteli sebességét jelzik. A tényleges átvitel számos környezeti tényezőtől függ, például az átlagos lapmérettől, a háttérpéldányok helyétől és a lapkiszolgálás feldolgozási időtartamától. A pontos teljesítményszámokhoz saját teszteket kell futtatnia. Ezek az értékek csupán útmutatóul szolgálnak a kapacitástervezéshez.

**Q. Módosítható példányméretének a közepes vagy nagyméretű megszakítása nélkül?**

Igen, Azure példányok elosztása frissítés és a tartalék tartományok győződjön meg arról, hogy minden példány nem egy időben. Alkalmazásátjáró támogatja, több példányát ugyanahhoz az átjáróhoz a terhelés hozzáadásával méretezés.

## <a name="ssl-configuration"></a>SSL konfigurálása

**Q. Milyen tanúsítványok Alkalmazásátjáró támogatottak?**

Önaláírt tanúsítványok, a CA-tanúsítványok, és a helyettesítő tanúsítványok támogatottak. EV tanúsítványok használata nem támogatott.

**Q. Mik az aktuális alkalmazás-átjáró által támogatott titkosító csomagok?**

Az aktuális alkalmazás-átjáró által támogatott titkosító csomagok a következők: Látogasson el: [SSL konfigurálása házirend verziója és az Application Gateway titkosító csomagok](application-gateway-configure-ssl-policy-powershell.md) megtudhatja, hogyan szabhatja testre az SSL-beállítások.

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

**Q. Alkalmazásátjáró is támogatja a háttér-forgalom újbóli titkosítása?**

Igen, a Application Gateway SSL kiszervezési, és a végpontok közötti SSL, amely újból titkosítja a forgalmat a háttérrendszer támogatja.

**Q. Konfigurálhatja a SSL házirendet, amellyel szabályozhatja az SSL protokoll verziója?**

Igen, Alkalmazásátjáró TLS1.0 TLS1.1 és TLS1.2 megtagadni konfigurálhatja. Az SSL 2.0 és 3.0 vannak már le van tiltva alapértelmezés szerint, és amelyek nem konfigurálhatók.

**Q. Titkosítási csomagok és a házirendek sorrendjének is konfigurálni?**

Igen, [titkosító csomagok konfigurációjának](application-gateway-ssl-policy-overview.md) esetén támogatott. Egyéni házirend meghatározása esetén a következő titkosító csomagok legalább egyikét engedélyezni kell. Alkalmazásátjáró háttérbeli felügyeleti az SHA256 titkosítást használ.

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

**Q. Hány SSL-tanúsítványok támogatottak?**

Legfeljebb 20 SSL támogatottak.

**Q. A háttérrendszer újbóli titkosítása hány tanúsítványhitelesítést biztosítsanak támogatottak?**

Legfeljebb 10 hitelesítési tanúsítványok az alapértelmezett érték 5 használata támogatott.

**Q. Nem Alkalmazásátjáró integrálása az Azure Key Vault natív módon?**

Nem, nem integrálva van az Azure Key Vault.

## <a name="web-application-firewall-waf-configuration"></a>Webes alkalmazás tűzfalat (WAF) konfigurációja

**Q. Nem a WAF SKU kínálnak a Standard Termékváltozat elérhető összes szolgáltatások?**

Igen, WAF összes funkcióját támogatja a Standard Termékváltozat.

**Q. Mi az Application Gateway CRS verzió támogatja?**

Alkalmazásátjáró támogatja CRS [program 2.2.9-es](application-gateway-crs-rulegroups-rules.md#owasp229) és CRS [3.0](application-gateway-crs-rulegroups-rules.md#owasp30).

**Q. Hogyan figyelhetek WAF?**

WAF keresztül diagnosztikai naplózás felügyelet alatt, további információt a diagnosztikai naplózás helyen találhatók [diagnosztikai naplózás és az Alkalmazásátjáró metrikák](application-gateway-diagnostics.md)

**Q. Blokkolja az észlelési mód forgalom?**

Nem, a észlelési mód csak naplózza a forgalmat, amely egy WAF szabály elindul.

**Q. Hogyan testre szabhatja a WAF szabályokat?**

Igen, testre szabható testre szabhatók találhat további tájékoztatást a-e WAF szabályok [testreszabása WAF csoportok és szabályok](application-gateway-customize-waf-rules-portal.md)

**Q. Milyen szabályok jelenleg érhetők el?**

WAF jelenleg CRS [program 2.2.9-es](application-gateway-crs-rulegroups-rules.md#owasp229) és [3.0](application-gateway-crs-rulegroups-rules.md#owasp30), szemben az első 10 biztonsági réseit által a nyitott webes alkalmazás biztonsági Project (OWASP) többségét eredeti biztonsági nyújt található itt [ OWASP első 10 biztonsági réseket](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013)

* SQL-injektálás elleni védelem

* Webhelyek közötti, parancsprogramot alkalmazó támadások elleni védelem

* Gyakori webes támadások (például parancsinjektálás, HTTP-kéréscsempészet, HTTP-válaszfelosztás és távolifájl-beszúrásos támadás) elleni védelem

* HTTP protokoll megsértése elleni védelem

* HTTP protokollanomáliák (például hiányzó gazdagép-felhasználói ügynök és Accept (Elfogadás) fejlécek) elleni védelem

* Robotprogramok, webbejárók és képolvasók elleni védelem

 * A gyakori alkalmazás konfigurációs hibák (Ez azt jelenti, hogy Apache, az IIS, stb.) észlelése

**Q. WAF is támogatja a DDoS megelőzési?**

WAF nem, nem biztosít DDoS megelőzése.

## <a name="diagnostics-and-logging"></a>Diagnosztika és naplózás

**Q. Milyen típusú naplók az Alkalmazásátjáró érhetők el?**

Nincsenek elérhető az Alkalmazásátjáró három naplókat. Ezek a naplók és más diagnosztikai képességek a további tudnivalókért keresse fel [háttér állapot, a diagnosztikai naplók és a metrikák az Alkalmazásátjáró](application-gateway-diagnostics.md).

- **ApplicationGatewayAccessLog** -a hozzáférési napló minden egyes kérelem elküldve az Alkalmazásátjáró előtér tartalmazza. Az adatok a hívó IP, kért, URL-cím válasz késés, bejövő és kimenő adatforgalma visszatérési kód, bájt. Hozzáférési napló gyűjtése 300 másodpercenként. Ez a napló Application Gateway-példányonként egy bejegyzést tartalmaz.
- **ApplicationGatewayPerformanceLog** – a teljesítmény naplóban rögzíti alapon / példány teljes kérelem kiszolgálása, beleértve az átviteli sebesség bájtban teljesítményadatok, kérelmek teljes száma a kiszolgált egy megfelelő és nem megfelelő háttér-, sikertelen kérelmek száma a példányok száma.
- **ApplicationGatewayFirewallLog** -a tűzfal a napló tartalmazza, amelyeket a rendszer a webalkalmazási tűzfal a konfigurált Alkalmazásátjáró észlelési vagy megelőzési módban kérelmeket.

**Q. Hogyan állapítható meg, ha a háttérkiszolgáló készlettagra megfelelő?**

A PowerShell-parancsmag `Get-AzureRmApplicationGatewayBackendHealth` vagy ellenőrizze a portálon keresztül állapotfigyelő ellátogatva [Alkalmazásdiagnosztika átjáró](application-gateway-diagnostics.md)

**Q. Mi az a diagnosztikai naplókat a megőrzési házirend?**

Diagnosztikai naplók folyamata az ügyfelek tárfiókba, és ügyfelek állíthatja be az adatmegőrzési, a beállítás alapján. Diagnosztikai naplók is lehet küldeni az Eseményközpont vagy Naplóelemzési. Látogasson el [átjáró Alkalmazásdiagnosztika](application-gateway-diagnostics.md) további részleteket.

**Q. Hogyan szerezhetek naplók az Alkalmazásátjáró?**

Az Alkalmazásátjáró naplók érhetők el. Kattintson a portál **tevékenységnapló** a menü paneljén az Alkalmazásátjáró a napló elérésére. 

**Q. Beállíthatja a Alkalmazásátjáró riasztások?**

Igen, Alkalmazásátjáró támogatja a riasztások, értesítések metrikák ki vannak konfigurálva. Alkalmazásátjáró "átviteli", amely konfigurálható egy metrika van riasztást. Riasztások kapcsolatos további információkért látogasson el a [riasztási értesítéseket](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

**Q. Háttér állapotfigyelő adja vissza állapota ismeretlen, mi okozza ezt az állapotot?**

A leggyakoribb oka a háttérkiszolgálón a hozzáférést egy NSG-t vagy egyéni DNS-megjelenítését blokkolják. Látogasson el [háttér állapot, a diagnosztikai naplózás és a metrikák az Alkalmazásátjáró](application-gateway-diagnostics.md) további.

## <a name="next-steps"></a>További lépések

További információt az Alkalmazásátjáró látogasson el [Mi az Azure Application Gateway?](overview.md)
