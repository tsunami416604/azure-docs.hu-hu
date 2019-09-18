---
title: Privát végpontok kezelése az Azure-ban
description: Megtudhatja, hogyan hozhat létre privát végpontot a Azure Portal használatával
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: e629a921b5c853f1cae2c224be35570cd71e5d37
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71067798"
---
# <a name="create-a-private-endpoint-using-azure-portal"></a>Privát végpont létrehozása Azure Portal használatával

A privát végpont az Azure-beli privát kapcsolat alapvető építőeleme. Lehetővé teszi az Azure-erőforrások, például a Virtual Machines (VM-EK) számára, hogy magánjellegű módon kommunikáljanak a privát kapcsolati erőforrásokkal. Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre virtuális gépet Azure-Virtual Network, egy Azure Private-végponttal rendelkező SQL Database-kiszolgálót Azure PowerShell használatával. Ezután biztonságosan hozzáférhet a SQL Database-kiszolgálóhoz a virtuális gépről.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-vm"></a>Virtuális gép létrehozása
Ebben a szakaszban létre fog hozni egy virtuális hálózatot és az alhálózatot a privát kapcsolati erőforrás eléréséhez használt virtuális gép (ebben a példában az Azure-beli SQL-kiszolgáló) üzemeltetéséhez.

### <a name="create-the-virtual-network"></a>A virtuális hálózat létrehozása


Ebben a szakaszban létre fog hozni egy Virtual Network és egy alhálózatot, amely a privát kapcsolati erőforrás eléréséhez használt virtuális gépet üzemelteti.

1. A képernyő bal felső részén válassza az **erőforrás** > létrehozása**hálózatkezelés** > **virtuális hálózat**lehetőséget.
1. A **virtuális hálózat létrehozása**lapon adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Name (Név) | Adja meg a *MyVirtualNetwork*. |
    | Címtartomány | Adja meg a *10.1.0.0/16*értéket. |
    | Subscription | Válassza ki előfizetését.|
    | Resource group | Válassza az **új létrehozása**elemet, írja be a *myResourceGroup*, majd kattintson **az OK gombra**. |
    | Location | Válassza a **WestCentralUS**lehetőséget.|
    | Alhálózat – név | Adja meg a *mySubnet*. |
    | Alhálózat – címtartomány | Adja meg a *10.1.0.0/24*értéket. |
    |||
1. Hagyja a többi értéket alapértelmezettként, és válassza a **Létrehozás**lehetőséget.


### <a name="create-virtual-machine"></a>Virtuális gép létrehozása

1. A Azure Portal képernyő bal felső részén válassza az **erőforrás** > létrehozása**számítási** > **virtuális gép**lehetőséget.

1. A **virtuális gép létrehozása – alapismeretek**területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Value |
    | ------- | ----- |
    | **PROJEKT RÉSZLETEI** | |
    | Subscription | Válassza ki előfizetését. |
    | Resource group | Válassza a **myResourceGroup**lehetőséget. Ezt az előző szakaszban hozta létre.  |
    | **PÉLDÁNY RÉSZLETEI** |  |
    | Virtuális gép neve | Adja meg a *myVm*. |
    | Régió | Válassza a **WestCentralUS**lehetőséget. |
    | Rendelkezésre állási beállítások | Az alapértelmezett **infrastruktúra-redundancia megadása nem kötelező**. |
    | Image | Válassza a **Windows Server 2019 Datacenter**lehetőséget. |
    | Size | Hagyja meg az alapértelmezett **standard DS1 v2**értéket. |
    | **RENDSZERGAZDAI FIÓK** |  |
    | Felhasználónév | Adja meg a választott felhasználónevet. |
    | Windows 10 | Adjon meg egy tetszőleges jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Jelszó megerősítése | Adja meg újra a jelszót. |
    | **BEJÖVŐ PORTOK SZABÁLYAI** |  |
    | Nyilvános bejövő portok | Hagyja meg az alapértelmezett **nincs**értéket. |
    | **PÉNZ MEGTAKARÍTÁSA** |  |
    | Már van Windows-licence? | Hagyja meg az alapértelmezett **nem**értéket. |
    |||

1. Válassza **a Next (tovább) lehetőséget: Lemezek**.

1. A **virtuális gép létrehozása – lemezek**területen hagyja meg az alapértelmezett értékeket, és **válassza a Next (tovább) gombot: Hálózatkezelés**.

