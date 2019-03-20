---
title: Az Azure Application Gateway összetevők
description: Ez a cikk a különféle összetevők Application Gateway ismertetése
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: 44c8b331ebb258c39a003c91e0711e6dfb87cb12
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58076293"
---
# <a name="application-gateway-components"></a>Application gateway összetevők

 Egy application gateway-ügyfelek a kapcsolódási pont szolgál. Az alkalmazások bejövő forgalmának több háttérkészletek, például az Azure virtuális gépek, a virtual machine scale sets, App Services vagy a helyszíni vagy külső kiszolgálók között osztja el. Ehhez használja az ebben a cikkben leírt több összetevőt.

![application-gateway-components](./media/application-gateway-components/application-gateway-components.png)

## <a name="frontend-ip-address"></a>Előtérbeli IP-cím

Egy előtérbeli IP-cím pedig az application gateway társított IP-címe. Konfigurálhatja az application gateway, vagy hogy a nyilvános IP-címmel vagy magánhálózati IP-cím, vagy mindkettőt. Csak egy nyilvános IP-cím vagy egy magánhálózati IP-cím támogatott. A virtuális hálózat és a nyilvános IP-címet az application gateway ugyanazon a helyen kell lennie.

Egy előtérbeli IP-cím társítva egy *figyelő* létrehozása után. 

### <a name="static-vs-dynamic-public-ip-address"></a>Static vs dynamic public IP address

A v1 Termékváltozatot statikus belső IP-címeket támogatja, de nem támogatja a statikus nyilvános IP-címeket. A VIP-címet módosíthatja, ha az application gateway leállítása és elindítása. Az application gateway társított DNS-név nem változik az átjáró életciklusa. Ezért ajánlott egy CNAME-aliast használni, és az application gateway DNS-címére mutasson.

Az Application Gateway v2 szintű Termékváltozatot támogatja a statikus nyilvános IP-címeket, valamint a statikus belső IP-címeket. 

## <a name="listeners"></a>Figyelők

Az application gateway használata előtt hozzá kell adnia egy vagy több figyelő. Egy figyelő olyan logikai entitás, amely ellenőrzi a bejövő kapcsolódási kérelmeket, és fogadja el a kérelmeket, ha a protokoll, port, állomás és IP-címet társítva a kérelem egyezés a protokollal, a port, a gazdagép és a figyelő konfigurációjába társított IP-cím. Az application gatewayhez csatolt több kérésfigyelőt is lehet, és a egy protokoll használható. Miután a figyelő azt észleli, hogy az ügyfelektől érkező bejövő kéréseket, az Application Gateway irányítja. Ezek a kérelmek azon tagjait a háttérkészlet, a figyelő, amely a bejövő kérelem érkezett a meghatározó kérés útválasztási szabályok használatával.

Figyelők a következő portokat és protokollokat támogatja:

### <a name="ports"></a>Portok

Ez az a port, amelyen a figyelő az ügyfélkérelem figyel. Konfigurálhatja a beállításnak 1 és közötti 65502 V1 Termékváltozatot és 1 a 65199 V2 szintű Termékváltozatot a portokat.

### <a name="protocols"></a>Protokollok

Az Application gateway az alábbi négy protokollokat támogatja: HTTP, HTTPS, HTTP/2 és WebSocket

