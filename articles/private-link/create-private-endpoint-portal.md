---
title: Rövid útmutató – Privát végpontok kezelése az Azure-ban
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre privát végpontot az Azure Portal használatával
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 1bdc089bb89a732e329bf7d3ffd3d5b5c09ba408
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637245"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-portal"></a>Rövid útmutató: Privát végpont létrehozása az Azure Portal használatával

A privát végpont az Azure-beli privát kapcsolat alapvető építőköve. Lehetővé teszi, hogy az Azure-erőforrások, például a virtuális gépek (VM-ek) privát módon kommunikáljanak a privát kapcsolaterőforrásaival. Ebben a rövid útmutatóban megtudhatja, hogyan hozhat létre virtuális gépet egy Azure virtuális hálózaton, egy SQL Database Server egy Azure-beli privát végpontot az Azure Portalon. Ezután biztonságosan elérheti az SQL Database Server a virtuális gépről.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.


## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-vm"></a>Virtuális gép létrehozása
Ebben a szakaszban virtuális hálózatot és az alhálózatot hoz létre a private link erőforrás (egy SQL-kiszolgáló az Azure-ban ebben a példában) eléréséhez használt virtuális gép üzemeltetéséhez.

## <a name="virtual-network-and-parameters"></a>Virtuális hálózat és paraméterek

Ebben a szakaszban egy virtuális hálózatot és az alhálózatot hoz létre a private link erőforrás eléréséhez használt virtuális gép üzemeltetéséhez.

Ebben a szakaszban a következő paramétereket kell lecserélnie az alábbi információkra:

| Paraméter                   | Érték                |
|-----------------------------|----------------------|
| **\<erőforráscsoport-név>**  | myResourceGroup |
| **\<virtuális hálózat neve>** | myVirtualNetwork          |
| **\<régiónév>**          | USA nyugati középső régiója    |
| **\<IPv4-címtér>**   | 10.1.0.0/16          |
| **\<alhálózat-név>**          | mySubnet        |
| **\<alhálózati címtartomány>** | 10.1.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machine"></a>Virtuális gép létrehozása

1. Az Azure Portal képernyőjének bal felső részén válassza az > **Erőforrás-számítási** > **virtuális gép** **létrehozása**lehetőséget.

1. A **Virtuális gép létrehozása - Alapjai**csoportban adja meg vagy jelölje ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | **A PROJEKT RÉSZLETEI** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup**lehetőséget. Ezt az előző szakaszban hozta létre.  |
    | **PÉLDÁNY részletei** |  |
    | Virtuális gép neve | Adja meg *a myVm*- |
    | Régió | Válassza a **WestCentralUS**lehetőséget. |
    | Rendelkezésre állási beállítások | Hagyja meg az alapértelmezett **Nincs szükség infrastruktúra-redundanciára**. |
    | Kép | Válassza a **Windows Server 2019 Datacenter lehetőséget.** |
    | Méret | Hagyja meg az alapértelmezett **Standard DS1 v2 -t.** |
    | **RENDSZERGAZDAI FIÓK** |  |
    | Felhasználónév | Adja meg az Ön által választott felhasználónevet. |
    | Jelszó | Adjon meg egy tetszőleges jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Jelszó megerősítése | Írja be újra a jelszót. |
    | **BEJÖVŐ PORTSZABÁLYOK** |  |
    | Nyilvános bejövő portok | Hagyja meg az alapértelmezett **Nincs**. |
    | **TAKARÍTSON MEG PÉNZT** |  |
    | Már van Windows-licence? | Hagyja meg az alapértelmezett **Nem értéket.** |
    |||

1. Válassza a **Tovább: Lemezek**lehetőséget.

1. A **Virtuális gép létrehozása – Lemezek**csoportban hagyja meg az alapértelmezett beállításokat, és válassza a **Tovább: Hálózat lehetőséget.**

1. A **Virtuális gép létrehozása - Hálózat csoportban**válassza ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | Virtuális hálózat | Hagyja meg az alapértelmezett **MyVirtualNetwork**.  |
    | Címtér | Hagyja meg az alapértelmezett **10.1.0.0/24**értéket.|
    | Alhálózat | Hagyja meg az alapértelmezett **mySubnet (10.1.0.0/24)**.|
    | Nyilvános IP-cím | Hagyja meg az alapértelmezett **(új) myVm-ip**. |
    | Nyilvános bejövő portok | Válassza **a Kijelölt portok engedélyezése**lehetőséget. |
    | Bejövő portok kiválasztása | Válassza a **HTTP** és **az RDP**lehetőséget.|
    |||


