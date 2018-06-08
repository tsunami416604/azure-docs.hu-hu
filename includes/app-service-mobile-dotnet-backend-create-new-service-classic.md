1. Jelentkezzen be a(z) a [Azure Portal].
2. Válassza ki **+ új** > **Web + mobil** > **mobilalkalmazás**, majd meg egy nevet a Mobile Apps háttér és.
3. A **erőforráscsoport**, válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat (az alkalmazás neve használatával). 
4. A **App Service-csomag**, az alapértelmezett terv (a a [Standard csomagra](https://azure.microsoft.com/pricing/details/app-service/)) van kiválasztva. Igény szerint kiválaszthatja egy másik csomagot, vagy [hozzon létre egy újat](../articles/app-service/app-service-plan-manage.md#create-an-app-service-plan). 

   Az App Service-csomag beállításai határozzák meg a [helyét, a szolgáltatások, a költség és a számítási erőforrásokat](https://azure.microsoft.com/pricing/details/app-service/) az alkalmazáshoz társított. További információk az App Service csomagokban hogyan hozzon létre egy új csomagot egy másik tarifacsomagban réteg és a kívánt helyre, a következő látható: [Azure App Service-csomagok részletes áttekintése](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
   
5. Kattintson a **Létrehozás** gombra. Ebben a lépésben létrehozza a Mobile Apps háttér. 
6. Az a **beállítások** ablaktáblán az új Mobile Apps háttér, válasszon **gyors üzembe helyezési** > saját ügyfélalkalmazás-platformjára > **adatbázis csatlakoztatása**. 
   
   ![Kapcsolódás adatbázis megerősítése](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)
7. Az a **adatkapcsolat hozzáadása** ablaktáblán válassza előbb **SQL-adatbázis** > **hozzon létre egy új adatbázist**. Adja meg az adatbázis nevét, válasszon egy tarifacsomagot, és válassza **Server**. Ezt az új adatbázist többször is felhasználhatja. Ha már rendelkezik adatbázissal ugyanazon a helyen, választhatja a **Meglévő adatbázis használata** lehetőséget is. Egy adatbázis egy másik helyen, mert a sávszélességhez kötődő költségekkel és nagyobb késleltetéssel járhat használata nem ajánlott.
   
   ![Egy adatbázis kiválasztása](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)
8. A a **új kiszolgáló** ablaktáblán meg egy egyedi nevet a a **kiszolgálónév** adja meg a felhasználónevét és jelszavát, válassza ki **engedélyezése Azure-szolgáltatások kiszolgálói hozzáférésének**, válassza ki a  **OK**. Ez a lépés az új adatbázist hoz létre.
9. Vissza a **adatkapcsolat hozzáadása** ablaktáblán válassza előbb **kapcsolati karakterlánc**, adja meg a felhasználónevet és jelszót az adatbázist, és válassza ki **OK**. 

   Várjon néhány percet, amíg az adatbázis sikeresen telepített, mielőtt továbblép.

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/
