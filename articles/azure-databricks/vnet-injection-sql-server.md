---
title: SQL Server Linux Docker-tároló lekérdezése Azure Databricks-szel
description: Ez a cikk ismerteti, hogyan telepítheti az Azure Databricks a virtuális hálózatra, más néven virtuális hálózat injektálás.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 460079248e6cbd939c36b84f94cac41dce4dda2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73747663"
---
# <a name="tutorial-query-a-sql-server-linux-docker-container-in-a-virtual-network-from-an-azure-databricks-notebook"></a>Oktatóanyag: SQL Server Linux Docker-tároló lekérdezése egy virtuális hálózatban egy Azure Databricks-jegyzetfüzetből

Ez az oktatóanyag bemutatja, hogyan integrálhatja az Azure Databricks egy SQL Server Linux Docker-tároló egy virtuális hálózatban. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Azure Databricks-munkaterület üzembe helyezése virtuális hálózatra
> * Linuxos virtuális gép telepítése nyilvános hálózatba
> * A Docker telepítése
> * A Microsoft SQL Server telepítése Linux docker-tárolóra
> * Az SQL Server lekérdezése a JDBC használatával Databricks-jegyzetfüzetből

## <a name="prerequisites"></a>Előfeltételek

* [Databricks-munkaterület létrehozása virtuális hálózatban.](quickstart-create-databricks-workspace-vnet-injection.md)

* Telepítse [az Ubuntu for Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab)rendszert .

* Töltse le az [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) alkalmazást.

## <a name="create-a-linux-virtual-machine"></a>Linuxos virtuális gép létrehozása

1. Az Azure Portalon válassza ki a **virtuális gépek**ikonját. Ezután válassza **a + Add**lehetőséget.

    ![Új Azure virtuális gép hozzáadása](./media/vnet-injection-sql-server/add-virtual-machine.png)

2. Az **Alapok** lapon válassza az Ubuntu Server 18.04 LTS lehetőséget, és módosítsa a virtuális gép méretét B2-re. Válasszon rendszergazdai felhasználónevet és jelszót.

    ![A virtuális gép új konfigurációjának alapjai lapja](./media/vnet-injection-sql-server/create-virtual-machine-basics.png)

3. Keresse meg a **Hálózat** lapot. Válassza ki a virtuális hálózatot és az Azure Databricks-fürtöt tartalmazó nyilvános alhálózatot. Válassza **a Véleményezés + létrehozás**lehetőséget, majd a **Létrehozás lehetőséget** a virtuális gép üzembe helyezéséhez.

    ![Az új virtuálisgép-konfiguráció Hálózat lapja](./media/vnet-injection-sql-server/create-virtual-machine-networking.png)

4. Amikor a központi telepítés befejeződött, keresse meg a virtuális gépet. Figyelje meg a nyilvános IP-címet és a virtuális hálózatot/alhálózatot az **Áttekintés ben.** A **nyilvános IP-cím** kiválasztása

    ![Virtuális gép – áttekintés](./media/vnet-injection-sql-server/virtual-machine-overview.png)

5. Módosítsa a **hozzárendelést** **Statikusra,** és adjon meg egy **DNS-névcímkét**. Válassza a **Mentés**lehetőséget, és indítsa újra a virtuális gépet.

    ![Nyilvános IP-cím konfigurációja](./media/vnet-injection-sql-server/virtual-machine-staticip.png)

6. Válassza a **Hálózat** lapot a **Beállítások csoportban.** Figyelje meg, hogy az Azure Databricks üzembe helyezése során létrehozott hálózati biztonsági csoport a virtuális géphez van társítva. Válassza **a Bejövő portszabály hozzáadása lehetőséget.**

