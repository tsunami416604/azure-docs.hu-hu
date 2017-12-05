# Áttekintés
## [Mi az az ExpressRoute?](expressroute-introduction.md)
## [ExpressRoute – Gyakori kérdések](expressroute-faqs.md)
## [Kapcsolati modellek](expressroute-connectivity-models.md)
## [Kapcsolatcsoportok és útválasztási tartományok](expressroute-circuit-peerings.md)
## [Földrajzi egységek és partnerek](expressroute-locations.md)
### [Szolgáltatók hely alapján](expressroute-locations-providers.md)
### [Helyek szolgáltató alapján](expressroute-locations.md)
## [Az ExpressRoute virtuális hálózati átjárói](expressroute-about-virtual-network-gateways.md)

# Első lépések
## [Előfeltételek](expressroute-prerequisites.md)
## [Munkafolyamatok](expressroute-workflows.md)
## [Útválasztási követelmények](expressroute-routing.md)
## [QoS-követelmények](expressroute-qos.md)
## [Információk a kapcsolatcsoportok módosításáról klasszikus üzemi modellből Resource Manager-alapú modellbe](expressroute-move.md)

# Útmutató
## Kapcsolatcsoport létrehozása és módosítása
### [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
### [Azure PowerShell](expressroute-howto-circuit-arm.md)
### [Azure CLI](howto-circuit-cli.md)
## Társhálózati konfiguráció létrehozása és módosítása
### [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
### [Azure PowerShell](expressroute-howto-routing-arm.md)
### [Azure CLI](howto-routing-cli.md)
## Virtuális hálózat összekapcsolása egy ExpressRoute-kapcsolatcsoporttal
### [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
### [Azure PowerShell](expressroute-howto-linkvnet-arm.md)
### [Azure CLI](howto-linkvnet-cli.md)
## Az ExpressRoute virtuális hálózati átjáróinak konfigurálása
### [Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
### [Azure PowerShell](expressroute-howto-add-gateway-resource-manager.md)
## [Párhuzamos ExpressRoute- és párhuzamos telephelyközi kapcsolatok konfigurálása](expressroute-howto-coexist-resource-manager.md)
## Microsoft társviszony-létesítés útvonalszűrőinek konfigurálása
### [Azure Portal](how-to-routefilter-portal.md)
### [Azure PowerShell](how-to-routefilter-powershell.md)
### [Azure CLI](how-to-routefilter-cli.md)
## [Váltás nyilvános társviszony-létesítésről Microsoft társviszony-létesítésre](how-to-move-peering.md)
## [Kapcsolatcsoportok módosítása klasszikusról Resource Manager-alapúra](expressroute-howto-move-arm.md)
## [Kapcsolódó virtuális hálózatok áttelepítése klasszikus üzemi modellből Resource Manager-alapú modellbe](expressroute-migration-classic-resource-manager.md)
## Útválasztó konfigurálása ExpressRoute-hoz
### [Útválasztó konfigurálása](expressroute-config-samples-routing.md)
### [Útválasztó-konfigurációs minták NAT-hoz](expressroute-config-samples-nat.md)
## [Network Performance Monitor for ExpressRoute konfigurálása](how-to-npm.md)
## Klasszikus üzemi modellhez kapcsolódó cikkek
### [Kapcsolatcsoport módosítása](expressroute-howto-circuit-classic.md)
### [Társhálózati konfiguráció létrehozása és módosítása](expressroute-howto-routing-classic.md)
### [Virtuális hálózat összekapcsolása egy ExpressRoute-kapcsolatcsoporttal](expressroute-howto-linkvnet-classic.md)
### [Párhuzamos ExpressRoute- és párhuzamos helyek közötti kapcsolatok konfigurálása](expressroute-howto-coexist-classic.md)
### [Átjáró hozzáadása virtuális hálózathoz](expressroute-howto-add-gateway-classic.md)

## Ajánlott eljárások
### [Hálózati biztonság és felhőszolgáltatások – ajánlott eljárások](../best-practices-network-security.md)
### [Útválasztás optimalizálása](expressroute-optimize-routing.md)
### [Aszimmetrikus útválasztás](expressroute-asymmetric-routing.md)
### [NAT az ExpressRoute-hoz](expressroute-nat.md)

## Hibaelhárítás
### [Az ExpressRoute-kapcsolat ellenőrzése](expressroute-troubleshooting-expressroute-overview.md)
### [Sikertelen kapcsolatcsoport alaphelyzetbe állítása](reset-circuit.md)
### [ARP-táblák lekérése](expressroute-troubleshooting-arp-resource-manager.md)
### [ARP-táblák lekérése (klasszikus)](expressroute-troubleshooting-arp-classic.md)

# Referencia
## [Azure PowerShell](/powershell/module/azurerm.network/?view=azurermps-4.0.0#expressroute)
## [Azure CLI](/cli/azure/network/express-route)
## [REST](https://msdn.microsoft.com/library/azure/mt586720)
## [REST (klasszikus)](https://msdn.microsoft.com/library/azure/dn606310)

# Kapcsolódó
## [Virtual Network](/azure/virtual-network/)
## [VPN Gateway](/azure/vpn-gateway/)
## [Virtual Machines](/azure/virtual-machines/)
## [Load Balancer](/azure/load-balancer/)
## [Traffic Manager](/azure/traffic-manager/)

# Erőforrások
## [Azure-ütemterv](https://azure.microsoft.com/roadmap/?category=networking)
## [Esettanulmányok](https://customers.microsoft.com/Pages/advancedsearch.aspx?mrmcproducts=More%20Products)
## [Hálózatkezelési blog](https://azure.microsoft.com/blog/topics/networking/)
## [Díjszabás](https://azure.microsoft.com/pricing/details/expressroute/)
## [Díjkalkulátor](https://azure.microsoft.com/pricing/calculator/)
## [Szolgáltatási hírek](https://azure.microsoft.com/updates/?product=expressroute)
## [SLA](https://azure.microsoft.com/support/legal/sla/)
## [Előfizetés- és szolgáltatáskorlátok](../azure-subscription-service-limits.md?toc=%2fazure%2fexpressroute%2ftoc.json)
## [Videók](https://azure.microsoft.com/documentation/videos/index/?services=expressroute)
### [Virtuális hálózati átjáró csatlakoztatása egy kapcsolatcsoporthoz](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit/)
### [Az ExpressRoute virtuális hálózati átjáróinak létrehozása](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-virtual-network/)
### [Az ExpressRoute virtuális hálózati átjáróinak létrehozása](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network/)
### [ExpressRoute-kapcsolatcsoport létrehozása](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit/)
### [A hálózati infrastruktúra fejlesztése a csatlakoztathatóság érdekében](https://go.microsoft.com/fwlink/p/?LinkId=615124)
### [Magánhálózati társviszony-létesítés beállítása a kapcsolatcsoportokhoz](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit/)
### [Hibrid partneri viszonyok: Helyszíni forgatókönyvek engedélyezése](https://go.microsoft.com/fwlink/p/?LinkId=615125)
### [Microsoft társviszony-létesítés beállítása a kapcsolatcsoportokhoz](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit/)
### [Nyilvános társviszony-létesítés beállítása a kapcsolatcsoportokhoz](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit/)
