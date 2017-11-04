1. A Feladatátvevőfürt-kezelőben bontsa ki a **szerepkörök**, és jelölje ki a rendelkezésre állási csoporthoz.  

2. Az a **erőforrások** fülre, kattintson a jobb gombbal a figyelő nevét, majd **tulajdonságok**.

3. Kattintson a **függőségek** fülre. Ha több erőforrások vannak felsorolva, győződjön meg arról, hogy az IP-címek rendelkezik-e, vagy nem és függőségeit.  

4. Kattintson az **OK** gombra.

5. Kattintson a jobb gombbal a figyelő nevét, és kattintson **online állapotba hozás**.

6. Miután a figyelő online, a **erőforrások** fülre, kattintson a jobb gombbal a rendelkezésre állási csoportot, majd **tulajdonságok**.
   
    ![A rendelkezésre állási csoport erőforrása konfigurálása](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678772.gif)

7. Függőség létrehozása a figyelő neve erőforrás (nem az IP-erőforrás nevét), és kattintson a **OK**.
   
    ![Adja hozzá a függőségi figyelő neve](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678773.gif)

8. Indítsa el az SQL Server Management Studio eszközt, és csatlakozzon az elsődleges másodpéldány.

9. Ugrás a **AlwaysOn magas rendelkezésre állás** > **rendelkezésre állási csoportok** > **\<AvailabilityGroupName\>**   >  **Rendelkezésre állási csoport figyelői**.  
    A figyelő nevét, amelyet a Feladatátvevőfürt-kezelőt hozott létre üzenetnek kell megjelennie.

10. Kattintson a jobb gombbal a figyelő nevét, és kattintson **tulajdonságok**.

11. Az a **Port** mezőben adja meg a portszámot a korábban használt $EndpointPort használatával a rendelkezésre állási csoport figyelője (ebben az oktatóanyagban a 1433-as volt az alapértelmezett), és kattintson a **OK**.

