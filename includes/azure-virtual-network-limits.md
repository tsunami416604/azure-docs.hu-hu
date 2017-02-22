Az alábbi korlátozások kizárólag olyan hálózati erőforrásokra érvényesek, amelyek kezelése előfizetésenként, a klasszikus üzemi modellel történik.

| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Virtuális hálózatok száma előfizetésenként |50 |100 |
| Helyi hálózati helyek száma előfizetésenként |20 |kapcsolatfelvétel az ügyfélszolgálattal |
| DNS-kiszolgálók száma virtuális hálózatonként |20 |100 |
| Magánhálózati IP-címek száma virtuális hálózatonként |4096 |4096 |
| Egyidejű TCP-kapcsolatok száma virtuális gépekhez vagy szerepkörpéldányokhoz |500&000; |500&000; |
| Hálózati biztonsági csoportok (NSG) |100 |200 |
| NSG-ben szereplő NSG-szabályok |200 |400 |
| Felhasználó által megadott útvonaltáblák |100 |200 |
| Felhasználó által megadott útvonalak száma útvonaltáblánként |100 |400 |
| Nyilvános IP-címek (dinamikus) |5 |kapcsolatfelvétel az ügyfélszolgálattal |
| Fenntartott nyilvános IP-címek |20 |kapcsolatfelvétel az ügyfélszolgálattal |
| Nyilvános virtuális IP-címek száma üzemelő példányonként |5 |kapcsolatfelvétel az ügyfélszolgálattal |
| Privát virtuális IP-címek (ILB) száma üzemelő példányonként |1 |1 |
| Végponthozzáférés-vezérlési listák (ACL-ek) |50 |50 |

#### <a name="a-nameazure-resource-manager-virtual-networking-limitsanetworking-limits---azure-resource-manager"></a><a name="azure-resource-manager-virtual-networking-limits"></a>Hálózatkezelési korlátok – Azure Resource Manager
Az alábbi korlátozások kizárólag olyan hálózati erőforrásokra érvényesek, amelyek kezelése régiónként és előfizetésenként, az Azure Resource Managerrel történik.

| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Virtuális hálózatok száma előfizetésenként |50 |500 |
| Alhálózatok száma virtuális hálózatonként |1,000 |kapcsolatfelvétel az ügyfélszolgálattal |
| DNS-kiszolgálók száma virtuális hálózatonként |9 |25 |
| Magánhálózati IP-címek száma virtuális hálózatonként |4096 |4096 |
| Egyidejű TCP-kapcsolatok száma virtuális gépekhez vagy szerepkörpéldányokhoz |500&000; |500&000; |
| Hálózati adapterek (NIC) |300 |10000 |
| Hálózati biztonsági csoportok (NSG) |100 |400 |
| NSG-ben szereplő NSG-szabályok |200 |500 |
| Felhasználó által megadott útvonaltáblák |100 |200 |
| Felhasználó által megadott útvonalak száma útvonaltáblánként |100 |400 |
| Nyilvános IP-címek (dinamikus) |60 |kapcsolatfelvétel az ügyfélszolgálattal |
| Nyilvános IP-címek (statikus) |20 |kapcsolatfelvétel az ügyfélszolgálattal |
| Terheléselosztók (belső és internetre irányuló) |100 |kapcsolatfelvétel az ügyfélszolgálattal |
| Terheléselosztási szabályok száma terheléselosztónként |150 |150 |
| Nyilvános előtéri IP-címek száma terheléselosztónként |10 |kapcsolatfelvétel az ügyfélszolgálattal |
| Privát előtéri IP-címek száma terheléselosztónként |10 |kapcsolatfelvétel az ügyfélszolgálattal |
| Virtuális hálózati társviszony-létesítések száma virtuális hálózatonként |10 |50 |
| Pont–hely kapcsolati főtanúsítványok száma VPN Gateway-példányonként |20 |20 |

Ha meg szeretné növelni a korlátozások alapértelmezett értékeit, lépjen kapcsolatba az ügyfélszolgálattal.



<!--HONumber=Feb17_HO1-->


