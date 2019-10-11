---
title: SQL Server Linux Docker-tároló lekérdezése Azure Databricks
description: Ez a cikk azt ismerteti, hogyan helyezhetők üzembe Azure Databricks a virtuális hálózatban (más néven VNet Injection).
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 773ffe264446e6a4d9ef2e88634e4f2c9b8aeb45
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72273982"
---
# <a name="tutorial-query-a-sql-server-linux-docker-container-in-a-virtual-network-from-an-azure-databricks-notebook"></a>Oktatóanyag: SQL Server Linux Docker-tároló lekérdezése egy virtuális hálózaton egy Azure Databricks jegyzetfüzetből

Ez az oktatóanyag bemutatja, hogyan integrálhatja a Azure Databrickst egy SQL Server Linux Docker-tárolóval egy virtuális hálózaton. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Azure Databricks munkaterület üzembe helyezése virtuális hálózaton
> * Linux rendszerű virtuális gép telepítése nyilvános hálózaton
> * A Docker telepítése
> * A Microsoft SQL Server telepítése Linux Docker-tárolón
> * SQL Server lekérdezése a Databricks-jegyzetfüzetből a JDBC használatával

## <a name="prerequisites"></a>Előfeltételek

* Hozzon létre egy [Databricks-munkaterületet egy virtuális hálózaton](quickstart-create-databricks-workspace-vnet-injection.md).

* Telepítse [az Ubuntu for Windowst](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab).

* Töltse le az [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) alkalmazást.

## <a name="create-a-linux-virtual-machine"></a>Linuxos virtuális gépek létrehozása

1. A Azure Portal válassza ki a **Virtual Machines**ikonját. Ezután válassza a **+ Hozzáadás**lehetőséget.

    ![Új Azure-beli virtuális gép hozzáadása](./media/vnet-injection-sql-server/add-virtual-machine.png)

2. Az **alapvető beállítások** lapon válassza az Ubuntu Server 16,04 LTS lehetőséget. Módosítsa a virtuális gép méretét B1ms értékre, amely egy VCPU és 2 GB RAM-mal rendelkezik. A Linux SQL Server Docker-tároló minimális követelménye 2 GB. Válasszon ki egy rendszergazdai felhasználónevet és jelszót.

    ![Az új virtuális gép konfigurációjának alapjai lap](./media/vnet-injection-sql-server/create-virtual-machine-basics.png)

3. Navigáljon a **hálózatkezelés** lapra. Válassza ki a virtuális hálózatot és a Azure Databricks fürtöt tartalmazó nyilvános alhálózatot. Válassza a **felülvizsgálat + létrehozás**, majd a **Létrehozás** lehetőséget a virtuális gép telepítéséhez.

    ![A virtuális gép új konfigurációjának hálózatkezelés lapja](./media/vnet-injection-sql-server/create-virtual-machine-networking.png)

4. A telepítés befejezésekor navigáljon a virtuális géphez. Figyelje meg a nyilvános IP-címet és a virtuális hálózatot/alhálózatot az **áttekintésben**. Válassza ki a **nyilvános IP-címet**

    ![Virtuális gépek – áttekintés](./media/vnet-injection-sql-server/virtual-machine-overview.png)

5. Módosítsa a **hozzárendelést** **statikusra** , és adja meg a **DNS-név címkéjét**. Válassza a **Mentés**lehetőséget, majd indítsa újra a virtuális gépet.

    ![Nyilvános IP-cím konfigurálása](./media/vnet-injection-sql-server/virtual-machine-staticip.png)

6. Válassza a **hálózatkezelés** fület a **Beállítások**területen. Figyelje meg, hogy a Azure Databricks központi telepítés során létrehozott hálózati biztonsági csoport a virtuális géphez van társítva. Válassza a **bejövő Port hozzáadása szabály**lehetőséget.

7. Adjon hozzá egy szabályt a 22-es port megnyitásához az SSH-hoz. Használja az alábbi beállításokat:
    
    |Beállítás|Ajánlott érték|Leírás|
    |-------|---------------|-----------|
    |Forrás|IP-címek|Az IP-címek azt határozzák meg, hogy a szabály engedélyezi vagy letiltja a bejövő forgalmat egy adott forrás IP-címről.|
    |Forrás IP-címek|< a nyilvános IP-cím @ no__t-0|Adja meg a nyilvános IP-címét. A nyilvános IP-cím megkereséséhez keresse fel a [Bing.com](https://www.bing.com/) , és keressen rá a **"saját IP"** kifejezésre.|
    |Forrásporttartományok|*|Bármilyen portról érkező forgalom engedélyezése.|
    |Cél|IP-címek|Az IP-címek azt határozzák meg, hogy a szabály engedélyezi vagy megtagadja a kimenő forgalmat egy adott forrás IP-címhez.|
    |Cél IP-címei|< a virtuális gép nyilvános IP-címe @ no__t-0|Adja meg a virtuális gép nyilvános IP-címét. Ezt a virtuális gép **Áttekintés** lapján találja.|
    |Célporttartományok|22|Nyissa meg a 22-es portot az SSH-hoz.|
    |Prioritás|290|Adja meg a szabályt prioritásként.|
    |Név|SSH-databricks-oktatóanyag – virtuális gép|Adjon nevet a szabálynak.|


    ![Bejövő biztonsági szabály hozzáadása a 22-es porthoz](./media/vnet-injection-sql-server/open-port.png)

8. Adjon hozzá egy szabályt az SQL 1433-es portjának megnyitásához a következő beállításokkal:

    |Beállítás|Ajánlott érték|Leírás|
    |-------|---------------|-----------|
    |Forrás|IP-címek|Az IP-címek azt határozzák meg, hogy a szabály engedélyezi vagy letiltja a bejövő forgalmat egy adott forrás IP-címről.|
    |Forrás IP-címek|10.179.0.0/16|Adja meg a virtuális hálózat címtartomány-tartományát.|
    |Forrásporttartományok|*|Bármilyen portról érkező forgalom engedélyezése.|
    |Cél|IP-címek|Az IP-címek azt határozzák meg, hogy a szabály engedélyezi vagy megtagadja a kimenő forgalmat egy adott forrás IP-címhez.|
    |Cél IP-címei|< a virtuális gép nyilvános IP-címe @ no__t-0|Adja meg a virtuális gép nyilvános IP-címét. Ezt a virtuális gép **Áttekintés** lapján találja.|
    |Célporttartományok|1433|Nyissa meg SQL Server a 22-es portot.|
    |Prioritás|300|Adja meg a szabályt prioritásként.|
    |Név|SQL-databricks-oktatóanyag – virtuális gép|Adjon nevet a szabálynak.|

    ![Bejövő biztonsági szabály hozzáadása a 1433-as porthoz](./media/vnet-injection-sql-server/open-port2.png)

## <a name="run-sql-server-in-a-docker-container"></a>SQL Server futtatása Docker-tárolóban

1. Nyissa meg az [Ubuntu for Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab)alkalmazást, vagy bármilyen más eszközt, amely lehetővé teszi az SSH-t a virtuális géphez. A Azure Portalban navigáljon a virtuális géphez, és válassza a **Kapcsolódás** lehetőséget a kapcsolódáshoz szükséges SSH-parancs beszerzéséhez.

    ![Csatlakozás virtuális géphez](./media/vnet-injection-sql-server/vm-ssh-connect.png)

2. Adja meg a parancsot az Ubuntu-terminálban, és adja meg a virtuális gép konfigurálásakor létrehozott rendszergazdai jelszót.

    ![Ubuntu Terminal SSH-bejelentkezés](./media/vnet-injection-sql-server/vm-login-terminal.png)

3. A következő parancs használatával telepítse a Docker-t a virtuális gépre.

    ```bash
    sudo apt-get install docker.io
    ```

    Ellenőrizze a Docker telepítését a következő paranccsal:

    ```bash
    sudo docker --version
    ```

4. Telepítse a rendszerképet.

    ```bash
    sudo docker pull mcr.microsoft.com/mssql/server:2017-latest
    ```

    Keresse meg a lemezképeket.

    ```bash
    sudo docker images
    ```

5. Futtassa a tárolót a rendszerképből.

    ```bash
    sudo docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=Password1234' -p 1433:1433 --name sql1  -d mcr.microsoft.com/mssql/server:2017-latest
    ```

    Ellenőrizze, hogy a tároló fut-e.

    ```bash
    sudo docker ps -a
    ```

## <a name="create-a-sql-database"></a>SQL-adatbázis létrehozása

1. Nyissa meg SQL Server Management Studio, és kapcsolódjon a kiszolgálóhoz a kiszolgáló neve és az SQL-hitelesítés használatával. A bejelentkezési Felhasználónév **sa** , és a jelszó a Docker-parancsban beállított jelszó. A példában szereplő parancsban található jelszó `Password1234`.

    ![Kapcsolódás SQL Server a SQL Server Management Studio használatával](./media/vnet-injection-sql-server/ssms-login.png)

2. A sikeres csatlakozás után válassza az **Új lekérdezés** lehetőséget, és írja be a következő kódrészletet egy adatbázis, egy tábla létrehozásához, és szúrjon be néhány rekordot a táblába.

    ```SQL
    CREATE DATABASE MYDB;
    GO
    USE MYDB;
    CREATE TABLE states(Name VARCHAR(20), Capitol VARCHAR(20));
    INSERT INTO states VALUES ('Delaware','Dover');
    INSERT INTO states VALUES ('South Carolina','Columbia');
    INSERT INTO states VALUES ('Texas','Austin');
    SELECT * FROM states
    GO
    ```

    ![SQL Server adatbázis létrehozásához szükséges lekérdezés](./media/vnet-injection-sql-server/create-database.png)

## <a name="query-sql-server-from-azure-databricks"></a>SQL Server lekérdezése Azure Databricks

1. Navigáljon a Azure Databricks munkaterületre, és ellenőrizze, hogy az előfeltételek részeként létrehozott-e fürtöt. Ezután válassza **a jegyzetfüzet létrehozása**lehetőséget. Adjon nevet a jegyzetfüzetnek, válassza ki a *Python* nyelvet, majd válassza ki a létrehozott fürtöt.

    ![Új Databricks notebook-beállítások](./media/vnet-injection-sql-server/create-notebook.png)

2. A következő parancs használatával pingelheti a SQL Server virtuális gép belső IP-címét. A pingelésnek sikeresnek kell lennie. Ha nem, ellenőrizze, hogy fut-e a tároló, és tekintse át a hálózati biztonsági csoport (NSG) konfigurációját.

    ```python
    %sh
    ping 10.179.64.4
    ```

    Az nslookup parancs használatával is áttekintheti a következőt:.

    ```python
    %sh
    nslookup databricks-tutorial-vm.westus2.cloudapp.azure.com
    ```

3. Miután sikeresen elvégezte a SQL Server pingelését, lekérdezheti az adatbázist és a táblákat. Futtassa a következő Python-kódot:

    ```python
    jdbcHostname = "10.179.64.4"
    jdbcDatabase = "MYDB"
    userName = 'SA'
    password = 'Password1234'
    jdbcPort = 1433
    jdbcUrl = "jdbc:sqlserver://{0}:{1};database={2};user={3};password={4}".format(jdbcHostname, jdbcPort, jdbcDatabase, userName, password)

    df = spark.read.jdbc(url=jdbcUrl, table='states')
    display(df)
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, a Azure Databricks munkaterületet és az összes kapcsolódó erőforrást. A feladatok törlése elkerüli a szükségtelen számlázást. Ha a jövőben a Azure Databricks munkaterület használatát tervezi, akkor leállíthatja a fürtöt, és később újraindíthatja. Ha nem kívánja tovább használni ezt a Azure Databricks munkaterületet, az oktatóanyagban létrehozott összes erőforrást az alábbi lépések segítségével törölheti:

1. A Azure Portal bal oldali menüjében kattintson az **erőforráscsoportok** elemre, majd kattintson a létrehozott erőforráscsoport nevére.

2. Az erőforráscsoport lapon válassza a **Törlés**lehetőséget, írja be a törölni kívánt erőforrás nevét a szövegmezőbe, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

A következő cikkből megtudhatja, hogyan kinyerheti, átalakíthatja és betöltheti az adatgyűjtést Azure Databricks használatával.
> [!div class="nextstepaction"]
> [Oktatóanyag: adatok kinyerése, átalakítása és betöltése a Azure Databricks használatával](databricks-extract-load-sql-data-warehouse.md)
