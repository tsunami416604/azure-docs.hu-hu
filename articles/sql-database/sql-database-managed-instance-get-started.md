---
title: 'Azure Portal: Felügyelt SQL-példány létrehozása | Microsoft Docs'
description: Felügyelt SQL-példányt, hálózati környezetet és ügyféloldali virtuális gépet hozhat létre a hozzáféréshez.
keywords: sql database-oktatóanyag, felügyelt sql-példány létrehozása
services: sql-database
author: jovanpop-msft
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: quickstart
ms.date: 08/13/2018
ms.author: jovanpop-msft
ms.openlocfilehash: cb378c2d2773096992ef688653fd77b2625f8754
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2018
ms.locfileid: "42023079"
---
# <a name="create-an-azure-sql-managed-instance"></a>Felügyelt Azure SQL-példány létrehozása

Ez a rövid útmutató végigvezeti annak a folyamatán, hogyan hozhat létre egy felügyelt SQL-példányt az Azure-ban. A felügyelt Azure SQL Database-példány egy olyan SQL Server adatbázismotor-példány, amely szolgáltatásként nyújtott platformnak (PaaS) számít, és amellyel magas rendelkezésre állású SQL Server-adatbázisokat futtathat és méretezhet az Azure Cloudban. Ez a rövid útmutató azt mutatja be, hogyan kezdhet neki a felügyelt SQL-példányok létrehozásának.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="prepare-network-environment"></a>A hálózati környezet előkészítése

A felügyelt SQL-példány egy olyan biztonságos szolgáltatás, amely a saját Azure-beli virtuális hálózatán (VNetén) található. Felügyelt példány létrehozásához elő kell készítenie az Azure-beli hálózati környezetet, amely a következő elemekből tevődik össze:
 - Azure VNet, ahová a felügyelt példány kerül.
 - Alhálózat az Azure VNet-en belül, amely helyez ad majd a felügyelt példányoknak.
 - Felhasználó által megadott útvonal – ezen keresztül kommunikál a felügyelt példány a példányt vezérlő és kezelő Azure-szolgáltatásokkal.

Az alhálózat a felügyelt példányoknak van kijelölve, más erőforrás (például Azure-beli virtuális gép) nem hozható benne létre. Ebben a rövid útmutatóban két alhálózatot fog létrehozni az Azure VNeten, hogy elhelyezhesse a felügyelt példányokat a számukra kijelölt, a többi erőforrást pedig az alapértelmezett alhálózaton.

1. A felügyelt Azure SQL Database-példányhoz előkészített Azure-beli hálózati környezet üzembe helyezéséhez kattintson az alábbi gombra:

    <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener"> <img src="http://azuredeploy.net/deploybutton.png"> </a>

    A gomb lenyomásakor egy űrlap nyílik meg az Azure Portalon, amelyen az üzembe helyezés előtt konfigurálhatja a hálózati környezetet.

