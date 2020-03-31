---
title: Az SQL Database Edge előzetes verzió központi telepítése az Azure Portalhasználatával | Microsoft dokumentumok
description: Ismerje meg, hogyan telepítheti az Azure SQL Database Edge-et az Azure Portalhasználatával
keywords: sql adatbázis peremének telepítése
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 9da922de38d820864b3f83de80fe64eb3ac792e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246721"
---
# <a name="deploy-azure-sql-database-edge-preview"></a>Az Azure SQL Database Edge előzetes verziójának üzembe helyezése

Az Azure SQL Database Edge Preview egy relációs adatbázis-motor, amely Az IoT és az Azure IoT Edge-telepítésekhez van optimalizálva. Lehetővé teszi, hogy nagy teljesítményű adattárolási és -feldolgozási réteget hozzon létre az IoT-alkalmazások és -megoldások számára. Ez a rövid útmutató bemutatja, hogyan kezdheti el az Azure SQL Database Edge-modul létrehozását az Azure IoT Edge-en keresztül az Azure Portalon keresztül.

## <a name="before-you-begin"></a>Előkészületek

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot.](https://azure.microsoft.com/free/)
* Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
* Küldje el a kérelmet [itt,](https://azure.microsoft.com/services/sql-database-edge/#contact)hogy az előfizetés engedélyezve van az SQL Database Edge telepítéséhez.
* Hozzon létre egy [Azure IoT Hub.](../iot-hub/iot-hub-create-through-portal.md)
* Regisztráljon egy [IoT Edge-eszközt az Azure Portalon.](../iot-edge/how-to-register-device-portal.md)
* Készítse elő az IoT Edge-eszközt [az IoT Edge-modul azure portalról történő üzembe helyezéséhez.](../iot-edge/how-to-deploy-modules-portal.md)

> [!NOTE]
> Az Azure Linux virtuális gép IoT Edge-eszközként való üzembe helyezéséhez tekintse meg ezt a [rövid útmutatót.](../iot-edge/quickstart-linux.md)

## <a name="deploy-sql-database-edge-module-from-azure-marketplace"></a>SQL Database Edge modul telepítése az Azure Piactérről

Az Azure Marketplace egy online alkalmazások és szolgáltatások piactere, ahol az Azure-ban hitelesített és optimalizált vállalati alkalmazások és megoldások széles körében böngészhet, beleértve az [IoT Edge modulokat is.](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) Az Azure SQL Database Edge a piactéren keresztül egy peremhálózati eszközre telepíthető.

1. Keresse meg az Azure SQL Database Edge modult az Azure Marketplace-en.<br><br>

   ![SQL database Edge a MarketPlace-ben](media/deploy-portal/find-offer-marketplace.png)

2. Válassza ki a követelményeknek leginkább megfelelő szoftvercsomagot, és kattintson a **Létrehozás gombra.** <br><br>

   ![Válassza ki a megfelelő szoftvertervet](media/deploy-portal/pick-correct-plan.png)

3. Az IoT Edge Module céleszközök lapján adja meg a következő részleteket, majd kattintson a **Létrehozás gombra.**

   |**Mező**  |**Leírás**  |
   |---------|---------|
   |Előfizetés  |  Az Az Azure-előfizetés, amely alatt az IoT Hub létrejött |
   |IoT Hub   |  Az IoT Hub neve, ahol az IoT Edge-eszköz regisztrálva van, majd válassza az "Üzembe helyezés egy eszközre" lehetőséget|
   |IoT peremhálózati eszköz neve  |  Annak az IoT Edge-eszköznek a neve, amelyen az SQL Database Edge telepítve lenne |

4. A **Modulok beállítása** lapon keresse meg a központi telepítési modulokszakaszát, és kattintson a **Konfigurálás** az SQL Database Edge modulhoz parancsra. 

5. Az **IoT Edge egyéni modulok** ablaktáblán adja meg a kívánt értékeket a környezeti változók hoz, és/vagy testre szabhatja a létrehozási beállításokat és a kívánt tulajdonságokat a modulhoz. A támogatott környezeti változók teljes listáját az [SQL Server Container Környezeti változók](/sql/linux/sql-server-linux-configure-environment-variables/)című dokumentum tartalmazza.

   |**Paraméter**  |**Leírás**|
   |---------|---------|
   | Név | A modul neve. |
   |SA_PASSWORD  | Adjon meg egy erős jelszót az SQL Database Edge felügyeleti fiókhoz. |
   |MSSQL_LCID   | Beállítja az SQL Server nyelvazonosítóját. Az 1036 például francia. |
   |MSSQL_COLLATION | Beállítja az SQL Server alapértelmezett illesztését. Ez a beállítás felülbírálja a nyelvi azonosító (LCID) alapértelmezett hozzárendelését a rendezéshez. |

   > [!NOTE]
   > Ne módosítsa vagy frissítse a **képURI-t** vagy a **modul ACCEPT_EULA** beállításait.

6. Az **IoT Edge egyéni modulok** ablaktábláján frissítse a tároló létrehozási beállításait a **gazdaporthoz.** Ha egynél több SQL DB Edge-modult kell telepítenie, győződjön meg arról, hogy frissíti a csatlakoztatási lehetőséget, hogy hozzon létre egy új forrás& célpár az állandó kötethez. A csatlakoztatásokkal és a kötetekkel kapcsolatos további információkért tekintse meg a [használati kötetek](https://docs.docker.com/storage/volumes/) a docker dokumentációjában című részét. 

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

7. Az **IoT Edge egyéni modulok** ablaktáblán frissítse a *Set module twin kívánt tulajdonságait,* hogy tartalmazza az SQL-csomag helyét és a stream elemzési feladat adatait. Ez a két mező nem kötelező, és kell használni, ha az SQL Database Edge modul t database és egy streamelési feladat.

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

8. Az **IoT Edge egyéni modulok** ablaktáblán állítsa *az újraindítási szabályzatot* mindig és *a kívánt állapotot* futásra.
9. Az **IoT Edge egyéni modulok** ablaktábláján kattintson a **Mentés gombra.**
10. A **Modulok beállítása** lapon kattintson a **Tovább gombra.**
11. A **Modulok beállítása lap Útvonal megadása (nem kötelező)** lapon adja meg a modulhoz vagy a modulhoz az IoT Edge Hub kommunikációjához vezető útvonalakat, lásd: Modulok üzembe helyezése és útvonalak létrehozása az [IoT Edge-ben.](../iot-edge/module-composition.md) **Set Modules**
12. Kattintson a **Tovább** gombra.
13. Kattintson a **Küldés gombra.**

Ebben a rövid útmutatóban egy SQL Database Edge-modult telepített egy IoT Edge-eszközön.

## <a name="next-steps"></a>Következő lépések

- [Gépi tanulás és mesterséges intelligencia ONNX-szel az SQL Database Edge-ben.](onnx-overview.md)
- Az IoT-megoldás és az SQLDatabase Edge teljes körű IoT-megoldás létrehozása az IoT Edge használatával.
