1. A portálon a bal oldalon kattintson a **+** gombra, és írja be a „Virtuális hálózati átjáró” kifejezést a keresőmezőbe. A keresési eredmények között keresse meg a **Virtuális hálózati átjáró** elemet, és kattintson a bejegyzésre. A **Virtuális hálózati átjáró** lap alsó részén kattintson a **Létrehozás** gombra a **Virtuális hálózati átjáró létrehozása** lap megnyitásához.
2. A **Virtuális hálózati átjáró létrehozása** lapon töltse ki a virtuális hálózati átjáró értékeit.

  ![Virtuális hálózati átjáró létrehozása lap mezői](./media/vpn-gateway-add-gw-rm-portal-include/gw.png "Virtuális hálózati átjáró létrehozása lap mezői")
3. Az a **virtuális hálózati átjáró létrehozása** adja meg azokat az értékeket a virtuális hálózati átjáró.

  - **Név**: adjon nevet az átjárónak. Ez nem ugyanaz, mint egy átjáró alhálózatának elnevezése. Ez a létrehozni kívánt átjáróobjektum neve.
  - **Átjáró típusa**: válassza ki a **VPN** elemet. A VPN-átjárók a **VPN** virtuális hálózati átjárótípust használják. 
  - **VPN típusa**: válassza ki a konfigurációjához megadott VPN-típust. A legtöbb konfigurációhoz útvonalalapú VPN-típus szükséges.
  - **SKU**: válassza ki az átjáró SKU-ját a legördülő listából. A legördülő listában szereplő SKU-k a kiválasztott VPN-típustól függenek. Az átjáró-termékváltozatokkal kapcsolatos további információkért lásd: [Gateway SKUs](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku) (Átjáró-termékváltozatok).
  - **Hely**: Lehetséges, hogy le kell görgetnie, hogy megjelenjen a Hely. Állítsa be úgy a **Hely** mezőt, hogy a virtuális hálózat helyére mutasson. Ha a hely a régió, ahol a virtuális hálózaton található, amikor egy virtuális hálózatot választ a következő lépésben nem mutat, már az nem jelenik meg a legördülő listában.
  - **Virtuális hálózat**: Válassza ki azt a virtuális hálózatot, amelyhez hozzá kívánja adni az átjárót. Kattintson a **virtuális hálózati** "Válasszon egy virtuális hálózatot" lapjának megnyitásához. Válassza ki a VNet elemet. Ha a VNet nem jelenik meg, ellenőrizze, hogy a Hely mező arra a régióra mutat-e, amelyikben a virtuális hálózata található.
  - **Átjáróalhálózat címtartománya**: csak akkor jelenik meg ezt a beállítást, ha nem korábban Ön egy átjáró-alhálózatot a virtuális hálózat. Ha korábban létrehozott egy érvényes átjáró-alhálózatot, ez a beállítás nem jelenik meg.
  - **Első IP-konfiguráció**: "Válassza ki a nyilvános IP-cím" lapon hoz létre egy nyilvános IP-cím objektum, amely lekérdezi a VPN-átjáró társítva. A nyilvános IP-cím dinamikusan van rendelve ehhez az objektumhoz, a VPN-átjáró létrehozásakor. A VPN Gateway jelenleg csak a *Dinamikus* nyilvános IP-cím lefoglalását támogatja. Ez azonban nem jelenti azt, hogy az IP-cím módosul a VPN Gateway-hez való hozzárendelése után. A nyilvános IP-cím kizárólag abban az esetben változik, ha az átjárót törli, majd újra létrehozza. Nem módosul átméretezés, alaphelyzetbe állítás, illetve a VPN Gateway belső karbantartása/frissítése során.

    - Első lépésként kattintson **létrehozás átjáró IP-konfiguráció** nyissa meg a "Nyilvános IP-cím kiválasztása" lapot, majd kattintson a **+ új létrehozása** "Nyilvános IP-cím létrehozása" lapjának megnyitásához.
    - Ezt követően adjon meg egy **neve** a nyilvános IP-cím. A Termékváltozat, hagyja **alapvető** kivéve, ha egy konkrét ok arra, hogy módosítsa valami mást, majd kattintson **OK** menti a módosításokat a lap alján.

      ![Nyilvános IP-cím létrehozása](./media/vpn-gateway-add-gw-s2s-rm-portal-include/gwip.png "PIP létrehozása")

4. Ellenőrizze a beállításokat. Kiválaszthatja **rögzítés az irányítópulton** , ha azt szeretné, hogy az átjáró megjelenjen az irányítópulton az oldal alján. 
5. A **Létrehozás** gombra kattintva hozzákezdhet a VPN-átjáró létrehozásához. A rendszer érvényesíti a beállításokat, és látni fogja, a "Deploying virtuális hálózati átjáró" csempét az irányítópulton. Az átjáró létrehozása akár 45 percet is igénybe vehet. Előfordulhat, hogy a kész állapot megjelenítéséhez frissítenie kell a portáloldalt.

Az átjáró létrehozása után úgy tekintheti meg a hozzárendelt IP-címet, ha megnézi a virtuális hálózatot a portálon. Az átjáró csatlakoztatott eszközként fog megjelenni. A csatlakoztatott eszközre (azaz a virtuális hálózati átjáróra) kattintva további információkat jeleníthet meg.
