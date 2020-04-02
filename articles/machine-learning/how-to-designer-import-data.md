---
title: Adatok importálása
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan importálhatja adatait az Azure Machine Learning tervezőjébe különböző adatforrásokból.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.date: 01/16/2020
ms.openlocfilehash: 1ad7677607d625f673546a6ea29ea58b80a8d1b5
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546757"
---
# <a name="import-your-data-into-azure-machine-learning-designer-preview"></a>Adatok importálása az Azure Machine Learning tervezőjében (előzetes verzió)

Ebben a cikkben megtudhatja, hogyan importálhatja a saját adatait a tervezőegyéni megoldások létrehozásához. Kétféleképpen importálhat adatokat a tervezőbe: 

* **Azure Machine Learning-adatkészletek** – Regisztráljon [adatkészleteket](concept-data.md#datasets) az Azure Machine Learningben, hogy olyan speciális funkciókat engedélyezhet, amelyek segítenek az adatok kezelésében.
* **Adatok importálása modul** – Az [Adatok importálása](algorithm-module-reference/import-data.md) modullal közvetlenül érheti el az online adatforrásokból származó adatokat.

## <a name="use-azure-machine-learning-datasets"></a>Azure Machine Learning-adatkészletek használata

Azt javasoljuk, hogy [adatkészletek](concept-data.md#datasets) használatával importálja az adatokat a tervezőbe. Adatkészlet regisztrálásakor teljes mértékben kihasználhatja az olyan speciális adatfunkciók előnyeit, mint a [verziószámozás, a nyomon követés](how-to-version-track-datasets.md) és [az adatfigyelés.](how-to-monitor-datasets.md)

### <a name="register-a-dataset"></a>Adatkészlet regisztrálása

A meglévő adatkészleteket programozott módon regisztrálhatja [az SDK-val](how-to-create-register-datasets.md#use-the-sdk) vagy vizuálisan az [Azure Machine Learning stúdióban.](how-to-create-register-datasets.md#use-the-ui)

Bármely tervezőmodul kimenetét adatkészletként is regisztrálhatja.

1. Válassza ki azt a modult, amely a regisztrálni kívánt adatokat adja ki.

1. A Tulajdonságok ablaktáblán válassza **a Kimenetek** > **regisztrálása adatkészlet lehetőséget.**

    ![Képernyőkép az Adatkészlet regisztrálása beállításról](media/how-to-designer-import-data/register-dataset-designer.png)

### <a name="use-a-dataset"></a>Adatkészlet használata

A regisztrált adatkészletek a modulpalettán, az Adatkészletek saját **adatkészletei** > **My Datasets**csoportban találhatók. Adatkészlet használatához húzza azt a folyamatvászonra. Ezután csatlakoztassa az adatkészlet kimeneti portját a paletta más moduljaihoz.

![Képernyőkép a mentett adatkészletek helyéről a tervezőpalettáján](media/how-to-designer-import-data/use-datasets-designer.png)


> [!NOTE]
> A tervező jelenleg csak a [táblázatos adatkészletek feldolgozását](how-to-create-register-datasets.md#dataset-types)támogatja. Ha [fájladatkészleteket](how-to-create-register-datasets.md#dataset-types)szeretne használni, használja az Azure Machine Learning SDK elérhető Python és R.

## <a name="import-data-using-the-import-data-module"></a>Adatok importálása az Adatok importálása modullal

Bár azt javasoljuk, hogy adatkészletek használatával importálja az adatokat, használhatja az [Adatok importálása](algorithm-module-reference/import-data.md) modult is. Az Adatok importálása modul kihagyja az adatkészlet regisztrálását az Azure Machine Learningben, és közvetlenül egy [adattárból](concept-data.md#datastores) vagy HTTP URL-címből importálja az adatokat.

Az Adatok importálása modul használatáról az [Adatok importálása hivatkozási lapon talál](algorithm-module-reference/import-data.md)részletes tájékoztatást.

> [!NOTE]
> Ha az adatkészlet túl sok oszlopot tartalmaz, a következő hiba jelenhet meg: "Az érvényesítés a méretkorlátozás miatt nem sikerült". Ennek elkerülése [érdekében regisztrálja az adatkészletet az Adatkészletek felületen.](how-to-create-register-datasets.md#use-the-ui)

## <a name="supported-sources"></a>Támogatott források

Ez a szakasz a tervező által támogatott adatforrásokat sorolja fel. Az adatok adattárból vagy [táblázatos adatkészletből](how-to-create-register-datasets.md#dataset-types)érkeznek a tervezőbe.

### <a name="datastore-sources"></a>Adattárforrásai
A támogatott adattárforrások listáját az [Access-adatok az Azure storage-szolgáltatásokban című témakörben található.](how-to-access-data.md#supported-data-storage-service-types)

### <a name="tabular-dataset-sources"></a>Táblázatos adatkészletforrások

A tervező a következő forrásokból létrehozott táblázatos adatkészleteket támogatja:
 * Tagolt fájlok
 * JSON-fájlok
 * Parquet-fájlok
 * SQL-lekérdezések

## <a name="data-types"></a>Adattípusok

A tervező belsőleg felismeri a következő adattípusokat:

* Sztring
* Egész szám
* Decimal
* Logikai
* Dátum

A tervező egy belső adattípust használ a modulok közötti adatátatovábbadáshoz. Az adatokat explicit módon adattábla formátumba konvertálhatja a [Convert to Dataset](algorithm-module-reference/convert-to-dataset.md) module segítségével. Minden olyan modul, amely elfogadja a belső formátumtól eltérő formátumokat, csendben konvertálja az adatokat, mielőtt átadná a következő modulnak.

## <a name="data-constraints"></a>Adatmegkötések

A tervező moduljait a számítási cél mérete korlátozza. Nagyobb adatkészletek esetén nagyobb Azure Machine Learning számítási erőforrást kell használnia. Az Azure Machine Learning-számításról a [Mik azok a számítási célok az Azure Machine Learningben című témakörben talál további információt?](concept-compute-target.md#azure-machine-learning-compute-managed)

## <a name="next-steps"></a>További lépések

Ismerje meg az alapokat a tervező [bemutató: megjósolni autó ára a tervező](tutorial-designer-automobile-price-train-score.md).
