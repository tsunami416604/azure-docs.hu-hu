---
title: Python-parancsfájlok futtatása Data Factory-Azure Batch Python használatával
description: Oktatóanyag – megtudhatja, hogyan futtathat Python-parancsfájlokat egy folyamat részeként a Azure Batch használatával Azure Data Factory segítségével.
services: batch
author: mammask
manager: jeconnoc
ms.service: batch
ms.devlang: python
ms.topic: tutorial
ms.date: 12/11/2019
ms.author: komammas
ms.custom: mvc
ms.openlocfilehash: 2995c5da4491f14471d9ed03022a144a02beab5a
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78201835"
---
# <a name="tutorial-run-python-scripts-through-azure-data-factory-using-azure-batch"></a>Oktatóanyag: Python-parancsfájlok futtatása Azure Data Factory használatával Azure Batch

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Hitelesítés Batch- és Storage-fiókokkal
> * Parancsfájl fejlesztése és futtatása Pythonban
> * Számításicsomópont-készlet létrehozása alkalmazás futtatásához
> * A Python számítási feladatainak beosztása
> * Az elemzési folyamat monitorozása
> * Hozzáférés a naplófájlokhoz

Az alábbi példa olyan Python-szkriptet futtat, amely egy blob Storage-tárolóból fogad CSV-bemenetet, végrehajt egy adatkezelési folyamatot, és egy külön blob Storage-tárolóba írja a kimenetet.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Telepített [Python](https://www.python.org/downloads/) -eloszlás helyi teszteléshez.
* Az [Azure](https://pypi.org/project/azure/) `pip`-csomag.
* Egy Azure Batch-fiók és egy társított Azure Storage-fiók. A Batch-fiókok Storage-fiókokhoz való létrehozásával és összekapcsolásával kapcsolatos további információkért tekintse meg [a Batch-fiók létrehozása](quick-create-portal.md#create-a-batch-account) című témakört.
* Egy Azure Data Factory-fiók. A adat-előállító létrehozásával kapcsolatos további információkért tekintse meg Azure Portal az adat-előállító [létrehozása](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory) című témakört.
* [Batch Explorer](https://azure.github.io/BatchExplorer/).
* [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)]

## <a name="create-a-batch-pool-using-batch-explorer"></a>Batch-készlet létrehozása Batch Explorer használatával

Ebben a szakaszban a Batch Explorer használatával hozza létre az Azure-beli adatfeldolgozó-folyamat által használt batch-készletet. 

1. Jelentkezzen be Batch Explorer Azure-beli hitelesítő adataival.
1. Válassza ki a Batch-fiókját
1. Hozzon létre egy készletet a bal oldali sávban található **készletek** kiválasztásával, majd a keresési űrlap fölé a **Hozzáadás** gombra kattintva. 
    1. Válassza ki az azonosítót és a megjelenítendő nevet. Ezt a példát `custom-activity-pool` fogjuk használni.
    1. Állítsa a skálázási típust **rögzített méretre**, és állítsa a dedikált csomópontok száma értéket a 2 értékre.
    1. Az **adatelemzés**területen válassza a **Windows Dsvm** operációs rendszerként lehetőséget.
    1. A virtuális gép méreteként válassza a `Standard_f2s_v2` lehetőséget.
    1. Engedélyezze az indítási feladatot, és adja hozzá a `cmd /c "pip install pandas"`parancsot. A felhasználói identitás az alapértelmezett **készlet felhasználója**maradhat.
    1. Kattintson az **OK** gombra.

## <a name="create-blob-containers"></a>BLOB-tárolók létrehozása

Itt olyan blob-tárolókat hoz létre, amelyek a bemeneti és kimeneti fájljait fogják tárolni az OCR batch-feladathoz.

1. Jelentkezzen be Storage Explorer Azure-beli hitelesítő adataival.
1. A Batch-fiókhoz csatolt Storage-fiók használatával hozzon létre két BLOB-tárolót (egyet a bemeneti fájlokhoz, egyet a kimeneti fájlokhoz) a [blob-tároló létrehozása](../vs-azure-tools-storage-explorer-blobs.md#create-a-blob-container)című témakör lépéseit követve.
    * Ebben a példában meghívjuk a bemeneti tárolót `input`és a kimeneti tárolót `output`.
1. Töltse fel `main.py` és `iris.csv` a bemeneti tárolóba `input` a Storage Explorer használatával a [Blobok blob-tárolóban való kezelésének](../vs-azure-tools-storage-explorer-blobs.md#managing-blobs-in-a-blob-container) lépéseit követve.


## <a name="develop-a-script-in-python"></a>Parancsfájl fejlesztése a Pythonban

A következő Python-szkript betölti a `iris.csv` adatkészletet a `input` tárolóból, végrehajt egy adatkezelési folyamatot, és visszamenti az eredményeket a `output` tárolóba.

``` python
# Load libraries
from azure.storage.blob import BlockBlobService
import pandas as pd

# Define parameters
storageAccountName = "<storage-account-name>"
storageKey         = "<storage-account-key>"
containerName      = "output"

# Establish connection with the blob storage account
blobService = BlockBlobService(account_name=storageAccountName,
                               account_key=storageKey
                               )

# Load iris dataset from the task node
df = pd.read_csv("iris.csv")

# Subset records
df = df[df['Species'] == "setosa"]

# Save the subset of the iris dataframe locally in task node
df.to_csv("iris_setosa.csv", index = False)

# Upload iris dataset
blobService.create_blob_from_text(containerName, "iris_setosa.csv", "iris_setosa.csv")
```

Mentse a parancsfájlt `main.py`ként, és töltse fel az **Azure Storage** -tárolóba. A blob-tárolóba való feltöltés előtt ügyeljen arra, hogy a funkcionalitását helyileg tesztelje és érvényesítse:

``` bash
python main.py
```

## <a name="set-up-an-azure-data-factory-pipeline"></a>Azure Data Factory folyamat beállítása

Ebben a szakaszban egy folyamatot hoz létre és érvényesít a Python-parancsfájl használatával.

1. Az adat-előállító létrehozásához kövesse a [jelen cikk](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory)"adat-előállító létrehozása" című szakaszának lépéseit.
1. A **gyári erőforrások** mezőben válassza a + (plusz) gombot, majd válassza a **folyamat** elemet.
1. Az **általános** lapon állítsa be a folyamat nevét "Python futtatása" értékre.

    ![](./media/run-python-batch-azure-data-factory/create-pipeline.png)

1. A **tevékenységek** mezőben bontsa ki a **Batch szolgáltatás**elemet. Húzza az egyéni tevékenységet a **tevékenységek** eszközkészletből a folyamat tervező felületére.
1. Az **általános** lapon adja meg a **testPipeline** nevet

    ![](./media/run-python-batch-azure-data-factory/create-custom-task.png)
1. A **Azure batch** lapon adja hozzá az előző lépésekben létrehozott **Batch-fiókot** , és **tesztelje a kapcsolódást** annak érdekében, hogy sikeres legyen.

    ![](./media/run-python-batch-azure-data-factory/integrate-pipeline-with-azure-batch.png)

1. A **Beállítások** lapon adja meg a `python main.py`parancsot.
1. Az **erőforráshoz társított szolgáltatáshoz**adja hozzá az előző lépésekben létrehozott Storage-fiókot. Ellenőrizze, hogy a kapcsolódás sikeres volt-e.
1. A **mappa elérési útja**mezőben válassza ki a Python-parancsfájlt és a hozzá tartozó bemeneti adatokat tartalmazó **Azure Blob Storage** tároló nevét. Ezzel letölti a kiválasztott fájlokat a tárolóból a készlet csomópont példányaira a Python-szkript végrehajtása előtt.

    ![](./media/run-python-batch-azure-data-factory/create-custom-task-py-script-command.png)
1. A folyamat beállításainak érvényesítéséhez a vászon fölött kattintson az **Érvényesítés** elemre a folyamat eszköztárán. Győződjön meg róla, hogy a folyamat érvényesítése sikerült. Az érvényesítési kimenet bezárásához kattintson a &gt;&gt; (jobbra mutató nyíl) gombra.
1. A folyamat teszteléséhez kattintson a **hibakeresés** elemre, és győződjön meg róla, hogy az megfelelően működik-e.
1. A folyamat közzétételéhez kattintson a **Közzététel** gombra.
1. Kattintson az **aktiválás** gombra a Python-szkript batch-folyamat részeként való futtatásához.

    ![](./media/run-python-batch-azure-data-factory/create-custom-task-py-success-run.png)

### <a name="monitor-the-log-files"></a>A naplófájlok figyelése

Ha a parancsfájl végrehajtásával figyelmeztetések vagy hibák jönnek létre, a naplózott kimenettel kapcsolatos további információkért tekintse meg `stdout.txt` vagy `stderr.txt`.

1. Batch Explorer bal oldalán válassza a **feladatok** lehetőséget.
1. Válassza ki az adatok előállítója által létrehozott feladatot. Ha elnevezte a készletét `custom-activity-pool`, válassza a `adfv2-custom-activity-pool`lehetőséget.
1. Kattintson arra a feladatra, amelynél hiba történt a kilépési kóddal.
1. `stdout.txt` és `stderr.txt` megtekintése a probléma kivizsgálásához és diagnosztizálásához.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag egy példát mutat be, amely azt tanította, hogyan futtathat Python-parancsfájlokat egy folyamat részeként a Azure Batch használatával Azure Data Factory.

A Azure Data Factoryról további információt a következő témakörben talál:

> [!div class="nextstepaction"]
> [Azure Data Factory](../data-factory/introduction.md)
> [folyamatok és tevékenységek](../data-factory/concepts-pipelines-activities.md)
> [egyéni tevékenységek](../data-factory/transform-data-using-dotnet-custom-activity.md)
