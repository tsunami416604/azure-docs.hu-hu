---
title: Adatimportálás a designerbe (előzetes verzió)
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan importálhat adatforrásokat a Azure Machine Learning designerbe (előzetes verzió) különböző adatforrásokból.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.date: 01/16/2020
ms.custom: designer
ms.openlocfilehash: 7a204310454cb636e26e6366d4ff7375f4faea8d
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86207515"
---
# <a name="import-data-into-azure-machine-learning-designer-preview"></a>Az adatimportálás Azure Machine Learning designerbe (előzetes verzió)

Ebből a cikkből megtudhatja, hogyan importálhat saját adatait a Designerben egyéni megoldások létrehozásához. Az adatgyűjtés kétféleképpen importálható a tervezőbe: 

* **Azure Machine learning adatkészletek** – [adatkészletek](concept-data.md#datasets) regisztrálása a Azure Machine Learningban az adatkezelést segítő speciális funkciók engedélyezéséhez.
* **Adatmodul importálása** – az adatok [importálása](algorithm-module-reference/import-data.md) modul használatával közvetlenül férhet hozzá az online adatforrásokból származó adatokhoz.

## <a name="use-azure-machine-learning-datasets"></a>Azure Machine Learning adatkészletek használata

Azt javasoljuk, hogy [adatkészletek](concept-data.md#datasets) használatával importálja az adatokat a tervezőbe. Amikor regisztrál egy adatkészletet, teljes mértékben kihasználhatja a fejlett adatszolgáltatások előnyeit, például a [verziószámozást és a nyomon követést](how-to-version-track-datasets.md) és [az adatfigyelést](how-to-monitor-datasets.md).

### <a name="register-a-dataset"></a>Adatkészlet regisztrálása

A meglévő adatkészleteket [programozott módon az SDK-val,](how-to-create-register-datasets.md#use-the-sdk) vagy [vizuálisan Azure Machine learning Studióban](how-to-create-register-datasets.md#use-the-ui)is.

Bármely tervező modul kimenetét adatkészletként is regisztrálhatja.

1. Válassza ki azt a modult, amely a regisztrálni kívánt adatokat kiírja.

1. A Tulajdonságok panelen válassza a **kimenetek**  >  **regisztrálása adatkészletet**.

    ![Az adatkészlet regisztrálása lehetőségre való váltást bemutató képernyőkép](media/how-to-designer-import-data/register-dataset-designer.png)

### <a name="use-a-dataset"></a>Adatkészlet használata

A regisztrált adatkészletek a modul palettáján **, az**  >  **adathalmazok adatkészletek**területen találhatók. Adatkészlet használatához húzza azt a folyamat vászonra. Ezután kapcsolja össze az adatkészlet kimeneti portját a paletta más moduljaival.

![A mentett adatkészletek helyét bemutató képernyőkép a tervező palettán](media/how-to-designer-import-data/use-datasets-designer.png)


> [!NOTE]
> A tervező jelenleg csak a [táblázatos adatkészletek](how-to-create-register-datasets.md#dataset-types)feldolgozását támogatja. Ha [fájl-adatkészleteket](how-to-create-register-datasets.md#dataset-types)szeretne használni, használja a Python és az R számára elérhető Azure Machine learning SDK-t.

## <a name="import-data-using-the-import-data-module"></a>Adatimportálás az Adatimportálási modul használatával

Habár azt javasoljuk, hogy adatkészletek használatával importálja az adatokat, az [adatimportálás](algorithm-module-reference/import-data.md) modult is használhatja. Az Adatimportálási modul kihagyja az adathalmaz regisztrálását Azure Machine Learning és közvetlenül egy [adattárból](concept-data.md#datastores) vagy http URL-címről importálja az adatokat.

Az adatok importálása modul használatával kapcsolatos részletes információkért lásd az [adatok importálása – hivatkozás lapot](algorithm-module-reference/import-data.md).

> [!NOTE]
> Ha az adatkészlet túl sok oszlopot tartalmaz, a következő hibaüzenet jelenhet meg: "az érvényesítés a méret korlátozása miatt meghiúsult". Ennek elkerüléséhez [regisztrálja az adatkészletet az adatkészletek kezelőfelületén](how-to-create-register-datasets.md#use-the-ui).

## <a name="supported-sources"></a>Támogatott források

Ez a szakasz a tervező által támogatott adatforrásokat sorolja fel. Az adatok egy adattárból vagy egy [táblázatos adatkészletből](how-to-create-register-datasets.md#dataset-types)származnak a tervezőbe.

### <a name="datastore-sources"></a>Adattár-források
A támogatott adattár-források listáját lásd: az [Azure Storage-szolgáltatásokban tárolt adathozzáférés](how-to-access-data.md#supported-data-storage-service-types).

### <a name="tabular-dataset-sources"></a>Táblázatos adatkészlet forrásai

A Designer a következő forrásokból létrehozott táblázatos adatkészleteket támogatja:
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

A tervező egy belső adattípust használ a modulok közötti adatátvitelhez. Az adatokat adattábla formátumba explicit módon konvertálhatja az [átalakítás adatkészletbe](algorithm-module-reference/convert-to-dataset.md) modul használatával. Minden olyan modul, amely a belső formátumtól eltérő formátumot fogad el, a következő modulba való továbbítás előtt visszafogja az adatátalakítást.

## <a name="data-constraints"></a>Adatkorlátozások

A tervezőben lévő modulokat a számítási cél mérete korlátozza. Nagyobb adatkészletek esetén nagyobb Azure Machine Learning számítási erőforrást kell használnia. További információ a Azure Machine Learning számítási feladatokról: [Mik a számítási célok a Azure Machine Learningban?](concept-compute-target.md#azure-machine-learning-compute-managed)

## <a name="access-data-in-a-virtual-network"></a>Virtuális hálózatban tárolt adathozzáférés

Ha a munkaterület virtuális hálózatban található, további konfigurációs lépéseket kell végrehajtania a tervezőben lévő adatok megjelenítéséhez. További információ az adattárolók és adatkészletek virtuális hálózatban való használatáról: [hálózati elkülönítés a betanítás során & a privát virtuális hálózatokkal való következtetés](how-to-enable-virtual-network.md#machine-learning-studio).

## <a name="next-steps"></a>További lépések

Ismerje meg a tervező alapjait az [oktatóanyaggal: az autó árának előrejelzése a tervezővel](tutorial-designer-automobile-price-train-score.md).
