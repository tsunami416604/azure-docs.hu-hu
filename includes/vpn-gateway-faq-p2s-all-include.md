### <a name="supportedclientos"></a>Milyen ügyfél operációs rendszereket használhatok pont–hely kapcsolatokhoz?

A következő ügyféloldali operációs rendszerek támogatottak:

* Windows 7 (32 bites és 64 bites)
* Windows Server 2008 R2 (csak 64 bites)
* Windows 8 (32 bites és 64 bites)
* Windows 8.1 (32 bites és 64 bites)
* Windows Server 2012 (csak 64 bites)
* Windows Server 2012 R2 (csak 64 bites)
* Windows 10
* OSX 10.11-es verzió Mac gépekhez (El Capitan)
* macOS 10.12-es verzió Mac gépekhez (Sierra)

### <a name="how-many-vpn-client-endpoints-can-i-have-in-my-point-to-site-configuration"></a>Hány VPN-ügyfélvégpont lehet a pont–hely konfigurációban?

Egy virtuális hálózathoz egyidejűleg 128 VPN-ügyfél csatlakozhat.

### <a name="can-i-traverse-proxies-and-firewalls-using-point-to-site-capability"></a>Lehetővé teszi-e a pont–hely kapcsolat a proxykon és tűzfalakon való áthaladást?

Az Azure kétféle pont-hely típusú VPN-lehetőséget támogat:

* Secure Socket Tunneling Protocol (SSTP). Az SSTP a Microsoft jogvédett, SSL-alapú megoldása, amely képes átjutni a tűzfalakon, mivel a legtöbb tűzfal nyitva hagyja az SSL által használt 443-as TCP-portot.

* IKEv2 VPN. Az IKEv2 VPN egy szabványalapú IPsec VPN-megoldás, amely az 500-as és 4500-as UDP-portokat, valamint a következő IP-protokollt használja: 50. A tűzfalak ezeket a portokat nem mindig nyitják meg, ezért elképzelhető, hogy az IKEv2 VPN nem képes átjutni egyes proxykon és tűzfalakon.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>Ha újraindítok egy pont–hely kapcsolat használatára konfigurált ügyfélszámítógépet, a VPN automatikusan újracsatlakozik?

Alapértelmezés szerint az ügyfélszámítógép nem létesíti újra a VPN-kapcsolatot.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>Támogatják a pont–hely kapcsolatok az automatikus újrakapcsolódást és a DDNS-t a VPN-ügyfeleken?

Az automatikus újrakapcsolódás és a DDNS jelenleg nem támogatott a pont–hely VPN-kapcsolatokhoz.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-coexist-for-the-same-virtual-network"></a>Lehetnek-e helyek közötti és pont–hely konfigurációk egyidejűleg egy virtuális hálózaton?

Igen. A Resource Manager-alapú üzemi modell esetén az átjáróhoz RouteBased (útvonalalapú) VPN-típust kell használni. A klasszikus üzemi modellhez dinamikus átjáróra van szükség. A pont–hely kapcsolat nem támogatott a statikus útválasztású vagy PolicyBased (házirendalapú) VPN-átjárókhoz.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>Konfigurálhatok úgy egy pont–hely ügyfelet, hogy több virtuális hálózathoz csatlakozzon egyszerre?

Nem. A pont-hely típusú ügyfelek csak olyan erőforrásokhoz tudnak csatlakozni, amelyek ugyanazon a virtuális hálózaton találhatók, mint a virtuális hálózati átjáró.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>Milyen átviteli sebességre számíthatok a helyek közötti és a pont–hely kapcsolatok esetében?

Az átviteli sebesség fenntartása nehéz a VPN-alagutakban. Az IPsec és az SSTP erős titkosítást használó VPN-protokoll. Az átviteli sebességet emellett a késés, valamint a helyszín és az internet közötti sávszélesség is korlátozza. Az olyan VPN-átjárók esetében, amelyek csak IKEv2 pont-hely típusú VPN-kapcsolattal rendelkeznek, a várható teljes átviteli sebesség az átjáró termékváltozatától függ. Az átviteli sebességekkel kapcsolatos további információkért lásd: [Az átjárók termékváltozatai](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku).

### <a name="can-i-use-any-software-vpn-client-for-point-to-site-that-supports-sstp-andor-ikev2"></a>Használhatok szoftveres VPN-ügyfelet az SSTP-t és/vagy IKEv2-t támogató pont–hely kapcsolatokhoz?

Nem. Az SSTP esetében csak a Windows natív VPN-ügyfele, az IKEv2 esetében pedig csak a Mac natív VPN-ügyfele használható. Tekintse át a támogatott ügyfél operációs rendszerek listáját.

### <a name="can-i-access-the-internet-when-i-am-connected-over-p2s-vpn"></a>Elérhető az internet P2S VPN-kapcsolat mellett?

Igen, P2S VPN-kapcsolattal elérhető az internet.

### <a name="does-azure-support-ikev2-vpn-with-windows"></a>Támogatja az Azure az IKEv2 VPN használatát Windows rendszeren?

A felhasználók kapcsolódhatnak az Azure-hoz a Windows beépített VPN-ügyfelével, amely támogatja az IKEv2-t. A következő esetekben azonban nem működik az IKEv2-kapcsolat Windows-eszközökről:

  Ha a felhasználó eszköze nagy mennyiségű megbízható főtanúsítványt tartalmaz, az üzenetek hasznos adatainak mérete túl nagy lesz az IKE-csere során, és ez az IP-réteg töredezettségét okozhatja. A töredékeket az Azure elutasítja, és a kapcsolatot nem sikerült létrehozni. Nehéz meghatározni, milyen mennyiségű tanúsítvány esetén jelenik meg ez a hiba, ezért a Windows-eszközökről indított IKEv2-kapcsolatok működése nem garantált. Ha SSTP-t és az IKEv2-t is konfigurál vegyes (Windows és Mac eszközökből álló) környezetben, a Windows VPN-profil először mindig az IKEv2-alagutat próbálja meg használni. Ha a fent leírt hiba miatt ez nem sikerül, a rendszer áttér az SSTP-alagútra.

### <a name="other-than-windows-and-mac-which-other-platforms-does-azure-support-for-p2s-vpn"></a>A Windows- és Mac-eszközökön kívül mely platformokhoz támogatja még az Azure a P2S VPN-t?

Az Azure kizárólag Windows- és Mac-eszközökhöz támogatja a P2S VPN-t.

### <a name="i-already-have-an-azure-vpn-gateway-deployed-can-i-enabled-radius-andor-ikev2-vpn-on-it"></a>Már rendelkezem üzembe helyezett Azure VPN-átjáróval. Engedélyezhetem ezen a RADIUS-t és/vagy az IKEv2 VPN-t?

Igen, ezeknek az új funkcióknak a működését már üzemelő átjárókon is engedélyezni lehet, mégpedig a PowerShell-len vagy az Azure Portalon keresztül.