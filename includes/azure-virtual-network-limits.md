<a name="virtual-networking-limits-classic"></a>Az alábbi korlátozások kizárólag olyan hálózati erőforrásokra érvényesek, amelyek kezelése előfizetésenként, a klasszikus üzemi modellel történik.

| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Virtuális hálózatok |50 |100 |
| Helyi hálózati helyek |20 |kapcsolatfelvétel az ügyfélszolgálattal |
| DNS-kiszolgálók száma virtuális hálózatonként |20 |100 |
| Magánhálózati IP-címek száma virtuális hálózatonként |4096 |4096 |
| Egyidejű TCP-kapcsolatok száma virtuális gépekhez vagy szerepkörpéldányokhoz |500 000 |500 000 |
| Hálózati biztonsági csoportok (NSG) |100 |200 |
| NSG-ben szereplő NSG-szabályok |200 |400 |
| Felhasználó által megadott útvonaltáblák |100 |200 |
| Felhasználó által megadott útvonalak száma útvonaltáblánként |100 |400 |
| Nyilvános IP-címek (dinamikus) |5 |kapcsolatfelvétel az ügyfélszolgálattal |
| Fenntartott nyilvános IP-címek |20 |kapcsolatfelvétel az ügyfélszolgálattal |
| Nyilvános virtuális IP-címek száma üzemelő példányonként |5 |kapcsolatfelvétel az ügyfélszolgálattal |
| Privát virtuális IP-címek (ILB) száma üzemelő példányonként |1 |1 |
| Végponthozzáférés-vezérlési listák (ACL-ek) |50 |50 |

#### <a name="azure-resource-manager-virtual-networking-limits"></a>Hálózatkezelési korlátok – Azure Resource Manager
Az alábbi korlátozások kizárólag olyan hálózati erőforrásokra érvényesek, amelyek kezelése régiónként és előfizetésenként, az Azure Resource Managerrel történik.

| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Virtuális hálózatok |50 |500 |
| Alhálózatok száma virtuális hálózatonként |1,000 |kapcsolatfelvétel az ügyfélszolgálattal |
| DNS-kiszolgálók száma virtuális hálózatonként |9 |25 |
| Magánhálózati IP-címek száma virtuális hálózatonként |4096 |8192 |
| Magánhálózati IP-címek száma hálózati adapterenként |50 |kapcsolatfelvétel az ügyfélszolgálattal |
| Egyidejű TCP-kapcsolatok száma virtuális gépekhez vagy szerepkörpéldányokhoz |500 000 |500 000 |
| Hálózati adapterek (NIC) |300 |10000 |
| Hálózati biztonsági csoportok (NSG) |100 |400 |
| NSG-ben szereplő NSG-szabályok |200 |500 |
| Felhasználó által megadott útvonaltáblák |100 |200 |
| Felhasználó által megadott útvonalak száma útvonaltáblánként |100 |400 |
| Nyilvános IP-címek (dinamikus) |60 |kapcsolatfelvétel az ügyfélszolgálattal |
| Nyilvános IP-címek (statikus) |20 |kapcsolatfelvétel az ügyfélszolgálattal |
| Terheléselosztók (belső és internetre irányuló) |100 |kapcsolatfelvétel az ügyfélszolgálattal |
| Terheléselosztási szabályok száma terheléselosztónként |150 |150 |
| Nyilvános előtéri IP-címek száma terheléselosztónként |10 |30 |
| Privát előtéri IP-címek száma terheléselosztónként |10 |kapcsolatfelvétel az ügyfélszolgálattal |
| Virtuális hálózati társviszony-létesítések száma virtuális hálózatonként |10 |50 |
| Pont–hely kapcsolati főtanúsítványok száma VPN Gateway-példányonként |20 |20 |
| Másodlagos IP-konfigurációk száma virtuális hálózatonként |1000 |kapcsolatfelvétel az ügyfélszolgálattal |

Ha meg szeretné növelni a korlátozások alapértelmezett értékeit, [lépjen kapcsolatba az ügyfélszolgálattal](../articles/azure-supportability/resource-manager-core-quotas-request.md ).