2. Igény szerint módosíthatja a VNetek és az alhálózatok nevét, és beállíthatja a hálózati erőforrásokhoz rendelt IP-címtartományokat. Ezután nyomja le a „Vásárlás” gombot a környezet létrehozásához és konfigurálásához:

    ![felügyelt példány környezetének létrehozása](./media/sql-database-managed-instance-get-started/create-mi-network-arm.png)

    > [!Note]
    > Az Azure Resource Manager két alhálózatot hoz létre a VNeten: egyet a felügyelt példányok számára (**ManagedInstances** néven), és egy másikat **Default** néven a többi erőforrás (például a felügyelt példányhoz való kapcsolódáshoz használt virtuális ügyfélszámítógép) számára. Ha nincs szüksége két alhálózatra, akkor később törölheti az alapértelmezett példányt, így azonban nem tudja majd végrehajtani a rövid útmutató 3. lépését ([Az ügyfélszámítógép előkészítése](#prepare-client-machine)).

    > [!Note]
    > Ha módosítja a VNet és az alhálózatok nevét, akkor jegyezze meg az új neveket, mivel a következő szakaszokban szüksége lesz rájuk. Az oktatóanyag további részében azt feltételezzük, hogy rendelkezik egy **MyNewVNet** nevű VNettel, egy **ManagedInstances** nevű alhálózattal a felügyelt SQL-példányok számára, és egy **Default** nevű alhálózattal a virtuális gépekhez és egyéb erőforrásokhoz.

## <a name="create-a-managed-instance"></a>Felügyelt példány létrehozása

A következő lépések azt mutatják be, hogyan hozhatja létre a felügyelt példányt az előzetes verzió jóváhagyása után.

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.
2. Keresse meg a **Felügyelt példány** lehetőséget, és válassza a **Felügyelt Azure SQL Database-példány (előzetes verzió)** elemet.
3. Kattintson a **Create** (Létrehozás) gombra.

   ![Felügyelt példány létrehozása](./media/sql-database-managed-instance-tutorial/managed-instance-create.png)

4. Válassza ki előfizetését, és ellenőrizze, hogy az előzetes verzió feltételeinél az **Elfogadva** állapot látható-e.

   ![felügyelt példány előzetes verziójának feltételei elfogadva](./media/sql-database-managed-instance-tutorial/preview-accepted.png)

5. Adja meg a kért adatokat a felügyelt példány űrlapján az alábbi táblázatban szereplő információk segítségével:

   | Beállítás| Ajánlott érték | Leírás |
   | ------ | --------------- | ----------- |
   |**Felügyelt példány neve**|Bármely érvényes név|Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket.|
   |**Felügyelt példány rendszergazdai bejelentkezési neve**|Bármely érvényes felhasználónév|Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket. Ne használja a „serveradmin” szerepkört, mert ez egy lefoglalt, kiszolgáló szintű szerepkör.| 
   |**Jelszó**|Bármely érvényes jelszó|A jelszónak legalább 16 karakter hosszúságúnak kell lennie, és teljesítenie kell [a meghatározott összetettségi követelményeket](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
   |**Erőforráscsoport**|A korábban létrehozott erőforráscsoport||
   |**Hely**|Az előzőleg kiválasztott hely|A régiókkal kapcsolatos információkért lásd [az Azure régióit](https://azure.microsoft.com/regions/) ismertető cikket.|
   |**Virtuális hálózat**|A korábban létrehozott virtuális hálózat| Ha az előző lépésben nem módosította a neveket, akkor válassza a **MyNewVNet/ManagedInstances** elemet. Ha módosította, akkor válassza ki az előző szakaszban megadott VNet-nevet és a felügyelt példány alhálózatát. **Ne használja az alapértelmezett alhálózatot, mert az nem a felügyelt példányok üzemeltetésére lett konfigurálva**. |

   ![felügyelt példány létrehozásának űrlapja](./media/sql-database-managed-instance-tutorial/managed-instance-create-form.png)

6. Kattintson a **Tarifacsomag** elemre a számítási és tárolási erőforrások méretezéséhez, valamint a lehetséges tarifacsomagok áttekintéséhez. Alapértelmezés szerint a példány 32 GB ingyenes tárterülettel rendelkezik, **amely nem feltétlenül elegendő az alkalmazásai számára**.
7. A csúszkák vagy a szövegmezők segítségével adja meg a tárterület méretét és a virtuális magok számát. 
   ![felügyelt példány tarifacsomagja](./media/sql-database-managed-instance-tutorial/managed-instance-pricing-tier.png)

8. Ha végzett, a beállítások mentéséhez kattintson az **Alkalmaz** gombra.  
9. Kattintson a **Létrehozás** elemre a felügyelt példány üzembe helyezéséhez.
10. Kattintson az **Értesítések** ikonra az üzembe helyezés állapotának megtekintéséhez.
 
   ![üzembe helyezés folyamatban](./media/sql-database-managed-instance-tutorial/deployment-progress.png)

11. Kattintson az **Üzembe helyezés folyamatban** értesítésre a felügyelt példány ablakának megnyitásához, amelyben részletesebben nyomon követheti az üzembehelyezési folyamatot.
 
   ![üzembe helyezés folyamatban 2](./media/sql-database-managed-instance-tutorial/managed-instance.png)

Amíg tart az üzembe helyezés, folytassa a következő eljárással.

> [!IMPORTANT]
> Az alhálózat első példánya esetében az üzembe helyezés általában jóval több időt (akár 24 óránál többet is) vesz igénybe, mint a további példányok esetében. Ne szakítsa meg az üzembehelyezési műveletet, ha az a vártnál tovább tart. Az első példány üzembe helyezéséhez csak átmenetileg szükséges ilyen hosszú idő. A nyilvános előzetes verzió használatának megkezdése után jelentős mértékben csökken majd az üzembehelyezési idő. Eltarthat néhány percig, amíg létrejön az alhálózatban a második felügyelt példány.

## <a name="prepare-client-machine"></a>Az ügyfélszámítógép előkészítése

Mivel a felügyelt SQL-példány az Ön privát virtuális hálózatán található, a felügyelt példányhoz való csatlakozáshoz és a lekérdezések végrehajtásához létre kell hoznia egy Azure-beli virtuális gépet egy olyan telepített SQL-ügyféleszközzel, amilyen például az SQL Server Management Studio vagy az SQL Operations Studio.

> [!Note]
> Azure-beli virtuális ügyfélszámítógép helyett [Pont–hely](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) kapcsolatot is konfigurálhat, és a felügyelt példányhoz a helyi számítógépről csatlakozhat.

Az összes szükséges eszközzel rendelkező virtuális ügyfélszámítógép létrehozásának legegyszerűbb módja az Azure Resource Manager-sablonok használata.

1. Kattintson az alábbi gombra (előtte jelentkezzen be az Azure Portalra egy másik böngészőlapon):

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjovanpop-msft%2Fazure-quickstart-templates%2Fsql-win-vm-w-tools%2F201-vm-win-vnet-sql-tools%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

2. A megnyíló űrlapon adja meg a virtuális gép nevét, illetve a virtuális géphez való kapcsolódáshoz használt rendszergazdai felhasználónevet és jelszót.

    ![ügyféloldali virtuális gép létrehozása](./media/sql-database-managed-instance-get-started/create-client-sql-vm.png)

    Ha nem módosította a VNet nevét és az alapértelmezett alhálózatot, akkor nem kell megváltoztatnia az utolsó két paramétert. Ha módosította, akkor adja meg a hálózati környezet beállításakor megadott értékeket.

3. Kattintson a „Vásárlás” gombra. Ekkor a rendszer üzembe helyezi az Azure-beli virtuális gépet az Ön által előkészített hálózaton.

4. Távoli asztali kapcsolattal csatlakozzon a virtuális géphez, és keresse meg a virtuális gépre automatikusan telepített SQL Server Management Studio vagy SQL Operations Studio szoftvert.

5. Nyissa meg az SSMS-t, majd írja be a felügyelt példány **gazdagépnevét** a **Server name** (Kiszolgáló neve) mezőbe, válassza az **SQL Server Authentication** (SQL Server-hitelesítés) lehetőséget, adja meg felhasználónevét és jelszavát a **Connect to Server** (Kapcsolódás kiszolgálóhoz) párbeszédpanelen, majd kattintson a **Connect** (Csatlakozás) gombra.

    ![ssms connect](./media/sql-database-managed-instance-tutorial/ssms-connect.png)  

A csatlakozás után megtekintheti a rendszer- és felhasználói adatbázisokat a Databases (Adatbázisok) csomóponton, valamint különféle objektumokat a Security (Biztonság), Server Objects (Kiszolgálóobjektumok), Replication (Replikáció), Management (Felügyelet), SQL Server Agent és XEvent Profiler csomópontokon.

## <a name="next-steps"></a>További lépések

 - [Alkalmazások csatlakoztatása egy felügyelt példányhoz](sql-database-managed-instance-connect-app.md).
 - [Adatbázisok migrálása a helyszínről egy felügyelt példányra](sql-database-managed-instance-migrate.md).


