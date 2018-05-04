Használja az Azure App Service Mobile Apps szolgáltatásának [Azure Notification Hubs] elküldeni a leküldéses értesítések, úgy kívánja beállítani egy értesítési központot a mobilalkalmazás számára.

1. A a [Azure-portálon], keresse fel **alkalmazásszolgáltatások**, majd válassza ki az alkalmazás háttérből. A **beállítások**, jelölje be **leküldéses**.
2. Értesítési központ erőforrás hozzáadása az alkalmazáshoz, jelölje ki a **Connect**. Létrehoz egy központot, vagy egy meglévő csatlakozni.

    ![A központ konfigurálása](./media/app-service-mobile-create-notification-hub/configure-hub-flow.png)

Most már csatlakozott egy értesítési központot a Mobile Apps háttér-projekthez. Később konfigurálhatja az értesítési központ egy platform notification system (PNS), majd az eszközökre való kapcsolódáshoz.

[Azure-portálon]: https://portal.azure.com/
[Azure Notification Hubs]: https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/
