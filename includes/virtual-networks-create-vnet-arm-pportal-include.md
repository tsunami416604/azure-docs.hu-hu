## VNet létrehozása az Azure Portalon

Ha a fenti forgatókönyv alapján kíván létrehozni egy VNetet az Azure Betekintő portál használatával, kövesse az alábbi lépéseket.

1. Egy böngészőből keresse fel a http://portal.azure.com címet, majd jelentkezzen be az Azure-fiókjával, ha szükséges.
2. Kattintson az **ÚJ** > **Hálózatkezelés** > **Virtuális hálózat** elemre, majd kattintson az **Erőforrás-kezelő** elemre a **Telepítési modell kiválasztása** listában, végül pedig kattintson a **Létrehozás** elemre, amint az alábbi ábrán látható.

    ![VNet létrehozása az Azure Portalon](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure1.gif)

3. A **Virtuális hálózat létrehozása** panelen konfigurálja a VNet beállításait az alábbi ábrán látható módon.

    ![Virtuális hálózat létrehozása panel](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure2.png)

4. Kattintson az **Erőforráscsoport** elemre, majd válassza ki az erőforráscsoportot, amelyhez a VNetet hozzá kívánja adni, vagy az **Új létrehozása** elemre kattintva adja hozzá a VNetet egy új erőforráscsoporthoz. Az alábbi ábrán a **TestRG** nevű új erőforráscsoport erőforráscsoport-beállításai láthatóak. További információ az erőforráscsoportokkal kapcsolatban: [Azure Resource Manager Overview](../articles/resource-group-overview.md#resource-groups) (Az Azure Resource Manager áttekintése).

    ![Erőforráscsoport](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure3.png)

5. Szükség esetén módosítsa a VNethez tartozó **Előfizetés** és **Hely** beállításokat. 

6. Ha nem szeretné, hogy a VNet csempe megjelenjen a **Kezdőpulton**, tiltsa le **A kezdőpulton rögzít** lehetőséget. 

7. Kattintson a **Létrehozás** gombra, és megjelenik a **Virtuális hálózat létrehozása** csempe, amint az az alábbi ábrán is látható.

    ![Virtuális hálózat csempéjének létrehozása](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure4.png)

8. Várja meg, amíg létrejön a VNet, majd a **Virtuális hálózat** panelen kattintson az **Összes beállítás** > **Alhálózatok** > **Hozzáadás** elemre az alább látható módon.

    ![Alhálózat hozzáadása az Azure Portalon](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure5.gif)

9. Adja meg a *BackEnd* alhálózathoz tartozó alhálózati beállításokat az alább látható módon, majd kattintson az **OK** gombra. 

    ![Alhálózat-beállítások](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure6.png)

10. Megjelenik az alhálózatok listája, amint az az alábbi ábrán látható.

    ![A VNet alhálózatainak listája](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure7.png)


<!--HONumber=Sep16_HO4-->


