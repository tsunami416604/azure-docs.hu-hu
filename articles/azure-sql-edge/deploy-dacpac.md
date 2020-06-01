---
title: SQL Database DAC-csomagok használata – Azure SQL Edge (előzetes verzió)
description: Tudnivalók a dacpacs Azure SQL Edge-ben való használatáról (előzetes verzió)
keywords: SQL Edge, sqlpackage
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 0ddd1544c6a51ff1e2f98a28e40d9eb2ee0b47c7
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/31/2020
ms.locfileid: "84233278"
---
# <a name="sql-database-dac-packages-in-sql-edge"></a>DAC-csomagok SQL Database az SQL Edge-ben

Az Azure SQL Edge (előzetes verzió) egy optimalizált, a IoT és a peremhálózati környezetekben üzemelő üzemelő adatbázis motorja. Ez az Microsoft SQL Server adatbázismotor legújabb verziójára épül, amely piacvezető teljesítményt, biztonságot és lekérdezés-feldolgozási képességeket biztosít. Az Azure SQL Edge a SQL Server piacvezető, az adatbázis-kezelési képességeivel együtt biztosítja a valós idejű elemzési és összetett események feldolgozására szolgáló, beépített folyamatos átviteli képességet.

Az Azure SQL Edge a SqlPackage. exe natív implementációját is biztosítja, amely lehetővé teszi, hogy [SQL Database DAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) -csomagot helyezzen üzembe az SQL Edge üzembe helyezése során. A SQL Database dacpacs az SQL Edge-modulon keresztül elérhető SqlPackage paraméterrel is üzembe helyezhetők az SQL Edge-ben `module twin's desired properties` :

```json
{
    "properties.desired":
    {
        "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
        "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
    }
}
```

|Mező | Leírás |
|------|-------------|
| SqlPackage | Az SQL Database DAC-csomagot tartalmazó *. zip fájl Azure Blob Storage URI azonosítója.
| ASAJobInfo | Az ASA Edge-feladatokhoz tartozó Azure Blob Storage URI-ja.

## <a name="use-a-sql-database-dac-package-with-sql-edge"></a>SQL Database DAC-csomag használata az SQL Edge használatával

Ha SQL Database DAC-csomagot (*. dacpac) kíván használni az SQL Edge használatával, kövesse az alábbi lépéseket:

1. SQL Database DAC-csomag létrehozása vagy kibontása. Lásd: [DAC kibontása egy adatbázisból](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) egy meglévő SQL Server adatbázis DAC-csomagjának létrehozásával kapcsolatban.

2. Zip a *. dacpac, és töltse fel egy Azure Blob Storage-fiókba. A fájlok Azure Blob Storage-ba való feltöltésével kapcsolatos további információkért lásd: [Blobok feltöltése, letöltése és listázása a Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md).

3. Közös hozzáférési aláírás létrehozása a zip-fájlhoz a Azure Portal használatával. További információ: [hozzáférés delegálása közös hozzáférésű aláírásokkal (SAS)](../storage/common/storage-sas-overview.md).

4. Frissítse az SQL Edge-modul konfigurációját, hogy tartalmazza a DAC-csomag közös hozzáférésű URI-JÁT. Az SQL Edge-modul frissítéséhez hajtsa végre a következő lépéseket:

    1. A Azure Portal lépjen a IoT Hub üzemelő példányra.

    2. A bal oldali ablaktáblán válassza a **IoT Edge**lehetőséget.

    3. A **IoT Edge** lapon keresse meg és válassza ki azt a IoT Edge, amelyben az SQL Edge-modul telepítve van.

    4. A **IoT Edge eszköz** eszköz lapon válassza a **modul beállítása**lehetőséget.

    5. A **modulok beállítása** lapon válassza a **Konfigurálás** az SQL Edge-modulban lehetőséget.

    6. A **IoT Edge egyéni modulok** ablaktáblán válassza a **modul Twin-k kívánt tulajdonságainak beállítása**lehetőséget. Frissítse a kívánt tulajdonságokat, hogy tartalmazza a beállítás URI-JÁT `SQLPackage` , ahogy az az alábbi példában is látható.

        > [!NOTE]
        > A következő JSON SAS URI-ja csupán egy példa. Cserélje le az URI-t az üzemelő példány tényleges URI azonosítójának helyére.

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "<<<SAS URL for the *.zip file containing the dacpac",
                }
            }
        ```

    7. Kattintson a **Mentés** gombra.

    8. A **modulok beállítása** lapon válassza a **tovább**lehetőséget.

    9. A **modulok beállítása** lapon válassza a **tovább** , majd a **Küldés**lehetőséget.

5. A modul frissítése után a DAC-csomagfájl le van töltve, kibontva, és az SQL Edge-példányon telepítve lesz.

Az Azure SQL Edge-tároló minden újraindításakor a *. dacpac csomagfájl letölti és értékeli a módosításokat. Ha a rendszer a dacpac fájl új verzióját észlelte, a módosításokat a rendszer az adatbázishoz az SQL Edge-ben telepíti.

## <a name="next-steps"></a>További lépések

- [Az SQL Edge üzembe helyezése Azure Portalon keresztül](deploy-portal.md).
- [Adatfolyam-adatforrások](stream-data.md)
- [Gépi tanulás és AI a ONNX az SQL Edge-ben (előzetes verzió)](onnx-overview.md)
