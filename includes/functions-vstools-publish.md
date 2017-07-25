A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, és válassza a **Publish** (Közzététel) lehetőséget. Válassza a **Create New** (Új létrehozása) elemet, majd kattintson a **Publish** (Közzététel) lehetőségre. 

![Új függvényalkalmazás közzététele és létrehozása](./media/functions-vstools-publish/functions-vstools-publish-new-function-app.png)

Ha még nem csatlakoztatta a Visual Studiót az Azure-fiókjához, kattintson az **Add an account...** (Fiók hozzáadása...) lehetőségre.  

A **Create App Service** (Alkalmazásszolgáltatás létrehozása) párbeszédpanelen alkalmazza a táblázatban megadott üzemeltetési beállításokat. 

![Az Azure helyi futtatókörnyezete](./media/functions-vstools-publish/functions-vstools-publish.png)

| Beállítás      | Ajánlott érték  | Leírás                                |
| ------------ |  ------- | -------------------------------------------------- |
| **Alkalmazás neve** | Globálisan egyedi név | Az új függvényalkalmazást azonosító egyedi név. |
| **Előfizetés** | Válassza ki az előfizetését | A használandó előfizetés. |
| **[Erőforráscsoport](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  Az erőforráscsoport neve, amelyben létrehozza a függvényalkalmazást. |
| **[App Service-csomag](../articles/azure-functions/functions-scale.md)** | Használatalapú csomag | Győződjön meg róla, hogy új csomag létrehozása esetén a **Felhasználás** lehetőséget választja a **Méret** elemnél.  |
| **[Tárfiók](../articles/storage/storage-create-storage-account.md#create-a-storage-account)** | Globálisan egyedi név | Használjon egy már létező tárfiókot, vagy hozzon létre egy újat.   |

Kattintson a **Létrehozás** elemre, hogy ezekkel a beállításokkal hozzon létre Azure-beli függvényalkalmazást. Miután az üzembe helyezés befejeződött, jegyezze fel a **webhely URL-címe** értékét, amely a függvényalkalmazás címe az Azure-ban. 

![Az Azure helyi futtatókörnyezete](./media/functions-vstools-publish/functions-vstools-publish-profile.png)
