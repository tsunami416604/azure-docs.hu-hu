---
title: Python-parancsfájlok futtatása a Data Factory szolgáltatáson keresztül – Azure Batch Python
description: Oktatóanyag – Ismerje meg, hogyan futtathat Python-parancsfájlokat egy folyamat részeként az Azure Data Factory azure-beli batch használatával.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78201835"
---
# <a name="tutorial-run-python-scripts-through-azure-data-factory-using-azure-batch"></a>Oktatóanyag: Python-parancsfájlok futtatása az Azure Data Factory használatával az Azure Batch használatával

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Hitelesítés Batch- és Storage-fiókokkal
> * Parancsfájl fejlesztése és futtatása pythonban
> * Számításicsomópont-készlet létrehozása alkalmazás futtatásához
> * A Python-munkaterhelések ütemezése
> * Az elemzési folyamat figyelése
> * A naplófájlok elérése

Az alábbi példa futtatja a Python-parancsfájlt, amely csv-bemenetet fogad egy blob tárolóból, adatkezelési folyamatot hajt végre, és a kimenetet egy külön blobtárolóba írja.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

* Telepített [Python](https://www.python.org/downloads/) Python-disztribúció helyi teszteléshez.
* Az [Azure-csomag.](https://pypi.org/project/azure/) `pip`
* Egy Azure Batch-fiók és egy társított Azure Storage-fiók. A [Batch-fiók létrehozása](quick-create-portal.md#create-a-batch-account) című témakörben további információt talál a Batch-fiókok létrehozásáról és a tárfiókokhoz való csatolásáról.
* Egy Azure Data Factory-fiók. Az [Adatgyár létrehozása](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory) című témakörben további információt talál arról, hogyan hozhat létre adatgyárat az Azure Portalon keresztül.
* [Batch Explorer .](https://azure.github.io/BatchExplorer/)
* [Az Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)alkalmazást.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure [https://portal.azure.com](https://portal.azure.com)Portalon a .

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)]

## <a name="create-a-batch-pool-using-batch-explorer"></a>Kötegkészlet létrehozása a Batch Explorer használatával

Ebben a szakaszban a Batch Explorer használatával hozza létre az Azure Data-folyamat által használni kívánt batch készletet. 

1. Jelentkezzen be a Batch-kezelőbe az Azure-hitelesítő adatok használatával.
1. Válassza ki a Batch-fiókot
1. Hozzon létre egy készletet a bal oldali sávon lévő **Készletek,** majd a keresési űrlap feletti **Hozzáadás** gomb kiválasztásával. 
    1. Válasszon azonosítót és megjelenítendő nevet. Ezt a `custom-activity-pool` példát fogjuk használni.
    1. Állítsa a méretezési típust **Rögzített méretre,** és állítsa a dedikált csomópontszámot 2-re.
    1. Az **Adatelemzés**csoportban válassza a **DSvm Windows** operációs rendszert.
    1. Válassza `Standard_f2s_v2` ki a virtuális gép méretét.
    1. Engedélyezze a kezdő feladatot, és adja hozzá a parancsot `cmd /c "pip install pandas"`. A felhasználói identitás maradhat az alapértelmezett **készletfelhasználó.**
    1. Válassza **az OK gombot.**

## <a name="create-blob-containers"></a>Blob-tárolók létrehozása

Itt hozhat létre blobtárolókat, amelyek az OCR kötegelt feladat bemeneti és kimeneti fájljait tárolják.

1. Jelentkezzen be a Storage Explorerbe az Azure-hitelesítő adatokhasználatával.
1. A Batch-fiókhoz kapcsolódó tárfiók használatával hozzon létre két blobtárolót (egyet a bemeneti fájlokhoz, egyet a kimeneti fájlokhoz) a [Blob-tároló létrehozása](../vs-azure-tools-storage-explorer-blobs.md#create-a-blob-container)című szakasz lépéseit követve.
    * Ebben a példában a bemeneti `input`tárolót és `output`a kimeneti tárolót hívjuk meg.
1. `main.py` Feltöltés `iris.csv` és a `input` bemeneti tárolóba a Storage Explorer használatával a [blobok kezelése egy blobtárolóban](../vs-azure-tools-storage-explorer-blobs.md#managing-blobs-in-a-blob-container) című lépések végrehajtásával


## <a name="develop-a-script-in-python"></a>Parancsfájl fejlesztése pythonban

A következő Python-parancsfájl betölti az adatkészletet a `iris.csv` `input` tárolóból, adatkezelési folyamatot `output` hajt végre, és menti az eredményeket a tárolóba.

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

Mentse a `main.py` parancsfájlt, és töltse fel az Azure Storage-tárolóba. **Azure Storage** Győződjön meg arról, hogy tesztelje és érvényesítse a funkcionalitáshelyileg feltöltés előtt a blob tárolóba:

``` bash
python main.py
```

## <a name="set-up-an-azure-data-factory-pipeline"></a>Azure Data Factory-folyamat beállítása

Ebben a szakaszban hozzon létre és érvényesítsen egy folyamatot a Python-parancsfájl használatával.

1. Az adat-előállító létrehozásához kövesse az adatgyár at the "Create a data factory" [című](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory)részében.
1. A **Gyári erőforrások** párbeszédpanelen válassza a + (plusz) gombot, majd a **Folyamat lehetőséget.**
1. Az **Általános** lapon állítsa be a folyamat nevét "Python futtatása" formában.

    ![](./media/run-python-batch-azure-data-factory/create-pipeline.png)

1. A **Tevékenységek** mezőben bontsa ki a **Kötegelt szolgáltatás csomópontot.** Húzza az egyéni tevékenységet a **Tevékenységek** eszközkészletből a folyamattervező felületére.
1. Az **Általános** lapon adja meg a **testPipeline** értéket a Név mezőbe.

    ![](./media/run-python-batch-azure-data-factory/create-custom-task.png)
1. Az **Azure Batch** lapon adja hozzá az előző lépésekben létrehozott **Batch-fiókot,** és tesztelje a **kapcsolatot,** hogy megbizonyosodjon arról, hogy sikeres

    ![](./media/run-python-batch-azure-data-factory/integrate-pipeline-with-azure-batch.png)

1. A **Beállítások** lapon adja `python main.py`meg a parancsot .
1. Az **erőforrás-csatolt szolgáltatáshoz**adja hozzá az előző lépésekben létrehozott tárfiókot. Tesztelje a kapcsolatot, hogy megbizonyosodjon a sikerességről.
1. A **mappaelérési út**ban válassza ki a Python-parancsfájlt és a kapcsolódó bemeneteket tartalmazó **Azure Blob Storage-tároló** nevét. Ez letölti a kijelölt fájlokat a tárolóból a készlet csomópontpéldányaiak a Python-parancsfájl végrehajtása előtt.

    ![](./media/run-python-batch-azure-data-factory/create-custom-task-py-script-command.png)
1. A folyamat beállításainak érvényesítéséhez a vászon fölött kattintson az **Érvényesítés** elemre a folyamat eszköztárán. Győződjön meg róla, hogy a folyamat érvényesítése sikerült. Az érvényesítés kimenetének bezárásához kattintson a &gt;&gt; (jobbra mutató nyíl) gombra.
1. Kattintson **a Hibakeresés gombra** a folyamat teszteléséhez és annak biztosításához, hogy az pontosan működjön.
1. A folyamat közzétételéhez kattintson a **Közzététel** gombra.
1. Kattintson **az Eseményindító** gombra a Python-parancsfájl kötegelt folyamat részeként való futtatásához.

    ![](./media/run-python-batch-azure-data-factory/create-custom-task-py-success-run.png)

### <a name="monitor-the-log-files"></a>A naplófájlok figyelése

Abban az esetben, ha a parancsfájl végrehajtása figyelmeztetéseket `stdout.txt` vagy `stderr.txt` hibákat hoz létre, kiveheti, vagy további információt a naplózott kimenetről.

1. Válassza a **Feladatok** lehetőséget a Batch Explorer bal oldalán.
1. Válassza ki az adat-előállító által létrehozott feladatot. Feltéve, hogy `custom-activity-pool`elnevezte a készletet, válassza a lehetőséget. `adfv2-custom-activity-pool`
1. Kattintson arra a feladatra, amelynek hibakilépési kódja volt.
1. Tekintse `stdout.txt` `stderr.txt` meg, vizsgálja meg és diagnosztizálja a problémát.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy példát tárt fel, amely megtanította, hogyan futtathatja a Python-parancsfájlokat az Azure Data Factory azure-beli data-folyamaton keresztül az Azure Batch használatával.

Ha többet szeretne megtudni az Azure Data Factoryról, olvassa el a következő témakört:

> [!div class="nextstepaction"]
> [Azure Data Factory](../data-factory/introduction.md)
> [folyamatok és tevékenységek](../data-factory/concepts-pipelines-activities.md)
> [Egyéni tevékenységek](../data-factory/transform-data-using-dotnet-custom-activity.md)
