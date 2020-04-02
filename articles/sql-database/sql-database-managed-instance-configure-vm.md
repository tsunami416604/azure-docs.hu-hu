---
title: Ügyfél virtuális gépének csatlakoztatása – felügyelt példány
description: Csatlakozzon egy Azure SQL Database felügyelt példányhoz az SQL Server Management Studio használatával egy Azure virtuális gépről.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, srbozovi, bonova
ms.date: 02/18/2019
ms.openlocfilehash: 8b5dce0b43fac7cfd0e974f26451338ca1541f8f
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80528415"
---
# <a name="quickstart-configure-azure-vm-to-connect-to-an-azure-sql-database-managed-instance"></a>Rövid útmutató: Konfigurálja az Azure VM-et egy Azure SQL-adatbázis felügyelt példányához való csatlakozáshoz

Ez a rövid útmutató bemutatja, hogyan konfigurálhat egy Azure virtuális gépet egy Azure SQL Database Felügyelt példányhoz az SQL Server Management Studio (SSMS) használatával. A helyi ügyfélszámítógépekről a helyek közötti kapcsolat használatával történő csatlakozás rövid útmutatóját a [pont-hely kapcsolat konfigurálása](sql-database-managed-instance-configure-p2s.md) című témakörben tismerteti.

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató a [Felügyelt példány létrehozása](sql-database-managed-instance-get-started.md) című csoportban létrehozott erőforrásokat használja kiindulási pontként.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

## <a name="create-a-new-subnet-in-the-managed-instance-vnet"></a>Új alhálózat létrehozása a felügyelt példány virtuális hálózatában

A következő lépések hozzon létre egy új alhálózatot a felügyelt példány virtuális hálózatában, így egy Azure virtuális gép csatlakozhat a felügyelt példányhoz. A felügyelt példány alhálózat felügyelt példányok számára van lerendelve. Az alhálózatban nem hozhat létre más erőforrásokat, például az Azure virtuális gépeket.

1. Nyissa meg a Felügyelt példány létrehozásához létrehozott erőforráscsoportot a [Felügyelt példány létrehozása](sql-database-managed-instance-get-started.md) rövid útmutatóban. Válassza ki a felügyelt példány virtuális hálózatát.

   ![Felügyelt példány erőforrásai](./media/sql-database-managed-instance-configure-vm/resources.png)

2. Válassza **az Alhálózatok lehetőséget,** majd **a + Alhálózat** lehetőséget új alhálózat létrehozásához.

   ![Felügyelt példány alhálózatai](./media/sql-database-managed-instance-configure-vm/subnets.png)

3. Töltse ki az űrlapot a táblázatban szereplő információk alapján:

   | Beállítás| Ajánlott érték | Leírás |
   | ---------------- | ----------------- | ----------- |
   | **Név** | Bármely érvényes név|Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming) ismertető cikket.|
   | **Címtartomány (CIDR-blokk)** | Érvényes tartomány | Az alapértelmezett érték jó ehhez a rövid útmutatóhoz.|
   | **Hálózati biztonsági csoport** | None | Az alapértelmezett érték jó ehhez a rövid útmutatóhoz.|
   | **Útvonaltábla** | None | Az alapértelmezett érték jó ehhez a rövid útmutatóhoz.|
   | **Szolgáltatásvégpontok** | 0 kijelölve | Az alapértelmezett érték jó ehhez a rövid útmutatóhoz.|
   | **Alhálózat delegálása** | None | Az alapértelmezett érték jó ehhez a rövid útmutatóhoz.|

   ![Új felügyelt példány alhálózat az ügyfél virtuális gépéhez](./media/sql-database-managed-instance-configure-vm/new-subnet.png)

4. Válassza **az OK lehetőséget** a további alhálózat létrehozásához a felügyelt példány virtuális hálózatában.

## <a name="create-a-virtual-machine-in-the-new-subnet-in-the-vnet"></a>Virtuális gép létrehozása a VNet új alhálózatában

A következő lépések bemutatják, hogyan hozhat létre egy virtuális gépet az új alhálózatban a felügyelt példányhoz való csatlakozáshoz.

