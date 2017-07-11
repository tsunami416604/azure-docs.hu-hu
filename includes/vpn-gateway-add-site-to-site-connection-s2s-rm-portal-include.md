1. Keresse meg, és nyissa meg a virtuális hálózati átjáró paneljét. A panelt többféleképpen is megtalálhatja. Ebben a példában a „VNet1GW” átjárót a **TestVNet1 -> Áttekintés -> Csatlakoztatott eszközök -> VNet1GW** helyen értük el.
2. A VNet1GW paneljén kattintson a **Kapcsolatok** elemre. A Kapcsolatok panelen kattintson a **+Hozzáadás** elemre a **Kapcsolat hozzáadása** panel megnyitásához.

    ![Helyek közötti kapcsolat létrehozása](./media/vpn-gateway-add-site-to-site-connection-s2s-rm-portal-include/connection1.png)

3. A **Kapcsolat hozzáadása** panelen adja meg az értékeket a kapcsolat létrehozásához.

  - **Név:** Nevezze el a kapcsolatot. A példában a **VNet1toSite2** nevet használjuk.
  - **Kapcsolat típusa:** Válassza a **Helyek közötti (IPSec)** típust.
  - **Virtuális hálózati átjáró:** Az értéke rögzített, mivel erről az átjáróról csatlakozik.
  - **Helyi hálózati átjáró:** Kattintson a **Helyi hálózati átjáró kiválasztása** elemre, és válassza ki a használni kívánt helyi hálózati átjárót. A példában a **Site2**-t használjuk.
  - **Megosztott kulcs:** Az értékének egyeznie kell azzal az értékkel, amit a helyi helyszíni VPN-eszközhöz használ. A példában az „abc123” értéket használtuk, de érdemesebb egy ennél összetettebb értéket használni. Vegye figyelembe, hogy az itt megadott értéknek meg kell egyeznie a VPN-eszköz konfigurálásakor meghatározott értékkel.
  - A fennmaradó **Subscription** (Előfizetés), **Resource Group** (Erőforráscsoport) és **Location** (Hely) mezők értékei rögzítettek.

4. A kapcsolat létrehozásához kattintson az **OK** gombra. A *Creating Connection* (Kapcsolat létrehozása) üzenet elkezd villogni a képernyőn.
5. A kapcsolatot a virtuális hálózat átjárójának **Kapcsolatok** paneljén tekintheti meg. Az Állapot *Ismeretlenről* *Kapcsolódásra*, majd *Sikeresre* vált.