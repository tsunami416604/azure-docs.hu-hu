<a name="virtual-networking-limits-classic"></a>Az alábbi korlátozások kizárólag olyan hálózati erőforrásokra érvényesek, amelyek kezelése előfizetésenként, a klasszikus üzemi modellel történik.

| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Virtuális hálózatok |50 |100 |
| Helyi hálózati helyek |20 |kapcsolatfelvétel az ügyfélszolgálattal |
| DNS-kiszolgálók száma virtuális hálózatonként |20 |100 |
| Magánhálózati IP-címek száma virtuális hálózatonként |4096 |4096 |
| Egyidejű TCP vagy UDP-forgalmat egy hálózati Adapterének virtuális gép vagy szerepkörpéldány |500 000 |500 000 |
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
| Virtuális hálózatok |50 |1000 |
| Alhálózatok száma virtuális hálózatonként |1000 |10000 |
| Virtuális hálózati társviszony virtuális hálózatonként |10 |50 |
| DNS-kiszolgálók száma virtuális hálózatonként |9 |25 |
| Magánhálózati IP-címek száma virtuális hálózatonként |4096 |8192 |
| Magánhálózati IP-címek száma hálózati adapterenként |256 |1024 |
| Egyidejű TCP vagy UDP-forgalmat egy hálózati Adapterének virtuális gép vagy szerepkörpéldány |500 000 |500 000 |
| Hálózati adapterek (NIC) |350 |20000 |
| Hálózati biztonsági csoportok (NSG) |100 |5000 |
| NSG-ben szereplő NSG-szabályok |200 |1000 |
| IP-címek és egy biztonsági csoportot a cél- és a megadott tartományok |2000 |4000 |
| Alkalmazásbiztonsági csoportok |200 |500 |
| Biztonsági csoportok / IP-konfiguráció, egy hálózati adapter |10 |20 |
| IP-konfigurációk alkalmazás biztonsági csoportonként |1000 |4000 |
| Biztonsági csoportok belül a hálózati biztonsági csoport összes biztonsági szabály adható meg |50 |100 |
| Felhasználó által megadott útvonaltáblák |100 |200 |
| Felhasználó által megadott útvonalak száma útvonaltáblánként |100 |400 |
| Nyilvános IP-címek – dinamikus |(Basic) 60 |kapcsolatfelvétel az ügyfélszolgálattal |
| Nyilvános IP-címek – statikus |(Basic) 20 |kapcsolatfelvétel az ügyfélszolgálattal |
| Nyilvános IP-címek – statikus |(Standard) 20 |kapcsolatfelvétel az ügyfélszolgálattal |
| Pont–hely kapcsolati főtanúsítványok száma VPN Gateway-példányonként |20 |20 |

#### <a name="load-balancer"></a>Korlátozza a terheléselosztó

| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Terheléselosztók | 100 | 1000 |
| Basic erőforrásonként szabályok | 150 | 250 |
| Standard erőforrásonként szabályok | 1250 | 1500 |
| IP-konfiguráció / szabályok | 299 |299 |
| Előtérbeli IP-konfigurációk, Basic | 10 | 200 |
| Előtérbeli IP-konfigurációk, a Standard | 10 | 600 |
| A háttérkészlet, Basic | 100, egy rendelkezésre állási csoport | - |
| Háttérkészlet, a Standard | 1000, egyetlen virtuális hálózat | - |
| Magas rendelkezésre ÁLLÁSÚ portok, a Standard | belső előtér / 1 | - |

Ha meg szeretné növelni a korlátozások alapértelmezett értékeit, [lépjen kapcsolatba az ügyfélszolgálattal](../articles/azure-supportability/resource-manager-core-quotas-request.md ).

