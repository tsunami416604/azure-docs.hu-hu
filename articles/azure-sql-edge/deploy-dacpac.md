---
title: SQL Database DACPAC és BACPAC-csomagok használata – Azure SQL Edge (előzetes verzió)
description: Tudnivalók a dacpacs és a bacpacs használatáról az Azure SQL Edge-ben (előzetes verzió)
keywords: SQL Edge, sqlpackage
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 52c8e9586d8ee53cdaac28cb1c48d2927d82c2ed
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2020
ms.locfileid: "89462759"
---
# <a name="sql-database-dacpac-and-bacpac-packages-in-sql-edge"></a>DACPAC-és BACPAC-csomagok beSQL Databasea az SQL Edge-ben

Az Azure SQL Edge (előzetes verzió) egy optimalizált, a IoT és a peremhálózati környezetekben üzemelő üzemelő adatbázis motorja. Ez az Microsoft SQL Server adatbázismotor legújabb verziójára épül, amely piacvezető teljesítményt, biztonságot és lekérdezés-feldolgozási képességeket biztosít. Az Azure SQL Edge a SQL Server piacvezető, az adatbázis-kezelési képességeivel együtt biztosítja a valós idejű elemzési és összetett események feldolgozására szolgáló, beépített folyamatos átviteli képességet.

Az Azure SQL Edge olyan SqlPackage.exe natív implementációját is biztosítja, amely lehetővé teszi [SQL Database DACPAC és BACPAC-](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) csomagok üzembe helyezését az SQL Edge üzembe helyezése során. A SQL Database dacpacs az SQL Edge-modulon keresztül elérhető SqlPackage paraméterrel is üzembe helyezhetők az SQL Edge-ben `module twin's desired properties` :

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
| SqlPackage | Az SQL Database DAC-vagy BACPAC-csomagot tartalmazó *. zip* fájl Azure Blob Storage URI azonosítója. A zip-fájl több DAC-csomagot vagy bacpac-fájlt is tartalmazhat.
| ASAJobInfo | Az ASA Edge-feladatokhoz tartozó Azure Blob Storage URI-ja.

## <a name="use-a-sql-database-dac-package-with-sql-edge"></a>SQL Database DAC-csomag használata az SQL Edge használatával

Ha SQL Database DAC `(*.dacpac)` -csomagot vagy SQL Edge-BACPAC-fájlt szeretne használni `(*.bacpac)` , kövesse az alábbi lépéseket:

1. Hozzon létre/bontson ki egy DAC-csomagot, vagy exportáljon egy Bacpac-fájlt az alább említett mechanizmus használatával. 
    - SQL Database DAC-csomag létrehozása vagy kibontása. Lásd: [DAC kibontása egy adatbázisból](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) egy meglévő SQL Server adatbázis DAC-csomagjának létrehozásával kapcsolatban.
    - Telepített DAC-csomag vagy-adatbázis exportálása. Az [adatrétegbeli alkalmazások exportálásával](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application/) kapcsolatos információkért lásd: bacpac-fájl létrehozása meglévő SQL Server-adatbázishoz.

2. Zip a `*.dacpac` vagy a `*.bacpac` fájlt, és töltse fel egy Azure Blob Storage-fiókba. A fájlok Azure Blob Storage-ba való feltöltésével kapcsolatos további információkért lásd: [Blobok feltöltése, letöltése és listázása a Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md).

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
                    "SqlPackage": "<<<SAS URL for the *.zip file containing the dacpac and/or the bacpac files",
                }
            }
        ```

    7. Kattintson a **Mentés** gombra.

    8. A **modulok beállítása** lapon válassza a **tovább**lehetőséget.

    9. A **modulok beállítása** lapon válassza a **tovább** , majd a **Küldés**lehetőséget.

5. A modul frissítését követően a csomagfájl le van töltve, kibontva, és az SQL Edge-példányon lesz telepítve.

Az Azure SQL Edge-tároló minden újraindításakor a `*.dacpac` rendszer letölti és kiértékeli a módosításokat. Ha a rendszer a dacpac fájl új verzióját észlelte, a módosításokat a rendszer az adatbázishoz az SQL Edge-ben telepíti. Bacpac-fájlok 

## <a name="next-steps"></a>Következő lépések

- [Az SQL Edge üzembe helyezése Azure Portalon keresztül](deploy-portal.md).
- [Adatfolyam-adatforrások](stream-data.md)
- [Gépi tanulás és AI a ONNX az SQL Edge-ben (előzetes verzió)](onnx-overview.md)
