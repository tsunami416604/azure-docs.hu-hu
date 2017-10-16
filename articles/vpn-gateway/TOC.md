# Áttekintés
## [Információk a VPN Gateway-ről](vpn-gateway-about-vpngateways.md)
## [VPN Gateway – gyakori kérdések](vpn-gateway-vpn-faq.md)
## [Előfizetés- és szolgáltatáskorlátok](../azure-subscription-service-limits.md?toc=%2fazure%2fvpn-gateway%2ftoc.json)

# Első lépések
## [A VPN Gateway tervezése és kialakítása](vpn-gateway-plan-design.md)
## [Tudnivalók a VPN Gateway beállításairól](vpn-gateway-about-vpn-gateway-settings.md)
## [Tudnivalók a VPN-eszközökről](vpn-gateway-about-vpn-devices.md)
## [Információ a kriptográfiai követelményekről](vpn-gateway-about-compliance-crypto.md)
## [A BGP és a VPN-átjáró ismertetése](vpn-gateway-bgp-overview.md)
## [Tudnivalók a magas rendelkezésre állású kapcsolatokról](vpn-gateway-highlyavailable.md)
## [A pont–hely kapcsolatokról](point-to-site-about.md)

# Útmutató
## Helyek közötti kapcsolatok konfigurálása
### [Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
### [Azure PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
### [Azure CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
### [(Klasszikus) Azure Portal](vpn-gateway-howto-site-to-site-classic-portal.md)

## Pont–hely kapcsolatok konfigurálása – hitelesítés natív Azure-tanúsítvánnyal
### Pont–hely VPN-kapcsolat konfigurálása
#### [Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
#### [Azure PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
#### [(Klasszikus) Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
### Önaláírt tanúsítványok létrehozása
#### [Azure PowerShell](vpn-gateway-certificates-point-to-site.md)
#### [Makecert](vpn-gateway-certificates-point-to-site-makecert.md)
### [A VPN-ügyfél konfigurációs fájljainak létrehozása és telepítése](point-to-site-vpn-client-configuration-azure-cert.md)
### [Ügyféltanúsítványok telepítése](point-to-site-how-to-vpn-client-install-azure-cert.md)

## Pont–hely kapcsolatok konfigurálása – RADIUS hitelesítés
### Pont–hely VPN-kapcsolat konfigurálása
#### [Azure PowerShell](point-to-site-how-to-radius-ps.md)
### [A VPN-ügyfél konfigurációs fájljainak létrehozása és telepítése](point-to-site-vpn-client-configuration-radius.md)

## Virtuális hálózatok közötti kapcsolatok konfigurálása
### [Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
### [Azure PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
### [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
### [(Klasszikus) Azure Portal](vpn-gateway-howto-vnet-vnet-portal-classic.md)
## Virtuális hálózatok közötti kapcsolat konfigurálása üzemi modellek között
### [Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
### [Azure PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
## Egyidejű helyek közötti és ExpressRoute-kapcsolatok konfigurálása
### [Azure PowerShell](../expressroute/expressroute-howto-coexist-resource-manager.md?toc=%2fazure%2fvpn-gateway%2ftoc.json)
## Több helyek közötti kapcsolat konfigurálása
### [Azure Portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
### [Azure PowerShell (klasszikus)](vpn-gateway-multi-site.md)
## Több házirendalapú VPN-eszköz csatlakoztatása
### [Azure PowerShell](vpn-gateway-connect-multiple-policybased-rm-ps.md)
## IPsec/IKE kapcsolati házirendjeinek konfigurálása
### [Azure PowerShell](vpn-gateway-ipsecikepolicy-rm-powershell.md)
## Magas rendelkezésre állású aktív–aktív kapcsolatok konfigurálása
### [Azure PowerShell](vpn-gateway-activeactive-rm-powershell.md)
## BGP konfigurálása egy VPN-átjáró számára
### [Azure PowerShell](vpn-gateway-bgp-resource-manager-ps.md)
### [Azure CLI](bgp-how-to-cli.md)
## Kényszerített bújtatás konfigurálása
### [Azure PowerShell](vpn-gateway-forced-tunneling-rm.md)
### [Azure PowerShell (klasszikus)](vpn-gateway-about-forced-tunneling.md)
## Helyi hálózati átjáró beállításainak módosítása
### [Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
### [Azure PowerShell](vpn-gateway-modify-local-network-gateway.md)
### [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
## [VPN Gateway-kapcsolat ellenőrzése](vpn-gateway-verify-connection-resource-manager.md)
## [VPN Gateway alaphelyzetbe állítása](vpn-gateway-resetgw-classic.md)
## VPN Gateway törlése
### [Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
### [Azure PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
### [Azure PowerShell (klasszikus)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
## [VPN-átjáró konfigurálása (klasszikus)](vpn-gateway-configure-vpn-gateway-mp.md)
## [Átjáró-termékváltozatok (örökölt)](vpn-gateway-about-skus-legacy.md)
## Külső VPN-eszközök konfigurálása
### [Áttekintés és Azure-konfiguráció](vpn-gateway-3rdparty-device-config-overview.md)
### [Minta: Cisco ASA-eszköz (IKEv2/nincs BGP)](vpn-gateway-3rdparty-device-config-cisco-asa.md)
## Hibaelhárítás
### [VPN teljesítményének érvényesítése VNeten](vpn-gateway-validate-throughput-to-vnet.md)
### [Közösség által javasolt VPN- vagy tűzfaleszköz-beállítások](vpn-gateway-third-party-settings.md)
### [Pont–hely kapcsolati probléma](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
### [A helyek közötti kapcsolat időnként megszakad](vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently.md)
### [A helyek közötti kapcsolat nem hozható létre](vpn-gateway-troubleshoot-site-to-site-cannot-connect.md) 
### [Application Gateway-munkamenet affinitási problémái](https://support.microsoft.com/help/4033827/troubleshooting-azure-application-gateway-session-affinity-issues)
### [VNet- vagy VPN-kapcsolatok konfigurálása és érvényesítése](https://support.microsoft.com/help/4032151/configuring-and-validating-vnet-or-vpn-connections)

# Referencia
## [Azure PowerShell](/powershell/module/azurerm.network/?view=azurermps-4.0.0#vpn)
## [Azure PowerShell (klasszikus)](/powershell/module/azure/?view=azuresmps-3.7.0#networking)
## [REST](/rest/api/network/virtualnetworkgateways)
## [REST (klasszikus)](https://msdn.microsoft.com/library/jj154113)
## [Azure CLI](/cli/azure/network/vnet-gateway)

# Kapcsolódó
## [Virtuális hálózat](/azure/virtual-network/)
## [Application Gateway](/azure/application-gateway/)
## [Azure DNS](/azure/dns/)
## [Traffic Manager](/azure/traffic-manager/)
## [Load Balancer](/azure/load-balancer/)
## [ExpressRoute](/azure/expressroute/)

# Erőforrások
## [Azure-ütemterv](https://azure.microsoft.com/roadmap/?category=networking)
## [Blog](https://azure.microsoft.com/blog/topics/networking)
## [Fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesVirtualNetwork)
## [Díjszabás](https://azure.microsoft.com/pricing/details/vpn-gateway)
## [Díjkalkulátor](https://azure.microsoft.com/pricing/calculator/)
## [SLA](https://azure.microsoft.com/support/legal/sla)
## [Videók](https://azure.microsoft.com/documentation/videos/index/?services=vpn-gateway)
