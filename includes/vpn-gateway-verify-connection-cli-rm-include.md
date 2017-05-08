Az [az network vpn-connection show](/cli/azure/network/vpn-connection#show) paranccsal ellenőrizheti, sikeres volt-e a kapcsolat. Konfigurálja az értékeket a sajátjainak megfelelően. A példában „--name” a létrehozott és tesztelni kívánt kapcsolat nevére utal.

```azurecli
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```

Ha a kapcsolat létrehozás alatt áll, a kapcsolat állapota „Connecting”. Ha a kapcsolat létrejött, az állapot „Connected” értékűre változik, és megtekintheti a bejövő és kimenő bájtokat.