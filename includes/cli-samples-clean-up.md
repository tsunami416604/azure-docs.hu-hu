## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az előző lépésekben Azure-erőforrásokat hozott létre egy erőforráscsoportban. Ha ezeket az erőforrásokat kell a jövőben nem valószínű, törölje a csoportot a Felhőhöz rendszerhéj a következő parancs futtatásával:

```azurecli-interactive
az group delete --name myResourceGroup
```

Ez a parancs futtatásához egy percet is igénybe vehet.