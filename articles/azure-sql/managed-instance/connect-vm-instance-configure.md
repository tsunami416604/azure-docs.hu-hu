---
title: Azure-beli virtuális gép kapcsolatának konfigurálása
titleSuffix: Azure SQL Managed Instance
description: Kapcsolódás Azure SQL felügyelt példányhoz SQL Server Management Studio használatával Azure-beli virtuális gépről.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, srbozovi, bonova
ms.date: 02/18/2019
ms.openlocfilehash: b94993a67fadad2c31c8787deab49e62dd457512
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84044373"
---
# <a name="quickstart-configure-azure-vm-to-connect-to-an-azure-sql-managed-instance"></a>Gyors útmutató: Azure virtuális gép konfigurálása felügyelt Azure SQL-példányhoz való kapcsolódáshoz
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ebből a rövid útmutatóból megtudhatja, hogyan konfigurálhat egy Azure-beli virtuális gépet a felügyelt Azure SQL-példányokhoz való kapcsolódáshoz SQL Server Management Studio (SSMS) használatával. 


A pont – hely kapcsolat használatával történő kapcsolódást bemutató rövid útmutatóban [megtekintheti](point-to-site-p2s-configure.md) , hogy hogyan lehet pont – hely kapcsolattal csatlakozni a helyszíni ügyfélszámítógépekről.

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató az [SQL felügyelt példány létrehozásakor](instance-create-quickstart.md) létrehozott erőforrásokat használja kiindulási pontként.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-a-new-subnet-vnet"></a>Új alhálózat VNet létrehozása

A következő lépések új alhálózatot hoznak létre a felügyelt SQL-példány VNet, így az Azure-beli virtuális gépek csatlakozhatnak az SQL felügyelt példányához. Az SQL felügyelt példányának alhálózata az SQL felügyelt példányaihoz van hozzárendelve. Ebben az alhálózatban nem hozhatók létre más erőforrások, például az Azure Virtual machines.

1. Nyissa meg az SQL felügyelt [példány létrehozása](instance-create-quickstart.md) rövid útmutatójában létrehozott SQL felügyelt példányhoz tartozó erőforráscsoportot. Válassza ki az SQL felügyelt példányának virtuális hálózatát.

   ![SQL felügyelt példány erőforrásai](./media/connect-vm-instance-configure/resources.png)

2. Válassza az **alhálózatok** lehetőséget, majd válassza az **+ alhálózat** lehetőséget egy új alhálózat létrehozásához.

   ![SQL felügyelt példány alhálózatai](./media/connect-vm-instance-configure/subnets.png)

3. Töltse ki az űrlapot a táblázatban található információk alapján:

   | Beállítás| Ajánlott érték | Leírás |
   | ---------------- | ----------------- | ----------- |
   | **Név** | Bármely érvényes név|Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming) ismertető cikket.|
   | **Címtartomány (CIDR-blokk)** | Érvényes tartomány | Az alapértelmezett érték jó ehhez a rövid útmutatóhoz.|
   | **Hálózati biztonsági csoport** | Nincs | Az alapértelmezett érték jó ehhez a rövid útmutatóhoz.|
   | **Útválasztási táblázat** | Nincs | Az alapértelmezett érték jó ehhez a rövid útmutatóhoz.|
   | **Szolgáltatásvégpontok** | 0 kijelölve | Az alapértelmezett érték jó ehhez a rövid útmutatóhoz.|
   | **Alhálózat delegálása** | Nincs | Az alapértelmezett érték jó ehhez a rövid útmutatóhoz.|

   ![Új SQL felügyelt példány alhálózata ügyfél virtuális géphez](./media/connect-vm-instance-configure/new-subnet.png)

4. Kattintson az **OK** gombra a további alhálózat létrehozásához az SQL felügyelt példányának VNet.

## <a name="create-vm-in-new-subnet"></a>Virtuális gép létrehozása új alhálózatban 

A következő lépések bemutatják, hogyan hozhat létre virtuális gépet az új alhálózatban az SQL-felügyelt példányhoz való kapcsolódáshoz.

