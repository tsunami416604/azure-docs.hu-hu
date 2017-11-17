A Cloud Shellben hozza létre az üzembehelyezési hitelesítő adatokat az [az webapp deployment user set](/cli/azure/webapp/deployment/user#set) paranccsal. Az FTP és a helyi Git webalkalmazásban történő üzembe helyezéséhez üzembe helyező felhasználóra van szükség. A felhasználónevek és a jelszavak megadása fiókszinten történik. _Ezek nem azonosak az Azure-előfizetés hitelesítő adataival._

Az alábbi példában cserélje le  *\<felhasználónév >* és  *\<jelszó >* (beleértve a szögletes zárójel) új felhasználónevet és jelszót. A felhasználónévnek egyedinek kell lennie. A jelszónak legalább nyolc karakter hosszúságúnak kell lennie, és a következő három elem közül kettőnek szerepelnie kell benne: betűk, számok, szimbólumok. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

` 'Conflict'. Details: 409` hibaüzenet esetén változtassa meg a felhasználónevet. ` 'Bad Request'. Details: 400` hibaüzenet esetén használjon erősebb jelszót.

Ezt az üzembe helyező felhasználót csak egyszer kell létrehoznia, és minden Azure-környezetben használhatja.

> [!NOTE]
> Jegyezze fel a felhasználónevet és a jelszót. Szüksége lesz rájuk a webalkalmazás későbbi üzembe helyezésekor.
>
>