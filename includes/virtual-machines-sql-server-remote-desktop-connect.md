A következő lépésekkel csatlakozzon az SQL Server virtuális géphez a távoli asztalról:

1. Miután az Azure virtuális gép létrejött és fut, kattintson a Virtual Machines ikonra az Azure Portalon a virtuális gépek megtekintéséhez.

1. Kattintson a három pontot ábrázoló gombra (**...**) az új virtuális gép mellett.

1. Kattintson a **Connect** (Csatlakozás) gombra.

   ![Csatlakozás a portálon lévő virtuális géphez](./media/virtual-machines-sql-server-remote-desktop-connect/azure-virtual-machine-connect.png)

1. Nyissa meg a böngésző által a virtuális géphez letöltött **RDP**-fájlt.

1. A távoli asztali kapcsolat arról értesíti, hogy a távoli kapcsolat közzétevője nem azonosítható. Kattintson a **Csatlakozás** gombra a folytatáshoz.

1. A **Windows rendszerbiztonság** párbeszédpanelen kattintson a **Másik fiók használata** elemre. Ha ez a lehetőség nem látható, a **További lehetőségek** gombra kattintva jeleníthető meg. Adja meg a virtuális gép létrehozásakor konfigurált felhasználónevet és jelszót. A felhasználónév elé írjon egy fordított perjelet.

   ![Távoli asztal hitelesítése](./media/virtual-machines-sql-server-remote-desktop-connect/remote-desktop-connect.png)

Az SQL Server virtuális géphez való csatlakozás után elindíthatja az SQL Server Management Studiót, és a helyi rendszergazdai hitelesítő adataival csatlakozhat Windows-hitelesítés használatával. Ha engedélyezte az SQL Server-hitelesítést, akkor az SQL-hitelesítésen keresztül is csatlakozhat a kiépítés során megadott SQL bejelentkezési azonosítójával és jelszavával.

A géphez való hozzáférés lehetővé teszi, hogy igény szerint közvetlenül módosítsa a gép és az SQL Server beállításait. Például konfigurálhatja a tűzfal beállításait, vagy módosíthatja az SQL Server-konfiguráció beállításait.