1. A **virtuálisgép-hálózat létrehozása**területen válassza ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | Virtuális hálózat | Hagyja meg az alapértelmezett **MyVirtualNetwork**.  |
    | Címtartomány | Hagyja meg az alapértelmezett **10.1.0.0/24**értéket.|
    | Subnet | Hagyja meg az alapértelmezett **mySubnet (10.1.0.0/24)** .|
    | Nyilvános IP-cím | Hagyja meg az alapértelmezett **(új) myVm-IP-címet**. |
    | Nyilvános bejövő portok | Válassza a **kiválasztott portok engedélyezése**lehetőséget. |
    | Bejövő portok kiválasztása | Válassza a **http** és az **RDP**lehetőséget.|
    |||


1. Válassza az **Áttekintés + létrehozás** lehetőséget. A **felülvizsgálat + létrehozás** oldalon az Azure ellenőrzi a konfigurációt.

1. Amikor megjelenik az **átadott üzenet ellenőrzése** lehetőség, válassza a **Létrehozás**lehetőséget.

## <a name="create-a-sql-database-server"></a>SQL Database-kiszolgáló létrehozása
Ebben a szakaszban egy SQL Database-kiszolgálót fog létrehozni az Azure-ban. 

1. A Azure Portal képernyő bal felső részén válassza az **erőforrás** > létrehozása**adatbázisok** > **SQL Database**lehetőséget.

1. Az **SQL Database létrehozása – alapok**lapon adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Value |
    | ------- | ----- |
    | **Adatbázis részletei** | |
    | Subscription | Válassza ki előfizetését. |
    | Resource group | Válassza a **myResourceGroup**lehetőséget. Ezt az előző szakaszban hozta létre.|
    | **PÉLDÁNY RÉSZLETEI** |  |
    | Adatbázis neve  | Adja meg a *mydatabase*. Ha ezt a nevet hozza, hozzon létre egy egyedi nevet. |
    |||
5. A **kiszolgáló**területen válassza az **új létrehozása**lehetőséget. 
6. Az **új kiszolgáló**mezőbe írja be vagy válassza ki az alábbi adatokat:

    | Beállítás | Value |
    | ------- | ----- |
    |Kiszolgálónév  | Adja meg a *MyServer*. Ha ezt a nevet hozza, hozzon létre egy egyedi nevet.|
    | Kiszolgáló rendszergazdájának felhasználóneve| Adja meg a választott rendszergazda nevét. |
    | Windows 10 | Adjon meg egy tetszőleges jelszót. A jelszónak legalább 8 karakter hosszúnak kell lennie, és meg kell felelnie a meghatározott követelményeknek. |
    | Location | Válassza ki azt az Azure-régiót, ahol a SQL Server szeretné tárolni. |
    
7. Kattintson **az OK gombra**. 
8. Válassza a **felülvizsgálat + létrehozás**lehetőséget. A **felülvizsgálat + létrehozás** oldalon az Azure ellenőrzi a konfigurációt. 
9. Amikor megjelenik az átadott üzenet ellenőrzése lehetőség, válassza a **Létrehozás**lehetőséget. 
10. Amikor megjelenik az átadott üzenet ellenőrzése lehetőség, válassza a létrehozás lehetőséget. 

## <a name="create-a-private-endpoint"></a>Privát végpont létrehozása

Ebben a szakaszban létre fog hozni egy SQL-kiszolgálót, és hozzá kell adnia egy privát végpontot. 

1. A Azure Portal képernyő bal felső részén válassza az **erőforrás** >  > létrehozása magánhálózat**Private link Center (előzetes verzió)** lehetőséget.
2. A **Private link Centerben – áttekintés**, a **szolgáltatáshoz való magánhálózati kapcsolat**létrehozásához válassza az **Indítás**lehetőséget.
1. A **privát végpont létrehozása (előzetes verzió) – alapismeretek**területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Value |
    | ------- | ----- |
    | **Projekt részletei** | |
    | Subscription | Válassza ki előfizetését. |
    | Resource group | Válassza a **myResourceGroup**lehetőséget. Ezt az előző szakaszban hozta létre.|
    | **PÉLDÁNY RÉSZLETEI** |  |
    | Name (Név) | Adja  * * meg a myPrivateEndpoint. Ha ezt a nevet hozza, hozzon létre egy egyedi nevet. |
    |Régió|Válassza a **WestCentralUS**lehetőséget.|
    |||
