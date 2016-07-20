### Előtagok felvétele vagy eltávolítása, ha még nem hozott létre VPN Gateway-kapcsolatot

- Ha további címelőtagokat szeretne **felvenni** egy Ön által létrehozott hálózati átjáróra, de az még nem rendelkezik VPN Gateway-kapcsolattal, akkor használja a lenti példát.

        $local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')


- Ha szeretne **eltávolítani** egy címelőtagot egy hálózati átjáróból, amely nem rendelkezik VPN-kapcsolattal, akkor használja az alábbi példát. Hagyja ki azokat címelőtagokat, amelyekre már nincs szüksége. Ebben a példában a 20.0.0.0/24 előtagra (az előző példából) már nincs szükségünk, ezért frissítjük a helyi hálózati átjárót, és kihagyjuk ezt az előtagot.

        $local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### Előtagok felvétele vagy eltávolítása, ha már létrehozott egy VPN Gateway-kapcsolatot

Ha már létrehozott egy VPN-kapcsolatot, és szeretné felvenni vagy eltávolítani a helyi hálózati átjáróban tárolt IP-címelőtagokat, akkor az alábbi lépéseket kell sorban végrehajtania. Ez némi állásidőt jelent majd a VPN-kapcsolata számára.

>[AZURE.IMPORTANT] Ne törölje a VPN Gatewayt. Ha törli, akkor újra végre kell hajtania a létrehozásához szükséges lépéseket, valamint a helyszíni útválasztót is újra kell konfigurálnia az új beállításokkal.
 
1. Távolítsa el az IPsec-kapcsolatot. 
2. Módosítsa a helyi hálózati átjáró előtagjait. 
3. Hozzon létre egy új IPsec-kapcsolatot. 

A következő mintát útmutatóként használhatja.

    $gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
    $local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

    Remove-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg

    $local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
    Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
    
    New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'



<!--HONumber=Jun16_HO2-->


