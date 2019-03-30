---
title: Az Azure Application Gateway konfigurálása – áttekintés
description: Ez a cikk ismerteti az Azure Application Gateway összetevői konfigurálása
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: absha
ms.openlocfilehash: 371d15f59c091f7ac38d36bfe3de5f4b31e4482c
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58629639"
---
# <a name="application-gateway-configuration-overview"></a>Application Gateway konfigurálása – áttekintés

Az Azure Application Gateway, amelyek a különböző helyzetekhez különböző módokon konfigurálhatja több összetevőből áll. Ez a cikk bemutatja, hogyan konfigurálhatja az egyes összetevők.

![Application Gateway összetevők folyamatábra](./media/configuration-overview/configuration-overview1.png)

Ez a rendszerkép egy alkalmazás, amely rendelkezik három figyelői mutatja be. Az első kettő az többhelyes figyelővel a `http://acme.com/*` és `http://fabrikam.com/*`, illetve. Mindkét figyelni, a 80-as porton. A harmadik pedig egy alapszintű figyelő, amely rendelkezik teljes körű Secure Sockets Layer (SSL) megszüntetése.

## <a name="prerequisites"></a>Előfeltételek

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Az Azure virtual network és a kijelölt alhálózatot

Egy application gateway-példány dedikált központi telepítés a virtuális hálózaton. A virtuális hálózaton belüli kijelölt alhálózatot az application gateway szükség. Az alhálózat egy átjáró központi telepítésű alkalmazás több példánya rendelkezhet. Az alhálózat más application Gateway átjárókon is telepítheti. De nem telepítheti az application gateway alhálózatának más erőforráshoz.

> [!NOTE]
> Standard_v2 és a standard szintű Azure Application Gateway nem használhat egyidejűleg ugyanabban az alhálózatban.

#### <a name="size-of-the-subnet"></a>Az alhálózat mérete

Az Application Gateway példányonként 1 privát IP-cím, valamint egy másik magánhálózati IP-címet használ fel, ha egy magánhálózati előtérbeli IP-Címmel van konfigurálva.

Az Azure is lefoglalja minden egyes belső használatra alhálózatban 5 IP-címek: az első 4 és az utolsó IP-címek. Vegyük példaként a 15 application gateway-példány nincs magánhálózati előtérbeli IP-Címmel rendelkező. You need at least 20 IP addresses for this subnet: belső használatra és az application gateway-példány a 15. 5. Ezért létre kell/27-es méretet, vagy nagyobb alhálózat.

Fontolja meg, amely rendelkezik 27 application gateway-példány és a egy IP-címet egy privát előtér-IP-alhálózatot. Ebben az esetben 33 IP-cím lesz szüksége: az application gateway-példány, a privát előtéri 1 és 5 belső használatra 27. Ezért létre kell egy/26-os méretet, vagy nagyobb alhálózat.

Azt javasoljuk, hogy használja-e legalább/28-as alhálózat mérete. Ez a méret 11 használható IP-címet biztosít. Ha az alkalmazások terhelésének 10-nél több IP-címeket igényel, fontolja meg egy/27-eset vagy/26-os alhálózat méretét.

#### <a name="network-security-groups-on-the-application-gateway-subnet"></a>A hálózati biztonsági csoportok az Application Gateway-alhálózat

Hálózati biztonsági csoportok (NSG-k) az Application Gatewayen támogatottak. Azonban számos korlátozások vonatkoznak:

- Meg kell adnia a v2 szintű Termékváltozatot a bejövő forgalmat az Application Gateway v1 Termékváltozatot 65503 – 65534 portokat és 65200 – 65535 portok kivételeket. Ezen a porttartományon szükség Azure-infrastruktúra kommunikációjához. Ezeket a portokat védik (zárják le) Azure-tanúsítványok. Külső entitások – például az ügyfelek, átjárók, a hely a megfelelő tanúsítványok nélkül végpontokra módosítások nem kezdeményezhető.

- Kimenő internetkapcsolattal nem lehet blokkolni. Alapértelmezett kimenő szabályokat az NSG engedélyezéséhez. Azt javasoljuk, hogy Ön:

  - Ne távolítsa el az alapértelmezett kimenő szabályok.
  - Ne hozzon létre más kimenő szabályok, amelyek a megtagadási kimenő internetkapcsolattal.

- A forgalom a **AzureLoadBalancer** címke engedélyezni kell.

##### <a name="whitelist-application-gateway-access-to-a-few-source-ips"></a>Néhány forrás IP-címek engedélyezési lista az Application Gateway elérésére

