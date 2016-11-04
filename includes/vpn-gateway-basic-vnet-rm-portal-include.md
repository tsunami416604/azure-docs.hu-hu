Az alábbi lépésekkel hozhat létre egy VNetet az Azure Portallal. A képernyőképek csak példaként szolgálnak. Ne felejtse el ezeket az értékeket a saját értékeire cserélni. További információ a virtuális hálózatok használatáról: [Virtual Network Overview](../articles/virtual-network/virtual-networks-overview.md) (Virtuális hálózatok áttekintése).

1. Egy böngészőből keresse fel az [Azure Portalt](http://portal.azure.com), majd jelentkezzen be az Azure-fiókjával, ha szükséges.
2. Kattintson az **Új** lehetőségre. A **Piactér keresése** mezőbe írja be a „Virtuális hálózat” kifejezést. A visszaadott listában keresse meg a **Virtuális hálózat** elemet, és rákattintva nyissa meg a **Virtuális hálózat** panelt.
   
    ![A Virtuális hálózati erőforrás keresése panel](./media/vpn-gateway-basic-vnet-rm-portal-include/newvnetportal700.png "Locate virtual network resource blade")
3. A Virtuális hálózat panel alján, a **Telepítési modell kiválasztása** listában válassza ki a **Resource Manager** elemet, majd kattintson a **Létrehozás** elemet.

    ![A Resource Manager kiválasztása](./media/vpn-gateway-basic-vnet-rm-portal-include/resourcemanager250.png "Select Resource Manager")

1. A **Virtuális hálózat létrehozása** panelen konfigurálja a VNet beállításait. A mezők kitöltése során a piros felkiáltójelből zöld pipa lesz, ha a mezőbe beírt karakterek érvényesek.
   
    ![Mező érvényesítése](./media/vpn-gateway-basic-vnet-rm-portal-include/checkmark300.png "Field validation")
2. A **Virtuális hálózat létrehozása** panel a következő példához hasonlóan néz ki. Előfordulhatnak automatikusan kitöltött értékek is. Ebben az esetben cserélje ki az értékeket a saját értékeire.
   
    ![Virtuális hálózat létrehozása panel](./media/vpn-gateway-basic-vnet-rm-portal-include/createvnet300.png "Create virtual network blade")
3. **Név**: adja meg a virtuális hálózat nevét.
4. **Címtér**: adja meg a címteret. Ha több címteret szeretne felvenni, vegye fel az elsőt. A virtuális hálózat létrehozása után később további címtereket is felvehet.
5. **Alhálózat neve**: adja meg az alhálózat nevét és az alhálózat címtartományát. A virtuális hálózat létrehozása után később további alhálózatokat is felvehet.
6. **Előfizetés**: ellenőrizze, hogy a megfelelő előfizetés jelenik-e meg a listában. Az előfizetéseket a legördülő menüben módosíthatja.
7. **Erőforráscsoport**: válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat az új erőforráscsoport nevének beírásával. Ha új csoportot hoz létre, a tervezett konfigurációs értékeknek megfelelően nevezze el az erőforráscsoportot. További információ az erőforráscsoportokkal kapcsolatban: [Azure Resource Manager Overview](../articles/resource-group-overview.md#resource-groups) (Az Azure Resource Manager áttekintése).
8. **Hely**: válassza ki a Vnet helyét. Ez a hely határozza meg a VNeten üzembe helyezett erőforrások helyét.
9. Ha szeretné könnyen megtalálni a VNetet az irányítópulton, akkor válassza a **Pin to dashboard** (Rögzítés az irányítópulton) lehetőséget, majd kattintson a **Create** (Létrehozás) gombra.
   
   ![Rögzítés az irányítópulton](./media/vpn-gateway-basic-vnet-rm-portal-include/pintodashboard150.png "pin to dashboard")
10. A **Create** (Létrehozás) gombra kattintva létrejön egy csempe az irányítópulton, amely a VNet állapotát mutatja. A VNet létrejöttével a csempe is módosul.
    
    ![Virtuális hálózat csempéjének létrehozása](./media/vpn-gateway-basic-vnet-rm-portal-include/deploying150.png "Creating virtual network tile")

<!--HONumber=Oct16_HO1-->


