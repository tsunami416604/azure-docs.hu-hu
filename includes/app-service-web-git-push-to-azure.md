## <a name="push-to-azure-from-git"></a>Leküldéses üzenet küldése a Gitből az Azure-ra

Adjon hozzá egy távoli Azure-mappát a helyi Git-tárházhoz.

```bash
git remote add azure <URI from previous step>
```

A távoli Azure-mappához történő küldéssel helyezze üzembe az alkalmazást. A rendszer rákérdez az előzőleg, az üzembe helyező felhasználó létrehozásakor létrehozott jelszóra. Ügyeljen arra, hogy az [Üzembe helyező felhasználó konfigurálása](#configure-a-deployment-user) szakaszban létrehozott jelszót adja meg, ne azt, amelyet az Azure Portalra való bejelentkezéshez használ.

```bash
git push azure master
```

A fenti parancs a következő példához hasonló információkat jelenít meg:
