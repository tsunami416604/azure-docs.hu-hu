> [!div class="op_single_selector"]
> * [Portál](../articles/virtual-network/virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](../articles/virtual-network/virtual-network-multiple-ip-addresses-powershell.md)
> * [CLI 2.0](../articles/virtual-network/virtual-network-multiple-ip-addresses-cli.md)
> * [CLI 1.0](../articles/virtual-network/virtual-network-multiple-ip-addresses-cli-nodejs.md)
> * [Sablon](../articles/virtual-network/virtual-network-multiple-ip-addresses-template.md)
>

Egy Azure virtuális géphez (VM) egy vagy több hálózati adapter (NIC) van csatlakoztatva. Bármelyik hálózati adapter rendelkezhet egy vagy több hozzárendelt statikus vagy dinamikus nyilvános vagy magánhálózati IP-címmel. Több IP-cím hozzárendelése egy virtuális géphez a következő képességeket teszi elérhetővé:

* Több webhely vagy szolgáltatás üzemeltetése különböző IP-címekkel és SSL-tanúsítványokkal egyetlen kiszolgálón.
* Használat hálózati virtuális készülékként, például tűzfalként vagy terheléselosztóként.
* Bármely hálózati adapter bármely magánhálózati IP-címének hozzáadása egy Azure Load Balancer háttérkészlethez. Korábban csak az elsődleges hálózati adapter elsődleges IP-címét lehetett hozzáadni a háttérkészlethez. Ha többet szeretne megtudni több IP-konfiguráció terheléselosztásáról, olvassa el a [több IP-konfiguráció terheléselosztásával kapcsolatos](../articles/load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json) cikket.

A virtuális géphez csatolt minden hálózati adapter egy vagy több hozzárendelt IP-konfigurációval rendelkezik. Az egyes konfigurációkhoz egy statikus vagy dinamikus magánhálózati IP-cím van hozzárendelve. Az egyes konfigurációkhoz egy nyilvános IP-cím erőforrás is hozzárendelhető. Egy nyilvános IP-cím erőforráshoz egy dinamikus vagy statikus nyilvános IP-cím van hozzárendelve. Ha többet szeretne megtudni az Azure-ban használt IP-címekről, olvassa el az [IP-címek az Azure-ban](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md) című cikket. 

Minden hálózati adapterhez legfeljebb 250 magánhálózati IP-címet rendelhet hozzá. Bár minden hálózati adapterhez több nyilvános IP-címet is hozzárendelhet, az Azure-előfizetésben használható nyilvános IP-címek száma korlátozva van. A részletekért tekintse meg az [Azure korlátairól](../articles/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) szóló cikket.
