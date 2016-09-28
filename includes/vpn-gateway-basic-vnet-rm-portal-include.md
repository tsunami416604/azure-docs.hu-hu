Az alábbi lépésekkel hozhat létre egy VNetet az Azure Portallal. Vegye figyelembe, hogy a képernyőképek csak példaként szolgálnak. Ne felejtse el ezeket az értékeket a saját értékeire cserélni. További információ a virtuális hálózatok használatáról: [Virtual Network Overview](../articles/virtual-network/virtual-networks-overview.md) (Virtuális hálózatok áttekintése).

1. Egy böngészőből keresse fel az [Azure Portalt](http://portal.azure.com), majd jelentkezzen be az Azure-fiókjával, ha szükséges.

2. Kattintson az **Új** **>** **Hálózatkezelés** **>** **Virtuális hálózat** elemre.

    ![VNetBlade](./media/vpn-gateway-basic-vnet-rm-portal-include/newvnetportal650.png)

3. A Virtuális hálózat panel alján, a **Telepítési modell kiválasztása** listában válassza ki a **Resource Manager** elemet, majd kattintson a **Létrehozás** elemet.


    ![A Resource Manager kiválasztása](./media/vpn-gateway-basic-vnet-rm-portal-include/resourcemanager250.png)

4. A **Virtuális hálózat létrehozása** panelen konfigurálja a VNet beállításait. Ezen a panelen adhatja majd hozzá az első címterét és egy önálló alhálózati címtartományt. A VNet létrehozása után visszaléphet, és további alhálózatokat és címtereket vehet fel. Ez az aktuális korlátozás a portál miatt van. Bármikor visszatérhet, és frissítheti ezeket az értékeket a VNet tulajdonságainak a portálon vagy a PowerShell használatával történő módosításával. A használt értékek a létrehozni kívánt konfigurációtól függnek majd. Ügyeljen arra, hogy a tervezett konfiguráció értékeit használja. 

    ![Virtuális hálózat létrehozása panel](./media/vpn-gateway-basic-vnet-rm-portal-include/createavnet250.png)

5. Ellenőrizze, hogy a megfelelő előfizetés jelenik-e meg a **Subscription** (Előfizetés) résznél. Az előfizetéseket a legördülő menüben módosíthatja.

6. Kattintson a **Resource group** (Erőforráscsoport) elemre, és válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat az új erőforráscsoport nevének beírásával. Ha új csoportot hoz létre, a tervezett konfigurációs értékeknek megfelelően nevezze el az erőforráscsoportot. További információ az erőforráscsoportokkal kapcsolatban: [Azure Resource Manager Overview](resource-group-overview.md#resource-groups) (Az Azure Resource Manager áttekintése).

7. Ezután válassza ki a VNethez tartozó **Location** (Hely) beállításokat. Vegye figyelembe, hogy ez a hely határozza meg a VNeten üzembe helyezett erőforrások helyét. Ezt a későbbiekben csak az erőforrások ismételt üzembe helyezésével módosíthatja.

8. Ha szeretné könnyen megtalálni a VNetet az irányítópulton, akkor válassza a **Pin to dashboard** (Rögzítés az irányítópulton) lehetőséget, majd kattintson a **Create** (Létrehozás) gombra.
    
    ![Rögzítés az irányítópulton](./media/vpn-gateway-basic-vnet-rm-portal-include/pintodashboard150.png)


9. A Create (Létrehozás) gombra kattintva létrejön egy csempe az irányítópulton, amely a VNet állapotát mutatja. A VNet létrejöttével a csempe is módosul.

    ![Virtuális hálózat csempéjének létrehozása](./media/vpn-gateway-basic-vnet-rm-portal-include/deploying150.png)

<!--HONumber=Sep16_HO4-->


