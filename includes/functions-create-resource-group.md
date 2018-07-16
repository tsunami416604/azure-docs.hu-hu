## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat (például a függvényalkalmazásokat, az adatbázisokat és a tárfiókokat).

A következő példában létrehozunk egy `myResourceGroup` nevű erőforráscsoportot.  
Ha nem a Cloud Shellt használja, először be kell jelentkeznie az `az login` használatával.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```
Az erőforráscsoportot és az erőforrásokat általában a közelében található régiókban hozhatja létre. Az összes Azure App Service-csomagot támogató hely megtekintéséhez futtassa az [az appservice list-locations](/cli/azure/appservice#az_appservice_list_locations) parancsot.