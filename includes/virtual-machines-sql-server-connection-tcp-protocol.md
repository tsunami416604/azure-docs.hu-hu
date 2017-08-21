1. Miközben a távoli asztalról csatlakozik a virtuális géphez, keressen rá a **Configuration Manager** kifejezésre:

    ![Az SSCM megnyitása](./media/virtual-machines-sql-server-connection-tcp-protocol/sql-server-configuration-manager.png)

1. Az SQL Server Konfigurációkezelő konzolablakában bontsa ki az **SQL Server Network Configuration** elemet.

1. A konzolablakban kattintson az **MSSQLSERVER protokolljai** (az alapértelmezett példány neve) lehetőségre. A részleteket tartalmazó ablaktáblán kattintson a jobb gombbal a **TCP** elemre, majd az **Engedélyezés** lehetőségre, ha még nincs engedélyezve.

    ![A TCP engedélyezése](./media/virtual-machines-sql-server-connection-tcp-protocol/enable-tcp.png)

1. A konzolablakban kattintson az **SQL Server Services** lehetőségre. Az SQL Server példányának leállításához és újraindításához a részleteket tartalmazó ablaktáblán kattintson a jobb gombbal az **SQL Server (*példánynév*)** (az alapértelmezett példány az **SQL Server (MSSQLSERVER)**) lehetőségre, majd az **Újraindítás** lehetőségre.

    ![Az Adatbázismotor újraindítása](./media/virtual-machines-sql-server-connection-tcp-protocol/restart-sql-server.png)

1. Zárja be az SQL Server Konfigurációkezelőt.

További információ az SQL Server Adatbázismotor protokolljainak engedélyezéséről: [Kiszolgálói hálózati protokoll engedélyezése vagy letiltása](http://msdn.microsoft.com/library/ms191294.aspx).
