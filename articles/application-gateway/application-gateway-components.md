---
title: Application gateway összetevők
description: Ez a cikk ismerteti egy application gateway különböző összetevői
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: f5dfa34760bcef23bf54d65b35e3ad8f48cc2ee5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60831834"
---
# <a name="application-gateway-components"></a>Application gateway összetevők

 Egy application gateway-ügyfelek a kapcsolódási pont szolgál. Az alkalmazások bejövő forgalmának több háttérkészletek, többek között az Azure virtuális gépek, a virtual machine scale sets, az Azure App Service és a helyszíni vagy külső kiszolgálók között osztja el. A forgalom elosztását, az application gateway ebben a cikkben leírt több összetevőt használja.

![Az application gateway használt összetevők](./media/application-gateway-components/application-gateway-components.png)

## <a name="frontend-ip-addresses"></a>Előtérbeli IP-címek

Egy előtérbeli IP-cím pedig az application gateway társított IP-címe. Konfigurálhatja az application gateway nyilvános IP-címmel vagy magánhálózati IP-cím. Egy application gateway támogatja az egyik nyilvános és a egy magánhálózati IP-cím. A virtuális hálózat és a nyilvános IP-címet az application gateway ugyanazon a helyen kell lennie. Miután elkészült, egy előtérbeli IP-cím egy figyelő társítva.

### <a name="static-versus-dynamic-public-ip-address"></a>Static versus dynamic public IP address

Az Azure Application Gateway v2 szintű Termékváltozatot támogatja mindkét statikus belső és a statikus nyilvános IP-címeket, bár a v1 Termékváltozatot támogatja a csak statikus belső IP-címeket. A virtuális IP-cím (VIP) cím módosíthatja, ha az application gateway leállítása és elindítása.

Egy application gateway társított DNS-nevet az átjáró életciklusa nem változik. Ennek eredményeképpen kell egy CNAME-alias használata és az application gateway DNS-címére mutasson.

## <a name="listeners"></a>Figyelők

Egy figyelő olyan logikai entitás, amely ellenőrzi a beérkező kapcsolat. Egy figyelő kérelmet fogad el, ha a protokoll, port, gazdagép és a kéréshez társított IP-cím egyezik meg a figyelő konfigurációjába társított ugyanazokat az elemeket.

Egy application gateway használatához hozzá kell adnia legalább egy figyelőt. Az application gatewayhez csatolt több kérésfigyelőt is lehet, és a egy protokoll használható.

Miután a figyelő észleli az ügyfelektől érkező bejövő kéréseket, az application gateway ezeket a kérelmeket a háttérkészletben tagok irányítja. Az application gateway használja, amely a bejövő kérelem érkezett a figyelő a megadott kérés útválasztási szabályok.

Figyelők támogatja a következő portokat és protokollokat.

### <a name="ports"></a>Portok

Egy port, ahol a figyelő az ügyfélkérelem figyeli. Portok és 1-től a v1 Termékváltozatot és 1 a 65199 65502 v2 termékváltozat konfigurálhatja.

### <a name="protocols"></a>Protokollok

Az Application Gateway négy protokollokat támogatja: HTTP, HTTPS, HTTP/2 és WebSocket:

