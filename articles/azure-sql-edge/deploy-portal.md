---
title: Az Azure SQL Edge (előzetes verzió) üzembe helyezése a Azure Portal használatával
description: Ismerje meg, hogyan helyezheti üzembe az Azure SQL Edge (előzetes verzió) szolgáltatását a Azure Portal használatával
keywords: SQL Edge üzembe helyezése
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 7dfc7b680c6b7d6b2a3641b25c0f82665143bde1
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597421"
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
   | Name | A modul neve. |
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

Ebben a rövid útmutatóban egy SQL Edge-modult telepített egy IoT Edge eszközön.

## <a name="next-steps"></a>Következő lépések

- [Machine learning és mesterséges intelligencia a ONNX az SQL Edge-ben](onnx-overview.md).
- Végpontok közötti IoT-megoldás létrehozása az SQL Edge használatával IoT Edge segítségével.