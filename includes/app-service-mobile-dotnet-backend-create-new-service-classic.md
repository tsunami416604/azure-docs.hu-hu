1. Jelentkezzen be az [Azure Portalra].
2. Kattintson az **+ÚJ** > **Web + mobil** > **Mobile App** elemre, majd adjon meg egy nevet a mobilalkalmazás háttérrendszerének.
3. Az **Erőforráscsoport** beállításánál válasszon ki egy létező erőforráscsoportot, vagy hozzon létre egy újat (az alkalmazás nevének használatával). 
   
    Kiválaszthat egy másik App Service-csomagot, vagy újat is létrehozhat. Az App Service-csomagokkal, valamint az új csomagok egy másik tarifacsomagban és a kívánt helyen történő létrehozásával kapcsolatos további információk: [Azure App Service plans in-depth overview](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) (Az Azure App Service-csomagok részletes áttekintése).
4. Az **App Service-csomag** elemnél az alapértelmezett ([standard szintbe](https://azure.microsoft.com/pricing/details/app-service/) tartozó) csomag van kiválasztva. Igény szerint kiválaszthatja egy másik csomagot, vagy [hozzon létre egy újat](../articles/app-service/app-service-plan-manage.md#create-an-app-service-plan). Az App Service-csomag beállításai határozzák meg az alkalmazáshoz társított [helyet, szolgáltatásokat, költségeket és számítási erőforrásokat](https://azure.microsoft.com/pricing/details/app-service/). 
   
    A csomag kiválasztása után kattintson a **Létrehozás** gombra. Létrejön a mobilalkalmazás háttérrendszere. 
5. A mobilalkalmazás új háttérrendszerének **Beállítások** panelén kattintson az **Első lépések** lehetőségre, majd a saját ügyfélalkalmazás-platformjára, végül az **Adatbázis csatlakoztatása** lehetőségre. 
   
    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)
6. Az **Adatkapcsolat hozzáadása** panelen kattintson az **SQL Database** > **Új adatbázis létrehozása** lehetőségre, adja meg az adatbázis nevét a **Név** mezőben, válassza ki a tarifacsomagot, majd kattintson a **Kiszolgáló** elemre.  Ezt az új adatbázist többször is felhasználhatja. Ha már rendelkezik adatbázissal ugyanazon a helyen, választhatja a **Meglévő adatbázis használata** lehetőséget is. Más helyen található adatbázis használata nem ajánlott, mert ez további, a sávszélességhez kötődő költségekkel és nagyobb késleltetéssel járhat.
   
    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)
7. Az **Új kiszolgáló** panelen írjon be egy egyedi kiszolgálónevet a **Kiszolgáló neve** mezőbe, adjon meg egy felhasználónevet és egy jelszót, jelölje be az **Azure-szolgáltatások kiszolgálói hozzáférésének engedélyezése** jelölőnégyzetet, majd kattintson az **OK** gombra. Létrejön az új adatbázis.
8. Az **Adatkapcsolat hozzáadása** panelen kattintson a **Kapcsolati karakterlánc** elemre, írja be az adatbázishoz tartozó felhasználónevet és jelszót, majd kattintson az **OK** gombra. Várjon pár percet, amíg az adatbázis telepítése sikeresen befejeződik, mielőtt továbblépne.

<!-- URLs. -->
[Azure Portalra]: https://portal.azure.com/
