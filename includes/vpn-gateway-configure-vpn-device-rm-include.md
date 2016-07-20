
A VPN-eszköz konfigurálásához szüksége lesz a virtuális hálózati átjáró nyilvános IP-címére, hogy konfigurálni tudja a helyszíni VPN-eszközt. A konkrét konfigurációs információkért forduljon az eszköz gyártójához, és konfigurálja az eszközt. Az Azure-ral jól használható VPN-eszközökről a [VPN-eszközöket](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md) ismertető cikkben talál további információt.

A virtuális hálózati átjáró IP-címét az alábbi minta alapján keresheti meg a PowerShell használatával:

    Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG

A virtuális hálózati átjáró IP-címét az Azure portálon is megtekintheti. Válassza a **Virtuális hálózati átjárók** elemet, majd kattintson az átjárója nevére.


<!--HONumber=Jun16_HO2-->


