---
title: Csatlakozás az Azure SQL Database felügyelt példány Virtuálisgép - ügyfél |} A Microsoft Docs
description: Csatlakozás egy Azure SQL Database felügyelt példányába történő SQL Server Management Studio segítségével az Azure virtuális gépből.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlrab, srbozovi, bonova
manager: craigg
ms.date: 02/18/2019
ms.openlocfilehash: 9e1001816e9a4cf62d2e6c84c72aae84428148d0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57997916"
---
# <a name="quickstart-configure-azure-vm-to-connect-to-an-azure-sql-database-managed-instance"></a>Gyors útmutató: Azure-beli virtuális gép csatlakozni egy Azure SQL Database felügyelt példányába konfigurálása

Ez a rövid útmutató bemutatja, hogyan konfigurálhatja az Azure virtuális gép csatlakozni egy Azure SQL Database felügyelt példánya SQL Server Management Studio (SSMS) használatával. A rövid útmutató, amely egy pont – hely kapcsolattal a helyszíni ügyfélszámítógépről összekapcsolása, lásd: [pont – hely kapcsolat konfigurálása](sql-database-managed-instance-configure-p2s.md) 

## <a name="prerequisites"></a>Előfeltételek

Ebben a rövid útmutatóban létrehozott erőforrásokat használja [létrehoz egy felügyelt példányt](sql-database-managed-instance-get-started.md) kiindulási pontként.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-a-new-subnet-in-the-managed-instance-vnet"></a>Hozzon létre egy új alhálózatot a felügyelt példány virtuális hálózaton

Az alábbi lépéseket hozzon létre egy új alhálózatot a felügyelt példány virtuális hálózat egy Azure virtuális gép csatlakozhat a felügyelt példányhoz. A felügyelt példány alhálózatára felügyelt példányra van kijelölve. Bármely más erőforrások, például az Azure virtual machines, az alhálózat nem hozható létre. 

1. Nyissa meg az erőforráscsoport a felügyelt példány, amelyet a [létrehoz egy felügyelt példányt](sql-database-managed-instance-get-started.md) rövid. Válassza ki a virtuális hálózatot a felügyelt példány.

   ![Felügyelt példány erőforrásai](./media/sql-database-managed-instance-configure-vm/resources.png)

2. Válassza ki **alhálózatok** majd **+ alhálózat** hozhat létre egy új alhálózatot.

   ![Felügyelt példány alhálózatok](./media/sql-database-managed-instance-configure-vm/subnets.png)

3. Töltse ki az űrlap ebben a táblában szereplő információk segítségével:

   | Beállítás| Ajánlott érték | Leírás |
   | ---------------- | ----------------- | ----------- | 
   | **Name (Név)** | Bármely érvényes név|Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket.|
   | **Címtartomány (CIDR-blokk)** | Érvénytelen tartomány | Az alapértelmezett érték: jó az ebben a rövid útmutatóban.|
   | **Hálózati biztonsági csoport** | None | Az alapértelmezett érték: jó az ebben a rövid útmutatóban.|
   | **Útvonaltábla** | None | Az alapértelmezett érték: jó az ebben a rövid útmutatóban.|
   | **Szolgáltatásvégpontok** | 0 kijelölt | Az alapértelmezett érték: jó az ebben a rövid útmutatóban.|
   | **Alhálózat delegálás** | None | Az alapértelmezett érték: jó az ebben a rövid útmutatóban.|
 
   ![Az ügyfél virtuális gép új felügyelt példány alhálózatára](./media/sql-database-managed-instance-configure-vm/new-subnet.png)

4. Válassza ki **OK** ilyen további alhálózat a felügyelt példány virtuális hálózat létrehozásához.

## <a name="create-a-virtual-machine-in-the-new-subnet-in-the-vnet"></a>Virtuális gép létrehozása a VNet új alhálózatában

A következő lépések bemutatják, hogyan hozhat létre egy virtuális gépet az új alhálózat a felügyelt példányhoz való csatlakozáshoz. 

