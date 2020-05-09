---
title: Adatok átalakítása
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan alakíthat át adatokat Azure Machine Learning Designerben saját adatkészletek létrehozásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.date: 05/04/2020
ms.openlocfilehash: 5296ac54cab403ef78b3e8bd32fe5ebe6ea43119
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82842876"
---
# <a name="transform-data-in-azure-machine-learning-designer-preview"></a>Az adatátalakítás Azure Machine Learning Designerben (előzetes verzió)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan alakíthat át és menthet adatkészleteket Azure Machine Learning Designerben, hogy előkészítse saját adatait a gépi tanuláshoz.

A minta [felnőtt népszámlálás jövedelmének bináris besorolási](sample-designer-datasets.md) adatkészletét a következő két adatkészlet előkészítésére fogja használni: egy adatkészletet, amely kizárólag a Egyesült Államok és egy olyan adathalmazt tartalmaz, amely nem USA-beli felnőtteknek szóló népszámlálási információkat tartalmaz.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

1. Alakítsa át az adatkészletet, hogy előkészítse a képzést.
1. Exportálja az eredményül kapott adatkészleteket egy adattárba.
1. Eredmények megtekintése.

Ez az útmutató a [tervezői modellek újratanításának](how-to-retrain-designer.md) előfeltétele. Ebből a cikkből megtudhatja, hogyan használhatja az átalakított adatkészleteket több modell a folyamat paramétereinek betanításához.

## <a name="transform-a-dataset"></a>Adatkészlet átalakítása

Ebből a szakaszból megtudhatja, hogyan importálhatja a minta-adatkészletet, és hogyan oszthatja fel az adatokat az USA-ba és a nem amerikai adatkészletekbe. A saját adatok tervezőbe történő importálásával kapcsolatos további információkért lásd: [adatok importálása](how-to-designer-import-data.md).

### <a name="import-data"></a>Adatok importálása

A minta adatkészlet importálásához kövesse az alábbi lépéseket.

1. Jelentkezzen be a <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.Azure.com</a>-be, és válassza ki a munkaterületet, amellyel dolgozni szeretne.

1. Nyissa meg a tervezőt. Az új folyamat létrehozásához válassza a **könnyen használható előépítő modulok** elemet.

1. Válasszon ki egy alapértelmezett számítási célt a folyamat futtatásához.

1. Az adatkészletek és modulok palettájának bal oldalán található. Válassza az **adatkészletek**lehetőséget. Ezután tekintse meg a **minták** szakaszt.

1. Húzza a **felnőtt népszámlálás jövedelmének bináris besorolási** adatkészletét a vászonra.

1. Válassza ki a **felnőtt népszámlálás-bevétel** adatkészletének modulját.

1. A vászon jobb oldalán megjelenő részletek ablaktáblán válassza a **kimenetek**lehetőséget.

1. Megjelenítés ikon kiválasztása ![ikon megjelenítése](media/how-to-designer-transform-data/visualize-icon.png).

1. Az adatkészlet megismeréséhez használja az adatelőnézet ablakot. Ügyeljen a "natív ország" oszlop értékeire.

### <a name="split-the-data"></a>Az adatok felosztása

Ebben a szakaszban az [Adatfelosztási modul](algorithm-module-reference/split-data.md) használatával azonosíthatja és feloszthatja az "Egyesült Államok" kifejezést tartalmazó sorokat a "natív ország" oszlopban. 

1. A vászon bal oldalán található modul palettán bontsa ki az **adatátalakítás** szakaszt, és keresse meg az **adatfelosztási** modult.

1. Húzza az **adat felosztása** modult a vászonra, és dobja el a modult az adatkészlet modul alatt.

1. Az adatkészlet modul csatlakoztatása az **Adatfelosztási** modulhoz.

1. Válassza ki az **Adatfelosztási** modult.

1. A vászontól jobbra található modul részletei ablaktáblán állítsa be a **felosztási módot** **reguláris kifejezésre**.

1. Adja meg a **reguláris kifejezést**: `\"native-country" United-States`.

    A **reguláris kifejezés** mód egyetlen oszlopot tesztel egy értékhez. További információ az adatfelosztási modulról: a kapcsolódó [algoritmus moduljának hivatkozási lapja](algorithm-module-reference/split-data.md).

A folyamatnak így kell kinéznie:

![A folyamat konfigurálását és az adatfelosztási modult bemutató képernyőkép](media/how-to-designer-transform-data/split-data.png).


## <a name="save-the-datasets"></a>Az adatkészletek mentése

Most, hogy a folyamat az adat felosztására van beállítva, meg kell adnia, hol kell megőriznie az adatkészleteket. Ebben a példában az **adat exportálása** modul használatával mentse az adatkészletet egy adattárba. További információ az [adattárakról: Kapcsolódás az Azure Storage Serviceshez](how-to-access-data.md)

1. A vászon bal oldalán található modul palettán bontsa ki az **adat-bemenet és kimenet** szakaszt, és keresse meg az **adatexportálás** modult.

1. Húzzon át két **exportálási** adatmodult a **felosztott** adatmodul alá.

