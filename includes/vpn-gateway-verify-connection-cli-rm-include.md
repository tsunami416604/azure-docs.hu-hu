Az [az network vpn-connection show](/cli/azure/network/vpn-connection#show) paranccsal ellenőrizheti, sikeres volt-e a kapcsolat. A példában a „--name” a tesztelni kívánt kapcsolat nevére utal. Ha a kapcsolat létrehozás alatt áll, a kapcsolat állapota „Connecting”. Ha a kapcsolat létrejött, az állapot „Connected” értékűre változik.

```azurecli
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```