5. Válassza **a Next (tovább) lehetőséget: Erőforrás**.
6. A **privát végpont létrehozása – erőforrás**területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Value |
    | ------- | ----- |
    |Kapcsolati módszer  | Válassza a kapcsolódás egy Azure-erőforráshoz a címtárban lehetőséget.|
    | Subscription| Válassza ki előfizetését. |
    | Erőforrás típusa | Válassza a **Microsoft. SQL/kiszolgálók**lehetőséget. |
    | Resource |*MyServer* kiválasztása|
    |Cél alerőforrása |*SqlServer* kiválasztása|
    |||
7. Válassza **a Next (tovább) lehetőséget: Konfiguráció**.
8. A **privát végpont létrehozása (előzetes verzió) – konfiguráció**területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Value |
    | ------- | ----- |
    |**HÁLÓZATI**| |
    | Virtuális hálózat| Válassza a *MyVirtualNetwork*lehetőséget. |
    | Subnet | Válassza a *mySubnet*lehetőséget. |
    |**MAGÁNHÁLÓZATI DNS-INTEGRÁCIÓ**||
    |Integrálás saját DNS-zónával |Válassza az **Igen**lehetőséget. |
    |saját DNS zóna |Válassza az *(új) privatelink. database. Windows. net* elemet |
    |||

1. Válassza a **felülvizsgálat + létrehozás**lehetőséget. A **felülvizsgálat + létrehozás** oldalon az Azure ellenőrzi a konfigurációt. 
2. Amikor megjelenik az **átadott** üzenet ellenőrzése lehetőség, válassza a **Létrehozás**lehetőséget. 
 
## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Kapcsolódás virtuális géphez Távoli asztal (RDP) használatával


A **myVm*létrehozása után az alábbi módon csatlakozhat az internetről: 

1. A portál keresési sávján adja meg a *myVm*.

1. Kattintson a **Csatlakozás** gombra. A **Kapcsolódás** gombra kattintva megnyílik a **virtuális géphez való kapcsolódás** .

1. Válassza ki **RDP-fájl letöltése**. Az Azure létrehoz egy RDP protokoll ( *. rdp*) fájlt, és letölti a számítógépre.

1. Nyissa meg a letöltött. rdp fájlt.

    1. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.

    1. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

        > [!NOTE]
        > Előfordulhat, hogy a virtuális gép létrehozásakor megadott hitelesítő adatok megadásához **több választási lehetőséget** > kell választania**egy másik fiók használatával**.

1. Kattintson az **OK** gombra.

1. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha a tanúsítvány figyelmeztetést kap, válassza az **Igen** vagy a **Folytatás**lehetőséget.

1. Ha megjelenik a virtuális gép asztala, csökkentse a helyi asztalra való visszatérést.  

## <a name="access-the-sql-database-server-privately-from-the-vm"></a>Az SQL Database-kiszolgáló magánhálózati elérése a virtuális gépről

1. A *myVM*távoli asztal nyissa meg a PowerShellt.
2. Adja `nslookup myserver.database.windows.net`meg a értéket. 

    Ehhez hasonló üzenet jelenik meg:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
3. Install [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

4. In **Connect to server**, enter or select this information:

    | Setting | Value |
    | ------- | ----- |
    | Server type| Select **Database Engine**.|
    | Server name| Select *myserver.database.windows.net* |
    | User name | Enter a password provided during the SQL server creation. |
    |Password |Enter a password provided during the SQL server creation. |
    |Remember password|Select **Yes**.|
    |||
1. Select **Connect**.
2. Browse databases from left menu.
3. (Optionally) Create or query information from mydatabase.
4. Close the remote desktop connection to *myVm*. 

## Clean up resources 
When you're done using the private endpoint, SQL server, and the VM, delete the resource group and all of the resources it contains: 
1. Enter *myResourceGroup* in the **Search** box at the top of the portal and select *myResourceGroup* from the search results. 
2. Select **Delete resource group**. 
3. Enter myResourceGroup for **TYPE THE RESOURCE GROUP NAME** and select **Delete**.

## Next steps

In this quickstart, you created a VM on a virtual network, a SQL database server, and a private endpoint for private access. You connected to one VM from the internet and securely communicated to the SQL database server using Private Link. To learn more about private endpoints, see [What is Azure private endpoint?](private-endpoint-overview.md).

