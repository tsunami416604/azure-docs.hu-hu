<a id="is-custom-ipsecike-policy-supported-on-all-azure-vpn-gateway-skus" class="xliff"></a>

### Minden Azure VPN-átjáróhoz tartozó termékváltozat támogatja az egyéni IPsec/IKE-házirendet?
Az egyéni IPsec/IKE-házirendet az Azure **VpnGw1, VpnGw2, VpnGw3, Standard** és a **Nagy teljesítményű** VPN-átjárók támogatják. Az **alapszintű** SKU NEM támogatott.

<a id="how-many-policies-can-i-specify-on-a-connection" class="xliff"></a>

### Hány házirendeket adhatok meg egy kapcsolathoz?
Egy adott kapcsolathoz csak ***egy*** házirendet adhat meg.

<a id="can-i-specify-a-partial-policy-on-a-connection-eg-only-ike-algorithms-but-not-ipsec" class="xliff"></a>

### Megadhatok részleges házirendet egy kapcsolathoz? (Például csak IKE-algoritmusokat IPsec nélkül)
Nem, minden algoritmust és paramétert meg kell adnia mind az IKE (Elsődleges mód), mind az IPsec (Gyors mód) esetében. A részleges házirend-megadás nem engedélyezett.

<a id="what-are-the-algorithms-and-key-strengths-supported-in-the-custom-policy" class="xliff"></a>

### Milyen algoritmusokat és milyen erősségű kulcsokat támogat az egyéni házirend?
Az alábbi táblázatban megtekintheti az ügyfelek által konfigurálható, támogatott titkosítási algoritmusokat és kulcserősségeket. Minden mezőhöz választania kell egy lehetőséget.

| **IPsec/IKEv2**  | **Beállítások**                                                                 |
| ---              | ---                                                                         |
| IKEv2-titkosítás | AES256, AES192, AES128, DES3, DES                                           |
| IKEv2-integritás  | SHA384, MD5, SHA1, SHA256                                                   |
| DH-csoport         | ECP384, ECP256, DHGroup24, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, Nincs |
| IPsec-titkosítás | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, Nincs    |
| IPsec-integritás  | GCMAES256, GCMAES192, GCMAES128, SHA-256, SHA1, MD5                          |
| PFS-csoport        | ECP384, ECP256, PFS24, PFS2048, PFS14, PFS2, PFS1, Nincs                     |
| QM SA élettartama*  | Másodperc (egész; **min. 300**) és kilobájt (egész; **min. 1024**)                                      |
| Forgalomválasztó | UsePolicyBasedTrafficSelectors** ($True/$False; alapértelmezett $False)                             |
|                  |                                                                             |

* (*) Az IKEv2 Elsődleges Módú SA élettartama pontosan 28 800 másodpercre van beállítva az Azure VPN-átjárókon
* (**) Tekintse meg a következő, a „UsePolicyBasedTrafficSelectors” című gyakori kérdést

<a id="does-everything-need-to-match-between-the-azure-vpn-gateway-policy-and-my-on-premises-vpn-device-configurations" class="xliff"></a>

### Mindennek egyeznie kell az Azure VPN-átjáró házirendjében és a helyszíni VPN-eszközkonfigurációkban?
A helyszíni VPN-eszköz konfigurációjának meg kell egyezniük velük, vagy tartalmazniuk kell az alábbi, az Azure IPsec/IKE-házirendben megadott algoritmusokat és paramétereket:

* IKE titkosítási algoritmus
* IKE integritási algoritmus
* DH-csoport
* IPsec titkosítási algoritmus
* IPsec integritási algoritmus
* PFS-csoport
* Forgalomválasztó (*)

AZ SA-k élettartamai helyi szinten definiáltak, így azoknak nem kell megegyezniük.

Ha engedélyezi az **UsePolicyBasedTrafficSelectors** lehetőséget, meg kell győződnie arról, hogy a VPN-eszköz megegyező forgalomválasztóihoz a helyszíni hálózat az Azure virtuális hálózatától (helyi hálózati átjáró), illetve onnan érkező előtagjainak minden kombinációja van definiálva a bármely elemek közötti definíció helyett. Például ha a helyszíni hálózati előtagok a 10.1.0.0/16 és a 10.2.0.0/16, a virtuális hálózati előtagok pedig 192.168.0.0/16 és 172.16.0.0/16, az alábbi forgalomválasztókat kell megadnia:
* 10.1.0.0/16 <====> 192.168.0.0/16
* 10.1.0.0/16 <====> 172.16.0.0/16
* 10.2.0.0/16 <====> 192.168.0.0/16
* 10.2.0.0/16 <====> 172.16.0.0/16

