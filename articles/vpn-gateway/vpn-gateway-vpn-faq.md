---
title: "Virtuális hálózati VPN Gateway – gyakori kérdések | Microsoft Docs"
description: "A VPN Gatewayjel kapcsolatos gyakori kérdések. Gyakori kérdések a Microsoft Azure Virtual Network létesítmények közötti kapcsolatairól, a hibrid konfigurációjú kapcsolatokról és a VPN-átjárókról"
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
ms.assetid: 6ce36765-250e-444b-bfc7-5f9ec7ce0742
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2016
ms.author: yushwang
translationtype: Human Translation
ms.sourcegitcommit: d653865993d75cf926151a14cc4f059e4eaba035
ms.openlocfilehash: f0e7c08a0783452665028ea3479c14b02a27258f


---
# <a name="vpn-gateway-faq"></a>VPN Gateway – gyakori kérdések
## <a name="connecting-to-virtual-networks"></a>Csatlakozás virtuális hálózatokhoz
### <a name="can-i-connect-virtual-networks-in-different-azure-regions"></a>Összekapcsolhatok eltérő Azure-régiókban található virtuális hálózatokat?
Igen. Nincs régiókorlátozás. A virtuális hálózatok összekapcsolhatók az azonos régióban vagy más Azure-régiókban található virtuális hálózatokkal is.

### <a name="can-i-connect-virtual-networks-in-different-subscriptions"></a>Összekapcsolhatok egymással különböző előfizetésekben található virtuális hálózatokat?
Igen.