## <a name="prepare-the-azure-virtual-machine"></a>Az Azure virtuális gép előkészítése

SQL felügyelt példánya a privát virtuális hálózat el van helyezve, mivel szüksége egy Azure virtuális gép létrehozása egy telepített SQL ügyféleszközben, például az SQL Server Management Studio vagy az Azure Data Studio. Ez az eszköz lehetővé teszi, hogy a felügyelt példány csatlakozhat, és lekérdezéseket. Ez a rövid útmutató az SQL Server Management Studiót használja.

Az ügyfél virtuális gép létrehozása az összes szükséges eszközökkel legegyszerűbben az Azure Resource Manager-sablonok használatára.

1. Győződjön meg arról, hogy van bejelentkezve az Azure Portalra egy új böngészőlapon. Ezután válassza ki az alábbi gombra az ügyfél virtuális gép létrehozása és telepítése az SQL Server Management Studio:

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjovanpop-msft%2Fazure-quickstart-templates%2Fsql-win-vm-w-tools%2F201-vm-win-vnet-sql-tools%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

2. Töltse ki az űrlapot, az alábbi táblázatban szereplő információk segítségével:

   | Beállítás| Ajánlott érték | Leírás |
   | ---------------- | ----------------- | ----------- |
   | **Előfizetés** | Egy érvényes előfizetést | Kell egy olyan előfizetést, amelyben új erőforrások létrehozásához szükséges engedéllyel rendelkezik. |
   | **Erőforráscsoport** |A megadott erőforráscsoport az [felügyelt példány létrehozása](sql-database-managed-instance-get-started.md) rövid.|Ez az erőforráscsoport, amelyben a virtuális hálózat létezik egy kell lennie.|
   | **Hely** | Az erőforráscsoport helyét | Ezt az értéket a kiválasztott erőforráscsoportba tartozó alapján van feltöltve. | 
   | **A virtuális gép neve**  | Bármely érvényes név | Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket.|
   |**Rendszergazdai felhasználónév**|Bármely érvényes felhasználónév|Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket. Ne használja a "serveradmin", mivel ez egy fenntartott kiszolgálói szintű szerepkört.<br>Ezt a felhasználónevet használ, tetszőleges időpontban [csatlakozzon a virtuális Géphez](#connect-to-virtual-machine).| 
   |**Jelszó**|Bármely érvényes jelszó|A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).<br>Használja ezt a jelszót, tetszőleges időpontban [csatlakozzon a virtuális Géphez](#connect-to-virtual-machine).|
   | **Virtuális gép mérete** | Bármely érvényes mérete | Ez a sablon az alapértelmezett **Standard_B2s** elegendő ehhez a gyors útmutatóhoz. |
   | **Hely**|[resourceGroup () .location].| Ez az érték nem módosítható. |
   | **Virtuális hálózat neve**|A virtuális hálózat, amelyben létrehozta a felügyelt példányhoz.|
   | **Alhálózat neve**|Az alhálózatot, amelyet az előző eljárásban létrehozott neve| Ne válassza ki az alhálózatot, amelyben létrehozta a felügyelt példányhoz.|
   | **összetevők helye** | [.properties.templateLink.uri a központi telepítés ()] | Ez az érték nem módosítható. |
   | **összetevők hely Sas-jogkivonat** | Hagyja üresen | Ez az érték nem módosítható. |

   ![ügyféloldali virtuális gép létrehozása](./media/sql-database-managed-instance-configure-vm/create-client-sql-vm.png)

   Ha a javasolt virtuális hálózat neve és az alapértelmezett alhálózat, a használt [a felügyelt példány létrehozása](sql-database-managed-instance-get-started.md), nem kell módosítani az utolsó két paramétert. Ellenkező esetben módosítsa ezeket az értékeket a hálózati környezet beállítása során megadott értékeket.

3. Válassza ki a **elfogadom a feltételeket és a fenti feltételeket** jelölőnégyzetet.
4. Válassza ki **beszerzési** a hálózat az Azure virtuális gép üzembe helyezéséhez.
5. Válassza ki a **értesítések** ikonra kattintva megtekintheti az üzembe helyezési állapotát.

> [!IMPORTANT]
> Ne folytassa a virtuális gép létrehozása az SQL Server Management Studio telepítése utáni létrehozási parancsfájlok ideje után körülbelül 15 perccel.

## <a name="connect-to-virtual-machine"></a>Csatlakozás virtuális géphez

Az alábbi lépések bemutatják, hogyan csatlakozhat az újonnan létrehozott virtuális géphez távoli asztali kapcsolattal.

1. Ha az üzembe helyezés elkészült, lépjen a virtuális gép erőforráshoz.

    ![VM](./media/sql-database-managed-instance-configure-vm/vm.png)  

2. Kattintson a **Csatlakozás** gombra. 
   
   Remote Desktop Protocol fájlt (.rdp-fájlt) űrlap jelenik meg a virtuális gép nyilvános IP-cím és port számát. 

   ![RDP-űrlap](./media/sql-database-managed-instance-configure-vm/rdp.png)  

3. Válassza ki **RDP-fájl letöltése**.
 
   > [!NOTE]
   > Az SSH segítségével kapcsolódni a virtuális Géphez.

4. Zárja be a **csatlakozhat a virtuális gép** űrlap.
5. Nyissa meg az RDP-fájlt a virtuális géphez való csatlakozáshoz. 
6. Amikor a rendszer kéri, válassza ki a **Connect**. Mac rendszerben szüksége van egy RDP-kliensre, mint például a Mac App Store áruházban elérhető [távoli asztali ügyfélre](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12).

6. Adja meg a felhasználónevet és a virtuális gép létrehozásakor megadott jelszót, majd válassza a **OK**.

7. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Válasszon **Igen** vagy **Folytatás** , a csatlakozás folytatásához.

A Kiszolgálókezelő irányítópultján a virtuális gép csatlakozik.

## <a name="use-ssms-to-connect-to-the-managed-instance"></a>Csatlakozhat a felügyelt példányhoz az SSMS használatával

1. A virtuális gépen nyissa meg az SQL Server Management Studio (SSMS).
 
   Megnyitni, mert a konfigurálás befejezéséhez, mivel az ssms-ben elindult első alkalommal kell néhány percet vesz igénybe.
2. Az a **kapcsolódás a kiszolgálóhoz** párbeszédpanelen adja meg a teljes **állomásnév** a felügyelt példány a **kiszolgálónév** mezőbe. Válassza ki **SQL Server-hitelesítés**, és adja meg a felhasználónevét és jelszavát, majd válassza ki **Connect**.

    ![ssms connect](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

A csatlakozás után megtekintheti a rendszer- és felhasználói adatbázisokat a Databases (Adatbázisok) csomóponton, valamint különféle objektumokat a Security (Biztonság), Server Objects (Kiszolgálóobjektumok), Replication (Replikáció), Management (Felügyelet), SQL Server Agent és XEvent Profiler csomópontokon.

## <a name="next-steps"></a>További lépések

- A rövid útmutató, amely egy pont – hely kapcsolattal a helyszíni ügyfélszámítógépről összekapcsolása, lásd: [pont – hely kapcsolat konfigurálása](sql-database-managed-instance-configure-p2s.md).
- Az alkalmazások csatlakozási lehetőségeinek áttekintéséért lásd: [Alkalmazások csatlakoztatása felügyelt példányhoz](sql-database-managed-instance-connect-app.md).
- Meglévő SQL Server-adatbázis visszaállítása helyi egy felügyelt példányra, használhatja a [Azure Database Migration Service (DMS) az áttelepítéshez](../dms/tutorial-sql-server-to-managed-instance.md) vagy a [T-SQL RESTORE parancsot](sql-database-managed-instance-get-started-restore.md) visszaállítása egy adatbázis biztonsági másolatát.