## <a name="prepare-the-azure-virtual-machine"></a>Az Azure-beli virtuális gép előkészítése

Mivel az SQL felügyelt példánya a privát Virtual Networkba kerül, létre kell hoznia egy Azure-beli virtuális gépet egy telepített SQL Client eszközzel, például SQL Server Management Studio vagy Azure Data Studio. Ez az eszköz lehetővé teszi az SQL felügyelt példányhoz való kapcsolódást és a lekérdezések végrehajtását. Ez a rövid útmutató az SQL Server Management Studio eszközt használja.

Az ügyfél virtuális gépnek az összes szükséges eszközzel való létrehozásának legegyszerűbb módja a Azure Resource Manager-sablonok használata.

1. Győződjön meg arról, hogy be van jelentkezve a Azure Portal egy másik böngésző lapon. Ezután válassza a következő gombot egy ügyfél virtuális gép létrehozásához és a SQL Server Management Studio telepítéséhez:

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjovanpop-msft%2Fazure-quickstart-templates%2Fsql-win-vm-w-tools%2F201-vm-win-vnet-sql-tools%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

2. Töltse ki az űrlapot az alábbi táblázatban szereplő információk alapján:

   | Beállítás| Ajánlott érték | Leírás |
   | ---------------- | ----------------- | ----------- |
   | **Előfizetés** | Érvényes előfizetés | Olyan előfizetésnek kell lennie, amelyben jogosult új erőforrások létrehozására. |
   | **Erőforráscsoport** |A [felügyelt SQL-példány létrehozása](instance-create-quickstart.md) rövid útmutatójában megadott erőforráscsoport.|Ez az erőforráscsoport a VNet létezésének egyike.|
   | **Hely** | Az erőforráscsoport helye | Ez az érték a kiválasztott erőforráscsoport alapján van feltöltve. |
   | **Virtuális gép neve**  | Bármely érvényes név | Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming) ismertető cikket.|
   |**Rendszergazdai Felhasználónév**|Bármilyen érvényes Felhasználónév|Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming) ismertető cikket. Ne használja a "ServerAdmin" kulcsszót, mert ez egy fenntartott kiszolgálói szintű szerepkör.<br>Ezt a felhasználónevet akkor használja, amikor [csatlakozik a virtuális géphez](#connect-to-virtual-machine).|
   |**Jelszó**|Bármely érvényes jelszó|A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).<br>Ezt a jelszót bármikor használhatja [a virtuális géphez való kapcsolódáskor](#connect-to-virtual-machine).|
   | **Virtuális gép mérete** | Bármilyen érvényes méret | A **Standard_B2s** ebben a sablonban szereplő alapértelmezett értéke elegendő ehhez a rövid útmutatóhoz. |
   | **Hely**|[resourceGroup (). location].| Ne módosítsa ezt az értéket. |
   | **Virtual Network neve**|Az a virtuális hálózat, amelyben létrehozta az SQL felügyelt példányát.|
   | **Alhálózat neve**|Az előző eljárásban létrehozott alhálózat neve| Ne válassza ki azt az alhálózatot, amelyben létrehozta az SQL felügyelt példányát.|
   | **összetevők helye** | [központi telepítés (). properties. templateLink. URI] | Ne módosítsa ezt az értéket. |
   | **összetevők helye sas-token** | hagyja üresen | Ne módosítsa ezt az értéket. |

   ![ügyféloldali virtuális gép létrehozása](./media/connect-vm-instance-configure/create-client-sql-vm.png)

   Ha az [SQL felügyelt példányának létrehozásakor](instance-create-quickstart.md)a javasolt VNet nevet és az alapértelmezett alhálózatot használta, nem kell módosítania az utolsó két paramétert. Ellenkező esetben módosítsa ezeket az értékeket a hálózati környezet beállításakor megadott értékekre.

3. Jelölje be az **Elfogadom a fenti feltételeket és kikötéseket** jelölőnégyzetet.
4. Válassza a **vásárlás** lehetőséget az Azure-beli virtuális gép üzembe helyezéséhez a hálózaton.
5. A telepítés állapotának megtekintéséhez kattintson az **értesítések** ikonra.

> [!IMPORTANT]
> Ha a virtuális gép létrehozása után körülbelül 15 percet vesz igénybe, hogy időt adjon a létrehozás utáni parancsfájloknak SQL Server Management Studio telepítéséhez.

## <a name="connect-to-virtual-machine"></a>Csatlakozás virtuális géphez

Az alábbi lépések bemutatják, hogyan csatlakozhat az újonnan létrehozott virtuális géphez távoli asztali kapcsolattal.

1. Ha az üzembe helyezés elkészült, lépjen a virtuális gép erőforráshoz.

    ![VM](./media/connect-vm-instance-configure/vm.png)  

2. Kattintson a **Csatlakozás** gombra.

   A virtuális gép nyilvános IP-címét és portszámát tartalmazó RDP protokoll fájl (. rdp fájl) űrlap jelenik meg.

   ![RDP-űrlap](./media/connect-vm-instance-configure/rdp.png)  

3. Válassza az **RDP-fájl letöltése**lehetőséget.

   > [!NOTE]
   > Az SSH használatával is csatlakozhat a virtuális géphez.

4. Zárjuk be a **Kapcsolódás virtuális géphez** űrlapot.
5. Nyissa meg az RDP-fájlt a virtuális géphez való csatlakozáshoz.
6. Ha a rendszer kéri, válassza a **Csatlakozás**lehetőséget. Mac rendszerben szüksége van egy RDP-kliensre, mint például a Mac App Store áruházban elérhető [távoli asztali ügyfélre](https://apps.apple.com/app/microsoft-remote-desktop-10/id1295203466?mt=12).

7. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót, majd kattintson **az OK gombra**.

8. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Válassza az **Igen** vagy a **Folytatás** lehetőséget a kapcsolódás folytatásához.

A Kiszolgálókezelő irányítópultján csatlakozik a virtuális géphez.

## <a name="connect-to-sql-managed-instance"></a>Csatlakozás felügyelt SQL-példányhoz 

1. A virtuális gépen nyissa meg SQL Server Management Studio (SSMS).

   A konfiguráció befejezéséhez néhány pillanatot kell megnyitnia, mivel ez az első alkalom, hogy a SSMS megkezdődött.
2. A **Kapcsolódás a kiszolgálóhoz** párbeszédpanelen adja meg az SQL felügyelt példányának teljes **állomásnevét** a **kiszolgáló neve** mezőben. Válassza ki **SQL Server hitelesítést**, adja meg felhasználónevét és jelszavát, majd válassza a **kapcsolat**lehetőséget.

    ![ssms connect](./media/connect-vm-instance-configure/ssms-connect.png)  

A csatlakozás után megtekintheti a rendszer- és felhasználói adatbázisokat a Databases (Adatbázisok) csomóponton, valamint különféle objektumokat a Security (Biztonság), Server Objects (Kiszolgálóobjektumok), Replication (Replikáció), Management (Felügyelet), SQL Server Agent és XEvent Profiler csomópontokon.

## <a name="next-steps"></a>További lépések

- A pont – hely kapcsolattal rendelkező helyszíni ügyfélszámítógépekről történő kapcsolódást bemutató rövid útmutató: [pont – hely kapcsolat konfigurálása](point-to-site-p2s-configure.md).
- Az alkalmazások csatlakozási lehetőségeinek áttekintését lásd: [alkalmazások csatlakoztatása SQL felügyelt példányhoz](connect-application-instance.md).
- Ha egy meglévő SQL Server-adatbázist szeretne visszaállítani egy SQL-alapú felügyelt példányra, használja a [Azure Database Migration Service (DMS) az áttelepítéshez](../../dms/tutorial-sql-server-to-managed-instance.md) , vagy a [T-SQL Restore parancsát](restore-sample-database-quickstart.md) egy adatbázis biztonságimásolat-fájljából való visszaállításhoz.
