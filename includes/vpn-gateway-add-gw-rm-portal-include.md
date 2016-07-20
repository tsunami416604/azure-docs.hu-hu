1. A portálon lépjen az **Új**, majd a **Hálózatkezelés** elemre. Válassza a listában a **Virtuális hálózati átjáró** elemet.

    ![Átjáró](./media/vpn-gateway-add-gw-rm-portal-include/creategw250.png)

2. A **Virtuális hálózati átjáró létrehozása** panel **Név** mezőjébe írjon be egy nevet az átjáró számára. Ez nem ugyanaz, mint egy átjáró alhálózatának elnevezése. Ez az átjáróobjektum neve.
 
3. Következő lépésként válassza ki a virtuális hálózatot, amelyben az átjárót üzembe kívánja helyezni. A nyílra kattintva nyissa meg a **Virtuális hálózat választása** panelt, majd kattintson a VNetre. A VNet csak akkor jelenik meg a listában, ha már rendelkezik egy érvényes átjáróalhálózattal.

4. Válasszon egy nyilvános IP-címet. A nyílra kattintva nyissa meg a **Nyilvános IP-cím választása** panelt. Ezután az **Új létrehozása** elemre kattintva nyissa meg a **Nyilvános IP-cím létrehozása** panelt. Írja be a nyilvános IP-címe nevét. Ne feledje, hogy a rendszer itt nem egy IP-cím megadását kéri. Az IP-cím hozzárendelése dinamikusan történik. Ez az IP-cím objektum neve lesz tartozik, amelyhez a cím hozzá lesz rendelve. Kattintson az **OK** gombra a módosítások mentéséhez.

5. Az **Átjáró típusa** beállításnál válassza ki a konfigurációjához megadott átjárótípust.

6. A **Virtuális magánhálózat típusa** beállításnál válassza ki a konfigurációjához megadott VPN-típust.

7. Az **Előfizetés** beállításnál ellenőrizze, hogy a megfelelő előfizetés van-e kiválasztva.

8. Az **Erőforráscsoport** beállításnál az erőforráscsoportot a kiválasztott virtuális hálózat határozza meg.

9. A **Hely** beállításnál ügyeljen arra, hogy azt a helyet válassza ki, amelyen az erőforráscsoport és a VNet is megtalálható.

10. Ha azt szeretné, hogy az átjáró megjelenjen az irányítópulton, válassza a **Rögzítés az irányítópulton** elemet. A **Létrehozás** gombra kattintva hozzákezdhet az átjáró létrehozásához. Az irányítópulton megjelenik a „Deploying Virtual network gateway” (Virtuális hálózati átjáró üzembe helyezése) felirat. Az átjáró létrehozása akár 45 percet is igénybe vehet. A háttérben számos művelet fut. Előfordulhat, hogy a kész állapot megjelenítéséhez frissítenie kell a portáloldalt.

    
    ![Átjáró](./media/vpn-gateway-add-gw-rm-portal-include/deployvnetgw150.png)

11. Az átjáró létrehozása után úgy tekintheti meg a hozzárendelt IP-címet, ha megnézi a virtuális hálózatot a portálon. Az átjáró csatlakoztatott eszközként fog megjelenni. A csatlakoztatott eszközre (azaz a virtuális hálózati átjáróra) kattintva további információkat jeleníthet meg.






<!--HONumber=Jun16_HO2-->


