
# Azure Resiliency
## [Az Azure Resiliency szolgáltatáslapja](https://azure.microsoft.com/features/resiliency)
## [Rugalmas alkalmazások tervezése az Azure-hoz](https://docs.microsoft.com/azure/architecture/resiliency/)

# Magas rendelkezésre állás

## [Magas rendelkezésre állás az Azure-alkalmazásokhoz](https://docs.microsoft.com/azure/architecture/resiliency/high-availability-azure-applications)

## Rendelkezésre állási zónák dokumentációja
### [Mik a rendelkezésre állási zónák?](az-overview.md)

### Virtual machines (Virtuális gépek)
#### [Linux rendszerű virtuális gépek létrehozása egy rendelkezésre állási zónában parancssori felület használatával](../virtual-machines/linux/create-cli-availability-zone.md)
#### [Windows rendszerű virtuális gépek létrehozása egy rendelkezésre állási zónában a PowerShell használatával](../virtual-machines/windows/create-powershell-availability-zone.md)
#### [Windows rendszerű virtuális gépek létrehozása egy rendelkezésre állási zónában az Azure Portalon](../virtual-machines/windows/create-portal-availability-zone.md)

### Felügyelt lemezek
#### [Felügyelt lemez hozzáadása egy rendelkezésre állási zónában parancssori felület használatával](../virtual-machines/linux/add-disk.md#use-managed-disks)
#### [Felügyelt lemez hozzáadása egy rendelkezésre állási zónában PowerShell használatával](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)

### Virtuálisgép-méretezési csoportok
#### [Méretezési csoport létrehozása egy rendelkezésre állási zónában](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)

### Load Balancer
#### [Mi az a Load Balancer?](../load-balancer/load-balancer-standard-overview.md)
#### [A Load Balancer Standard és a rendelkezésre állási zónák](../load-balancer/load-balancer-standard-availability-zones.md)

#### [Zónaredundáns nyilvános Standard Load Balancer létrehozása](../load-balancer/load-balancer-get-started-internet-az-portal.md)
##### [Zónaredundáns nyilvános Standard Load Balancer létrehozása (PowerShell)](../load-balancer/load-balancer-get-started-internet-az-powershell.md)
##### [Zónaredundáns nyilvános Standard Load Balancer létrehozása (CLI)](../load-balancer/load-balancer-get-started-internet-az-cli.md)
#### [Zónaszintű nyilvános Standard Load Balancer létrehozása](../load-balancer/load-balancer-get-started-internet-availability-zones-zonal-portal.md)
##### [Zónaszintű nyilvános Standard Load Balancer létrehozása (PowerShell)](../load-balancer/load-balancer-get-started-internet-availability-zones-zonal-powershell.md)
##### [Zónaszintű nyilvános Standard Load Balancer létrehozása (CLI)](../load-balancer/load-balancer-get-started-internet-availability-zones-zonal-cli.md)
#### [Virtuális gépek terheléselosztása rendelkezésre állási zónák között](../load-balancer/load-balancer-standard-public-availability-zones-portal.md)
##### [Virtuális gépek terheléselosztása rendelkezésre állási zónák között az Azure-ral (CLI)](../load-balancer/load-balancer-standard-public-zone-redundant-cli.md)

### [Nyilvános IP-cím](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)

### SQL Database
#### [Rendelkezésre állási zónák és SQL Database](../sql-database/sql-database-high-availability.md#zone-redundant-configuration)

### Storage
#### [Zónaredundáns tárolás](../storage/common/storage-redundancy-zrs.md)

### Event Hubs
#### [Event Hubs georeplikációs vészhelyreállítás](../event-hubs/event-hubs-geo-dr.md#availability-zones-preview)

### Service Bus
#### [Service Bus georeplikációs vészhelyreállítás](../service-bus-messaging/service-bus-geo-dr.md#availability-zones-preview)

### VPN Gateway
#### [Zónaredundáns virtuális hálózati átjáró létrehozása](../vpn-gateway/create-zone-redundant-vnet-gateway.md)

### ExpressRoute
#### [Zónaredundáns virtuális hálózati átjáró létrehozása](../vpn-gateway/create-zone-redundant-vnet-gateway.md)

# Vészhelyreállítás
## [Az Azure Site Recovery használata](https://docs.microsoft.com/azure/site-recovery/)

# Azure Backup
## [Az Azure Backup használata](https://docs.microsoft.com/azure/backup/)

# További források
## [Azure-ütemterv](https://azure.microsoft.com/roadmap/)
## [Azure-régiók](https://azure.microsoft.com/regions/)