7. Adjon hozzá egy szabályt az SSH 22-es portjának megnyitásához. Használja a következő beállításokat:
    
    |Beállítás|Ajánlott érték|Leírás|
    |-------|---------------|-----------|
    |Forrás|IP-címek|Az IP-címek azt határozzák meg, hogy ez a szabály engedélyezi vagy megtagadja az adott forrás IP-címéről érkező bejövő forgalmat.|
    |Forrás IP-címek|<nyilvános ip\>|Adja meg a nyilvános IP-címét. Megtalálhatja a nyilvános IP-címét, ha [ellátogat bing.com](https://www.bing.com/) és keresi a **"my IP"**.|
    |Forrásporttartományok|*|Bármely portról engedélyezi a forgalmat.|
    |Cél|IP-címek|Az IP-címek azt határozzák meg, hogy egy adott forrás IP-cím kimenő forgalmát ez a szabály engedélyezi vagy megtagadja.|
    |Cél IP-címek|<a vm nyilvános ip\>|Adja meg a virtuális gép nyilvános IP-címét. Ezt a virtuális gép **Áttekintés lapján** találja.|
    |Célporttartományok|22|Nyissa meg a 22-es portot az SSH számára.|
    |Prioritás|290|Adja meg a szabály elsőbbséget.|
    |Név|ssh-databricks-bemutató-vm|Adjon nevet a szabálynak.|


    ![Bejövő biztonsági szabály hozzáadása a 22-es porthoz](./media/vnet-injection-sql-server/open-port.png)

8. Adjon hozzá egy szabályt az 1433-as port SQL-hez való megnyitásához a következő beállításokkal:

    |Beállítás|Ajánlott érték|Leírás|
    |-------|---------------|-----------|
    |Forrás|Bármelyik|A forrás azt adja meg, hogy egy adott forrás IP-címéről érkező bejövő forgalmat ez a szabály engedélyezze vagy megtagadja.|
    |Forrásporttartományok|*|Bármely portról engedélyezi a forgalmat.|
    |Cél|IP-címek|Az IP-címek azt határozzák meg, hogy egy adott forrás IP-cím kimenő forgalmát ez a szabály engedélyezi vagy megtagadja.|
    |Cél IP-címek|<a vm nyilvános ip\>|Adja meg a virtuális gép nyilvános IP-címét. Ezt a virtuális gép **Áttekintés lapján** találja.|
    |Célporttartományok|1433|Nyissa meg a 22-es portot az SQL Server hez.|
    |Prioritás|300|Adja meg a szabály elsőbbséget.|
    |Név|sql-databricks-tutorial-vm|Adjon nevet a szabálynak.|

    ![Bejövő biztonsági szabály hozzáadása az 1433-as porthoz](./media/vnet-injection-sql-server/open-port2.png)

## <a name="run-sql-server-in-a-docker-container"></a>SQL Server futtatása Docker-tárolóban

1. Nyissa meg [az Ubuntu for Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab), vagy bármely más eszköz, amely lehetővé teszi, hogy SSH a virtuális gép. Keresse meg a virtuális gépet az Azure Portalon, és válassza a **Csatlakozás** lehetőséget a csatlakozáshoz szükséges SSH-parancs lefelvételéhez.

    ![Csatlakozás virtuális géphez](./media/vnet-injection-sql-server/vm-ssh-connect.png)

2. Írja be a parancsot az Ubuntu terminál, és adja meg a rendszergazdai jelszót létrehozott, amikor beállította a virtuális gép.

    ![Ubuntu terminál SSH bejelentkezés](./media/vnet-injection-sql-server/vm-login-terminal.png)

3. A docker virtuális gépen való telepítéséhez használja a következő parancsot.

    ```bash
    sudo apt-get install docker.io
    ```

    Ellenőrizze a Docker telepítését a következő paranccsal:

    ```bash
    sudo docker --version
    ```

4. Telepítse a lemezképet.

    ```bash
    sudo docker pull mcr.microsoft.com/mssql/server:2017-latest
    ```

    Nézd meg a képeket.

    ```bash
    sudo docker images
    ```

5. Futtassa a tárolót a lemezképből.

    ```bash
    sudo docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=Password1234' -p 1433:1433 --name sql1  -d mcr.microsoft.com/mssql/server:2017-latest
    ```

    Ellenőrizze, hogy fut-e a tároló.

    ```bash
    sudo docker ps -a
    ```

## <a name="create-a-sql-database"></a>SQL-adatbázis létrehozása

1. Nyissa meg az SQL Server Management Studio alkalmazást, és csatlakozzon a kiszolgálóhoz a kiszolgáló nevével és az SQL-hitelesítéssel. A bejelentkezési felhasználónév **SA,** a jelszó pedig a Docker-parancsban beállított jelszó. A példaparancsban a `Password1234`jelszó a .

    ![Csatlakozás az SQL Server kiszolgálóhoz az SQL Server Management Studio használatával](./media/vnet-injection-sql-server/ssms-login.png)

2. A sikeres csatlakozás takarásában válassza az **Új lekérdezés** lehetőséget, és írja be a következő kódrészletet adatbázis, tábla létrehozásához és néhány rekord beszúrásához a táblába.

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

    ![SQL Server-adatbázis létrehozásához lekérdező lekérdezés](./media/vnet-injection-sql-server/create-database.png)

## <a name="query-sql-server-from-azure-databricks"></a>SQL Server lekérdezése az Azure Databricks-ből

1. Keresse meg az Azure Databricks-munkaterületet, és ellenőrizze, hogy az előfeltételek részeként létrehozott-e fürtöt. Ezután válassza **a Jegyzetfüzet létrehozása**lehetőséget. Adjon nevet a jegyzetfüzetnek, válassza a *Python* nyelvet, és válassza ki a létrehozott fürtöt.

    ![Új Databricks notebook beállítások](./media/vnet-injection-sql-server/create-notebook.png)

2. A következő paranccsal pingelje az SQL Server virtuális gép belső IP-címét. Ennek a pingnek sikeresnek kell lennie. Ha nem, ellenőrizze, hogy fut-e a tároló, és tekintse át a hálózati biztonsági csoport (NSG) konfigurációját.

    ```python
    %sh
    ping 10.179.64.4
    ```

    Az nslookup paranccsal is áttekintheti a vezérlőt.

    ```python
    %sh
    nslookup databricks-tutorial-vm.westus2.cloudapp.azure.com
    ```

3. Miután sikeresen pingelte az SQL Servert, lekérdezheti az adatbázist és a táblákat. Futtassa a következő python kódot:

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

Ha már nincs szükség, törölje az erőforráscsoportot, az Azure Databricks munkaterületet és az összes kapcsolódó erőforrást. A feladat törlése szükségtelen számlázást. Ha azt tervezi, hogy az Azure Databricks munkaterületet a jövőben, leállíthatja a fürtöt, és később újraindíthatja. Ha nem fogja tovább használni ezt az Azure Databricks-munkaterületet, törölje az oktatóanyagban létrehozott összes erőforrást az alábbi lépésekkel:

1. Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok** elemre, majd a létrehozott erőforráscsoport nevére.

2. Az erőforráscsoport lapján válassza a **Törlés**lehetőséget, írja be a törlendő erőforrás nevét a szövegmezőbe, majd kattintson ismét a **Törlés** gombra.

## <a name="next-steps"></a>További lépések

Az Azure Databricks használatával az adatok kinyerése, átalakítása és betöltése című cikkhez továbbjut.
> [!div class="nextstepaction"]
> [Oktatóanyag: Adatok kinyerése, átalakítása és betöltése az Azure Databricks használatával](databricks-extract-load-sql-data-warehouse.md)
