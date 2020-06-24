---
title: Privát hivatkozás – Azure Portal-Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Megtudhatja, hogyan konfigurálhat Azure Database for PostgreSQL – egyetlen kiszolgáló magánhálózati hivatkozását Azure Portal
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 40e30d4011a52342c6fb610b19f70c8523a0331f
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/12/2020
ms.locfileid: "84736713"
---
# <a name="create-and-manage-private-link-for-azure-database-for-postgresql---single-server-using-portal"></a>Privát hivatkozás létrehozása és kezelése Azure Database for PostgreSQL – egyetlen kiszolgáló számára a portál használatával

A privát végpont az Azure-beli privát kapcsolat alapvető építőeleme. Lehetővé teszi az Azure-erőforrások, például a Virtual Machines (VM-EK) számára, hogy magánjellegű módon kommunikáljanak a privát kapcsolati erőforrásokkal.  Ebből a cikkből megtudhatja, hogyan hozhat létre egy virtuális gépet egy Azure-beli Virtual Networkban és egy Azure Private-végponttal rendelkező Azure Database for PostgreSQL egyetlen kiszolgáló Azure Portal használatával.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> Ez a funkció minden olyan Azure-régióban elérhető, ahol Azure Database for PostgreSQL egyetlen kiszolgáló támogatja a általános célú és a memóriához optimalizált díjszabási szintet.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="create-an-azure-vm"></a>Azure-beli virtuális gép létrehozása

Ebben a szakaszban létre fog hozni egy virtuális hálózatot és az alhálózatot, amely a privát kapcsolati erőforrás (az Azure-beli PostgreSQL-kiszolgáló) elérésére használt virtuális GÉPET üzemelteti.

### <a name="create-the-virtual-network"></a>A virtuális hálózat létrehozása
Ebben a szakaszban létre fog hozni egy Virtual Network és egy alhálózatot, amely a privát kapcsolati erőforrás eléréséhez használt virtuális gépet üzemelteti.

1. A képernyő bal felső részén válassza az **erőforrás létrehozása**  >  **hálózatkezelés**  >  **virtuális hálózat**lehetőséget.
2. A **virtuális hálózat létrehozása**lapon adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Name (Név) | Adja meg a *MyVirtualNetwork*. |
    | Címtér | Adja meg a *10.1.0.0/16*értéket. |
    | Előfizetés | Válassza ki az előfizetését.|
    | Erőforráscsoport | Válassza az **új létrehozása**elemet, írja be a *myResourceGroup*, majd kattintson **az OK gombra**. |
    | Hely | Válassza a **Nyugat-Európa** régiót.|
    | Alhálózat – név | Adja meg a *mySubnet*. |
    | Alhálózat – címtartomány | Adja meg a *10.1.0.0/24*értéket. |
    |||
3. Hagyja a többi értéket alapértelmezettként, és válassza a **Létrehozás**lehetőséget.

### <a name="create-virtual-machine"></a>Virtuális gép létrehozása

1. A Azure Portal képernyő bal felső részén válassza az **erőforrás létrehozása**  >  **számítási**  >  **virtuális gép**lehetőséget.

2. A **virtuális gép létrehozása – alapismeretek**területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **PROJEKT RÉSZLETEI** | |
    | Előfizetés | Válassza ki az előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup**lehetőséget. Ezt az előző szakaszban hozta létre.  |
    | **PÉLDÁNY RÉSZLETEI** |  |
    | Virtuális gép neve | Adja meg a *myVm*. |
    | Régió | Válassza a **Nyugat-Európa** régiót. |
    | Rendelkezésre állási beállítások | Az alapértelmezett **infrastruktúra-redundancia megadása nem kötelező**. |
    | Kép | Válassza a **Windows Server 2019 Datacenter**lehetőséget. |
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
    | Alhálózat | Hagyja meg az alapértelmezett **mySubnet (10.1.0.0/24)**.|
    | Nyilvános IP-cím | Hagyja meg az alapértelmezett **(új) myVm-IP-címet**. |
    | Nyilvános bejövő portok | Válassza a **kiválasztott portok engedélyezése**lehetőséget. |
    | Bejövő portok kiválasztása | Válassza a **http** és az **RDP**lehetőséget.|
    |||


1. Válassza az **Áttekintés + létrehozás** lehetőséget. A **felülvizsgálat + létrehozás** oldalon az Azure ellenőrzi a konfigurációt.

1. Amikor megjelenik az **átadott üzenet ellenőrzése** lehetőség, válassza a **Létrehozás**lehetőséget.

> [!NOTE]
> Bizonyos esetekben a Azure Database for PostgreSQL és a VNet különböző előfizetésekben találhatók. Ezekben az esetekben a következő konfigurációkat kell biztosítania:
> - Győződjön meg arról, hogy mindkét előfizetés regisztrálva van a **Microsoft. DBforPostgreSQL** erőforrás-szolgáltatónál. További információ: [Resource-Manager-regisztráció][resource-manager-portal]

