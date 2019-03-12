---
title: Az Azure Application Gateway konfigurálása – áttekintés
description: Ez a cikk a különböző összetevők konfigurálása az Azure Application Gateway ismertetése
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 03/04/2019
ms.author: absha
ms.openlocfilehash: 702101039c03b30bb8883ef0308fe68c5567a0c4
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2019
ms.locfileid: "57733215"
---
# <a name="application-gateway-configuration-overview"></a>Application Gateway konfigurálása – áttekintés

Az Application gateway lehet megvalósítani a különböző helyzetekhez különböző módon konfigurálható több összetevőből áll. Ez a cikk végigvezeti hogyan minden összetevője kell konfigurálni.

![application-gateway-components](.\media\configuration-overview\configuration-overview1.png)

A fenti példaképen alkalmazás 3 figyelők konfigurációját mutatja be. Első többhelyes figyelővel a kettő http://acme.com/* és http://fabrikam.com/*, illetve. Mindkét figyelik a 80-as porton. A harmadik figyelő végpontok közötti SSL-lezárást az alapszintű figyelő. 

## <a name="prerequisites"></a>Előfeltételek

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Az Azure virtual network és a kijelölt alhálózatot

Application gateway-példány dedikált központi telepítés a virtuális hálózaton. A virtuális hálózaton belüli kijelölt alhálózatot az application gateway szükség. Az alhálózat egy átjáró központi telepítésű alkalmazás több példánya rendelkezhet. Az alhálózat más application Gateway átjárókon is telepítheti, de nem telepítheti az application gateway alhálózatának más erőforráshoz.  

> [!NOTE]   
> Standard_v2 és a Standard Application Gateway keverve ugyanazon az alhálózaton nem támogatott.

#### <a name="size-of-the-subnet"></a>Az alhálózat mérete

Esetén a v1 Termékváltozatot application Gateway felhasznál egy példány egy magánhálózati IP-címet, valamint egy másik magánhálózati IP-cím Ha magánhálózati előtérbeli IP-konfiguráció van konfigurálva. Emellett az Azure lefoglalja az első négy és utolsó IP-cím mindegyik olyan alhálózatban, belső használatra. Ha például egy application gateway beállítása három példányban, és nincs magánhálózati előtérbeli IP-címet, majd egy/29 méretű vagy nagyobb alhálózat szükséges. Ebben az esetben az application gateway három IP-címet használ. Ha rendelkezik három példányban és a egy IP-címet a magánhálózati előtérbeli IP-konfigurációhoz, majd egy/28-as méretet, vagy nagyobb alhálózat van szükség, mert négy IP-címeket kell megadni.

#### <a name="network-security-groups-supported-on-the-application-gateway-subnet"></a>Támogatott az Application Gateway-alhálózatot a hálózati biztonsági csoportok

Hálózati biztonsági csoportok (NSG-k) az Application Gateway-alhálózat a következő korlátozásokkal támogatottak: 

- Kivételek kell elhelyezni, bejövő forgalom a portokon 65503 – 65534 az Application Gateway v1 Termékváltozatot és portok 65200 – 65535 v2 termékváltozat. Ezen a porttartományon szükség Azure-infrastruktúra kommunikációjához. A portokat Azure-tanúsítványok védik (zárják le). Megfelelő tanúsítványok nélkül a külső entitások – például az ügyfelek átjárók kihasználására nem képes a végpontokra módosításokat kezdeményezni.

- Kimenő internetkapcsolattal nem lehet blokkolni.

- Az AzureLoadBalancer címkét a forgalmat engedélyezni kell.

##### <a name="whitelist-application-gateway-access-to-a-few-source-ips"></a>Néhány forrás IP-címek engedélyezési lista az Application Gateway elérésére

Ebben a forgatókönyvben teheti meg az application gateway alhálózatának az NSG-k használatával. Az alábbi korlátozásokat kell elhelyezni az alhálózat prioritás a listában szereplő sorrendben:

