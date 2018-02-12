# Áttekintés
## [Virtuális hálózatok](virtual-networks-overview.md)
## [Útválasztás](virtual-networks-udr-overview.md)
## [Társviszony létesítése virtuális hálózatok között](virtual-network-peering-overview.md)
## [Virtuális hálózati szolgáltatásvégpontok](virtual-network-service-endpoints-overview.md)
## [Virtuális hálózatot az Azure-szolgáltatásokhoz](virtual-network-for-azure-services.md)
## [Biztonság](security-overview.md)
## [Tárolóalapú hálózatkezelés](container-networking.md)
## [Az üzletmenet folytonossága](virtual-network-disaster-recovery-guidance.md)
## [IP-címzés](virtual-network-ip-addresses-overview-arm.md)
## [Védelem DDOS-támadások ellen](ddos-protection-overview.md)
## [GYIK](virtual-networks-faq.md)
## Klasszikus
### [IP-címzés](virtual-network-ip-addresses-overview-classic.md)
### [Hozzáférés-vezérlési listák](virtual-networks-acl.md)

# Első lépések
## [Virtuális hálózat létrehozása – portál](quick-create-portal.md)
## [Virtuális hálózat létrehozása – PowerShell](quick-create-powershell.md)
## [Virtuális hálózat létrehozása – Azure CLI](quick-create-cli.md)

# Útmutató
## Tervezés és kialakítás
### [Virtuális hálózatok](virtual-network-vnet-plan-design-arm.md)
### [Hálózati biztonsági csoportok](virtual-networks-nsg.md)

## Üzembe helyezés
### [Virtuális hálózatok](virtual-networks-create-vnet-arm-pportal.md)
#### [Azure PowerShell](virtual-networks-create-vnet-arm-ps.md)
#### [Azure CLI](virtual-networks-create-vnet-arm-cli.md)
#### [Sablon](virtual-networks-create-vnet-arm-template-click.md)

### Network security groups (Hálózati biztonsági csoportok)
#### [Azure Portal](virtual-networks-create-nsg-arm-pportal.md)
#### [Azure PowerShell](virtual-networks-create-nsg-arm-ps.md)
#### [Azure CLI](virtual-networks-create-nsg-arm-cli.md)
#### [Sablon](virtual-networks-create-nsg-arm-template.md)
#### [Alkalmazás biztonsági csoportjai](create-network-security-group-preview.md)
#### Klasszikus
##### [Azure PowerShell](virtual-networks-create-nsg-classic-ps.md)
##### [Azure CLI 1.0](virtual-networks-create-nsg-classic-cli.md)

### Felhasználó által megadott útvonalak
#### [Azure Portal](create-user-defined-route-portal.md)
#### [Azure PowerShell](virtual-network-create-udr-arm-ps.md)
#### [Azure CLI](virtual-network-create-udr-arm-cli.md)
#### [Sablon](virtual-network-create-udr-arm-template.md)
#### Klasszikus
##### [Azure PowerShell](virtual-network-create-udr-classic-ps.md)
##### [Azure CLI](virtual-network-create-udr-classic-cli.md)

### Társviszony létesítése virtuális hálózatok között
#### [Egyazon üzemi modell – egyazon előfizetés](virtual-network-create-peering.md)
#### [Egyazon üzemi modell – különböző előfizetések](create-peering-different-subscriptions.md)
#### [Különböző üzemi modellek – egyazon előfizetés](create-peering-different-deployment-models.md)
#### [Különböző üzemi modellek – különböző előfizetések](create-peering-different-deployment-models-subscriptions.md)

### [Virtuális hálózati szolgáltatásvégpontok](virtual-network-service-endpoints-configure.md)

### Nyilvános IP-cím – rendelkezésre állási zóna
#### [Azure Portal](create-public-ip-availability-zone-portal.md)
#### [Azure CLI](create-public-ip-availability-zone-cli.md)
#### [PowerShell](create-public-ip-availability-zone-powershell.md)

