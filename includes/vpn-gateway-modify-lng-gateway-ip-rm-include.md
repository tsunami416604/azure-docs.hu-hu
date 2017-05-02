Az átjáró IP-címének módosításához használja a New-AzureRmVirtualNetworkGatewayConnection parancsmagot. A Set parancsmag jelenleg nem támogatja az átjáró IP-címének módosítását.

### <a name="gwipnoconnection"></a>Átjáró IP-címének módosítása – átjárókapcsolat nélkül
Ha módosítani szeretné a kapcsolattal nem rendelkező helyi hálózati átjáró IP-címét, akkor használja az alábbi példát. Ugyanekkor módosíthatja a címelőtagokat is. Ügyeljen arra, hogy a helyi hálózati átjáró meglévő nevét használja az aktuális beállítások felülírásakor. Ha nem ezt teszi, új helyi hálózati átjárót hoz létre a meglévő felülírása helyett.

Használja a következő példát, és cserélje le az értékeket saját értékeire:

```powershell
New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
-Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
-GatewayIpAddress "5.4.3.2" -ResourceGroupName MyRGName
```

### <a name="gwipwithconnection"></a>Átjáró IP-címének módosítása – meglévő átjárókapcsolat
Ha már létezik egy átjárókapcsolat, először el kell távolítania a kapcsolatot. Ha a kapcsolatot eltávolította, módosíthatja az átjáró IP-címét, és létrehozhat egy új kapcsolatot. Ugyanekkor módosíthatja a címelőtagokat is. Ez némi állásidőt jelent a VPN-kapcsolata számára.

> [!IMPORTANT]
> Ne törölje a VPN Gatewayt. Ha törli, akkor újra végre kell hajtania a létrehozásához szükséges lépéseket. Emellett frissítenie kell a helyszíni VPN-eszközt az új VPN-átjáró IP-címével.
> 
> 

1. Távolítsa el a kapcsolatot. A kapcsolat nevét a Get-AzureRmVirtualNetworkGatewayConnection parancsmaggal keresheti meg.

  ```powershell
  Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
  -ResourceGroupName MyRGName
  ```
2. Módosítsa a GatewayIpAddress értéket. Ugyanekkor módosíthatja a címelőtagokat is. Ügyeljen arra, hogy a helyi hálózati átjáró meglévő nevét használja az aktuális beállítások felülírásakor. Ha nem ezt teszi, új helyi hálózati átjárót hoz létre a meglévő felülírása helyett.

  ```powershell
  New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
  -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
  -GatewayIpAddress "104.40.81.124" -ResourceGroupName MyRGName
  ```
3. Hozza létre a kapcsolatot. Ebben a példában egy IPsec kapcsolattípust konfigurálunk. A kapcsolat létrehozásakor a konfigurációban meghatározott kapcsolattípust használja. További kapcsolattípusok esetén tekintse meg a [PowerShell-parancsmag](https://msdn.microsoft.com/library/mt603611.aspx) oldalát.  A VirtualNetworkGateway nevét a Get-AzureRmVirtualNetworkGateway parancsmag futtatásával szerezheti be.
   
    Állítsa be a változókat.

  ```powershell
  $local = Get-AzureRMLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
  $vnetgw = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName MyRGName
  ```
   
    Hozza létre a kapcsolatot.

  ```powershell 
  New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName `
  -Location "West US" `
  -VirtualNetworkGateway1 $vnetgw `
  -LocalNetworkGateway2 $local `
  -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
  ```