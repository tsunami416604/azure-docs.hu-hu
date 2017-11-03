1. Kattintson az Azure Portal bal felső sarkában található **Új** gombra.

1. Kattintson a **számítási** > **Függvényalkalmazásnak**. Ezután használja a táblázatban megadott függvényalkalmazás-beállításokat.

    ![Függvényalkalmazás létrehozása az Azure Portalon](./media/functions-create-function-app-portal/function-app-create-flow.png)

    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Alkalmazás neve** | Globálisan egyedi név | Az új függvényalkalmazást azonosító név. Érvénytelen karakterek vannak `a-z`, `0-9`, és `-`.  | 
    | **Előfizetés** | Az Ön előfizetése | Az előfizetés, amely alatt ez új funkció az alkalmazás létrehozza. | 
    | **[Erőforráscsoport](../articles/azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Az új erőforráscsoport neve, amelyben létrehozza a függvényalkalmazást. | 
    | **[Szolgáltatási csomag](../articles/azure-functions/functions-scale.md)** |   Használatalapú csomag | Szolgáltatási csomag, amely meghatározza az erőforrások lefoglalását a függvényalkalmazáshoz. Az alapértelmezett **használatalapú csomagban** az erőforrások hozzáadása dinamikusan történik a függvények igényeinek megfelelően. Csak a függvények futási idejéért kell fizetnie.   |
    | **Hely** | Nyugat-Európa | Válasszon egy helyet a közelben vagy a függvények által elérendő más szolgáltatások közelében. |
    | **[Tárfiók](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account)** |  Globálisan egyedi név |  A függvényalkalmazás által használt új tárfiók neve. A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat. Meglévő fiókot is használhat. |

1. Kattintson a **Létrehozás** elemre az új függvényalkalmazás létrehozásához és üzembe helyezéséhez.
