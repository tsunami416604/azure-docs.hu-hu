# Áttekintés
## [Virtuális hálózatok](virtual-networks-overview.md)
## [Hálózati biztonsági csoportok](virtual-networks-nsg.md)
## [Felhasználó által megadott útvonalak és IP-továbbítás](virtual-networks-udr-overview.md)
## IP-címzés
### [Resource Manager](virtual-network-ip-addresses-overview-arm.md)
### [Klasszikus](virtual-network-ip-addresses-overview-classic.md)
## [Társviszony létesítése virtuális hálózatok között](virtual-network-peering-overview.md)
## Virtual machines (Virtuális gépek)
### [Hálózati illesztők](virtual-network-network-interface-overview.md)
### [Névfeloldás](virtual-networks-name-resolution-for-vms-and-role-instances.md)
## [Az üzletmenet folytonossága](virtual-network-disaster-recovery-guidance.md)
## [Díjszabás](https://azure.microsoft.com/pricing/details/virtual-network)

# Első lépések
## [Virtuális hálózat létrehozása](virtual-networks-create-vnet-arm-pportal.md)
## [Virtuális gép üzembe helyezése virtuális hálózaton](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

# Útmutató
## Tervezés és kialakítás
### [Virtuális hálózatok](virtual-network-vnet-plan-design-arm.md)
### [Hálózati biztonsági csoportok](virtual-networks-nsg.md)

## Üzembe helyezés
### Virtuális hálózatok
#### [Portál](virtual-networks-create-vnet-arm-pportal.md)
#### [PowerShell](virtual-networks-create-vnet-arm-ps.md)
#### [Parancssori felület](virtual-networks-create-vnet-arm-cli.md)
#### [Sablon](virtual-networks-create-vnet-arm-template-click.md)
#### [Portál (klasszikus)](virtual-networks-create-vnet-classic-pportal.md)
#### [PowerShell (klasszikus)](virtual-networks-create-vnet-classic-netcfg-ps.md)
#### [Parancssori felület (klasszikus)](virtual-networks-create-vnet-classic-cli.md)

### Network security groups (Hálózati biztonsági csoportok)
#### [Portál](virtual-networks-create-nsg-arm-portal.md)
#### [PowerShell](virtual-networks-create-nsg-arm-ps.md)
#### [Parancssori felület](virtual-networks-create-nsg-arm-cli.md)
#### [Sablon](virtual-networks-create-nsg-arm-template.md)
#### [PowerShell (klasszikus)](virtual-networks-create-nsg-classic-ps.md)
#### [Parancssori felület (klasszikus)](virtual-networks-create-nsg-classic-cli.md)

### Felhasználó által megadott útvonalak
#### [PowerShell](virtual-network-create-udr-arm-ps.md)
#### [Parancssori felület](virtual-network-create-udr-arm-cli.md)
#### [Sablon](virtual-network-create-udr-arm-template.md)
#### [PowerShell (klasszikus)](virtual-network-create-udr-classic-ps.md)
#### [Parancssori felület (klasszikus)](virtual-network-create-udr-classic-cli.md)

### Társviszony létesítése virtuális hálózatok között
#### [Portál](virtual-networks-create-vnetpeering-arm-portal.md)
#### [PowerShell](virtual-networks-create-vnetpeering-arm-ps.md)
#### [Sablon](virtual-networks-create-vnetpeering-arm-template-click.md)

### Virtual machines (Virtuális gépek)

#### Statikus nyilvános IP-címek
##### [Portál](virtual-network-deploy-static-pip-arm-portal.md)
##### [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
##### [Parancssori felület](virtual-network-deploy-static-pip-arm-cli.md)
##### [Sablon](virtual-network-deploy-static-pip-arm-template.md)
##### [PowerShell (klasszikus)](virtual-networks-reserved-public-ip.md)

#### Statikus magánhálózati IP-címek
##### [Portál](virtual-networks-static-private-ip-arm-pportal.md)
##### [PowerShell](virtual-networks-static-private-ip-arm-ps.md)
##### [Parancssori felület](virtual-networks-static-private-ip-arm-cli.md)
##### [Portál (klasszikus)](virtual-networks-static-private-ip-classic-pportal.md)
##### [PowerShell (klasszikus)](virtual-networks-static-private-ip-classic-ps.md)
##### [Parancssori felület (klasszikus)](virtual-networks-static-private-ip-classic-cli.md)

