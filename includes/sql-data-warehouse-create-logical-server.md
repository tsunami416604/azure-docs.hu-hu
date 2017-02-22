### <a name="create-a-new-logical-sql-server-in-the-azure-portal"></a>Új logikai SQL-kiszolgáló létrehozása az Azure Portalon

1. Kattintson az **Új** gombra, keresse meg a **logikai kiszolgáló** lehetőséget, majd nyomja le az **ENTER** billentyűt.

    ![logikai kiszolgáló keresése](./media/sql-data-warehouse-create-logical-server/search-logical-server.png)
2. Válassza az **SQL Server (logikai kiszolgáló)** elemet. 

    ![logikai kiszolgáló kiválasztása](./media/sql-data-warehouse-create-logical-server/select-logical-server.png)
  
3. Az új SQL Server (logikai kiszolgáló) panel megnyitásához kattintson a **Létrehozás** gombra.

   <kbd> ![logikai kiszolgáló paneljének megnyitása](./media/sql-data-warehouse-create-logical-server/open-logical-server-blade.png) </kbd>
    <kbd>![logikai kiszolgáló panelje](./media/sql-data-warehouse-create-logical-server/logical-server-blade.png) </kbd>
  
3. Az SQL Server (logikai kiszolgáló) panel Kiszolgáló neve szövegbeviteli mezőjében adjon meg egy érvényes nevet az új logikai kiszolgálónak. Zöld pipa jelzi, hogy érvényes nevet adott meg.
    
    ![új kiszolgáló neve](./media/sql-data-warehouse-create-logical-server/new-name-logical-server.png)

    > [!IMPORTANT]
    > Az új kiszolgáló teljes neve a következő lesz: <kiszolgáló_neve>.database.windows.net.
    >
    
4. A Kiszolgáló rendszergazdájának felhasználóneve szövegbeviteli mezőben adjon meg egy felhasználónevet a kiszolgáló SQL-hitelesítéséhez. Az ilyen bejelentkezést kiszolgálószintű bejelentkezésnek nevezik. Zöld pipa jelzi, hogy érvényes nevet adott meg.
    
    ![SQL-rendszergazda felhasználóneve](./media/sql-data-warehouse-create-logical-server/sql-admin-login.png)
5. A **Jelszó** és a **Jelszó megerősítése** szövegbeviteli mezőben adjon meg egy jelszót a kiszolgálószintű bejelentkezési fiókhoz. Zöld pipa jelzi, hogy érvényes jelszót adott meg.
    
    ![SQL-rendszergazda jelszava](./media/sql-data-warehouse-create-logical-server/sql-admin-password.png)
6. Válasszon ki egy olyan előfizetést, amelyben rendelkezik objektumok létrehozásához szükséges engedéllyel.

    ![előfizetést](./media/sql-data-warehouse-create-logical-server/subscription.png)
7. Az Erőforráscsoport szövegbeviteli mezőben válassza az **Új létrehozása** lehetőséget, majd adjon egy érvényes nevet az új erőforráscsoportnak az erőforráscsoport mezőjében (egy korábban létrehozott erőforráscsoport is használhat, amennyiben rendelkezik ilyennel). Zöld pipa jelzi, hogy érvényes nevet adott meg.

    ![új erőforráscsoport](./media/sql-data-warehouse-create-logical-server/new-resource-group.png)

8. A **Hely** szövegbeviteli mezőben válasszon a tartózkodási helyének megfelelő adatközpontot (pl. „Kelet-Ausztrália”).
    
    ![kiszolgáló helye](./media/sql-data-warehouse-create-logical-server/server-location.png)
    
    > [!TIP]
    > Az **Azure-szolgáltatások kiszolgálói hozzáférésének engedélyezése** jelölőnégyzet állapota ezen a panelen nem módosítható. Ezt a beállítást a kiszolgálói tűzfal paneljén módosíthatja. További információkért lásd a [biztonságos használat első lépéseit](../articles/sql-database/sql-database-manage-servers-portal.md) ismertető témakört.
    >
    
9. Kattintson a **Létrehozás** gombra.

    ![létrehozás gomb](./media/sql-data-warehouse-create-logical-server/create.png)



<!--HONumber=Feb17_HO3-->


