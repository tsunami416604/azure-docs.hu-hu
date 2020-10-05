---
title: Rövid útmutató – privát végpontok kezelése az Azure-ban
description: Ismerje meg, hogyan hozhat létre privát végpontot a rövid útmutató Azure Portal használatával
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: ef6d49c9046ba04bbac40ec9bf555e12d2faa8f6
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "84021704"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-portal"></a>Rövid útmutató: privát végpont létrehozása Azure Portal használatával

A privát végpont az Azure-beli privát kapcsolat alapvető építőeleme. Lehetővé teszi az Azure-erőforrások, például a Virtual Machines (VM-EK) számára, hogy magánjellegű módon kommunikáljanak a privát kapcsolati erőforrásokkal. Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre virtuális gépet Azure-Virtual Network, egy Azure Private-végponttal rendelkező logikai SQL Servert a Azure Portal használatával. Ezután biztonságosan hozzáférhet SQL Database a virtuális gépről.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.


## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-vm"></a>Virtuális gép létrehozása
Ebben a szakaszban létre fog hozni egy virtuális hálózatot és az alhálózatot a privát kapcsolati erőforrás eléréséhez használt virtuális gép (ebben a példában az Azure-beli SQL-kiszolgáló) üzemeltetéséhez.

## <a name="virtual-network-and-parameters"></a>Virtuális hálózat és paraméterek

Ebben a szakaszban létre fog hozni egy Virtual Network és egy alhálózatot, amely a privát kapcsolati erőforrás eléréséhez használt virtuális gépet üzemelteti.

Ebben a szakaszban le kell cserélnie a következő paramétereket a lépésekben az alábbi információkkal:

| Paraméter                   | Érték                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myVirtualNetwork          |
| **\<region-name>**          | USA nyugati középső régiója    |
| **\<IPv4-address-space>**   | 10.1.0.0/16          |
| **\<subnet-name>**          | mySubnet        |
| **\<subnet-address-range>** | 10.1.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machine"></a>Virtuális gép létrehozása

1. A Azure Portal képernyő bal felső részén válassza az **erőforrás létrehozása**  >  **számítási**  >  **virtuális gép**lehetőséget.

