### <a name="noconnection"></a>Előtagok módosítása – átjárókapcsolat nélkül

- További címelőtagok felvétele:

  ```powershell
  $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
  Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
  -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
  ```

- Címelőtag eltávolítása:<br>
  Hagyja ki azokat címelőtagokat, amelyekre már nincs szüksége. Ebben a példában a 20.0.0.0/24 előtagra (az előző példából) már nincs szükségünk, ezért frissítjük a helyi hálózati átjárót, kihagyva ezt az előtagot.

  ```powershell
  $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
  Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
  -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')
  ```

### <a name="withconnection"></a>Előtagok módosítása – meglévő átjárókapcsolat
Ha már rendelkezik átjárókapcsolattal, és szeretné felvenni vagy eltávolítani a helyi hálózati átjáróban tárolt IP-címelőtagokat, akkor az alábbi lépéseket kell sorban végrehajtania. Ez némi állásidőt jelent a VPN-kapcsolata számára.

> [!IMPORTANT]
> Ne törölje a VPN Gatewayt. Ha törli, akkor újra végre kell hajtania a létrehozásához szükséges lépéseket. Emellett frissítenie kell a helyszíni VPN-eszközt az új VPN-átjáró IP-címével.
> 
> 

1. Távolítsa el a kapcsolatot.

  ```powershell
  Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName
  ```
2. Módosítsa a helyi hálózati átjáró címelőtagjait.
   
  Állítsa be a LocalNetworkGateway változóját.

  ```powershell
  $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName
  ```
   
  Módosítsa az előtagokat.
   
  ```powershell
  Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
  -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
  ```
3. Hozza létre a kapcsolatot. Ebben a példában egy IPsec kapcsolattípust konfigurálunk. A kapcsolat létrehozásakor a konfigurációban meghatározott kapcsolattípust használja. További kapcsolattípusok esetén tekintse meg a [PowerShell-parancsmag](https://msdn.microsoft.com/library/mt603611.aspx) oldalát.
   
  Állítsa be a VirtualNetworkGateway változóját.

  ```powershell
  $gateway1 = Get-AzureRmVirtualNetworkGateway -Name RMGateway  -ResourceGroupName MyRGName
  ```
   
  Hozza létre a kapcsolatot. Ez a példa a 2. lépésben beállított $local változót használja.

  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
  -ResourceGroupName MyRGName -Location 'West US' `
  -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
  -ConnectionType IPsec `
  -RoutingWeight 10 -SharedKey 'abc123'
  ```