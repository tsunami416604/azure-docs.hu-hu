---
title: "Azure VPN Gateway – gyakori kérdések | Microsoft Docs"
description: "A VPN Gatewayjel kapcsolatos gyakori kérdések. Gyakori kérdések a Microsoft Azure Virtual Network telephelyek közötti kapcsolatairól, a hibrid konfigurációjú kapcsolatokról és a VPN-átjárókról."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
ms.assetid: 6ce36765-250e-444b-bfc7-5f9ec7ce0742
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 49aeb711de73bf967f330ec54385fb8bb36dd7ca
ms.lasthandoff: 04/17/2017


---
# <a name="vpn-gateway-faq"></a>VPN Gateway – gyakori kérdések
## <a name="connecting-to-virtual-networks"></a>Csatlakozás virtuális hálózatokhoz
### <a name="can-i-connect-virtual-networks-in-different-azure-regions"></a>Összekapcsolhatok eltérő Azure-régiókban található virtuális hálózatokat?
Igen. Nincs régiókorlátozás. A virtuális hálózatok összekapcsolhatók az azonos régióban vagy más Azure-régiókban található virtuális hálózatokkal is. 

### <a name="can-i-connect-virtual-networks-in-different-subscriptions"></a>Összekapcsolhatok egymással különböző előfizetésekben található virtuális hálózatokat?
Igen.

