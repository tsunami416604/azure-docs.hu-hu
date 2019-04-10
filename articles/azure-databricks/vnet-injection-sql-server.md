---
title: A lekérdezés egy SQL Server Linux Docker-tárolót egy virtuális hálózaton lévő az Azure Databricks-jegyzetfüzet
description: Ez a cikk ismerteti, hogyan helyezhet üzembe az Azure Databricks a virtuális hálózathoz, más néven a virtuális hálózatok közötti injektálási.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 345e07fac30f4ad0c8e9918cb8a1ff0fb8aeb811
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59288951"
---
# <a name="tutorial-query-a-sql-server-linux-docker-container-in-a-virtual-network-from-an-azure-databricks-notebook"></a>Oktatóanyag: A lekérdezés egy SQL Server Linux Docker-tárolót egy virtuális hálózaton lévő az Azure Databricks-jegyzetfüzet

Ez az oktatóanyag bemutatja, hogyan integrálható az Azure Databricks és a egy SQL Server Linux Docker-tárolót egy virtuális hálózaton. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Azure Databricks-munkaterület üzembe helyezése virtuális hálózaton
> * Linux rendszerű virtuális gép telepítése egy nyilvános hálózaton
> * A Docker telepítése
> * Telepítse a Microsoft SQL Server Linux docker-tároló
> * Az SQL Server JDBC segítségével a Databricks-jegyzetfüzet lekérdezése

## <a name="prerequisites"></a>Előfeltételek

* Hozzon létre egy [egy virtuális hálózatot a Databricks-munkaterület](quickstart-create-databricks-workspace-vnet-injection.md).

* Telepítés [Ubuntu, a Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab).

* Töltse le az [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) alkalmazást.

## <a name="create-a-linux-virtual-machine"></a>Linuxos virtuális gépek létrehozása

1. Az Azure Portalon válassza ki a ikonjára **virtuális gépek**. Ezután válassza ki **+ Hozzáadás**.

    ![Új Azure virtuális gép hozzáadása](./media/vnet-injection-sql-server/add-virtual-machine.png)

2. Az a **alapjai** lapra, válassza a Ubuntu Server 16.04 LTS. Módosítsa a Virtuálisgép-méretet B1ms, amely rendelkezik egy vcpu-k és a 2 GB RAM. A minimális rendszerkövetelményt a Linux rendszerű SQL Server Docker-tárolóban 2 GB. Válassza ki a rendszergazda felhasználónevét és jelszavát.

    ![Új virtuális gép konfigurációs lapján alapjai](./media/vnet-injection-sql-server/create-virtual-machine-basics.png)

3. Keresse meg a **hálózatkezelés** fülre. Válassza ki a virtuális hálózat és a nyilvános alhálózatot, amely tartalmazza az Azure Databricks-fürt. Válassza ki **felülvizsgálat + létrehozása**, majd **létrehozás** a virtuális gép üzembe helyezése.

    ![Hálózat lap az új virtuálisgép-konfiguráció](./media/vnet-injection-sql-server/create-virtual-machine-networking.png)

4. Ha a telepítés befejeződött, lépjen a virtuális gép. Figyelje meg, hogy a nyilvános IP-cím és a virtuális hálózat/alhálózat a **áttekintése**. Válassza ki a **nyilvános IP-cím**

    ![Virtuális gépek – áttekintés](./media/vnet-injection-sql-server/virtual-machine-overview.png)

5. Módosítsa a **hozzárendelés** való **statikus** , és adja meg egy **DNS-névcímke**. Válassza ki **mentése**, és indítsa újra a virtuális gépet.

    ![Nyilvános IP-cím konfigurálása](./media/vnet-injection-sql-server/virtual-machine-staticip.png)

6. Válassza ki a **hálózatkezelés** lapjára **beállítások**. Figyelje meg, hogy a virtuális gép társítva-e a hálózati biztonsági csoportot, amely az Azure Databricks üzembe helyezés során jött létre. Válassza ki **bejövőport-szabály hozzáadása**.

