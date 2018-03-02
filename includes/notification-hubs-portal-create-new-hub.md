

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Válassza az **Erőforrás létrehozása** > **Web + mobil** > **Értesítési központ** elemet.
   
      ![Azure Portal – Értesítési központ létrehozása](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-create.png)
      
3. Az **Értesítési központ** mezőbe írjon be egy egyedi nevet. Válassza ki a **Helyet**, **Előfizetést** és **Erőforráscsoportot** (ha már rendelkezik ilyennel). 
   
      Ha még nem rendelkezik szolgáltatásbusz-névtérrel, használhatja a központ neve alapján létrehozott alapértelmezett nevet (ha a névtér neve elérhető).
    
      Ha már rendelkezik szolgáltatásbusz-névtérrel, amelyben létrehozná a központot, kövesse ezeket a lépéseket

    a. A **Névtér** területen válassza a **Meglévő használata** hivatkozást. 
   
    b. Kattintson a **Létrehozás** gombra.
   
      ![Azure Portal – értesítési központ tulajdonságainak megadása](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

4. Miután létrehozta a névteret és az értesítési központot, a megnyitásához válassza a **Minden erőforrás** elemet, majd válassza ki a létrehozott értesítési központot a listából. 
   
      ![Azure Portal – értesítési központ portáloldala](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-resources.png)

5. A listáról válassza a **Hozzáférési szabályzatok** lehetőséget. Figyelje meg a két elérhető kapcsolati karakterláncot. Ezekre később szükség lesz a leküldéses értesítések kezeléséhez.

      >[!IMPORTANT]
      >**NE** használja a DefaultFullSharedAccessSignature elemet az alkalmazásban. Ezt csak a háttérben használhatja.
      >
   
      ![Azure Portal – értesítési központ kapcsolati karakterláncai](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)

