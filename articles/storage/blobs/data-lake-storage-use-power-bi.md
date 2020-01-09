---
title: Azure Data Lake Storage Gen2 adatai elemzése a Power BI használatával | Microsoft Docs
description: Power BI használata a Azure Data Lake Storage Gen2 tárolt adatelemzéshez
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: normesta
ms.reviewer: bensack
ms.openlocfilehash: 156999c6b6d8451516ac1c0f095e1a864420d0b2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75354820"
---
# <a name="analyze-data-in-azure-data-lake-storage-gen2-by-using-power-bi"></a>Azure Data Lake Storage Gen2i adatelemzés Power BI használatával

Ebből a cikkből megtudhatja, hogyan használhatók a Power BI Desktop a hierarchikus névtérrel (Azure Data Lake Storage Gen2) rendelkező Storage-fiókokban tárolt adatelemzéshez és megjelenítéshez.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

> [!div class="checklist"]
> * Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
> * Hierarchikus névtérrel rendelkező Storage-fiók. Az [alábbi](data-lake-storage-quickstart-create-account.md) útmutatást követve hozzon létre egyet.
> Ez a cikk azt feltételezi, hogy létrehozott egy `myadlsg2`nevű fiókot.
> * A következő szerepkörök egyike adható meg a Storage-fiókhoz: **blob-Adatolvasó**, **blob-adat közreműködői**vagy **blob-adat tulajdonosa**.
> * Egy `Drivers.txt` nevű minta adatfájl, amely a Storage-fiókban található.
> Ezt a mintát [Azure Data Lake git-tárházból](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt)töltheti le, majd feltöltheti a fájlt a Storage-fiókjába.
> * **Power bi Desktop**. Ezt letöltheti a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=45331). 

## <a name="create-a-report-in-power-bi-desktop"></a>Jelentés létrehozása a Power BI Desktopban

1. Power BI Desktop elindítása a számítógépen.
2. A menüszalag **kezdőlapján** kattintson az **adatok lekérése**elemre, majd kattintson a **továbbiak**elemre.
3. Az **adatlekérdezés** párbeszédpanelen kattintson az **Azure**elemre, majd a **Azure Data Lake Store Gen2 (bétaverzió)** elemre, majd a **kapcsolat**lehetőségre.

    ![Adatlekérdezés lap](media/data-lake-storage-use-power-bi/get-data-page.png)

4. A **Azure Data Lake Storage Gen2** párbeszédpanelen megadhatja az Azure Data Lake Storage Gen2-fiók, a fájlrendszer vagy az almappa URL-címét a tároló végpontjának formátuma alapján. A Data Lake Storage Gen2 URL-címei rendelkeznek a következő mintával `https://<accountname>.dfs.core.windows.net/<filesystemname>/<subfolder>`, majd kattintson **az OK**gombra.

    ![URL-cím](media/data-lake-storage-use-power-bi/adls-url.png)

5. A következő párbeszédpanelen kattintson a Bejelentkezés gombra a Storage **-** fiókba való bejelentkezéshez. A rendszer átirányítja a szervezet bejelentkezési oldalára. Az utasításokat követve jelentkezzen be a fiókba.

    ![Bejelentkezés lap](media/data-lake-storage-use-power-bi/sign-in.png)

6. Miután sikeresen bejelentkezett, kattintson a **kapcsolat**gombra.

    ![Bejelentkezve oldal](media/data-lake-storage-use-power-bi/signed-in.png)

7. A következő párbeszédpanelen a 4. lépésben megadott URL-cím alatti összes fájl látható, beleértve a Storage-fiókba feltöltött fájlt is. Ellenőrizze az adatokat, majd kattintson a **Betöltés**elemre.

    ![Fájlrendszerek](media/data-lake-storage-use-power-bi/file-systems.png)

8. Miután az adat sikeresen betöltődött Power BIba, a **mezők** lapon láthatja a következő mezőket.

    ![Mezők lap](media/data-lake-storage-use-power-bi/fields.png)

    Az adat megjelenítéséhez és elemzéséhez azonban inkább az alábbi mezőkben elérhetővé tettük az adatmennyiséget.

    ![Mezők](media/data-lake-storage-use-power-bi/preferred-fields.png)

    A következő lépésekben frissíteni fogjuk a lekérdezést, hogy az importált adattípusokat a kívánt formátumban alakítsa át.

9. A menüszalag **Kezdőlap** lapján kattintson a **lekérdezések szerkesztése**elemre.

    ![Lekérdezések](media/data-lake-storage-use-power-bi/queries.png)

10. A **Lekérdezés-szerkesztő** **tartalom** oszlopában kattintson a **bináris**elemre. A rendszer automatikusan CSV-fájlként észleli a fájlt, és az alább látható kimenet jelenik meg. Az adatai mostantól a vizualizációk létrehozásához használható formátumban érhetők el.

    ![Kimenet](media/data-lake-storage-use-power-bi/binary.png)

11. A menüszalag **kezdőlapján** kattintson a **Bezárás** gombra, majd kattintson a **Bezárás** **gombra, és**válassza az **alkalmaz**lehetőséget.

    ![Bezárás és alkalmazás](media/data-lake-storage-use-power-bi/close-apply.png)

12. A lekérdezés frissítése után a **mezők** lapon megjelennek a vizualizációhoz elérhető új mezők.

    ![Új mezők](media/data-lake-storage-use-power-bi/new-fields.png)

13. Hozzunk létre egy tortadiagramot, amely az adott ország összes városának illesztőprogramjait jelöli. Ehhez végezze el a következő beállításokat.

    A **vizualizációk** lapon kattintson a tortadiagram szimbólumára.

    ![Vizualizációk](media/data-lake-storage-use-power-bi/visualizations.png)

    A használni kívánt oszlopok a 4. oszlop (a város neve) és a 7-es oszlop (az ország neve). Húzza ezeket az oszlopokat a **mezők** lapról a **vizualizációk** lapra az alább látható módon.

    ![Mezők húzása](media/data-lake-storage-use-power-bi/visualizations-drag-fields.png)

    A tortadiagramnak most az alább láthatóhoz hasonlónak kell lennie.

    ![Tortadiagram](media/data-lake-storage-use-power-bi/pie-chart.png)

14. Ha kijelöl egy adott országot az oldal szintjének szűrők közül, láthatja az illesztőprogramok számát a kiválasztott ország minden városában. Például a **vizualizációk** lap **oldal szintű szűrők**területén válassza a **Brazília**elemet.

    ![Oldal szűrők](media/data-lake-storage-use-power-bi/page-filters.png)

15. A tortadiagramot a rendszer automatikusan frissíti a brazil városokban lévő illesztőprogramok megjelenítéséhez.

    ![Brazília](media/data-lake-storage-use-power-bi/pie-chart-updated.png)

16. A **fájl** menüben kattintson a **Mentés** gombra a vizualizáció Power bi Desktop fájlként való mentéséhez.

## <a name="publish-report-to-power-bi-service"></a>Jelentés közzététele Power BI szolgáltatás

Miután létrehozta a vizualizációkat a Power BI Desktopban, megoszthatja másokkal, ha közzéteszi azt a Power BI szolgáltatás. Ennek módjával kapcsolatos útmutatásért lásd: [közzététel Power bi Desktopról](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).