1. Válassza az **Áttekintés + létrehozás** lehetőséget. A véleményezés + **létrehozás** lap, ahol az Azure érvényesíti a konfigurációt.

1. Amikor megjelenik az **Érvényesítési átadott** üzenet, válassza a **Létrehozás gombot.**

## <a name="create-a-sql-database-server"></a>SQL-adatbázis-kiszolgáló létrehozása
Ebben a szakaszban egy SQL-adatbázis-kiszolgálót hoz létre az Azure-ban. 

1. Az Azure Portal képernyőjének bal felső részén válassza az > **Erőforrás-adatbázisok** > **SQL-adatbázis** **ának létrehozása**lehetőséget.

1. Az **SQL-adatbázis létrehozása - Alapjai**mezőbe írja be vagy jelölje ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Adatbázis adatai** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup**lehetőséget. Ezt az előző szakaszban hozta létre.|
    | **PÉLDÁNY részletei** |  |
    | Adatbázis neve  | Adja meg *a saját adatbázist*. Ha ez a név foglalt, hozzon létre egy egyedi nevet. |
    |||
5. A **Kiszolgálóban**válassza **az Új létrehozása lehetőséget.** 
6. Az **Új kiszolgáló mezőbe**írja be vagy jelölje ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    |Kiszolgálónév  | Adja meg *a myserver*. Ha ez a név foglalt, hozzon létre egy egyedi nevet.|
    | Kiszolgáló-rendszergazdai bejelentkezés| Adja meg a választott rendszergazdai nevet. |
    | Jelszó | Adjon meg egy tetszőleges jelszót. A jelszónak legalább 8 karakter hosszúnak kell lennie, és meg kell felelnie a meghatározott követelményeknek. |
    | Hely | Válassza ki azt az Azure-régiót, ahol szeretné, hogy az SQL Server található. |
    
7. Válassza **az OK gombot.** 
8. Válassza az **Áttekintés + létrehozás** lehetőséget. A véleményezés + **létrehozás** lap, ahol az Azure érvényesíti a konfigurációt. 
9. Amikor megjelenik az Érvényesítési átadott üzenet, válassza a **Létrehozás gombot.** 
10. Amikor megjelenik az Érvényesítési átadott üzenet, válassza a Létrehozás gombot. 

## <a name="create-a-private-endpoint"></a>Privát végpont létrehozása

Ebben a szakaszban létre kell hoznia egy SQL-kiszolgálót, és hozzá kell adnia egy privát végpontot. 

1. Az Azure Portal képernyőjének bal felső részén válassza az Erőforrás > létrehozása**hálózati privát** > **kapcsolati központ (előzetes verzió)** **lehetőséget.**
2. A **Privát kapcsolati központban – Áttekintés**, a **szolgáltatáshoz való privát kapcsolat létrehozásának**lehetőségénválassza a **Start**lehetőséget.
1. A **Saját végpont létrehozása (előzetes verzió) – Alapjai**csoportban adja meg vagy jelölje ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Projekt részletei** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup**lehetőséget. Ezt az előző szakaszban hozta létre.|
    | **PÉLDÁNY részletei** |  |
    | Név | Adja meg *a myPrivateEndpoint*. Ha ez a név foglalt, hozzon létre egy egyedi nevet. |
    |Régió|Válassza a **WestCentralUS**lehetőséget.|
    |||
5. Válassza a **Tovább: Erőforrás**lehetőséget.
6. A **Saját végpont - Erőforrás létrehozása**mezőbe írja be vagy jelölje ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    |Kapcsolati módszer  | Válassza ki a csatlakozás egy Azure-erőforrás a címtárban.|
    | Előfizetés| Válassza ki előfizetését. |
    | Erőforrás típusa | Válassza a **Microsoft.Sql/servers**lehetőséget. |
    | Erőforrás |*MyServer* kiválasztása|
    |Cél alerőforrás |*SqlServer* kiválasztása|
    |||
