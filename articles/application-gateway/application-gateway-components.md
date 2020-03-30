---
title: Alkalmazásátjáró-összetevők
description: Ez a cikk az alkalmazásátjáró különböző összetevőiről nyújt tájékoztatást
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: 90b3c3fd18bc9211c731ccf16dd646a64a4a1116
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133097"
---
# <a name="application-gateway-components"></a>Alkalmazásátjáró-összetevők

 Az alkalmazásátjáró az ügyfelek egyetlen kapcsolattartópontjaként szolgál. A bejövő alkalmazásforgalmat több háttérkészlet között osztja el, amelyek magukban foglalják az Azure virtuális gépeket, a virtuális gép méretezési csoportjait, az Azure App Service-t és a helyszíni/külső kiszolgálókat. A forgalom terjesztéséhez az alkalmazásátjáró a jelen cikkben ismertetett több összetevőt használ.

![Az alkalmazásátjáróban használt összetevők](./media/application-gateway-components/application-gateway-components.png)

## <a name="frontend-ip-addresses"></a>Előtér IP-címei

Az előtér-IP-cím az alkalmazásátjáróhoz társított IP-cím. Beállíthatja, hogy egy alkalmazásátjáró nyilvános IP-címmel, privát IP-címmel vagy mindkettővel rendelkezik. Az alkalmazásátjáró egy nyilvános vagy egy privát IP-címet támogat. A virtuális hálózatnak és a nyilvános IP-címnek ugyanazon a helyen kell lennie, mint az alkalmazásátjárónak. Létrehozása után egy előtér-IP-cím van társítva egy figyelő.

### <a name="static-versus-dynamic-public-ip-address"></a>Statikus és dinamikus nyilvános IP-cím

Az Azure Application Gateway V2 Termékváltozat konfigurálható statikus belső IP-cím és statikus nyilvános IP-cím, vagy csak statikus nyilvános IP-cím támogatása. Nem konfigurálható úgy, hogy csak a statikus belső IP-címet támogassa.

A V1 Termékváltozat konfigurálható statikus vagy dinamikus belső IP-cím és dinamikus nyilvános IP-cím támogatásához. Az Application Gateway dinamikus IP-címe nem változik a futó átjárón. Csak akkor változhat, ha leállítja vagy elindítja az átjárót. Nem változik a rendszerhibák, frissítések, Azure-gazdagép-frissítések stb. 

Az alkalmazásátjáróhoz társított DNS-név nem változik az átjáró életciklusa során. Ennek eredményeképpen egy CNAME aliast kell használnia, és az alkalmazásátjáró DNS-címére kell mutatnia.

## <a name="listeners"></a>Figyelők

A figyelő egy logikai entitás, amely ellenőrzi a bejövő kapcsolatkérelmeket. A figyelő elfogadja a kérelmet, ha a protokoll, port, állomásnév és IP-cím a kérelemhez társított egyezik a figyelő konfigurációjához társított elemekkel.

Az alkalmazásátjáró használata előtt hozzá kell adnia legalább egy figyelőt. Egy alkalmazásátjáróhoz több figyelő is lehet, és ugyanahhoz a protokollhoz használhatók.

Miután a figyelő észleli az ügyfelektől érkező bejövő kérelmeket, az alkalmazásátjáró ezeket a kérelmeket a szabályban konfigurált háttérkészlet tagjaihoz irányítja.

A figyelők a következő portokat és protokollokat támogatják.

### <a name="ports"></a>Portok

A port, ahol a figyelő figyeli az ügyfél kérelmet. A v1 Termékváltozathoz 1 és 65502 közötti, a v2 termékváltozathoz pedig 1 és 65199 közötti portokat konfigurálhat.

### <a name="protocols"></a>Protokollok

Az Application Gateway négy protokollt támogat: HTTP, HTTPS, HTTP/2 és WebSocket:
>[!NOTE]
>A HTTP/2 protokoll támogatása csak az alkalmazásátjáró-figyelőkhöz csatlakozó ügyfelek számára érhető el. A háttérkiszolgáló-készletek kommunikációja mindig HTTP/1.1 felett van. Alapértelmezés szerint a HTTP/2 támogatás le van tiltva. Engedélyezheti azt.

