### <a name="noconnection"></a>Helyi hálózati átjáró IP-címelőtagjainak módosítása – nincs átjárókapcsolat

#### <a name="to-add-additional-address-prefixes"></a>További címelőtagok felvétele:

1. A helyi hálózati átjáró erőforráson az a **beállítások** területén kattintson **konfigurációs**.
2. Adja hozzá az IP-címterének a *újabb címtartomány felvétele* mezőbe.
3. Kattintson a **mentése** a beállítások mentéséhez.

#### <a name="to-remove-address-prefixes"></a>Címelőtagok eltávolítása:

1. A helyi hálózati átjáró erőforráson az a **beállítások** területén kattintson **konfigurációs**.
2. Kattintson a **"..."** a sort, amelyben az eltávolítani kívánt előtagot.
3. Kattintson a **eltávolítása**.
4. Kattintson a **mentése** a beállítások mentéséhez.

### <a name="withconnection"></a>Helyi hálózati átjáró IP-címelőtagjainak módosítása – létező átjárókapcsolat

Ha már rendelkezik átjárókapcsolattal, és szeretné felvenni vagy eltávolítani a helyi hálózati átjáróban tárolt IP-címelőtagokat, akkor az alábbi lépéseket kell sorban végrehajtania. Ez némi állásidőt jelent a VPN-kapcsolata számára. Mikor módosítja az IP-címelőtagokat, nem kell törölnie a VPN-átjárót. Csak a kapcsolatot kell eltávolítania.

#### <a name="1-remove-the-connection"></a>1. Távolítsa el a kapcsolatot.

1. A helyi hálózati átjáró erőforráson az a **beállítások** területén kattintson **kapcsolatok**.
2. Kattintson a **...**  minden kapcsolat sorában, majd kattintson **törlése**.
3. Kattintson a **mentése** a beállítások mentéséhez.

#### <a name="2-modify-the-address-prefixes"></a>2. Módosítsa a címelőtagokat.

További címelőtagok felvétele:

1. A helyi hálózati átjáró erőforráson az a **beállítások** területén kattintson **konfigurációs**.
2. Az IP-címterület hozzáadása.
3. Kattintson a **mentése** a beállítások mentéséhez.

Címelőtagok eltávolítása:

1. A helyi hálózati átjáró erőforráson az a **beállítások** területén kattintson **konfigurációs**.
2. Kattintson a **...**  a sort, amelyben az eltávolítani kívánt előtagot.
3. Kattintson a **eltávolítása**.
4. Kattintson a **mentése** a beállítások mentéséhez.

#### <a name="3-recreate-the-connection"></a>3. Hozza létre újra a kapcsolatot.

1. Nyissa meg a virtuális hálózati átjáró a Vnethez tartozó. (Nem a helyi hálózati átjáró.)
2. A virtuális hálózati átjárón az a **beállítások** kattintson **kapcsolatok**.
3. Kattintson a **+ Hozzáadás** megnyitásához a **kapcsolat hozzáadása a** panelen.
4. Hozza létre újra a kapcsolatot.
5. Kattintson a **OK** a VPN-kapcsolat létrehozásához.