### DNS-címke konfigurálása a nyilvános IP-címhez

Ha az internetről szeretne csatlakozni az SQL Server adatbázismotorhoz, először konfiguráljon egy DNS-címkét a nyilvános IP-címhez.

> [AZURE.NOTE] Nincs szükség DNS-címkére, ha csak az adott virtuális hálózaton belül vagy csak helyben szeretne csatlakozni az SQL Server-példányhoz.

DNS-címke létrehozásához először válassza a **Virtuális gépek** elemet a portálon. Jelölje ki az SQL Server rendszerű virtuális gépet a tulajdonságai megjelenítéséhez.

1. A virtuális gép panelén válassza ki a **nyilvános IP-címét**.

    ![nyilvános IP-cím](./media/virtual-machines-sql-server-connection-steps/rm-public-ip-address.png)

2. A Nyilvános IP-cím tulajdonságai között bontsa ki a **Konfiguráció** elemet.

3. Adjon meg egy DNS-címkenevet. Ez a név egy A rekord, amelynek használatával név szerint csatlakozhat az SQL Server rendszerű virtuális géphez az IP-cím megadásával való közvetlen csatlakozás helyett.

    ![dns-címke](./media/virtual-machines-sql-server-connection-steps/rm-dns-label.png)

### Csatlakozás az adatbázismotorhoz egy másik számítógépről

1. Nyissa meg az SQL Server Management Studio (SSMS) alkalmazást egy internethez csatlakozó számítógépen.

2. A **Kapcsolódás a kiszolgálóhoz** vagy a ** Kapcsolódás az adatbázismotorhoz** párbeszédpanelen szerkessze a **Kiszolgáló neve** értéket. Adja meg a virtuális gép (az előző feladat során meghatározott) teljes DNS-nevét.

3. A **Hitelesítés** mezőben válassza az **SQL Server-hitelesítés** lehetőséget.

5. A **Bejelentkezés** szövegmezőbe írjon be egy érvényes SQL-bejelentkezési nevet.

6. A **Jelszó** szövegmezőbe írja be a bejelentkezési jelszót.

7. Kattintson a **Csatlakozás** gombra.

    ![ssms connect](./media/virtual-machines-sql-server-connection-steps/rm-ssms-connect.png)



<!--HONumber=Jun16_HO2-->


