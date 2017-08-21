Az alábbi lépésekkel hozhat létre egy VNetet a Resource Manager-alapú üzemi modellben az Azure Portallal. A képernyőképek csak példaként szolgálnak. Ne felejtse el ezeket az értékeket a saját értékeire cserélni. További információ a virtuális hálózatok használatáról: [Virtual Network Overview](../articles/virtual-network/virtual-networks-overview.md) (Virtuális hálózatok áttekintése).

1. Egy böngészőből keresse fel az [Azure portált](http://portal.azure.com), majd jelentkezzen be az Azure-fiókjával, ha szükséges.
2. Kattintson az **+** elemre. A **Piactér keresése** mezőbe írja be a „Virtuális hálózat” kifejezést. A visszaadott listában keresse meg a **Virtuális hálózat** elemet, és rákattintva nyissa meg a **Virtuális hálózat** lapot.

  ![Virtuális hálózati erőforrás keresése lap](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/newvnetportal700.png "Virtuális hálózati erőforrás keresése lap")
3. A Virtuális hálózat lap alján, a **Telepítési modell kiválasztása** listában válassza ki a **Resource Manager** elemet, majd kattintson a **Létrehozás** elemre.

  ![A Resource Manager kiválasztása](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/resourcemanager250.png "A Resource Manager kiválasztása")
4. A **Virtuális hálózat létrehozása** lapon konfigurálja a VNet beállításait. A mezők kitöltése közben a vörös felkiáltójelből zöld pipa lesz, ha a mezőbe beírt karakterek érvényesek. Előfordulhatnak automatikusan kitöltött értékek is. Ebben az esetben cserélje ki az értékeket a saját értékeire. A **Virtuális hálózat létrehozása** lap a következő példához hasonlóan néz ki:

  ![Mező érvényesítése](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/createp2sgvnet.png "Mező érvényesítése")
5. **Név**: adja meg a virtuális hálózat nevét.
6. **Címtér**: adja meg a címteret. Ha több címteret szeretne felvenni, vegye fel az elsőt. A virtuális hálózat létrehozása után később további címtereket is felvehet.
7. **Alhálózat neve**: adja meg az alhálózat nevét és az alhálózat címtartományát. A virtuális hálózat létrehozása után később további alhálózatokat is felvehet.
8. **Előfizetés**: ellenőrizze, hogy a megfelelő előfizetés jelenik-e meg a listában. Az előfizetéseket a legördülő menüben módosíthatja.
9. **Erőforráscsoport**: válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat az új erőforráscsoport nevének beírásával. Ha új csoportot hoz létre, a tervezett konfigurációs értékeknek megfelelően nevezze el az erőforráscsoportot. További információ az erőforráscsoportokkal kapcsolatban: [Azure Resource Manager Overview](../articles/azure-resource-manager/resource-group-overview.md#resource-groups) (Az Azure Resource Manager áttekintése).
10. **Hely**: válassza ki a Vnet helyét. Ez a hely határozza meg a VNeten üzembe helyezett erőforrások helyét.
11. Ha szeretné könnyen megtalálni a VNetet az irányítópulton, akkor válassza a **Pin to dashboard** (Rögzítés az irányítópulton) lehetőséget, majd kattintson a **Create** (Létrehozás) gombra.

 ![Rögzítés az irányítópulton](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/pintodashboard150.png "Rögzítés az irányítópulton")
12. A **Create** (Létrehozás) gombra kattintva létrejön egy csempe az irányítópulton, amely a VNet állapotát mutatja. A virtuális hálózat létrejöttével a csempe is módosul.

  ![Virtuális hálózat létrehozása csempe](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/deploying150.png "Virtuális hálózat létrehozása csempe")