### Virtual machines (Virtuális gépek)
#### [Virtuális gépek hálózati átviteli sebessége](virtual-machine-network-throughput.md)
#### Statikus nyilvános IP-címmel rendelkező virtuális gép létrehozása
##### [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
##### [Azure PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
##### [Azure CLI](virtual-network-deploy-static-pip-arm-cli.md)
##### [Sablon](virtual-network-deploy-static-pip-arm-template.md)
##### Klasszikus
###### [Azure PowerShell](virtual-networks-reserved-public-ip.md)

#### Virtuális gép létrehozása – Statikus magánhálózati IP-cím
##### [Azure Portal](virtual-networks-static-private-ip-arm-pportal.md)
##### [Azure PowerShell](virtual-networks-static-private-ip-arm-ps.md)
##### [Azure CLI](virtual-networks-static-private-ip-arm-cli.md)
##### Klasszikus
###### [Azure Portal](virtual-networks-static-private-ip-classic-pportal.md)
###### [Azure PowerShell](virtual-networks-static-private-ip-classic-ps.md)
###### [Azure CLI](virtual-networks-static-private-ip-classic-cli.md)

#### Virtuális gép létrehozása – Több hálózati adapter
##### [Azure PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [Azure CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [Sablon](virtual-network-deploy-multinic-arm-template.md)

##### Klasszikus
###### [Azure PowerShell](virtual-network-deploy-multinic-classic-ps.md)
###### [Azure CLI](virtual-network-deploy-multinic-classic-cli.md)

#### Virtuális gép létrehozása – Több IP-cím
##### [Azure Portal](virtual-network-multiple-ip-addresses-portal.md)
##### [Azure PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
##### [Azure CLI](virtual-network-multiple-ip-addresses-cli.md)
##### [Sablon](virtual-network-multiple-ip-addresses-template.md)

#### Virtuális gép létrehozása – Gyorsított hálózatkezelés
##### [Azure PowerShell](create-vm-accelerated-networking-powershell.md)
##### [Azure CLI](create-vm-accelerated-networking-cli.md)

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
##### [Szegélyhálózat létrehozása NSG-kkel](virtual-networks-dmz-nsg.md)
##### [Szegélyhálózat létrehozása NSG-kkel (Klasszikus)](virtual-networks-dmz-nsg-asm.md)
##### [Szegélyhálózat létrehozása tűzfallal és NSG-kkel (Klasszikus)](virtual-networks-dmz-nsg-fw-asm.md)
##### [Szegélyhálózat tűzfallal, felhasználó által megadott útválasztással és NSG-kkel (Klasszikus)](virtual-networks-dmz-nsg-fw-udr-asm.md)

##### [Mintaalkalmazás](virtual-networks-sample-app.md)

### Klasszikus
#### [Virtuális hálózat](create-virtual-network-classic.md)
##### [Azure Portal](virtual-networks-create-vnet-classic-pportal.md)
##### [Azure PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md)
##### [Azure CLI](virtual-networks-create-vnet-classic-cli.md)
#### [DNS-beállítások megadása egy virtuális hálózat konfigurációs fájljában](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)
#### [DNS-beállítások megadása egy szolgáltatáskonfigurációs fájlban](virtual-networks-specifying-dns-settings-in-a-service-configuration-file.md)

## Konfigurálás
### Virtual machines (Virtuális gépek)
#### [Hálózati adapterek hozzáadása vagy eltávolítása](virtual-network-network-interface-vm.md)
#### [A virtuális gépek és felhőszolgáltatások névfeloldása](virtual-networks-name-resolution-for-vms-and-role-instances.md)
#### [Dinamikus DNS használata a gazdagépek regisztrálásához a saját DNS-kiszolgálón](virtual-networks-name-resolution-ddns.md)
#### [Hálózati teljesítmény optimalizálása](virtual-network-optimize-network-bandwidth.md)
#### [Gazdagépnevek megtekintése és módosítása](virtual-networks-viewing-and-modifying-hostnames.md)
#### Klasszikus
##### Statikus IP-címek
###### [PowerShell](virtual-networks-reserved-private-ip.md)
###### [Parancssori felület](virtual-networks-static-private-ip-classic-cli.md)
##### [Példányszintű nyilvános IP-cím](virtual-networks-instance-level-public-ip.md)

