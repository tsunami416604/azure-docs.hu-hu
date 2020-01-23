---
title: Adathalmazok létrehozása és feltárása címkékkel
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan exportálhat adatfeliratokat a Azure Machine Learning címkéző projektjeiből, és hogyan használhatja őket gépi tanulási feladatokhoz.
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.topic: how-to
ms.date: 01/21/2020
ms.openlocfilehash: 5138109de3f80d405ce95b605714b511480563f5
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76549488"
---
# <a name="create-and-explore-azure-machine-learning-dataset-with-labels"></a>Azure Machine Learning adatkészlet létrehozása és feltárása címkékkel

Ebből a cikkből megtudhatja, hogyan exportálhatja az adatfeliratokat egy Azure Machine Learning adatcímkéző projektből, és hogyan töltheti be azokat népszerű formátumba, például egy Panda dataframe az adatfeltáráshoz vagy egy Torchvision adatkészlethez a képátalakításhoz. 

## <a name="what-are-datasets-with-labels"></a>A címkékkel rendelkező adatkészletek 

Azure Machine Learning címkéket tartalmazó adatkészleteket [TabularDatasets](how-to-create-register-datasets.md#dataset-types) , a címkével ellátott adatkészletként fogunk hivatkozni rájuk. Ezek a TabularDatasets-típusok csak Azure Machine Learning adatcímkéző projektek kimenetében jönnek létre. Hozzon létre egy adatcímkéző projektet a [következő lépésekkel](how-to-create-labeling-projects.md). A Machine Learning támogatja a képbesoroláshoz a többcímkés vagy a többosztályos, valamint az objektum-azonosítókat a kötött mezőkkel együtt.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://aka.ms/AMLFree) a virtuális gép létrehozásának megkezdése előtt.
* A [Pythonhoz készült Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py), vagy a [Azure Machine learning studióhoz](https://ml.azure.com/)való hozzáférés.
    * Az [Azure--beli-adathalmaz-adatkészlet](https://docs.microsoft.com/python/api/azureml-contrib-dataset/?view=azure-ml-py) csomagjának telepítése
* Machine Learning munkaterület. Lásd: [Azure Machine learning munkaterület létrehozása](how-to-manage-workspace.md).
* Hozzáférés egy Azure Machine Learning adatcímkéző projekthez. Ha nem rendelkezik címkéző projekttel, hozzon létre egyet [ezekkel a lépésekkel](how-to-create-labeling-projects.md).

## <a name="export-data-labels"></a>Adatfeliratok exportálása 

Amikor elvégez egy adatcímkéző projektet, exportálhatja a címke adatait egy címkéző projektből. Ezzel lehetővé teszi, hogy rögzítse az adatokat és a hozzájuk tartozó címkéket is, és exportálja őket [kókusz formátumban](http://cocodataset.org/#format-data) vagy Azure Machine learning adatkészletként. Használja az **Exportálás** gombot a címkéző projekt **Project Details (projekt részletei** ) lapján.

### <a name="coco"></a>COCO 

 A kókusz-fájl a Azure Machine Learning munkaterület alapértelmezett blob-tárolójában jön létre az *export/Coco*mappában lévő mappában. 

### <a name="azure-machine-learning-dataset"></a>Azure Machine Learning adatkészlet

Az exportált Azure Machine Learning adatkészletet a Azure Machine Learning Studio **adatkészletek** szakaszában érheti el. Az adatkészlet **részletei** lap a Pythonból elérhető feliratok eléréséhez is tartalmaz mintakód-kódot.

![Exportált adatkészlet](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="explore-labeled-datasets"></a>Címkézett adatkészletek megismerése

Betöltheti a címkével ellátott adatkészleteket egy Panda dataframe vagy Torchvision-adatkészletbe a népszerű nyílt forráskódú kódtárak kihasználása érdekében az adatok feltárásához, valamint a képek átalakításához és betanításához a PyTorch biztosított könyvtárakat.

### <a name="pandas-dataframe"></a>Panda dataframe

A címkézett adatkészleteket egy Panda dataframe is betöltheti a `azureml-contrib-dataset` osztály [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) metódusával. Telepítse az osztályt a következő rendszerhéj-paranccsal: 

```shell
pip install azureml-contrib-dataset
```

>[!NOTE]
>A azureml. a névterek gyakran változnak, ahogy dolgozunk a szolgáltatás tökéletesítésén. Ennek megfelelően az ebben a névtérben található bármit előzetes verziónak kell tekinteni, és a Microsoft nem támogatja teljes mértékben.

A következő fájlkezelési lehetőségeket kínáljuk a fájl-adatfolyamok dataframe való konvertálásakor.
* Letöltés: töltse le az adatfájlokat egy helyi elérési útra.
* Csatlakoztatás: az adatfájlok csatlakoztatása csatlakoztatási ponthoz. A Mount csak a Linux-alapú számítási feladatokhoz használható, beleértve a Azure Machine Learning notebook VM-et és a Azure Machine Learning számítási feladatait.

```Python
import azureml.contrib.dataset
from azureml.contrib.dataset import FileHandlingOption
animal_pd = animal_labels.to_pandas_dataframe(file_handling_option=FileHandlingOption.DOWNLOAD, target_path='./download/', overwrite_download=True)

import matplotlib.pyplot as plt
import matplotlib.image as mpimg

#read images from downloaded path
img = mpimg.imread(animal_pd.loc[0,'image_url'])
imgplot = plt.imshow(img)
```

### <a name="torchvision-datasets"></a>Torchvision adatkészletek

Címkézett adatkészleteket is betölthet a Torchvision adatkészletbe a [to_torchvision ()](https://docs.microsoft.com/python/api/azureml-contrib-dataset/azureml.contrib.dataset.tabulardataset?view=azure-ml-py#to-torchvision--) metódussal a `azureml-contrib-dataset` osztályból is. Ennek a módszernek a használatához telepítenie kell a [PyTorch](https://pytorch.org/) . 

```python
from torchvision.transforms import functional as F

# load animal_labels dataset into torchvision dataset
pytorch_dataset = animal_labels.to_torchvision()
img = pytorch_dataset[0][0]
print(type(img))

# use methods from torchvision to transform the img into grayscale
pil_image = F.to_pil_image(img)
gray_image = F.to_grayscale(pil_image, num_output_channels=3)

imgplot = plt.imshow(gray_image)
```

## <a name="next-steps"></a>Következő lépések

* Tekintse meg az [adathalmazt a címkék jegyzetfüzettel](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb) a teljes betanítási minta megtekintéséhez.