## <a name="create-an-azure-database-for-postgresql-single-server"></a>Azure Database for PostgreSQL önálló kiszolgáló létrehozása

Ebben a szakaszban egy Azure Database for PostgreSQL-kiszolgálót fog létrehozni az Azure-ban. 

1. A Azure Portal képernyő bal felső részén válassza az **erőforrás létrehozása**  >  **adatbázisok**  >  **Azure Database for PostgreSQL**lehetőséget.

1. **Azure Database for PostgreSQL központi telepítés lehetőségnél**válassza az **egyetlen kiszolgáló** lehetőséget, és adja meg a következő információkat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Projekt részletei** | |
    | Előfizetés | Válassza ki az előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup**lehetőséget. Ezt az előző szakaszban hozta létre.|
    | **Kiszolgáló adatai** |  |
    |Kiszolgáló neve  | Adja meg a *MyServer*. Ha ezt a nevet hozza, hozzon létre egy egyedi nevet.|
    | Rendszergazdai Felhasználónév| Adja meg a választott rendszergazda nevét. |
    | Jelszó | Adjon meg egy tetszőleges jelszót. A jelszónak legalább 8 karakter hosszúnak kell lennie, és meg kell felelnie a meghatározott követelményeknek. |
    | Hely | Válassza ki azt az Azure-régiót, ahol a PostgreSQL-kiszolgálót tárolni szeretné. |
    |Verzió  | Válassza ki a szükséges PostgreSQL-kiszolgáló adatbázis-verzióját.|
    | Számítás és tárolás| Válassza ki a kiszolgálón a munkaterhelés alapján szükséges díjszabási szintet. |
    |||
 
7. Válassza az **OK** lehetőséget. 
8. Válassza az **Áttekintés + létrehozás** lehetőséget. A **felülvizsgálat + létrehozás** oldalon az Azure ellenőrzi a konfigurációt. 
9. Amikor megjelenik az átadott üzenet ellenőrzése lehetőség, válassza a **Létrehozás**lehetőséget. 
10. Amikor megjelenik az átadott üzenet ellenőrzése lehetőség, válassza a létrehozás lehetőséget. 

## <a name="create-a-private-endpoint"></a>Privát végpont létrehozása

Ebben a szakaszban létre fog hozni egy PostgreSQL-kiszolgálót, és hozzá kell adnia egy privát végpontot. 

1. A Azure Portal képernyő bal felső részén válassza az **erőforrás létrehozása**  >  **hálózati**  >  **privát hivatkozás**lehetőséget.
2. A **Private link Centerben – áttekintés**, a **szolgáltatáshoz való magánhálózati kapcsolat**létrehozásához válassza az **Indítás**lehetőséget.

    ![Privát hivatkozás áttekintése](media/concepts-data-access-and-security-private-link/privatelink-overview.png)

1. A **privát végpont létrehozása – alapismeretek**területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Projekt részletei** | |
    | Előfizetés | Válassza ki az előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup**lehetőséget. Ezt az előző szakaszban hozta létre.|
    | **Példány adatai** |  |
    | Name | Adja meg a *myPrivateEndpoint*. Ha ezt a nevet hozza, hozzon létre egy egyedi nevet. |
    |Régió|Válassza a **Nyugat-Európa** régiót.|
    |||
5. Válassza a **Tovább: erőforrás**elemet.
6. A **privát végpont létrehozása – erőforrás**területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    |Kapcsolati módszer  | Válassza a kapcsolódás egy Azure-erőforráshoz a címtárban lehetőséget.|
    | Előfizetés| Válassza ki az előfizetését. |
    | Erőforrás típusa | Válassza a **Microsoft. DBforPostgreSQL/kiszolgálók**lehetőséget. |
    | Erőforrás |*MyServer* kiválasztása|
    |Cél alerőforrása |*PostgresqlServer* kiválasztása|
    |||
7. Válassza a **Tovább: konfigurálás**lehetőséget.
8. A **privát végpont létrehozása – konfiguráció**területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    |**HÁLÓZATI**| |
    | Virtuális hálózat| Válassza a *MyVirtualNetwork*lehetőséget. |
    | Alhálózat | Válassza a *mySubnet*lehetőséget. |
    |**MAGÁNHÁLÓZATI DNS-INTEGRÁCIÓ**||
    |Integrálás saját DNS-zónával |Válassza az **Igen** lehetőséget. |
    |saját DNS zóna |Válassza az *(új) privatelink. postgres. database. Azure. com* elemet |
    |||

    > [!Note] 
    > Használja az előre definiált saját DNS-zónát a szolgáltatáshoz, vagy adja meg a kívánt DNS-zóna nevét. A részletekért tekintse meg az [Azure-szolgáltatások DNS-zóna konfigurációját](../private-link/private-endpoint-dns.md) .