A beállítás alkalmazásáról további információt a [Több helyszíni házirendalapú VPN-eszköz csatlakoztatása](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md) című cikkben találhat.

<a id="does-the-custom-policy-replace-the-default-ipsecike-policy-sets-for-azure-vpn-gateways" class="xliff"></a>

### Az egyéni házrend helyettesíti az alapértelmezett IPsec/IKE-házirendet az Azure VPN-átjárókon?
Igen. Miután megadott egy egyéni házirendet egy kapcsolathoz, az Azure VPN-átjáró csak a kapcsolat házirendjét használja, mind IKE-kezdeményezőként, mind IKE-válaszadóként.

<a id="if-i-remove-a-custom-ipsecike-policy-does-the-connection-become-unprotected" class="xliff"></a>

### Ha eltávolítok egy egyéni IPsec/IKE-házirendet, azzal a kapcsolat nem védetté válik?
Nem, a kapcsolatot továbbra is védi az IPsec/IKE. Miután eltávolítja a kapcsolat egyéni házirendjét, az Azure VPN-átjáró visszaáll az [IPsec/IKE-javaslatok alapértelmezett listájára](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md), és újraindítja az IKE-kézfogást a helyszíni VPN-eszközzel.

<a id="would-adding-or-updating-an-ipsecike-policy-disrupt-my-vpn-connection" class="xliff"></a>

### Az IPsec/IKE-házirend hozzáadása vagy frissítése megszakítja a VPN-kapcsolatot?
Igen, ez okozhat rövid (néhány másodperces) megszakítást, mivel az Azure VPN-átjáró bontja a meglévő kapcsolatot, és újraindítja az IKE-kézfogást, így újra létrehozza az IPsec-alagutat az új titkosítási algoritmusokkal és paraméterekkel. Győződjön meg róla, hogy a helyszíni VPN-eszközt is konfigurálta ugyanazokkal az algoritmusokkal és kulcserősségekkel, így minimálisra csökkentheti a megszakítások időtartamát.

<a id="can-i-use-different-policies-on-different-connections" class="xliff"></a>

### Használhatok különböző házirendeket különböző kapcsolatokhoz?
Igen. Az egyéni házirendeket kapcsolatonként hozza létre. A különböző kapcsolatokhoz különböző IPsec/IKE-házirendeket hozhat létre és alkalmazhat. Emellett a kapcsolatok egy részhalmazára is alkalmazhat egyéni házirendeket. A fennmaradó kapcsolatok az Azure alapértelmezett IPsec/IKE-házirendjét használják.

<a id="can-i-use-the-custom-policy-on-vnet-to-vnet-connection-as-well" class="xliff"></a>

### Használhatok egyéni házirendet a virtuális hálózatok közötti kapcsolatokhoz is?
Igen, mind az IPsec létesítmények közötti kapcsolataihoz, mind a virtuális hálózatok közötti kapcsolatokhoz alkalmazhat egyéni házirendet.

<a id="do-i-need-to-specify-the-same-policy-on-both-vnet-to-vnet-connection-resources" class="xliff"></a>

### Ugyanazt a házirendet kell megadnom mindkét, virtuális hálózatok közötti kapcsolat erőforrásaihoz?
Igen. A virtuális hálózatok közötti alagút két kapcsolati erőforrásból áll az Azure-ban, amelyek a két különböző irányba mutatnak. Meg kell győződnie arról, hogy mindkét kapcsolati erőforrás azonos házirenddel rendelkezik, ellenkező esetben a virtuális hálózatok közötti kapcsolat nem jön létre.

<a id="does-custom-ipsecike-policy-work-on-expressroute-connection" class="xliff"></a>

### Működik az egyéni IPsec/IKE-házirend az ExpressRoute-kapcsolatokkal?
Nem. Az IPsec/IKE-házirend csak az S2S VPN- és a virtuális hálózatok közötti kapcsolatokkal, az Azure VPN-átjárókon keresztül működik.