## <a name="prepare-the-azure-virtual-machine"></a>Az Azure virtuális gép előkészítése

Mivel az SQL felügyelt példány a privát virtuális hálózatban van elhelyezve, létre kell hoznia egy Azure virtuális gépet egy telepített SQL-ügyféleszközzel, például az SQL Server Management Studio vagy az Azure Data Studio használatával. Ez az eszköz lehetővé teszi, hogy csatlakozzon a felügyelt példányhoz, és lekérdezéseket hajtson végre. Ez a rövid útmutató az SQL Server Management Studio eszközt használja.

Az összes szükséges eszközzel rendelkező ügyfélvirtuális gépek létrehozásának legegyszerűbb módja az Azure Resource Manager-sablonok használata.

1. Győződjön meg arról, hogy be van jelentkezve az Azure Portalon egy másik böngészőlapon. Ezután válassza ki a következő gombot egy ügyfél virtuális gép létrehozásához és az SQL Server Management Studio telepítéséhez:

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjovanpop-msft%2Fazure-quickstart-templates%2Fsql-win-vm-w-tools%2F201-vm-win-vnet-sql-tools%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

2. Töltse ki az űrlapot az alábbi táblázatban szereplő információk alapján:

   | Beállítás| Ajánlott érték | Leírás |
   | ---------------- | ----------------- | ----------- |
   | **Előfizetés** | Érvényes előfizetés | Olyan előfizetésnek kell lennie, amelyben új erőforrások létrehozására vonatkozó engedéllyel rendelkezik. |
   | **Erőforráscsoport** |A [Felügyelt példány létrehozása](sql-database-managed-instance-get-started.md) rövid útmutatóban megadott erőforráscsoport.|Ennek az erőforráscsoportnak annak az erőforráscsoportnak kell lennie, amelyben a virtuális hálózat létezik.|
   | **Helyen** | Az erőforráscsoport helye | Ez az érték a kiválasztott erőforráscsoport alapján kerül feltöltésre. |
   | **Virtuális gép neve**  | Bármely érvényes név | Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming) ismertető cikket.|
   |**Rendszergazdai felhasználónév**|Bármely érvényes felhasználónév|Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming) ismertető cikket. Ne használja a "serveradmin" kifejezést, mivel az fenntartott kiszolgálószintű szerepkör.<br>Ezt a felhasználónevet mindig használhatja, amikor [a virtuális géphez csatlakozik.](#connect-to-virtual-machine)|
   |**Jelszó**|Bármely érvényes jelszó|A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).<br>Ezt a jelszót mindig használhatja, amikor [a virtuális géphez csatlakozik.](#connect-to-virtual-machine)|
   | **Virtuális gép mérete** | Bármilyen érvényes méret | Az alapértelmezett ebben a sablonban a **Standard_B2s** elegendő ehhez a rövid útmutatóhoz. |
   | **Helyen**|[resourceGroup(.location].| Ne változtassa meg ezt az értéket. |
   | **Virtuális hálózat neve**|Az a virtuális hálózat, amelyben létrehozta a felügyelt példányt.|
   | **Alhálózat neve**|Az előző eljárásban létrehozott alhálózat neve| Ne válassza ki azt az alhálózatot, amelyben létrehozta a felügyelt példányt.|
   | **összetevők helye** | [deployment().properties.templateLink.uri] | Ne változtassa meg ezt az értéket. |
   | **összetevők Hely Sas token** | hagyja üresen | Ne változtassa meg ezt az értéket. |

   ![ügyféloldali virtuális gép létrehozása](./media/sql-database-managed-instance-configure-vm/create-client-sql-vm.png)

   Ha a javasolt virtuális hálózat nevét és az alapértelmezett alhálózatot használta [a felügyelt példány létrehozásához,](sql-database-managed-instance-get-started.md)nem kell módosítania az utolsó két paramétert. Ellenkező esetben ezeket az értékeket a hálózati környezet beállításakor megadott értékekre kell módosítania.

3. Válassza az **Elfogadom a fenti feltételeket** jelölőnégyzetet.
4. Válassza **a Vásárlás** lehetőséget az Azure virtuális gép hálózati üzembe helyezéséhez.
5. A telepítés állapotának megtekintéséhez kattintson az **Értesítések** ikonra.

> [!IMPORTANT]
> Ne folytassa a virtuális gép létrehozása után körülbelül 15 perccel, hogy időt adjon a létrehozás utáni parancsfájlok számára az SQL Server Management Studio telepítéséhez.

## <a name="connect-to-virtual-machine"></a>Csatlakozás virtuális géphez

Az alábbi lépések bemutatják, hogyan csatlakozhat az újonnan létrehozott virtuális géphez távoli asztali kapcsolattal.

1. Ha az üzembe helyezés elkészült, lépjen a virtuális gép erőforráshoz.

    ![VM](./media/sql-database-managed-instance-configure-vm/vm.png)  

2. Kattintson a **Csatlakozás** gombra.

   A távoli asztali protokollfájl (.rdp fájl) űrlapja megjelenik a virtuális gép nyilvános IP-címével és portszámával.

   ![RDP képernyő](./media/sql-database-managed-instance-configure-vm/rdp.png)  

3. Válassza **az RDP-fájl letöltése lehetőséget.**

   > [!NOTE]
   > Az SSH használatával is csatlakozhat a virtuális géphez.

4. Zárja be a **Csatlakozás a virtuális géphez** űrlapot.
5. Nyissa meg az RDP-fájlt a virtuális géphez való csatlakozáshoz.
6. Amikor a rendszer kéri, válassza a **Csatlakozás lehetőséget.** Mac rendszerben szüksége van egy RDP-kliensre, mint például a Mac App Store áruházban elérhető [távoli asztali ügyfélre](https://apps.apple.com/app/microsoft-remote-desktop-10/id1295203466?mt=12).

7. Írja be a virtuális gép létrehozásakor megadott felhasználónevet és jelszót, majd válassza az **OK gombot.**

8. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. A kapcsolat folytatásához válassza az **Igen** vagy a **Folytatás** lehetőséget.

A Kiszolgálókezelő irányítópultján csatlakozik a virtuális géphez.

## <a name="use-ssms-to-connect-to-the-managed-instance"></a>A felügyelt példányhoz való csatlakozás hoz az SSMS használatával

1. A virtuális gépen nyissa meg az SQL Server Management Studio (SSMS) alkalmazást.

   Néhány percet vesz igénybe a megnyitása, mivel be kell fejeznie a konfigurációját, mivel ez az első alkalom, hogy az SSMS elindult.
2. A **Csatlakozás kiszolgálóhoz** párbeszédpanelen írja be a felügyelt példány teljesen minősített **állomásnevét** a **Kiszolgáló neve** mezőbe. Válassza **az SQL Server Hitelesítés**lehetőséget, adja meg felhasználónevét és jelszavát, majd válassza a **Csatlakozás**lehetőséget.

    ![ssms connect](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

A csatlakozás után megtekintheti a rendszer- és felhasználói adatbázisokat a Databases (Adatbázisok) csomóponton, valamint különféle objektumokat a Security (Biztonság), Server Objects (Kiszolgálóobjektumok), Replication (Replikáció), Management (Felügyelet), SQL Server Agent és XEvent Profiler csomópontokon.

## <a name="next-steps"></a>További lépések

- A pont-hely kapcsolat [konfigurálása](sql-database-managed-instance-configure-p2s.md)című témakörben rövid útmutatót, amely bemutatja, hogyan lehet helyszíni ügyfélszámítógépről csatlakozni egy pont-hely kapcsolaton keresztül.
- Az alkalmazások csatlakozási lehetőségeinek áttekintéséért lásd: [Alkalmazások csatlakoztatása egy felügyelt példányhoz](sql-database-managed-instance-connect-app.md).
- Meglévő SQL Server-adatbázis visszaállítása a helyszíni felügyelt példányból, az [Azure Database Migration Service (DMS)](../dms/tutorial-sql-server-to-managed-instance.md) segítségével az áttelepítéshez vagy a [T-SQL RESTORE paranccsal](sql-database-managed-instance-get-started-restore.md) visszaállíthatja az adatbázis biztonsági másolatfájlját.
