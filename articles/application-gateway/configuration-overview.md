---
title: Az Azure Application Gateway konfigurációjának áttekintése
description: Ez a cikk az Azure-Application Gateway összetevőinek konfigurálását ismerteti.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/15/2019
ms.author: absha
ms.openlocfilehash: f42be2a3075d313a490703562761a5df13542c85
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79279220"
---
# <a name="application-gateway-configuration-overview"></a>Application Gateway konfiguráció áttekintése

Az Azure Application Gateway számos olyan összetevőből áll, amelyek különböző helyzetekben különböző módokon konfigurálhatók. Ez a cikk bemutatja, hogyan konfigurálhatja az egyes összetevőket.

![Application Gateway összetevők folyamatábrája](./media/configuration-overview/configuration-overview1.png)

Ez a rendszerkép olyan alkalmazást mutat be, amely három figyelővel rendelkezik. Az első kettő `http://acme.com/*` és `http://fabrikam.com/*`többhelyes figyelő. Figyelje a 80-es portot is. A harmadik olyan alapszintű figyelő, amely végpontok közötti SSL (SSL) megszakítással rendelkezik.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Azure-beli virtuális hálózat és dedikált alhálózat

Az Application Gateway egy dedikált üzembe helyezés a virtuális hálózaton. A virtuális hálózaton belül egy dedikált alhálózat szükséges az Application Gateway számára. Egy adott Application Gateway-telepítés több példánya is lehet egy alhálózaton. Az alhálózaton más Application Gateway-átjárókat is üzembe helyezhet. Más erőforrás azonban nem helyezhető üzembe az Application Gateway alhálózatban.

> [!NOTE]
> Standard_v2 és standard Azure-Application Gateway nem keverhető ugyanazon az alhálózaton.

#### <a name="size-of-the-subnet"></a>Az alhálózat mérete

A Application Gateway egy magánhálózati IP-címet és egy másik magánhálózati IP-címet használ, ha a magánhálózati előtéri IP-cím konfigurálva van.

Az Azure az egyes alhálózatokban 5 IP-címet is fenntart belső használatra: az első 4 és az utolsó IP-címet. Vegyünk például 15 Application Gateway-példányt, amelyek nem rendelkeznek privát előtéri IP-címmel. Ehhez az alhálózathoz legalább 20 IP-címnek kell lennie: 5 belső használatra és 15 az Application Gateway-példányokhoz. Ezért egy/27 alhálózat vagy nagyobb méretűnek kell lennie.

Vegyünk egy olyan alhálózatot, amelyben 27 Application Gateway-példány és egy magánhálózati előtér-IP-cím IP-címe található. Ebben az esetben 33 IP-cím szükséges: 27 az Application Gateway példányaihoz, 1 a privát kezelőfelülethez, és 5 belső használatra. Ezért a/26 alhálózat vagy nagyobb méretűnek kell lennie.

Azt javasoljuk, hogy legalább/28 alhálózat-alhálózati méretet használjon. Ez a méret 11 használható IP-címet biztosít. Ha az alkalmazás terhelése több mint 10 Application Gateway példányt igényel, vegye fontolóra a/27 vagy/26 alhálózat méretét.

#### <a name="network-security-groups-on-the-application-gateway-subnet"></a>Hálózati biztonsági csoportok a Application Gateway alhálózaton

Application Gateway a hálózati biztonsági csoportok (NSG-EK) támogatottak. Vannak azonban korlátozások:

- Engedélyeznie kell a bejövő internetes forgalmat a 65503-65534 **-as TCP** -portokon a Application Gateway v1 SKU-hoz, valamint a 65200-65535-es TCP-portokat a v2 SKU-hoz a célként megadott alhálózattal a **GatewayManager** szolgáltatás címkéjének megfelelően. Ez a porttartomány az Azure-infrastruktúra kommunikációja esetén szükséges. Ezeket a portokat az Azure-tanúsítványok védik (zárolják). A külső entitások, beleértve az átjárók ügyfeleit, nem tudnak kommunikálni ezeken a végpontokon.

- A kimenő internetkapcsolatot nem lehet blokkolni. A NSG alapértelmezett kimenő szabályai lehetővé teszik az internetkapcsolatot. A következő megoldást javasoljuk:

  - Ne távolítsa el az alapértelmezett kimenő szabályokat.
  - Ne hozzon létre más kimenő szabályokat, amelyek megtagadják a kimenő kapcsolatokat.

- A **AzureLoadBalancer** címkétől érkező forgalmat engedélyezni kell.

#### <a name="allow-application-gateway-access-to-a-few-source-ips"></a>Application Gateway hozzáférés engedélyezése néhány forrás IP-cím számára

Ebben a forgatókönyvben a Application Gateway alhálózat NSG használja. A következő korlátozásokat helyezze az alhálózatra az adott prioritási sorrendben:

