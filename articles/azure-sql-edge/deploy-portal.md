---
title: Az Azure SQL Edge (előzetes verzió) üzembe helyezése a Azure Portal használatával
description: Ismerje meg, hogyan helyezheti üzembe az Azure SQL Edge (előzetes verzió) szolgáltatását a Azure Portal használatával
keywords: SQL Edge üzembe helyezése
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 7af4264860f8d9950515cd5302f03822e7cbac39
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816864"
---
# <a name="deploy-azure-sql-edge-preview"></a>Az Azure SQL Edge üzembe helyezése (előzetes verzió) 

Az Azure SQL Edge (előzetes verzió) egy IoT és Azure IoT Edge üzemelő példányokhoz optimalizált, összehasonlítható adatbázis-motor. Lehetőséget biztosít nagy teljesítményű adattárolási és feldolgozási réteg létrehozására a IoT alkalmazások és megoldások számára. Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre egy Azure SQL Edge-modult Azure IoT Edge segítségével a Azure Portal használatával.

## <a name="before-you-begin"></a>Előkészületek

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
* Hozzon létre egy [Azure-IoT hub](../iot-hub/iot-hub-create-through-portal.md).
* [IoT Edge eszköz regisztrálása a Azure Portal](../iot-edge/how-to-register-device-portal.md).
* Készítse elő a IoT Edge eszközt [IoT Edge modul üzembe helyezéséhez a Azure Portal](../iot-edge/how-to-deploy-modules-portal.md).

> [!NOTE]
> Ha IoT Edge eszközként szeretne üzembe helyezni egy Azure-beli linuxos virtuális gépet, tekintse meg ezt a rövid [útmutatót](../iot-edge/quickstart-linux.md).

## <a name="deploy-sql-edge-module-from-azure-marketplace"></a>Az SQL Edge-modul üzembe helyezése az Azure Marketplace-en

Az Azure Marketplace egy online alkalmazások és szolgáltatások piaca, ahol az Azure-on futó Nagyvállalati alkalmazások és megoldások széles körét böngészheti, beleértve a [IoT Edge modulokat](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)is. Az Azure SQL Edge üzembe helyezhető egy peremhálózati eszközön a piactéren.

1. Keresse meg az Azure SQL Edge-modult az Azure piactéren.<br><br>

   ![SQL Edge a piactéren](media/deploy-portal/find-offer-marketplace.png)

2. Válassza ki az igényeinek leginkább megfelelő szoftvercsomagot, és kattintson a **Létrehozás**gombra. <br><br>

   ![Válassza ki a megfelelő szoftvercsomagot](media/deploy-portal/pick-correct-plan.png)

3. A IoT Edge modul megcélzott eszközei lapon adja meg a következő adatokat, majd kattintson a **Létrehozás** gombra.

   |**Mező**  |**Leírás**  |
   |---------|---------|
   |Előfizetés  |  Az Azure-előfizetés, amely alatt a IoT Hub létre lett hozva |
   |IoT Hub   |  Azon IoT Hub neve, ahol a IoT Edge eszköz regisztrálva van, majd válassza az "üzembe helyezés eszközre" lehetőséget.|
   |IoT Edge eszköz neve  |  Annak a IoT Edge eszköznek a neve, amelyen az SQL Edge üzembe lett helyezve |

4. A **modulok beállítása** lapon navigáljon az üzembe helyezési modulok szakaszhoz, és kattintson a **Konfigurálás** az SQL Edge-modulra elemre. 

