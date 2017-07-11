1. A portáloldal bal oldalán kattintson a **+** gombra, és írja be a „Virtuális hálózati átjáró” kifejezést a keresőmezőbe. Az **Eredmények** között keresse meg a **Virtuális hálózati átjáró** elemet, és kattintson rá.
2. A Virtuális hálózati átjáró panel alján kattintson a **Létrehozás** gombra. Ez megnyitja a **Virtuális hálózati átjáró létrehozása** panelt.

    ![Virtuális hálózati átjáró létrehozása panel mezői](./media/vpn-gateway-add-gw-s2s-rm-portal-include/vnet_gw.png "Új átjáró")

3. A **Virtuális hálózati átjáró létrehozása** panelen adja meg a virtuális hálózati átjáró értékeit.

  - **Név**: adjon nevet az átjárónak. Ez nem ugyanaz, mint egy átjáró alhálózatának elnevezése. Ez a létrehozni kívánt átjáróobjektum neve.
  - **Átjáró típusa**: válassza ki a **VPN** elemet. A VPN-átjárók a **VPN** virtuális hálózati átjárótípust használják. 
  - **VPN típusa**: válassza ki a konfigurációjához megadott VPN-típust. A legtöbb konfigurációhoz útvonalalapú VPN-típus szükséges.
  - **SKU**: válassza ki az átjáró SKU-ját a legördülő listából. A legördülő listában szereplő SKU-k a kiválasztott VPN-típustól függenek. Az átjáró-termékváltozatokkal kapcsolatos további információkért lásd: [Gateway SKUs](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku) (Átjáró-termékváltozatok).
  - **Hely**: Lehetséges, hogy le kell görgetnie, hogy megjelenjen a Hely. Állítsa be úgy a **Hely** mezőt, hogy a virtuális hálózat helyére mutasson. Ha a hely nem egyezik meg azzal a régióval, ahol a virtuális hálózat található, a virtuális hálózat nem jelenik meg a következő lépésben a Virtuális hálózat választása legördülő menüben.
  - **Virtuális hálózat**: Válassza ki azt a virtuális hálózatot, amelyhez hozzá kívánja adni az átjárót. Kattintson a **Virtuális hálózat** elemre a „Virtuális hálózat választása” panel megnyitásához. Válassza ki a VNet elemet. Ha a VNet nem jelenik meg, ellenőrizze, hogy a Hely mező arra a régióra mutat-e, amelyikben a virtuális hálózata található.
  - **Nyilvános IP-cím:** A „Nyilvános IP-cím létrehozása” panel egy nyilvános IP-címobjektumot hoz létre. A nyilvános IP-címet a rendszer dinamikusan rendeli hozzá a VPN-átjáró létrehozásakor. A VPN Gateway jelenleg csak a *Dinamikus* nyilvános IP-cím lefoglalását támogatja. Ez azonban nem jelenti azt, hogy az IP-cím módosul a VPN Gateway-hez való hozzárendelése után. A nyilvános IP-cím kizárólag abban az esetben változik, ha az átjárót törli, majd újra létrehozza. Nem módosul átméretezés, alaphelyzetbe állítás, illetve a VPN Gateway belső karbantartása/frissítése során.

    - Először a **Nyilvános IP-cím** elemre kattintva nyissa meg a „Nyilvános IP-cím választása” panelt, majd az **+Új létrehozása** elemre kattintva a „Nyilvános IP-cím létrehozása” panelt.
    - Ezután adjon **Nevet** a nyilvános IP-címnek, a módosítások mentéséhez pedig kattintson az **OK** gombra a panel alján.

      ![Nyilvános IP-cím létrehozása](./media/vpn-gateway-add-gw-s2s-rm-portal-include/pip.png "PIP létrehozása")

4. Ellenőrizze a beállításokat. Ha azt szeretné, hogy az átjáró megjelenjen az irányítópulton, válassza a panel alján lévő **Rögzítés az irányítópulton** elemet. 
5. A **Létrehozás** gombra kattintva hozzákezdhet a VPN-átjáró létrehozásához. A rendszer érvényesíti a beállításokat, és az irányítópulton megjelenik a „Deploying Virtual network gateway” (Virtuális hálózati átjáró üzembe helyezése) csempe. Az átjáró létrehozása akár 45 percet is igénybe vehet. Előfordulhat, hogy a kész állapot megjelenítéséhez frissítenie kell a portáloldalt.

Az átjáró létrehozása után úgy tekintheti meg a hozzárendelt IP-címet, ha megnézi a virtuális hálózatot a portálon. Az átjáró csatlakoztatott eszközként fog megjelenni. A csatlakoztatott eszközre (azaz a virtuális hálózati átjáróra) kattintva további információkat jeleníthet meg.