### <a name="can-i-connect-to-multiple-sites-from-a-single-virtual-network"></a>Csatlakozhatok több helyhez egyetlen virtuális hálózatból?
A Windows PowerShell és az Azure REST API-k használatával kapcsolódhat több helyhez is. Lásd a gyakori kérdések [Többhelyes és virtuális hálózatok közötti kapcsolatok](#multi-site-and-vnet-to-vnet-connectivity) című szakaszát.

## <a name="what-are-my-cross-premises-connection-options"></a>Milyen lehetőségeim vannak a létesítmények közötti kapcsolódásra?
A következő létesítmények közötti kapcsolattípusok támogatottak:

* [Helyek közötti kapcsolat](vpn-gateway-site-to-site-create.md) – VPN-kapcsolat IPsec (IKE v1 és IKE v2) használatával. Ehhez a kapcsolattípushoz VPN-eszköz vagy RRAS szükséges.
* [Pont–hely kapcsolat](vpn-gateway-point-to-site-create.md) – VPN-kapcsolat STTP (Secure Socket Tunneling Protocol) használatával. Ehhez a kapcsolattípushoz nem szükséges VPN-eszköz.
* [Virtuális hálózatok közötti kapcsolat](virtual-networks-configure-vnet-to-vnet-connection.md) – A helyek közötti kapcsolattal megegyező kapcsolattípus. A virtuális hálózatok közötti kapcsolat egy IPsec-et (IKE v1 és IKE v2) használó VPN-kapcsolat, nem szükséges hozzá VPN-eszköz.
* [Többhelyes kapcsolat](vpn-gateway-multi-site.md) – Ez a helyek közötti kapcsolat egy változata, amely több helyszíni hely csatlakoztatását teszi lehetővé egy virtuális hálózathoz.
* [ExpressRoute](../expressroute/expressroute-introduction.md) – Az ExpressRoute közvetlen kapcsolatot biztosít az Azure-ral WAN-on (nem a nyilvános interneten) keresztül. További információk: [ExpressRoute Technical Overview](../expressroute/expressroute-introduction.md) (Az ExpressRoute műszaki áttekintése) és [ExpressRoute FAQ](../expressroute/expressroute-faqs.md) (ExpressRoute – gyakori kérdések).

További információk a kapcsolatokról: [Információk a VPN Gateway-ről](vpn-gateway-about-vpngateways.md).

### <a name="what-is-the-difference-between-a-site-to-site-connection-and-point-to-site"></a>Mi a különbség a helyek közötti és a pont–hely kapcsolatok között?
A **helyek közötti** kapcsolatok lehetővé teszik a helyszínen található számítógépek csatlakoztatását a virtuális hálózaton belüli virtuális gépekhez vagy szerepkörpéldányokhoz az útválasztás konfigurációjától függően. Ez ideális megoldás folyamatosan elérhető létesítmények közötti kapcsolatokhoz, és hibrid konfigurációkhoz is használható. Ez a kapcsolattípus IPsec VPN-készüléket használ (hardver- vagy szoftverkészüléket), amelyet a hálózat szélén kell üzembe helyezni. Az ilyen típusú kapcsolat létrehozásához szükség van a megfelelő VPN-hardverre és egy kifelé irányuló IPv4-címre.

A **pont–hely** kapcsolattal csatlakozhat egy tetszőleges helyen található számítógépről a virtuális hálózata összes eleméhez. Ez a típus a Windows beépített VPN-ügyfelét használja. A pont–hely konfiguráció részeként telepíteni kell egy tanúsítványt és egy VPN-ügyfélkonfigurációs csomagot, amelyben azok a beállítások találhatók, amelyeket a számítógépe használ a virtuális hálózatban található virtuális gépekhez vagy szerepkörpéldányokhoz való csatlakozáshoz. Ez ideális megoldás, ha csatlakozni szeretne egy virtuális hálózathoz, de nem a helyszínen tartózkodik, valamint akkor is jól használható, ha nincs hozzáférése VPN-hardverhez vagy kifelé irányuló IPv4-címhez, amelyek a helyek közötti kapcsolatok kialakításához szükségesek.

A virtuális hálózatát konfigurálhatja úgy, hogy helyek közötti és pont–hely kapcsolatokat használjon egyidejűleg – ha a helyek közötti kapcsolatot útvonalalapú VPN-típussal hozza létre az átjáróhoz. Az útvonalalapú VPN-típusok korábbi megnevezése dinamikus átjáró volt a klasszikus üzemi modellben.

### <a name="what-is-expressroute"></a>Mi az az ExpressRoute?
Az ExpressRoute használatával magánkapcsolatok hozhatók létre a Microsoft-adatközpontok és a helyszíni vagy közös elhelyezésű környezet között. Az ExpressRoute használatával egy ExpressRoute-partneri közös elhelyezési létesítményben létesíthet kapcsolatot Microsoft-felhőszolgáltatásokkal – például a Microsoft Azure-ral vagy az Office 365-tel – vagy közvetlenül csatlakozhat azokhoz meglévő WAN hálózatról (például egy hálózati szolgáltató által biztosított MPLS VPN-ről).

Az ExpressRoute-kapcsolatok a tipikus internetes kapcsolatoknál biztonságosabbak, megbízhatóbbak, valamint nagyobb a sávszélességük és alacsonyabb a késésük. Egyes esetekben kiemelkedően költséghatékony lehet az ExpressRoute-kapcsolat használata a helyszíni hálózat és az Azure közötti adatátvitelhez. Ha már csatlakoztatta helyszíni hálózatát létesítmények közötti kapcsolattal az Azure-hoz, áttérhet ExpressRoute-kapcsolatra a virtuális hálózat módosítása nélkül.

További részletek: [ExpressRoute FAQ](../expressroute/expressroute-faqs.md) (ExpressRoute – gyakori kérdések).

## <a name="site-to-site-connections-and-vpn-devices"></a>Helyek közötti kapcsolatok és VPN-eszközök
### <a name="what-should-i-consider-when-selecting-a-vpn-device"></a>Mit érdemes figyelembe venni a VPN-eszköz kiválasztásakor?
Eszközszállítói partnereinkkel különböző standard helyek közötti VPN-eszközöket ellenőriztünk. Az [itt](vpn-gateway-about-vpn-devices.md) elérhető listán szerepelnek a kompatibilis VPN-eszközök és a hozzájuk tartozó konfigurációs útmutatók vagy minták, valamint az eszközökre vonatkozó műszaki adatok. A listán kompatibilisként szereplő eszközcsaládokba tartozó összes eszköz működik a virtuális hálózatokkal. A VPN-eszköz konfigurálásához tekintse meg az eszközkonfigurációs mintát, vagy kövesse a megfelelő eszközcsaládhoz tartozó hivatkozást.

### <a name="what-do-i-do-if-i-have-a-vpn-device-that-isnt-in-the-known-compatible-device-list"></a>Mi a teendő, ha az én VPN-eszközöm nincs az ismert kompatibilis eszközök listájában?
Ha nem látja az eszközét a kompatibilis VPN-eszközök listájában, de azt szeretné használni a VPN-kapcsolathoz, ellenőriznie kell, hogy megfelel-e az [itt](vpn-gateway-about-vpn-devices.md) felsorolt támogatott IPsec/IKE-konfigurációknak és paramétereknek. A minimális rendszerkövetelményeknek megfelelő eszközök működnek a VPN-átjárókkal. További támogatásért és konfigurációs útmutatásért lépjen kapcsolatba az eszköze gyártójával.

### <a name="why-does-my-policy-based-vpn-tunnel-go-down-when-traffic-is-idle"></a>Miért áll le a házirendalapú VPN-alagutam, amikor nincs adatforgalom?
Ez normális működés házirendalapú (más néven statikus útválasztású) VPN-átjárók esetében. Ha az alagúton átmenő adatforgalom több mint 5 percig inaktív, a rendszer megszakítja az alagutat, Amikor az adatforgalom megindul bármelyik irányba, az alagút azonnal újra létrejön.

### <a name="can-i-use-software-vpns-to-connect-to-azure"></a>Csatlakozhatok az Azure-hoz szoftveres VPN-nel?
A helyek közötti létesítmények közötti konfigurációkhoz támogatottak a Windows Server 2012 útválasztási és távelérési (RRAS) kiszolgálók is.

Az egyéb szoftveres VPN-megoldások szintén működhetnek, ha megfelelnek az iparági szabványos IPsec-megvalósításoknak. A konfigurációs és támogatási útmutatáshoz vegye fel a kapcsolatot a szoftver szállítójával.

## <a name="point-to-site-connections"></a>Pont–hely kapcsolatok
### <a name="what-operating-systems-can-i-use-with-point-to-site"></a>Milyen operációs rendszereket használhatok pont–hely kapcsolatokhoz?
A következő operációs rendszerek támogatottak:

* Windows 7 (32 bites és 64 bites)
* Windows Server 2008 R2 (csak 64 bites)
* Windows 8 (32 bites és 64 bites)
* Windows 8.1 (32 bites és 64 bites)
* Windows Server 2012 (csak 64 bites)
* Windows Server 2012 R2 (csak 64 bites)
* Windows 10

### <a name="can-i-use-any-software-vpn-client-for-point-to-site-that-supports-sstp"></a>Használhatok szoftveres VPN-ügyfelet az SSTP-t támogató pont–hely kapcsolatokhoz?
Nem. Kizárólag a Windows operációs rendszerek fent felsorolt verziói támogatottak.

### <a name="how-many-vpn-client-endpoints-can-i-have-in-my-point-to-site-configuration"></a>Hány VPN-ügyfélvégpont lehet a pont–hely konfigurációban?
Egy virtuális hálózathoz egyidejűleg 128 VPN-ügyfél csatlakozhat.

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>Használhatom a saját PKI legfelső szintű hitelesítésszolgáltatómat a pont–hely kapcsolathoz?
Igen. Korábban csak önaláírt főtanúsítványt lehetett használni. Továbbra is 20 főtanúsítvány tölthető fel.

### <a name="can-i-traverse-proxies-and-firewalls-using-point-to-site-capability"></a>Lehetővé teszi-e a pont–hely kapcsolat a proxykon és tűzfalakon való áthaladást?
Igen. A tűzfalakon áthaladó alagutakhoz STTP-t (Secure Socket Tunneling Protocol) használunk. Ez az alagút HTTPS-kapcsolatként jelenik meg.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>Ha újraindítok egy pont–hely kapcsolat használatára konfigurált ügyfélszámítógépet, a VPN automatikusan újracsatlakozik?
Alapértelmezés szerint az ügyfélszámítógép nem létesíti újra a VPN-kapcsolatot.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>Támogatják a pont–hely kapcsolatok az automatikus újrakapcsolódást és a DDNS-t a VPN-ügyfeleken?
Az automatikus újrakapcsolódás és a DDNS jelenleg nem támogatott a pont–hely VPN-kapcsolatokhoz.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-coexist-for-the-same-virtual-network"></a>Lehetnek-e helyek közötti és pont–hely konfigurációk egyidejűleg egy virtuális hálózaton?
Igen. Mindkét megoldás működhet, ha az átjárójához RouteBased (útvonalapú) VPN-típust használ. A klasszikus üzemi modellhez dinamikus átjáróra van szükség. A pont–hely kapcsolat nem támogatott a statikus útválasztású VPN-átjárókhoz vagy a PolicyBased értékű -VpnType paraméterrel rendelkező átjárókhoz.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>Konfigurálhatok úgy egy pont–hely ügyfelet, hogy több virtuális hálózathoz csatlakozzon egyszerre?
Igen, ez lehetséges. Azonban ezen virtuális hálózatok IP-előtagjai nem lehetnek egymással átfedésben, és a pont–hely kapcsolatok címterei nem lehetnek egymással átfedésben a virtuális hálózatok között.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>Milyen átviteli sebességre számíthatok a helyek közötti és a pont–hely kapcsolatok esetében?
Az átviteli sebesség fenntartása nehéz a VPN-alagutakban. Az IPsec és az SSTP erős titkosítást használó VPN-protokoll. Az átviteli sebességet emellett a késés, valamint a helyszín és az internet közötti sávszélesség is korlátozza.

## <a name="gateways"></a>Átjárók
### <a name="what-is-a-policy-based-static-routing-gateway"></a>Mik azok a házirendalapú (statikus útválasztású) átjárók?
A házirendalapú átjárók házirendalapú VPN-kapcsolatokat valósítanak meg. A házirendalapú VPN-ek a helyszíni hálózat és az Azure VNet közötti címelőtag-kombinációk alapján titkosítják és irányítják a csomagokat az IPsec-alagutakon keresztül. A házirend (vagy forgalomválasztó) általában egy hozzáférési listaként van megadva a VPN-konfigurációban.

### <a name="what-is-a-route-based-dynamic-routing-gateway"></a>Mik azok az útvonalalapú (dinamikus útválasztású) átjárók?
Az útvonalalapú átjárók útvonalalapú VPN-kapcsolatokat valósítanak meg. Az útvonalalapú VPN-ek „útvonalakat” használnak az IP-továbbítási vagy útvonalválasztási táblán, hogy a csomagokat a megfelelő alagútkapcsolatokhoz irányítsák. Az alagútkapcsolatok ezután titkosítják vagy visszafejtik az alagutakba bemenő vagy onnan kijövő csomagokat. Az útvonalalapú VPN-ek házirendje (vagy forgalomválasztója) bármely két elem közöttiként (vagy helyettesítő karakterekként) van konfigurálva.

### <a name="can-i-get-my-vpn-gateway-ip-address-before-i-create-it"></a>Megszerezhetem a VPN-átjáróm IP-címét, mielőtt létrehozom az átjárót?
Nem. Az átjárót létre kell hozni az IP-cím beszerzéséhez. Ha törli, majd újra létrehozza a VPN-átjárót, az IP-cím megváltozik.

### <a name="how-does-my-vpn-tunnel-get-authenticated"></a>Hogyan történik a VPN-alagút hitelesítése?
Az Azure VPN PSK (előmegosztott kulcsos) hitelesítést használ. A VPN-alagút létrehozásakor létrehozunk egy előmegosztott kulcsot (PSK) is. Az automatikusan létrehozott PSK-t az Előmegosztott kulcs beállítása PowerShell-parancsmaggal vagy a REST API-vel lecserélheti a sajátjára.

### <a name="can-i-use-the-set-pre-shared-key-api-to-configure-my-policy-based-static-routing-gateway-vpn"></a>Használhatom-e az Előmegosztott kulcs beállítása API-t a házirendalapú (statikus útválasztású) átjárói VPN konfigurálásához?
Igen, az Előmegosztott kulcs beállítása API és PowerShell-parancsmag használható az Azure házirendalapú (statikus) VPN-ek és útvonalalapú (dinamikus) VPN-ek konfigurálásához is.

### <a name="can-i-use-other-authentication-options"></a>Használhatok más hitelesítési módszert?
Hitelesítésként csak az előmegosztott kulcsok (PSK-k) használhatók.

### <a name="what-is-the-gateway-subnet-and-why-is-it-needed"></a>Mi az az „átjáróalhálózat,” és miért szükséges?
A létesítmények közötti kapcsolatok az átjárószolgáltatásunkkal engedélyezhetők.

A VPN-átjáró konfigurálásához létre kell hozni egy átjáróalhálózatot a virtuális hálózathoz. A megfelelő működéshez az összes átjáró-alhálózatnak a GatewaySubnet névvel kell rendelkeznie. Ne nevezze el másként az átjáróalhálózatát, és ne helyezzen üzembe rajta virtuális gépeket vagy más eszközt.

Az átjáró-alhálózat minimális mérete teljes mértékben a létrehozni kívánt konfigurációtól függ. Ugyan egyes konfigurációkhoz létrehozhat kicsi, akár /29-es méretű átjáróalhálózatot is, ajánlott /28-ast vagy nagyobbat létrehozni (/28, /27, /26 stb.).

### <a name="can-i-deploy-virtual-machines-or-role-instances-to-my-gateway-subnet"></a>Telepíthetek virtuális gépeket vagy szerepkörpéldányokat az átjáróalhálózatomra?
Nem.

### <a name="how-do-i-specify-which-traffic-goes-through-the-vpn-gateway"></a>Hogyan határozhatom meg, milyen adatforgalom haladjon át a VPN-átjárón?
Ha a klasszikus Azure portált használja, a Hálózatok lap Helyi hálózatok részében adja hozzá az összes tartományt, amelyet átküldene az átjárón a virtuális hálózathoz.

### <a name="can-i-configure-forced-tunneling"></a>Konfigurálhatok kényszerített bújtatást?
Igen. Lásd: [Kényszerített bújtatás konfigurálása](vpn-gateway-about-forced-tunneling.md).

### <a name="can-i-set-up-my-own-vpn-server-in-azure-and-use-it-to-connect-to-my-on-premises-network"></a>Üzembe helyezhetem a saját VPN-kiszolgálómat az Azure-ban, és csatlakozhatok vele a helyszíni hálózatomhoz?
Igen, az Azure-ban üzembe helyezheti saját VPN-átjáróit vagy -kiszolgálóit az Azure Piactérről, vagy saját VPN-útválasztók létrehozásával. Ilyenkor a virtuális hálózatában felhasználó által definiált útvonalakat kell konfigurálnia, hogy az adatforgalom megfelelően legyen irányítva a helyszíni hálózatai és a virtuális hálózatainak alhálózatai között.

### <a name="why-are-certain-ports-opened-on-my-vpn-gateway"></a>Miért vannak egyes portok nyitva a VPN-átjárómon?
Ezek szükségesek az Azure-infrastruktúra kommunikációjához. A portokat Azure-tanúsítványok védik (zárják le). A megfelelő tanúsítványok nélkül a külső entitások – például az átjárók ügyfelei – nem gyakorolhatnak semmilyen hatást a végpontokra.

A VPN-átjáró alapvetően egy többhelyű eszköz, amelynek egy hálózati adaptere az ügyfél magánhálózatához, egy másik hálózati adaptere pedig a nyilvános hálózathoz csatlakozik. Az Azure-infrastruktúra entitásai megfelelőségi okokból nem csatlakozhatnak az ügyfelek magánhálózataihoz, így az infrastruktúra-kommunikációhoz nyilvános végpontokat kell használniuk. A nyilvános végpontokat az Azure biztonsági naplózás rendszeresen ellenőrzi.

### <a name="more-information-about-gateway-types-requirements-and-throughput"></a>További információk az átjárótípusokról, a követelményekről és az adatátviteli sebességről
További információk: [Tudnivalók a VPN Gateway beállításairól](vpn-gateway-about-vpn-gateway-settings.md).

## <a name="multi-site-and-vnet-to-vnet-connectivity"></a>Többhelyes és virtuális hálózatok közötti kapcsolatok
### <a name="which-type-of-gateways-can-support-multi-site-and-vnet-to-vnet-connectivity"></a>Milyen átjárótípusok támogatják a többhelyes és a virtuális hálózatok közötti kapcsolatokat?
Csak az útvonalalapú (dinamikus útválasztású) VPN-ek.

### <a name="can-i-connect-a-vnet-with-a-routebased-vpn-type-to-another-vnet-with-a-policybased-vpn-type"></a>Összekapcsolhatok egy RouteBased (útvonalapú) VPN-típussal rendelkező virtuális hálózatot egy házirendalapú VPN-típussal rendelkezővel?
Nem, mindkét virtuális hálózatnak útvonalalapú (dinamikus útválasztású) VPN-t KELL használnia.

### <a name="is-the-vnet-to-vnet-traffic-secure"></a>Biztonságos-e a virtuális hálózatok közötti adatforgalom?
Igen, az adatforgalmat IPsec/IKE-titkosítás védi.

### <a name="does-vnet-to-vnet-traffic-travel-over-the-azure-backbone"></a>A virtuális hálózatok közötti adatforgalom az Azure gerinchálózatát használja?
Igen.

### <a name="how-many-on-premises-sites-and-virtual-networks-can-one-virtual-network-connect-to"></a>Hány helyszíni helyhez és virtuális hálózathoz kapcsolódhat egyetlen virtuális hálózat?
Legfeljebb egyszerre 10-hez alapszintű és standard dinamikus útválasztású átjárók esetén, illetve 30-hoz nagy teljesítményű VPN-átjárók esetében.

### <a name="can-i-use-point-to-site-vpns-with-my-virtual-network-with-multiple-vpn-tunnels"></a>Használhatok pont–hely VPN-t több VPN-alagúttal a virtuális hálózatomhoz?
Igen, a pont–hely (P2S) VPN-ek több helyszíni helyhez és egyéb virtuális hálózatokhoz csatlakozó VPN-átjárókkal is használhatók.

### <a name="can-i-configure-multiple-tunnels-between-my-virtual-network-and-my-on-premises-site-using-multi-site-vpn"></a>Konfigurálhatok több alagutat a virtuális hálózatom és a helyszíni helyem között többhelyes VPN használatával?
Nem, a redundáns alagutak az Azure Virtual Network hálózatok és a helyszíni helyek között nem támogatottak.

### <a name="can-there-be-overlapping-address-spaces-among-the-connected-virtual-networks-and-on-premises-local-sites"></a>Lehetnek-e egymással átfedésben lévő címterek a csatlakoztatott virtuális hálózatok és helyszíni helyek között?
Nem. Ha a címterek átfedésben vannak egymással, a hálózati konfigurációs fájl feltöltése vagy a virtuális hálózat létrehozása meghiúsul.

### <a name="do-i-get-more-bandwidth-with-more-site-to-site-vpns-than-for-a-single-virtual-network"></a>Nagyobb sávszélességhez jutok több helyek közötti VPN használatával, mint egyetlen virtuális hálózattal?
Nem, az összes VPN-alagút, így a pont–hely VPN-ek is ugyanazt az Azure VPN Gateway átjárót és elérhető sávszélességet használják.

### <a name="can-i-use-azure-vpn-gateway-to-transit-traffic-between-my-on-premises-sites-or-to-another-virtual-network"></a>Használhatok Azure VPN Gateway átjárót az adatforgalomhoz a helyszíni helyeim között vagy egy másik virtuális hálózatba?
**Klasszikus üzemi modell**<br>
Az Azure VPN Gateway-átjárókon keresztüli adatátvitel a klasszikus üzemi modellel lehetséges, de ez a hálózati konfigurációs fájlban statikusan meghatározott címterekre hagyatkozik. A BGP jelenleg nem támogatott az Azure Virtual Networkökhöz és VPN Gateway-átjárókhoz a klasszikus üzemi modell használatával. BGP nélkül az átviteli címterek manuális meghatározása sok hibalehetőséggel jár, ezért nem ajánlott.<br>
**Resource Manager-alapú üzemi modell**<br>
Ha a Resource Manager-alapú üzemi modellt használja, további információért tekintse meg a [BGP](#bgp) szakaszt.

### <a name="does-azure-generate-the-same-ipsecike-pre-shared-key-for-all-my-vpn-connections-for-the-same-virtual-network"></a>Egy adott virtuális hálózaton az Azure ugyanazt az IPsec/IKE előmegosztott kulcsot hozza létre az összes VPN-kapcsolathoz?
Nem, az Azure alapértelmezés szerint különböző előmegosztott kulcsokat hoz létre a különböző VPN-kapcsolatokhoz. Azonban a VPN-átjáró kulcsának megadása REST API-val vagy PowerShell-parancsmaggal tetszőlegesen megadhatja a kulcs értékét. A kulcsnak 1–128 karakter közötti hosszúságú alfanumerikus karakterláncnak KELL lennie.

### <a name="does-azure-charge-for-traffic-between-virtual-networks"></a>Felszámol az Azure díjat a virtuális hálózatok közötti adatforgalomért?
A különböző Azure Virtual Networkök közötti adatforgalom esetében az Azure csak az egyik Azure-régióból másikba való adatátvitelért számít fel díjat. A díjszabás az Azure [VPN Gateway díjszabás](https://azure.microsoft.com/pricing/details/vpn-gateway/) oldalán található.

### <a name="can-i-connect-a-virtual-network-with-ipsec-vpns-to-my-expressroute-circuit"></a>Csatlakoztathatok IPsec VPN-ekkel rendelkező virtuális hálózatot az ExpressRoute-kapcsolatcsoportomhoz?
Igen, ez támogatott. További információk: [Párhuzamosan fennálló ExpressRoute- és helyek közötti VPN-kapcsolatok konfigurálása](../expressroute/expressroute-howto-coexist-classic.md).

## <a name="a-namebgpabgp"></a><a name="bgp"></a>BGP
[!INCLUDE [vpn-gateway-bgp-faq-include](../../includes/vpn-gateway-bpg-faq-include.md)]

## <a name="cross-premises-connectivity-and-vms"></a>Létesítmények közötti kapcsolatok és virtuális gépek
### <a name="if-my-virtual-machine-is-in-a-virtual-network-and-i-have-a-cross-premises-connection-how-should-i-connect-to-the-vm"></a>Ha a virtuális gépem egy virtuális hálózaton található, és rendelkezem egy létesítmények közötti kapcsolattal, hogyan csatlakozhatok a virtuális géphez?
Több lehetősége van. Ha az RDP engedélyezve van, és létrehozott egy végpontot, a virtuális IP-címmel csatlakozhat a virtuális géphez. Ebben az esetben meg kell adnia a virtuális IP-címet és a portot, amelyhez csatlakozni szeretne, a virtuális gép portját pedig konfigurálnia kell az adatátvitelhez. Általános esetben ehhez lépjen a klasszikus Azure portálra, és mentse az RDP-kapcsolat beállításait a számítógépén. Ezek a beállítások tartalmazzák a szükséges kapcsolati információkat.

Ha a virtuális hálózatán konfigurálva van létesítmények közötti kapcsolat, a belső dedikált IP-címmel vagy magánhálózati IP-címmel csatlakozhat a virtuális géphez. A belső dedikált IP-címmel egy, a virtuális hálózaton található másik virtuális gépről is csatlakoztathat a virtuális géphez. Ha a virtuális hálózatán kívüli helyről csatlakozik, nem használhatja az RDP-t a virtuális gépéhez való csatlakozáshoz a dedikált IP-címmel. Ha például pont–hely virtuális hálózatot konfigurált, és nem létesít kapcsolatot a számítógépéről, nem csatlakozhat a virtuális gépéhez a dedikált IP-címmel.

### <a name="if-my-virtual-machine-is-in-a-virtual-network-with-cross-premises-connectivity-does-all-the-traffic-from-my-vm-go-through-that-connection"></a>Ha a virtuális gépem létesítmények közötti kapcsolattal rendelkező virtuális hálózaton található, a virtuális gépem teljes adatforgalma ezen a kapcsolaton halad át?
Nem. Csak az az adatforgalom fog áthaladni a virtuális hálózati átjárón, amely a virtuális hálózat Ön által meghatározott helyi hálózati IP-címtartományaiban található cél-IP-címmel rendelkezik. A virtuális hálózaton belüli cél-IP-címmel rendelkező adatforgalom a virtuális hálózaton belül marad. Az egyéb adatforgalom a terheléselosztón keresztül a nyilvános hálózatok felé lesz irányítva, vagy ha kényszerített bújtatást használ, akkor az Azure VPN Gatewayen megy keresztül. Ha hibaelhárítást végez, ügyeljen arra, hogy az összes tartomány fel legyen sorolva a helyi hálózatán, amelyet át szeretne küldeni az átjárón. Ellenőrizze, hogy a helyi hálózat címtartományai nincsenek-e átfedésben a virtuális hálózatban található címtartományok valamelyikével. Emellett ellenőrizze azt is, hogy a használt DNS-kiszolgáló a megfelelő IP-címre oldja-e fel a nevet.

## <a name="virtual-network-faq"></a>Virtual Network – gyakori kérdések
A virtuális hálózatokkal kapcsolatos további információk: [Virtual Network FAQ](../virtual-network/virtual-networks-faq.md) (Virtual Network – gyakori kérdések).



<!--HONumber=Nov16_HO2-->