#### Több hálózati adapter
##### [PowerShell](virtual-network-deploy-multinic-arm-ps.md)
##### [Parancssori felület](virtual-network-deploy-multinic-arm-cli.md)
##### [Sablon](virtual-network-deploy-multinic-arm-template.md)
##### [PowerShell (klasszikus)](virtual-network-deploy-multinic-classic-ps.md)
##### [Parancssori felület (klasszikus)](virtual-network-deploy-multinic-classic-cli.md)

#### [Több IP-cím](virtual-network-multiple-ip-addresses-powershell.md)

### Kapcsolódási forgatókönyvek
#### [Virtuális hálózatok közötti kapcsolat](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Resource Manager-alapú és klasszikus virtuális hálózat közötti kapcsolat](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Virtuális hálózat és helyszíni hálózat (VPN) közötti kapcsolat](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Virtuális hálózat és helyszíni hálózat (ExpressRoute) közötti kapcsolat](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Magas rendelkezésre állású hibrid hálózati architektúra](../guidance/guidance-hybrid-network-expressroute-vpn-failover.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

### Biztonsági forgatókönyvek
#### [Hálózatok biztonságossá tétele virtuális készülékekkel](virtual-network-scenario-udr-gw-nva.md)
#### [Szegélyhálózat (DMZ) az Azure és az internet között](../guidance/guidance-iaas-ra-secure-vnet-dmz.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Felhőszolgáltatás és hálózati biztonság](../best-practices-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [Egyszerű szegélyhálózat NSG-kkel](virtual-networks-dmz-nsg-asm.md)
##### [Szegélyhálózat tűzfallal és NSG-kkel](virtual-networks-dmz-nsg-fw-asm.md)
##### [Szegélyhálózat tűzfallal, felhasználó által megadott útvonallal (UDR-rel) és NSG-kkel](virtual-networks-dmz-nsg-fw-udr-asm.md)
##### [Mintaalkalmazás](virtual-networks-sample-app.md)

## Kezelés
### Network security groups (Hálózati biztonsági csoportok)
#### [Portál](virtual-network-manage-nsg-arm-portal.md)
#### [PowerShell](virtual-network-manage-nsg-arm-ps.md)
#### [Parancssori felület](virtual-network-manage-nsg-arm-cli.md)
#### [Naplók](virtual-network-nsg-manage-log.md)
#### Hibaelhárítás
##### [Portál](virtual-network-nsg-troubleshoot-portal.md)
##### [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
### Útvonalak hibaelhárítása
#### [Portál](virtual-network-routes-troubleshoot-portal.md)
#### [PowerShell](virtual-network-routes-troubleshoot-powershell.md)
### Virtual machines (Virtuális gépek)
#### [Gazdagépnevek megtekintése és módosítása](virtual-networks-viewing-and-modifying-hostnames.md)
#### [Virtuális gép áthelyezése másik alhálózatra](virtual-networks-move-vm-role-to-subnet.md)

# Referencia
## [PowerShell-parancsmagok (Resource Manager)](https://msdn.microsoft.com/library/mt163510(v=azure.300))
## [PowerShell-parancsmagok (klasszikus)](https://msdn.microsoft.com/library/mt270335(v=azure.300))
## [API-k (Resource Manager)](https://msdn.microsoft.com/library/mt163658.aspx)
## [API-k (klasszikus)](https://msdn.microsoft.com/library/jj157182.aspx)
## [Hálózatkezelési blog](http://azure.microsoft.com/blog/topics/networking)
## [Gyakori kérdések](virtual-networks-faq.md)

# Kapcsolódó
## [Virtuális gépek](https://azure.microsoft.com/documentation/services/virtual-machines)
## [Application Gateway](https://azure.microsoft.com/documentation/services/application-gateway)
## [Azure DNS](https://azure.microsoft.com/documentation/services/dns)
## [Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager)
## [Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer)
## [VPN Gateway](https://azure.microsoft.com/documentation/services/vpn-gateway)
## [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute)



<!--HONumber=Nov16_HO2-->


