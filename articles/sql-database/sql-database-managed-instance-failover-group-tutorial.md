---
title: 'Oktatóanyag: SQL Database felügyelt példány hozzáadása feladatátvételi csoporthoz'
description: Megtudhatja, hogyan konfigurálhat egy feladatátvételi csoportot a Azure SQL Database felügyelt példányához.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sashan, carlrab
manager: jroth
ms.date: 06/27/2019
ms.openlocfilehash: 04802b8b25ca21cc0099874e5a9ea69748868f6e
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71103212"
---
# <a name="tutorial-add-a-sql-database-managed-instance-to-a-failover-group"></a>Oktatóanyag: SQL Database felügyelt példány hozzáadása feladatátvételi csoporthoz

SQL Database felügyelt példány hozzáadása egy feladatátvételi csoporthoz. Ez a cikk azt ismerteti, hogyan lehet:

> [!div class="checklist"]
> - Elsődleges felügyelt példány létrehozása
> - Hozzon létre egy másodlagos felügyelt példányt egy [feladatátvételi csoport](sql-database-auto-failover-group.md)részeként. 
> - Feladatátvételi teszt

  > [!NOTE]
  > - Ha ezt az oktatóanyagot hajtja végre, győződjön meg arról, hogy az erőforrásokat a [felügyelt példányhoz tartozó feladatátvételi csoportok beállításának előfeltételei](sql-database-auto-failover-group.md#enabling-geo-replication-between-managed-instances-and-their-vnets)szerint konfigurálja. 
  > - A felügyelt példányok létrehozása jelentős időt vehet igénybe. Ennek eredményeképpen ez az oktatóanyag több órát is igénybe vehet. További információ a kiépítési időszakokról: [felügyelt példányok kezelési műveletei](sql-database-managed-instance.md#managed-instance-management-operations). 


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik a következőkkel: 

- Egy Azure-előfizetés, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) , ha még nem rendelkezik ilyennel. 


## <a name="1---create-resource-group-and-primary-managed-instance"></a>1 – erőforráscsoport és elsődleges felügyelt példány létrehozása
Ebben a lépésben létrehozza az erőforráscsoportot és a feladatátvételi csoport elsődleges felügyelt példányát a Azure Portal használatával. 

1. Válassza az **Azure SQL** lehetőséget a Azure Portal bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **minden szolgáltatás**lehetőséget, majd írja be az Azure SQL kifejezést a keresőmezőbe. Választható Válassza ki az **Azure SQL** melletti csillagot a kedvencekhez, és adja hozzá elemként a bal oldali navigációs sávon. 
1. Válassza a **+ Hozzáadás** lehetőséget az **SQL-telepítés kiválasztása** lap megnyitásához. A különböző adatbázisokkal kapcsolatos további információkat az adatbázisok csempén a Részletek megjelenítése lehetőség kiválasztásával tekintheti meg.
1. Válassza a **Létrehozás** lehetőséget a **felügyelt SQL-példányok** csempén. 

    ![Felügyelt példány kiválasztása](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. A **Azure SQL Database felügyelt példány létrehozása** lap **alapok** lapján
    1. A **Project Details (projekt részletei**) területen válassza ki az **előfizetését** a legördülő menüből, majd válassza az **Új erőforráscsoport létrehozása** lehetőséget. Írja be az erőforráscsoport nevét, például `myResourceGroup`:. 
    1. A **felügyelt példány részletei**területen adja meg a felügyelt példány nevét, valamint azt a régiót, ahol a felügyelt példányt telepíteni szeretné. Hagyja meg a számítási és a **tárolási** értékeket az alapértelmezett értékeken. 
    1. A **rendszergazdai fiók**területen adjon meg egy rendszergazdai bejelentkezési azonosítót, `azureuser`például, és egy összetett rendszergazdai jelszót. 

    ![Elsődleges MI létrehozása](media/sql-database-managed-instance-failover-group-tutorial/primary-sql-mi-values.png)

1. Hagyja meg a többi beállítást az alapértelmezett értékeken, majd válassza a **felülvizsgálat + létrehozás** lehetőséget a felügyelt példányok beállításainak áttekintéséhez. 
1. Válassza a **Létrehozás** lehetőséget az elsődleges felügyelt példány létrehozásához. 


## <a name="2---create-a-virtual-network"></a>2 – virtuális hálózat létrehozása
Ebben a lépésben létre fog hozni egy virtuális hálózatot a másodlagos felügyelt példányhoz. Erre a lépésre azért van szükség, mert az elsődleges és a másodlagos felügyelt példányok alhálózata nem átfedésben lévő címtartományt tartalmaz. 

Az elsődleges virtuális hálózat alhálózati tartományának ellenőrzéséhez kövesse az alábbi lépéseket:
1. A [Azure Portal](https://portal.azure.com)navigáljon az erőforráscsoporthoz, és válassza ki az elsődleges példány virtuális hálózatát. 
1. Válassza ki az **alhálózatok** lehetőséget a **Beállítások** területen, és jegyezze fel a **címtartományt**. A másodlagos felügyelt példányhoz tartozó virtuális hálózat alhálózati címtartomány nem fedi át ezt a tartományt. 


   ![Elsődleges alhálózat](media/sql-database-managed-instance-failover-group-tutorial/verify-primary-subnet-range.png)

Virtuális hálózat létrehozásához kövesse az alábbi lépéseket:

1. A [Azure Portal](https://portal.azure.com)válassza az **erőforrás létrehozása** és a *virtuális hálózat*keresése lehetőséget. 
1. Válassza ki a Microsoft által közzétett **Virtual Network** lehetőséget, majd válassza a **Létrehozás** elemet a következő oldalon. 
1. Adja meg a szükséges mezőket a másodlagos felügyelt példány virtuális hálózatának konfigurálásához, majd válassza a **Létrehozás**lehetőséget. 

   A következő táblázat a másodlagos virtuális hálózathoz szükséges értékeket mutatja be:

    | **Mező** | Value |
    | --- | --- |
    | **Name** |  A másodlagos felügyelt példány által használandó virtuális hálózat neve, például `vnet-sql-mi-secondary`:. |
    | **Címtér** | A virtuális hálózat címterület, például `10.128.0.0/16`:. | 
    | **Előfizetés** | Az az előfizetés, amelyben az elsődleges felügyelt példány és az erőforráscsoport található. |
    | **Régió** | Az a hely, ahová a másodlagos felügyelt példányt telepíteni fogja. |
    | **Alhálózat** | Az alhálózat neve. `default`Alapértelmezés szerint meg van biztosítva. |
    | **Címtartomány**| Az alhálózat címtartomány. Ennek eltérőnek kell lennie, mint az elsődleges felügyelt példány virtuális hálózata által használt alhálózat-címtartomány, `10.128.0.0/24`például:.  |
    | &nbsp; | &nbsp; |

    ![Másodlagos virtuális hálózati értékek](media/sql-database-managed-instance-failover-group-tutorial/secondary-virtual-network.png)


## <a name="3---create-a-secondary-managed-instance"></a>3 – másodlagos felügyelt példány létrehozása
Ebben a lépésben egy másodlagos felügyelt példányt fog létrehozni a Azure Portalban, amely a két felügyelt példány közötti hálózatkezelést is konfigurálja. 

A második felügyelt példánynak a következőket kell tennie:
- Üresnek kell lennie. 
- Az elsődleges felügyelt példánytól eltérő alhálózattal és IP-tartománnyal rendelkezik. 

A másodlagos felügyelt példány létrehozásához kövesse az alábbi lépéseket: 

1. Válassza az **Azure SQL** lehetőséget a Azure Portal bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **minden szolgáltatás**lehetőséget, majd írja be az Azure SQL kifejezést a keresőmezőbe. Választható Válassza ki az **Azure SQL** melletti csillagot a kedvencekhez, és adja hozzá elemként a bal oldali navigációs sávon. 
1. Válassza a **+ Hozzáadás** lehetőséget az **SQL-telepítés kiválasztása** lap megnyitásához. A különböző adatbázisokkal kapcsolatos további információkat az adatbázisok csempén a Részletek megjelenítése lehetőség kiválasztásával tekintheti meg.
1. Válassza a **Létrehozás** lehetőséget a **felügyelt SQL-példányok** csempén. 

    ![Felügyelt példány kiválasztása](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. A **Azure SQL Database felügyelt példány létrehozása** lap **alapok** lapján töltse ki a szükséges mezőket a másodlagos felügyelt példány konfigurálásához. 

   A következő táblázat a másodlagos felügyelt példányhoz szükséges értékeket tartalmazza:
 
    | **Mező** | Value |
    | --- | --- |
    | **Előfizetés** |  Az az előfizetés, amelyben az elsődleges felügyelt példánya. |
    | **Erőforráscsoport**| Az az erőforráscsoport, amelyben az elsődleges felügyelt példánya. |
    | **Felügyelt példány neve** | Az új másodlagos felügyelt példány neve, például`sql-mi-secondary`  | 
    | **Régió**| A másodlagos felügyelt példány helye.  |
    | **Felügyelt példány rendszergazdai bejelentkezési neve** | Az új másodlagos felügyelt példányhoz használni kívánt bejelentkezési azonosító, például `azureuser`:. |
    | **Jelszó** | Összetett jelszó, amelyet az új másodlagos felügyelt példányhoz tartozó rendszergazdai bejelentkezés fog használni.  |
    | &nbsp; | &nbsp; |

1. A **hálózatkezelés** lap **Virtual Network**területén válassza ki a másodlagos felügyelt példányhoz létrehozott virtuális hálózatot a legördülő menüből.

   ![Másodlagos MI hálózatkezelés](media/sql-database-managed-instance-failover-group-tutorial/networking-settings-for-secondary-mi.png)

1. A **További beállítások** lap **geo-replikáció**területén válassza az **Igen** lehetőséget a _feladatátvétel másodlagosként való használathoz_. Válassza ki az elsődleges felügyelt példányt a legördülő menüből. 
    1. Ügyeljen arra, hogy a rendezés és az időzóna megfeleljen az elsődleges felügyelt példánynak. Az oktatóanyagban létrehozott elsődleges felügyelt példány az alapértelmezett `SQL_Latin1_General_CP1_CI_AS` rendezést és az `(UTC) Coordinated Universal Time` időzónát használta. 

   ![Másodlagos MI hálózatkezelés](media/sql-database-managed-instance-failover-group-tutorial/secondary-mi-failover.png)

1. Válassza a **felülvizsgálat + létrehozás** lehetőséget a másodlagos felügyelt példány beállításainak áttekintéséhez. 
1. Válassza a **Létrehozás** lehetőséget a másodlagos felügyelt példány létrehozásához. 


## <a name="4---create-primary-virtual-network-gateway"></a>4 – elsődleges virtuális hálózati átjáró létrehozása 

Két felügyelt példánynak a feladatátvételi csoportban való részvételhez a két felügyelt példány virtuális hálózatai között olyan átjárót kell konfigurálni, amely engedélyezi a hálózati kommunikációt. Az elsődleges felügyelt példány átjáróját a Azure Portal használatával hozhatja létre:

1. A [Azure Portal](https://portal.azure.com)nyissa meg az erőforráscsoportot, és válassza ki a **virtuális hálózati** erőforrást az elsődleges felügyelt példányhoz. 
1. Válassza ki az **alhálózatok** lehetőséget a **Beállítások** területen, majd válassza a lehetőséget egy új **átjáró-alhálózat**hozzáadásához. Hagyja meg az alapértelmezett értékeket. 

   ![Átjáró hozzáadása az elsődleges felügyelt példányhoz](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. Az alhálózat-átjáró létrehozása után válassza az **erőforrás létrehozása** lehetőséget a bal oldali navigációs ablaktáblán, majd `Virtual network gateway` írja be a keresőmezőbe a kifejezést. Válassza ki a **Microsoft**által közzétett **Virtual Network Gateway** -erőforrást. 

   ![Új virtuális hálózati átjáró létrehozása](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. Töltse ki a kötelező mezőket az elsődleges felügyelt példány átjárójának konfigurálásához. 

   Az alábbi táblázat az elsődleges felügyelt példány átjárója számára szükséges értékeket tartalmazza:
 
    | **Mező** | Value |
    | --- | --- |
    | **Előfizetés** |  Az az előfizetés, amelyben az elsődleges felügyelt példánya. |
    | **Name** | A virtuális hálózati átjáró neve, például `primary-mi-gateway`:. | 
    | **Régió** | Az a régió, amelyben a másodlagos felügyelt példány található. |
    | **Átjáró típusa** | Válassza a **VPN**lehetőséget. |
    | **VPN-típus** | **Útvonal-alapú** |
    | **Termékváltozat**| Hagyja meg az `VpnGw1`alapértelmezett értéket. |
    | **Location**| Az elsődleges felügyelt példány és az elsődleges virtuális hálózat helye.   |
    | **Virtuális hálózat**| Válassza ki a 2 `vnet-sql-mi-primary`. szakaszban létrehozott virtuális hálózatot, például:. |
    | **Nyilvános IP-cím**| Válassza az **Új létrehozása** lehetőséget. |
    | **Nyilvános IP-cím neve**| Adja meg az IP-cím nevét, például `primary-gateway-IP`:. |
    | &nbsp; | &nbsp; |
1. Hagyja meg a többi értéket alapértelmezettként, majd válassza a **felülvizsgálat + létrehozás** lehetőséget a virtuális hálózati átjáró beállításainak áttekintéséhez.

   ![Elsődleges átjáró beállításai](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. Válassza a **Létrehozás** lehetőséget az új virtuális hálózati átjáró létrehozásához. 

## <a name="5---configure-secondary-virtual-network-gateway"></a>5 – másodlagos virtuális hálózati átjáró konfigurálása 

Az előző szakaszban leírt lépések megismétlésével hozza létre a virtuális hálózati alhálózatot és az átjárót a másodlagos felügyelt példányhoz. Adja meg a szükséges mezőket a másodlagos felügyelt példány átjárójának konfigurálásához. 

   A következő táblázat a másodlagos felügyelt példány átjárója számára szükséges értékeket tartalmazza:

   | **Mező** | Value |
   | --- | --- |
   | **Előfizetés** |  Az előfizetés, amelyben a másodlagos felügyelt példánya van. |
   | **Name** | A virtuális hálózati átjáró neve, például `secondary-mi-gateway`:. | 
   | **Régió** | Az a régió, amelyben a másodlagos felügyelt példány található. |
   | **Átjáró típusa** | Válassza a **VPN**lehetőséget. |
   | **VPN-típus** | **Útvonal-alapú** |
   | **Termékváltozat**| Hagyja meg az `VpnGw1`alapértelmezett értéket. |
   | **Location**| A másodlagos felügyelt példány és a másodlagos virtuális hálózat helye.   |
   | **Virtuális hálózat**| Válassza ki a 2 `vnet-sql-mi-secondary`. szakaszban létrehozott virtuális hálózatot, például:. |
   | **Nyilvános IP-cím**| Válassza az **Új létrehozása** lehetőséget. |
   | **Nyilvános IP-cím neve**| Adja meg az IP-cím nevét, például `secondary-gateway-IP`:. |
   | &nbsp; | &nbsp; |

   ![Másodlagos átjáró beállításai](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)

## <a name="6---connect-the-gateways"></a>6 – az átjárók összekötése
Ebben a lépésben hozzon létre egy kapcsolatot az átjárók között. Az elsődlegesről a másodlagos átjáróra kell létrehozni a kapcsolatot, majd külön kapcsolatot kell létesíteni a másodlagos és az elsődleges átjáró között. Az átjárók közötti kapcsolat konfigurálásakor ügyeljen arra, hogy ugyanazt a **megosztott kulcsot** használja. 

A kapcsolat konfigurálásához kövesse az alábbi lépéseket:

1. Navigáljon az erőforráscsoporthoz a [Azure Portalban](https://portal.azure.com) , és válassza ki a 4. lépésben létrehozott elsődleges átjárót. 
1. A **Beállítások** területen válassza a **kapcsolatok** lehetőséget, majd válassza a **Hozzáadás** lehetőséget egy új kapcsolat létrehozásához. 

   ![Elsődleges átjáróhoz való kapcsolódás hozzáadása](media/sql-database-managed-instance-failover-group-tutorial/add-primary-gateway-connection.png)

1. Adja meg a kapcsolatok nevét, például `Primary-connection`:, és írjon be egy értéket a **megosztott** `mi1mi2psk`kulcshoz (például). 
1. Válassza ki a **második virtuális hálózati átjárót** , majd válassza ki a másodlagos felügyelt példány átjáróját `secondary-mi-gateway`, például:. 

   ![Elsődlegesről másodlagosra történő kapcsolódás létrehozása](media/sql-database-managed-instance-failover-group-tutorial/create-primary-to-secondary-connection.png)

1. Az új elsődleges – másodlagos átjárói kapcsolatok hozzáadásához kattintson **az OK gombra** .
1. Ismételje meg ezeket a lépéseket a másodlagos felügyelt példány átjárója és az elsődleges felügyelt példány átjárója közötti kapcsolatok létrehozásához. 

   ![Másodlagos – elsődleges kapcsolatok létrehozása](media/sql-database-managed-instance-failover-group-tutorial/create-secondary-to-primary-connection.png)


## <a name="7---create-a-failover-group"></a>7 – feladatátvételi csoport létrehozása
Ebben a lépésben létrehozza a feladatátvételi csoportot, és hozzáadja a felügyelt példányokat is. 

1. Válassza az **Azure SQL** lehetőséget a [Azure Portal](https://portal.azure.com)bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **minden szolgáltatás**lehetőséget, majd írja be az Azure SQL kifejezést a keresőmezőbe. Választható Válassza ki az **Azure SQL** melletti csillagot a kedvencekhez, és adja hozzá elemként a bal oldali navigációs sávon. 
1. Válassza ki az első szakaszban létrehozott elsődleges felügyelt példányt, például `sql-mi-primary`:. 
1. A **Beállítások**területen navigáljon a **példány feladatátvételi csoportok** elemre, majd válassza a **Csoport hozzáadása** lehetőséget a **példány feladatátvételi csoport** lap megnyitásához. 

   ![Feladatátvételi csoport hozzáadása](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. A **példány feladatátvételi csoport** lapján írja be a feladatátvételi csoport nevét, például `failovergrouptutorial` :, majd válassza ki a másodlagos felügyelt `sql-mi-secondary` példányt, például a legördülő menüből. Válassza a **Létrehozás** lehetőséget a feladatátvételi csoport létrehozásához. 

   ![Feladatátvételi csoport létrehozása](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. A feladatátvételi csoport központi telepítésének befejezése után vissza fog térni a **feladatátvételi csoport** lapra. 

## <a name="8---test-failover"></a>8 – feladatátvételi teszt
Ebben a lépésben a feladatátvételi csoportot a másodlagos kiszolgálóra fogja felvenni, majd a Azure Portal használatával hajtja végre a feladatokat. 

1. Navigáljon a felügyelt példányhoz a [Azure Portalon](https://portal.azure.com) belül, és válassza a **példányok feladatátvételi csoportok** lehetőséget a beállítások területen. 
1. Tekintse át, hogy melyik felügyelt példány az elsődleges, és melyik felügyelt példány a másodlagos. 
1. Válassza a **feladatátvétel** lehetőséget, majd válassza az **Igen** lehetőséget a TDS-munkamenetek leválasztására vonatkozó figyelmeztetésben. 

   ![Feladatátvételi csoport feladatátvétele](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. Tekintse át az elsődleges és a másodlagos példányt. Ha a feladatátvétel sikeres volt, a két példánynak átváltott szerepkörökkel kell rendelkeznie. 

   ![A felügyelt példányok a feladatátvételt követően váltanak át szerepköröket](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. Ismét válassza a **feladatátvétel** lehetőséget, ha az elsődleges példányt vissza szeretné adni az elsődleges szerepkörnek. 


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Az erőforrások tisztításához először törölje a felügyelt példányt, majd a virtuális fürtöt, majd a többi erőforrást, végül pedig az erőforráscsoportot. 

1. Navigáljon az erőforráscsoporthoz a [Azure Portal](https://portal.azure.com). 
1. Válassza ki a felügyelt példányt, majd válassza a **Törlés**lehetőséget. Írja `yes` be a szövegmezőbe, és erősítse meg, hogy törölni kívánja az erőforrást, majd válassza a **Törlés**lehetőséget. Ez a folyamat hosszabb időt is igénybe vehet a háttérben, és amíg el nem végzi a műveletet, nem fogja tudni törölni a *virtuális fürtöt* vagy bármely más függő erőforrást. Figyelje meg a törlést a tevékenység lapon a felügyelt példány törlésének megerősítéséhez. 
1. A felügyelt példány törlése után törölje a *virtuális fürtöt* úgy, hogy kiválasztja az erőforráscsoportot, majd a **Törlés**lehetőséget választja. Írja `yes` be a szövegmezőbe, és erősítse meg, hogy törölni kívánja az erőforrást, majd válassza a **Törlés**lehetőséget. 
1. Törölje a többi erőforrást. Írja `yes` be a szövegmezőbe, és erősítse meg, hogy törölni kívánja az erőforrást, majd válassza a **Törlés**lehetőséget. 
1. Törölje az erőforráscsoportot az **erőforráscsoport törlése**elem kiválasztásával, írja be az erőforráscsoport `myResourceGroup`nevét, majd válassza a **Törlés**lehetőséget. 

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy feladatátvételi csoportot konfigurált két felügyelt példány között. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> - Elsődleges felügyelt példány létrehozása
> - Hozzon létre egy másodlagos felügyelt példányt egy [feladatátvételi csoport](sql-database-auto-failover-group.md)részeként. 
> - Feladatátvételi teszt

Folytassa a következő rövid útmutatóval, hogyan csatlakozhat a felügyelt példányhoz, és hogyan állíthatja vissza az adatbázist a felügyelt példányra: 

> [!div class="nextstepaction"]
> [Kapcsolódás felügyelt példányhoz](sql-database-managed-instance-configure-vm.md)
> [adatbázis visszaállítása felügyelt példányra](sql-database-managed-instance-get-started-restore.md)


