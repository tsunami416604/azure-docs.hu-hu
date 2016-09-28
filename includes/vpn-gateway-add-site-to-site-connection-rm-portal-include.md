1. Keresse meg a virtuális hálózati átjáróját, és kattintson az **All Settings** (Összes beállítás) elemre a **Settings** (Beállítások) panel megnyitásához.

2. A **Settings** (Beállítások) panelen kattintson a **Connections** (Kapcsolatok) elemre, majd a panel tetején látható **Add** (Hozzáadás) elemre a **Add connection** (Kapcsolat hozzáadása) panel megnyitásához.

    ![Helyek közötti kapcsolat létrehozása](./media/vpn-gateway-add-site-to-site-connection-rm-portal-include/addconnection250.png)

3. Az **Add Connection** (Kapcsolat hozzáadása) panelen **nevezze el** a kapcsolatot. 

4. A **Connection type** (Kapcsolat típusa) beállításnál válassza a **Site-to-site(IPSec)** Helyek közötti (IPSec) típust.

5. A **Virtual network gateway** (Virtuális hálózati átjáró) értéke fix, mivel erről az átjáróról csatlakozik.

6. A **Local network gateway** (Helyi hálózati átjáró) beállításnál kattintson a **Choose a local network gateway** (Helyi hálózati átjáró kiválasztása) elemre, és válassza ki a használni kívánt helyi hálózati átjárót. 

7. A **Shared Key** (Megosztott kulcs) értékének egyeznie kell azzal az értékkel, amit a helyi VPN-eszközhöz használ. Ha a helyi hálózatán található VPN-eszköz nem ad meg egy megosztott kulcsot, akkor kitalálhat egyet, és beírhatja ide és a helyi eszközén. A lényeg az, hogy megegyezzenek.

8. A fennmaradó **Subscription** (Előfizetés), **Resource Group** (Erőforráscsoport) és **Location** (Hely) mezők értékei rögzítettek.

9. A kapcsolat létrehozásához kattintson az **OK** gombra. A *Creating Connection* (Kapcsolat létrehozása) üzenet elkezd villogni a képernyőn.

10. Amikor a kapcsolat elkészült, megjelenik az átjáró **Connections** (Kapcsolatok) paneljén.

    ![Helyek közötti kapcsolat létrehozása](./media/vpn-gateway-add-site-to-site-connection-rm-portal-include/connectionstatus450.png)



<!--HONumber=Sep16_HO4-->


