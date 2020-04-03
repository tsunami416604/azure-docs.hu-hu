---
title: Az Azure Application Gateway konfigurációjának áttekintése
description: Ez a cikk bemutatja, hogyan konfigurálható az Azure Application Gateway összetevői
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: absha
ms.openlocfilehash: f08cfab8f8de9183e6bee241959f7feabc31c8e3
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585911"
---
# <a name="application-gateway-configuration-overview"></a>Alkalmazásátjáró konfigurációjának áttekintése

Az Azure Application Gateway több összetevőből áll, amelyek különböző módon konfigurálhatók a különböző forgatókönyvekhez. Ez a cikk bemutatja, hogyan kell konfigurálni az egyes összetevőket.

![Alkalmazásátjáró-összetevők folyamatábrája](./media/configuration-overview/configuration-overview1.png)

Ez a kép egy olyan alkalmazást ábrázol, amely három figyelővel rendelkezik. Az első kettő több-site `http://acme.com/*` `http://fabrikam.com/*`hallgatók, illetve, illetve. Mindketten figyeljenek a 80-as porton. A harmadik egy alapvető figyelő, amely a végpontok között Transport Layer Security (TLS) megszüntetése, korábbi nevén Secure Sockets Layer (SSL) végződés.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Azure virtuális hálózat és dedikált alhálózat

Az alkalmazásátjáró egy dedikált központi telepítés a virtuális hálózatban. A virtuális hálózaton belül egy dedikált alhálózat szükséges az alkalmazásátjáróhoz. Egy adott alkalmazásátjáró központi telepítésének több példánya is lehet egy alhálózatban. Más alkalmazásátjárókat is telepíthet az alhálózatban. De nem telepíthet más erőforrást az alkalmazásátjáró alhálózatában.

> [!NOTE]
> Nem keverheti Standard_v2 és a Standard Azure Application Gateway ugyanazon az alhálózaton.

#### <a name="size-of-the-subnet"></a>Az alhálózat mérete

Az Application Gateway példányonként egy privát IP-címet és egy másik privát IP-címet használ, ha egy privát előtér-IP-cím konfigurálva van.

Az Azure is fenntartja öt IP-címek minden alhálózat belső használatra: az első négy és az utolsó IP-címek. Vegyünk például 15 alkalmazásátjáró-példányt, amelyek nem rendelkeznek privát előtér-IP-címnélkül. Ehhez az alhálózathoz legalább 20 IP-címre van szükség: ötre a belső használatra és 15-re az alkalmazásátjáró-példányokhoz. Tehát szüksége van egy /27 alhálózat mérete vagy nagyobb.

Vegyünk egy alhálózat, amely 27 alkalmazás átjáró példányok és egy IP-cím egy privát előtér-IP-cím. Ebben az esetben 33 IP-címre van szükség: 27 az alkalmazásátjáró-példányok, egy a privát előtér, és öt belső használatra. Tehát szüksége van egy /26 alhálózat mérete vagy nagyobb.

Azt javasoljuk, hogy legalább /28 alhálózati méretet használjon. Ez a méret 11 használható IP-címet biztosít. Ha az alkalmazás terhelése 10-nél több Application Gateway-példányt igényel, fontolja meg a /27 vagy /26 alhálózati méretet.

#### <a name="network-security-groups-on-the-application-gateway-subnet"></a>Hálózati biztonsági csoportok az Application Gateway alhálózaton

A hálózati biztonsági csoportok (NSG-k) az Application Gateway-en támogatottak. De vannak bizonyos korlátozások:

- Engedélyeznie kell a bejövő internetes forgalmat a 65503-65534-es TCP-portokon az Application Gateway v1 Termékváltozat, és a 65200-65535 TCP-portok a v2 termékváltozatesetében, amelynek célalhálózata **bármilyen,** a forrás pedig **GatewayManager** szolgáltatáscímkeként. Ez a porttartomány az Azure-infrastruktúra kommunikációjához szükséges. Ezeket a portokat az Azure-tanúsítványok védik (zárolják). Külső entitások, beleértve az átjárók ügyfeleit is, nem tudnak kommunikálni ezeken a végpontokon.

- A kimenő internetkapcsolat nem tiltható le. Az NSG alapértelmezett kimenő szabályai lehetővé teszik az internetkapcsolatot. A következő megoldást javasoljuk:

  - Ne távolítsa el az alapértelmezett kimenő szabályokat.
  - Ne hozzon létre más kimenő szabályokat, amelyek megtagadják a kimenő kapcsolatot.

- Az **AzureLoadBalancer** címkéből származó forgalmat engedélyezni kell.

#### <a name="allow-application-gateway-access-to-a-few-source-ips"></a>Az Alkalmazásátjáró néhány forrás IP-jéhez való hozzáférés engedélyezése

Ebben a forgatókönyvben az Alkalmazásátjáró alhálózatNS-akta. A következő korlátozásokat kell az alhálózaton korlátozni a következő prioritási sorrendben:

