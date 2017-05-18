## <a name="configure-a-deployment-user"></a>Üzembe helyező felhasználó konfigurálása  

Az FTP és a helyi Git esetében üzembe helyező felhasználót kell konfigurálni a kiszolgálón az üzemelő példány hitelesítéséhez.

> [!NOTE]
> Az FTP és a helyi Git WebAppban történő üzembe helyezéséhez üzembe helyező felhasználóra van szükség.
> `username` és `password` fiókszintű. Nem azonosak az Azure-előfizetés hitelesítő adataival.
>

Az üzembe helyező hitelesítő adatok létrehozásához futtassa az [az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) parancsot.

```azurecli
az appservice web deployment user set --user-name <username> --password <password>
```

A felhasználónévnek egyedinek, a jelszónak pedig erősnek kell lennie. ` 'Conflict'. Details: 409` hibaüzenet esetén változtassa meg a felhasználónevet. ` 'Bad Request'. Details: 400` hibaüzenet esetén használjon erősebb jelszót.

Az üzembe helyező felhasználót elég egyszer létrehoznia. Minden Azure-beli üzembe helyezéshez használható.

A felhasználónevet és jelszót jegyezze fel, hiszen szükség lesz rájuk később, az alkalmazás üzembe helyezésekor.