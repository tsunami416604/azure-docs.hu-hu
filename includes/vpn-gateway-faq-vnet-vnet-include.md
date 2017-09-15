A Virtuális hálózatok közötti kapcsolat – gyakori kérdések szakasz a VPN Gateway-kapcsolatokra vonatkozik. Ha társviszonyt szeretne létesíteni virtuális hálózatok között, lásd: [Társviszony létesítése virtuális hálózatok között](../articles/virtual-network/virtual-network-peering-overview.md)

### <a name="does-azure-charge-for-traffic-between-vnets"></a>Felszámol az Azure díjat a virtuális hálózatok közötti adatforgalomért?

Az azonos régión belüli virtuális hálózatok közötti adatforgalom ingyenes mindkét irányban VPN-átjárókapcsolat használata esetén. A régiókon átívelő virtuális hálózatok közötti kimenő forgalom díjának megállapítása a virtuális hálózatok közötti kimenő adatátviteli tarifák alapján történik a forrásrégiók alapján. A részletekért tekintse meg a [VPN-átjáró díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/vpn-gateway/). Ha VPN-átjáró helyett virtuális hálózatok közötti társviszonnyal csatlakoztatja a virtuális hálózatokat, tekintse meg a [virtuális hálózat díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/virtual-network/).

### <a name="does-vnet-to-vnet-traffic-travel-across-the-internet"></a>A virtuális hálózatok közötti adatforgalom az interneten halad át?

Nem. A virtuális hálózatok közötti adatforgalom a Microsoft Azure gerinchálózatán halad át, nem az interneten.

### <a name="is-vnet-to-vnet-traffic-secure"></a>Biztonságos-e a virtuális hálózatok közötti adatforgalom?

Igen, az adatforgalmat IPsec/IKE-titkosítás védi.

### <a name="do-i-need-a-vpn-device-to-connect-vnets-together"></a>Szükségem van VPN-eszközre a virtuális hálózatok egymáshoz kapcsolásához?

Nem. Az Azure Virtual Networkök összekapcsolása nem igényel VPN-eszközöket, hacsak nem szükséges a létesítmények közötti kapcsolat.

### <a name="do-my-vnets-need-to-be-in-the-same-region"></a>A virtuális hálózataimnak ugyanabban a régióban kell lenniük?

Nem. A virtuális hálózatok lehetnek azonos vagy eltérő Azure-régiókban (helyeken).

### <a name="if-the-vnets-are-not-in-the-same-subscription-do-the-subscriptions-need-to-be-associated-with-the-same-ad-tenant"></a>Ha a virtuális hálózatok nem ugyanabban az előfizetésben szerepelnek, az előfizetéseket társítani kell ugyanazzal az AD-bérlővel?

Nem.

### <a name="can-i-use-vnet-to-vnet-to-connect-virtual-networks-in-separate-azure-instances"></a>Összekapcsolhatok egymással különböző Azure-példányokban található virtuális hálózatokat? 

Nem. A virtuális hálózatok közötti kapcsolat az azonos Azure-példányon belüli virtuális hálózatok csatlakoztatását támogatja. Például nem hozható létre kapcsolat nyilvános Azure- és kínai/német/az Amerikai Egyesült Államok kormánya által használt Azure-példányok között. Ezekben az esetekben érdemes lehet inkább helyek közötti VPN-kapcsolatot használni.

### <a name="can-i-use-vnet-to-vnet-along-with-multi-site-connections"></a>A virtuális hálózatok közötti kapcsolatot használhatom többhelyes kapcsolatokhoz?

Igen. A virtuális hálózati kapcsolat használható többhelyes virtuális VPN-ekkel együtt.

### <a name="how-many-on-premises-sites-and-virtual-networks-can-one-virtual-network-connect-to"></a>Hány helyszíni helyhez és virtuális hálózathoz kapcsolódhat egyetlen virtuális hálózat?

Lásd [Az átjáróra vonatkozó követelmények](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#requirements) táblázatot.

### <a name="can-i-use-vnet-to-vnet-to-connect-vms-or-cloud-services-outside-of-a-vnet"></a>A virtuális hálózatok közötti kapcsolattal csatlakoztathatok a virtuális hálózaton kívüli virtuális gépeket vagy felhőszolgáltatásokat?

Nem. A virtuális hálózatok közötti kapcsolat támogatja a virtuális hálózatok csatlakoztatását, Nem támogatja a nem virtuális hálózatban lévő virtuális gépek és felhőszolgáltatások csatlakoztatását.

### <a name="can-a-cloud-service-or-a-load-balancing-endpoint-span-vnets"></a>A felhőszolgáltatások és a terheléselosztási végpontok átívelhetnek több virtuális hálózaton?

Nem. A felhőszolgáltatás és a terheléselosztási végpont nem ívelhet át több virtuális hálózaton, akkor sem, ha ezek össze vannak kapcsolva.

### <a name="can-i-used-a-policybased-vpn-type-for-vnet-to-vnet-or-multi-site-connections"></a>Használhatok házirendalapú VPN-típust a virtuális hálózatok közötti vagy többhelyes kapcsolatokhoz?

Nem. A virtuális hálózatok közötti és többhelyes kapcsolatokhoz útvonalalapú (korábbi nevén dinamikus útválasztású) VPN-típussal rendelkező Azure VPN Gateway átjárók szükségesek.

### <a name="can-i-connect-a-vnet-with-a-routebased-vpn-type-to-another-vnet-with-a-policybased-vpn-type"></a>Összekapcsolhatok egy RouteBased (útvonalapú) VPN-típussal rendelkező virtuális hálózatot egy házirendalapú VPN-típussal rendelkezővel?

Nem, mindkét virtuális hálózatnak útvonalalapú (korábban dinamikus útválasztású) VPN-t KELL használnia.

### <a name="do-vpn-tunnels-share-bandwidth"></a>A VPN-alagutak osztoznak a sávszélességen?

Igen. A virtuális hálózat minden VPN-alagútja az Azure VPN Gateway átjárón elérhető sávszélességet használja, és azonos VPN-átjáró üzemidőre vonatkozó SLA-t az Azure-ban.

### <a name="are-redundant-tunnels-supported"></a>Támogatottak a redundáns alagutak?

A virtuális hálózatok párjai közötti redundáns alagutak nem támogatottak, amikor a virtuális hálózati átjáró aktív-aktívként van konfigurálva.

### <a name="can-i-have-overlapping-address-spaces-for-vnet-to-vnet-configurations"></a>Lehetnek átfedő címterek a virtuális hálózatok közötti konfigurációkhoz?

Nem. Nem lehetnek átfedő IP-címtartományok.

### <a name="can-there-be-overlapping-address-spaces-among-connected-virtual-networks-and-on-premises-local-sites"></a>Lehetnek-e egymással átfedésben lévő címterek a csatlakoztatott virtuális hálózatok és helyszíni helyek között?

Nem. Nem lehetnek átfedő IP-címtartományok.