A jelen esetben használja az Application Gateway-alhálózat NSG-k. Az alábbi korlátozások PUT ez prioritásuk szerinti sorrendben kell az alhálózaton található:

1. Engedélyezik a bejövő forgalmat a forrás IP-/ IP-címtartomány.
2. Lehetővé teszi minden forrás 65503 – 65534 portokat a beérkező kérések [háttérrendszer állapota kommunikációs](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics). Ezen a porttartományon szükség Azure-infrastruktúra kommunikációjához. Ezeket a portokat védik (zárják le) Azure-tanúsítványok. Helyben megfelelő tanúsítványok nélkül a külső entitások módosításokat a végpontokra nem kezdeményezhető.
3. Engedélyezi a bejövő Azure Load Balancer-mintavételek (*AzureLoadBalancer* címke) és a bejövő forgalom a virtuális hálózat (*VirtualNetwork* címke) az a [hálózati biztonsági csoport](https://docs.microsoft.com/azure/virtual-network/security-overview).
4. Minden egyéb adatforgalmat letiltása Megtagadás – minden szabály segítségével.
5. Az összes célhelyre az internetre irányuló kimenő forgalom engedélyezése.

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>Felhasználó által megadott útvonalakat támogatja az Application Gateway-alhálózat

Felhasználó által megadott útvonalak (udr-EK) esetében a v1 Termékváltozatot támogatottak az Application Gateway-alhálózat, mindaddig, amíg azok kérés/válasz végpontok közötti kommunikáció nem módosítható. Ha például állíthat be egy UDR az Application Gateway-alhálózat, hogy a csomagok vizsgálata készülékként egy tűzfalat mutasson. Azonban meg kell győződnie arról, hogy a csomagot a vizsgálat után elérje a kívánt rendeltetési. Ezt a helytelen állapotmintát vagy forgalom-útválasztási viselkedés eredményezhet. Ez magában foglalja a megismert vagy alapértelmezett 0.0.0.0/0 útvonalakat, amelyek a virtuális hálózat az Azure ExpressRoute vagy VPN Gateway átjárók által lépnek.

A v2 termékváltozat az udr-EK az Application Gateway-alhálózat nem támogatottak. További információkért lásd: [automatikus skálázást és az Application Gateway zóna redundancia](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#known-issues-and-limitations).

> [!NOTE]
> Az állapot ellenőrzése az udr-EK használata az Application Gateway-alhálózat hatására a [háttérrendszer állapota nézet](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health) megjelenik az "Ismeretlen". Az Application Gateway-naplók és mérőszámok sikertelen generációja is okoz. Azt javasoljuk, hogy nem használja udr-EK az Application Gateway-alhálózat, hogy a háttérrendszer állapota, naplók és metrikák is megtekintheti.

## <a name="front-end-ip"></a>Előtérbeli IP-címet

Konfigurálhatja az application gateway nyilvános IP-címmel vagy magánhálózati IP-cím. Nyilvános IP-cím megadása kötelező a háttéralkalmazás számára az ügyfelek az interneten egy internetre irányuló virtuális IP-cím (VIP) keresztül kell elérniük üzemeltetése. 

Nyilvános IP-cím nem az internettel nem érintkező belső végpont szükséges. Néven, amely egy *belső load balancer* (ILB) végponthoz. Egy application gateway ILB hasznos internettel nem érintkező belső üzleti alkalmazások. Emellett akkor is hasznos szolgáltatásokhoz, és szinten belül biztonsági határként többrétegű alkalmazásokban, amelyek nem érhetőek el az internethez, de a Ciklikus időszeleteléses igénylő terjesztési, a munkamenet tartós használatát vagy az SSL-lezárást betöltése.

Csak 1 nyilvános IP-cím vagy 1 privát IP-cím támogatott. Az application gateway létrehozásakor kiválaszthatja az előtérbeli IP-címet.

- Egy nyilvános IP-címek használatához hozzon létre egy új nyilvános IP-címet, vagy egy meglévő nyilvános IP-címet használja az application gateway ugyanazon a helyen. Ha létrehoz egy új nyilvános IP-címet, a kiválasztott IP-cím típusa (statikus vagy dinamikus) később nem módosítható. További információkért lásd: [statikus vagy dinamikus nyilvános IP-cím](https://docs.microsoft.com/en-us/azure/application-gateway/application-gateway-components#static-vs-dynamic-public-ip-address).

- Egy magánhálózati IP-címhez megadhat egy magánhálózati IP-címet az alhálózatról, ahol az application gateway létrejön. Ha nem ad meg egy, a rendszer automatikusan kiválasztja tetszőleges IP-címet az alhálózatról. További információkért lásd: [application gateway létrehozása belső terheléselosztóval](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm).

Előtérbeli IP-cím társítva egy *figyelő*, amely ellenőrzi a bejövő kéréseket az előtér-IP-címen.

## <a name="listeners"></a>Figyelők

Egy figyelő egy logikai entitás, amely ellenőrzi, bejövő kapcsolódási kérelmeket a port, protokoll, állomás és IP-cím használatával. Amikor konfigurálja a figyelőt, ezek a megfelelő értékekre, az átjárón a bejövő kérelemben szereplő értékeket kell megadnia.

Egy application gateway létrehozásakor az Azure portal használatával is létrehozhat egy alapértelmezett figyelőt válassza ki a protokollt és portot a figyelőhöz. Kiválaszthatja, hogy a HTTP2 támogatása a figyelő az engedélyezi-e. Miután létrehozta az application gateway, szerkesztheti a figyelő alapértelmezett beállításait (*appGatewayHttpListener*/*appGatewayHttpsListener*), vagy hozzon létre új figyelők.

### <a name="listener-type"></a>Figyelő típusa

Amikor létrehoz egy új figyelőt, akkor választhat [ *alapszintű* és *többhelyes*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners).

- Ha egy application gateway mögött egyetlen hely, válassza a alapszintű. Ismerje meg, [hogyan hozhat létre egy application gateway egy alapszintű figyelő](https://docs.microsoft.com/azure/application-gateway/quick-create-portal).

- Ha egynél több webalkalmazást vagy több altartománnyal, ugyanazon szülőtartomány ugyanazon application gateway-példányon konfigurálásakor, válassza ki a többhelyes figyelő. A többhelyes figyelő számára is meg kell adnia egy nevet. Ennek az oka az Application Gateway a HTTP 1.1-állomásfejlécek segítségével az ugyanazon nyilvános IP-cím és port egynél több webhely üzemeltetése támaszkodik.

#### <a name="order-of-processing-listeners"></a>A figyelők feldolgozási sorrendje

A v1 Termékváltozatot a figyelők sorrendjében, hogy a rendelés feldolgozása történik. Egy alapszintű figyelő megfelel egy bejövő kérésnek, ha a figyelő először feldolgozza a kérést. Tehát konfigurálása előtt győződjön meg arról, hogy az adatforgalom a megfelelő háttérbe legyen irányítva, az alapszintű figyelői többhelyes figyelőket.

A v2 szintű Termékváltozatot, a többhelyes figyelővel dolgozza fel előbb alapszintű figyelők.

### <a name="front-end-ip"></a>Előtérbeli IP-címet

Válassza ki az előtérbeli IP-cím, amelyet szeretne társítani ehhez a figyelőhöz. A figyelő az IP-címen bejövő kérelmek fogadni fog.

### <a name="front-end-port"></a>Előtérbeli port

Válassza ki az előtérbeli portot. Válassza ki a meglévő port, vagy hozzon létre egy újat. Válassza ki az értéket a [engedélyezett portok tartományát](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports). Nem csak a jól ismert portot, például a 80-as és 443-as, de a bármely engedélyezett, amely lehetővé teszi egyéni portot is használhat. Port figyelői nyilvános vagy privát néző figyelői is használható.

### <a name="protocol"></a>Protokoll

Válassza ki a HTTP vagy HTTPS:

- Ha úgy dönt, hogy a HTTP, az ügyfél és az application gateway közötti adatforgalom nem titkosítottak.

- Ha azt szeretné, válassza ki a HTTPS [SSL-lezárást](https://docs.microsoft.com/azure/application-gateway/overview#secure-sockets-layer-ssl-terminationl) vagy [végpontok közötti SSL-titkosítást](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Az ügyfél és az application gateway közötti adatforgalom titkosítva van. És az SSL-kapcsolatot az application gatewayben befejeződik. Ha azt szeretné, hogy a teljes körű SSL-titkosítást, válassza ki a HTTPS és konfigurálnia kell a **háttér-HTTP** beállítás. Ez biztosítja, hogy a forgalom újra titkosítva, amikor az application gateway a háttérbe való segít.

SSL-lezárások és teljes körű SSL-titkosítás konfigurálása, hozzá kell adnia egy tanúsítványt a figyelőt, hogy engedélyezze az application gateway, hogy a szimmetrikus kulcs. Ez az SSL protokoll-meghatározása szabja meg. A szimmetrikus kulcs titkosítása és visszafejtése az átjáró küldött szolgál. Az átjáró tanúsítványa a személyes információcsere (PFX) formátumúnak kell lennie. Ebben a formátumban exportálja a titkos kulcsot az átjáró által használt titkosítása és visszafejtése a teszi lehetővé.

#### <a name="supported-certificates"></a>Támogatott tanúsítványok

Lásd: [tanúsítványokat az SSL-lezárást támogatott](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination).

### <a name="additional-protocol-support"></a>További protokollok támogatása

#### <a name="http2-support"></a>A HTTP2 támogatása

HTTP/2 protokoll támogatása az application gateway figyelői kapcsolódó ügyfelek számára érhető el. A háttér kiszolgálókészlettel kommunikációs HTTP/1.1 felett van. Alapértelmezés szerint a HTTP/2-támogatás le van tiltva. Azure PowerShell a következő kódrészletet bemutatja, hogyan ennek engedélyezéséhez:

```azurepowershell
$gw = Get-AzureRmApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzureRmApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>WebSocket támogatás

Alapértelmezés szerint engedélyezve van a WebSocket-támogatás. Nem tartozik a felhasználó által konfigurálható beállítás engedélyezéséhez vagy letiltásához. A HTTP és HTTPS-figyelőként való használja a websockets protokollt.

### <a name="custom-error-pages"></a>Egyéni hibalapok

A globális vagy a figyelő szintjén egyéni hibaüzenetet adhatja meg. Azonban a globális szintű vlastní chybové stránky létrehozása az Azure Portal jelenleg nem támogatott. A figyelő szintjén konfigurálhatja a 403-as webes alkalmazás tűzfal hiba az egyéni hibalap vagy egy 502 karbantartás oldalon. Meg kell adnia egy nyilvánosan elérhető-e a blob URL-címet az adott hiba állapotkódot. További információt az [egyéni Application Gateway-hibaoldalak létrehozását ismertető részben](https://docs.microsoft.com/azure/application-gateway/custom-error) talál.

![Application Gateway-hibakódok](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

Egy globális vlastní chybovou stránku beállítása: [Azure PowerShell-konfiguráció](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration).

### <a name="ssl-policy"></a>SSL-szabályzat

SSL-tanúsítványkezelés központosítása és a titkosítási-visszafejtési egy háttér-kiszolgálófarm a terhelés csökkentése. Központi SSL-kezelési is lehetővé teszi egy központi SSL-szabályzat, amely a biztonsági követelményeknek megfelelő adja meg. Választhat *alapértelmezett*, *előre meghatározott*, vagy *egyéni* SSL-szabályzat.

A vezérlő az SSL-protokollverziók SSL-szabályzat konfigurálása Egy application gateway számára megtagadja a TLS1.0 TLS1.1 és TLS1.2 konfigurálhatja. Alapértelmezés szerint az SSL 2.0 és 3.0 le vannak tiltva, és nem konfigurálható. További információkért lásd: [Application-Gateway SSL-házirend áttekintése](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview).

Miután létrehozott egy figyelőt, hogy társítsa azt egy kérelem-útválasztási szabály. Ez a szabály azt határozza meg, hogyan legyenek átirányítva a figyelő a fogadott kéréseket a háttér.

## <a name="request-routing-rules"></a>Kérelem-útválasztási szabályok

Egy application gateway az Azure portal használatával hoz létre, amikor létrehoz egy alapértelmezett szabályt (*felhasználóval a rule1*). Ez a szabály köti a alapértelmezett figyelőt (*appGatewayHttpListener*) és az alapértelmezett háttér-címkészletet (*appGatewayBackendPool*) és az alapértelmezett háttér-HTTP-beállítások ( *appGatewayBackendHttpSettings*). Miután létrehozta az átjárót, szerkessze a beállításokat az alapértelmezett szabály, vagy létrehozhat új szabályokat.

### <a name="rule-type"></a>Szabálytípus

Szabály létrehozásakor választhat a között [ *alapszintű* és *-alapú*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rule).

- Válassza az alapszintű, ha azt szeretné továbbítani a kapcsolódó figyelőt az összes kérelem (például *blog<i></i>.contoso.com/\*)* egyetlen háttér-készlethez.
- Válassza ki a-alapú Ha irányíthatja a kérelmeket az adott URL elérési utak adott háttér-tárolókészletekben. Az elérési út mintája csak az elérési utat az URL-cím, hogy a lekérdezési paraméterek nem érvényes.

#### <a name="order-of-processing-rules"></a>A szabályok feldolgozása sorrendben

A v1 Termékváltozatot a mintamegfeleltetés bejövő kérelmek feldolgozása a ahhoz, hogy az elérési utakat az URL-Címtérkép útvonalalapú szabály szerepelnek. A kérelem megfelel a mintának két vagy több útvonal létezik az az elérési út térkép az, ha az elérési út a listában szereplő első egyezik. És a rendszer továbbítja a kérést a háttérbe, amely az elérési út van társítva.

A v2 szintű Termékváltozatot pontos egyezés magasabb prioritású, mint az elérési utat az URL-Címtérkép megadott sorrendben. Ha a kérelem megfelel a mintának két vagy több elérési utakban, a rendszer továbbítja a kérést a háttérbe, amely pontosan egyezik a kérelem elérési társított. Ha az elérési utat a bejövő kérelem nem felel meg pontosan bármilyen útvonalat a térképen, az elérési út térkép sorrend listában az útvonalalapú szabály megadhatók a kérelem feldolgozása.

### <a name="associated-listener"></a>Kapcsolódó figyelő

Társítsa a szabály a figyelőt, hogy a *kérelem-útválasztási szabály* , amely társítva van a háttérkészlet irányítsa át a kérést a meghatározásához a figyelő abban az esetben.

### <a name="associated-back-end-pool"></a>Társított háttérkészlet

Társítsa a szabály a háttérbeli készletet, amely tartalmazza a háttér-tárolók kiszolgálni a kérelmeket, amely megkapja a figyelőt.

 - Egy alapszintű szabály csak egy háttérkészlet használata engedélyezett. A kapcsolódó figyelőt az összes kérelmet továbbítja a háttérkészlethez.

 - Útvonalalapú szabály vegyen fel több háttér-címkészletet, amelyek megfelelnek a minden egyes URL-címet. A megfelelő háttérkészlet továbbítja a kérelmeket, amelyek megfelelnek a megadott URL-címet. Továbbá adja hozzá egy alapértelmezett háttér-címkészletet. A szabály minden olyan URL-cím nem egyezik a kérelem a készlet továbbítja.

### <a name="associated-back-end-http-setting"></a>Társított háttér-HTTP-beállítás

Adjon hozzá minden egyes szabály egy háttér-HTTP-beállítás. Kérelem az Alkalmazásátjáró háttérbeli célelemein kerüljön a portszám, protokoll és egyéb információkat ebben a beállításban megadott irányul.

Egy alapszintű szabály csak egy háttér-HTTP-beállítás engedélyezett. A kapcsolódó figyelőt az összes kérelmet a rendszer a HTTP-beállítás használatával továbbítja a megfelelő háttér-tárolókra.

Adjon hozzá minden egyes szabály egy háttér-HTTP-beállítás. Kérelem az application gateway szolgáltatást a háttér-tárolók használatával, a portszám, protokoll és egyéb adataihoz, ebben a beállításban megadott irányul.

Egy alapszintű szabály csak egy háttér-HTTP-beállítás engedélyezett. A a kapcsolódó figyelőt a kérelmeket a megfelelő háttér-tárolók továbbítja a HTTP-beállítás használatával.

Útvonalalapú szabály adja hozzá több háttér-HTTP-beállítások, amelyek megfelelnek az egyes URL-cím. Az URL-címet, ezzel a beállítással a feltételeknek megfelelő kérelmeket a megfelelő háttér-tárolók továbbítja a HTTP-beállítások, amelyek megfelelnek az egyes URL-cím használatával. Továbbá adja hozzá egy alapértelmezett HTTP-beállítás. Az alapértelmezett háttérkészlet-kéréseket, amelyek nem felelnek meg a szabály minden olyan URL-cím továbbítja az alapértelmezett HTTP-beállítás használatával.

### <a name="redirection-setting"></a>Átirányítási beállítás

Ha átirányítása egy alapszintű szabály van konfigurálva, a kapcsolódó figyelőt az összes kérelmet a rendszer átirányítja a cél. Ez a *globális* átirányítást. Ha átirányítása egy útvonalalapú szabály van konfigurálva, a rendszer átirányítja csak egy adott hely területen kérelmeket. Ilyen például, egy vásárlási bevásárlókocsi területet, amely helyén */cart/\**. Ez a *-alapú* átirányítást.

Átirányítások kapcsolatos további információkért lásd: [Application Gateway átirányítás áttekintése](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

#### <a name="redirection-type"></a>Átirányítás típusa

Válassza ki a szükséges átirányítás típusát: *Permanent(301)*, *Temporary(307)*, *Found(302)*, vagy *other(303) lásd*.

#### <a name="redirection-target"></a>Átirányítás célhelye

Válasszon egy másik figyelő vagy külső helyre átirányítási céljaként.

##### <a name="listener"></a>Figyelő

Válassza ki a figyelő átirányítja a forgalmat egy figyelőt a a másikra, az átjárón átirányítási céljaként. A beállítás akkor szükséges, ha szeretné engedélyezni a HTTP – HTTPS átirányításról. Átirányítja a forgalmat a forrás-figyelő, amely a bejövő HTTP-kéréseket a cél-figyelő, amely ellenőrzi a bejövő HTTPS-kéréseket, ellenőrzi. Azt is beállíthatja a lekérdezési karakterlánc és az eredeti kérelem elérési útját tartalmazza, amely az átirányítás célhelye továbbíthatja a rendszer a kérésben.

![Application Gateway összetevők párbeszédpanel](./media/configuration-overview/configure-redirection.png)

HTTP – HTTPS átirányítás kapcsolatos további információkért lásd:
- [Az Azure portal használatával HTTP-HTTP-átirányítás](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal)
- [HTTP-HTTP-átirányítás PowerShell-lel](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-powershell)
- [Az Azure CLI-vel HTTP a HTTP-átirányítás](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-cli)

##### <a name="external-site"></a>Külső webhely

Külső webhely akkor válassza, ha szeretné irányítani a forgalmat a figyelőt, hogy ez a szabály egy külső helyre van társítva. Kiválaszthatja, hogy a lekérdezési karakterláncot az eredeti kérést az szerepeljenek a kérelemhez, amely az átirányítás célhelye lesznek továbbítva. Az elérési útját, amely az eredeti kérés az volt a külső hely nem tudja továbbítani.

Átirányítás kapcsolatos további információkért lásd:
- [Forgalom átirányítása egy külső helyre a PowerShell használatával](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-powershell)
- [Forgalom átirányítása egy külső helyre a parancssori felület használatával](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-cli)

#### <a name="rewrite-the-http-header-setting"></a>A HTTP-fejléc beállítás újraírása

Ez a beállítás hozzáadása vagy eltávolítása HTTP-kérelmek és válaszfejlécek a kérelem közben frissíti, és a válasz-csomagok áthelyezése az ügyfél és a háttér-készletek között. Csak ez a funkció a PowerShell segítségével konfigurálhatja. Az Azure portal és CLI támogatási még nem érhetők el. További információkért lásd:

 - [Újraírási HTTP-fejlécek áttekintése](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)
 - [HTTP-fejléc újraírási konfigurálása](https://docs.microsoft.com/azure/application-gateway/add-http-header-rewrite-rule-powershell#specify-your-http-header-rewrite-rule-configuration)

## <a name="http-settings"></a>HTTP-beállítások

Az application gateway irányítja a forgalmat a háttérbeli kiszolgálók itt az Ön által megadott konfiguráció használatával. Miután létrehozta a HTTP-beállítás, társítania kell azt egy vagy több kérelem-útválasztási szabályok.

### <a name="cookie-based-affinity"></a>Cookie-alapú affinitás

Ez a funkció akkor hasznos, ha meg szeretné tartani a felhasználói munkamenet ugyanazon a kiszolgálón. Átjáró által kezelt cookie-k lehetővé teszik az application gateway közvetlen későbbi érkező forgalom felhasználói munkamenet ugyanarra a kiszolgálóra a feldolgozáshoz. Ez azért fontos, a munkamenet-állapot mentése helyileg a kiszolgálón a felhasználói munkamenet. Ha az alkalmazás nem tudja kezelni a cookie-alapú affinitás, nem használhatja ezt a szolgáltatást. A használatához győződjön meg róla, hogy az ügyfelek támogatják-e a cookie-kat.

### <a name="connection-draining"></a>Kapcsolatkiürítés

A kapcsolat kiürítése segítségével szabályosan tervezett szolgáltatás frissítései során a háttérkészlet-tagok eltávolítása. Ezzel a beállítással egy háttér-készlet összes tagjának alkalmazhat szabály létrehozása során. Ez biztosítja, hogy egy háttérkészlet megszüntetéséhez regisztrálása példányai nem kapja meg új kérések. Ugyanakkor meglévő kérelmek engedélyezettek a beállított időn belül befejezni. Háttérkiszolgáló-példányokhoz, amely explicit módon törlődnek a háttérkészlet-egy API-hívás által a kapcsolat kiürítése vonatkozik. Háttérkiszolgáló-példányokhoz jelentett is vonatkozik *nem megfelelő állapotú* által az állapot-mintavételei.

### <a name="protocol"></a>Protokoll

Az Application Gateway támogatja a HTTP és HTTPS a háttérkiszolgálók érkező kérések útválasztására. Ha úgy dönt, hogy a HTTP, a forgalmat a háttér-kiszolgálókon nem titkosítottak. Ha a titkosítatlan kommunikáció nem elfogadható, válassza ki a HTTPS.

Ez a beállítás a figyelő támogatja a kombinálva HTTPS [végpontok közötti SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Ez lehetővé teszi, hogy küldheti a bizalmas adatokat a háttérrendszerének titkosítva. Háttér-kiszolgálókhoz a háttér-készlet, amely teljes körű SSL engedélyezve van a biztonságos kommunikációhoz tanúsítványt kell konfigurálni.

### <a name="port"></a>Port

Ez a beállítás meghatározza azt a portot, ahol a háttérkiszolgálók figyeli a forgalmat az application gateway az. Konfigurálhatja a beállításnak 1 és 65535 közötti port.

### <a name="request-timeout"></a>Kérés időtúllépése

A beállítás akkor a válasz érkezik a háttérkészlet, mielőtt egy "kapcsolat időkorlátja lejárt" hibaüzenetet ad vissza, amely az application gateway másodpercek számát.

### <a name="override-back-end-path"></a>Bírálja felül a háttér-elérési útja

Ez a beállítás lehetővé teszi egy nem kötelező egyéni továbbítási útvonal használja, ha a rendszer továbbítja a kérést a háttérbe való konfigurálását. Bármely részét a bejövő, amely megfelel az egyéni elérési utat a **bírálja felül a háttérszolgáltatás elérési útja** mező másolja a továbbított elérési útja. Az alábbi táblázat bemutatja a funkció működésének módját:

- Amikor a HTTP-beállítás kapcsolódik egy alapszintű kérelem-útválasztási szabályt:

  | Eredeti kérést  | Bírálja felül a háttér-elérési útja | Kérelem továbbított háttéralkalmazáshoz |
  | ----------------- | --------------------- | ---------------------------- |
  | /Home/            | /override/            | / / home/felülbírálása              |
  | / home/secondhome / | /override/            | / felülbírálás/home/secondhome /   |

- Amikor a HTTP-beállítás kapcsolódik egy útvonalalapú kérelem-útválasztási szabály:

  | Eredeti kérést           | Elérésiút-szabály       | Bírálja felül a háttér-elérési útja | Kérelem továbbított háttéralkalmazáshoz |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home /            | /pathrule*      | /override/            | / / home/felülbírálása              |
  | / elérésiút-szabályban/home/secondhome / | /pathrule*      | /override/            | / felülbírálás/home/secondhome /   |
  | /Home/                     | /pathrule*      | /override/            | / / home/felülbírálása              |
  | / home/secondhome /          | /pathrule*      | /override/            | / felülbírálás/home/secondhome /   |
  | /pathrule/home /            | / elérésiút-szabályban/home * | /override/            | /override/                   |
  | / elérésiút-szabályban/home/secondhome / | / elérésiút-szabályban/home * | /override/            | / / secondhome/felülbírálása        |

### <a name="use-for-app-service"></a>Az app service használata

Ez a felhasználói felület parancsikon, ami az Azure App Service háttérrendszer két szükséges beállításait. Lehetővé teszi, hogy **válassza ki a háttér-cím állomásnév**, és létrehoz egy új egyéni mintavétel. (További információkért lásd: a [kivételezési állomásnév háttér-címről](#pick) beállítás című szakaszban.) Létrejön egy új szondát, és a mintavételi fejléc kivétele történik a háttér-tag-címről.

### <a name="use-custom-probe"></a>Egyéni mintavétel használata

Ezt a beállítást társítja a [egyéni mintavétel](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe) a HTTP-beállítás. HTTP-beállítás csak egy egyéni mintát is társíthat. Ha nincs explicit módon társíthatja egy egyéni mintát a [alapértelmezett mintavétel](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#default-health-probe-settings) a háttérrendszer állapotának figyelésére szolgál. Azt javasoljuk, hogy a nagyobb mértékben vezérelheti a háttérrendszerek állapotfigyelés az egyéni mintavétel létrehozása.

> [!NOTE]
> Az egyéni mintavétel nem monitorozza a háttérkészlet-állapotát, kivéve, ha explicit módon társítva egy figyelőt a megfelelő HTTP-beállítás.

### <a id="pick"/></a>Válassza ki a háttér-cím állomásnév

Ez a funkció dinamikusan állítja be a *gazdagép* a háttérkészlet-állomás nevét a kérelemben szereplő tartományfejléc. IP-cím vagy teljes Tartománynevet használja.

Ez a funkció segít a háttérrendszer tartományneve eltér az application gateway DNS-nevét, és a háttérrendszer egy adott állomásfejlécet vagy kiszolgálónév jelzése (SNI) bővítményt a megfelelő végpontra való feloldáshoz támaszkodik.

Egy példa funkcióban több-bérlős szolgáltatások háttérrendszerként. Az app service szolgáltatás egy több-bérlős szolgáltatás, amely egy megosztott terület egyetlen IP-címet használ. Tehát egy app Service-ben is csak keresztül érhetők el a gazdagép neve, amely az egyéni tartomány között vannak konfigurálva.

Alapértelmezés szerint az egyéni tartománynév a *example.azurewebsites.<i> </i>nettó*. Elérni az app service az application gateway egy állomásnevet, amely nincs explicit módon regisztrálva van az app service-ben vagy az application gateway FQDN használatával, bírálja felül az eredeti kérést, az app service-gazdagépnév az állomásnevet. Ehhez engedélyezze a **állomásnévvel címet válasszon** beállítás.

Meglévő egyéni DNS-nevű le van képezve az app service egyéni tartománnyal nem rendelkezik engedélyezi ezt a beállítást.

> [!NOTE]
> A beállítás nem szükséges az App Service Environment-környezet a powerapps szolgáltatásra, azaz dedikált központi telepítés.

### <a name="host-name-override"></a>Gazdagép nevének felülbírálása

Ez a lehetőség váltja fel a *gazdagép* a bejövő kérelem, az application gatewayen az Ön által megadott állomás nevét a fejlécet.

Például ha *www.contoso<i></i>.com* van megadva a **állomásnév** állítja, az eredeti kérést *https:/<i></i>/appgw.eastus.cloudapp.net/path1* módosul, amelyikben *https:/<i></i>/www.contoso.com/path1* amikor továbbítja a kérést a háttérkiszolgáló-.

## <a name="back-end-pool"></a>Háttérkészlet

A háttérkészlet mutasson négy típusú háttér tagok: egy adott virtuális gép, virtuálisgép-méretezési csoportot, egy IP-címet/FQDN vagy egy app Service-ben. Minden egyes háttérkészlethez azonos típusú több tagot is mutat. Ugyanazt a háttér-készletet a különböző típusú tagok mutató nem támogatott.

Miután létrehozott egy háttérkészlet, társítania kell azt egy vagy több kérelem-útválasztási szabályok. Az application gateway-en is minden egyes háttérkészlethez állapotfigyelő mintavételezőket kell konfigurálnia. Amikor egy kérelem-útválasztási szabály a feltétel teljesül, az application gateway továbbítja a forgalmat a kifogástalan állapotú kiszolgálók a megfelelő háttérkészlet (határoz meg az állapotmintákat).

## <a name="health-probes"></a>Állapotminták

Egy application gateway alapértelmezés szerint a háttérrendszerének összes erőforrásának állapotát figyeli. De erősen ajánlott, hogy Ön egyéni mintavétel létrehozása az egyes háttér-HTTP-beállítás nagyobb mértékben vezérelheti a Szolgáltatásállapot-figyelést beolvasásához. Egyéni mintavétel konfigurálásával kapcsolatban lásd: [egyéni egészségügyi mintavételi beállítások](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe-settings).

> [!NOTE]
> Miután létrehozott egy egyéni állapotadat-mintavétel, kell hozzárendelni a háttér-HTTP-beállítás. Egyéni mintavétel nem figyeli a háttérkészlet-állapotát, kivéve, ha explicit módon társítva egy figyelőt a megfelelő HTTP-beállítás.

## <a name="next-steps"></a>További lépések

Most, hogy megismerkedett az Application Gateway-összetevők, akkor a következőket teheti:

- [Application gateway létrehozása az Azure Portalon](quick-create-portal.md)
- [Egy application gateway létrehozása a PowerShell használatával](quick-create-powershell.md)
- [Application gateway létrehozása az Azure CLI-vel](quick-create-cli.md)