### <a name="can-i-connect-to-multiple-sites-from-a-single-virtual-network"></a>Csatlakozhatok több helyhez egyetlen virtuális hálózatból?
A Windows PowerShell és az Azure REST API-k használatával kapcsolódhat több helyhez is. Lásd a gyakori kérdések [Többhelyes és virtuális hálózatok közötti kapcsolatok](#V2VMulti) című szakaszát.

### <a name="what-are-my-cross-premises-connection-options"></a>Milyen lehetőségeim vannak a létesítmények közötti kapcsolódásra?
A következő létesítmények közötti kapcsolattípusok támogatottak:

* Helyek közötti kapcsolat – VPN-kapcsolat IPsec (IKE v1 és IKE v2) használatával. Ehhez a kapcsolattípushoz VPN-eszköz vagy RRAS szükséges. További információ: [Helyek közötti kapcsolat](vpn-gateway-howto-site-to-site-resource-manager-portal.md).
* Pont–hely kapcsolat – VPN-kapcsolat SSTP (Secure Socket Tunneling Protocol) használatával. Ehhez a kapcsolattípushoz nem szükséges VPN-eszköz. További információ: [Pont–hely kapcsolat](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* Virtuális hálózatok közötti kapcsolat – A helyek közötti kapcsolattal megegyező kapcsolattípus. A virtuális hálózatok közötti kapcsolat egy IPsec-et (IKE v1 és IKE v2) használó VPN-kapcsolat, nem szükséges hozzá VPN-eszköz. További információ: [Virtuális hálózatok közötti kapcsolat](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).
* Többhelyes kapcsolat – Ez a helyek közötti kapcsolat egy változata, amely több helyszíni hely csatlakoztatását teszi lehetővé egy virtuális hálózathoz. További információ: [Többhelyes kapcsolat](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md).
* ExpressRoute – Az ExpressRoute közvetlen kapcsolatot biztosít az Azure-ral WAN-on (nem egy VPN-kapcsolattal a nyilvános interneten) keresztül. További információk: [ExpressRoute Technical Overview](../expressroute/expressroute-introduction.md) (Az ExpressRoute műszaki áttekintése) és [ExpressRoute FAQ](../expressroute/expressroute-faqs.md) (ExpressRoute – gyakori kérdések).

További információk a VPN Gateway-kapcsolatokról: [Információk a VPN Gateway-ről](vpn-gateway-about-vpngateways.md).

### <a name="what-is-the-difference-between-a-site-to-site-connection-and-point-to-site"></a>Mi a különbség a helyek közötti és a pont–hely kapcsolatok között?
A **helyek közötti** (IPsec/IKE VPN-alagút) konfigurációk az Ön telephelye és az Azure között vannak. Ez azt jelenti, hogy a helyszínen található számítógépek bármelyikéről csatlakozhat a virtuális hálózaton belüli virtuális gépek vagy szerepkörpéldányok bármelyikéhez az útválasztás és az engedélyek konfigurációjától függően. Ez ideális megoldás folyamatosan elérhető létesítmények közötti kapcsolatokhoz, és hibrid konfigurációkhoz is használható. Ez a kapcsolattípus IPsec VPN-készüléket használ (hardvereszközt vagy szoftverkészüléket), amelyet a hálózat szélén kell üzembe helyezni. Az ilyen típusú kapcsolat létrehozásához szükség van egy kifelé irányuló IPv4-címre, amely nem NAT mögött helyezkedik el.

A **pont–hely** (SSTP-alapú VPN) konfigurációkkal csatlakozhat egy tetszőleges helyen található számítógépről a virtuális hálózata összes eleméhez. Ez a típus a Windows beépített VPN-ügyfelét használja. A pont–hely konfiguráció részeként telepíteni kell egy tanúsítványt és egy VPN-ügyfélkonfigurációs csomagot, amelyben azok a beállítások találhatók, amelyeket a számítógépe használ a virtuális hálózatban található virtuális gépekhez vagy szerepkörpéldányokhoz való csatlakozáshoz. Ez ideális megoldás, ha csatlakozni szeretne egy virtuális hálózathoz, de nem a helyszínen tartózkodik, valamint akkor is jól használható, ha nincs hozzáférése VPN-hardverhez vagy kifelé irányuló IPv4-címhez, amelyek a helyek közötti kapcsolatok kialakításához szükségesek.

A virtuális hálózatát konfigurálhatja úgy, hogy helyek közötti és pont–hely kapcsolatokat használjon egyidejűleg – ha a helyek közötti kapcsolatot útvonalalapú VPN-típussal hozza létre az átjáróhoz. Az útvonalalapú VPN-típusok korábbi megnevezése dinamikus átjáró volt a klasszikus üzemi modellben.

## <a name="virtual-network-gateways"></a>Virtuális hálózati átjárók

### <a name="is-a-vpn-gateway-a-virtual-network-gateway"></a>A VPN Gateway virtuális hálózati átjáró?
A VPN Gateway a virtuális hálózati átjárók egy típusa. A VPN Gateway titkosított adatforgalmat továbbít nyilvános kapcsolaton keresztül a virtuális hálózat és az Ön telephelye között. VPN Gateway használatával a virtuális hálózatok között is továbbíthat adatforgalmat. VPN Gateway létrehozásakor a „Vpn” -GatewayType értéket használja. További információ: [Információk a VPN Gateway konfigurációs beállításairól](vpn-gateway-about-vpn-gateway-settings.md).

### <a name="what-is-a-policy-based-static-routing-gateway"></a>Mik azok a házirendalapú (statikus útválasztású) átjárók?
A házirendalapú átjárók házirendalapú VPN-kapcsolatokat valósítanak meg. A házirendalapú VPN-ek a helyszíni hálózat és az Azure VNet közötti címelőtag-kombinációk alapján titkosítják és irányítják a csomagokat az IPsec-alagutakon keresztül. A házirend (vagy forgalomválasztó) általában egy hozzáférési listaként van megadva a VPN-konfigurációban.

### <a name="what-is-a-route-based-dynamic-routing-gateway"></a>Mik azok az útvonalalapú (dinamikus útválasztású) átjárók?
Az útvonalalapú átjárók útvonalalapú VPN-kapcsolatokat valósítanak meg. Az útvonalalapú VPN-ek „útvonalakat” használnak az IP-továbbítási vagy útvonalválasztási táblán, hogy a csomagokat a megfelelő alagútkapcsolatokhoz irányítsák. Az alagútkapcsolatok ezután titkosítják vagy visszafejtik az alagutakba bemenő vagy onnan kijövő csomagokat. Az útvonalalapú VPN-ek házirendje (vagy forgalomválasztója) bármely két elem közöttiként (vagy helyettesítő karakterekként) van konfigurálva.

### <a name="do-i-need-a-gatewaysubnet"></a>Szükségem van GatewaySubnetre?
Igen. Az átjáróalhálózat tartalmazza a virtuális hálózati átjáró-szolgáltatások által használt IP-címeket. A virtuális hálózati átjáró konfigurálásához létre kell hozni egy átjáróalhálózatot a virtuális hálózathoz. A megfelelő működéshez az összes átjáró-alhálózatnak a „GatewaySubnet” névvel kell rendelkeznie. Ne nevezze el másként az átjáróalhálózatát, és ne helyezzen üzembe rajta virtuális gépeket vagy más eszközt.

Az átjáróalhálózat létrehozásakor meg kell adnia, hogy hány IP-címet tartalmaz az alhálózat. Az átjáróalhálózatban lévő IP-címeket az átjárószolgáltatás számára foglalja le a rendszer. Egyes konfigurációk a többinél nagyobb számú IP-cím kiosztását követelik meg az átjárószolgáltatásokhoz. Győződjön meg arról, hogy az átjáróalhálózat elég IP-címet tartalmaz a későbbi növekedéshez és az esetleges új kapcsolatkonfigurációk kialakításához. Tehát, míg egyes konfigurációkhoz létrehozhat kicsi, akár /29-es méretű átjáróalhálózatot is, ajánlott /27-est vagy nagyobbat létrehozni (/27, /26, /25 stb.). Vizsgálja meg a létrehozni kívánt konfiguráció követelményeit és ellenőrizze, hogy az átjáró-alhálózat megfelel-e ezeknek a követelményeknek.

### <a name="can-i-deploy-virtual-machines-or-role-instances-to-my-gateway-subnet"></a>Telepíthetek virtuális gépeket vagy szerepkörpéldányokat az átjáróalhálózatomra?
Nem.

### <a name="can-i-get-my-vpn-gateway-ip-address-before-i-create-it"></a>Megszerezhetem a VPN-átjáróm IP-címét, mielőtt létrehozom az átjárót?
Nem. Az átjárót létre kell hozni az IP-cím beszerzéséhez. Ha törli, majd újra létrehozza a VPN-átjárót, az IP-cím megváltozik.

### <a name="how-does-my-vpn-tunnel-get-authenticated"></a>Hogyan történik a VPN-alagút hitelesítése?
Az Azure VPN PSK (előmegosztott kulcsos) hitelesítést használ. A VPN-alagút létrehozásakor létrehozunk egy előmegosztott kulcsot (PSK) is. Az automatikusan létrehozott PSK-t az Előmegosztott kulcs beállítása PowerShell-parancsmaggal vagy a REST API-vel lecserélheti a sajátjára.

### <a name="can-i-use-the-set-pre-shared-key-api-to-configure-my-policy-based-static-routing-gateway-vpn"></a>Használhatom-e az Előmegosztott kulcs beállítása API-t a házirendalapú (statikus útválasztású) átjárói VPN konfigurálásához?
Igen, az Előmegosztott kulcs beállítása API és PowerShell-parancsmag használható az Azure házirendalapú (statikus) VPN-ek és útvonalalapú (dinamikus) VPN-ek konfigurálásához is.

### <a name="can-i-use-other-authentication-options"></a>Használhatok más hitelesítési módszert?
Hitelesítésként csak az előmegosztott kulcsok (PSK-k) használhatók.


### <a name="how-do-i-specify-which-traffic-goes-through-the-vpn-gateway"></a>Hogyan határozhatom meg, milyen adatforgalom haladjon át a VPN-átjárón?

####<a name="resource-manager-deployment-model"></a>Resource Manager-alapú üzemi modell
* PowerShell esetén: Használja az „AddressPrefix” parancsot a helyi hálózati átjáró forgalmának meghatározásához.
* Azure Portal esetén: Lépjen a Helyi hálózati átjáró > Konfiguráció > Címtér felületre.

####<a name="classic-deployment-model"></a>Klasszikus üzemi modell
* Azure Portal esetén: Lépjen a klasszikus virtuális hálózat > VPN connections (VPN-kapcsolatok) > Site-to-site VPN connections (Helyek közötti VPN-kapcsolatok) > Helyi hely neve > Helyi hely > Client address space (Ügyfélcímtér) felületre. 
* Klasszikus portál esetén: A Networks (Hálózatok) lap Local Networks (Helyi hálózatok) részében adja hozzá az összes tartományt, amelyet átküldene az átjárón a virtuális hálózathoz. 

### <a name="can-i-configure-forced-tunneling"></a>Konfigurálhatok kényszerített bújtatást?
Igen. Lásd: [Kényszerített bújtatás konfigurálása](vpn-gateway-about-forced-tunneling.md).

### <a name="can-i-set-up-my-own-vpn-server-in-azure-and-use-it-to-connect-to-my-on-premises-network"></a>Üzembe helyezhetem a saját VPN-kiszolgálómat az Azure-ban, és csatlakozhatok vele a helyszíni hálózatomhoz?
Igen, az Azure-ban üzembe helyezheti saját VPN-átjáróit vagy -kiszolgálóit az Azure Piactérről, vagy saját VPN-útválasztók létrehozásával. Ilyenkor a virtuális hálózatában felhasználó által definiált útvonalakat kell konfigurálnia, hogy az adatforgalom megfelelően legyen irányítva a helyszíni hálózatai és a virtuális hálózatainak alhálózatai között.

### <a name="why-are-certain-ports-opened-on-my-vpn-gateway"></a>Miért vannak egyes portok nyitva a VPN-átjárómon?
Ezek szükségesek az Azure-infrastruktúra kommunikációjához. A portokat Azure-tanúsítványok védik (zárják le). A megfelelő tanúsítványok nélkül a külső entitások – például az átjárók ügyfelei – nem gyakorolhatnak semmilyen hatást a végpontokra.

A VPN-átjáró alapvetően egy többhelyű eszköz, amelynek egy hálózati adaptere az ügyfél magánhálózatához, egy másik hálózati adaptere pedig a nyilvános hálózathoz csatlakozik. Az Azure-infrastruktúra entitásai megfelelőségi okokból nem csatlakozhatnak az ügyfelek magánhálózataihoz, így az infrastruktúra-kommunikációhoz nyilvános végpontokat kell használniuk. A nyilvános végpontokat az Azure biztonsági naplózás rendszeresen ellenőrzi.

### <a name="more-information-about-gateway-types-requirements-and-throughput"></a>További információk az átjárótípusokról, a követelményekről és az adatátviteli sebességről
További információ: [Információk a VPN Gateway konfigurációs beállításairól](vpn-gateway-about-vpn-gateway-settings.md).

## <a name="site-to-site-connections-and-vpn-devices"></a>Helyek közötti kapcsolatok és VPN-eszközök

### <a name="what-should-i-consider-when-selecting-a-vpn-device"></a>Mit érdemes figyelembe venni a VPN-eszköz kiválasztásakor?
Eszközszállítói partnereinkkel különböző standard helyek közötti VPN-eszközöket ellenőriztünk. A kompatibilis VPN-eszközök, a hozzájuk tartozó konfigurációs útmutatók vagy minták, valamint az eszközökre vonatkozó műszaki adatok listája a [Tudnivalók a VPN-eszközökről](vpn-gateway-about-vpn-devices.md) című cikkben található. A listán kompatibilisként szereplő eszközcsaládokba tartozó összes eszköz működik a virtuális hálózatokkal. A VPN-eszköz konfigurálásához tekintse meg az eszközkonfigurációs mintát, vagy kövesse a megfelelő eszközcsaládhoz tartozó hivatkozást.

### <a name="where-can-i-find-configuration-settings-for-vpn-devices"></a>Hol találom a VPN-eszközök konfigurációs beállításait?

Az eszköz konfigurációs beállításaira mutató hivatkozásokért tekintse meg az [ellenőrzött VPN-eszközökkel](vpn-gateway-about-vpn-devices.md#devicetable) kapcsolatos témakört. Az eszközkonfigurációs hivatkozásokat képességeinkhez mérten biztosítjuk. A legfrissebb konfigurációs információkért érdemes az eszköz gyártójához fordulni.

A VPN-eszköz konfigurálása előtt ellenőrizze, hogy a használni kívánt VPN-eszközre nem vonatkoznak-e [ismert eszközkompatibilitási problémák](vpn-gateway-about-vpn-devices.md#known).

### <a name="how-do-i-edit-vpn-device-configuration-samples"></a>Hogyan szerkeszthetem a VPN-eszközök konfigurációs mintáit?

Az eszközök konfigurációs mintáinak szerkesztésével kapcsolatos információkért tekintse meg a [minták szerkesztésével](vpn-gateway-about-vpn-devices.md#editing) kapcsolatos részt.

### <a name="where-do-i-find-ipsec-and-ike-parameters"></a>Hol találom az IPsec/IKE-paramétereket?

Az IPsec/IKE-paraméterekkel kapcsolatos információkért tekintse meg a [paraméterekkel](vpn-gateway-about-vpn-devices.md#ipsec) kapcsolatos részt.

### <a name="why-does-my-policy-based-vpn-tunnel-go-down-when-traffic-is-idle"></a>Miért áll le a házirendalapú VPN-alagutam, amikor nincs adatforgalom?
Ez normális működés házirendalapú (más néven statikus útválasztású) VPN-átjárók esetében. Ha az alagúton átmenő adatforgalom több mint 5 percig inaktív, a rendszer megszakítja az alagutat, Amikor az adatforgalom megindul bármelyik irányba, az alagút azonnal újra létrejön.

### <a name="can-i-use-software-vpns-to-connect-to-azure"></a>Csatlakozhatok az Azure-hoz szoftveres VPN-nel?
A helyek közötti létesítmények közötti konfigurációkhoz támogatottak a Windows Server 2012 útválasztási és távelérési (RRAS) kiszolgálók is.

Az egyéb szoftveres VPN-megoldások szintén működhetnek, ha megfelelnek az iparági szabványos IPsec-megvalósításoknak. A konfigurációs és támogatási útmutatáshoz vegye fel a kapcsolatot a szoftver szállítójával.

## <a name="P2S"></a>Pont–hely kapcsolatok

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## <a name="V2VMulti"></a>Virtuális hálózatok közötti kapcsolat és többhelyes kapcsolatok

[!INCLUDE [vpn-gateway-vnet-vnet-faq-include](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]

### <a name="can-i-use-azure-vpn-gateway-to-transit-traffic-between-my-on-premises-sites-or-to-another-virtual-network"></a>Használhatok Azure VPN Gateway átjárót az adatforgalomhoz a helyszíni helyeim között vagy egy másik virtuális hálózatba?

**Resource Manager-alapú üzemi modell**<br>
Igen. További információért lásd a [BGP](#bgp) szakaszt.

**Klasszikus üzemi modell**<br>
Az Azure VPN Gateway-átjárókon keresztüli adatátvitel a klasszikus üzemi modellel lehetséges, de ez a hálózati konfigurációs fájlban statikusan meghatározott címterekre hagyatkozik. A BGP jelenleg nem támogatott az Azure Virtual Networkökhöz és VPN Gateway-átjárókhoz a klasszikus üzemi modell használatával. BGP nélkül az átviteli címterek manuális meghatározása sok hibalehetőséggel jár, ezért nem ajánlott.

### <a name="does-azure-generate-the-same-ipsecike-pre-shared-key-for-all-my-vpn-connections-for-the-same-virtual-network"></a>Egy adott virtuális hálózaton az Azure ugyanazt az IPsec/IKE előmegosztott kulcsot hozza létre az összes VPN-kapcsolathoz?
Nem, az Azure alapértelmezés szerint különböző előmegosztott kulcsokat hoz létre a különböző VPN-kapcsolatokhoz. Azonban a VPN-átjáró kulcsának megadása REST API-val vagy PowerShell-parancsmaggal tetszőlegesen megadhatja a kulcs értékét. A kulcsnak 1–128 karakter közötti hosszúságú alfanumerikus karakterláncnak KELL lennie.

### <a name="do-i-get-more-bandwidth-with-more-site-to-site-vpns-than-for-a-single-virtual-network"></a>Nagyobb sávszélességhez jutok több helyek közötti VPN használatával, mint egyetlen virtuális hálózattal?
Nem, az összes VPN-alagút, így a pont–hely VPN-ek is ugyanazt az Azure VPN Gateway átjárót és elérhető sávszélességet használják.

### <a name="can-i-configure-multiple-tunnels-between-my-virtual-network-and-my-on-premises-site-using-multi-site-vpn"></a>Konfigurálhatok több alagutat a virtuális hálózatom és a helyszíni helyem között többhelyes VPN használatával?
Igen, de mindkét alagúton ugyanarra a helyre kell konfigurálnia a BGP-t.

### <a name="can-i-use-point-to-site-vpns-with-my-virtual-network-with-multiple-vpn-tunnels"></a>Használhatok pont–hely VPN-t több VPN-alagúttal a virtuális hálózatomhoz?
Igen, a pont–hely (P2S) VPN-ek több helyszíni helyhez és egyéb virtuális hálózatokhoz csatlakozó VPN-átjárókkal is használhatók.

### <a name="can-i-connect-a-virtual-network-with-ipsec-vpns-to-my-expressroute-circuit"></a>Csatlakoztathatok IPsec VPN-ekkel rendelkező virtuális hálózatot az ExpressRoute-kapcsolatcsoportomhoz?
Igen, ez támogatott. További információk: [Párhuzamosan fennálló ExpressRoute- és helyek közötti VPN-kapcsolatok konfigurálása](../expressroute/expressroute-howto-coexist-classic.md).

## <a name="bgp"></a>BGP
[!INCLUDE [vpn-gateway-bgp-faq-include](../../includes/vpn-gateway-bpg-faq-include.md)]

## <a name="cross-premises-connectivity-and-vms"></a>Létesítmények közötti kapcsolatok és virtuális gépek
### <a name="if-my-virtual-machine-is-in-a-virtual-network-and-i-have-a-cross-premises-connection-how-should-i-connect-to-the-vm"></a>Ha a virtuális gépem egy virtuális hálózaton található, és rendelkezem egy létesítmények közötti kapcsolattal, hogyan csatlakozhatok a virtuális géphez?
Több lehetősége van. Ha az RDP engedélyezve van, és létrehozott egy végpontot, a virtuális IP-címmel csatlakozhat a virtuális géphez. Ebben az esetben meg kell adnia a virtuális IP-címet és a portot, amelyhez csatlakozni szeretne, a virtuális gép portját pedig konfigurálnia kell az adatátvitelhez. Általános esetben ehhez lépjen a klasszikus Azure portálra, és mentse az RDP-kapcsolat beállításait a számítógépén. Ezek a beállítások tartalmazzák a szükséges kapcsolati információkat.

Ha a virtuális hálózatán konfigurálva van létesítmények közötti kapcsolat, a belső dedikált IP-címmel vagy magánhálózati IP-címmel csatlakozhat a virtuális géphez. A belső dedikált IP-címmel egy, a virtuális hálózaton található másik virtuális gépről is csatlakoztathat a virtuális géphez. Ha a virtuális hálózatán kívüli helyről csatlakozik, nem használhatja az RDP-t a virtuális gépéhez való csatlakozáshoz a dedikált IP-címmel. Ha például pont–hely virtuális hálózatot konfigurált, és nem létesít kapcsolatot a számítógépéről, nem csatlakozhat a virtuális gépéhez a dedikált IP-címmel.

### <a name="if-my-virtual-machine-is-in-a-virtual-network-with-cross-premises-connectivity-does-all-the-traffic-from-my-vm-go-through-that-connection"></a>Ha a virtuális gépem létesítmények közötti kapcsolattal rendelkező virtuális hálózaton található, a virtuális gépem teljes adatforgalma ezen a kapcsolaton halad át?
Nem. Csak az az adatforgalom fog áthaladni a virtuális hálózati átjárón, amely a virtuális hálózat Ön által meghatározott helyi hálózati IP-címtartományaiban található cél-IP-címmel rendelkezik. A virtuális hálózaton belüli cél-IP-címmel rendelkező adatforgalom a virtuális hálózaton belül marad. Az egyéb adatforgalom a terheléselosztón keresztül a nyilvános hálózatok felé lesz irányítva, vagy ha kényszerített bújtatást használ, akkor az Azure VPN Gatewayen megy keresztül. Ha hibaelhárítást végez, ügyeljen arra, hogy az összes tartomány fel legyen sorolva a helyi hálózatán, amelyet át szeretne küldeni az átjárón. Ellenőrizze, hogy a helyi hálózat címtartományai nincsenek-e átfedésben a virtuális hálózatban található címtartományok valamelyikével. Emellett ellenőrizze azt is, hogy a használt DNS-kiszolgáló a megfelelő IP-címre oldja-e fel a nevet.

## <a name="virtual-network-faq"></a>Virtual Network – gyakori kérdések
A virtuális hálózatokkal kapcsolatos további információk: [Virtual Network FAQ](../virtual-network/virtual-networks-faq.md) (Virtual Network – gyakori kérdések).

## <a name="next-steps"></a>Következő lépések

* További információk a VPN Gatewayről: [Információk a VPN Gatewayről](vpn-gateway-about-vpngateways.md).
* További információk a VPN Gateway konfigurációs beállításairól: [Információk a VPN Gateway konfigurációs beállításairól](vpn-gateway-about-vpn-gateway-settings.md).
