# [A Load Balancer dokumentációja](index.md)

# Áttekintés
## [Mi az a Load Balancer?](load-balancer-overview.md)
## [Mi az a Load Balancer Standard?](load-balancer-standard-overview.md)
## [Nyilvános Load Balancer](load-balancer-internet-overview.md)
## [Belső terheléselosztó](load-balancer-internal-overview.md)
## [A Load Balancer vizsgálatok ismertetése](load-balancer-custom-probe-overview.md)
## [A magas rendelkezésre állású portok ismertetése](load-balancer-ha-ports-overview.md)
## [Azure Resource Manager-támogatás](load-balancer-arm.md)
## [IPv6-támogatás](load-balancer-ipv6-overview.md)
## [Több virtuális IP-cím](load-balancer-multivip-overview.md)
## [Kimenő kapcsolatok áttekintése](load-balancer-outbound-connections.md)

# Első lépések

## [Belső Load Balancer konfigurálása](load-balancer-get-started-ilb-arm-portal.md)
### [Belső Load Balancer konfigurálása (PowerShell)](load-balancer-get-started-ilb-arm-ps.md)
### [Belső Load Balancer konfigurálása (parancssori felület)](load-balancer-get-started-ilb-arm-cli.md)
### [Belső Load Balancer konfigurálása (sablon)](load-balancer-get-started-ilb-arm-template.md)

## [Nyilvános Load Balancer konfigurálása](load-balancer-get-started-internet-portal.md)
### [Nyilvános Load Balancer konfigurálása (PowerShell)](load-balancer-get-started-internet-arm-ps.md)
### [Nyilvános Load Balancer konfigurálása (parancssori felület)](load-balancer-get-started-internet-arm-cli.md)
### [Nyilvános Load Balancer konfigurálása (sablon)](load-balancer-get-started-internet-arm-template.md)

## [Nyilvános Load Balancer létrehozása IPv6-tal](load-balancer-ipv6-internet-ps.md)
### [Nyilvános Load Balancer létrehozása IPv6-tal (parancssori felület)](load-balancer-ipv6-internet-cli.md)
### [Nyilvános Load Balancer létrehozása IPv6-tal (sablon)](load-balancer-ipv6-internet-template.md)

## [Zónaredundáns nyilvános Load Balancer Standard létrehozása](load-balancer-get-started-internet-az-portal.md)
### [Zónaredundáns nyilvános Load Balancer Standard létrehozása (PowerShell)](load-balancer-get-started-internet-az-powershell.md)
### [Zónaredundáns nyilvános Load Balancer Standard létrehozása (parancssori felület)](load-balancer-get-started-internet-az-cli.md)

# Útmutató
## [TCP üresjárati időkorlát konfigurálása Load Balancer](load-balancer-tcp-idle-timeout.md)
## [Elosztási mód konfigurálása a Load Balancer számára](load-balancer-distribution-mode.md)
## [Belső Load Balancer konfigurálása a felhőszolgáltatásokhoz az SQL AlwaysOn-hoz](load-balancer-configure-sqlao.md)
## [Több virtuális IP-cím konfigurálása felhőszolgáltatáshoz](load-balancer-multivip.md)
## [Többféle terheléselosztási szolgáltatás használata](../traffic-manager/traffic-manager-load-balancing-azure.md?toc=%2fazure%2fload-balancer%2ftoc.json)
## [Több IP-konfiguráció használata](load-balancer-multiple-ip.md)
### [Több IP-konfiguráció használata (parancssori felület)](load-balancer-multiple-ip-cli.md)
### [Több IP-konfiguráció használata (PowerShell)](load-balancer-multiple-ip-powershell.md)
## [Naplóelemzés az Azure Load Balancerhez](load-balancer-monitor-log.md)
## [A DHCPv6 konfigurálása Linux rendszerű virtuális gépekhez](load-balancer-ipv6-for-linux.md)
## [Magas rendelkezésre állású portok konfigurálása belső Load Balancerhez](load-balancer-configure-ha-ports.md)

## Hibaelhárítás
### [Azure Load Balancer hibaelhárítása](load-balancer-troubleshoot.md)

## Klasszikus üzemi modellhez kapcsolódó cikkek
### [Belső Load Balancer konfigurálása a Cloud Services szolgáltatáshoz](load-balancer-get-started-ilb-classic-cloud.md)
#### [Belső Load Balancer konfigurálása a Cloud Services szolgáltatáshoz (PowerShell)](load-balancer-get-started-ilb-classic-ps.md)
#### [Belső Load Balancer konfigurálása a Cloud Services szolgáltatáshoz (parancssori felület)](load-balancer-get-started-ilb-classic-cli.md)
### [Nyilvános Load Balancer konfigurálása (klasszikus PowerShell)](load-balancer-get-started-internet-classic-ps.md)
#### [Nyilvános Load Balancer konfigurálása (klasszikus felhő)](load-balancer-get-started-internet-classic-cloud.md)
#### [Nyilvános Load Balancer konfigurálása (klasszikus parancssori felület)](load-balancer-get-started-internet-classic-cli.md)

# Referencia
## [Kódminták](https://azure.microsoft.com/en-us/resources/samples/?service=load-balancer)
## [Azure PowerShell](/powershell/module/azurerm.network)
## [Azure Azure CLI](/cli/azure/network/lb)
## [.NET](/dotnet/api/microsoft.azure.management.network.models)
## [Java](/java/api/com.microsoft.azure.management.network)
## [Node.js](http://azure.github.io/azure-sdk-for-node/azure-arm-network/latest/LoadBalancers.html)
## [Ruby](http://www.rubydoc.info/gems/azure_mgmt_network/Azure/ARM/Network/LoadBalancers)
## [Python](http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.mgmt.network.operations.html#azure.mgmt.network.operations.LoadBalancersOperations)
## [REST](https://msdn.microsoft.com/library/azure/mt163651.aspx)

# Kapcsolódó témakörök
## [Application Gateway](/azure/application-gateway/)
## [Express Route](/azure/expressroute/)
## [Virtuális hálózat](/azure/virtual-network/)
## [VPN Gateway](/azure/vpn-gateway/)
## [Virtuális gép](/azure/virtual-machines/)
## [Traffic Manager](/azure/traffic-manager/)
## [DNS](/azure/dns/)

# További források
## [Azure-ütemterv](https://azure.microsoft.com/roadmap/?category=networking)
## [Díjszabás](https://azure.microsoft.com/pricing/details/load-balancer/)
## [Díjkalkulátor](https://azure.microsoft.com/pricing/calculator/)
## [Szolgáltatási hírek](https://azure.microsoft.com/updates/?product=load-balancer)