1. A **virtuális gép létrehozása – alapismeretek**területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **PROJEKT RÉSZLETEI** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup** lehetőséget. Ezt az előző szakaszban hozta létre.  |
    | **PÉLDÁNY RÉSZLETEI** |  |
    | Virtuális gép neve | Adja meg a *myVm*. |
    | Region | Válassza a **WestCentralUS**lehetőséget. |
    | Rendelkezésre állási beállítások | Az alapértelmezett **infrastruktúra-redundancia megadása nem kötelező**. |
    | Kép | Válassza a **Windows Server 2019 Datacenter**lehetőséget. |
    | Méret | Hagyja meg az alapértelmezett **standard DS1 v2**értéket. |
    | **RENDSZERGAZDAFIÓK** |  |
    | Felhasználónév | Adja meg a választott felhasználónevet. |
    | Jelszó | Adjon meg egy tetszőleges jelszót. A jelszónak legalább 12 karakter hosszúnak kell lennie, és meg kell felelnie a [meghatározott összetettségi követelményeknek](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Jelszó megerősítése | Adja meg újra a jelszót. |
    | **BEJÖVŐPORT-SZABÁLYOK** |  |
    | Nyilvános bejövő portok | Hagyja meg az alapértelmezett **nincs**értéket. |
    | **MEGTAKARÍTÁSI LEHETŐSÉG** |  |
    | Már van Windows-licence? | Hagyja meg az alapértelmezett **nem**értéket. |
    |||

1. Válassza a **Tovább: lemezek**lehetőséget.

1. A **Virtuális gép létrehozása – Lemezek** lehetőségnél hagyja meg az alapértelmezett értékeket, és válassza a **Tovább: Hálózatkezelés** lehetőséget.

1. A **virtuálisgép-hálózat létrehozása**területen válassza ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | Virtuális hálózat | Hagyja meg az alapértelmezett **MyVirtualNetwork**.  |
    | Címtér | Hagyja meg az alapértelmezett **10.1.0.0/24**értéket.|
    | Alhálózat | Hagyja meg az alapértelmezett **mySubnet (10.1.0.0/24)**.|
    | Nyilvános IP-cím | Hagyja meg az alapértelmezett **(új) myVm-IP-címet**. |
    | Nyilvános bejövő portok | Válassza a **kiválasztott portok engedélyezése**lehetőséget. |
    | Válassza ki a bejövő portokat | Válassza a **http** és az **RDP**lehetőséget.|
    |||


1. Válassza az **Áttekintés + létrehozás** lehetőséget. Az **Áttekintés és létrehozása** lapra kerül, ahol az Azure érvényesíti az Ön konfigurációját.

1. Amikor megjelenik a **Megfelelt az ellenőrzésen** üzenet, válassza a **Létrehozás** lehetőséget.

## <a name="create-a-logical-sql-server"></a>Logikai SQL-kiszolgáló létrehozása

Ebben a szakaszban egy logikai SQL Server-kiszolgálót fog létrehozni az Azure-ban. 

1. A Azure Portal képernyő bal felső részén válassza az **erőforrás létrehozása**  >  **adatbázisok**  >  **SQL Database**lehetőséget.

1. Az **SQL Database létrehozása – alapok**lapon adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Adatbázis adatai** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup** lehetőséget. Ezt az előző szakaszban hozta létre.|
    | **PÉLDÁNY RÉSZLETEI** |  |
    | Adatbázis neve  | Adja meg a *mydatabase*. Ha ezt a nevet hozza, hozzon létre egy egyedi nevet. |
    |||
5. A **kiszolgáló**területen válassza az **új létrehozása**lehetőséget. 
6. Az **új kiszolgáló**mezőbe írja be vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    |Kiszolgálónév  | Adja meg a *MyServer*. Ha ezt a nevet hozza, hozzon létre egy egyedi nevet.|
    | Kiszolgáló-rendszergazdai bejelentkezés| Adja meg a választott rendszergazda nevét. |
    | Jelszó | Adjon meg egy tetszőleges jelszót. A jelszónak legalább 8 karakter hosszúnak kell lennie, és meg kell felelnie a meghatározott követelményeknek. |
    | Hely | Válassza ki azt az Azure-régiót, ahol a SQL Server szeretné tárolni. |
    
7. Válassza az **OK** lehetőséget. 
8. Válassza az **Áttekintés + létrehozás** lehetőséget. Az **Áttekintés és létrehozása** lapra kerül, ahol az Azure érvényesíti az Ön konfigurációját. 
9. Amikor megjelenik a Megfelelt az ellenőrzésen üzenet, válassza a **Létrehozás** lehetőséget. 
10. Amikor megjelenik a Megfelelt az ellenőrzésen üzenet, válassza a Létrehozás lehetőséget. 

## <a name="create-a-private-endpoint"></a>Privát végpont létrehozása

Ebben a szakaszban létre fog hozni egy SQL-kiszolgálót, és hozzá kell adnia egy privát végpontot. 

1. A Azure Portal képernyő bal felső részén válassza az **erőforrás létrehozása**  >  **magánhálózat**  >  **Private link Center (előzetes verzió)** lehetőséget.
2. A **Private link Centerben – áttekintés**, a **szolgáltatáshoz való magánhálózati kapcsolat**létrehozásához válassza az **Indítás**lehetőséget.
1. A **privát végpont létrehozása (előzetes verzió) – alapismeretek**területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Projekt részletei** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup** lehetőséget. Ezt az előző szakaszban hozta létre.|
    | **PÉLDÁNY RÉSZLETEI** |  |
    | Name | Adja meg a *myPrivateEndpoint* nevet. Ha ezt a nevet hozza, hozzon létre egy egyedi nevet. |
    |Region|Válassza a **WestCentralUS**lehetőséget.|
    |||
5. Válassza a **Tovább: erőforrás**elemet.
6. A **privát végpont létrehozása – erőforrás**területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    |Kapcsolati módszer  | Válassza a kapcsolódás egy Azure-erőforráshoz a címtárban lehetőséget.|
    | Előfizetés| Válassza ki előfizetését. |
    | Erőforrás típusa | Válassza a **Microsoft. SQL/kiszolgálók**lehetőséget. |
    | Erőforrás |*MyServer* kiválasztása|
    |Célzott alerőforrás |*SqlServer* kiválasztása|
    |||
7. Válassza a **Tovább: konfigurálás**lehetőséget.
8. A **privát végpont létrehozása (előzetes verzió) – konfiguráció**területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    |**HÁLÓZATKEZELÉS**| |
    | Virtuális hálózat| Válassza a *MyVirtualNetwork*lehetőséget. |
    | Alhálózat | Válassza a *mySubnet*lehetőséget. |
    |**PRIVÁT DNS-INTEGRÁCIÓ**||
    |Integrálás saját DNS-zónával |Válassza az **Igen** lehetőséget. |
    |Privát DNS-zóna |Válassza az *(új) privatelink. database. Windows. net* elemet |
    |||

1. Válassza az **Áttekintés + létrehozás** lehetőséget. Az **Áttekintés és létrehozása** lapra kerül, ahol az Azure érvényesíti az Ön konfigurációját. 
2. Amikor megjelenik a **Megfelelt az ellenőrzésen** üzenet, válassza a **Létrehozás** lehetőséget. 
 
## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Kapcsolódás a virtuális géphez Távoli asztal (RDP) használatával


A **myVm**létrehozása után az alábbi módon csatlakozhat az internetről: 

1. A portál keresési sávjába írja be a *myVm* szöveget.

1. Kattintson a **Csatlakozás** gombra. A **Kapcsolódás** gombra kattintva megnyílik a **virtuális géphez való kapcsolódás** .

1. Válassza az **RDP-fájl letöltése** lehetőséget. Az Azure létrehoz egy RDP protokoll (*. rdp*) fájlt, és letölti a számítógépre.

1. Nyissa meg a *letöltött. rdp* fájlt.

    1. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.

    1. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

        > [!NOTE]
        > Előfordulhat, hogy a **More choices**  >  virtuális gép létrehozásakor megadott hitelesítő adatok megadásához több választási lehetőséget kell választania**egy másik fiók használatával**.

1. Válassza az **OK** lehetőséget.

1. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha a tanúsítvány figyelmeztetést kap, válassza az **Igen** vagy a **Folytatás** lehetőséget.

1. Ha megjelenik a virtuális gép asztala, csökkentse a helyi asztalra való visszatérést.  

## <a name="access-sql-database-privately-from-the-vm"></a>Hozzáférési SQL Database a virtuális gépről

1. A *myVM*távoli asztal nyissa meg a PowerShellt.

2. Írja be a következő szöveget: `nslookup myserver.database.windows.net`. 

    Egy ehhez hasonló üzenet jelenik meg:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
3. Telepítse a [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

4. A **Kapcsolódás a kiszolgálóhoz**lapon adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Server type (Kiszolgáló típusa)| Válassza a **Database Engine** (Adatbázismotor) lehetőséget.|
    | Kiszolgálónév| *MyServer.database.Windows.net* kiválasztása |
    | Felhasználónév | Adja meg a felhasználónevet, username@servername amely az SQL Server létrehozásakor van megadva. |
    |Jelszó |Adja meg az SQL-kiszolgáló létrehozásakor megadott jelszót. |
    |Jelszó megjegyzése|Válassza az **Igen** lehetőséget.|
    |||
1. Kattintson a **Csatlakozás** gombra.
2. A bal oldali menüben lévő adatbázisok tallózása.
3. Opcionálisan Információk létrehozása vagy lekérdezése a mydatabase.
4. A távoli asztali kapcsolat bezárásával *myVm*. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 
Ha végzett a magánhálózati végpont, az SQL Server és a virtuális gép használatával, törölje az erőforráscsoportot és az összes benne lévő erőforrást: 
1. Adja meg a *myResourceGroup* a portál tetején található **keresőmezőbe** , és válassza a *myResourceGroup* lehetőséget a keresési eredmények közül. 
2. Válassza az **Erőforráscsoport törlése** elemet. 
3. Írja be **a myResourceGroup nevet az erőforráscsoport neveként** , majd válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy virtuális GÉPET egy virtuális hálózaton, egy logikai SQL Serveren és egy privát végponton a privát eléréshez. Az internetről csatlakozik egy virtuális géphez, és biztonságosan kommunikál SQL Database privát kapcsolat használatával. További információ a privát végpontokról: [Mi az az Azure Private Endpoint?](private-endpoint-overview.md).
