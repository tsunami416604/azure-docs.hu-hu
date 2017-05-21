1. Kattintson az Azure Portal bal felső sarkában található **Új** gombra.

1. Kattintson a **Számítás** > **Függvényalkalmazás** elemre, és válassza az **Előfizetés** elemet. Ezután használja a táblázatban megadott függvényalkalmazás-beállításokat.

    ![Függvényalkalmazás létrehozása az Azure Portalon](./media/functions-create-function-app-portal/function-app-create-flow.png)

    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Alkalmazás neve** | Globálisan egyedi név | Az új függvényalkalmazást azonosító név. | 
    | **[Erőforráscsoport](../articles/azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Az új erőforráscsoport neve, amelyben létrehozza a függvényalkalmazást. | 
    | **[Szolgáltatási csomag](../articles/azure-functions/functions-scale.md)** |   Használatalapú csomag | Szolgáltatási csomag, amely meghatározza az erőforrások lefoglalását a függvényalkalmazáshoz. Az alapértelmezett **használatalapú csomagban** az erőforrások hozzáadása dinamikusan történik a függvények igényeinek megfelelően. Csak a függvények futási idejéért kell fizetnie.   |
    | **Hely** | Nyugat-Európa | Válasszon egy helyet a közelben vagy a függvények által elérendő más szolgáltatások közelében. |
    | **[Tárfiók](../articles/storage/storage-create-storage-account.md#create-a-storage-account)** |  Globálisan egyedi név |  A függvényalkalmazás által használt új tárfiók neve. Meglévő fiókot is használhat. |

1. Kattintson a **Létrehozás** elemre az új függvényalkalmazás létrehozásához és üzembe helyezéséhez.