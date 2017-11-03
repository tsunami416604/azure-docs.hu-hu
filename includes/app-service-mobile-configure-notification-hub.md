Használja az Azure App Service Mobile Apps szolgáltatásának [Azure Notification Hubs] elküldeni a leküldéses értesítések, úgy kívánja beállítani egy értesítési központot a mobilalkalmazás számára.

1. A a [Azure-portálon], keresse fel **alkalmazásszolgáltatások**, majd kattintson az alkalmazás háttér. A **beállítások**, kattintson a **leküldéses**.
2. Kattintson a **Connect** notification hub erőforrás hozzáadása az alkalmazáshoz. Létrehoz egy központot, vagy egy meglévő csatlakozni.

    ![](./media/app-service-mobile-create-notification-hub/configure-hub-flow.png)

Most már csatlakozott egy értesítési központot a Mobile Apps háttér-projekthez. Később konfigurál majd az értesítési központ egy platform notification system (PNS), majd az eszközökre való kapcsolódáshoz.

[Azure-portálon]: https://portal.azure.com/
[Azure Notification Hubs]: https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-push-notification-overview/
