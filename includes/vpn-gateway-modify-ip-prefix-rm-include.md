### <a name="a-namenoconnectionahow-to-add-or-remove-prefixes-no-gateway-connection"></a><a name="noconnection"></a>Előtagok hozzáadása vagy eltávolítása – átjárókapcsolat nélkül
* Ha további címelőtagokat szeretne **felvenni** egy Ön által létrehozott hálózati átjáróra, de az még nem rendelkezik átjárókapcsolattal, akkor használja a lenti példát. Az értékeket módosítsa a sajátjaira.
  
        $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
        -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
* Ha szeretne **eltávolítani** egy címelőtagot egy hálózati átjáróból, amely nem rendelkezik VPN-kapcsolattal, akkor használja az alábbi példát. Hagyja ki azokat címelőtagokat, amelyekre már nincs szüksége. Ebben a példában a 20.0.0.0/24 előtagra (az előző példából) már nincs szükségünk, ezért frissítjük a helyi hálózati átjárót, és kihagyjuk ezt az előtagot.
  
        $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
        -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### <a name="a-namewithconnectionahow-to-add-or-remove-prefixes-existing-gateway-connection"></a><a name="withconnection"></a>Előtagok hozzáadása vagy eltávolítása – meglévő átjárókapcsolattal
Ha már létrehozott egy átjárókapcsolatot, és szeretné felvenni vagy eltávolítani a helyi hálózati átjáróban tárolt IP-címelőtagokat, akkor az alábbi lépéseket kell sorban végrehajtania. Ez némi állásidőt jelent majd a VPN-kapcsolata számára. Az előtagok frissítésekor először távolítsa el a kapcsolatot, módosítsa az előtagokat, majd hozzon létre egy új kapcsolatot. Az alábbi példákban található értékeket módosítsa a sajátjaira.

> [!IMPORTANT]
> Ne törölje a VPN Gatewayt. Ha törli, akkor újra végre kell hajtania a létrehozásához szükséges lépéseket, valamint a helyszíni útválasztót is újra kell konfigurálnia az új beállításokkal.
> 
> 

1. Távolítsa el a kapcsolatot.
   
        Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName
2. Módosítsa a helyi hálózati átjáró címelőtagjait.
   
    Állítsa be a LocalNetworkGateway változóját.
   
        $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName
   
    Módosítsa az előtagokat.
   
        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
        -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
3. Hozza létre a kapcsolatot. Ebben a példában IPsec kapcsolattípust konfigurálunk. A kapcsolat létrehozásakor a konfigurációban meghatározott kapcsolattípust használja. További kapcsolattípusok esetén tekintse meg a [PowerShell-parancsmag](https://msdn.microsoft.com/library/mt603611.aspx) oldalát.
   
     Állítsa be a VirtualNetworkGateway változóját.
   
        $gateway1 = Get-AzureRmVirtualNetworkGateway -Name RMGateway  -ResourceGroupName MyRGName
   
    Hozza létre a kapcsolatot. Vegye figyelembe, hogy a példa az előző lépésben beállított $local változót használja.

        New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
        -ResourceGroupName MyRGName -Location 'West US' `
        -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
        -ConnectionType IPsec `
        -RoutingWeight 10 -SharedKey 'abc123'


<!--HONumber=Nov16_HO2-->


