### <a name="determine-the-dns-name-of-the-virtual-machine"></a>A virtuális gép DNS-nevének meghatározása
Ha egy másik számítógépről szeretne az SQL Server Adatbázismotorhoz csatlakozni, ismernie kell a virtuális gép Tartománynév-szolgáltatójának (DNS) nevét. (Az internet ezzel a névvel azonosítja a virtuális gépet. Az IP-címet is használhatja, azonban az megváltozhat, ha az Azure erőforrásokat helyez át redundancia vagy karbantartás miatt. A DNS-név mindig stabil, mivel átirányítható egy új IP-címre.)  

1. Az Azure Portalon (vagy az előző lépésben) válassza a **Virtuális gépek (klasszikus)** lehetőséget.
2. Válassza ki az SQL virtuális gépét.
3. A **Virtuális gép** panelen másolja ki a virtuális gép **DNS-nevét**.
   
    ![DNS-név](./media/virtual-machines-sql-server-connection-steps/sql-vm-dns-name.png)

### <a name="connect-to-the-database-engine-from-another-computer"></a>Csatlakozás az adatbázismotorhoz egy másik számítógépről
1. Nyissa meg az SQL Server Management Studio alkalmazást egy internethez csatlakozó számítógépen.
2. A **Kapcsolódás a kiszolgálóhoz** vagy a **Kapcsolódás az Adatbázismotorhoz** párbeszédpanel **Kiszolgálónév** mezőjében adja meg a virtuális gép DNS-nevét (amelyet az előző feladatban határozott meg), valamint egy nyilvános végponthoz tartozó portszámot az alábbi formátumban: *DNSNév, portszám*, például **mysqlvm.cloudapp.net,57500**.
   
    ![Csatlakozás az SSMS használatával](./media/virtual-machines-sql-server-connection-steps/33Connect-SSMS.png)
   
    Ha nem emlékszik a korábban létrehozott nyilvános végpont portszámára, azt megtalálhatja a **Virtuális gép** panel **Végpontok** területén.
   
    ![Nyilvános port](./media/virtual-machines-sql-server-connection-steps/sql-vm-port-number.png)
3. A **Hitelesítés** mezőben válassza az **SQL Server-hitelesítés** lehetőséget.
4. A **Bejelentkezés** mezőbe írja be egy korábbi feladatban létrehozott bejelentkezés nevét.
5. A **Jelszó** mezőbe írja be a korábbi feladatban létrehozott bejelentkezés jelszavát.
6. Kattintson a **Connect** (Csatlakozás) gombra.