1. Bejövő forgalom engedélyezése egy forrás IP-címről vagy IP-tartományból, amelynek célja az Application Gateway teljes alhálózati címtartománya és célportja bejövő hozzáférési portként, például a HTTP-hozzáférés 80-as portjaként.
2. A forrásból érkező bejövő kérelmek **engedélyezése GatewayManager** szolgáltatáscímkeként és célként **bármely** és célportként: 65503-65534 az Application Gateway v1 Termékváltozathoz, és a 65200-65535 port a v2 Termékváltozathoz a [háttérszintű állapotállapot-kommunikációhoz.](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics) Ez a porttartomány az Azure-infrastruktúra kommunikációjához szükséges. Ezeket a portokat az Azure-tanúsítványok védik (zárolják). Megfelelő tanúsítványok nélkül a külső entitások nem kezdeményezhetnek módosításokat ezeken a végpontokon.
3. Bejövő Azure Load Balancer mintavételek *(AzureLoadBalancer* címke) és a bejövő virtuális hálózati forgalom *(VirtualNetwork* címke) engedélyezése a [hálózati biztonsági csoportban.](https://docs.microsoft.com/azure/virtual-network/security-overview)
4. Az összes többi bejövő forgalom blokkolása egy mindenre letiltási szabály használatával.
5. Az internetfelé irányuló kimenő forgalom engedélyezése az összes célállomáson.

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>A felhasználó által megadott útvonalak támogatottak az Application Gateway alhálózatán

> [!IMPORTANT]
> Az UdRs használata az Application Gateway alhálózaton azt eredményezheti, hogy a [háttérrendszer állapotának](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health) állapota **ismeretlenként**jelenik meg. Emellett az Application Gateway-naplók és metrikák létrehozását is okozhatja. Azt javasoljuk, hogy ne használja az UdRs az Application Gateway alhálózaton, így megtekintheti a háttér-rendszer állapotát, naplók és metrikák.

- **v1**

   A v1 Termékváltozat esetében a felhasználó által definiált útvonalak (UDRs) támogatottak az Application Gateway alhálózaton, feltéve, hogy azok nem módosítják a végpontok közötti kérés/válasz kommunikációt. Beállíthat például egy UDR-t az Application Gateway alhálózatban, hogy egy tűzfalberendezésre mutasson csomagvizsgálatra. De meg kell győződnie arról, hogy a csomag elérheti a tervezett rendeltetési ellenőrzés után. Ennek elmulasztása helytelen állapotmintavételt vagy forgalom-útválasztási viselkedést eredményezhet. Ez magában foglalja a tanult útvonalakat vagy az alapértelmezett 0.0.0.0/0 útvonalakat, amelyeket az Azure ExpressRoute vagy vpn-átjárók a virtuális hálózatban propagálnak.

- **v2**

   A v2 termékváltozat esetén támogatott és nem támogatott forgatókönyvek vannak:

   **v2 támogatott forgatókönyvek**
   > [!WARNING]
   > Az útvonaltábla helytelen konfigurációja aszimmetrikus útválasztást eredményezhet az Application Gateway v2 alkalmazásban. Győződjön meg arról, hogy az összes felügyeleti/vezérlősík-forgalom közvetlenül az internetre kerül, és nem egy virtuális berendezésen keresztül. A naplózás és a mérőszámok is érintettek lehetnek.


  **1. forgatókönyv:** Az UDR a Border Gateway Protocol (BGP) útvonalpropagálásának letiltásához az Application Gateway alhálózatba

   Néha az alapértelmezett átjáróútvonal (0.0.0/0) az Application Gateway virtuális hálózathoz társított ExpressRoute- vagy VPN-átjárókon keresztül történik. Ez megszakítja a felügyeleti sík forgalmát, amely közvetlen elérési utat igényel az internethez. Ilyen esetekben az UDR segítségével letilthatja a BGP-útvonal propagálását. 

   A BGP-útvonal propagálásának letiltásához kövesse az alábbi lépéseket:

   1. Hozzon létre egy routetábla-erőforrást az Azure-ban.
   2. Tiltsa le a **Virtuális hálózati átjáró útvonalpropagálási paraméterét.** 
   3. Társítsa az útvonaltáblát a megfelelő alhálózathoz. 

   Az UDR engedélyezése ebben a forgatókönyvben nem szakítja meg a meglévő beállításokat.

  **2. forgatókönyv:** Az UDR a 0.0.0.0/0-s rendszerközvetlen irányítása az internetre

   Létrehozhat egy UDR-t, hogy a 0.0.0.0/0-s forgalmat közvetlenül az internetre küldje. 

  **3. forgatókönyv:** UDR az Azure Kubernetes szolgáltatás kubenet

  Ha kubenet az Azure Kubernetes-szolgáltatás (AKS) és az Application Gateway ingres controller (AGIC) használatával, be kell állítania egy útvonaltáblát, amely lehetővé teszi a podok számára küldött forgalom a megfelelő csomópontra való átirányításhoz. Erre nem lesz szükség, ha az Azure CNI használatával. 

   Ha az útvonaltáblát a kubenet működéséhez szeretné beállítani, kövesse az alábbi lépéseket:

  1. Hozzon létre egy routetábla-erőforrást az Azure-ban. 
  2. Létrehozása után lépjen az **Útvonalak** lapra. 
  3. Új útvonal hozzáadása:
     - A címelőtagnak az AKS-ben elérni kívánt podok IP-tartományának kell lennie. 
     - A következő ugrástípusnak **virtuális berendezésnek**kell lennie. 
     - A következő ugrási cím a podokat tároló csomópont IP-címe a címelőtag mezőben meghatározott IP-tartományon belül kell lennie. 
    
  **v2 nem támogatott forgatókönyvek**

  **1. forgatókönyv:** UDR virtuális készülékekhez

  Minden olyan forgatókönyv, ahol a 0.0.0.0/0-t át kell irányítani bármely virtuális berendezésen, egy hub/küllővirtuális hálózaton vagy a helyszíni (kényszerített bújtatás) keresztül, nem támogatott a v2 nyilvános előzetes verzióban. 

## <a name="front-end-ip"></a>Előtér-IP

Beállíthatja, hogy az alkalmazásátjáró nyilvános IP-címmel, privát IP-címmel vagy mindkettővel rendelkezik. Nyilvános IP-cím szükséges, ha a fogadó egy háttér-, hogy az ügyfelek kell elérni az interneten keresztül egy internetre néző virtuális IP (VIP). 

Nyilvános IP-cím nem szükséges egy belső végpont, amely nincs kitéve az interneten. Ez az úgynevezett belső terheléselosztó (ILB) végpont vagy privát előtér IP.That's known as a *internal load-balancer* (ILB) endpoint or private frontend IP. Az alkalmazásátjáró ILB-je olyan belső üzletági alkalmazások esetében hasznos, amelyek nincsenek kitéve az internetnek. Ez is hasznos a szolgáltatások és a szintek egy többrétegű alkalmazás egy biztonsági határon belül, amelyek nincsenek kitéve az interneten, de amelyek megkövetelik a ciklikus multiplexelés terheléselosztás, munkamenet stickiness, vagy A TLS megszüntetése.

Csak 1 nyilvános IP-cím vagy egy privát IP-cím támogatott. Az alkalmazásátjáró létrehozásakor az előtér-IP-címet választja.

- Nyilvános IP-cím hez létrehozhat egy új nyilvános IP-címet, vagy használhatja a meglévő nyilvános IP-cím ugyanazon a helyen, mint az alkalmazás átjáró. További információ: [statikus és dinamikus nyilvános IP-cím.](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-versus-dynamic-public-ip-address)

- Privát IP-cím esetén megadhat egy privát IP-címet abból az alhálózatból, amelyen az alkalmazásátjáró létrejön. Ha nem ad meg egyet, a rendszer automatikusan tetszőleges IP-címet választ ki az alhálózatból. A kiválasztott (statikus vagy dinamikus) IP-címtípus később nem módosítható. További információ: [Alkalmazásátjáró létrehozása belső terheléselosztóval című témakörben.](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm)

Az előtér-IP-cím egy *figyelőhöz*van társítva, amely ellenőrzi a bejövő kérelmeket az előtér-IP-cím.

## <a name="listeners"></a>Figyelők

A figyelő egy logikai entitás, amely a port, protokoll, állomás és IP-cím használatával ellenőrzi a bejövő kapcsolatkérelmeket. A figyelő konfigurálásakor meg kell adnia azokat az értékeket, amelyek megfelelnek az átjáró bejövő kérésének megfelelő értékeinek.

Amikor létrehoz egy alkalmazásátjárót az Azure Portal használatával, egy alapértelmezett figyelőt is létrehoz a figyelő protokoll és port kiválasztásával. Kiválaszthatja, hogy engedélyezi-e a HTTP2-támogatást a figyelőn. Az alkalmazásátjáró létrehozása után módosíthatja az alapértelmezett figyelő (*appGatewayHttpListener)* beállításait, vagy új figyelők hozhat létre.

### <a name="listener-type"></a>Figyelő típusa

Amikor új figyelőt hoz létre, az [ *alap-* és *a többhelyes*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners)terület közül választhat.

- Ha azt szeretné, hogy az összes kérelmet (bármely tartományhoz) elfogadja és továbbítsa a háttérkészletekbe, válassza az alapszintű lehetőséget. [Megtudhatja, hogyan hozhat létre alkalmazásátjárót alapszintű figyelővel.](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)

- Ha a *gazdagépfejléc* vagy állomásnév alapján különböző háttérkészletekre szeretné továbbítani a kérelmeket, válassza a többhelyes figyelőt, ahol meg kell adnia egy állomásnevet is, amely megfelel a bejövő kérésnek. Ennek az az oka, hogy az Application Gateway a HTTP 1.1 állomásfejlécekre támaszkodik, hogy több webhelyet is üzemeltetjen ugyanazon a nyilvános IP-címen és porton.

#### <a name="order-of-processing-listeners"></a>A hallgatók feldolgozásának sorrendje

A v1 Termékváltozat esetén a kérelmek a szabályok sorrendjének és a figyelő típusa szerint egyeztetve. Ha egy szabály az alapvető figyelő jön az első a sorrendben, először feldolgozva, és elfogadja a kérelmet, hogy a port és az IP-kombináció. Ennek elkerülése érdekében konfigurálja a szabályokat a többhelyes figyelők először, és nyomja le a szabályt az alapszintű figyelő az utolsó a listában.

A v2 Termékváltozat, többhelyes figyelők feldolgozása az alapvető figyelők előtt.

### <a name="front-end-ip"></a>Előtér-IP

Válassza ki az előtér-IP-címet, amelyet társítani kíván ehhez a figyelőhöz. A figyelő megfigyeli a bejövő kérelmeket ezen az IP-címén.

### <a name="front-end-port"></a>Előtér-port

Válassza ki az előtér-portot. Jelöljön ki egy meglévő portot, vagy hozzon létre egy újat. Válasszon bármilyen értéket a [portok megengedett tartományából.](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports) Nem csak a jól ismert portokat használhatja, például a 80-as és a 443-as portot, hanem minden engedélyezett egyéni portot, amely megfelelő. A port nyilvános vagy privát néző figyelők számára használható.

### <a name="protocol"></a>Protocol (Protokoll)

Válassza a HTTP vagy https lehetőséget:

- Ha a HTTP lehetőséget választja, az ügyfél és az alkalmazásátjáró közötti forgalom titkosítatlan lesz.

- Válassza a HTTPS lehetőséget, ha [ATLS-végződtetést](features.md#secure-sockets-layer-ssltls-termination) vagy [végpontok közötti TLS-titkosítást](https://docs.microsoft.com/azure/application-gateway/ssl-overview)szeretne. Az ügyfél és az alkalmazásátjáró közötti forgalom titkosítva van. És a TLS-kapcsolat megszakad az alkalmazás átjáró. Ha végpontok közötti TLS-titkosítást szeretne, válassza a HTTPS-t, és konfigurálja a **háttérHTTP-beállítást.** Ez biztosítja, hogy a forgalom újra titkosítva, amikor az alkalmazás átjáróból a háttérrendszerbe utazik.


A TLS-végződtetés és a végpontok közötti TLS-titkosítás konfigurálásához hozzá kell adnia egy tanúsítványt a figyelőhöz, hogy az alkalmazásátjáró szimmetrikus kulcsot hozhasson le. Ezt a TLS protokoll specifikációja határozza meg. A szimmetrikus kulcs az átjárónak küldött forgalom titkosítására és visszafejtésére szolgál. Az átjárótanúsítványnak Személyes adatcsere (PFX) formátumúnak kell lennie. Ez a formátum lehetővé teszi az átjáró által a forgalom titkosításához és visszafejtéséhez használt személyes kulcs exportálását.

#### <a name="supported-certificates"></a>Támogatott tanúsítványok

Lásd: [A TLS-megszüntetéshez támogatott tanúsítványok.](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination)

### <a name="additional-protocol-support"></a>További protokolltámogatás

#### <a name="http2-support"></a>HTTP2 támogatás

A HTTP/2 protokoll támogatása csak az alkalmazásátjáró-figyelőkhöz csatlakozó ügyfelek számára érhető el. A háttérkiszolgáló-készletekkel való kommunikáció HTTP/1.1 felett van. Alapértelmezés szerint a HTTP/2 támogatás le van tiltva. A következő Azure PowerShell-kódrészlet bemutatja, hogyan engedélyezheti ezt:

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>WebSocket támogatás

A WebSocket-támogatás alapértelmezés szerint engedélyezve van. Nincs felhasználó által konfigurálható beállítás az engedélyezéshez vagy a letiltáshoz. A WebSockets http- és HTTPS-figyelőkkel is használható.

### <a name="custom-error-pages"></a>Egyéni hibalapok

Megadhatja az egyéni hiba a globális szinten, vagy a figyelő szintjén. De globális szintű egyéni hibalapok létrehozása az Azure Portalon jelenleg nem támogatott. Egyéni hibalapot állíthat be egy 403-as webalkalmazás tűzfalának hibájához vagy egy 502-es karbantartási laphoz a figyelő szintjén. Meg kell adnia egy nyilvánosan elérhető blob URL-címet is az adott hibaállapot-kódhoz. További információt az [egyéni Application Gateway-hibaoldalak létrehozását ismertető részben](https://docs.microsoft.com/azure/application-gateway/custom-error) talál.

![Alkalmazásátjáró hibakódjai](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

Globális egyéni hibalap konfigurálásához olvassa el az [Azure PowerShell-konfiguráció című témakört.](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration)

### <a name="tls-policy"></a>TLS-házirend

Központosíthatja a TLS/SSL tanúsítványkezelést, és csökkentheti a titkosítás-visszafejtési terhelést egy háttérkiszolgáló-farm esetében. A központi TLS-kezelés lehetővé teszi a biztonsági követelményeknek megfelelő központi TLS-házirend megadását is. Választhat *alapértelmezett*, *előre definiált*vagy *egyéni* TLS-házirendet.

A TLS-házirendet úgy konfigurálhatja, hogy vezérelje a TLS protokollverziókat. Az alkalmazásátjáró konfigurálható úgy, hogy a TLS1.0, TLS1.1 és TLS1.2-es TLS-kézfogásokhoz egy minimális protokollverziót használjon. Alapértelmezés szerint az SSL 2.0 és a 3.0 le van tiltva, és nem konfigurálható. További információt az [Application Gateway TLS-házirend – áttekintés című témakörben talál.](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview)

Miután létrehozott egy figyelőt, társítja azt egy kérelem-útválasztási szabállyal. Ez a szabály határozza meg, hogy a figyelőáltal fogadott kérelmek hogyan lesznek irányítva a háttér-végpontra.

## <a name="request-routing-rules"></a>Útválasztási szabályok kérése

Amikor létrehoz egy alkalmazásátjárót az Azure Portal használatával, létrehoz egy alapértelmezett szabályt (*1. szabály).* Ez a szabály az alapértelmezett figyelőt (*appGatewayHttpListener*) az alapértelmezett háttérkészlettel *(appGatewayBackendPool)* és az alapértelmezett háttérHTTP-beállításokkal *(appGatewayBackendHttpSettings)* köti. Az átjáró létrehozása után módosíthatja az alapértelmezett szabály beállításait, vagy új szabályokat hozhat létre.

### <a name="rule-type"></a>Szabály típusa

Szabály létrehozásakor választhat az [ *alap-* és *az elérési út alapján.*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rules)

- Válassza az alapszintű, ha azt szeretné, hogy továbbítsa az összes kérelmet a társított figyelő (például *blog<i></i>.contoso.com/)\** egyetlen háttér-készlet.
- Válassza ki az elérési utat, ha adott URL-elérési utakról szeretne bizonyos háttérkészletekre irányítani a kérelmeket. Az elérési út minta csak az URL elérési útjána vonatkozik, a lekérdezési paraméterekre nem.

#### <a name="order-of-processing-rules"></a>A feldolgozási szabályok sorrendje

A v1 Termékváltozat esetében a bejövő kérelmek mintaegyeztetése abban a sorrendben történik, hogy az elérési utak szerepeljenek az elérési út alapú szabály URL-elérési útleképezésében. Ha egy kérelem megegyezik az útvonaltérkép két vagy több útvonalának mintájával, a listaelsőként felsorolt elérési út egyeztetésre kerül. És a kérelem továbbításra kerül a háttérbe, amely az adott elérési úthoz kapcsolódik.

A v2 Termékváltozat esetében a pontos egyezés magasabb prioritású, mint az URL-elérési út térképének elérési útja. Ha egy kérelem két vagy több elérési út mintájának felel meg, a kérelem továbbítása a háttérbe, amely a kérelemnek pontosan megfelelő elérési úthoz van társítva. Ha a bejövő kérelemben szereplő elérési út nem pontosan egyezik a térkép egyik elérési útvonalával sem, a kérelem mintaegyeztetése fellesz dolgozva az elérési út alapú szabály útvonaltérkép-sorrendjének listájában.

### <a name="associated-listener"></a>Társított hallgató

Társítson egy figyelőt a szabályhoz, hogy a figyelőhöz társított *kérelem-útválasztási szabály* kiértékelésre kerül a kérelem továbbításához szükséges háttérkészlet meghatározásához.

### <a name="associated-back-end-pool"></a>Társított háttérkészlet

Társítsa a szabályhoz a háttér-készlet, amely tartalmazza a háttér-célokat, amelyek a figyelő által kapott kéréseket szolgálnak.

 - Egy alapvető szabály, csak egy háttér-készlet engedélyezett. A társított figyelő összes kérelmet továbbítja a háttérkészlet.

 - Görbealapú szabály esetén adjon hozzá több háttérkészletet, amelyek megfelelnek az egyes URL-címek elérési útjának. A megadott URL-elérési útnak megfelelő kérelmeket a megfelelő háttérkészletbe továbbítja a kérelem. Adjon hozzá egy alapértelmezett háttérkészletet is. Az olyan kérelmeket, amelyek nem egyeznek meg a szabály egyetlen URL-elérési útjának sem, a készlet továbbítja a következőhöz.

### <a name="associated-back-end-http-setting"></a>Társított háttér-HTTP-beállítás

Minden szabályhoz adjon hozzá egy háttér-HTTP-beállítást. A kérelmek et az alkalmazásátjáróból a háttércélokhoz irányítja a portszám, a protokoll és az ebben a beállításban megadott egyéb információk használatával.

Egy alapszabály esetén csak egy háttérHTTP-beállítás engedélyezett. A társított figyelő összes kérelmet továbbítja a megfelelő háttér-célok ezzel a HTTP-beállítással.

Elérési út alapú szabály esetén adjon hozzá több háttér-HTTP-beállítást, amelyek megfelelnek az egyes URL-címek elérési útjának. Az ebben a beállításban szereplő URL-elérési útnak megfelelő kérelmeket a rendszer az egyes URL-útvonalaknak megfelelő HTTP-beállítások kal továbbítja a megfelelő háttércélokhoz. Adjon hozzá egy alapértelmezett HTTP-beállítást is. Azok a kérelmek, amelyek nem felelnek meg a szabály egyetlen URL-elérési útjának sem, az alapértelmezett HTTP-beállítással továbbítják az alapértelmezett háttérkészletbe.

### <a name="redirection-setting"></a>Átirányítási beállítás

Ha az átirányítás alapszabályhoz van konfigurálva, a társított figyelő összes kérelme átlesz irányítva a célhoz. Ez *a globális* átirányítás. Ha az átirányítás egy útvonalalapú szabályhoz van konfigurálva, a program csak egy adott helyterület kérelmeit irányítja át. Ilyen például a */cart/\** jelöléssel ellátott bevásárlókosár terület. Ez *az útvonal-alapú* átirányítás.

Az átirányításokról az [Application Gateway átirányítás – áttekintés című témakörben olvashat bővebben.](redirect-overview.md)

#### <a name="redirection-type"></a>Átirányítási típus

Válassza ki a szükséges átirányítás típusát: *Permanent(301)*, *Temporary(307),* *Found(302)* vagy *Lásd az egyéb(303) .*

#### <a name="redirection-target"></a>Átirányítási cél

Válasszon egy másik figyelőt vagy egy külső webhelyet átirányítási célként.

##### <a name="listener"></a>Figyelő

Válassza ki a figyelő, mint az átirányítási cél átirányítani a forgalmat az egyik figyelő a másikra az átjárón. Ez a beállítás akkor szükséges, ha engedélyezni szeretné a HTTP-HTTPS átirányítást. Átirányítja a forgalmat a forrásfigyelő, amely ellenőrzi a bejövő HTTP-kérelmeket a célfigyelő, amely ellenőrzi a bejövő HTTPS-kérelmeket. Azt is választhatja, hogy az eredeti kérelem ből származó lekérdezési karakterláncot és elérési utat tartalmazza az átirányítási célhoz továbbított kérelemben.

![Az Alkalmazásátjáró összetevőipárbeszédpanel](./media/configuration-overview/configure-redirection.png)

A HTTP-HTTPS átirányításról további információt a következő témakörben talál:
- [HTTP-HTTPS átirányítás az Azure Portal használatával](redirect-http-to-https-portal.md)
- [HTTP-HTTPS átirányítás a PowerShell használatával](redirect-http-to-https-powershell.md)
- [HTTP-HTTPS átirányítás az Azure CLI használatával](redirect-http-to-https-cli.md)

##### <a name="external-site"></a>Külső hely

Válassza ki a külső webhelyet, ha át szeretné irányítani a szabályhoz társított figyelő forgalmát egy külső webhelyre. Választhat, hogy az eredeti kérelem lekérdezési karakterláncát is belefoglalja az átirányítási célba továbbított kérelembe. Az eredeti kérelemben lévő külső helyre nem továbbíthatja az elérési utat.

Az átirányításról további információt a következő témakörben talál:
- [Forgalom átirányítása külső webhelyre a PowerShell használatával](redirect-external-site-powershell.md)
- [Forgalom átirányítása külső webhelyre a CLI használatával](redirect-external-site-cli.md)

#### <a name="rewrite-the-http-header-setting"></a>A HTTP-fejléc beállítás újraírása

Ez a beállítás http-kérelem- és válaszfejléceket ad hozzá, távolít el vagy frissít, miközben a kérés- és válaszcsomagok az ügyfél- és a háttérkészletek között mozognak. További információkért lásd:

 - [HTTP-fejlécek újraírása – áttekintés](rewrite-http-headers.md)
 - [HTTP-fejléc újraírásának konfigurálása](rewrite-http-headers-portal.md)

## <a name="http-settings"></a>HTTP-beállítások

Az alkalmazásátjáró az itt megadott konfigurációval irányítja a forgalmat a háttérkiszolgálókra. Http-beállítás létrehozása után hozzá kell rendelnie egy vagy több kérelem-útválasztási szabályhoz.

### <a name="cookie-based-affinity"></a>Cookie-alapú affinitás

Az Azure Application Gateway átjáró által kezelt cookie-kat használ a felhasználói munkamenetek karbantartásához. Amikor egy felhasználó elküldi az első kérelmet az Application Gateway-nek, beállítja az affinitás cookie-t a válaszban egy kivonatértékkel, amely tartalmazza a munkamenet részleteit, így az affinitáscookie-t hordozó további kérelmek ugyanahhoz a háttérkiszolgálóhoz lesznek irányítva a stickiness fenntartása érdekében. 

Ez a szolgáltatás akkor hasznos, ha egy felhasználói munkamenetet ugyanazon a kiszolgálón szeretne tartani, és ha a munkamenet-állapotot helyileg menti a kiszolgálón egy felhasználói munkamenethez. Ha az alkalmazás nem tudja kezelni a cookie-alapú affinitást, nem használhatja ezt a funkciót. A használatához győződjön meg arról, hogy az ügyfelek támogatják a cookie-kat.

A [Chromium böngésző](https://www.chromium.org/Home) [v80 frissítése](https://chromiumdash.appspot.com/schedule) megbízást adott arra, hogy a [SameSite](https://tools.ietf.org/id/draft-ietf-httpbis-rfc6265bis-03.html#rfc.section.5.3.7) attribútum nélküli HTTP-cookie-kat SameSite=Lax néven kell kezelni. A CORS (Cross-Origin Resource Sharing) kérelmek esetében, ha a cookie-t harmadik fél környezetében kell elküldeni, akkor a *SameSite=None-t kell használnia; Biztonságos* attribútumok, és csak HTTPS-en keresztül kell elküldeni. Ellenkező esetben csak HTTP-ben a böngésző nem küldi el a cookie-kat a harmadik fél környezetében. A Chrome-tól származó frissítés célja a biztonság növelése és a webhelyek közötti kérelemhamisítás (CSRF) támadások elkerülése. 

A módosítás támogatásához 2020. február 17-től az Application Gateway (az összes Termékváltozat-típus) a meglévő *ApplicationGatewayAffinityCORS* nevű cookie-t is befecskendezi. *ApplicationGatewayAffinityCORS* Az *ApplicationGatewayAffinityCORS* cookie-hoz további két attribútum tartozik hozzá (*"SameSite=None; Secure"*) úgy, hogy a ragadós munkamenet még a kereszt-eredetű kérelmek esetén is megmaradjon.

Vegye figyelembe, hogy az alapértelmezett affinitás cookie-név *ApplicationGatewayAffinity,* és módosíthatja azt. Abban az esetben, ha egyéni affinitáscookie-nevet használ, egy további cookie-t ad hozzá a CORS utótagként. *Például, CustomCookieNameCORS*.

> [!NOTE]
> Ha a *SameSite=None* attribútum be van állítva, kötelező, hogy a cookie tartalmazza a *Biztonságos* jelzőt is, és HTTPS-en keresztül kell elküldeni.  Ha munkamenet-affinitás szükséges a CORS-on keresztül, át kell telepítenie a számítási feladatokHTTPS-re. Kérjük, olvassa el a TLS kiszervezés és end-to-end TLS dokumentáció alkalmazásátjáró itt – [Áttekintés](ssl-overview.md), [Konfigurálása egy alkalmazásátjáró TLS-végződtetés az Azure Portalon,](create-ssl-portal.md) [Konfigurálja a végpontok közötti TLS segítségével Application Gateway a portállal](end-to-end-ssl-portal.md).

### <a name="connection-draining"></a>Kapcsolatkiürítés

A kapcsolat kiürítése segít a háttérkészlet tagjainak kecses eltávolításában a tervezett szolgáltatásfrissítések során. Ezt a beállítást a háttérkészlet minden tagjára alkalmazhatja a szabály létrehozása során. Ez biztosítja, hogy a háttérkészlet minden regisztrációjának megszüntetése továbbra is fenntartsa a meglévő kapcsolatokat, és kiszolgálja a konfigurálható időtúlkéréseket, és ne kapjon új kéréseket vagy kapcsolatokat. Ez alól az egyetlen kivétel az átjáró által felügyelt munkamenet-affinitás miatt a példányok regisztrációjának törléséhez kötött kérelmek, amelyek továbbra is továbbításra kerülnek a regisztráció megszüntetése kornem.The only exception this are requests bound for deregistering instances because of gateway-managed session affinity and will continue to be forwarded to the deregistering instances. A kapcsolat kiürítése a háttérpéldányokra vonatkozik, amelyek kifejezetten törlődnek a háttérkészletből.

### <a name="protocol"></a>Protocol (Protokoll)

Az Application Gateway támogatja a HTTP és https protokollt is a háttérkiszolgálókra irányuló útválasztási kérelmekhez. Ha a HTTP lehetőséget választja, a háttérkiszolgálókra irányuló forgalom titkosítatlan. Ha a titkosítatlan kommunikáció nem elfogadható, válassza a HTTPS lehetőséget.

Ez a beállítás https-lel kombinálva támogatja a [végpontok között a TLS-t.](ssl-overview.md) Ez lehetővé teszi a háttérrendszerbe titkosított bizalmas adatok biztonságos továbbítását. Minden olyan háttérkiszolgálót, amelyen a háttérkészletben engedélyezve van a végpontok közötti TLS, konfigurálni kell egy tanúsítvánnyal a biztonságos kommunikáció engedélyezéséhez.

### <a name="port"></a>Port

Ez a beállítás azt a portot adja meg, ahol a háttérkiszolgálók figyelik az alkalmazásátjáróból érkező forgalmat. Az 1 és 65535 közötti portokat beállíthatja.

### <a name="request-timeout"></a>Időkérés időelte

Ez a beállítás az a másodpercek száma, ameddig az alkalmazásátjáró a háttérkiszolgálótól érkező válasz fogadására vár.

### <a name="override-back-end-path"></a>Háttérelérési út felülbírálása

Ezzel a beállítással konfigurálhat egy választható egyéni továbbítási útvonalat, amelyet a kérelem háttérrendszerre történő továbbításakor használhat. A bejövő elérési út bármely olyan része, amely megfelel a **felülíró háttérgörbe** mező egyéni elérési útjának, a program a továbbított elérési útra másolja. Az alábbi táblázat bemutatja, hogyan működik ez a szolgáltatás:

- Ha a HTTP-beállítás egy alapvető kérelem-útválasztási szabályhoz van csatolva:

  | Eredeti kérelem  | Háttérelérési út felülbírálása | A kérelem átküldött a háttérbe |
  | ----------------- | --------------------- | ---------------------------- |
  | /home/            | /felülbírálás/            | /felülbírálás/otthon/              |
  | /home/secondhome/ | /felülbírálás/            | /felülbírálás/otthon/secondhome/   |

- Ha a HTTP-beállítás egy elérési útválasztás-szabályhoz van csatolva:

  | Eredeti kérelem           | Elérési út szabálya       | Háttérelérési út felülbírálása | A kérelem átküldött a háttérbe |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | /pathrule*      | /felülbírálás/            | /felülbírálás/otthon/              |
  | /pathrule/home/secondhome/ | /pathrule*      | /felülbírálás/            | /felülbírálás/otthon/secondhome/   |
  | /home/                     | /pathrule*      | /felülbírálás/            | /felülbírálás/otthon/              |
  | /home/secondhome/          | /pathrule*      | /felülbírálás/            | /felülbírálás/otthon/secondhome/   |
  | /pathrule/home/            | /pathrule/home* | /felülbírálás/            | /felülbírálás/                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | /felülbírálás/            | /felülbírálás/második otthon/        |
  | /pathrule/                 | /pathrule/      | /felülbírálás/            | /felülbírálás/                   |

### <a name="use-for-app-service"></a>Alkalmazásszolgáltatás használata

Ez egy csak egy felhasználói felület parancsikont, amely kiválasztja a két szükséges beállításokat az Azure App Service háttér. Lehetővé teszi **az állomásnév kivételezését a háttércímből,** és új egyéni mintavételt hoz létre, ha még nem rendelkezik ilyensel. (További információt a cikk [háttércím-beállítási](#pick) szakaszában talál. Létrejön egy új mintavétel, és a mintavételfejléc a háttértag címéről lesz kiválasztva.

### <a name="use-custom-probe"></a>Egyéni mintavétel használata

Ez a beállítás [egy egyéni mintavételt](application-gateway-probe-overview.md#custom-health-probe) társít egy HTTP-beállításhoz. Csak egy egyéni mintavételt társíthat egy HTTP-beállításhoz. Ha nem társít kifejezetten egyéni mintavételt, az [alapértelmezett mintavétel](application-gateway-probe-overview.md#default-health-probe-settings) a háttérrendszer állapotának figyelésére szolgál. Azt javasoljuk, hogy hozzon létre egy egyéni mintavétel t a háttér-rendszerek állapotfigyelésének jobb szabályozása érdekében.

> [!NOTE]
> Az egyéni mintavétel nem figyeli a háttérkészlet állapotát, kivéve, ha a megfelelő HTTP-beállítás explicit módon társítva van egy figyelőhöz.

### <a name="pick-host-name-from-back-end-address"></a><a id="pick"/></a>Állomásnév kivétele a háttércímből

Ez a képesség dinamikusan állítja be a kérelem ben lévő *állomásfejlécet* a háttérkészlet állomásnevére. Ip-címet vagy teljes tartománynát használ.

Ez a szolgáltatás akkor nyújt segítséget, ha a háttérrendszer tartományneve eltér az alkalmazásátjáró DNS-nevétől, és a háttérrendszer egy adott állomásfejlécre támaszkodik a megfelelő végpontra való megoldáshoz.

Példaként a több-bérlős szolgáltatások, mint a háttér. Az alkalmazásszolgáltatás egy több-bérlős szolgáltatás, amely egyetlen IP-címmel rendelkező megosztott területet használ. Így egy alkalmazásszolgáltatás csak az egyéni tartománybeállításokban konfigurált állomásneveken keresztül érhető el.

Alapértelmezés szerint az egyéni tartománynév *example.azurewebsites.net.* Az alkalmazásszolgáltatás eléréséhez egy alkalmazásátjáró használatával egy állomásnév, amely nincs kifejezetten regisztrálva az alkalmazásszolgáltatásban, vagy az alkalmazásátjáró teljes tartománynév, felülbírálja a hostname az eredeti kérelemben az alkalmazásszolgáltatás hostname. Ehhez engedélyezze a **kivételezési állomás nevét a háttércím-cím beállításból.**

Olyan egyéni tartomány esetén, amelynek meglévő egyéni DNS-neve le van képezve az alkalmazásszolgáltatásra, nem kell engedélyeznie ezt a beállítást.

> [!NOTE]
> Ez a beállítás nem szükséges az App Service-környezetben, amely egy dedikált központi telepítés.

### <a name="host-name-override"></a>Állomásnév felülbírálása

Ez a funkció lecseréli az alkalmazásátjáró bejövő kérelmében lévő *állomásfejlécet* a megadott állomásnévre.

Ha például *www.contoso.com* az **állomásnév** beállításban van megadva, az eredeti *`https://appgw.eastus.cloudapp.azure.com/path1` kérelem *`https://www.contoso.com/path1` lesz, amikor a kérés t a háttérkiszolgálóra továbbítja.

## <a name="back-end-pool"></a>Háttérkészlet

A háttérkészletet négy féle háttértagra irányíthatja: egy adott virtuális gépre, egy virtuálisgép-méretezési csoportra, egy IP-címre/FQDN-re vagy egy alkalmazásszolgáltatásra. 

Miután létrehozott egy háttérkészletet, hozzá kell rendelnie egy vagy több kérelem-útválasztási szabályhoz. Az alkalmazásátjáró minden háttérkészletéhez konfigurálnia kell az állapotmintákat is. Ha egy kérelem-útválasztási szabály feltétel teljesül, az alkalmazás átjáró továbbítja a forgalmat a megfelelő háttérkészlet ben a kifogástalan állapotú kiszolgálók (az állapotmintak meghatározása szerint).

## <a name="health-probes"></a>Állapotminták

Az alkalmazásátjáró alapértelmezés szerint figyeli az összes erőforrás állapotát a háttérben. De azt javasoljuk, hogy hozzon létre egy egyéni mintavétel minden háttér-HTTP-beállítás, hogy nagyobb felügyeletet az állapotfigyelés. Az egyéni mintavétel konfigurálásáról az [Egyéni állapotminta beállításai című](application-gateway-probe-overview.md#custom-health-probe-settings)témakörben olvashat.

> [!NOTE]
> Miután létrehozott egy egyéni állapotminta, hozzá kell rendelnie egy háttér-HTTP-beállításhoz. Az egyéni mintavétel nem figyeli a háttér-készlet állapotát, kivéve, ha a megfelelő HTTP-beállítás explicit módon van társítva egy szabály használatával figyelő.

## <a name="next-steps"></a>További lépések

Most, hogy már tud az Application Gateway összetevőiről, a következőket teheti:

- [Alkalmazásátjáró létrehozása az Azure Portalon](quick-create-portal.md)
- [Alkalmazásátjáró létrehozása a PowerShell használatával](quick-create-powershell.md)
- [Alkalmazásátjáró létrehozása az Azure CLI használatával](quick-create-cli.md)