1. Engedélyezik a bejövő forgalmat a forrás IP-/ IP-címtartományt.
2. Lehetővé teszi minden forrás 65503 – 65534 portokat a beérkező kérések [háttérrendszer állapotának kommunikációs](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics). Ezen a porttartományon szükség Azure-infrastruktúra kommunikációjához. A portokat Azure-tanúsítványok védik (zárják le). Megfelelő tanúsítványok nélkül a külső entitások – például az ügyfelek átjárók nem lesz a végpontokra módosításokat kezdeményezhetnek.
3. Lehetővé teszi a bejövő Azure Load Balancer vizsgálatok (AzureLoadBalancer címke) és a bejövő virtuális hálózati forgalom (VirtualNetwork címke) a a [NSG](https://docs.microsoft.com/azure/virtual-network/security-overview).
4. Minden egyéb bejövő forgalmat egy megtagadási az összes szabály letiltása.
5. Az összes célhelyre az internetre irányuló kimenő forgalom engedélyezése.

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>Felhasználó által megadott útvonalakat támogatja az Application Gateway-alhálózat

Felhasználó által megadott útvonalak (udr-EK), a v1-Termékváltozat esetén támogatottak a az application gateway alhálózatának mindaddig, amíg nem módosítja a kérelem/válasz végpontok közötti kommunikáció.

Például beállíthat egy UDR az application gateway alhálózatának az, hogy a csomagok vizsgálata készülékként egy tűzfalat mutasson, de győződjön meg arról, hogy a csomag elérje a kívánt rendeltetési post ellenőrzés. Ezt a nem megfelelő állapot vizsgálatok és a forgalom útválasztási viselkedés eredményezhet. Ez magában foglalja a megismert vagy alapértelmezett 0.0.0.0/0 útvonalakat a virtuális hálózat ExpressRoute vagy VPN-átjárók propagálása.

V2 esetén Termékváltozat, az application gateway alhálózatának az udr-EK nem támogatottak. További információkért lásd: [automatikus skálázás és zónaredundáns Application Gateway (nyilvános előzetes verzió)](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#known-issues-and-limitations).

## <a name="frontend-ip"></a>Előtérbeli IP-címet

Konfigurálhatja az application gateway, vagy hogy a nyilvános IP-címmel vagy magánhálózati IP-cím, vagy mindkettőt. Nyilvános IP-cím kötelező, ha egy háttérszolgáltatás, amelyek elérhetők az ügyfelek által az interneten keresztül egy internetre irányuló virtuális IP-cím megadása kötelező. Nyilvános IP-cím nem csatlakozik az internethez, más néven egy belső load balancer (ILB) végpont nem érintkező belső végpont szükséges. Az átjáró ILB-vel történő konfigurálása a belső üzletági alkalmazásoknál hasznos, amelyek nem érintkeznek az internettel. Emellett a többrétegű alkalmazások szolgáltatásai és rétegei számára is hasznos, amelyek egy internettel nem érintkező biztonsági korláton belül vannak, de attól még igényelik az időszeleteléses terheléselosztást, a munkamenet tartós használatát vagy a Secure Sockets Layer (SSL) lezárását.

Csak egy nyilvános IP-cím vagy egy magánhálózati IP-cím támogatott. Az Application Gateway létrehozása során kiválaszthatja az előtérbeli IP-címet. 

- Egy nyilvános IP-cím esetén lehet váltani, hozzon létre egy új nyilvános IP-címet, vagy egy meglévő nyilvános IP-címet használja az Application Gateway ugyanazon a helyen. Ha létrehoz egy új nyilvános IP-cím, az IP-cím típusa kiválasztott (statikus vagy dinamikus) később nem módosítható. További információkért lásd: [statikus és dinamikus nyilvános IP-cím](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-vs-dynamic-public-ip) 

- Esetén egy magánhálózati IP-címet kiválaszthatja az alhálózatról, amelyben az Application Gateway létrejön egy magánhálózati IP-cím megadásához. Ha nincs explicit módon meghatározva, tetszőleges IP-cím lesz automatikusan kiválasztva az alhálózatról. További információkért lásd: [hozzon létre egy application gateway egy belső terheléselosztó (ILB) végponthoz.](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm)

Egy előtérbeli IP-Címmel társítva *figyelő* amely ellenőrzi a bejövő kéréseket az előtérbeli IP-címen.

## <a name="listeners"></a>Figyelők

Egy figyelő olyan logikai entitás, amely ellenőrzi, hogy a bejövő kérelmek kapcsolat, a port, protokoll, állomás és IP-cím használatával. Ezért amikor konfigurálja a figyelőt, meg kell adnia ezeket az értékeket port, protokoll, állomás és IP-ugyanaz a megfelelő értékeket a bejövő kérelem, az átjárón, amelyeket. Amikor létrehoz egy application gateway az Azure portal használatával, akkor is létrehozhat egy alapértelmezett figyelőt válassza ki a protokollt és portot a figyelőhöz. Emellett kiválaszthatja, hogy szeretné-e a figyelő a HTTP2 támogatásának engedélyezése. Az Application Gateway létrehozása után szerkesztheti a beállítás az alapértelmezett figyelőjének (*appGatewayHttpListener*/*appGatewayHttpsListener*) és/vagy hozzon létre új figyelők.

### <a name="listener-type"></a>Figyelő típusa

Választhat [alap- vagy többhelyes figyelő](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners) létrehozásakor egy új figyelőt. 

- Ha egy Application gateway mögött egyetlen hely üzemelteti, válassza ki az alapszintű figyelő. Ismerje meg, [hogyan hozhat létre egy application gateway az alapszintű figyelő](https://docs.microsoft.com/azure/application-gateway/quick-create-portal).

- Ha egynél több webalkalmazást vagy konfigurálásakor, ugyanazon szülőtartomány több altartománnyal ugyanazon application gateway-példányon, majd válassza ki a többhelyes figyelő. A többhelyes figyelő továbbá kell adjon meg egy állomásnevet. Ennek az oka az Application Gateway a HTTP 1.1-állomásfejlécek segítségével az ugyanazon nyilvános IP-cím és port egynél több webhely üzemeltetése támaszkodik.![1551057450710](C:\Users\absha\AppData\Roaming\Typora\typora-user-images\1551057450710.png)


> [!NOTE]
> V1 termékváltozatot, esetén figyelők láthatók a rendelés feldolgozása történik. Ezért ha egy alapszintű figyelő megfelel egy bejövő kérésnek feldolgozza a először. Ezért többhelyes figyelőket konfigurálni kell egy alapszintű figyelő annak biztosítása érdekében az adatforgalmat a megfelelő háttér előtt.
>
> V2 SKU-k esetén többhelyes figyelővel dolgozza fel előbb alapszintű figyelők.

### <a name="frontend-ip"></a>Előtérbeli IP-címet

Válassza ki az előtérbeli IP-címet, amelyet meg kíván társítani ehhez a figyelőhöz. A figyelőt a bejövő kérelem, az IP-címen fog figyelésére.

### <a name="frontend-port"></a>Előtérbeli Port

Válassza ki az előtérbeli portot. Válassza ki a meglévő portot, vagy hozzon létre egy újat. Választhat között bármilyen érték a [engedélyezett portok tartományát](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports). Ez lehetővé teszi, nem csak jól ismert, például a 80-as és 443-as portokat használó, de bármelyik engedélyezett egyéni port használatára alkalmas. Több portot vagy irányuló figyelői nyilvános vagy privát irányuló figyelői használható.

### <a name="protocol"></a>Protokoll

Kell a HTTP és HTTPS protokoll közül választhat. 

- Ha úgy dönt, hogy a HTTP, az az ügyfél és az alkalmazás-átjáró közötti forgalom nem titkosított.

- Ha érdekli, válassza a HTTPS [Secure Sockets Layer (SSL) megszüntetése](https://docs.microsoft.com/azure/application-gateway/overview#secure-sockets-layer-ssl-terminationl) vagy [teljes körű SSL-titkosítást](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Ha úgy dönt, hogy a HTTPS, a rendszer titkosítja a forgalmat az ügyfél és az alkalmazás-átjáró között, és az SSL-kapcsolatot az application gatewayben befejeződik.  Ha azt szeretné, hogy a teljes körű SSL-titkosítást, továbbá kell konfigurálása során válassza ki a HTTPS protokoll *háttérbeli HTTP-beállítás*. Ez biztosítja, hogy a forgalom újbóli titkosítva, a háttérkiszolgáló az alkalmazásátjáróról segít.

  Konfigurálja a Secure Sockets Layer (SSL)-lezárások és teljes körű SSL-titkosítást, a tanúsítvány vehető fel a figyelővel, lehetővé téve az Application Gateway, hogy a szimmetrikus kulcs megfelelően az SSL protokoll-meghatározása szükséges. A szimmetrikus kulcs titkosításához és visszafejtéséhez az átjáró küldött forgalmat majd szolgál. Az átjáró tanúsítványa kell lennie a személyes információcsere (PFX) formátumban. Ez a fájlformátum exportálja a titkos kulcsot, a titkosítási és visszafejtési forgalmat az application gateway által igényelt teszi lehetővé. 

#### <a name="supported-certs"></a>Támogatott tanúsítványok

Önaláírt tanúsítványok, a CA-tanúsítványok, a helyettesítő tanúsítványok és a Bővített-tanúsítványok használata támogatott.

### <a name="additional-protocol-support"></a>További protokollok támogatása

#### <a name="http2-support"></a>A HTTP2 támogatása

HTTP/2 protokoll támogatása az application gateway figyelői-ügyfelekhez érhető el. A háttér-kiszolgálókészlethez kommunikációs HTTP/1.1 felett van. Alapértelmezés szerint a HTTP/2-támogatás le van tiltva. A következő Azure PowerShell-lel kódrészlet példakód bemutatja, hogyan tehető lehetővé, hogy:

```azurepowershell
$gw = Get-AzureRmApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzureRmApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>Websocket-támogatás

Alapértelmezés szerint engedélyezve van a Websocket-támogatás. Kizárólag WebSocket-támogatásra vonatkozó felhasználói beállítás nem létezik. A HTTP és HTTPS-figyelőként való használja a websockets protokollt. 

### <a name="custom-error-page"></a>Vlastní Chybovou Stránku

Egyéni hibalapok határozhatók globális szinten, valamint a figyelő szintet, ugyanakkor globális szintű vlastní chybové stránky létrehozása az Azure Portal jelenleg nem támogatott. A figyelő szintjén konfigurálhatja a WAF 403-as hibaüzenet egyéni hibalap vagy egy 502 karbantartás oldalon. Is kell az adott hiba állapotkód egy nyilvánosan elérhető-e a blob URL-címet. További információkért lásd: [létrehozása egyéni hibalap](https://docs.microsoft.com/azure/application-gateway/custom-error).

![Application Gateway-hibakódok](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

Egy globális vlastní chybovou stránku konfigurálásához használja [Azure PowerShell-konfiguráció](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration) 

### <a name="ssl-policy"></a>SSL-házirend

Központosíthatja az SSL-tanúsítványok kezelését, és a egy háttér-kiszolgálófarm titkosítási és visszafejtési terhelés csökkentése. A központi SSL kezelésére is lehetővé teszi egy központi SSL-szabályzat, amely a szervezeti biztonsági követelményeknek megfelelő megadását.  Alapértelmezett, előre meghatározott és egyéni SSL-szabályzat közül választhat. 

Beállíthatja, hogy az SSL-szabályzat az SSL-protokollverziók szabályozására. Alkalmazásátjáró számára megtagadja a TLS1.0 TLS1.1 és TLS1.2 konfigurálhatja. Az SSL 2.0 és 3.0 alapértelmezés szerint már letiltott és nem konfigurálhatók. További információkért lásd: [Application-Gateway SSL-házirend áttekintése](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview).

Miután létrehozott egy figyelőt, társítsa azt egy kérés útválasztási szabályt, amely meghatározza, hogy hogyan a kérelem érkezett a figyelő a háttérrendszer lesznek irányítva.

## <a name="request-routing-rule"></a>Kérelemirányítási szabály

Az application gateway az Azure portal használatával hoz létre, miközben egy alapértelmezett szabályt hoz létre (*felhasználóval a rule1*), amely összeköti az alapértelmezett figyelő (*appGatewayHttpListener*) az alapértelmezett háttérkészlet (*appGatewayBackendPool*) és a háttérbeli HTTP-beállítások alapértelmezett (*appGatewayBackendHttpSettings*). Az application gateway létrehozása után módosíthatja a beállítást, ez az alapértelmezett szabály, illetve új szabályokat hozhat létre.

### <a name="rule-type"></a>Szabálytípus

Választhat [alap- vagy útvonalalapú szabály](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rule) új szabály létrehozása során. 

- Ha szeretné továbbítani a kapcsolódó figyelőt az összes kérelem (például: blog.contoso.com/*) egyetlen háttérkészlethez, válassza az alapszintű figyelő. 
- Válassza ki a figyelő-alapú, ha szeretné átirányítani kéréseket a meghatározott háttérkészletek az adott URL-cím. Az elérési út mintája csak az elérési utat az URL-cím, hogy a lekérdezési paraméterek nem érvényes.


> [!NOTE]
>
> V1 termékváltozatot, esetén egyező minta a bejövő kérelem feldolgozása a sorrendben, amelyben az elérési utakat az URL-Címtérkép útvonalalapú szabály szerepel. Éppen ezért, ha a kérelem megfelel a mintának a két vagy több útvonal létezik az az URL-Címtérkép, majd az elérési utat, amely szerepel a listán előbb található, karakterként lesz, és a rendszer továbbítja a kérést társított azt az elérési utat a háttérkiszolgálón.
>
> V2 SKU-k esetén pontos egyezést keresztül, amelyben az elérési utakat az URL-Címtérkép szereplő sorrendben tárolja a magasabb prioritású. Az adott ok, ha a kérelem megfelel a mintának két vagy több elérési útvonalat, a akkor a rendszer továbbítja a kérést a háttérrendszer társított, az elérési útvonalat, amely pontosan megegyezik a kérés. Az elérési utat a bejövő kérelem nem egyezik az URL-Címtérkép bármely elérési út, ha a sorrendben, amelyben az elérési utakat az URL-Címtérkép útvonalalapú szabály szerepel majd egyező minta a bejövő kérelem feldolgozása.

### <a name="associated-listener"></a>Kapcsolódó figyelő

Hozzá kell rendelni a szabály a figyelőt, hogy a *kérelemirányítási szabály* társított a *figyelő* meghatározásához abban az esetben a *háttérkészlet* , amelyhez a kérelem van át szeretné irányítani.

### <a name="associated-backend-pool"></a>Társított háttér-címkészlet

A háttérkészlet, a háttérbeli célokat szolgálnak majd a kérelmek, a figyelő által fogadott tartalmazó társítja. Egy egyszerű szabályt esetén csak egy háttérkészlet engedélyezett, mert a rendszer továbbítja a kapcsolódó figyelőt kérelmei ehhez a háttérkészlethez. Útvonalalapú szabály esetén adjon hozzá több háttérkészletek minden egyes URL-cím megfelelő. A megfelelő háttérkészlet a rendszer továbbítja a kérelmeket, amelyek egyeznek az itt megadott URL-címet. Mivel a kérelmek, amelyek nem felelnek meg az összes ebben a szabályban megadott URL-cím a rendszer továbbítja azt is, alapértelmezett háttérkészlet hozzáadása.

### <a name="associated-backend-http-setting"></a>Társított háttérbeli HTTP-beállítás

Adjon hozzá minden egyes szabály egy háttérbeli HTTP-beállítás. A kérelmeket fogja átirányítani az Alkalmazásátjáró háttérbeli célelemein kerüljön a portszám, protokoll és más ebben a beállításban megadott beállítások használatával. Egy egyszerű szabályt esetén csak egy háttérbeli HTTP-beállítás engedélyezett, mert a a kapcsolódó figyelőt a kérelmeket a rendszer továbbítja a megfelelő háttér célokat a HTTP-beállítás használatával. Útvonalalapú szabály esetén adjon hozzá több háttérbeli HTTP-beállítások minden egyes URL-cím megfelelő. A megfelelő háttér célokat, az egyes URL-cím megfelelő HTTP-beállítások használatával a rendszer továbbítja a kérelmeket, amelyek egyeznek az itt megadott URL-címet. Továbbá adja hozzá egy alapértelmezett HTTP-beállítások, mivel a rendszer továbbítja a kérelmeket, amelyek bármely ebben a szabályban megadott URL-cím nem egyezik az alapértelmezett háttérkészlet használata az alapértelmezett HTTP-beállítások.

### <a name="redirection-setting"></a>Átirányítási beállítás

Ha az átirányítási alapszintű szabály van konfigurálva, a kapcsolódó figyelőt az összes kérelem átirányítja az átirányítás célhelye, ezáltal a globális átirányítás. Ha az átirányítási konfigurálva van egy útvonalalapú szabály, a kérelmek csak egy adott hely területen, a például a bevásárlókosár-terület kimaradásával/bevásárlókocsi / *, az átirányítás célhelye, ezáltal az átirányítási elérési út alapján lesz átirányítva. 

Az átirányítás képesség kapcsolatos információkért lásd: [mappaátirányítás áttekintése](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

- #### <a name="redirection-type"></a>Átirányítás típusa

  Válassza ki a szükséges átirányítás típusát: Állandó, ideiglenes, található, vagy mások megtekintése.

- #### <a name="redirection-target"></a>Átirányítás célhelye

  Választhat egy másik figyelő vagy külső helyre átirányítási céljaként. 

  - ##### <a name="listener"></a>Figyelő

    Átirányítási céljaként lehetőséget választva figyelő segít egy figyelő átirányítása egy másik figyelő az átjárón. A beállítás akkor szükséges, ha engedélyezi a HTTP – HTTPS átirányításról, azaz a átirányítási forgalmat a forrás figyelőt a bejövő HTTP-kérések ellenőrzése a bejövő HTTPS-kéréseket a cél-figyelővel ellenőrzése. Azt is beállíthatja a lekérdezési karakterlánc és elérési útját a szerepeltetni a átirányítás célhelyre továbbítja a kérést küldött eredeti kéréssel.![application-gateway-components](.\media\configuration-overview\configure-redirection.png)

    További részletekért a HTTP – HTTPS átirányításról, tekintse meg a [portállal HTTP a HTTP-átirányítás](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal), [PowerShell-lel HTTP a HTTP-átirányítás](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-powershell), [parancssori felület használatával a HTTP, HTTP-átirányítás](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-cli)

  - ##### <a name="external-site"></a>Külső webhely

    Külső webhely akkor válassza, ha szeretné irányítani a forgalmat a figyelőt, így a szabályhoz társított átirányítja őket egy külső helyre. A lekérdezési karakterlánc szerepeltetni a átirányítás célhelyre továbbítja a kérést küldött eredeti kéréssel választhatja ki. Az elérési utat az eredeti kérést a külső hely nem tudja továbbítani.

    Átirányítás külső helyre vonatkozó további információkért lásd: [forgalom átirányítása a PowerShell-lel külső helyre](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-powershell) és [https://docs.microsoft.com/azure/application-gateway/redirect-external-site-cli](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-cli)

#### <a name="rewrite-http-header-setting"></a>HTTP-fejléc beállítás újraírása

Ez a funkció lehetővé teszi hozzáadása, eltávolítása vagy HTTP-kérelmek és válaszfejlécek történt a kérelem frissítése, és a válasz-csomagok áthelyezése az ügyfél és a háttérkiszolgáló készletek között.    Ez a funkció csak a PowerShell segítségével konfigurálhatja. Portal és CLI támogatási még nem érhető el. További információkért lásd: [Újraírási HTTP-fejlécek](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) áttekintése és [konfigurálása HTTP-fejléc újraírási](https://docs.microsoft.com/azure/application-gateway/add-http-header-rewrite-rule-powershell#specify-your-http-header-rewrite-rule-configuration).

## <a name="http-settings"></a>HTTP-beállítások

Az application gateway irányítja a forgalmat a háttérkiszolgálókhoz, ez az összetevő a megadott konfiguráció használatával. Miután létrehozott egy HTTP-beállítás, kell társítani egy vagy több kérés útválasztási szabályokat.

### <a name="cookie-based-affinity"></a>Cookie-alapú affinitás

Ez a funkció akkor hasznos, ha meg szeretné tartani a felhasználói munkamenet ugyanazon a kiszolgálón. Az átjáróval kezelt cookie-k használatával az Application Gateway képes egy felhasználói munkamenet minden újabb forgalmát ugyanarra a kiszolgálóra irányítani feldolgozásra. Ez a funkció olyan esetekben lehet fontos, amelyekben egy felhasználói munkamenethez tartozó munkamenet-állapotot helyileg ment a rendszer a kiszolgálón. Ha az alkalmazás nem tudja kezelni a cookie-alapú affinitás, majd nem tudja használni ezt a funkciót. Használ cookie-alapú munkamenet-affinitást, biztosítania kell, hogy az ügyfelek támogatnia kell a cookie-kat. 

### <a name="connection-draining"></a>Kapcsolatkiürítés

A kapcsolatkiürítéssel zökkenőmentesen végrehajtható a háttérkészlettagok eltávolítása a tervezett szolgáltatásfrissítések során. Ez a beállítás háttérkészlet összes tagjára alkalmazhatók szabály létrehozása során. Ha engedélyezve van, az application gateway biztosítja, hogy, hogy háttérkészlet megszüntetéséhez regisztrálása példányai nem kap új kérések miközben lehetővé teszi a meglévő kérések végrehajtásához beállított időkorláton belül. Ez egyaránt vonatkozik azokra a háttérpéldányokra, amelyek API-hívással, explicit módon lettek eltávolítva a háttérkészletből, és azokra is, amelyeket az állapot-mintavételek „Nem kifogástalan” állapotúnak minősítenek.

### <a name="protocol"></a>Protokoll

Az Application gateway támogatja a HTTP és HTTPS protokollok útválasztási kérelmek a háttérkiszolgálókhoz. Ha a HTTP protokollt választja, majd forgalom folyamatok nem titkosított a háttérkiszolgálókhoz. Ezekben az esetekben, ahol a háttérkiszolgálók felé irányuló titkosítatlan kommunikáció lehetőség nem elfogadható akkor a HTTPS protokollt kell választania. Ezt a beállítást, a figyelő a HTTPS protokoll kiválasztásával együtt lehetővé teszi, hogy engedélyezze [teljes körű SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Lehetővé teszi a küldheti a bizalmas adatokat, a háttérkiszolgáló titkosítva. A biztonságos kommunikációhoz tanúsítványt kell konfigurálni a teljes körű SSL-lel engedélyezett háttérkészlet minden egyes háttérkiszolgálójához.

### <a name="port"></a>Port

Ez az a port, amelyet a háttérkiszolgálók figyelik a az Application Gateway érkező forgalomra. Konfigurálhatja a beállításnak 1 és 65535 közötti port.

### <a name="request-timeout"></a>Kérés időtúllépése

Az application gateway háttérkészlet válasz érkezik egy "Kapcsolat időkorlátja lejárt" hiba visszaküldése előtt vár másodpercek számát.

### <a name="override-backend-path"></a>Háttéralkalmazás elérési útjának felülbírálása

Ezzel a beállítással konfigurálhatja egy választható egyéni továbbítási elérési útját használja, ha a rendszer továbbítja a kérést a háttérkiszolgálóra. Ez bármely részét a bejövő, amely megfelel a megadott egyéni elérési út, másolja a **bírálja felül a háttérszolgáltatás elérési útja** mezőt a továbbított elérési utat. A funkció működésének megismerése az alábbi táblázatban talál.

- Amikor a HTTP-beállítások kapcsolódik egy egyszerű kérelem-útválasztási szabály:

  | Eredeti kérést  | Háttéralkalmazás elérési útjának felülbírálása | Továbbított háttérrendszeréhez kérelem |
  | ----------------- | --------------------- | ---------------------------- |
  | /Home/            | /override/            | / / home/felülbírálása              |
  | / home/secondhome / | /override/            | / felülbírálás/home/secondhome /   |

- Amikor a HTTP-beállítások kapcsolódik egy kérelem-alapú útválasztási szabályt:

  | Eredeti kérést           | Elérésiút-szabály       | Háttéralkalmazás elérési útjának felülbírálása | Továbbított háttérrendszeréhez kérelem |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home /            | /pathrule*      | /override/            | / / home/felülbírálása              |
  | / elérésiút-szabályban/home/secondhome / | /pathrule*      | /override/            | / felülbírálás/home/secondhome /   |
  | /Home/                     | /pathrule*      | /override/            | / / home/felülbírálása              |
  | / home/secondhome /          | /pathrule*      | /override/            | / felülbírálás/home/secondhome /   |
  | /pathrule/home /            | / elérésiút-szabályban/home * | /override/            | /override/                   |
  | / elérésiút-szabályban/home/secondhome / | / elérésiút-szabályban/home * | /override/            | / / secondhome/felülbírálása        |

### <a name="use-for-app-service"></a>App Service esetén

Ez a felhasználói felület parancsikon kiválasztja a két szükséges beállításokat az App service háttérrendszer - lehetővé teszi, hogy válasszon címet állomás nevét, és létrehoz egy új egyéni mintavételt. A szakaszban az az oka, hogy miért volt történik kifejtett **állomásnévvel címet válasszon** beállítás. Egy új mintavételt jön létre, ahol a mintavétel fejlécet is ki háttér-tag-címről.

### <a name="use-custom-probe"></a>Egyéni mintavétel használata

Ezzel a beállítással társítani egy [egyéni mintavétel](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe) a HTTP-beállítás. HTTP-beállítás csak egy egyéni mintát is társíthat. Ha nincs explicit módon társít egy egyéni mintát, majd [alapértelmezett mintavétel](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#default-health-probe-settings) a háttérrendszer állapotának figyelésére használható. Javasoljuk, hogy a háttérrendszerekre állapotfigyelés részletesebb szabályozhassák egyéni mintavétel létrehozása.

> [!NOTE]   
> Egyéni mintavétel nem kezdődik a háttérkészlet állapotának figyelése, kivéve, ha explicit módon társítva egy figyelőt a megfelelő HTTP-beállításokat.

### <a name="pick-host-name-from-backend-address"></a>Háttérbeli címről származó Pick-gazdagépnév

Ez a funkció dinamikusan állítja be a *gazdagép* az állomásnév egy IP-címét vagy teljesen minősített tartománynevét (FQDN) használatával a háttérkészlet, a kérelemben szereplő tartományfejléc. Ez hasznos az esetekben, ahol a tartománynév, a háttérrendszer eltér az application gateway DNS-nevét, és a háttérrendszer támaszkodik egy adott állomásfejlécet vagy SNI-bővítményt a megfelelő végpontra való feloldáshoz, mint a több-bérlős szolgáltatások esetén a háttérrendszert. Mivel az App service egy több-bérlős szolgáltatás egy megosztott terület használata egyetlen IP-cím, egy App Service-ben a gazdagép neve, az egyéni tartomány beállításai csak a érhető el. Alapértelmezés szerint az egyéni tartománynév a *example.azurewebsites.net*. Ezért ha szeretne hozzáférni az App service az application gateway használatával, vagy egy állomásnév nem kifejezetten az App Service-ben regisztrált vagy az Application gateway teljes Tartománynevét, hogy bírálja felül az eredeti kérést, az App service-állomásnév, az állomásnév szerint engedélyezés **állomásnévvel címet válasszon** beállítás.

Ha egy egyéni tartománnyal, és a meglévő egyéni DNS-név van leképezve az App Service-ben, majd nem kívánja engedélyezni ezt a beállítást.

> [!NOTE]   
> A beállítás nem áll az App Service Environment (ASE) szükséges, mivel az ASE egy dedikált központi telepítést. 

### <a name="host-name-override"></a>Gazdagép nevének felülbírálása

Ez a lehetőség váltja fel a *gazdagép* a bejövő kérelem, az application gateway az itt megadott állomás nevét a fejlécet. Például, ha a www.contoso.com a következőként van megadva a **állomásnév** állítja, az eredeti kérést https://appgw.eastus.cloudapp.net/path1 változik https://www.contoso.com/path1 amikor rendszer továbbítja a kérést a háttérkiszolgálóra. 

## <a name="backend-pool"></a>Háttérkészlet

Háttérkészlet mutatott háttérrendszer tagok négy típusú is lehet: egy adott virtuális gép, virtuálisgép-méretezési csoportot, egy IP-címet/FQDN vagy egy app Service-ben. Minden egyes háttérkészlethez azonos típusú több tagot is mutat. Tagok ugyanazon háttérkészletéhez különböző típusú mutató nem támogatott. 

Miután létrehozta a háttérkészlet, társíthatja azt egy vagy több kérés útválasztási szabályokat szeretne. Szükség minden egyes háttérkészlethez állapotfigyelő mintavételezőket konfigurálása az application gateway-en. Amikor egy kérés útválasztási szabály a feltétel teljesül, az application gateway továbbítja a forgalmat a kifogástalan állapotú kiszolgálók a megfelelő háttérkészlet (határoz meg az állapotmintákat).

## <a name="health-probes"></a>Állapotminták

Annak ellenére, hogy az application gateway alapértelmezés szerint a háttérrendszerének összes erőforrásának állapotát figyeli, azt javasoljuk, egyéni mintavétel létrehozása az egyes háttérbeli HTTP-beállítás annak érdekében, hogy egy több szabályozható a Szolgáltatásállapot-figyelést. Egyéni állapotadat-mintavétel konfigurálása kapcsolatban lásd: [egyéni egészségügyi mintavételi beállítások](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe-settings).

> [!NOTE]   
> Ha egy egyéni állapotmintát hoz létre, meg kell társítsa azt egy háttérbeli HTTP-beállítás. Egyéni mintavétel nem kezdődik a háttérkészlet állapotának figyelése, kivéve, ha explicit módon társítva egy figyelőt a megfelelő HTTP-beállításokat.

## <a name="next-steps"></a>További lépések

Miután megismerkedett az Application Gateway-összetevők, a következőket teheti:

- [Application Gateway létrehozása az Azure Portalon](quick-create-portal.md)
- [Hozzon létre egy Application Gatewayen a PowerShell használatával](quick-create-powershell.md)
- [Hozzon létre egy Application Gateway az Azure CLI használatával](quick-create-cli.md)