Explicit módon adja meg a választott, a figyelő konfigurációjába a HTTP és HTTPS protokollok között. A [a websockets protokoll és a HTTP/2 protokoll támogatása](https://docs.microsoft.com/azure/application-gateway/overview#websocket-and-http2-traffic) natív módon van megadva. [Websocket-támogatás](https://docs.microsoft.com/azure/application-gateway/application-gateway-websocket) alapértelmezés szerint engedélyezve van. Kizárólag WebSocket-támogatásra vonatkozó felhasználói beállítás nem létezik. A HTTP és HTTPS-figyelőként való használja a websockets protokollt. HTTP/2 protokoll támogatása az application gateway figyelői-ügyfelekhez érhető el. A háttér-kiszolgálókészlethez kommunikációs HTTP/1.1 felett van. Alapértelmezés szerint a HTTP/2-támogatás le van tiltva. Ha szeretné, engedélyezze azt.

SSL-lezárást egy HTTPS-figyelőre is használhat. Egy HTTPS-figyelő kiszervezi a titkosítás és a visszafejtés, az application gateway, hogy a webkiszolgálók nem rójanak úgy, hogy a terhelést. Az alkalmazások majd szabadon az üzleti logikára összpontosíthat.

### <a name="custom-error-pages"></a>Egyéni hibalapok

Az Application gateway lehetővé teszi, hogy hozzon létre egyéni hibalapok alapértelmezett hibák részleteiről szóló lap megjelenítése helyett. Az egyéni hibaoldalakon feltüntetheti saját védjegyeit, és egyéni elrendezést használhat. Az Application gateway is egy egyéni hibalap megjelenítése, amikor a kérelem nem érhető el a háttérrendszert. További információkért lásd: [egyéni hibalapok az Application Gateway számára](https://docs.microsoft.com/azure/application-gateway/custom-error).

### <a name="types-of-listeners"></a>Figyelők típusai

Figyelők két típusa van:

- **Alapszintű**: Ez a figyelő típusú egyetlen tartományt helyre, ahol az application Gateway IP-címre egyetlen DNS-leképezést rendelkezik figyeli. A figyelő konfigurációra szükség, ha egy application gateway mögött egyetlen hely.
- **Többhelyes**: Ez a figyelő konfigurációra szükség, egynél több webalkalmazás ugyanazon alkalmazásátjárókhoz való konfigurálásakor. Ez lehetővé teszi a hatékonyabb topológiát beállításához ad hozzá egy application gateway akár 100 webhelyet. Mindegyik webhelyet a saját háttérkészletéhez lehet irányítani. Példa:  A három altartományok – abc.contoso.com, xyz.contoso.com és az application Gateway IP-címre mutató pqr.contoso.com. Hozzon létre három figyelői típusú *többhelyes* és a megfelelő port minden egyes figyelő konfigurálása és a protokoll beállítása. További információkért lásd: [több hely üzemeltetése](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview).

Miután létrehozott egy figyelőt, társítsa azt egy kérés útválasztási szabályt, amely meghatározza, hogy hogyan a kérelem érkezett a figyelő a háttérrendszer lesznek irányítva.

Figyelők láthatók a rendelés feldolgozása történik. Ezért ha egy alapszintű figyelő megfelel egy bejövő kérésnek feldolgozza a először. Többhelyes figyelőket konfigurálni kell egy alapszintű figyelő annak biztosítása érdekében az adatforgalmat a megfelelő háttér előtt.

## <a name="request-routing-rule"></a>Kérelemirányítási szabály

Ez az application gateway a legfontosabb összetevője, amely meghatározza, hogy a társított Ez a szabály a figyelőt az adatforgalmat. A szabály köti a figyelőt, a háttérkiszolgáló-készlet és a háttérbeli HTTP-beállítások. Miután a figyelő egy kérelem elfogadja, a kérelem-útválasztási szabály meghatározza, hogy a kérés a háttérrendszere továbbítja vagy máshol átirányítva. A kérelem el kell küldeni, a háttérkiszolgáló határozza meg, ha a kérelem-útválasztási szabály határozza meg hogy melyik háttérkiszolgáló-készletre továbbítani kell lennie. Ezenkívül a kérelem-útválasztási szabály is meghatározza, hogy a fejlécek, a kérelem-e írni. Egy figyelő csak egy szabályt lehet rendelni.

Kérelem-útválasztási szabályok két típusú lehet:

- **Alapszintű:** A kapcsolódó figyelőt az összes kérelem (Példa: blog.contoso.com/*) a társított háttérkészlet, a kapcsolódó HTTP-beállítás használatával továbbítja.
- **Útvonalalapú:** Ezt a szabálytípust lehetővé teszi a kérelmek átirányítása a a kapcsolódó figyelőt, hogy egy adott háttérkészlet, a kérelem URL-cím alapján. Ha a kérelem URL-cím elérési útja megegyezik egy útvonalalapú szabály az elérésiút-minta, a irányítja a kérelmeket, hogy a szabály használatával. Az elérési út mintája csak az elérési utat az URL-cím, hogy a lekérdezési paraméterek nem érvényes. Az elérési útját egy figyelőt a kérelem URL-címe nem egyezik a útvonalalapú szabály, akkor a rendszer ilyenkor úgy irányítja a kérést a *alapértelmezett* háttérkészlet és a *alapértelmezett* HTTP-beállítások. További információkért lásd: [URL-cím alapú útválasztást](https://docs.microsoft.com/azure/application-gateway/url-route-overview).

### <a name="redirection-support"></a>Átirányítási támogatás

A kérelem-útválasztási szabály lehetővé teszi az application gatewayen forgalom átirányítása. Ez egy általános átirányítási mechanizmus, így a szabályokkal bármilyen megadott portról és portra átirányíthat. Válasszon egy másik figyelő (amely megkönnyíti a automatikus HTTP – HTTPS átirányításról engedélyezése) vagy külső helyre kell lennie az átirányítás célhelye, válassza ki az ideiglenes vagy állandó átirányítás, vagy válassza ki az URI elérési út és a lekérdezési karakterlánc hozzáfűzi az átirányított URL-címet. További információkért lásd: [átirányítja a forgalmat az Application Gateway](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

#### <a name="rewrite-http-headers"></a>HTTP-fejlécek átírása

A kérés útválasztási szabályok hozzáadása, eltávolítása vagy módosítása a HTTP (S)-kérelem használatával, és a válaszfejlécet a kérelem során, és a válasz-csomagok áthelyezése az ügyfél és a háttérkiszolgáló-készletek, az application gateway-n keresztül. A fejlécek csak nem állítható be statikus értékre, hanem és egyéb fontos kiszolgálói változókat. Ez segít elvégezni néhány fontos alkalmazási, például az ügyfelek, eltávolítja a háttérrendszer bizalmas információ hozzáadása a további biztonsági intézkedéseket, és így tovább IP-címének beolvasása. További információkért lásd: [Újraírási HTTP-fejlécek az application gateway-en](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).

## <a name="http-settings"></a>HTTP-beállítások

Az application gateway útvonalakat a forgalmat a háttérbeli kiszolgálók (a kérés útválasztási szabályt, amely a HTTP-beállítások csatolt megadva) protokoll használatával a port számát, és az egyéb beállításokat, ez az összetevő megadott. A port és protokoll, a HTTP-beállítások az határozza meg, hogy az alkalmazás átjáró és a háttérkiszolgáló-kiszolgálók közötti forgalom titkosítva van, így teljes körű SSL megvalósítására, vagy titkosítás nélkül. Ez az összetevő is szolgál a: megállapításához, hogy a felhasználói munkamenet az ugyanazon a kiszolgálón kell tartani a [cookie-alapú munkamenet-affinitás](https://docs.microsoft.com/azure/application-gateway/overview#session-affinity), elvégzéséhez háttérkészlet-tagokra használatával biztonságos eltávolítása [kapcsolat a kiürítés](https://docs.microsoft.com/azure/application-gateway/overview#connection-draining), társíthatja a háttérrendszer állapotának figyeléséhez, állítsa be a kérelem időkorlátja, bírálja felül az állomás neve és elérési útja a kérésben egyéni mintavétel, és adjon meg egy egyszerű, az App service háttérrendszer háttér-beállítás megadásához. 

## <a name="backend-pool"></a>Háttérkészlet

A háttérkészlet segítségével irányíthatja a kérelmeket a háttérkiszolgálók, amelyeket teljesíteni a kérést. Háttérkészletek összeállítható a hálózati adapterek, a virtuális gép méretezése a készletek, nyilvános IP címeket, belső IP címeket, teljes tartománynév és több-bérlős háttérrendszereket, például az Azure App Service-ben. Application gateway háttérkészlet érintett tagjai nem egy rendelkezésre állási csoporthoz vannak társítva. Az Application Gateway tudnak kommunikálni a virtuális hálózaton kívüli példányok, hogy van, ezért a háttérkészletek tagja lehet, fürtök és adatközpontok között, vagy Azure-on kívül mindaddig, amíg nincs IP-kapcsolat. Háttérkészlet-tagként belső IP-címek használatát tervezi, akkor a szükséges [virtuális hálózatok közötti társviszony-létesítés](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) vagy [VPN-átjáró](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Támogatott és a virtuális hálózatok közötti társviszony-létesítés számára előnyös forgalom terhelését elosztja a más virtuális hálózatokkal. Az application gateway is kommunikálhat a helyszíni kiszolgálók ExpressRoute vagy VPN-alagút, csatlakozáskor mindaddig, amíg forgalom engedélyezve van.

A kérelmek különböző típusú különböző háttérkészletek hozhat létre. Hozzon létre például egy háttérkészlet általános kérelmek és a kéréseket, a mikroszolgáltatások az alkalmazás más háttérkészlet.

## <a name="health-probes"></a>Állapotminták

Az Application gateway alapértelmezés szerint a háttérkészlet összes erőforrásának állapotát figyeli, és automatikusan eltávolítja az összes erőforrást a készletből sérültnek. Továbbra is figyeli a nem megfelelő állapotú példányokat, és hozzáadja őket a megfelelően működő háttér-készlet számára, elérhetővé válnak, és a állapotadat-mintavételek válaszolni. Alapértelmezett mintavételi állapotfigyelés használatán is testreszabhatja az állapotminta az alkalmazás követelményeinek megfelelően. Az egyéni minták lehetővé teszik egy részletesebb felügyelheti a Szolgáltatásállapot-figyelést. Egyéni mintavétel használata esetén konfigurálhatja úgy a mintavételi időköz, az URL-cím és elérési útja, teszteléséhez és hány sikertelen válaszokat elfogadja a háttérkészlet-példány állapotúként van megjelölve. Azt javasoljuk, hogy konfigurálja-e az egyéni minták minden háttérkészlet állapotának monitorozásához. További információkért lásd: [figyelhető az Application gateway állapota](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview).

## <a name="next-steps"></a>További lépések

Miután megismerkedett az Application Gateway-összetevők, a következőket teheti:
* [Application Gateway létrehozása az Azure Portalon](quick-create-portal.md)
* [Application Gateway létrehozása az Azure PowerShell-lel](quick-create-powershell.md)
* [Application Gateway létrehozása az Azure CLI-vel](quick-create-cli.md)
