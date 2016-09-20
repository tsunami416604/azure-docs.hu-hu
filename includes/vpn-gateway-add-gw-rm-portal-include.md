1. A portálon lépjen az **Új** > **Hálózatkezelés** > **Virtuális hálózati átjáró** elemre. Ez megnyitja a **Virtuális hálózati átjáró létrehozása** panelt.

    ![Átjáró](./media/vpn-gateway-add-gw-rm-portal-include/creategw250.png)

2. A **Virtuális hálózati átjáró létrehozása** panel **Név** mezőjébe írjon be egy nevet az átjáró számára. Ez nem ugyanaz, mint egy átjáró alhálózatának elnevezése. Ez a létrehozni kívánt átjáróobjektum neve.

3. Állítsa be úgy a **Hely** mezőt, hogy a virtuális hálózat helyére mutasson. Ha ezt nem teszi meg, a VNet listán nem jelenik meg a virtuális hálózata.
 
4. Következő lépésként válassza ki a virtuális hálózatot, amelyhez hozzá kívánja adni az átjárót. Kattintson a **Virtuális hálózat** elemre a **Virtuális hálózat választása** panel megnyitásához. Válassza ki a VNet elemet. A VNet csak akkor jelenik meg a listában, ha már rendelkezik egy érvényes átjáróalhálózattal.

5. Válasszon egy nyilvános IP-címet. A **Nyilvános IP-cím** elemre kattintva nyissa meg a **Nyilvános IP-cím választása** panelt. Ezután az **+Új létrehozása** elemre kattintva nyissa meg a **Nyilvános IP-cím létrehozása** panelt. Adjon egy nevet a nyilvános IP-címnek. Ezzel létrejön egy nyilvános IP-cím objektum, amelyhez dinamikusan hozzá lesz rendelve egy nyilvános IP-cím. <br>Kattintson az **OK** gombra a módosítások mentéséhez.

5. Az **Átjáró típusa** beállításnál válassza ki a konfigurációjához megadott átjárótípust.

6. A **Virtuális magánhálózat típusa** beállításnál válassza ki a konfigurációjához megadott VPN-típust.

7. Az **Előfizetés** beállításnál ellenőrizze, hogy a megfelelő előfizetés van-e kiválasztva.

8. Az **Erőforráscsoportot** a kiválasztott virtuális hálózat határozza meg. 

9. Ne módosítsa a **Helyet** a fenti beállítások megadása után. 

10. Ekkor a panel hasonlóan néz ki az 1. lépésnél látható ábrához. Ellenőrizze, hogy a beállítások egyeznek-e a saját konfigurációjával. Ha azt szeretné, hogy az átjáró megjelenjen az irányítópulton, válassza a panel alján lévő **Rögzítés az irányítópulton** elemet.

11. A **Létrehozás** gombra kattintva hozzákezdhet az átjáró létrehozásához. A rendszer érvényesíti a beállításokat, és az irányítópulton megjelenik a „Deploying Virtual network gateway” (Virtuális hálózati átjáró üzembe helyezése) csempe. Az átjáró létrehozása akár 45 percet is igénybe vehet. Előfordulhat, hogy a kész állapot megjelenítéséhez frissítenie kell a portáloldalt.

    ![Átjáró](./media/vpn-gateway-add-gw-rm-portal-include/deployvnetgw150.png)

11. Az átjáró létrehozása után úgy tekintheti meg a hozzárendelt IP-címet, ha megnézi a virtuális hálózatot a portálon. Az átjáró csatlakoztatott eszközként fog megjelenni. A csatlakoztatott eszközre (azaz a virtuális hálózati átjáróra) kattintva további információkat jeleníthet meg.






<!--HONumber=sep16_HO1-->


