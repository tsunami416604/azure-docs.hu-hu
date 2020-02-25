---
title: Privát hivatkozás a Azure Database for MySQL (előzetes verzió) portál telepítési módszeréhez
description: Megtudhatja, hogyan konfigurálhatja a Azure Database for MySQLhoz tartozó magánhálózati hivatkozást Azure Portal
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 193227ff98accb31c15e388c810aea7460a2cb5d
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2020
ms.locfileid: "77561663"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mysql-preview-using-portal"></a>Azure Database for MySQL (előzetes verzió) privát hivatkozásának létrehozása és kezelése a portál használatával

A privát végpont az Azure-beli privát kapcsolat alapvető építőeleme. Lehetővé teszi az Azure-erőforrások, például a Virtual Machines (VM-EK) számára, hogy magánjellegű módon kommunikáljanak a privát kapcsolati erőforrásokkal. Ebből a cikkből megtudhatja, hogyan hozhat létre virtuális gépet egy Azure-Virtual Networkban és egy Azure-beli privát végponttal rendelkező Azure Database for MySQL-kiszolgálón a Azure Portal használatával.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

> [!NOTE]
> Ez a funkció minden olyan Azure-régióban elérhető, ahol a Azure Database for MySQL támogatja a általános célú és a memória optimalizált díjszabási szintjeit.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Jelentkezzen be az [Azure Portal](https://portal.azure.com).

## <a name="create-an-azure-vm"></a>Azure-beli virtuális gép létrehozása

Ebben a szakaszban létre fog hozni egy virtuális hálózatot és az alhálózatot, amely a privát kapcsolati erőforráshoz (az Azure-ban MySQL-kiszolgálóhoz) való hozzáféréshez használt virtuális GÉPET üzemelteti.

### <a name="create-the-virtual-network"></a>A virtuális hálózat létrehozása
Ebben a szakaszban létre fog hozni egy Virtual Network és egy alhálózatot, amely a privát kapcsolati erőforrás eléréséhez használt virtuális gépet üzemelteti.

1. A képernyő bal felső részén válassza az **erőforrás létrehozása** > **hálózatkezelés** > **virtuális hálózat**lehetőséget.
2. A **virtuális hálózat létrehozása**lapon adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Név | Adja meg a *MyVirtualNetwork*. |
    | Címtér | Adja meg a *10.1.0.0/16*értéket. |
    | Előfizetés | Válassza ki előfizetését.|
    | Erőforráscsoport | Válassza az **új létrehozása**elemet, írja be a *myResourceGroup*, majd kattintson **az OK gombra**. |
    | Hely | Válassza a **Nyugat-Európa** régiót.|
    | Alhálózat – név | Adja meg a *mySubnet*. |
    | Alhálózat – címtartomány | Adja meg a *10.1.0.0/24*értéket. |
    |||
3. Hagyja a többi értéket alapértelmezettként, és válassza a **Létrehozás**lehetőséget.

### <a name="create-virtual-machine"></a>Virtuális gép létrehozása

1. A Azure Portal képernyő bal felső részén válassza az **erőforrás létrehozása** > **számítási** > **virtuális gép**lehetőséget.

2. A **virtuális gép létrehozása – alapismeretek**területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **PROJEKT RÉSZLETEI** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup**lehetőséget. Ezt az előző szakaszban hozta létre.  |
    | **PÉLDÁNY RÉSZLETEI** |  |
    | Virtuális gép neve | Adja meg a *myVm*. |
    | Régió | Válassza a **Nyugat-Európa** régiót. |
    | Rendelkezésre állási beállítások | Az alapértelmezett **infrastruktúra-redundancia megadása nem kötelező**. |
    | Image (Kép) | Válassza a **Windows Server 2019 Datacenter**lehetőséget. |
    | Méret | Hagyja meg az alapértelmezett **standard DS1 v2**értéket. |
    | **RENDSZERGAZDAI FIÓK** |  |
    | Felhasználónév | Adja meg a választott felhasználónevet. |
    | Jelszó | Adjon meg egy tetszőleges jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Jelszó megerősítése | Adja meg újra a jelszót. |
    | **BEJÖVŐ PORTOK SZABÁLYAI** |  |
    | Nyilvános bejövő portok | Hagyja meg az alapértelmezett **nincs**értéket. |
    | **PÉNZ MEGTAKARÍTÁSA** |  |
    | Már van Windows-licence? | Hagyja meg az alapértelmezett **nem**értéket. |
    |||

1. Válassza a **Tovább: lemezek**lehetőséget.

1. A **virtuális gép létrehozása – lemezek**területen hagyja meg az alapértelmezett értékeket, és válassza a **Tovább: hálózatkezelés**lehetőséget.

1. A **virtuálisgép-hálózat létrehozása**területen válassza ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | Virtuális hálózat | Hagyja meg az alapértelmezett **MyVirtualNetwork**.  |
    | Címtér | Hagyja meg az alapértelmezett **10.1.0.0/24**értéket.|
    | Subnet | Hagyja meg az alapértelmezett **mySubnet (10.1.0.0/24)** .|
    | Nyilvános IP-cím | Hagyja meg az alapértelmezett **(új) myVm-IP-címet**. |
    | Nyilvános bejövő portok | Válassza a **kiválasztott portok engedélyezése**lehetőséget. |
    | Bejövő portok kiválasztása | Válassza a **http** és az **RDP**lehetőséget.|
    |||


1. Válassza az **Áttekintés + létrehozás** lehetőséget. A **felülvizsgálat + létrehozás** oldalon az Azure ellenőrzi a konfigurációt.

1. Amikor megjelenik az **átadott üzenet ellenőrzése** lehetőség, válassza a **Létrehozás**lehetőséget.

## <a name="create-an-azure-database-for-mysql"></a>Azure Database for MySQL létrehozása

Ebben a szakaszban egy Azure Database for MySQL-kiszolgálót fog létrehozni az Azure-ban. 

1. A Azure Portal képernyő bal felső részén válassza az **erőforrás létrehozása** > **adatbázisok** > **Azure Database for MySQL**lehetőséget.

1. A **Azure Database for MySQL** adja meg a következő információkat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Projekt részletei** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup**lehetőséget. Ezt az előző szakaszban hozta létre.|
    | **Kiszolgáló adatai** |  |
    |Kiszolgálónév  | Adja meg a *myServer*. Ha ezt a nevet hozza, hozzon létre egy egyedi nevet.|
    | Rendszergazdai Felhasználónév| Adja meg a választott rendszergazda nevét. |
    | Jelszó | Adjon meg egy tetszőleges jelszót. A jelszónak legalább 8 karakter hosszúnak kell lennie, és meg kell felelnie a meghatározott követelményeknek. |
    | Hely | Válassza ki azt az Azure-régiót, ahol a MySQL-kiszolgálót szeretné tárolni. |
    |Verzió  | Válassza ki a szükséges MySQL-kiszolgáló adatbázis-verzióját.|
    | Számítás és tárolás| Válassza ki a kiszolgálón a munkaterhelés alapján szükséges díjszabási szintet. |
    |||
 
7. Kattintson az **OK** gombra. 
8. Válassza az **Áttekintés + létrehozás** lehetőséget. A **felülvizsgálat + létrehozás** oldalon az Azure ellenőrzi a konfigurációt. 
9. Amikor megjelenik az átadott üzenet ellenőrzése lehetőség, válassza a **Létrehozás**lehetőséget. 
10. Amikor megjelenik az átadott üzenet ellenőrzése lehetőség, válassza a létrehozás lehetőséget. 

## <a name="create-a-private-endpoint"></a>Privát végpont létrehozása

Ebben a szakaszban létre fog hozni egy MySQL-kiszolgálót, és hozzá kell adnia egy privát végpontot. 

1. A Azure Portal képernyő bal felső részén válassza az **erőforrás létrehozása** > **hálózatkezelés** > **Private link Center (előzetes verzió)** lehetőséget.

2. A **Private link Centerben – áttekintés**, a **szolgáltatáshoz való magánhálózati kapcsolat**létrehozásához válassza az **Indítás**lehetőséget.

    ![Privát hivatkozás áttekintése](media/concepts-data-access-and-security-private-link/privatelink-overview.png)

1. A **privát végpont létrehozása (előzetes verzió) – alapismeretek**területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Projekt részletei** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup**lehetőséget. Ezt az előző szakaszban hozta létre.|
    | **Példány részletei** |  |
    | Név | Adja meg a *myPrivateEndpoint*. Ha ezt a nevet hozza, hozzon létre egy egyedi nevet. |
    |Régió|Válassza a **Nyugat-Európa** régiót.|
    |||

5. Válassza a **Tovább: erőforrás**elemet.
6. A **privát végpont létrehozása – erőforrás**területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    |Kapcsolati módszer  | Válassza a kapcsolódás egy Azure-erőforráshoz a címtárban lehetőséget.|
    | Előfizetés| Válassza ki előfizetését. |
    | Erőforrás típusa | Válassza a **Microsoft. DBforMySQL/kiszolgálók**lehetőséget. |
    | Resource |*MyServer* kiválasztása|
    |Cél alerőforrása |*Portra beállított mysqlserver* kiválasztása|
    |||
7. Válassza a **Tovább: konfigurálás**lehetőséget.
8. A **privát végpont létrehozása (előzetes verzió) – konfiguráció**területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    |**HÁLÓZATI**| |
    | Virtuális hálózat| Válassza a *MyVirtualNetwork*lehetőséget. |
    | Subnet | Válassza a *mySubnet*lehetőséget. |
    |**MAGÁNHÁLÓZATI DNS-INTEGRÁCIÓ**||
    |Integrálás saját DNS-zónával |Válassza az **Igen** lehetőséget. |
    |saját DNS zóna |Válassza az *(új) privatelink. mysql. database. Azure. com* elemet |
    |||

1. Válassza az **Áttekintés + létrehozás** lehetőséget. A **felülvizsgálat + létrehozás** oldalon az Azure ellenőrzi a konfigurációt. 
2. Amikor megjelenik az **átadott üzenet ellenőrzése** lehetőség, válassza a **Létrehozás**lehetőséget. 

    ![Saját hivatkozás létrehozva](media/concepts-data-access-and-security-private-link/show-mysql-private-link.png)

    > [!NOTE] 
    > Az ügyfél DNS-beállításában lévő teljes tartománynév nem oldható fel a magánhálózati IP-címekre konfigurálva. Az [itt](../dns/dns-operations-recordsets-portal.md)látható módon konfigurálnia kell egy DNS-zónát a beállított FQDN-hez.

## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Kapcsolódás virtuális géphez Távoli asztal (RDP) használatával


A **myVm**létrehozása után az alábbi módon csatlakozhat az internetről: 

1. A portál keresési sávján adja meg a *myVm*.

1. Kattintson a **Csatlakozás** gombra. A **Kapcsolódás** gombra kattintva megnyílik a **virtuális géphez való kapcsolódás** .

1. Válassza az **RDP-fájl letöltése**lehetőséget. Az Azure létrehoz egy RDP protokoll ( *. rdp*) fájlt, és letölti a számítógépre.

1. Nyissa meg a *letöltött. rdp* fájlt.

    1. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.

    1. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

        > [!NOTE]
        > Előfordulhat, hogy a virtuális gép létrehozásakor megadott hitelesítő adatok megadásához **több választási lehetőséget** kell kiválasztania > **eltérő fiókot használjon**.

1. Kattintson az **OK** gombra.

1. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha a tanúsítvány figyelmeztetést kap, válassza az **Igen** vagy a **Folytatás**lehetőséget.

1. Ha megjelenik a virtuális gép asztala, csökkentse a helyi asztalra való visszatérést.

## <a name="access-the-mysql-server-privately-from-the-vm"></a>A MySQL-kiszolgáló elérése a virtuális gépről

1. A *myVM*távoli asztal nyissa meg a PowerShellt.

2. Adja meg a `nslookup  myServer.privatelink.mysql.database.azure.com`. 

    Ehhez hasonló üzenet jelenik meg:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myServer.privatelink.mysql.database.azure.com
    Address:  10.1.3.4
    ```

3. Tesztelje a MySQL-kiszolgáló magánhálózati kapcsolati kapcsolatát bármely elérhető ügyfél használatával. Az alábbi példában a [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) használatával végeztem el a műveletet.

4. Az **új kapcsolatok**területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Kiszolgáló típusa| Válassza a **MySQL**lehetőséget.|
    | Kiszolgálónév| *MyServer.privatelink.mysql.database.Azure.com* kiválasztása |
    | Felhasználónév | Adja meg a felhasználónevet username@servername, amelyet a MySQL-kiszolgáló létrehozásakor adott meg. |
    |Jelszó |Adja meg a MySQL-kiszolgáló létrehozásakor megadott jelszót. |
    |SSL|Válassza a **kötelező**lehetőséget.|
    ||

5. Válassza a kapcsolat lehetőséget.

6. A bal oldali menüben lévő adatbázisok tallózása.

7. Opcionálisan Információk létrehozása vagy lekérdezése a MySQL-kiszolgálóról.

8. A távoli asztali kapcsolat bezárásával myVm.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha végzett a magánhálózati végpont, a MySQL-kiszolgáló és a virtuális gép használatával, törölje az erőforráscsoportot és az összes benne lévő erőforrást:

1. Adja meg a *myResourceGroup* a portál tetején található **keresőmezőbe** , és válassza ki a *myResourceGroup* a keresési eredmények közül.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Írja be **a myResourceGroup nevet az erőforráscsoport neveként** , majd válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben az útmutatóban létrehozott egy virtuális GÉPET egy virtuális hálózaton, egy Azure Database for MySQLt és egy privát végpontot a privát eléréshez. Az internetről csatlakozik egy virtuális géphez, és biztonságosan kommunikál a MySQL-kiszolgálóval a privát hivatkozás használatával. További információ a privát végpontokról: [Mi az az Azure Private Endpoint](https://docs.microsoft.com/azure/private-link/private-endpoint-overview).
