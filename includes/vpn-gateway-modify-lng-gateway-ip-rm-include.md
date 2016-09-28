Az átjáró IP-címének módosításához használja a `New-AzureRmVirtualNetworkGatewayConnection` parancsmagot. Amíg a helyi hálózati átjáró neve pontosan megegyezik a meglévő névvel, felülírja a beállításokat. Ekkor a „Set” parancsmag nem támogatja az átjáró IP-címének módosítását.

### <a name="gwipnoconnection"></a>Átjáró IP-címének módosítása – átjárókapcsolat nélkül

Ha frissíteni szeretné a kapcsolattal nem rendelkező helyi hálózati átjáró IP-címét, akkor használja az alábbi példát. Ugyanekkor frissítheti a címelőtagokat is. A megadott beállítások felülírják a meglévő beállításokat. Ügyeljen arra, hogy a helyi hálózati átjáró meglévő nevét használja. Ha nem ezt teszi, új helyi hálózati átjárót hoz létre, és nem írja felül a meglévőt.

Használja a következő példát, és cserélje le az értékeket saját értékekre.

    New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
    -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
    -GatewayIpAddress "5.4.3.2" -ResourceGroupName MyRGName


### <a name="gwipwithconnection"></a>Átjáró IP-címének módosítása – meglévő átjárókapcsolat

Ha már létezik egy átjárókapcsolat, először el kell távolítania a kapcsolatot. Ezután módosíthatja az átjáró IP-címét, és létrehozhat egy új kapcsolatot. Ez némi állásidőt jelent majd a VPN-kapcsolata számára.


>[AZURE.IMPORTANT] Ne törölje a VPN Gatewayt. Ha törli, akkor újra végre kell hajtania a létrehozásához szükséges lépéseket, valamint a helyszíni útválasztót is újra kell konfigurálnia az újonnan létrehozott átjáróhoz rendelt IP-címmel.
 

1. Távolítsa el a kapcsolatot. A kapcsolat nevét a `Get-AzureRmVirtualNetworkGatewayConnection` parancsmaggal keresheti meg.

        Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
        -ResourceGroupName MyRGName

2. Módosítsa a GatewayIpAddress értéket. Szükség esetén ekkor a cím előtagjait is módosíthatja. Vegye figyelembe, hogy ez felülírja a meglévő helyi hálózati átjáró beállításait. A helyi hálózati átjáró meglévő nevét használja a módosításkor, hogy felülírja a beállításokat. Ha nem ezt teszi, új helyi hálózati átjárót hoz létre, és nem módosítja a meglévőt.

        New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
        -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
        -GatewayIpAddress "104.40.81.124" -ResourceGroupName MyRGName

3. Hozza létre a kapcsolatot. Ebben a példában IPsec kapcsolattípust konfigurálunk. A kapcsolat létrehozásakor a konfigurációban meghatározott kapcsolattípust használja. További kapcsolattípusok esetén tekintse meg a [PowerShell-parancsmag](https://msdn.microsoft.com/library/mt603611.aspx) oldalát.  A VirtualNetworkGateway nevét a `Get-AzureRmVirtualNetworkGateway` parancsmag futtatásával szerezheti be.

    Állítsa be a változókat:

        $local = Get-AzureRMLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
        $vnetgw = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName MyRGName

    Hozza létre a kapcsolatot:
    
        New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName `
        -Location "West US" `
        -VirtualNetworkGateway1 $vnetgw `
        -LocalNetworkGateway2 $local `
        -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'



<!--HONumber=Sep16_HO4-->