- Adja meg a HTTP és HTTPS protokollok a figyelő konfigurációjába.
- Támogatja a [a websockets protokoll és a HTTP/2 protokoll](https://docs.microsoft.com/azure/application-gateway/overview#websocket-and-http2-traffic) biztosított natív módon, és [WebSocket-támogatás](https://docs.microsoft.com/azure/application-gateway/application-gateway-websocket) alapértelmezés szerint engedélyezve van. Kizárólag WebSocket-támogatásra vonatkozó felhasználói beállítás nem létezik. A HTTP és HTTPS-figyelőként való használja a websockets protokollt.
- HTTP/2 protokoll támogatása az application gateway figyelői-ügyfelekhez érhető el. A háttér-kiszolgálókészlethez kommunikációs HTTP/1.1 felett van. Alapértelmezés szerint a HTTP/2-támogatás le van tiltva. Ha szeretné, engedélyezze azt.

Használjon SSL-lezárást egy HTTPS-figyelő. Egy HTTPS-figyelő kiszervezi a titkosítási és visszafejtési munka az application gateway, így a webkiszolgálók nem rójanak terhelés alapján. Az alkalmazások majd használata ingyenes, az üzleti logikára összpontosíthat.

### <a name="custom-error-pages"></a>Egyéni hibalapok

Az Application Gateway segítségével hozhat létre egyéni hibalapok alapértelmezett hibák részleteiről szóló lap megjelenítése helyett. Az egyéni hibaoldalakon feltüntetheti saját védjegyeit, és egyéni elrendezést használhat. Az Application Gateway egyéni hibalap jeleníti meg, ha a háttérrendszer nem érhető el a kérelmet.

További információkért lásd: [egyéni hibalapok az application gateway számára](https://docs.microsoft.com/azure/application-gateway/custom-error).

### <a name="types-of-listeners"></a>Figyelők típusai

Figyelők két típusa van:

- **Alapszintű**. Ez a figyelő típusú figyeli egy egyetlen tartományt helyhez, ahol az application gateway IP-címére egyetlen DNS-leképezést rendelkezik. Ez a figyelő konfigurációra szükség, üzemeltetése az application gateway mögött egyetlen hely.

- **Többhelyes**. Ez a figyelő konfigurációra szükség, egynél több webalkalmazás ugyanazon alkalmazásátjárókhoz való konfigurálásakor. Lehetővé teszi a hatékonyabb topológiát konfigurálásához ad hozzá egy application gateway akár 100 webhelyet. Mindegyik webhelyet a saját háttérkészletéhez lehet irányítani. Például három altartományt, abc.contoso.com, xyz.contoso.com és pqr.contoso.com, mutasson az application gateway IP-címét. Ehhez hozzon létre három többhelyes figyelőket, és a megfelelő port és protokoll beállítását minden egyes figyelő konfigurálása.

    További információkért lásd: [több helyen üzemeltető](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview).

Miután létrehozott egy figyelőt, hogy társítsa azt egy kérelem-útválasztási szabály. Ez a szabály azt határozza meg, hogyan a kérelem érkezett a figyelőt kell átirányítani a háttérrendszerhez.

Az Application Gateway dolgozza fel a figyelővel az itt látható sorrendben. Ha alapszintű figyelő megfelel egy bejövő kérésnek, a feldolgozása először. Irányíthatja a forgalmat a háttérkészlet megfelelő, egy többhelyes figyelő előtt egy alapszintű figyelő konfigurálása.

## <a name="request-routing-rules"></a>Kérelem-útválasztási szabályok

Egy kérelem-útválasztási szabály egy application gateway egyik legfontosabb összetevőjeként azért azt határozza meg, hogyan irányíthatja a forgalmat a figyelőn. A szabály köti a figyelőt, a háttérkiszolgáló-készlet és a háttérbeli HTTP-beállítások.

Ha egy figyelő fogad egy kérést, a kérelem-útválasztási szabály továbbítja a kérést a háttérkiszolgálóra, vagy máshol átirányítja azt. Ha a rendszer a háttérrendszere továbbítja a kérést, a kérelem-útválasztási szabály továbbítani, hogy melyik háttérkiszolgáló-készletre határozza meg. Emellett a kérelem-útválasztási szabály is meghatározza, hogy a fejlécek, a kérelem-e írni. Egy figyelő egy szabályt lehet rendelni.

Kérelem-útválasztási szabályok két típusa van:

- **Alapszintű**. A kapcsolódó figyelőt (például blog.contoso.com/*) az összes kérelmet a társított háttérkészlet továbbítja a kapcsolódó HTTP-beállítás használatával.

- **Útvonalalapú**. A útválasztási szabály lehetővé teszi a kérelmek átirányítása a a kapcsolódó figyelőt, hogy egy adott háttérkészlet, a kérelem URL-cím alapján. Ha a kérelem URL-cím elérési útja megegyezik egy útvonalalapú szabály az elérésiút-minta, a szabály irányítja a kérelmet. Az elérési út mintája csak az URL-címet, nem a lekérdezési paraméterek vonatkozik. Ha az URL-címet figyelő kérelem nem egyezik a útvonalalapú szabály, az alapértelmezett háttérkészlet és a HTTP-beállítások irányítja a kérést.

További információkért lásd: [URL-cím-alapú útválasztás](https://docs.microsoft.com/azure/application-gateway/url-route-overview).

### <a name="redirection-support"></a>Átirányítási támogatás

A kérelem-útválasztási szabály lehetővé teszi az application gatewayen forgalom átirányítása. Ez az egy általános átirányítás mechanizmust, így átirányíthatja, és bármely portról szabályok használatával meghatározhatja.

Választhat egy másik figyelő (amely megkönnyíti a automatikus HTTP – HTTPS átirányításról engedélyezése) vagy külső helyre kell lennie az átirányítás célhelye. Azt is beállíthatja az ideiglenes vagy állandó átirányítás, vagy az átirányított URL-címhez fűzze hozzá a az URI elérési út és a lekérdezési karakterlánc.

További információkért lásd: [átirányítja a forgalmat az application gateway](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

### <a name="rewrite-http-headers"></a>HTTP-fejlécek átírása

A kérelem-útválasztási szabályok segítségével hozzáadása, eltávolítása vagy frissítési kérelem HTTP (S), és válaszfejlécek, mint a kérelmek és válaszok csomagok áthelyezése az ügyfél és a háttérkiszolgáló közötti készletek az application gateway-n keresztül.

A fejlécek a statikus értékre, vagy más kiszolgálói változók és állítható. Ez segít a fontos használati esetek, például az ügyfél IP-címek, a háttér hozzáadása a további biztonság érdekében bizalmas információt eltávolítása kibontása és így tovább.

További információkért lásd: [Újraírási HTTP-fejlécek az application gateway-en](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).

## <a name="http-settings"></a>HTTP-beállítások

Egy application gateway irányítja a forgalmat a háttérkiszolgálókhoz (a kérés útválasztási szabályt, amely HTTP-beállítások közé tartozik a megadott) használatával, a portszám, protokoll és egyéb beállítások részleteit a ezt az összetevőt.

A port és protokoll, a HTTP-beállítások az határozza meg, hogy az alkalmazás átjáró és a háttérkiszolgáló-kiszolgálók közötti forgalom titkosítva legyen (amely a végpontok közötti SSL) vagy titkosítatlan.

Ez az összetevő is használható:

- Megállapítható, hogy van-e a felhasználói munkamenet az ugyanazon a kiszolgálón kell tartani a [cookie-alapú munkamenet-affinitás](https://docs.microsoft.com/azure/application-gateway/overview#session-affinity).

- Biztonságosan távolítsa el a háttérkészlet-tagokra használatával [kapcsolat kiürítése](https://docs.microsoft.com/azure/application-gateway/overview#connection-draining).

- Társíthatja a háttérrendszer állapotának figyelése, a kérelem időkorlátja, állomás neve és elérési útja a kérés felülbírálása, és egyetlen kattintással egyszerűen az App Service háttérrendszer beállításainak megadásához adjon meg egy egyéni mintát.

## <a name="backend-pools"></a>Háttérkészletek

Háttérkészlet teljesíteni a kérést háttérkiszolgálók kérelmet irányítja. Háttérkészletek tartalmazza:

- Hálózati adapterek (NIC-k)
- Virtuálisgép-méretezési csoportok
- Nyilvános IP-címek
- Belső IP-címek
- Teljes tartománynév
- Több-bérlős háttérrendszerek (például az App Service-ben)

Application Gateway háttérkészlet-tagokra egy rendelkezésre állási csoport nem kötődik. Egy application gateway az, hogy a virtuális hálózaton kívüli példányok kommunikálhat. Ennek eredményeképpen a háttérkészletek tagjai lehet különböző fürtökben, adatközpontok, vagy az Azure-on kívülről, mindaddig, amíg nincs IP-kapcsolat.

Ha használja a belső IP-címek háttérkészlet-tagokra, kell használnia a [virtuális hálózatok közötti társviszony](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) vagy egy [VPN-átjáró](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Támogatott és előnyös a forgalom terhelését elosztja a többi virtuális hálózatba tartozó virtuális hálózatok közötti társviszony.

Egy application gateway is kommunikálhat a helyszíni kiszolgálók, amikor azok már kapcsolódik, Azure ExpressRoute vagy VPN-alagúton, ha a forgalom engedélyezve van.

A kérelmek különböző típusú különböző háttérkészletek hozhat létre. Hozzon létre például egy háttérkészlet általános kérelmek, és a egy másik háttérkészlet a kérelmeket az alkalmazás a mikroszolgáltatások.

## <a name="health-probes"></a>Állapotminták

Alapértelmezés szerint az application gateway a háttérkészlet összes erőforrásának állapotát figyeli, és automatikusan eltávolítja a nem megfelelő állapotú kapcsolatok. Ezután figyeli a nem megfelelő állapotú példányokat és hozzáadja őket a megfelelően működő háttér-készlet számára, amikor elérhetővé válik, és állapot-mintavételei válaszolni.

Alapértelmezett mintavételi állapotfigyelés használatán is testreszabhatja az állapotminta az alkalmazás követelményeinek megfelelően. Az egyéni minták lehetővé teszik több szabályozható a Szolgáltatásállapot-figyelést. Egyéni mintavétel használata esetén konfigurálhatja úgy a mintavételi időköz, az URL-cím és elérési útja, teszteléséhez és hány sikertelen válaszokat elfogadja a háttérkészletpéldányt állapotúként van megjelölve. Azt javasoljuk, hogy konfigurálja-e egyéni mintavételek minden háttérkészlet állapotának monitorozásához.

További információkért lásd: [az Alkalmazásátjáró állapotának figyelésére](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview).

## <a name="next-steps"></a>További lépések

Egy application gateway létrehozásához:

* [Az Azure Portalon](quick-create-portal.md)
* [Azure PowerShell-lel](quick-create-powershell.md)
* [Az Azure CLI-vel](quick-create-cli.md)
