---
title: Privát hivatkozás – Azure-portál – Azure-adatbázis a MariaDB-hez
description: Megtudhatja, hogy miként konfigurálhatja a MariaDB Azure Database privát hivatkozását az Azure Portalról
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 3f421cad64caf91b898bb1ec13dc909b93b7f72d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370338"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mariadb-using-portal"></a>Privát kapcsolat létrehozása és kezelése a MariaDB-hez a MariaDB-hez a Portál használatával

A privát végpont az Azure-beli privát kapcsolat alapvető építőköve. Lehetővé teszi, hogy az Azure-erőforrások, például a virtuális gépek (VM-ek) privát módon kommunikáljanak a privát kapcsolaterőforrásaival.  Ebben a cikkben megtudhatja, hogyan használhatja az Azure Portalon egy virtuális gépet egy Azure virtuális hálózatban és egy Azure Database for MariaDB-kiszolgálót egy Azure-beli privát végpontdal.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

> [!NOTE]
> Ez a funkció minden Olyan Azure-régióban elérhető, ahol az Azure Database for MariaDB támogatja az általános célú és a memóriaoptimalizált tarifacsomagokat.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

## <a name="create-an-azure-vm"></a>Azure-beli virtuális gép létrehozása

Ebben a szakaszban virtuális hálózatot és az alhálózatot hoz létre a private link erőforrás (az Azure-beli MariaDB-kiszolgáló) eléréséhez használt virtuális gép üzemeltetéséhez.

### <a name="create-the-virtual-network"></a>A virtuális hálózat létrehozása
Ebben a szakaszban egy virtuális hálózatot és az alhálózatot hoz létre a private link erőforrás eléréséhez használt virtuális gép üzemeltetéséhez.

1. A képernyő bal felső részén válassza az **Erőforrás** > **létrehozása hálózati** > **virtuális hálózat**lehetőséget.
2. A **Virtuális hálózat létrehozása**mezőbe írja be vagy jelölje ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | Név | Írja be *a MyVirtualNetwork*. |
    | Címtér | Írja be *a 10.1.0.0/16*értéket . |
    | Előfizetés | Válassza ki előfizetését.|
    | Erőforráscsoport | Válassza **az Új létrehozása**lehetőséget, írja be a *myResourceGroup parancsot,* majd kattintson **az OK gombra.** |
    | Hely | Válassza a **Nyugat-Európa** régiót.|
    | Alhálózat - név | Adja meg *a mySubnet*. |
    | Alhálózat – címtartomány | Írja be *a 10.1.0.0/24*értéket . |
    |||
3. Hagyja a többit alapértelmezettként, és válassza a **Létrehozás lehetőséget.**

### <a name="create-virtual-machine"></a>Virtuális gép létrehozása

1. Az Azure Portal képernyőjének bal felső részén válassza az > **Erőforrás-számítási** > **virtuális gép** **létrehozása**lehetőséget.

2. A **Virtuális gép létrehozása - Alapjai**csoportban adja meg vagy jelölje ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | **A PROJEKT RÉSZLETEI** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup**lehetőséget. Ezt az előző szakaszban hozta létre.  |
    | **PÉLDÁNY részletei** |  |
    | Virtuális gép neve | Adja meg *a myVm*- |
    | Régió | Válassza a **Nyugat-Európa** régiót. |
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

## <a name="create-an-azure-database-for-mariadb"></a>Azure Database for MariaDB létrehozása

Ebben a szakaszban létrehoz egy Azure-adatbázist a MariaDB-kiszolgálóhoz az Azure-ban. 

1. Az Azure Portal képernyőjének bal felső részén válassza az Erőforrás-adatbázisok > **Databases** > **létrehozása Azure-adatbázis**létrehozása a MariaDB számára **lehetőséget.**

1. Az **Azure Database for MariaDB a** következő információkat adja meg:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Projekt részletei** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup**lehetőséget. Ezt az előző szakaszban hozta létre.|
    | **Kiszolgáló adatai** |  |
    |Kiszolgálónév  | Adja meg *a myserver*. Ha ez a név foglalt, hozzon létre egy egyedi nevet.|
    | Rendszergazdai felhasználónév| Adja meg a választott rendszergazdai nevet. |
    | Jelszó | Adjon meg egy tetszőleges jelszót. A jelszónak legalább 8 karakter hosszúnak kell lennie, és meg kell felelnie a meghatározott követelményeknek. |
    | Hely | Válassza ki azt az Azure-régiót, ahol szeretné, hogy a MariaDB-kiszolgáló található legyen. |
    |Verzió  | Válassza ki a MariaDB-kiszolgáló szükséges adatbázisverzióját.|
    | Számítás + Tárolás| Válassza ki a kiszolgálóhoz a munkaterhelés alapján szükséges tarifacsomagot. |
    |||
 
7. Válassza **az OK gombot.** 
8. Válassza az **Áttekintés + létrehozás** lehetőséget. A véleményezés + **létrehozás** lap, ahol az Azure érvényesíti a konfigurációt. 
9. Amikor megjelenik az Érvényesítési átadott üzenet, válassza a **Létrehozás gombot.** 
10. Amikor megjelenik az Érvényesítési átadott üzenet, válassza a Létrehozás gombot. 

## <a name="create-a-private-endpoint"></a>Privát végpont létrehozása

Ebben a szakaszban létre kell hoznia egy privát végpontot a MariaDB-kiszolgálóhoz. 

