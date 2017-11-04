Ebben a lépésben kézzel létrehozhat a rendelkezésre állási csoport figyelőjének a Feladatátvevőfürt-kezelő és az SQL Server Management Studio.

1. Nyissa meg a Feladatátvevőfürt-kezelőt az elsődleges másodpéldányt futtató csomópontjából.

2. Válassza ki a **hálózatok** csomópont, és figyelje meg a fürt hálózati nevét. Ez a név szerepel a $ClusterNetworkName változót a PowerShell-parancsfájlt.

3. Bontsa ki a fürt nevét, és kattintson a **szerepkörök**.

4. Az a **szerepkörök** ablaktáblájában kattintson a jobb gombbal a rendelkezésre állási csoport nevét, majd válassza ki **erőforrás hozzáadása** > **ügyfél-hozzáférési pont**.
   
    ![Ügyfél-hozzáférési pont rendelkezésre állási csoport hozzáadása](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678769.gif)

5. A a **neve** mezőben hozzon létre egy nevet az új figyelőt, kattintson a **következő** kétszer, majd **Befejezés**.  
    Nem kapcsolja a figyelő vagy erőforrás online ezen a ponton.

6. Kattintson a **erőforrások** lapot, és ezután bontsa ki az imént létrehozott ügyfél-hozzáférési pont. 
    Az IP-cím erőforrás a fürtön minden fürthálózat jelenik meg. Ha ez egy csak Azure megoldás, csak egy IP-cím erőforrás jelenik meg.

7. A következő lehetőségek közül választhat:
   
   * Hibrid megoldás konfigurálása:
     
        a. Kattintson a jobb gombbal az IP-cím erőforrás a helyszíni alhálózatnak megfelelő, majd válassza ki **tulajdonságok**. Megjegyzés: az IP-cím neve és hálózati névtől.
   
        b. Válassza ki **statikus IP-cím**, nem használt IP-címet, és kattintson a **OK**.
 
   * Egy Azure-csak a megoldás konfigurálása:

        a. Kattintson a jobb gombbal az IP-cím erőforrás a Azure alhálózatnak megfelelő, majd válassza ki **tulajdonságok**.
       
       > [!NOTE]
       > Ha a figyelő később sikertelen egy ütköző IP-cím szerint DHCP miatt online állapotba, konfigurálhatja a egy érvényes statikus IP-címet a Tulajdonságok ablakban.
       > 
       > 

       b. Ugyanazon **IP-cím** tulajdonságai ablakban keresse a **IP-cím neve**.  
        Ez a név szerepel a PowerShell parancsfájl $IPResourceName változó. Ha a megoldás több Azure virtuális hálózat is, ismételje meg ezt a lépést minden IP-erőforrás.

