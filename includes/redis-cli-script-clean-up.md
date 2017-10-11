## <a name="clean-up-deployment"></a>Központi telepítés tisztítása 

A parancsfájl-minta futtatása után a következő parancs segítségével távolítsa el az erőforráscsoportot, Azure Redis Cache példány és a kapcsolódó erőforrások az erőforráscsoportban.

```azurecli
az group delete --name contosoGroup
```