1. Engedélyezi a bejövő forgalmat egy forrás IP-címről vagy IP-tartományból a célhelyként a teljes Application Gateway alhálózati címtartomány és célportként a bejövő hozzáférési portként, például a HTTP-hozzáférés 80-es portját.
2. A forrástól érkező, a **GatewayManager** szolgáltatásként használt és a célként megadott 65503-65534 portokként való bejövő kérések engedélyezése a Application Gateway v1 SKU esetében, valamint a **65200-65535-es** port a v2 SKU-hoz a [háttér állapotának kommunikációja](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)érdekében. Ez a porttartomány az Azure-infrastruktúra kommunikációja esetén szükséges. Ezeket a portokat az Azure-tanúsítványok védik (zárolják). A megfelelő tanúsítványok nélkül a külső entitások nem indíthatnak módosításokat ezeken a végpontokon.
3. A bejövő Azure Load Balancer mintavételek (*AzureLoadBalancer* -címkék) és a bejövő virtuális hálózati forgalom (*VirtualNetwork* címke) engedélyezése a [hálózati biztonsági csoporton](https://docs.microsoft.com/azure/virtual-network/security-overview).
4. Az összes többi bejövő forgalom blokkolása megtagadás – minden szabály használatával.
5. Az internetre irányuló kimenő adatforgalom engedélyezése az összes célhelyre.

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>A Application Gateway alhálózat által támogatott, felhasználó által megadott útvonalak

A v1 SKU esetében a felhasználó által megadott útvonalak (UDR-EK) a Application Gateway alhálózat esetében támogatottak, feltéve, hogy nem változtatják meg a végpontok közötti kérés/válasz kommunikációt. Beállíthat például egy UDR a Application Gateway alhálózatban úgy, hogy egy tűzfal-berendezésre mutasson a csomagok ellenőrzéséhez. Azonban győződjön meg arról, hogy a csomag a vizsgálat után is elérheti a kívánt célját. Ennek elmulasztása helytelen állapot-mintavételi vagy forgalom-útválasztási viselkedést eredményezhet. Ebbe beletartoznak a megtanult útvonalak vagy az alapértelmezett 0.0.0.0/0 útvonalak, amelyeket a virtuális hálózat Azure ExpressRoute vagy VPN-átjárói továbbítanak.

A v2 SKU esetében a UDR nem támogatottak a Application Gateway alhálózaton. További információ: [Azure Application Gateway v2 SKU](application-gateway-autoscaling-zone-redundant.md#differences-with-v1-sku).

> [!NOTE]
> A v2 SKU jelenleg nem támogatja a UDR.

> [!NOTE]
> Ha a UDR-t használja az Application Gateway alhálózaton, a [háttér állapot nézetében](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health) a "Unknown" érték jelenik meg. Azt is okozhatja, hogy Application Gateway naplók és mérőszámok generálása meghiúsul. Azt javasoljuk, hogy ne használja a UDR a Application Gateway alhálózaton, így megtekintheti a háttér állapotát, a naplókat és a metrikákat.

## <a name="front-end-ip"></a>Előtér-IP-cím

Beállíthatja, hogy az Application Gateway nyilvános IP-címmel, magánhálózati IP-címmel vagy mindkettővel rendelkezzen. Egy nyilvános IP-címet kell megadni, ha a háttérrendszer futtatásakor az ügyfeleknek internetre irányuló virtuális IP-címmel (VIP) keresztül kell elérniük az internetet. 

Nem szükséges egy nyilvános IP-cím egy olyan belső végponthoz, amely nem érhető el az interneten. Ezt nevezzük *belső terheléselosztó-* (ILB-) végpontnak vagy privát előtér-IP-nek. Az Application Gateway ILB olyan belső üzletági alkalmazások esetében hasznos, amelyek nem érhetők el az interneten. A többrétegű alkalmazások szolgáltatásai és szintjei is hasznosak egy olyan biztonsági határon belül, amely nem érhető el az interneten, azonban a ciklikus időszeleteléses terheléselosztást, a munkamenet-stickiet vagy az SSL-lezárást igényli.

Csak 1 nyilvános IP-cím vagy 1 magánhálózati IP-cím támogatott. Az előtérbeli IP-címet az Application Gateway létrehozásakor választhatja ki.

- Nyilvános IP-cím esetén létrehozhat egy új nyilvános IP-címet, vagy használhat egy meglévő nyilvános IP-címet az Application Gateway szolgáltatással megegyező helyen. További információ: statikus vagy [dinamikus nyilvános IP-cím](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-versus-dynamic-public-ip-address).

- A magánhálózati IP-címek esetében megadhat egy magánhálózati IP-címet abban az alhálózatban, amelyben az Application Gateway létrejött. Ha nem ad meg ilyet, a rendszer automatikusan kijelöl egy tetszőleges IP-címet az alhálózatból. A kiválasztott IP-cím típusa (statikus vagy dinamikus) később nem módosítható. További információ: [Application Gateway létrehozása belső terheléselosztó használatával](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm).

Az előtér-IP-cím egy *figyelőhöz*van társítva, amely az ELŐTÉR-IP-címen érkező kérelmeket ellenőrzi.

## <a name="listeners"></a>Hallgatók

A figyelő olyan logikai entitás, amely a port, a protokoll, a gazdagép és az IP-cím használatával ellenőrzi a bejövő kapcsolati kérelmeket. A figyelő konfigurálásakor meg kell adnia azokat az értékeket, amelyek megfelelnek az átjárón bejövő kérelemben szereplő megfelelő értékeknek.

Amikor az Azure Portal használatával hoz létre egy Application Gateway-t, a figyelőhöz tartozó protokoll és port választásával létrehoz egy alapértelmezett figyelőt is. Megadhatja, hogy engedélyezi-e a HTTP2 támogatását a figyelőn. Az Application Gateway létrehozása után szerkesztheti az alapértelmezett figyelő (*appGatewayHttpListener*) beállításait, vagy létrehozhat új figyelőket.

### <a name="listener-type"></a>Figyelő típusa

Új figyelő létrehozásakor az [ *alapszintű* és a *többhelyes*beállítások](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners)közül választhat.

- Ha azt szeretné, hogy minden kérelem (bármely tartományhoz) el legyen fogadva és továbbítva legyen a háttérbeli készletekbe, válassza az alapszintű lehetőséget. Útmutató az [Application Gateway alapszintű figyelővel való létrehozásához](https://docs.microsoft.com/azure/application-gateway/quick-create-portal).

- Ha a kérelmeket a *állomásfejléc* vagy az állomásnév alapján különböző háttérbeli készletekre szeretné továbbítani, válassza a többhelyes figyelő lehetőséget, ahol meg kell adnia a bejövő kérelemnek megfelelő állomásnevet is. Ennek az az oka, hogy Application Gateway a HTTP 1,1 állomásfejléc-ra támaszkodik, hogy egynél több webhelyet működtessen ugyanazon a nyilvános IP-címen és porton.

#### <a name="order-of-processing-listeners"></a>Feldolgozási figyelők sorrendje

A v1 SKU esetében a kérelmek sorrendje a szabályok sorrendjének és a figyelő típusának megfelelően történik. Ha az alapszintű figyelővel rendelkező szabály először a sorrendben kerül feldolgozásra, a rendszer először feldolgozza, és fogadja a portra és az IP-kombinációra vonatkozó összes kérelmet. Ennek elkerülése érdekében először konfigurálja a többhelyes figyelőkkel rendelkező szabályokat, és az alapszintű figyelővel küldje el a szabályt az utolsó listán.

A v2 SKU esetében a többhelyes figyelők az alapszintű figyelők előtt lesznek feldolgozva.

### <a name="front-end-ip"></a>Előtér-IP-cím

Válassza ki azt az előtér-IP-címet, amelyet hozzá szeretne rendelni ehhez a figyelőhöz. A figyelő a bejövő kérelmeket az adott IP-címen fogja figyelni.

### <a name="front-end-port"></a>Előtér-port

Válassza ki az előtér-portot. Válasszon ki egy meglévő portot, vagy hozzon létre egy újat. Válassza ki a [portok megengedett tartományának](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports)tetszőleges értékét. Nem csak a jól ismert portok, például a 80 és a 443, de a megfelelő egyéni portok is használhatók. Egy port használható nyilvános figyelők vagy magánjellegű figyelők számára.

### <a name="protocol"></a>Protokoll

HTTP vagy HTTPS kiválasztása:

- Ha a HTTP lehetőséget választja, az ügyfél és az Application Gateway közötti forgalom titkosítatlan.

- Válassza a HTTPS lehetőséget, ha [SSL-lezárást](https://docs.microsoft.com/azure/application-gateway/overview#secure-sockets-layer-ssltls-termination) vagy [végpontok közötti SSL-titkosítást](https://docs.microsoft.com/azure/application-gateway/ssl-overview)szeretne használni. Az ügyfél és az Application Gateway közötti forgalom titkosítva van. Az SSL-kapcsolat leáll az Application gatewaynél. Ha a végpontok közötti SSL-titkosítást szeretné használni, a HTTPS lehetőséget kell választania, és konfigurálnia kell a **háttérbeli http** -beállítást. Ez biztosítja, hogy a forgalom újra titkosítva legyen, amikor az Application Gateway-ből a háttérbe utazik.

Az SSL-lezárás és a végpontok közötti SSL-titkosítás konfigurálásához hozzá kell adnia egy tanúsítványt a figyelőhöz, hogy az Application Gateway szimmetrikus kulcsot lehessen származtatni. Ezt az SSL protokoll specifikációja határozza meg. A szimmetrikus kulcs az átjárónak továbbított forgalom titkosítására és visszafejtésére szolgál. Az átjáró tanúsítványának személyes információcsere (PFX) formátumúnak kell lennie. Ez a formátum lehetővé teszi a titkos kulcs exportálását, amelyet az átjáró használ a forgalom titkosításához és visszafejtéséhez.

#### <a name="supported-certificates"></a>Támogatott tanúsítványok

Tekintse [meg az SSL-lezáráshoz támogatott tanúsítványokat](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination).

### <a name="additional-protocol-support"></a>További protokollok támogatása

#### <a name="http2-support"></a>HTTP2-támogatás

A HTTP/2 protokoll támogatása csak az Application Gateway-figyelőkhöz csatlakozó ügyfelek számára érhető el. A háttér-kiszolgáló készletekkel való kommunikáció HTTP/1.1-n keresztül történik. Alapértelmezés szerint a HTTP/2 támogatás le van tiltva. A következő Azure PowerShell kódrészlet azt mutatja be, hogyan engedélyezheti ezt:

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>WebSocket támogatás

A WebSocket-támogatás alapértelmezés szerint engedélyezve van. A felhasználó által konfigurálható beállítás nem engedélyezhető vagy tiltható le. A websocketek a HTTP és a HTTPS-figyelővel is használhatók.

### <a name="custom-error-pages"></a>Egyéni hibalapok

Egyéni hibát a globális szinten vagy a figyelő szintjén adhat meg. Azonban a Azure Portal globális szintű egyéni hibaüzenetek létrehozása jelenleg nem támogatott. Az 403-es webalkalmazási tűzfal hibája, vagy a figyelő szintjén a 502-es karbantartási oldal egyéni hibaüzenetet adhat meg. Meg kell adnia egy nyilvánosan elérhető blob URL-címet is az adott hibakódhoz. További információt az [egyéni Application Gateway-hibaoldalak létrehozását ismertető részben](https://docs.microsoft.com/azure/application-gateway/custom-error) talál.

![Application Gateway hibakódok](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

A globális egyéni hiba lap konfigurálásához lásd: [Azure PowerShell konfiguráció](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration).

### <a name="ssl-policy"></a>SSL-szabályzat

Központosíthatja az SSL-tanúsítványok kezelését, és csökkentheti a titkosítási és visszafejtési terhelést a háttér-kiszolgálófarm számára. A központosított SSL-kezelést is lehetővé teszi, hogy megadhat egy központi SSL-házirendet, amely megfelel a biztonsági követelményeknek. Kiválaszthatja az *alapértelmezett*, az *előre definiált*vagy az *Egyéni* SSL-házirendet.

Az SSL-szabályzat konfigurálásával szabályozhatja az SSL protokoll verzióit. Az Application Gateway konfigurálható úgy, hogy a TLS-kézfogások minimális protokoll-verzióját használják a TLS 1.0, a TLS 1.1 és a TLS 1.2 használatával. Alapértelmezés szerint az SSL 2,0 és 3,0 le van tiltva, és nem konfigurálható. További információ: [Application Gateway SSL-házirend áttekintése](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview).

A figyelő létrehozása után társítsa azt egy kérelem-útválasztási szabállyal. Ez a szabály határozza meg, hogy a figyelőre érkező kérések hogyan legyenek átirányítva a háttér felé.

## <a name="request-routing-rules"></a>Kérelmek útválasztási szabályai

Amikor az Azure Portal használatával hoz létre egy Application Gateway-t, létrehoz egy alapértelmezett szabályt (*rule1*). Ez a szabály az alapértelmezett háttér-készlettel (*appGatewayBackendPool*) és az alapértelmezett HÁTTÉRbeli http-beállításokkal (*appGatewayBackendHttpSettings*) köti össze az alapértelmezett figyelőt (*appGatewayHttpListener*). Az átjáró létrehozása után szerkesztheti az alapértelmezett szabály beállításait, vagy létrehozhat új szabályokat is.

### <a name="rule-type"></a>Szabály típusa

Szabály létrehozásakor az [ *alapszintű* és az *elérési út alapján*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rules)kell választania.

- Válassza az alapszintű lehetőséget, ha szeretné továbbítani az összes kérelmet a társított figyelőn (például: *blog<i></i>. contoso.com/\*)* egyetlen háttérbeli készletre.
- Válassza az elérésiút-alapú lehetőséget, ha az adott URL-címről érkező kérelmeket adott háttérbeli készletekre szeretné irányítani. Az elérésiút-minta csak az URL elérési útjára lesz alkalmazva, nem pedig a lekérdezési paraméterekre.

#### <a name="order-of-processing-rules"></a>Feldolgozási szabályok sorrendje

A v1 SKU esetében a bejövő kérések egyeztetését abban a sorrendben dolgozza fel a rendszer, hogy az elérési utak megjelennek az elérésiút-alapú szabály URL-címének elérési útja alatt. Ha egy kérelem megegyezik az elérési út két vagy több elérési útjában szereplő mintázattal, az elsőként megjelenő elérési út illeszkedik. A rendszer továbbítja a kérést az elérési úthoz társított háttér-végponthoz.

A v2 SKU esetében az URL-cím elérési útjának pontos egyezése magasabb prioritás, mint az elérésiút-sorrend. Ha egy kérelem két vagy több elérési úton egyezik a mintával, a rendszer továbbítja a kérést a háttérnek, amely ahhoz az elérési úthoz van társítva, amely pontosan megfelel a kérelemnek. Ha a bejövő kérelem elérési útja nem egyezik meg pontosan a Térkép egyik elérési útjával sem, a rendszer a kérelem megfeleltetését dolgozza fel a Path-alapú szabály elérésiút-leképezési sorrendjének listájában.

### <a name="associated-listener"></a>Társított figyelő

Rendeljen egy figyelőt a szabályhoz, hogy a figyelőhöz társított *kérelem-útválasztási szabály* kiértékelése megtörténjen, hogy a rendszer a kérést átirányítsa a háttér-készletbe.

### <a name="associated-back-end-pool"></a>Társított háttérrendszer-készlet

Rendeljen hozzá egy olyan háttér-készletet, amely tartalmazza a figyelő által fogadott kérelmeket kézbesítő háttérbeli célokat.

 - Alapszintű szabályok esetében csak egy háttérbeli készlet engedélyezett. A társított figyelő összes kérelmét továbbítja a rendszer a háttér-készletnek.

 - Elérésiút-alapú szabály esetén adjon hozzá több háttér-készletet, amelyek megfelelnek az egyes URL-címek elérési útjának. A megadott URL-címhez tartozó kérelmek továbbítva lesznek a megfelelő háttér-készletbe. Emellett adjon hozzá egy alapértelmezett háttér-készletet. Azok a kérelmek, amelyek nem egyeznek meg a szabály URL-címével, továbbítva lesznek erre a készletre.

### <a name="associated-back-end-http-setting"></a>Társított háttérrendszer HTTP-beállítása

Adjon hozzá egy háttérbeli HTTP-beállítást az egyes szabályokhoz. A rendszer a kérelmeket az Application Gateway-ből a háttérbeli célokhoz irányítja a beállításban megadott portszám, protokoll és egyéb információk használatával.

Alapszintű szabályok esetében csak egy háttérbeli HTTP-beállítás engedélyezett. Ezzel a HTTP-beállítással a társított figyelő összes kérelmét továbbítja a rendszer a megfelelő háttérbeli tárolókra.

Elérésiút-alapú szabály esetén adjon hozzá több háttérbeli HTTP-beállítást, amelyek megfelelnek az egyes URL-címek elérési útjának. Az ebben a beállításban szereplő URL elérési úttal egyező kérelmeket a rendszer az egyes URL-címekhez tartozó HTTP-beállításokkal továbbítja a megfelelő háttérbeli célokhoz. Emellett adjon hozzá egy alapértelmezett HTTP-beállítást. Azok a kérelmek, amelyek nem felelnek meg a szabály URL-címének, az alapértelmezett HTTP-beállítással továbbítódnak az alapértelmezett háttér-készletbe.

### <a name="redirection-setting"></a>Átirányítás beállítása

Ha az átirányítás alapszintű szabályhoz van konfigurálva, a rendszer a társított figyelő összes kérelmét átirányítja a célhelyre. Ez *globális* átirányítás. Ha az átirányítás egy elérésiút-alapú szabályhoz van konfigurálva, akkor a rendszer csak egy adott hely területén lévő kérelmeket irányítja át. Ilyenek például a */cart/\** által jegyzett bevásárlókosár-területek. Ez az *elérésiút-alapú* átirányítás.

További információ az átirányításokról: [Application Gateway átirányítások áttekintése](redirect-overview.md).

#### <a name="redirection-type"></a>Átirányítás típusa

Válassza ki a szükséges átirányítás típusát: *Permanent (301)* , *ideiglenes (307*), *Found (302)* , vagy *más (303)* .

#### <a name="redirection-target"></a>Átirányítás célja

Válasszon egy másik figyelőt vagy egy külső helyet az átirányítás céljaként.

##### <a name="listener"></a>Hallgató

Válassza a figyelő lehetőséget, hogy átirányítsa a forgalmat az egyik figyelőről egy másikra az átjárón. Erre a beállításra akkor van szükség, ha engedélyezni szeretné a HTTP – HTTPS átirányítást. Átirányítja a forgalmat a forrás-figyelőtől, amely ellenőrzi a bejövő HTTP-kéréseket a cél figyelőnek, amely ellenőrzi a bejövő HTTPS-kéréseket. Azt is megteheti, hogy a lekérdezési karakterláncot és az elérési utat az eredeti kérelemből is felveszi az átirányítási célra továbbított kérelembe.

![Összetevők Application Gateway párbeszédpanel](./media/configuration-overview/configure-redirection.png)

További információ a HTTP – HTTPS átirányítással kapcsolatban:
- [HTTP – HTTPS átirányítás a Azure Portal használatával](redirect-http-to-https-portal.md)
- [HTTP – HTTPS átirányítás a PowerShell használatával](redirect-http-to-https-powershell.md)
- [HTTP – HTTPS átirányítása az Azure CLI használatával](redirect-http-to-https-cli.md)

##### <a name="external-site"></a>Külső hely

Válassza a külső hely lehetőséget, ha át szeretné irányítani a szabályhoz társított figyelő forgalmát egy külső helyre. Dönthet úgy, hogy a lekérdezési karakterláncot az eredeti kérelemből adja meg az átirányítási célra továbbított kérelemben. Az elérési út nem továbbítható az eredeti kérelemben szereplő külső helyhez.

További információ az átirányítással kapcsolatban:
- [Forgalom átirányítása egy külső helyre a PowerShell használatával](redirect-external-site-powershell.md)
- [Forgalom átirányítása egy külső helyre a parancssori felület használatával](redirect-external-site-cli.md)

#### <a name="rewrite-the-http-header-setting"></a>A HTTP-fejléc beállításának újraírása

Ezzel a beállítással a HTTP-kérések és a válaszok fejlécei is hozzáadhatók, eltávolíthatók vagy frissülnek, míg a kérelmek és válaszok csomagjai az ügyfél és a háttérbeli készletek között mozognak. További információkért lásd:

 - [HTTP-fejlécek újraírása – áttekintés](rewrite-http-headers.md)
 - [HTTP-fejléc újraírásának konfigurálása](rewrite-http-headers-portal.md)

## <a name="http-settings"></a>HTTP-beállítások

Az Application Gateway az itt megadott konfiguráció használatával irányítja át a forgalmat a háttér-kiszolgálókra. A HTTP-beállítás létrehozása után hozzá kell rendelnie egy vagy több kérelem-útválasztási szabályhoz.

### <a name="cookie-based-affinity"></a>Cookie-alapú affinitás

Az Azure Application Gateway átjáróval felügyelt cookie-kat használ a felhasználói munkamenetek karbantartásához. Amikor egy felhasználó az első kérést Application Gateway küldi el, az egy affinitási cookie-t állít be a válaszban egy olyan kivonatoló értékkel, amely tartalmazza a munkamenet részleteit, így az affinitási cookie-t tartalmazó további kérelmeket ugyanarra a háttér-kiszolgálóra irányítja a rendszer a stickiion karbantartása. 

Ez a funkció akkor hasznos, ha egy felhasználói munkamenetet szeretne megőrizni ugyanazon a kiszolgálón, és amikor a munkamenet-állapotot helyileg menti a kiszolgálón egy felhasználói munkamenethez. Ha az alkalmazás nem tudja kezelni a cookie-alapú affinitást, nem használhatja ezt a funkciót. A használatához győződjön meg arról, hogy az ügyfelek támogatják a cookie-kat.

A [Chromium böngésző](https://www.chromium.org/Home) [V80 frissítése](https://chromiumdash.appspot.com/schedule) olyan mandátumot hozott, amelyben a [SameSite](https://tools.ietf.org/id/draft-ietf-httpbis-rfc6265bis-03.html#rfc.section.5.3.7) attribútum nélküli http-cookie-kat SameSite = LAX-ként kell kezelni. CORS (eltérő eredetű erőforrás-megosztási) kérelmek esetén, ha a cookie-t harmadik féltől származó környezetben kell elküldeni, akkor a SameSite = none értéket kell használnia *. Biztonságos* attribútumok, és csak HTTPS protokollal küldhetők el. Ellenkező esetben a böngészőben a böngésző nem küldi el a cookie-kat a harmadik féltől származó környezetben. Ennek a frissítésnek a célja, hogy fokozza a biztonságot, és elkerülje a helyek közötti kérelmek hamisításának (CSRF) elleni támadásait. 

Ha támogatni szeretné ezt a változást, február 17 – 2020, Application Gateway (az összes SKU-típus) bekerül egy másik, *ApplicationGatewayAffinityCORS* nevű cookie-t a meglévő *ApplicationGatewayAffinity* -cookie mellett. A *ApplicationGatewayAffinityCORS* -cookie két további attribútummal bővült ( *"SameSite = none; Biztonságos "* ), így a Sticky-munkamenetek még a forráson kívüli kérelmek esetében is megmaradnak.

Vegye figyelembe, hogy az alapértelmezett affinitási cookie neve *ApplicationGatewayAffinity* , és módosítható. Ha egyéni affinitási cookie-nevet használ, további cookie-t adunk hozzá a CORS utótagként. Például: *CustomCookieNameCORS*.

> [!NOTE]
> Ha a *SameSite = none* attribútum be van állítva, akkor kötelező, hogy a cookie a *biztonságos* jelzőt is tartalmazza, és HTTPS-kapcsolaton keresztül kell elküldeni.  Ha a CORS-kapcsolaton keresztül szükséges a munkamenet-affinitás, a számítási feladatokat HTTPS-re kell áttelepítenie. Tekintse meg az SSL-alapú kiszervezést és a teljes körű SSL-dokumentációt Application Gateway itt – [Áttekintés](ssl-overview.md), [útmutató az SSL-kiszervezés konfigurálásához](create-ssl-portal.md), a [végpontok közötti SSL konfigurálásához](end-to-end-ssl-portal.md).

### <a name="connection-draining"></a>Kapcsolatkiürítés

A kapcsolatok kiürítése megkönnyíti a háttérbeli készlet tagjainak biztonságos eltávolítását a tervezett szolgáltatási frissítések során. Ezt a beállítást a szabályok létrehozásakor a háttérbeli készlet összes tagjára alkalmazhatja. Gondoskodik róla, hogy a háttér-készletek összes regisztrációja továbbra is megőrizze a meglévő kapcsolatokat, és a rendelkezésre állási kérelmeket a konfigurálható időtúllépés mellett kézbesítse, és ne kapjon új kéréseket és kapcsolatokat. Ez alól kivételt képeznek a példányok deregisztrációja az átjáró által felügyelt munkamenet-affinitás miatt, és a rendszer továbbra is továbbítja őket a deregistering példányokra. A kapcsolatok kiürítése olyan háttérbeli példányokra vonatkozik, amelyek kifejezetten eltávolíthatók a háttér-készletből.

### <a name="protocol"></a>Protokoll

A Application Gateway a HTTP-t és a HTTPS-t is támogatja a háttér-kiszolgálókra irányuló útválasztási kérelmek esetében. Ha a HTTP lehetőséget választja, a háttér-kiszolgálókra irányuló forgalom titkosítatlan. Ha a titkosítatlan kommunikáció nem elfogadható, válassza a HTTPS lehetőséget.

A figyelőben lévő HTTPS-vel kombinált beállítás támogatja a [végpontok közötti SSL-t](ssl-overview.md). Ez lehetővé teszi, hogy biztonságosan továbbítsa a háttérben titkosított bizalmas adatokat. A végpontok közötti SSL-t használó háttérbeli készletben lévő minden háttér-kiszolgálót tanúsítvány használatával kell konfigurálni a biztonságos kommunikáció érdekében.

### <a name="port"></a>Port

Ez a beállítás határozza meg azt a portot, ahol a háttér-kiszolgálók az Application gatewaytől érkező forgalmat figyelik. A portok 1-től 65535-ig állíthatók be.

### <a name="request-timeout"></a>Kérelem időtúllépése

Ez a beállítás azon másodpercek száma, ameddig az Application Gateway várakozik a háttér-kiszolgálótól érkező válasz fogadására.

### <a name="override-back-end-path"></a>Háttérbeli elérési út felülbírálása

Ezzel a beállítással beállíthatja, hogy a rendszer mikor továbbítsa a kérést a háttérbe, ha nem kötelező egyéni továbbítási útvonalat szeretne használni. A rendszer átmásolja a **felülbírálási háttér elérési útjának** egyéni elérési útjával megegyező bejövő elérési út bármely részét a továbbított elérési útra. A következő táblázat bemutatja, hogyan működik ez a szolgáltatás:

- Ha a HTTP-beállítás egy alapszintű kérelemhez van csatolva – útválasztási szabály:

  | Eredeti kérelem  | Háttérbeli elérési út felülbírálása | Kérelem továbbítva a háttérbe |
  | ----------------- | --------------------- | ---------------------------- |
  | /Home            | felülbírálás            | /override/home/              |
  | /home/secondhome/ | felülbírálás            | /override/home/secondhome/   |

- Ha a HTTP-beállítás egy elérésiút-alapú kérelem – útválasztási szabályhoz van csatolva:

  | Eredeti kérelem           | Elérésiút-szabály       | Háttérbeli elérési út felülbírálása | Kérelem továbbítva a háttérbe |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | elérésiút      | felülbírálás            | /override/home/              |
  | /pathrule/home/secondhome/ | elérésiút      | felülbírálás            | /override/home/secondhome/   |
  | /Home                     | elérésiút      | felülbírálás            | /override/home/              |
  | /home/secondhome/          | elérésiút      | felülbírálás            | /override/home/secondhome/   |
  | /pathrule/home/            | /pathrule/home* | felülbírálás            | felülbírálás                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | felülbírálás            | /override/secondhome/        |
  | elérésiút                 | elérésiút      | felülbírálás            | felülbírálás                   |

### <a name="use-for-app-service"></a>Az App Service használata

Ez egy felhasználói felületi parancsikon, amely kiválasztja a Azure App Service háttér két kötelező beállítását. Lehetővé teszi **az állomásnév kiválasztását a háttérbeli címről**, és létrehoz egy új egyéni mintavételt, ha még nem rendelkezik ilyennel. (További tudnivalókért tekintse meg a jelen cikk az [állomásnév kiválasztása a háttérbeli címekről](#pick) beállítását ismertető szakaszt.) Létrejön egy új mintavétel, és a mintavételi fejléc a háttérbeli tag címe alapján van kiválasztva.

### <a name="use-custom-probe"></a>Egyéni mintavétel használata

Ez a beállítás egy [Egyéni](application-gateway-probe-overview.md#custom-health-probe) mintavételt TÁRSÍT egy http-beállítással. Egy HTTP-beállítással csak egy egyéni mintavételt rendelhet hozzá. Ha nem kifejezetten egyéni mintavételt rendel hozzá, az [alapértelmezett](application-gateway-probe-overview.md#default-health-probe-settings) mintavétel a háttér állapotának figyelésére szolgál. Javasoljuk, hogy hozzon létre egy egyéni mintavételt a háttér állapotának monitorozásához.

> [!NOTE]
> Az egyéni mintavétel nem figyeli a háttér-készlet állapotát, kivéve, ha a megfelelő HTTP-beállítás explicit módon hozzá van rendelve egy figyelőhöz.

### <a id="pick"/></a>az állomásnév kiválasztása a háttérbeli címről

Ez a funkció dinamikusan beállítja a kérésben szereplő *állomásfejléc* -fejlécet a háttérbeli készlet állomásneve számára. IP-címet vagy teljes tartománynevet használ.

Ez a funkció segít, ha a háttér tartományneve eltér az Application Gateway DNS-nevétől, a háttér pedig egy adott állomásfejléc-re támaszkodik a megfelelő végpontra való feloldáshoz.

Ilyen eset például a több-bérlős szolgáltatás, mint a háttérrendszer. Az App Service egy több-bérlős szolgáltatás, amely egyetlen IP-címmel rendelkező megosztott területet használ. Így az App Service csak az egyéni tartomány beállításaiban konfigurált gazdagépeken keresztül érhető el.

Alapértelmezés szerint az Egyéni tartománynév a *example.azurewebsites.net*. Ha az App Service-t olyan állomásnév használatával szeretné elérni az alkalmazás-átjárón keresztül, amely nincs explicit módon regisztrálva az App Service-ben vagy az Application Gateway FQDN-n keresztül, akkor a rendszer felülbírálja az eredeti kérelemben szereplő állomásnév nevét az App Service állomásneve számára. Ehhez engedélyezze az **állomásnév kiválasztása a háttérbeli címekről** beállítást.

Olyan egyéni tartomány esetében, amelynek meglévő egyéni DNS-neve az App Service-re van leképezve, nem kell engedélyeznie ezt a beállítást.

> [!NOTE]
> Ez a beállítás nem szükséges a App Service Environmenthoz, amely egy dedikált központi telepítés.

### <a name="host-name-override"></a>Állomásnév felülbírálása

Ez a funkció a megadott állomásnévvel helyettesíti az Application Gateway bejövő kérelmében szereplő *állomásfejléc* -fejlécet.

Ha például a *www.contoso.com* meg van adva az **állomásnév** beállításban, akkor az eredeti kérelem * https://appgw.eastus.cloudapp.azure.com/path1 módosul a * https://www.contoso.com/path1ra, amikor a kérést a rendszer továbbítja a háttér-kiszolgálónak.

## <a name="back-end-pool"></a>Háttérkészlet

Egy háttér-készletet a háttérbeli tagok négy típusára irányíthat: egy adott virtuális gépet, egy virtuálisgép-méretezési készletet, egy IP-címet/teljes tartománynevet vagy egy app Service-t. 

Miután létrehozta a háttér-készletet, hozzá kell rendelnie egy vagy több kérelem-útválasztási szabályhoz. Az alkalmazás-átjárón az összes háttér-készlethez is be kell állítania az állapot-mintavételt. Ha egy kérés-útválasztási szabály feltétele teljesül, az Application Gateway továbbítja a forgalmat a megfelelő háttér-készletben lévő kifogástalan állapotú kiszolgálókra.

## <a name="health-probes"></a>Állapotminták

Az Application Gateway alapértelmezés szerint figyeli az összes erőforrás állapotát a háttérben. Javasoljuk azonban, hogy az egyes háttérbeli HTTP-beállításokhoz hozzon létre egy egyéni mintavételt, hogy jobban kézben legyen az állapot figyelése. Az egyéni mintavétel konfigurálásával kapcsolatos további információkért lásd: [Egyéni állapot mintavételi beállításai](application-gateway-probe-overview.md#custom-health-probe-settings).

> [!NOTE]
> Az egyéni állapotú mintavétel létrehozása után hozzá kell rendelnie azt egy háttérbeli HTTP-beállításhoz. Az egyéni mintavétel nem figyeli a háttér-készlet állapotát, kivéve, ha a megfelelő HTTP-beállítás explicit módon van társítva egy figyelővel egy szabály használatával.

## <a name="next-steps"></a>Következő lépések

Most, hogy már ismeri a Application Gateway összetevőket, a következőket teheti:

- [Application Gateway létrehozása a Azure Portalban](quick-create-portal.md)
- [Application Gateway létrehozása a PowerShell használatával](quick-create-powershell.md)
- [Application Gateway létrehozása az Azure CLI használatával](quick-create-cli.md)
