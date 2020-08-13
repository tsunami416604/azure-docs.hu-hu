---
title: Adatkészletek létrehozása az Azure Open-adatkészletekkel
titleSuffix: Azure Open Datasets
description: Megtudhatja, hogyan hozhat létre Azure Machine Learning adatkészletet az Azure Open adatkészletekről.
ms.service: open-datasets
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.date: 08/05/2020
ms.custom: how-to, tracking-python
ms.openlocfilehash: e6f3a541f1e9dbca2c9949fb0c5cde28cd43e8e5
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88183024"
---
# <a name="create-azure-machine-learning-datasets-from-azure-open-datasets"></a>Azure Machine Learning adatkészletek létrehozása az Azure Open-adatkészletből
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan hozhat létre Azure Machine Learning adatkészletet az [Azure Open adatkészletek](https://azure.microsoft.com/services/open-datasets/) használatával a helyi vagy távoli kísérletek adatainak eléréséhez. 

[Azure Machine learning adatkészlet](../machine-learning/how-to-create-register-datasets.md)létrehozásával létrehoz egy hivatkozást az adatforrás helyére, valamint a metaadatok másolatát. Mivel az információk a meglévő helyükön maradnak, nem számítunk fel extra tárolási költséget, és nem kockáztatják véletlenül az eredeti adatforrások módosítását. Emellett az adathalmazok lustán is kiértékelésre kerülnek, ami a munkafolyamatok teljesítményének sebességét segíti elő.
 
Annak megismeréséhez, hogy az adatkészletek hogyan illeszkednek Azure Machine Learning összesített adatelérési munkafolyamataihoz, tekintse meg a [biztonságos hozzáférés adatai](../machine-learning/concept-data.md#data-workflow) című cikket.


A nyílt adatkészletek a felhőben vannak Microsoft Azure, és a [Azure Machine learning PYTHON SDK](#create-datasets-with-the-sdk) és a [Azure Machine learning Studióban](#create-datasets-with-the-studio)is szerepelnek.

## <a name="why-use-azure-open-datasets"></a>Miért érdemes az Azure Open-adatkészleteket használni? 

Az Azure Open-adatkészletek olyan beszerzett nyilvános adatkészletek, amelyekkel pontosabb modelleket adhat hozzá a gépi tanulási megoldásokhoz. Az adatkészletek olyan nyilvános tartományi adatokat foglalnak magukban, mint az időjárás, a népszámlálás, az ünnepnapok, a közbiztonság és a gépi tanulási modellek betanítását és a prediktív megoldások bővítését segítő hely. 

További információ: [Mik a megnyitott adatkészletek?](overview-what-are-open-datasets.md)

## <a name="prerequisites"></a>Előfeltételek

Az adatkészletek létrehozásához és működéséhez a következőkre lesz szüksége:

* Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy ingyenes fiókot, mielőtt hozzákezd. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree).

* Egy [Azure Machine learning munkaterület](../machine-learning/how-to-manage-workspace.md).

* A [Azure Machine learning SDK for Python telepítve](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), amely tartalmazza a azureml-adatkészletek csomagot.

    * Hozzon létre egy [Azure Machine learning számítási példányt](../machine-learning/concept-compute-instance.md#managing-a-compute-instance), amely egy teljesen konfigurált és felügyelt fejlesztői környezet, amely integrált jegyzetfüzeteket és már telepített SDK-t tartalmaz.

    **VAGY**

    * Saját Jupyter notebookján dolgozhat, és saját kezűleg is telepítheti az SDK-t [ezekkel az utasításokkal](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

> [!NOTE]
> Egyes adatkészlet-osztályok függőségekkel rendelkeznek a [azureml-adatelőkészítés](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) csomagon, amely csak a 64 bites Python rendszerrel kompatibilis. A Linux-felhasználók esetében ezek az osztályok csak a következő disztribúciókban támogatottak: Red Hat Enterprise Linux (7, 8), Ubuntu (14,04, 16,04, 18,04), Fedora (27, 28), Debian (8, 9) és CentOS (7).

## <a name="create-datasets-with-the-sdk"></a>Adatkészletek létrehozása az SDK-val

Adatkészletek létrehozásához a Azure Machine Learning Python SDK-ban nyissa meg az adatkészletek osztályait, győződjön meg róla, hogy telepítette a csomagot a következővel: `pip install azureml-opendatasets` . Minden különálló adatkészletet a saját osztálya képvisel az SDK-ban, és bizonyos osztályok akár a,, `TabularDataset` akár `FileDataset` mindkettőként is elérhetők. Az osztályok teljes listáját a [dokumentációban](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py) találja.

Bizonyos osztályokat lekérhet a `TabularDataset` vagy rendszerbe `FileDataset` , amely lehetővé teszi a fájlok közvetlen kezelését és/vagy letöltését. Más osztályok **csak** az egyik vagy függvény használatával kaphatnak adatkészletet `get_tabular_dataset()` `get_file_dataset()` . Az alábbi mintakód néhány példát mutat be az ilyen típusú osztályokra.

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

Amikor egy megnyitott adatkészletből létrehozott adatkészletet regisztrál, az adatok letöltése nem történik meg azonnal, de az adatok a kéréskor (például a betanítás során) egy központi tárolóhelyről lesznek elérhetők.

## <a name="create-datasets-with-the-studio"></a>Adatkészletek létrehozása a Studióval

Létrehozhat adatkészleteket is a nyílt adatkészletek között a [Azure Machine learning Studióval](https://ml.azure.com).

1. A munkaterületen válassza az **adatkészletek** lapot az **eszközök**területen. Az **adatkészlet létrehozása** legördülő menüben válassza a **megnyitott adatkészletek**lehetőséget.

    ![Adatkészlet megnyitása a felhasználói felületen](./media/how-to-create-dataset-from-open-dataset/open-datasets-1.png)

1. Válasszon ki egy adatkészletet a csempe kiválasztásával. (A keresősáv használatával szűrheti a szűrőt.) Válassza a **tovább**lehetőséget.

    ![Adatkészlet kiválasztása](./media/how-to-create-dataset-from-open-dataset/open-datasets-2.png)

1. Válassza ki azt a nevet, amelyben regisztrálni kívánja az adatkészletet, és opcionálisan szűrheti az adatokat az elérhető szűrők használatával. Ebben az esetben a **munkaszüneti** adatkészletek esetében az időszakot egy évig, az országkód pedig csak az Egyesült Államokban szűri. Kattintson a **Létrehozás** gombra.

    ![Adatkészlet-paraméterek beállítása és adatkészlet létrehozása](./media/how-to-create-dataset-from-open-dataset/open-datasets-3.png)

    Az adatkészlet mostantól elérhető a munkaterületen az **adatkészletek**területen. Azt ugyanúgy használhatja, mint a létrehozott többi adatkészletet.


## <a name="access-datasets-for-your-experiments"></a>A kísérletek hozzáférési adatkészletei

Az adatkészleteket a gépi tanulási kísérletekben használhatja a ML-modellek betanításához. [További információ az adatkészletek betanításáról](../machine-learning/how-to-train-with-datasets.md).

## <a name="example-notebooks"></a>Példajegyzetfüzetek

A nyílt adatkészletek funkcióinak példái és bemutatói esetében ezek a [jegyzetfüzetek](https://github.com/Azure/OpenDatasetsNotebooks/tree/master/tutorials)találhatók.

## <a name="next-steps"></a>Következő lépések

* [Modell betanítása adatkészletekkel](../machine-learning/how-to-train-with-datasets.md).

* [Hozzon létre egy Azure Machine learning-adatkészletet](../machine-learning/how-to-create-register-datasets.md).



