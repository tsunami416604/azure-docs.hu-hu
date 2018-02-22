1. A portál bal oldalán kattintson az **+ Erőforrás létrehozása** gombra, és írja be a „Virtuális hálózati átjáró” kifejezést a keresőmezőbe. A keresési eredmények között keresse meg a **Virtuális hálózati átjáró** elemet, és kattintson a bejegyzésre. A **Virtuális hálózati átjáró** lap alsó részén kattintson a **Létrehozás** gombra a **Virtuális hálózati átjáró létrehozása** lap megnyitásához.
2. A **Virtuális hálózati átjáró létrehozása** lapon töltse ki a virtuális hálózati átjáró értékeit.

  ![Virtuális hálózati átjáró létrehozása lap mezői](./media/vpn-gateway-add-gw-p2s-rm-portal-include/p2sgw.png "Virtuális hálózati átjáró létrehozása lap mezői")
3. A **Virtuális hálózati átjáró létrehozása** lapon adja meg a virtuális hálózati átjáró értékeit.

  - **Név**: adjon nevet az átjárónak. Ez nem ugyanaz, mint egy átjáró alhálózatának elnevezése. Ez a létrehozni kívánt átjáróobjektum neve.
  - **Átjáró típusa**: válassza ki a **VPN** elemet. A VPN-átjárók a **VPN** virtuális hálózati átjárótípust használják. 
  - **VPN típusa**: válassza ki a konfigurációjához megadott VPN-típust. A legtöbb konfigurációhoz útvonalalapú VPN-típus szükséges.
  - **SKU**: válassza ki az átjáró SKU-ját a legördülő listából. A legördülő listában szereplő SKU-k a kiválasztott VPN-típustól függenek. Az átjáró-termékváltozatokkal kapcsolatos további információkért lásd: [Gateway SKUs](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku) (Átjáró-termékváltozatok).
  - **Hely**: Lehetséges, hogy le kell görgetnie, hogy megjelenjen a Hely. Állítsa be úgy a **Hely** mezőt, hogy a virtuális hálózat helyére mutasson. Ha a hely nem egyezik meg azzal a régióval, ahol a virtuális hálózat található, akkor a következő lépésben egy virtuális hálózat kiválasztásakor az nem jelenik meg a legördülő listában.
  - **Virtuális hálózat**: Válassza ki azt a virtuális hálózatot, amelyhez hozzá kívánja adni az átjárót. Kattintson a **Virtuális hálózat** elemre a Virtuális hálózat választása lap megnyitásához. Válassza ki a VNet elemet. Ha a VNet nem jelenik meg, ellenőrizze, hogy a Hely mező arra a régióra mutat-e, amelyikben a virtuális hálózata található.
  - **Átjáróalhálózat címtartománya**: Ez a beállítás csak akkor jelenik meg, ha előzőleg nem hozott létre átjáróalhálózatot a virtuális hálózathoz. Ha korábban létrehozott egy érvényes átjáróalhálózatot, ez a beállítás nem jelenik meg.
  - **Első IP-konfiguráció**: A „Nyilvános IP-cím választása” lap létrehoz egy nyilvános IP-cím-objektumot, amelyhez hozzá lesz rendelve a VPN-átjáró. A nyilvános IP-címet a rendszer dinamikusan rendeli hozzá ehhez az objektumhoz a VPN-átjáró létrehozásakor. A VPN Gateway jelenleg csak a *Dinamikus* nyilvános IP-cím lefoglalását támogatja. Ez azonban nem jelenti azt, hogy az IP-cím módosul a VPN Gateway-hez való hozzárendelése után. A nyilvános IP-cím kizárólag abban az esetben változik, ha az átjárót törli, majd újra létrehozza. Nem módosul átméretezés, alaphelyzetbe állítás, illetve a VPN Gateway belső karbantartása/frissítése során.

    - Először az **Átjáró IP-konfigurációjának létrehozása** elemre kattintva nyissa meg a „Nyilvános IP-cím választása” lapot, majd az **+Új létrehozása** elemre kattintva a „Nyilvános IP-cím létrehozása” lapot.
    - Ezután adjon egy **nevet** a nyilvános IP-címnek. A termékváltozatot hagyja **Alapszintű** értéken, ha nincs valamilyen konkrét érv amellett, hogy megváltoztassa, majd kattintson a lap alján lévő **OK** gombra a módosítások mentéséhez.

      ![Nyilvános IP-cím létrehozása](./media/vpn-gateway-add-gw-s2s-rm-portal-include/gwip.png "PIP létrehozása")

4. Ellenőrizze a beállításokat. Ha azt szeretné, hogy az átjáró megjelenjen az irányítópulton, válassza a lap alján lévő **Rögzítés az irányítópulton** elemet. 
5. A **Létrehozás** gombra kattintva hozzákezdhet a VPN-átjáró létrehozásához. A rendszer ellenőrzi a beállításokat, és az irányítópulton megjelenik a „Virtuális hálózati átjáró üzembe helyezése” csempe. Az átjáró létrehozása akár 45 percet is igénybe vehet. Előfordulhat, hogy a kész állapot megjelenítéséhez frissítenie kell a portáloldalt.

Az átjáró létrehozása után úgy tekintheti meg a hozzárendelt IP-címet, ha megnézi a virtuális hálózatot a portálon. Az átjáró csatlakoztatott eszközként fog megjelenni. A csatlakoztatott eszközre (azaz a virtuális hálózati átjáróra) kattintva további információkat jeleníthet meg.