7. Adjon hozzá egy szabályt a 22-es port megnyitásához az SSH-hoz. Használja a következő beállításokat:
    
    |Beállítás|Ajánlott érték|Leírás|
    |-------|---------------|-----------|
    |Forrás|IP-címek|IP-címek Megadja, hogy egy adott forrás IP-cím lesz engedélyezett vagy letiltott Ez a szabály érkező bejövő forgalmat.|
    |Forrás IP-címek|< a nyilvános IP-cím\>|Adja meg a a nyilvános IP-címet. Látogasson el a nyilvános IP-cím megkereséséhez [bing.com](https://www.bing.com/) kifejezésre való kereséssel **"saját IP-cím"**.|
    |Forrásporttartományok|*|Bármely porton érkező adatforgalom engedélyezéséhez.|
    |Cél|IP-címek|IP-címek megadja a kimenő forgalmat egy adott forrás IP-cím lesz engedélyezett vagy letiltott a szabály által.|
    |Destination IP addresses|< a virtuális gép nyilvános IP-cím\>|Adja meg a virtuális gép nyilvános IP-címet. A megtalálhatja a **áttekintése** a virtuális gép oldalán.|
    |Célporttartományok|22|Nyissa meg a 22-es port az SSH-hoz.|
    |Prioritás|290|Adjon a szabálynak prioritást.|
    |Name (Név)|ssh-databricks-tutorial-vm|Adjon nevet a szabálynak.|


    ![A 22-es porton bejövő biztonsági szabály felvétele](./media/vnet-injection-sql-server/open-port.png)

8. Adjon hozzá egy szabályt, amely az 1433-as port megnyitása az SQL a következő beállításokkal:

    |Beállítás|Ajánlott érték|Leírás|
    |-------|---------------|-----------|
    |Forrás|IP-címek|IP-címek Megadja, hogy egy adott forrás IP-cím lesz engedélyezett vagy letiltott Ez a szabály érkező bejövő forgalmat.|
    |Forrás IP-címek|10.179.0.0/16|Adja meg a címtartományt a virtuális hálózat.|
    |Forrásporttartományok|*|Bármely porton érkező adatforgalom engedélyezéséhez.|
    |Cél|IP-címek|IP-címek megadja a kimenő forgalmat egy adott forrás IP-cím lesz engedélyezett vagy letiltott a szabály által.|
    |Destination IP addresses|< a virtuális gép nyilvános IP-cím\>|Adja meg a virtuális gép nyilvános IP-címet. A megtalálhatja a **áttekintése** a virtuális gép oldalán.|
    |Célporttartományok|1433|Nyissa meg a 22-es portot az SQL Serverhez.|
    |Prioritás|300|Adjon a szabálynak prioritást.|
    |Name (Név)|sql-databricks-tutorial-vm|Adjon nevet a szabálynak.|

    ![Az 1433-as porton bejövő biztonsági szabály felvétele](./media/vnet-injection-sql-server/open-port2.png)

## <a name="run-sql-server-in-a-docker-container"></a>Az SQL Server futtatása Docker-tárolóban

1. Nyissa meg [Ubuntu for Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab), vagy más eszköz, amely lehetővé teszi az SSH a virtuális géppel. Keresse meg az Azure Portalon, és válassza a virtuális gép **Connect** , csatlakoznia kell az SSH parancsot.

    ![Csatlakozás virtuális géphez](./media/vnet-injection-sql-server/vm-ssh-connect.png)

2. Írja be a parancsot az Ubuntu terminált, és adja meg a rendszergazdai jelszót hozott létre a virtuális gép konfigurálásakor.

    ![Ubuntu terminál SSH bejelentkezés](./media/vnet-injection-sql-server/vm-login-terminal.png)

3. A következő paranccsal telepítheti a Dockert a virtuális gépen.

    ```bash
    sudo apt-get install docker.io
    ```

    Ellenőrizze a telepítés a Docker a következő paranccsal:

    ```bash
    sudo docker --version
    ```

4. A lemezkép telepítéséhez.

    ```bash
    sudo docker pull mcr.microsoft.com/mssql/server:2017-latest
    ```

    Ellenőrizze a lemezképeket.

    ```bash
    sudo docker images
    ```

5. Futtassa a tároló a lemezképet.

    ```bash
    sudo docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=Password1234' -p 1433:1433 --name sql1  -d mcr.microsoft.com/mssql/server:2017-latest
    ```

    Győződjön meg arról, hogy a tároló fut-e.

    ```bash
    sudo docker ps -a
    ```

## <a name="create-a-sql-database"></a>SQL-adatbázis létrehozása

1. SQL Server Management Studio megnyitása és csatlakozás a kiszolgálóhoz, a kiszolgáló nevét és az SQL-hitelesítés használatával. A bejelentkezési felhasználónevet, a rendszer **SA** és a jelszót pedig a jelszót, állítsa be a Docker-parancsban. A jelszó az a példában a parancs `Password1234`.

    ![Csatlakozás az SQL Serverhez az SQL Server Management Studio használatával](./media/vnet-injection-sql-server/ssms-login.png)

2. Miután sikeresen csatlakozott, válassza ki a **új lekérdezés** és adja meg az alábbi kódrészlet egy adatbázis, egy tábla létrehozásához és néhány rekord beillesztése a táblázatban.

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

    ![Hozzon létre egy SQL Server-adatbázis lekérdezése](./media/vnet-injection-sql-server/create-database.png)

## <a name="query-sql-server-from-azure-databricks"></a>Lekérdezés SQL-kiszolgálóhoz az Azure Databricks

1. Keresse meg az Azure Databricks-munkaterület, és győződjön meg arról, hogy az Előfeltételek részeként létrehozott egy fürtöt. Ezután válassza ki **hozzon létre egy jegyzetfüzetet**. Adja meg a jegyzetfüzet nevét, válassza ki *Python* nyelvet, és válassza ki a létrehozott fürtöt.

    ![Databricks-jegyzetfüzet új beállításai](./media/vnet-injection-sql-server/create-notebook.png)

2. A következő parancs használatával az SQL Server virtuális gép belső IP-cím. A ping sikeres legyen. Ha nem, ellenőrizze, hogy a tároló fut-e, tekintse át a hálózati biztonsági csoport (NSG) konfigurációját.

    ```python
    %sh
    ping 10.179.64.4
    ```

    Az nslookup parancs segítségével is áttekintheti.

    ```python
    %sh
    nslookup databricks-tutorial-vm.westus2.cloudapp.azure.com
    ```

3. Ha Ön már ping paranccsal sikerül elérni az SQL Server, lekérdezheti, ha az adatbázis és a táblák. Futtassa a következő python kódot:

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

Ha már nincs rá szükség, törölje az erőforráscsoportot, az Azure Databricks-munkaterület és minden kapcsolódó erőforrás. A feladat törlése elkerülhető a felesleges számlázás. Ha a jövőben használni az Azure Databricks-munkaterület, állítsa le a fürtöt, és indítsa újra később. Ha nem kívánja tovább használhatja az Azure Databricks-munkaterület, törölje az ebben az oktatóanyagban az alábbi lépésekkel létrehozott összes erőforrást:

1. Az Azure Portal baloldali menüjében kattintson **erőforráscsoportok** és kattintson a létrehozott erőforráscsoport nevét.

2. Az erőforráscsoport lapján, válassza ki a **törlése**, adja meg az erőforrás törlése a szövegmezőbe, és válassza ki a **törlése** újra.

## <a name="next-steps"></a>További lépések

Folytassa a következő cikk megtudhatja, hogyan kinyerése, átalakítása és betöltése az Azure Databricks segítségével adatokat.
> [!div class="nextstepaction"]
> [Oktatóanyag: A kinyerési, átalakítási és az adatok betöltése az Azure Databricks használatával](databricks-extract-load-sql-data-warehouse.md)