1. Az összes **felosztott** adatmodul kimeneti portjának összekötése egy másik **adatexportálási** modulba.

    A folyamatnak a következőhöz hasonlóan kell kinéznie:

    ![Az adatexportálási modulok összekapcsolását bemutató képernyőkép](media/how-to-designer-transform-data/export-data-pipeline.png).

1. Válassza ki az **adatexportálási** modult, amely az **osztott** adatmodul *bal*szélső portjához csatlakozik.

    A kimeneti portok sorrendje az **Adatfelosztási** modulhoz. Az első kimeneti port tartalmazza azokat a sorokat, amelyekben a reguláris kifejezés igaz. Ebben az esetben az első port az USA-alapú bevétel sorát tartalmazza, a második port pedig a nem USA-alapú bevétel sorait tartalmazza.

1. A vászontól jobbra a modul részletei ablaktábláján adja meg a következő beállításokat:
    
    **Adattár típusa**: Azure Blob Storage

    **Adattár**: válasszon ki egy meglévő adattárat, vagy válassza az "új adattár" lehetőséget a létrehozáshoz.

    **Elérési út**:`/data/us-income`

    **Fájl formátuma**: CSV

    > [!NOTE]
    > Ez a cikk azt feltételezi, hogy van hozzáférése az aktuális Azure Machine Learning munkaterülethez regisztrált adattárhoz. Az adattár beállításával kapcsolatos utasításokért lásd: [Kapcsolódás az Azure Storage Serviceshez](how-to-access-data.md#azure-machine-learning-studio).

    Ha nincs adattára, létrehozhat egyet. Ez a cikk például az adatkészletek mentését fogja menteni a munkaterülethez társított alapértelmezett blob Storage-fiókba. Az adatkészleteket egy új mappában fogja `azureml` menteni a tárolóba `data`.

1.  Válassza ki az **adatexportálási** modult az **osztott** adatmodul *jobb*szélső portjához csatlakoztatva.

1. A vászontól jobbra a modul részletei ablaktábláján adja meg a következő beállításokat:
    
    **Adattár típusa**: Azure Blob Storage

    **Adattár**: válassza ki a fenti adattárat

    **Elérési út**:`/data/non-us-income`

    **Fájl formátuma**: CSV

1. Erősítse meg, hogy az **adatexportálási** modul a **felosztott** adatmennyiség bal oldali portjához **csatlakozik.** `/data/us-income`

1. Erősítse meg, hogy a megfelelő porthoz csatlakoztatott **adatexportálási** modul **elérési útja** `/data/non-us-income`.

    A folyamatnak és a beállításoknak így kell kinéznie:
    
    ![Az adatexportálási modulok konfigurálását bemutató képernyőkép](media/how-to-designer-transform-data/us-income-export-data.png).

### <a name="submit-the-run"></a>A Futtatás elküldése

Most, hogy a folyamat az adatfelosztást és-exportálást állítja be, elküld egy folyamat futtatását.

1. A vászon tetején válassza a **Küldés**lehetőséget.

1. A **folyamat futtatásának beállítása** párbeszédpanelen válassza az **új létrehozása** lehetőséget a kísérlet létrehozásához.

    A kísérletek logikailag csoportosítják a kapcsolódó folyamat-futtatásokat. Ha ezt a folyamatot a jövőben futtatja, ugyanazt a kísérletet kell használnia a naplózási és nyomkövetési célokra.

1. Adjon meg egy beszédes kísérletet, például a "Split-Census-adatai" nevet.

1. Válassza a **Küldés** lehetőséget.

## <a name="view-results"></a>Eredmények megtekintése

A folyamat futásának befejeződése után megtekintheti az eredményeket, ha a blob Storage-ban navigál a Azure Portal. Megtekintheti az **Adatfelosztási** modul közbenső eredményeit is, így ellenőrizheti, hogy az adatai megfelelően vannak-e felosztva.

1. Válassza ki az **Adatfelosztási** modult.

1. A vászon jobb oldalán található modul részletei ablaktáblán válassza a **kimenetek + naplók**lehetőséget. 

1. Válassza a vizualizáció ikon ![megjelenítése ikont](media/how-to-designer-transform-data/visualize-icon.png) az **eredmények DataSet1 elemet**mellett. 

1. Ellenőrizze, hogy a "natív ország" oszlop csak az "Egyesült Államok" értéket tartalmazza-e.

1. Válassza a vizualizáció ikon ![megjelenítése ikont](media/how-to-designer-transform-data/visualize-icon.png) az **eredmények dataset2**mellett. 

1. Ellenőrizze, hogy a "natív ország" oszlop nem tartalmazza-e az "Egyesült Államok" értéket.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ugorja át ezt a szakaszt, ha továbbra is az útmutató 2. részében kívánja folytatni a [modellek újratanítását Azure Machine learning Designer](how-to-retrain-designer.md)használatával.

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan alakíthat át egy adatkészletet, és hogyan mentheti egy regisztrált adattárba.

Folytassa a következő lépéssel, hogy az adatsorozat a [modelleket Azure Machine learning designerrel](how-to-retrain-designer.md) együtt használja az átalakított adatkészletek és a folyamat paramétereinek a gépi tanulási modellek betanításához való használatára.
