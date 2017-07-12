Hozza létre az üzembe helyezési hitelesítő adatokat az [az webapp deployment user set](/cli/azure/webapp/deployment/user#set) paranccsal.

Az FTP és a helyi Git webalkalmazásban történő üzembe helyezéséhez üzembe helyező felhasználóra van szükség. A felhasználónevek és a jelszavak megadása fiókszinten történik. Ezek nem azonosak az Azure-előfizetés hitelesítő adataival.

A következő parancsban cserélje ki az *\<user-name>* és a *\<password>* elemeket az új felhasználónévvel és jelszóval.

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

A felhasználónévnek egyedinek kell lennie. A jelszónak legalább 8 karakterből kell állnia, és a következő három elemből legalább kettőt tartalmaznia kell: betűk, számok, szimbólumok. ` 'Conflict'. Details: 409` hibaüzenet esetén változtassa meg a felhasználónevet. ` 'Bad Request'. Details: 400` hibaüzenet esetén használjon erősebb jelszót.

Az üzembe helyező felhasználót elég egyszer létrehoznia. Minden Azure-beli üzembe helyezéshez használható.

> [!NOTE]
> Jegyezze fel a felhasználónevet és a jelszót. Szüksége lesz rájuk a webalkalmazás későbbi üzembe helyezésekor.
>
>