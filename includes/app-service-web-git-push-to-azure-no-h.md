A helyi terminálablakban adjon hozzá egy távoli Azure-mappát a helyi Git-tárházhoz. Az Azure távoli lett létrehozva a [webalkalmazás létrehozása](#create-a-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

A távoli Azure-mappához történő küldéssel helyezze üzembe az alkalmazást a következő paranccsal. Amikor a rendszer jelszót kér, ügyeljen arra, hogy az [Üzembe helyező felhasználó konfigurálása](#configure-a-deployment-user) szakaszban létrehozott jelszót adja meg, ne azt, amelyet az Azure Portalra való bejelentkezéshez használ.

```bash
git push azure master
```

Ez a parancs futtatásához néhány percig is eltarthat. Futás közben az alábbi példához hasonló adatait jeleníti meg:
