Létre kell hoznia egy VNet és egy átjáró-alhálózatot, először a következő tevékenységek használata előtt.

> [!NOTE]
> Ezek a példák nem vonatkoznak a S2S/ExpressRoute konfigurációk lehet.
> Működő coexist konfigurációban átjárókkal kapcsolatos további információkért lásd: [vizsgálatát a kísérő kapcsolatok konfigurálása](../articles/expressroute/expressroute-howto-coexist-classic.md#gw)

## <a name="add-a-gateway"></a>Átjáró hozzáadása

Az alábbi parancs segítségével hozzon létre. Ne felejtse el behelyettesíteni bármely értékeket a saját.

```powershell
New-AzureVNetGateway -VNetName "MyAzureVNET" -GatewayName "ERGateway" -GatewayType DynamicRouting -GatewaySKU  Standard
```

## <a name="verify-the-gateway-was-created"></a>Ellenőrizze az átjáró létrejött-e

Az alábbi parancs segítségével győződjön meg arról, hogy létrejött-e az átjáró. Ez a parancs is lekéri az átjáró azonosítója, amely a többi művelet van szüksége.

```powershell
Get-AzureVNetGateway
```

## <a name="resize-a-gateway"></a>Átjáró méretezése

A több [Gateway SKU-n](../articles/expressroute/expressroute-about-virtual-network-gateways.md). A következő paranccsal átjáró-Termékváltozat bármikor módosíthatja.

> [!IMPORTANT]
> Ez a parancs UltraPerformance átjáró nem működik. Ha módosítani szeretné az átjárót egy UltraPerformance átjárót, először távolítsa el a meglévő ExpressRoute-átjárót, és ezután hozzon létre újat UltraPerformance. Az átjáró egy UltraPerformance átjáró használni, először távolítsa el a UltraPerformance átjáró, és ezután hozzon létre újat. 
>
>

```powershell
Resize-AzureVNetGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="remove-a-gateway"></a>Átjáró eltávolítása

Az alábbi parancs segítségével átjáró eltávolítása

```powershell
Remove-AzureVnetGateway -GatewayId <Gateway ID>
```