1. Válassza az **Áttekintés + létrehozás** lehetőséget. A **felülvizsgálat + létrehozás** oldalon az Azure ellenőrzi a konfigurációt. 
2. Amikor megjelenik az **átadott üzenet ellenőrzése** lehetőség, válassza a **Létrehozás**lehetőséget. 

    ![Saját hivatkozás létrehozva](media/concepts-data-access-and-security-private-link/show-postgres-private-link.png)

    > [!NOTE] 
    > Az ügyfél DNS-beállításában lévő teljes tartománynév nem oldható fel a magánhálózati IP-címekre konfigurálva. Az [itt](../dns/dns-operations-recordsets-portal.md)látható módon konfigurálnia kell egy DNS-zónát a beállított FQDN-hez.

## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Kapcsolódás virtuális géphez Távoli asztal (RDP) használatával


A **myVm**létrehozása után az alábbi módon csatlakozhat az internetről: 

1. A portál keresési sávján adja meg a *myVm*.

1. Kattintson a **Csatlakozás** gombra. A **Kapcsolódás** gombra kattintva megnyílik a **virtuális géphez való kapcsolódás** .

1. Válassza az **RDP-fájl letöltése**lehetőséget. Az Azure létrehoz egy RDP protokoll (*. rdp*) fájlt, és letölti a számítógépre.

1. Nyissa meg a *letöltött. rdp* fájlt.

    1. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.

    1. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

        > [!NOTE]
        > Előfordulhat, hogy a **More choices**  >  virtuális gép létrehozásakor megadott hitelesítő adatok megadásához több választási lehetőséget kell választania**egy másik fiók használatával**.

1. Válassza az **OK** lehetőséget.

1. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha a tanúsítvány figyelmeztetést kap, válassza az **Igen** vagy a **Folytatás**lehetőséget.

1. Ha megjelenik a virtuális gép asztala, csökkentse a helyi asztalra való visszatérést.

## <a name="access-the-postgresql-server-privately-from-the-vm"></a>A PostgreSQL-kiszolgáló elérése a virtuális gépről

1. A *myVM*távoli asztal nyissa meg a PowerShellt.

2. Adja meg a értéket  `nslookup mydemopostgresserver.privatelink.postgres.database.azure.com` . 

    Ehhez hasonló üzenet jelenik meg:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemopostgresserver.privatelink.postgres.database.azure.com
    Address:  10.1.3.4
    ```

3. A PostgreSQL-kiszolgáló magánhálózati kapcsolati kapcsolatának tesztelése bármely elérhető ügyfél használatával. Az alábbi példában az [Azure](https://docs.microsoft.com/sql/azure-data-studio/download?view=sql-server-ver15) -beli adattárat használtuk a művelet végrehajtásához.

4. Az **új kapcsolatok**területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Kiszolgáló típusa| Válassza a **PostgreSQL**lehetőséget.|
    | Kiszolgáló neve| *Mydemopostgresserver.privatelink.postgres.database.Azure.com* kiválasztása |
    | Felhasználónév | Adja meg username@servername a PostgreSQL-kiszolgáló létrehozásakor megadott felhasználónevet. |
    |Jelszó |Adja meg a PostgreSQL-kiszolgáló létrehozásakor megadott jelszót. |
    |SSL|Válassza a **kötelező**lehetőséget.|
    ||

5. Kattintson a Csatlakozás gombra.

6. A bal oldali menüben lévő adatbázisok tallózása.

7. Opcionálisan Információk létrehozása vagy lekérdezése a postgreSQL-kiszolgálóról.

8. A távoli asztali kapcsolat bezárásával myVm.

## <a name="clean-up-resources"></a>Erőforrások felszabadítása
Ha végzett a privát végpont, a PostgreSQL-kiszolgáló és a virtuális gép használatával, törölje az erőforráscsoportot és a benne lévő összes erőforrást:

1. Adja meg a *myResourceGroup*a   portál tetején található **keresőmezőbe** , és válassza a *myResourceGroup*lehetőséget   a keresési eredmények közül.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Írja be **a myResourceGroup nevet az erőforráscsoport neveként** , majd válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben az útmutatóban létrehozott egy virtuális GÉPET egy virtuális hálózaton, egy Azure Database for PostgreSQL-kiszolgálót és egy privát végpontot a privát eléréshez. Az internetről csatlakozik egy virtuális géphez, és biztonságos módon kommunikál a PostgreSQL-kiszolgálóval a privát hivatkozás használatával. További információ a privát végpontokról: [Mi az az Azure Private Endpoint](https://docs.microsoft.com/azure/private-link/private-endpoint-overview).

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md