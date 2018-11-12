---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 4d77e9b57301bea30d8a33985071c28e972a81a6
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51264292"
---
### <a name="open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine"></a>A Windows-tűzfal TCP-portjainak megnyitása az Adatbázismotor alapértelmezett példányában
1. Csatlakozzon a virtuális géphez a távoli asztalról. Részletes útmutatás a virtuális géphez való csatlakozásról: [SQL virtuális gép megnyitása a távoli asztalról](../articles/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md#remotedesktop).
2. Miután bejelentkezett, a kezdőképernyőn írja be a **WF.msc**, majd nyomja le az ENTER BILLENTYŰT.
   
    ![A tűzfalprogram elindítása](./media/virtual-machines-sql-server-connection-steps/12Open-WF.png)
3. A **fokozott biztonságú Windows tűzfal** bal oldali ablaktábláján kattintson jobb gombbal a **Bejövő szabályok** elemre, majd a műveletpanelen az **Új szabály** lehetőségre.
   
    ![Új szabály](./media/virtual-machines-sql-server-connection-steps/13New-FW-Rule.png)
4. Az **Új bejövő szabály varázsló** párbeszédpanel **Szabálytípus** területén válassza a **Port** lehetőséget, és kattintson a **Tovább** gombra.
5. A **Protokoll és portok** párbeszédpanelen használja az alapértelmezett **TCP** beállítást. Az **Adott helyi portok** mezőben írja be az Adatbázismotor példányának portszámát (**1433** az alapértelmezett példányhoz, vagy végpont lépésének választott magánhálózati portja).
   
    ![1433-as TCP-port](./media/virtual-machines-sql-server-connection-steps/14Port-1433.png)
6. Kattintson a **Tovább** gombra.
7. A **Művelet** párbeszédpanelen válassza a **Kapcsolat engedélyezése** lehetőséget, majd kattintson a **Tovább** gombra.
   
    **Biztonsági megjegyzés:** A **Csak akkor engedélyezze a kapcsolatot, ha biztonságos** beállítással fokozhatja a biztonságot. Ha a környezethez további biztonsági beállításokat szeretne megadni, válassza ezt a lehetőséget.
   
    ![Kapcsolatok engedélyezése](./media/virtual-machines-sql-server-connection-steps/15Allow-Connection.png)
8. A **Profil** párbeszédpanelen válassza a **Nyilvános**, **Privát** és a **Tartomány** lehetőséget. Ezután kattintson a **Next** (Tovább) gombra.
   
    **Biztonsági megjegyzés:** A **Nyilvános** beállítással engedélyezi az internetelérést. Amikor csak lehetséges, válasszon egy szigorúbb profilt.
   
    ![Nyilvános profil](./media/virtual-machines-sql-server-connection-steps/16Public-Private-Domain-Profile.png)
9. A **Név** párbeszédpanelen írjon be egy nevet és leírást a szabályhoz, majd kattintson a **Befejezés** lehetőségre.
   
    ![Szabály neve](./media/virtual-machines-sql-server-connection-steps/17Rule-Name.png)

Szükség szerint nyisson meg további portokat a többi összetevőhöz. További információ: [A Windows Tűzfal konfigurálása SQL Server-hozzáféréshez](https://msdn.microsoft.com/library/cc646023.aspx).

### <a name="configure-sql-server-to-listen-on-the-tcp-protocol"></a>Az SQL Server konfigurálása a TCP-protokoll figyeléséhez

[!INCLUDE [Enable TCP](virtual-machines-sql-server-connection-tcp-protocol.md)]

### <a name="configure-sql-server-for-mixed-mode-authentication"></a>Az SQL Server konfigurálása vegyes módú hitelesítéshez
Az SQL Server Adatbázismotor nem használhatja a Windows-hitelesítést egy tartománykörnyezet nélkül. Ha az Adatbázismotorhoz egy másik számítógépről szeretne csatlakozni, konfigurálja az SQL Servert vegyes módú hitelesítéshez. A vegyes módú hitelesítés az SQL Server-hitelesítést és a Windows-hitelesítést is lehetővé teszi.

> [!NOTE]
> Előfordulhat, hogy a vegyes módú hitelesítés konfigurálása nem szükséges, ha az Azure Virtual Networköt egy már konfigurált tartománykörnyezettel konfigurálta.
> 
> 

1. A virtuális géphez csatlakozva a kezdőlapon írja be az **SQL Server Management Studio** kifejezést, és kattintson a kijelölt ikonra.
   
    A Management Studio első megnyitásakor annak létre kell hoznia a felhasználó Management Studio-környezetét. Ez eltarthat néhány pillanatig.
2. A Management Studio ezután megjeleníti a **Kapcsolódás a kiszolgálóhoz** párbeszédpanelt. A **Kiszolgálónév** mezőbe írja be a virtuális gép nevét, így az Object Explorerrel kapcsolódhat az Adatbázismotorhoz (A virtuális gép neve helyett a **(helyi)** kifejezést vagy egy pontot is megadhat mint **kiszolgálónév**). Válassza ki **Windows-hitelesítés**, és hagyja ***virtuális_gép_neve * \your_local_administrator** a a **felhasználónév** mezőbe. Kattintson a **Connect** (Csatlakozás) gombra.
   
    ![Csatlakozás kiszolgálóhoz](./media/virtual-machines-sql-server-connection-steps/19Connect-to-Server.png)
3. Az SQL Server Management Studio Object Explorer felületén kattintson jobb gombbal az SQL Server példányának nevére (a virtuális gép nevére), majd a **Tulajdonságok** lehetőségre.
   
    ![Kiszolgáló tulajdonságai](./media/virtual-machines-sql-server-connection-steps/20Server-Properties.png)
4. A **Biztonság** lap **Kiszolgálóhitelesítés** területén válassza az **SQL Server és Windows-hitelesítési mód** lehetőséget, majd kattintson az **OK** gombra.
   
    ![A hitelesítési mód kiválasztása](./media/virtual-machines-sql-server-connection-steps/21Mixed-Mode.png)
5. Az SQL Server Management Studio párbeszédpanelen kattintson az **OK** gombra az SQL Server újraindítását kérő üzenet elfogadásához.
6. Az Object Explorerben kattintson a jobb gombbal a kiszolgálóra, majd az **Újraindítás** lehetőségre. (Ha az SQL Server Agent fut, azt is újra kell indítani.)
   
    ![Újraindítás](./media/virtual-machines-sql-server-connection-steps/22Restart2.png)
7. Az SQL Server Management Studio párbeszédpanelen kattintson az **Igen** lehetőségre az SQL Server újraindításának megerősítését kérő üzenet elfogadásához.

### <a name="create-sql-server-authentication-logins"></a>SQL Server-hitelesítési bejelentkezés létrehozása
Ha az Adatbázismotorhoz egy másik számítógépről szeretne csatlakozni, létre kell hoznia legalább egy SQL Server hitelesítési bejelentkezést.

1. Az SQL Server Management Studio Object Explorer felületén bontsa ki azon kiszolgáló példányának mappáját, amelyben létre szeretné hozni az új bejelentkezést.
2. Kattintson a jobb gombbal a **Biztonság** mappára, mutasson az **Új** lehetőségre, és válassza a **Bejelentkezés...**  lehetőséget.
   
    ![Új bejelentkezés](./media/virtual-machines-sql-server-connection-steps/23New-Login.png)
3. A **Bejelentkezés – Új** párbeszédpanel **Általános** lapján adja meg az új felhasználó nevét a **Bejelentkezési név** mezőben.
4. Kattintson az **SQL Server-hitelesítés** lehetőségre.
5. A **Jelszó** mezőbe írja be az új felhasználó jelszavát. Adja meg újra a jelszót a **Jelszó megerősítése** mezőben.
6. Válassza ki a szükséges jelszókényszerítési beállításokat (**Jelszóházirend kényszerítése**, **Jelszóelévülés kényszerítése** és **A felhasználónak módosítania kell a jelszavát a következő bejelentkezéskor**). Ha ezt a bejelentkezést saját célra használja, nem szükséges jelszómódosítást kérnie a következő bejelentkezéshez.
7. Az **Alapértelmezett adatbázis** listából válasszon ki egy alapértelmezett adatbázist a bejelentkezéshez. Az alapértelmezett beállítás a **master**. Ha még nem hozott létre egy felhasználói adatbázist, hagyja meg a **master** beállítást.
   
    ![Bejelentkezési tulajdonságok](./media/virtual-machines-sql-server-connection-steps/24Test-Login.png)
8. Ha ez az első létrehozott bejelentkezése, célszerű lehet SQL Server-rendszergazdaként megadnia a bejelentkezést. Ebben az esetben a **Kiszolgálói szerepkörök** lapon jelölje be a **sysadmin** jelölőnégyzetet.
   
   > [!NOTE]
   > A sysadmin rögzített kiszolgálói szerepkör tagjai teljes vezérlést kapnak az Adatbázismotor felett. A szerepkör tagságát így gondosan határozza meg.
   > 
   > 
   
   ![sysadmin](./media/virtual-machines-sql-server-connection-steps/25sysadmin.png)
9. Kattintson az OK gombra.

További információ az SQL Server-bejelentkezésekről: [Bejelentkezés létrehozása](https://msdn.microsoft.com/library/aa337562.aspx).

