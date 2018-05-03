Az Azure CLI használatával megszerezheti az API-alkalmazás távoli üzembe helyezési URL-címét. A következő parancsban az *\<app_name>* helyett használja a webalkalmazás nevét.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

A Git helyi üzemelő példányának konfigurálásával végezhet leküldést a távoli mappába.

```bash
git remote add azure <URI from previous step>
```

A távoli Azure-mappához történő küldéssel helyezze üzembe az alkalmazást. A rendszer rákérdez az előzőleg, az üzembe helyező felhasználó létrehozásakor létrehozott jelszóra. Ügyeljen arra, hogy a gyors útmutató korábbi szakaszában létrehozott jelszót adja meg, ne azt, amelyet az Azure Portalra való bejelentkezéshez használ.

```bash
git push azure master
```
