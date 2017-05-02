A következő parancssori felületi paranccsal ellenőrizheti, sikeres volt-e a kapcsolat. Konfigurálja az értékeket a sajátjainak megfelelően. A példában -n a létrehozott és tesztelni kívánt kapcsolat nevére utal.

```azurecli
az network vpn-connection show -n VNet1toSite2 -g TestRG1
```

Ha a kapcsolat létrehozás alatt áll, a kapcsolat állapota „Connecting”. Ha a kapcsolat létrejött, az állapot „Connected” értékűre változik, és megtekintheti a bejövő és kimenő bájtokat.