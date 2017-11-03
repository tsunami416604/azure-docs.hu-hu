A helyi terminálablakban adjon hozzá egy távoli Azure-mappát a helyi Git-tárházhoz. Az Azure távoli lett létrehozva a [webalkalmazás létrehozása](#create-a-web-app).

```bash
git remote add azure <URI from previous step>
```

A távoli Azure-mappához történő küldéssel helyezze üzembe az alkalmazást a következő paranccsal. Amikor a rendszer jelszót kér, ügyeljen arra, hogy az [Üzembe helyező felhasználó konfigurálása](#configure-a-deployment-user) szakaszban létrehozott jelszót adja meg, ne azt, amelyet az Azure Portalra való bejelentkezéshez használ.

```bash
git push azure master
```

A fenti parancs a következő példához hasonló információkat jelenít meg:
