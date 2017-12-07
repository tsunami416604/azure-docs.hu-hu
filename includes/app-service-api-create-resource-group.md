Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal.

[!INCLUDE [resource group intro text](resource-group.md)]

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *westeurope* helyen.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

Az elérhető helyek megtekintéséhez futtassa az `az appservice list-locations` parancsot. Az erőforrásokat általában a közelében található régiókban hozhatja létre.
