

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Válassza az **Új** > **Web + mobil** > **Értesítési központ** elemet.
   
      ![Azure Portal – Értesítési központ létrehozása](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-create.png)
      
3. Az **Értesítési központ** mezőbe írjon be egy egyedi nevet. Válassza ki a **Helyet**, **Előfizetést** és **Erőforráscsoportot** (ha már rendelkezik ilyennel). 
   
    Ha már rendelkezik egy szolgáltatásbusz-névtérrel, amelyben létrehozná a központot, tegye a következőket:

    a. A **Névtér** területen válassza a **Meglévő használata** hivatkozást. 
   
    b. Kattintson a **Létrehozás** gombra.

    Ha még nem rendelkezik szolgáltatásbusz-névtérrel, használhatja a központ neve alapján létrehozott alapértelmezett nevet (ha a névtér neve elérhető).
   
      ![Azure Portal – értesítési központ tulajdonságainak megadása](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

    A névtér és az értesítési központ létrehozása után megnyílik az Azure Portal. 
   
      ![Azure Portal – értesítési központ portáloldala](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-page.png)

4. Válassza a **Beállítások** > **Hozzáférési szabályzatok** lehetőséget. Figyelje meg a két elérhető kapcsolati karakterláncot. Ezekre később szükség lesz a leküldéses értesítések kezeléséhez.
   
      ![Azure Portal – értesítési központ kapcsolati karakterláncai](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)

