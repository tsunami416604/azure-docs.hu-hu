A Cloud Shellben hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal.

[!INCLUDE [resource group intro text](resource-group.md)]

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *West Europe* helyen. Minden támogatott helyek az App Service megtekintéséhez futtassa a `az appservice list-locations` parancsot.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

Az erőforráscsoportot és az erőforrásokat általában a közelében található régiókban hozhatja létre. 
