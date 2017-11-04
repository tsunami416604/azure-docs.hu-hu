## <a name="how-to-create-a-classic-vnet-in-the-azure-portal"></a>A klasszikus virtuális hálózat létrehozása az Azure-portálon
A fenti forgatókönyv alapján klasszikus virtuális hálózat létrehozásához kövesse az alábbi lépéseket.

1. Egy böngészőből keresse fel a http://portal.azure.com címet, majd jelentkezzen be az Azure-fiókjával, ha szükséges.
2. Kattintson a **új** > **hálózati** > **virtuális hálózati**, figyelje meg, hogy a **telepítési modell kiválasztása** lista már bemutatja **klasszikus**, és kattintson a **létrehozása**, az alábbi ábrán látható módon.
   
    ![VNet létrehozása az Azure Portalon](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure1.gif)
3. Az a **virtuális hálózati** panelen, írja be a **neve** a virtuális hálózaton, és kattintson a **Címtéren**. Konfigurálja a virtuális hálózat és az első alhálózati cím terület beállításait, majd kattintson a **OK**. Az alábbi ábrán a mi esetünkben a CIDR üzenetblokk beállításainak.
   
    ![Cím terület panel](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure2.png)
4. Kattintson a **erőforráscsoport** , és válasszon egy erőforráscsoportot, adja hozzá a Vnetet, vagy kattintson **hozzon létre új erőforráscsoport** a virtuális hálózat hozzáadása egy új erőforráscsoportot. Az alábbi ábrán a **TestRG** nevű új erőforráscsoport erőforráscsoport-beállításai láthatóak. További információ az erőforráscsoportokkal kapcsolatban: [Azure Resource Manager Overview](../articles/azure-resource-manager/resource-group-overview.md#resource-groups) (Az Azure Resource Manager áttekintése).
   
    ![Erőforráscsoport panel létrehozása](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure3.png)
5. Szükség esetén módosítsa a VNethez tartozó **Előfizetés** és **Hely** beállításokat. 
6. Ha nem szeretné, hogy a VNet csempe megjelenjen a **Kezdőpulton**, tiltsa le **A kezdőpulton rögzít** lehetőséget. 
7. Kattintson a **Létrehozás** gombra, és megjelenik a **Virtuális hálózat létrehozása** csempe, amint az az alábbi ábrán is látható.
   
    ![VNet létrehozása a portálon](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure4.png)
8. Várjon, amíg a vnet létrejön, és amikor megjelenik az alábbi csempe, kattintson rá a További alhálózatok felvétele.
   
    ![VNet létrehozása a portálon](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure5.png)
9. Megjelenik a **konfigurációs** a vnet alább látható módon. 
   
    ![VNet létrehozása a portálon](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure6.png)
10. Kattintson a **alhálózatok** > **hozzáadása**, írja be a **neve** , és adja meg egy **-címtartományt (CIDR-blokkja)** az alhálózathoz, majd Kattintson a **OK**. Az alábbi ábra az aktuális esetünkben beállításait.
    
    ![VNet létrehozása az Azure Portalon](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure7.gif)

