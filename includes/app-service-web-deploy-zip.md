## <a name="deploy-uploaded-zip-file"></a>Feltöltött ZIP-fájl üzembe helyezése

A Cloud Shellben helyezze üzembe a feltöltött ZIP-fájlt a webalkalmazásban az [`az webapp deployment source config-zip`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_zip) paranccsal. Győződjön meg arról, hogy az *\<app_name>* helyett a webalkalmazása neve szerepel.

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResouceGroup --name <app_name> --src clouddrive/myAppFiles.zip
```

Ez a parancs üzembe helyezi a ZIP-fájlban szereplő fájlokat és könyvtárakat az alapértelmezett App Service-alkalmazásmappában (`\home\site\wwwroot`), majd újraindítja az alkalmazást. Ha további egyéni kiépítési folyamatok vannak megadva, azokat is futtatja.
