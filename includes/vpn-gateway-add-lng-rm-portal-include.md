1. A portál **Minden erőforrás** területén kattintson a **+Hozzáadás** gombra. Az a **mindent** panel keresési mezőbe, írja be **helyi hálózati átjáró**, majd kattintson az erőforrások listájához való visszatéréshez. Kattintson a **Helyi hálózati átjáró** elemre a panel megnyitásához, majd kattintson a **Létrehozás** elemre a **Helyi hálózati átjáró létrehozása** panel megnyitásához.
   
    ![helyi hálózati átjáró létrehozása](./media/vpn-gateway-add-lng-rm-portal-include/lng.png)

2. A **Helyi hálózati átjáró létrehozása** panelen adjon meg egy **Nevet** a helyi hálózati átjáróobjektumnak. Ha lehetséges, használjon egy egyszerűen elsajátítható, többek között **ClassicVNetLocal** vagy **TestVNet1Local**. Így könnyebben azonosíthatja a helyi hálózati átjáró a portálon.
3. Adjon meg egy érvényes nyilvános **IP-cím** a VPN-eszköz vagy a virtuális hálózati átjáró, amelyhez csatlakozni szeretne.<br>**Ha a helyi hálózati jelenti-e egy helyszíni hely:** adja meg a nyilvános IP-címe a VPN-eszközön, amelyhez csatlakozni kíván. Ez nem lehet a NAT mögött, és elérhetőnek kell lennie az Azure számára.<br>**Ha a helyi hálózat egy másik virtuális hálózatot jelöli:** adja meg, hogy a vnet rendelt hozzá a virtuális hálózati átjáró nyilvános IP-címét.<br>**Ha még nem rendelkezik az IP-cím:** jött létre egy érvényes helyőrző IP-címet, majd térjen vissza és csatlakozás előtt a beállítás módosításához.
4. A **Címtartomány** annak a hálózatnak a címtartományára utal, amelyet a helyi hálózat képvisel. Több címtartományt is felvehet. Győződjön meg arról, hogy az itt megadott tartományok nem lehetnek, amelyhez csatlakozni más hálózatokhoz a tartományait.
5. Az **Előfizetés** résznél ellenőrizze, hogy a megfelelő előfizetés jelenik-e meg.
6. Az **Erőforráscsoport** beállításnál válassza ki a használni kívánt erőforráscsoportot. Létrehozhat egy új erőforráscsoportot, vagy kiválaszthat egy korábban létrehozottat.
7. A **hely**, válassza ki a helyet, amely létrehozza az erőforráshoz. Érdemes ugyanazt a helyet kiválasztani, ahol a virtuális hálózat is található, de ez nem kötelező.
8. A helyi hálózati átjáró létrehozásához kattintson a **Create** (Létrehozás) gombra.