- Adja meg a HTTP és HTTPS protokollok között a figyelő konfigurációjában.
- A [WebSockets és a HTTP/2 protokollok](features.md#websocket-and-http2-traffic) támogatása natív módon biztosított, és a [WebSocket-támogatás](application-gateway-websocket.md) alapértelmezés szerint engedélyezve van. Kizárólag WebSocket-támogatásra vonatkozó felhasználói beállítás nem létezik. WebSockets http- és HTTPS-figyelőkkel is használható.

Https-figyelő használata a TLS-megszüntetéshez. A HTTPS-figyelő kiszervezi a titkosítási és visszafejtési munkát az alkalmazásátjáróba, így a webkiszolgálókat nem terheli a többletterhelés.

### <a name="custom-error-pages"></a>Egyéni hibalapok

Az Application Gateway lehetővé teszi, hogy az alapértelmezett hibalapok megjelenítése helyett egyéni hibalapokat hozzon létre. Az egyéni hibaoldalakon feltüntetheti saját védjegyeit, és egyéni elrendezést használhat. Az Application Gateway egy egyéni hibalapot jelenít meg, ha egy kérelem nem érhető el a háttérrendszerhez.

További információt az [alkalmazásátjáró Egyéni hibalapjai című témakörben talál.](custom-error.md)

### <a name="types-of-listeners"></a>A hallgatók típusai

Kétféle hallgató létezik:

- **Alapszintű**. Ez a figyelőtípus egyetlen tartományhelyet figyel, ahol egyetlen DNS-hozzárendeléssel rendelkezik az alkalmazásátjáró IP-címéhez. Ez a figyelőkonfiguráció akkor szükséges, ha egyetlen helyet üzemeltet egy alkalmazásátjáró mögött.

- **Többoldalas**. Ez a figyelőkonfiguráció akkor szükséges, ha egynél több webalkalmazást konfigurál ugyanazon az alkalmazásátjáró-példányon. Lehetővé teszi, hogy hatékonyabb topológiát állítson be a központi telepítésekhez, ha legfeljebb 100 webhelyet ad hozzá egy alkalmazásátjáróhoz. Mindegyik webhelyet a saját háttérkészletéhez lehet irányítani. Három altartomány, abc.contoso.com, xyz.contoso.com és pqr.contoso.com például az alkalmazásátjáró IP-címére mutatnak. Hozzon létre három többhelyes figyelők és konfigurálja az egyes figyelők a megfelelő port- és protokollbeállításhoz.

    További információ: [Multiple-site hosting](application-gateway-web-app-overview.md).

Miután létrehozott egy figyelőt, társítja azt egy kérelem-útválasztási szabályhoz. Ez a szabály határozza meg, hogy a figyelő által fogadott kérelem hogyan kell a háttérszabályos.

Az Application Gateway a [megjelenített sorrendben](configuration-overview.md#order-of-processing-listeners)dolgozza fel a hallgatókat.

## <a name="request-routing-rules"></a>Útválasztási szabályok kérése

A kérelem-útválasztási szabály az alkalmazásátjáró kulcsfontosságú összetevője, mert meghatározza a figyelő forgalom irányításának módját. A szabály köti a figyelő, a háttérkiszolgáló-készlet és a háttérrendszer HTTP-beállításait.

Amikor egy figyelő elfogadja a kérelmet, a kérelem-útválasztási szabály továbbítja a kérelmet a háttér-, vagy átirányítja azt máshol. Ha a kérelem a háttérrendszernek van továbbítva, a kérelem-útválasztási szabály határozza meg, hogy melyik háttérkiszolgáló-készletnek továbbítandó. A kérelem-útválasztási szabály azt is meghatározza, hogy a kérelemben lévő fejléceket újra kell-e írni. Egy figyelő egy szabályhoz csatolható.

A kérelem-útválasztási szabályoknak két típusa van:

- **Alapszintű**. A társított figyelő összes kérelme (például blog.contoso.com/*) továbbítása a társított háttérkészlethez a társított HTTP-beállítás használatával.

- **Elérési út alapú**. Ez az útválasztási szabály lehetővé teszi, hogy a kérelmeket a társított figyelő egy adott háttérkészlet, a kérelemben szereplő URL-cím alapján. Ha a kérelemben lévő URL-cím elérési útja megegyezik egy elérési út alapú szabály elérési útmintájával, a szabály irányítja a kérelmet. Az elérési utat csak az URL-elérési útra alkalmazza, a lekérdezési paraméterekre nem. Ha a figyelőkérés URL-elérési útja nem egyezik meg az elérési út alapú szabályok egyikével sem, a kérelmet az alapértelmezett háttérkészletbe és HTTP-beállításokba irányítja.

További információt az [URL-alapú útválasztás](url-route-overview.md)című témakörben talál.

### <a name="redirection-support"></a>Átirányítási támogatás

A kérelem-útválasztási szabály azt is lehetővé teszi, hogy átirányítsa a forgalmat az alkalmazásátjárón. Ez egy általános átirányítási mechanizmus, így átirányíthatja bármelyik portra, amelyet szabályok használatával definiál.

Kiválaszthatja az átirányítási célt, hogy egy másik figyelő (amely segíthet az automatikus HTTP-HTTPS átirányítás) vagy egy külső webhely. Azt is beállíthatja, hogy az átirányítás ideiglenes vagy állandó legyen, vagy hozzáfűzze az URI elérési útját és a lekérdezési karakterláncot az átirányított URL-címhez.

További információ: [A forgalom átirányítása az alkalmazásátjárón.](redirect-overview.md)

### <a name="rewrite-http-headers"></a>HTTP-fejlécek átírása

A kérelem-útválasztási szabályok használatával hozzáadhatja, eltávolíthatja vagy frissítheti a HTTP(S) kérés- és válaszfejléceket, miközben a kérelem- és válaszcsomagok az ügyfél- és háttérkészletek között mozognak az alkalmazásátjárón keresztül.

A fejlécek statikus értékekre vagy más fejlécekés kiszolgálóváltozókra állíthatók be. Ez segít a fontos használati esetekben, például az ügyfél IP-címek kinyerése, a háttérrendszer bizalmas adatainak eltávolítása, nagyobb biztonság hozzáadása stb.

További információt a [HTTP-fejlécek újraírása az alkalmazásátjárón című témakörben talál.](rewrite-http-headers.md)

## <a name="http-settings"></a>HTTP-beállítások

Az alkalmazásátjáró a http-beállításokat tartalmazó kérelem-útválasztási szabályban megadott háttérkiszolgálókra irányítja a forgalmat az összetevőben részletezett portszám, protokoll és egyéb beállítások használatával.

A HTTP-beállításokban használt port és protokoll határozza meg, hogy az alkalmazásátjáró és a háttérkiszolgálók közötti forgalom titkosítva van-e (végpontok közötti TLS-t biztosítva) vagy titkosítatlan.The port and protocol used in the HTTP settings determine whether the traffic between the application gateway and backend servers is encrypted (providing end-to-end TLS) or unencrypted.

Ez az összetevő a következőkre is használható:

- Határozza meg, hogy egy felhasználói munkamenetet ugyanazon a kiszolgálón kell-e tartani a [cookie-alapú munkamenet-affinitás](features.md#session-affinity)használatával.

- A [kapcsolat kiürítésével](features.md#connection-draining)szabályosan távolítsa el a háttérkészlet-tagokat.

- Egyéni mintavétel társítása a háttérrendszer állapotának figyeléséhez, a kérelem időtúllépési időközének beállításához, az állomásnév és az elérési út felülbírálásához a kérelemben, és egy kattintással adja meg az App Service-háttérrendszer beállításait.

## <a name="backend-pools"></a>Háttérmedencék

A háttérkészlet-készlet útvonalak kérelem háttér-kiszolgálók, amely a kérést szolgálja. A háttérkészletek a következőket tartalmazhatják:

- Hálózati adapterek (NIC-k)
- Virtuálisgép-méretezési csoportok
- Nyilvános IP-címek
- Belső IP-címek
- FQDN
- Több-bérlős háttérrendszerek (például App Service)

Az Application Gateway háttérkészlet-tagjai nincsenek egy rendelkezésre állási csoporthoz kötve. Az alkalmazásátjáró kommunikálhat a virtuális hálózaton kívüli példányokkal, amelyben van. Ennek eredményeképpen a háttérkészletek tagjai lehetnek fürtök, adatközpontok között vagy az Azure-on kívül, mindaddig, amíg van IP-kapcsolat.

Ha belső IP-tagokat használ háttérkészlet-tagként, [virtuális hálózati társviszony-létesítést](../virtual-network/virtual-network-peering-overview.md) vagy [VPN-átjárót](../vpn-gateway/vpn-gateway-about-vpngateways.md)kell használnia. A virtuális hálózati társviszony-létesítés támogatott és hasznos a terheléselosztási forgalom más virtuális hálózatokban.

Az alkalmazásátjáró is kommunikálhat a helyszíni kiszolgálókkal, ha az Azure ExpressRoute vagy VPN-alagutak csatlakoznak, ha a forgalom engedélyezett.

Különböző típusú kérelmekhez különböző háttérkészleteket hozhat létre. Hozzon létre például egy háttérkészletet az általános kérelmekhez, majd egy másik háttérkészletet az alkalmazás mikroszolgáltatásaihoz.

## <a name="health-probes"></a>Állapotminták

Alapértelmezés szerint az alkalmazásátjáró figyeli a háttérkészletében lévő összes erőforrás állapotát, és automatikusan eltávolítja a nem megfelelő állapotúakat. Ezután figyeli a nem megfelelő állapotú példányokat, és hozzáadja őket az egészséges háttérkészlethez, amikor azok elérhetővé válnak, és reagálnak az állapotmintákra.

Az alapértelmezett állapotvizsgáló figyelése mellett testre is szabhatja az állapotminta az alkalmazás igényeinek megfelelően. Az egyéni mintavételek részletesebb vezérlést engedélyeznek az állapotfigyelés felett. Egyéni mintavételek használatakor konfigurálhatja a mintavételi időközt, az URL-címet és a teszteléshez szükséges elérési utat, valamint azt, hogy hány sikertelen választ kell elfogadnia, mielőtt a háttérkészlet-példány nem kifogástalanként lenne megjelölve. Azt javasoljuk, hogy konfigurálja az egyéni mintavételek az egyes háttérkészletek állapotának figyelésére.

További információ: [Az alkalmazásátjáró állapotának figyelése.](../application-gateway/application-gateway-probe-overview.md)

## <a name="next-steps"></a>További lépések

Alkalmazásátjáró létrehozása:

* [Az Azure portalon](quick-create-portal.md)
* [Az Azure PowerShell használatával](quick-create-powershell.md)
* [Az Azure CLI használatával](quick-create-cli.md)
