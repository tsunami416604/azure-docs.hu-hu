# Áttekintés
## [Virtuális hálózatok](virtual-networks-overview.md)
## [Felhasználó által megadott útvonalak és IP-továbbítás](virtual-networks-udr-overview.md)
## [Társviszony létesítése virtuális hálózatok között](virtual-network-peering-overview.md)
## [Az üzletmenet folytonossága](virtual-network-disaster-recovery-guidance.md)
## [Gyakori kérdések](virtual-networks-faq.md)
## IP-címzés
### [Resource Manager](virtual-network-ip-addresses-overview-arm.md)
### [Klasszikus](virtual-network-ip-addresses-overview-classic.md)

# Első lépések
## [Az első virtuális hálózat létrehozása](virtual-network-get-started-vnet-subnet.md)

# Útmutató
## Tervezés és kialakítás
### [Virtuális hálózatok](virtual-network-vnet-plan-design-arm.md)
### [Hálózati biztonsági csoportok](virtual-networks-nsg.md)

## Üzembe helyezés
### [Virtuális hálózatok](virtual-networks-create-vnet-arm-pportal.md)
#### [PowerShell](virtual-networks-create-vnet-arm-ps.md)
#### [Parancssori felület](virtual-networks-create-vnet-arm-cli.md)
#### [Sablon](virtual-networks-create-vnet-arm-template-click.md)
#### [Portál (klasszikus)](virtual-networks-create-vnet-classic-pportal.md)
#### [PowerShell (klasszikus)](virtual-networks-create-vnet-classic-netcfg-ps.md)
#### [Parancssori felület (klasszikus)](virtual-networks-create-vnet-classic-cli.md)

### Network security groups (Hálózati biztonsági csoportok)
#### [Portál](virtual-networks-create-nsg-arm-pportal.md)
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

### [Társviszony létesítése virtuális hálózatok között](virtual-network-create-peering.md)

### Virtual machines (Virtuális gépek)
#### Statikus nyilvános IP-címmel rendelkező virtuális gép létrehozása
##### [Portal](virtual-network-deploy-static-pip-arm-portal.md)
##### [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
##### [Parancssori felület](virtual-network-deploy-static-pip-arm-cli.md)
##### [Sablon](virtual-network-deploy-static-pip-arm-template.md)
##### [PowerShell (klasszikus)](virtual-networks-reserved-public-ip.md)

#### Statikus magánhálózati IP-címmel rendelkező virtuális gép létrehozása
##### [Portal](virtual-networks-static-private-ip-arm-pportal.md)
##### [PowerShell](virtual-networks-static-private-ip-arm-ps.md)
##### [Parancssori felület](virtual-networks-static-private-ip-arm-cli.md)
##### [Portál (klasszikus)](virtual-networks-static-private-ip-classic-pportal.md)
##### [PowerShell (klasszikus)](virtual-networks-static-private-ip-classic-ps.md)
##### [Parancssori felület (klasszikus)](virtual-networks-static-private-ip-classic-cli.md)

#### Több hálózati adapterrel rendelkező virtuális gép létrehozása
##### [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [Parancssori felület](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [PowerShell (klasszikus)](virtual-network-deploy-multinic-classic-ps.md)
##### [Parancssori felület (klasszikus)](virtual-network-deploy-multinic-classic-cli.md)

#### Több IP-címmel rendelkező virtuális gép létrehozása
##### [Azure Portal](virtual-network-multiple-ip-addresses-portal.md)
##### [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
##### [Parancssori felület](virtual-network-multiple-ip-addresses-cli.md)
##### [Sablon](virtual-network-multiple-ip-addresses-template.md)

#### [Gyorsított hálózatkezeléssel rendelkező virtuális gép létrehozása](virtual-network-create-vm-accelerated-networking.md)

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

## Konfigurálás

### Hozzáférés-vezérlési listák
#### [Klasszikus portál](virtual-networks-acl.md)
#### [PowerShell](virtual-networks-acl-powershell.md)
### Virtual machines (Virtuális gépek)
#### [Hálózati adapterek hozzáadása vagy eltávolítása](virtual-network-network-interface-vm.md)
#### [A virtuális gépek és felhőszolgáltatások névfeloldása](virtual-networks-name-resolution-for-vms-and-role-instances.md)
#### [Hálózati teljesítmény optimalizálása](virtual-network-optimize-network-bandwidth.md)
#### [Gazdagépnevek megtekintése és módosítása](virtual-networks-viewing-and-modifying-hostnames.md)

## Kezelés
### [Virtuális hálózatok](virtual-network-manage-network.md)
#### [Alhálózatok](virtual-network-manage-subnet.md)
#### [Társviszonyok](virtual-network-manage-peering.md)
### Network security groups (Hálózati biztonsági csoportok)
#### [Portál](virtual-network-manage-nsg-arm-portal.md)
#### [PowerShell](virtual-network-manage-nsg-arm-ps.md)
#### [Parancssori felület](virtual-network-manage-nsg-arm-cli.md)
#### [Naplók](virtual-network-nsg-manage-log.md)
### Hálózati adapterek (NIC-k)
#### [Hálózati adapterek létrehozása, módosítása vagy törlése](virtual-network-network-interface.md)
#### [IP-címek hozzáadása, szerkesztése vagy eltávolítása](virtual-network-network-interface-addresses.md)
### Virtual machines (Virtuális gépek)
#### [Virtuális gép áthelyezése másik alhálózatra](virtual-networks-move-vm-role-to-subnet.md)
### [Nyilvános IP-címek](virtual-network-public-ip-address.md)

## Hibaelhárítás
### Network security groups (Hálózati biztonsági csoportok)
#### [Portál](virtual-network-nsg-troubleshoot-portal.md)
#### [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
### Útvonalak
#### [Portál](virtual-network-routes-troubleshoot-portal.md)
#### [PowerShell](virtual-network-routes-troubleshoot-powershell.md)
### [Az átviteli sebesség tesztelése](virtual-network-bandwidth-testing.md)

# Referencia
## [PowerShell (Resource Manager)](/powershell/module/azurerm.network)
## [PowerShell (klasszikus)](/powershell/module/azure/?view=azuresmps-3.7.0)
## [Azure CLI](/cli/azure/network)
## [Java](/java/api/)
## [REST (Resource Manager)](https://msdn.microsoft.com/library/mt163658.aspx)
## [REST (klasszikus)](https://msdn.microsoft.com/library/jj157182.aspx)


# Kapcsolódó
## [Virtual Machines](/azure/virtual-machines/)
## [Application Gateway](/azure/application-gateway/)
## [Azure DNS](/azure/dns/)
## [Traffic Manager](/azure/traffic-manager/)
## [Load Balancer](/azure/load-balancer/)
## [VPN Gateway](/azure/vpn-gateway/)
## [ExpressRoute](/azure/expressroute/)

# Erőforrások
## [Hálózatkezelési blog](http://azure.microsoft.com/blog/topics/networking)
## [Hálózatkezelési fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesVirtualNetwork)
## [Díjszabás](https://azure.microsoft.com/pricing/details/virtual-network)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-virtual-network)
