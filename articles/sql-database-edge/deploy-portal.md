---
title: SQL Database Edge előzetes verziójának üzembe helyezése a Azure Portal használatával | Microsoft Docs
description: Megtudhatja, hogyan helyezheti üzembe a Azure SQL Database Edge-t a Azure Portal használatával
keywords: az SQL Database Edge üzembe helyezése
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 9da756b702c994d69aae42ecef0e2da4d44eed39
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514094"
---
# <a name="deploy-azure-sql-database-edge-preview"></a>Azure SQL Database Edge előzetes verziójának üzembe helyezése

Az Azure SQL Database Edge Preview egy IoT-re optimalizált, és a Azure IoT Edge üzemelő példányok. Lehetőséget biztosít nagy teljesítményű adattárolási és feldolgozási réteg létrehozására a IoT alkalmazások és megoldások számára. Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre egy Azure SQL Database Edge-modult az Azure Portal használatával Azure IoT Edge segítségével.

## <a name="before-you-begin"></a>Előzetes teendők

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* Bejelentkezés az [Azure Portalra](https://portal.azure.com/).
* [Itt](https://azure.microsoft.com/services/sql-database-edge/#contact)küldheti el a kérelmet, hogy az előfizetés engedélyezve legyen SQL Database Edge telepítéséhez.
* Hozzon létre egy [Azure-IoT hub](../iot-hub/iot-hub-create-through-portal.md).
* [IoT Edge eszköz regisztrálása a Azure Portal](../iot-edge/how-to-register-device-portal.md).
* Készítse elő a IoT Edge eszközt [IoT Edge modul üzembe helyezéséhez a Azure Portal](../iot-edge/how-to-deploy-modules-portal.md).

> [!NOTE]
> Ha IoT Edge eszközként szeretne üzembe helyezni egy Azure-beli linuxos virtuális gépet, tekintse meg ezt a rövid [útmutatót](../iot-edge/quickstart-linux.md).

## <a name="deploy-sql-database-edge-module-from-azure-marketplace"></a>SQL Database Edge-modul üzembe helyezése az Azure Marketplace-en

Az Azure Marketplace egy online alkalmazások és szolgáltatások piaca, ahol az Azure-on futó Nagyvállalati alkalmazások és megoldások széles körét böngészheti, beleértve a [IoT Edge modulokat](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)is. Azure SQL Database Edge üzembe helyezhető egy peremhálózati eszközön a piactéren.

1. Keresse meg az Azure SQL Database Edge-modult az Azure piactéren.<br><br>

   ![SQL Database Edge a piactéren](media/deploy-portal/find-offer-marketplace.png)

2. Válassza ki az igényeinek leginkább megfelelő szoftvercsomagot, és kattintson a **Létrehozás**gombra. <br><br>

   ![Válassza ki a megfelelő szoftvercsomagot](media/deploy-portal/pick-correct-plan.png)

3. A IoT Edge modul megcélzott eszközei lapon adja meg a következő adatokat, majd kattintson a **Létrehozás** gombra.

   |**Mező**  |**Leírás**  |
   |---------|---------|
   |Előfizetés  |  Az Azure-előfizetés, amely alatt a IoT Hub létre lett hozva |
   |IoT Hub   |  Azon IoT Hub neve, ahol a IoT Edge eszköz regisztrálva van, majd válassza az "üzembe helyezés eszközre" lehetőséget.|
   |IoT Edge eszköz neve  |  Annak a IoT Edge eszköznek a neve, amelyben a SQL Database Edge üzembe lett helyezve |

4. A **modulok beállítása** lapon navigáljon a telepítési modulok szakaszhoz, és kattintson a **Konfigurálás** az SQL Database Edge-modulban elemre. 

5. A **IoT Edge egyéni modulok** ablaktáblán válassza ki a környezeti változók kívánt értékeit, és/vagy szabja testre a modul létrehozási beállításait és kívánt tulajdonságait. A támogatott környezeti változók teljes listájáért tekintse meg [SQL Server tároló környezeti változóit](/sql/linux/sql-server-linux-configure-environment-variables/).

   |**Paraméter**  |**Leírás**|
   |---------|---------|
   | Name (Név) | A modul neve. |
   |SA_PASSWORD  | Erős jelszót kell megadnia az SQL Database Edge rendszergazdai fiókhoz. |
   |MSSQL_LCID   | Beállítja a SQL Server használandó nyelvi azonosítót. Például 1036 a francia. |
   |MSSQL_COLLATION | Beállítja SQL Server alapértelmezett rendezését. Ez a beállítás felülbírálja a nyelvi azonosító (LCID) alapértelmezett leképezését a rendezéshez. |

   > [!NOTE]
   > Ne módosítsa vagy frissítse a **rendszerkép URI-ját** vagy a **ACCEPT_EULA** beállításait a modulon.

6. A **IoT Edge egyéni modulok** ablaktáblán frissítse a tároló létrehozási beállításait a **gazdagép portjához** és a csatlakoztatási pontok **céljához** . A csatlakoztatási pont célja, hogy az SQL-adatbázis fájljai a gazdagépen IoT Edge eszközön legyenek tárolva.

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

7. A **IoT Edge egyéni modulok** ablaktáblán frissítse a *set Module Twin 's kívánt tulajdonságait* , hogy tartalmazza az SQL-csomag és a stream Analytics-feladatának helyét. Ez a két mező nem kötelező, és akkor használható, ha az SQL Database Edge-modult egy adatbázissal és egy folyamatos átviteli feladattal szeretné üzembe helyezni.

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

Ebben a rövid útmutatóban üzembe helyezett egy SQL Database Edge-modult egy IoT Edge eszközön.

## <a name="next-steps"></a>További lépések

- [Machine learning és mesterséges intelligencia a ONNX SQL Database Edge-ben](onnx-overview.md).
- Végpontok közötti IoT-megoldás létrehozása SQL Database Edge használatával IoT Edge segítségével.