1. Az Azure Portal képernyőjének bal felső részén válassza az Erőforrás > létrehozása**hálózati** > **magánkapcsolat** **lehetőséget.**
2. A **Privát kapcsolati központban – Áttekintés**, a **szolgáltatáshoz való privát kapcsolat létrehozásának**lehetőségénválassza a **Start**lehetőséget.

    ![Privát hivatkozás – áttekintés](media/concepts-data-access-and-security-private-link/privatelink-overview.png)

1. A **Saját végpont létrehozása - Alapjai**mezőbe írja be vagy jelölje ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Projekt részletei** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup**lehetőséget. Ezt az előző szakaszban hozta létre.|
    | **Példány adatai** |  |
    | Név | Adja meg *a myPrivateEndpoint*. Ha ez a név foglalt, hozzon létre egy egyedi nevet. |
    |Régió|Válassza a **Nyugat-Európa** régiót.|
    |||
5. Válassza a **Tovább: Erőforrás**lehetőséget.
6. A **Saját végpont - Erőforrás létrehozása**mezőbe írja be vagy jelölje ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    |Kapcsolati módszer  | Válassza ki a csatlakozás egy Azure-erőforrás a címtárban.|
    | Előfizetés| Válassza ki előfizetését. |
    | Erőforrás típusa | Válassza a **Microsoft.DBforMariaDB/servers lehetőséget.** |
    | Erőforrás |*MyServer* kiválasztása|
    |Cél alerőforrás |Válassza *a mariadbServer lehetőséget*|
    |||
7. Válassza a **Tovább lehetőséget: Konfiguráció**.
8. A **Saját végpont létrehozása - Konfiguráció**csoportban adja meg vagy jelölje ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    |**Hálózati**| |
    | Virtuális hálózat| Válassza a *MyVirtualNetwork*lehetőséget. |
    | Alhálózat | Válassza a *mySubnet*lehetőséget . |
    |**PRIVÁT DNS-INTEGRÁCIÓ**||
    |Integrálás privát DNS-zónával |Válassza az **Igen** lehetőséget. |
    |Privát DNS-zóna |Válassza *ki az (új)privatelink.mariadb.database.azure.com lehetőséget* |
    |||

1. Válassza az **Áttekintés + létrehozás** lehetőséget. A véleményezés + **létrehozás** lap, ahol az Azure érvényesíti a konfigurációt. 
2. Amikor megjelenik az **Érvényesítési átadott** üzenet, válassza a **Létrehozás gombot.** 

    ![Privát hivatkozás létrehozva](media/concepts-data-access-and-security-private-link/show-mariadb-private-link.png)

    > [!NOTE] 
    > Az ügyfél DNS-beállításában lévő teljes tartományna nem oldódik fel a beállított privát IP-címre. Be kell állítania egy DNS-zónát a konfigurált teljes tartománynna számára az [itt](../dns/dns-operations-recordsets-portal.md)látható módon.

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

## <a name="access-the-mariadb-server-privately-from-the-vm"></a>A MariaDB-kiszolgáló elérése a virtuális gépről

1. A *myVM*távoli asztalán nyissa meg a PowerShellt.

2. Írja `nslookup mydemomserver.privatelink.mariadb.database.azure.com`be a be. 

    A következőhöz hasonló üzenetet fog kapni:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemoMariaDBserver.privatelink.mariadb.database.azure.com
    Address:  10.1.3.4
    ```

3. Tesztelje a MariaDB-kiszolgáló privát kapcsolatát bármely elérhető ügyfél használatával. Az alábbi példában a [MySQL Workbench-et](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) használtam a művelethez.


4. Az **Új kapcsolat ban**adja meg vagy jelölje ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | Kiszolgáló típusa| Válassza a **MariaDB**lehetőséget.|
    | Kiszolgálónév| *mydemoserver.privatelink.mariadb.database.azure.com* kijelölése |
    | Felhasználónév | Adja meg username@servername a felhasználónevet úgy, ahogy az a MariaDB-kiszolgáló létrehozása során biztosított. |
    |Jelszó |Adja meg a MariaDB-kiszolgáló létrehozása során megadott jelszót. |
    |SSL|Válassza a **Kötelező**lehetőséget.|
    ||

5. Válassza **a Kapcsolat tesztelése** vagy **az OK lehetőséget.**

6. (Opcionálisan) Adatbázisok tallózása a bal oldali menüből és A MariaDB adatbázisból származó adatok létrehozása vagy lekérdezése

7. Zárja be a távoli asztali kapcsolatot a myVm-mel.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha elkészült a privát végpont, a MariaDB-kiszolgáló és a virtuális gép használatával, törölje az erőforráscsoportot és a benne lévő összes erőforrást:

1. Írja be a *myResourceGroup* kifejezést a portál tetején lévő **Keresőmezőbe,** és válassza a *myResourceGroup* elemet a keresési eredmények közül.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Írja be a myResourceGroup értéket **az ERŐFORRÁSCSOPORT NEVÉNEK BEÍRÁSához,** és válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben az útmutatóban virtuális gépet hozott létre egy virtuális hálózaton, egy Azure-adatbázist a MariaDB-hez, és egy privát végpontot a privát hozzáféréshez. Ön az internetről egy virtuális géphez csatlakozott, és a Privát hivatkozás használatával biztonságosan kommunikált a MariaDB-kiszolgálóval. A privát végpontokról a [Mi az Azure privát végpontja.](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)