### Klasszikus
#### Hozzáférés-vezérlési listák
##### [Azure Portal](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [Azure PowerShell](virtual-networks-acl-powershell.md)

## Kezelés
### [Virtuális hálózatok](virtual-network-manage-network.md)
#### [Alhálózatok](virtual-network-manage-subnet.md)
#### [Társviszonyok](virtual-network-manage-peering.md)
#### Klasszikus
##### [Hálózati konfigurációs fájl](virtual-networks-using-network-configuration-file.md)
##### [Migrálás affinitáscsoportból régióba](virtual-networks-migrate-to-regional-vnet.md)
### Network security groups (Hálózati biztonsági csoportok)
#### [Azure Portal](virtual-network-manage-nsg-arm-portal.md)
#### [Azure PowerShell](virtual-network-manage-nsg-arm-ps.md)
#### [Azure CLI](virtual-network-manage-nsg-arm-cli.md)

#### [Naplók](virtual-network-nsg-manage-log.md)
### [Útválasztási táblázatok](manage-route-table.md)
### Hálózati adapterek (NIC-k)
#### [Hálózati adapterek létrehozása, módosítása vagy törlése](virtual-network-network-interface.md)
#### [IP-címek hozzáadása, szerkesztése vagy eltávolítása](virtual-network-network-interface-addresses.md)
### Virtual machines (Virtuális gépek)
#### [Virtuális gép áthelyezése másik alhálózatra](virtual-networks-move-vm-role-to-subnet.md)
### [Nyilvános IP-címek](virtual-network-public-ip-address.md)
### Védelem DDOS-támadások ellen
#### [Azure Portal](ddos-protection-manage-portal.md)
#### [Azure PowerShell](ddos-protection-manage-ps.md)

## Hibaelhárítás
### Network security groups (Hálózati biztonsági csoportok)
#### [Azure Portal](virtual-network-nsg-troubleshoot-portal.md)
#### [Azure PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
### Útvonalak
#### [Azure Portal](virtual-network-routes-troubleshoot-portal.md)
#### [Azure PowerShell](virtual-network-routes-troubleshoot-powershell.md)
### [Az átviteli sebesség tesztelése](virtual-network-bandwidth-testing.md)
### [Nem lehet törölni a virtuális hálózatokat](virtual-network-troubleshoot-cannot-delete-vnet.md)
### [Virtuális gépek közötti kapcsolódási problémák](virtual-network-troubleshoot-connectivity-problem-between-vms.md)
### [PTR konfigurálása SMTP szalagcím-ellenőrzésre](create-ptr-for-smtp-service.md)

# Referencia
## [Kódminták](https://azure.microsoft.com/en-us/resources/samples/?service=virtual-network)
## [Azure PowerShell (Resource Manager)](/powershell/module/azurerm.network)
## [Azure PowerShell (klasszikus)](/powershell/module/azure/)
## [Azure CLI](/cli/azure/network)
## [Java](/java/api/)
## [REST (Resource Manager)](https://msdn.microsoft.com/library/mt163658.aspx)
## [REST (klasszikus)](https://msdn.microsoft.com/library/jj157182.aspx)


# Kapcsolódó témakörök
## [Virtuális gépek](/azure/virtual-machines/)
## [Application Gateway](/azure/application-gateway/)
## [Azure DNS](/azure/dns/)
## [Traffic Manager](/azure/traffic-manager/)
## [Load Balancer](/azure/load-balancer/)
## [VPN Gateway](/azure/vpn-gateway/)
## [ExpressRoute](/azure/expressroute/)

# További források
## [Azure-ütemterv](https://azure.microsoft.com/roadmap/?category=networking)
## [Hálózatkezelési blog](http://azure.microsoft.com/blog/topics/networking)
## [Hálózatkezelési fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesVirtualNetwork)
## [Díjszabás](https://azure.microsoft.com/pricing/details/virtual-network)
## [Díjkalkulátor](https://azure.microsoft.com/pricing/calculator/)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-virtual-network)
## [Hálózati erőforrás-szolgáltató](resource-groups-networking.md)