7. Válassza a **Tovább lehetőséget: Konfiguráció**.
8. A **Saját végpont létrehozása (előzetes verzió) – Konfiguráció csoportban**adja meg vagy jelölje ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    |**Hálózati**| |
    | Virtuális hálózat| Válassza a *MyVirtualNetwork*lehetőséget. |
    | Alhálózat | Válassza a *mySubnet*lehetőséget . |
    |**PRIVÁT DNS-INTEGRÁCIÓ**||
    |Integrálás privát DNS-zónával |Válassza az **Igen** lehetőséget. |
    |Privát DNS-zóna |Válassza *ki az (Új)privatelink.database.windows.net lehetőséget* |
    |||

1. Válassza az **Áttekintés + létrehozás** lehetőséget. A véleményezés + **létrehozás** lap, ahol az Azure érvényesíti a konfigurációt. 
2. Amikor megjelenik az **Érvényesítési átadott** üzenet, válassza a **Létrehozás gombot.** 
 
## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Csatlakozás virtuális géphez távoli asztallal (RDP)


Miután létrehozta **myVm**, csatlakozzon hozzá az internetről az alábbiak szerint: 

1. A portál keresősávjában írja be a *myVm*.

1. Kattintson a **Csatlakozás** gombra. A **Csatlakozás** gomb kiválasztása után megnyílik **a Csatlakozás a virtuális géphez.**

1. Válassza **az RDP-fájl letöltése lehetőséget.** Az Azure létrehoz egy Remote Desktop Protocol (*.rdp*) fájlt, és letölti azt a számítógépre.

1. Nyissa meg a *downloaded.rdp* fájlt.

    1. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.

    1. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

        > [!NOTE]
        > Előfordulhat, hogy **további lehetőségek közül** > **választhat: Másik fiók használatával**adja meg a virtuális gép létrehozásakor megadott hitelesítő adatokat.

1. Válassza **az OK gombot.**

1. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha tanúsítványfigyelmeztetést kap, válassza az **Igen** vagy **a Folytatás**lehetőséget.

1. Miután megjelenik a virtuális gép asztala, minimalizálja azt, hogy visszatérjen a helyi asztalra.  

## <a name="access-the-sql-database-server-privately-from-the-vm"></a>Az SQL adatbázis-kiszolgáló elérése a virtuális gépről

1. A *myVM*távoli asztalán nyissa meg a PowerShellt.

2. Írja be a `nslookup myserver.database.windows.net` (igen) kifejezést. 

    A következőhöz hasonló üzenetet fog kapni:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
3. Telepítse [az SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)alkalmazást .

4. A **Csatlakozás a kiszolgálóhoz**mezőbe írja be vagy jelölje ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | Kiszolgáló típusa| Válassza a **Database Engine** (Adatbázismotor) lehetőséget.|
    | Kiszolgálónév| Myserver.database.windows.net *myserver.database.windows.net* kijelölése |
    | Felhasználónév | Adja meg username@servername a felhasználónevet úgy, ahogy az az SQL-kiszolgáló létrehozása során megvan adva. |
    |Jelszó |Adja meg az SQL-kiszolgáló létrehozása során megadott jelszót. |
    |Jelszó megjegyzése|Válassza az **Igen** lehetőséget.|
    |||
1. Kattintson a **Csatlakozás** gombra.
2. Tallózzon az adatbázisok között a bal oldali menüből.
3. (Opcionálisan) Adatok létrehozása vagy lekérdezése a saját adatbázisból.
4. Zárja be a távoli asztali kapcsolatot a *myVm-mel.* 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 
Ha elkészült a privát végpont, az SQL-kiszolgáló és a virtuális gép használatával, törölje az erőforráscsoportot és a benne lévő összes erőforrást: 
1. Írja be a *myResourceGroup* kifejezést a portál tetején lévő **Keresőmezőbe,** és válassza a *myResourceGroup* elemet a keresési eredmények közül. 
2. Válassza az **Erőforráscsoport törlése** elemet. 
3. Írja be a myResourceGroup értéket **az ERŐFORRÁSCSOPORT NEVÉNEK BEÍRÁSához,** és válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban virtuális gépet hozott létre egy virtuális hálózaton, egy SQL-adatbázis-kiszolgálón és egy privát végpontot a magánhozzáféréshez. Egy virtuális géphez csatlakozott az internetről, és biztonságosan kommunikált az SQL-adatbázis-kiszolgálóval a Private Link használatával. Ha többet szeretne megtudni a privát végpontokról, olvassa el [a Mi az Azure privát végpontja.](private-endpoint-overview.md)
