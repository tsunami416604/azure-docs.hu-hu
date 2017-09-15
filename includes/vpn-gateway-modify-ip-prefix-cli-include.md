### <a name="noconnection"></a>Helyi hálózati átjáró IP-címelőtagjainak módosítása – nincs átjárókapcsolat

Ha nincs átjárókapcsolata, és szeretne hozzáadni vagy eltávolítani IP-címelőtagokat, használja ugyanazt a parancsot, amellyel létrehozza a helyi hálózati átjárót: [az network local-gateway create](https://docs.microsoft.com/cli/azure/network/local-gateway#az_network_local_gateway_create). Ezzel a paranccsal emellett a VPN-eszközhöz tartozó átjárói IP-címet is frissítheti. Az aktuális beállítások felülírásakor a helyi hálózati átjáró meglévő nevét használja. Ha más nevet használ, új helyi hálózati átjárót hoz létre a meglévő felülírása helyett.

Minden módosításkor az előtagok teljes listáját meg kell adnia, nem csak azokat, amelyeket módosítani kíván. Csak a megtartandó előtagokat adja meg. Ebben az esetben a 10.0.0.0/24 és a 20.0.0.0/24 előtagokat.

```azurecli
az network local-gateway create --gateway-ip-address 23.99.221.164 --name Site2 --connection-name TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
```

### <a name="withconnection"></a>Helyi hálózati átjáró IP-címelőtagjainak módosítása – létező átjárókapcsolat

Ha van átjárókapcsolata és szeretne hozzáadni vagy eltávolítani IP-címelőtagokat, frissítheti az előtagokat az [az network local-gateway update](https://docs.microsoft.com/cli/azure/network/local-gateway#az_network_local_gateway_update) parancs használatával. Ez némi állásidőt jelent a VPN-kapcsolata számára. Mikor módosítja az IP-címelőtagokat, nem kell törölnie a VPN-átjárót.

Minden módosításkor az előtagok teljes listáját meg kell adnia, nem csak azokat, amelyeket módosítani kíván. Ebben a példában a 10.0.0.0/24 és a 20.0.0.0/24 már léteznek, A 30.0.0.0/24 és a 40.0.0.0/24 előtagokat most adjuk hozzá, és frissítéskor megadjuk mind a 4 előtagot.

```azurecli
az network local-gateway update --local-address-prefixes 10.0.0.0/24 20.0.0.0/24 30.0.0.0/24 40.0.0.0/24 --name VNet1toSite2 --connection-name TestRG1
```