5. A **IoT Edge egyéni modulok** ablaktáblán válassza ki a környezeti változók kívánt értékeit, és/vagy szabja testre a modul létrehozási beállításait és kívánt tulajdonságait. A támogatott környezeti változók teljes listájáért tekintse meg [SQL Server tároló környezeti változóit](/sql/linux/sql-server-linux-configure-environment-variables/).

   |**Paraméter**  |**Leírás**|
   |---------|---------|
   | Név | A modul neve. |
   |SA_PASSWORD  | Erős jelszót kell megadnia az SQL Edge rendszergazdai fiókjához. |
   |MSSQL_LCID   | Beállítja a SQL Server használandó nyelvi azonosítót. Például 1036 a francia. |
   |MSSQL_COLLATION | Beállítja SQL Server alapértelmezett rendezését. Ez a beállítás felülbírálja a nyelvi azonosító (LCID) alapértelmezett leképezését a rendezéshez. |

   > [!NOTE]
   > A modulban ne módosítsa vagy frissítse a **rendszerkép URI-ját** vagy a **ACCEPT_EULA** beállításait.

6. A **IoT Edge egyéni modulok** ablaktáblán frissítse a tároló létrehozási beállításai kívánt értékét a **gazdagép portjához**. Ha egynél több SQL DB Edge-modult kell üzembe helyeznie, frissítse a csatlakoztatások lehetőséget, és hozzon létre egy új forrás & cél párt az állandó kötethez. A csatlakoztatásokról és a kötetekről a [kötetek használata](https://docs.docker.com/storage/volumes/) a Docker-dokumentációban című témakörben olvashat bővebben. 

   ```json
       {
         "HostConfig": {
           "Binds": [
             "sqlvolume:/sqlvolume"
           ],
           "PortBindings": {
             "1433/tcp": [
               {
                 "HostPort": "1433"
               }
             ]
           },
           "Mounts": [
             {
               "Type": "volume",
               "Source": "sqlvolume",
               "Target": "/var/opt/mssql"
             }
           ]
         },
         "Env": [
           "MSSQL_AGENT_ENABLED=TRUE",
           "MSSQL_PID=Developer"
         ]
       }
   ```

7. A **IoT Edge egyéni modulok** ablaktáblán frissítse a *set Module Twin 's kívánt tulajdonságait* , hogy tartalmazza az SQL-csomag és a stream Analytics-feladatának helyét. Ez a két mező nem kötelező, és akkor használható, ha az SQL Edge-modult egy adatbázissal és egy folyamatos átviteli feladattal szeretné üzembe helyezni.

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

8. A **IoT Edge egyéni modulok** ablaktáblán állítsa az *Újraindítási szabályzatot* mindig és a *kívánt állapotra* a futtatáshoz.
9. A **IoT Edge egyéni modulok** ablaktáblán kattintson a **Mentés**gombra.
10. A **modulok beállítása** lapon kattintson a **tovább**gombra.
11. A **modulok beállítása** lap **útvonal megadása (nem kötelező)** lapján adja meg az útvonalakat a modulhoz vagy modulhoz IoT Edge hub-kommunikációhoz, lásd: [modulok üzembe helyezése és útvonalak létrehozása IoT Edgeokban](../iot-edge/module-composition.md).
12. Kattintson a **Tovább** gombra.
13. Kattintson a **Submit (Küldés**) gombra.

## <a name="connect-to-azure-sql-edge"></a>Kapcsolódás az Azure SQL Edge-hez

A következő lépések az Azure SQL Edge parancssori eszközt, az **Sqlcmd**-t használják a tárolón belül az Azure SQL Edge-hez való kapcsolódáshoz.

> [!NOTE]
> a Sqlcmd eszköz nem érhető el az SQL Edge-tárolók ARM64-verzióján belül.

1. A `docker exec -it` paranccsal interaktív bash-rendszerhéj indítható el a futó tárolóban. A következő példában a `azuresqledge` `Name` IoT Edge modul paraméterében megadott név szerepel.

   ```bash
   sudo docker exec -it azuresqledge "bash"
   ```

2. A tárolón belül a helyi kapcsolat Sqlcmd. A Sqlcmd alapértelmezés szerint nem található az elérési úton, ezért meg kell adnia a teljes elérési utat.

   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P "<YourNewStrong@Passw0rd>"
   ```

   > [!TIP]
   > A parancssorból kihagyhatja a jelszót, hogy a rendszer megkérje a jelszó megadását.

3. Ha a művelet sikeres, egy **Sqlcmd** parancssorba kell beolvasnia: `1>` .

## <a name="create-and-query-data"></a>Adatlekérdezés létrehozása és lekérdezése

A következő szakasz végigvezeti a **Sqlcmd** és a Transact-SQL használatával egy új adatbázis létrehozásához, az adathozzáadáshoz és egy egyszerű lekérdezés futtatásához.

### <a name="create-a-new-database"></a>Új adatbázis létrehozása

A következő lépések egy nevű új adatbázist hoznak létre `TestDB` .

1. A **Sqlcmd** parancssorba illessze be a következő Transact-SQL parancsot egy teszt adatbázis létrehozásához:

   ```sql
   CREATE DATABASE TestDB
   Go
   ```

2. A következő sorban írjon egy lekérdezést a kiszolgálón található összes adatbázis nevének visszaküldéséhez:

   ```sql
   SELECT Name from sys.Databases
   Go
   ```

### <a name="insert-data"></a>Adat beszúrása

Ezután hozzon létre egy új táblát, `Inventory` és szúrjon be két új sort.

1. A **Sqlcmd** -parancssorban váltson a kontextusra az új `TestDB` adatbázisra:

   ```sql
   USE TestDB
   ```

2. Új nevű tábla létrehozása `Inventory` :

   ```sql
   CREATE TABLE Inventory (id INT, name NVARCHAR(50), quantity INT)
   ```

3. Az új táblába szúrja be az adatbevitelt:

   ```sql
   INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (2, 'orange', 154);
   ```

4. `GO`Az előző parancsok végrehajtásához írja be a következőt:

   ```sql
   GO
   ```

### <a name="select-data"></a>Adatok kiválasztása

Most futtasson egy lekérdezést a tábla adatainak visszaküldéséhez `Inventory` .

1. A **Sqlcmd** parancssorában adjon meg egy lekérdezést, amely a tábla azon sorait adja vissza, amelyekben a mennyiség meghaladja a 152-et `Inventory` :

   ```sql
   SELECT * FROM Inventory WHERE quantity > 152;
   ```

2. Hajtsa végre a parancsot:

   ```sql
   GO
   ```

### <a name="exit-the-sqlcmd-command-prompt"></a>Kilépés a Sqlcmd-parancssorból

1. A **Sqlcmd** -munkamenet befejezéséhez írja be a következőt `QUIT` :

   ```sql
   QUIT
   ```

2. A tárolóban lévő interaktív parancssorból való kilépéshez írja be a következőt: `exit` . Az interaktív bash-rendszerhéjból való kilépés után a tároló továbbra is fut.

## <a name="connect-from-outside-the-container"></a>A tárolón kívülről való kapcsolat

SQL-lekérdezéseket kapcsolódhat és futtathat az Azure SQL Edge-példányon minden olyan külső Linux-, Windows-vagy macOS-eszközön, amely támogatja az SQL-kapcsolatokat. Az SQL Edge-tárolón kívülről való csatlakozásról a [csatlakozás és az Azure SQL Edge lekérdezése](https://docs.microsoft.com/azure/azure-sql-edge/connect)című témakörben olvashat bővebben.

Ebben a rövid útmutatóban egy SQL Edge-modult telepített egy IoT Edge eszközön. 

## <a name="next-steps"></a>További lépések

- [Machine learning és mesterséges intelligencia a ONNX az SQL Edge-ben](onnx-overview.md).
- [Végpontok közötti IoT-megoldás létrehozása az SQL Edge használatával IoT Edge segítségével](tutorial-deploy-azure-resources.md).
- [Adatfolyamok az Azure SQL Edge-ben](stream-data.md)
