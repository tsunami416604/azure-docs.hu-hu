---
title: IoT Edge modulok beállítása az Azure SQL Edge-ben
description: A három részből álló Azure SQL Edge-oktatóanyag első részében a vasérc-szennyeződések előrejelzéséhez IoT Edge modulokat és kapcsolatokat kell beállítania.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: a4087ef56712e098443009bd0457029394ea7b51
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235024"
---
# <a name="set-up-iot-edge-modules-and-connections"></a>IoT Edge modulok és kapcsolatok beállítása

Az alábbi, a három részből álló oktatóanyagban az Iron Ore-szennyeződések előrejelzéséhez az Azure SQL Edge-ben a következő IoT Edge modulokat fogja beállítani:

- Azure SQL Edge
- Adatgenerátor IoT Edge modul

## <a name="create-azure-stream-analytics-module"></a>Azure Stream Analytics modul létrehozása

Hozzon létre egy Azure Stream Analytics modult, amely ebben az oktatóanyagban lesz felhasználva. Ha többet szeretne megtudni a streaming-feladatok SQL Edge használatával történő használatáról, tekintse meg a következőt: [streaming Jobs használata az SQL Edge használatával](stream-analytics.md).

Miután létrehozta a Azure Stream Analytics feladatot az Edge-vel beállított üzemeltetési környezettel, állítsa be az oktatóanyag bemeneteit és kimeneteit.

1. A **bemenet**létrehozásához kattintson az **+ stream-bemenet hozzáadása**elemre. Töltse ki a részletek szakaszt a következő információk alapján:

   Mező|Érték
   -----|-----
   Esemény szerializálási formátuma|JSON
   Encoding|UTF-8
   Esemény tömörítési típusa|None

2. A **kimenet**létrehozásához kattintson a **+ Hozzáadás** elemre, és válassza a SQL Database lehetőséget. Töltse ki a részletek szakaszt az alábbi információk alapján.

   > [!NOTE]
   > Az ebben a szakaszban megadott jelszót az SQL Edge-modul üzembe **helyezése az Azure SQL Edge-modul központi**telepítése című szakaszában kell megadni az SQL SA-jelszóhoz.

   Mező|Érték
   -----|-----
   Adatbázis|IronOreSilicaPrediction
   Kiszolgálónév|TCP:., 1433
   Felhasználónév|sa
   Jelszó|Erős jelszó megadása
   Tábla|IronOreMeasurements1

3. Navigáljon a **lekérdezés** szakaszhoz, és állítsa be a lekérdezést a következőképpen:

   `SELECT * INTO <name_of_your_output_stream> FROM <name_of_your_input_stream>`
   
4. A **Konfigurálás**területen válassza a **Közzététel**lehetőséget, majd kattintson a **Közzététel** gombra. Mentse az SAS URI-t az SQL Database Edge-modullal való használatra.

## <a name="specify-container-registry-credentials"></a>Tároló beállításjegyzékbeli hitelesítő adatainak megadása

Meg kell adni a tároló-beállításjegyzékek modul-rendszerképeket üzemeltető hitelesítő adatait. Ezek a tároló-beállításjegyzékben találhatók, amely az erőforráscsoporthoz lett létrehozva. Navigáljon a **hozzáférési kulcsok** szakaszhoz. Jegyezze fel a következő mezőket:

- Beállításjegyzék neve
- Bejelentkezési kiszolgáló
- Felhasználónév
- Jelszó

Most a IoT Edge modulban meg kell adnia a tároló hitelesítő adatait.

1. Navigáljon az erőforráscsoporthoz létrehozott IoT hubhoz.

2. Az **automatikus eszközkezelés**területen az **IoT Edge** szakaszban kattintson az **eszköz azonosítója**elemre. Ebben az oktatóanyagban az azonosító a következő: `IronOrePredictionDevice` .

3. Válassza a **modulok beállítása** szakaszt.

4. A **Container Registry hitelesítő adatok**területen adja meg a következő értékeket:

   _Mező_|_Érték_
   -------|-------
   Name (Név)|Beállításjegyzék neve
   Cím|Bejelentkezési kiszolgáló
   Felhasználónév|Felhasználónév
   Jelszó|Jelszó
  
## <a name="deploy-the-data-generator-module"></a>Az adatgenerátor modul üzembe helyezése

1. A **IoT Edge-modulok** szakaszban kattintson a **+ Hozzáadás** elemre, és válassza ki **IoT Edge modult**.

2. Adja meg IoT Edge modul nevét és a rendszerkép URI-JÁT.
   A rendszerkép URI-ja megtalálható az erőforráscsoport tároló-beállításjegyzékében. Válassza ki a **tárolók** szakaszt a **szolgáltatások**területen. Ebben az oktatóanyagban válassza ki a nevű tárat `silicaprediction` . Válassza ki a megfelelő címkét. A képuri formátuma a következőket eredményezi:

   *a containerregistry* / bejelentkezési kiszolgálója *tárház neve*:*címke neve*

   Például:

   ```
   ASEdemocontregistry.azurecr.io/silicaprediction:amd64
   ```

3. Kattintson a **Hozzáadás**gombra.

## <a name="deploy-the-azure-sql-edge-module"></a>Az Azure SQL Edge-modul üzembe helyezése

1. Telepítse az Azure SQL Edge-modult az [Azure SQL Edge (előzetes verzió) üzembe helyezése](https://docs.microsoft.com/azure/azure-sql-edge/deploy-portal)című témakörben ismertetett lépéseket követve.

2. A **modulok beállítása** lap **útvonal megadása** lapján adja meg a modul útvonalait IoT Edge hub-kommunikációt az alábbiak szerint. 

   ```
   FROM /messages/modules/<your_data_generator_module>/outputs/<your_output_stream_name> INTO
   BrokeredEndpoint("/modules/<your_azure_sql_edge_module>/inputs/<your_input_stream_name>")
   ```

   Például:

   ```
   FROM /messages/modules/ASEDataGenerator/outputs/IronOreMeasures INTO BrokeredEndpoint("/modules/AzureSQLEdge/inputs/Input1")
   ```

3. A **modul Twin** beállításaiban győződjön meg arról, hogy a SQLPackage és a ASAJonInfo frissülnek az oktatóanyag során korábban mentett megfelelő sas URL-címekkel.

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

## <a name="next-steps"></a>Következő lépések

- [ML modell üzembe helyezése az Azure SQL Edge-ben a ONNX használatával](tutorial-run-ml-model-on-sql-edge.md)
