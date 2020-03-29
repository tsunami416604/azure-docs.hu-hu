---
title: Adatkészletek létrehozása és feltárása címkékkel
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan exportálhatja az adatcímkéket az Azure Machine Learning címkézési projektekből, és hogyan használhatja őket gépi tanulási feladatokhoz.
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.topic: how-to
ms.date: 01/21/2020
ms.openlocfilehash: 5138109de3f80d405ce95b605714b511480563f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76549488"
---
# <a name="create-and-explore-azure-machine-learning-dataset-with-labels"></a>Az Azure Machine Learning-adatkészlet létrehozása és feltárása címkékkel

Ebben a cikkben megtudhatja, hogyan exportálhatja az adatcímkéket egy Azure Machine Learning-adatcímkézési projektből, és hogyan töltheti be őket olyan népszerű formátumokba, mint például egy panda stafa adatkeret adatfeltáráshoz vagy egy Torchvision adatkészlet képátalakításhoz. 

## <a name="what-are-datasets-with-labels"></a>Mik azok a címkékkel ellátott adatkészletek? 

A címkékkel rendelkező Azure Machine Learning-adatkészletek címketulajdonsággal rendelkező [TabularDatasets,](how-to-create-register-datasets.md#dataset-types) akkor címkével ellátott adatkészletekként hivatkozunk rájuk. Ezek a tabulardatasets ezek a konkrét típusú csak az Azure Machine Learning-adatok címkézési projektek kimeneteként jönnek létre. Ezekkel a lépésekkel hozzon létre adatcímkézési [projektet.](how-to-create-labeling-projects.md) A Machine Learning támogatja a képbesoroláshoz kapcsolódó adatcímkézési projekteket, akár többcímkét, akár többosztályos, valamint az objektumazonosítást a kötött mezőkkel együtt.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://aka.ms/AMLFree) mielőtt elkezdené.
* Az [Azure Machine Learning SDK pythonhoz](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py), vagy az Azure Machine Learning [stúdióhoz](https://ml.azure.com/)való hozzáférés.
    * Az [azure-contrib-adatkészletcsomag](https://docs.microsoft.com/python/api/azureml-contrib-dataset/?view=azure-ml-py) telepítése
* Machine Learning-munkaterület. Lásd: [Azure Machine Learning-munkaterület létrehozása.](how-to-manage-workspace.md)
* Hozzáférés egy Azure Machine Learning-adatcímkézési projekthez. Ha nem rendelkezik címkézési projekttel, hozzon létre egyet [ezekkel](how-to-create-labeling-projects.md)a lépésekkel.

## <a name="export-data-labels"></a>Adatfeliratok exportálása 

Amikor befejez egy adatcímkézési projektet, exportálhatja a feliratadatokat egy címkézési projektből. Ezzel lehetővé teszi, hogy rögzítse az adatokra és a címkékre mutató hivatkozást, és exportálja őket [COCO formátumban](http://cocodataset.org/#format-data) vagy Azure Machine Learning-adatkészletként. Használja az **Exportálás** gombot a címkeprojekt **Projekt részletei** lapján.

### <a name="coco"></a>Coco 

 A COCO-fájl az Azure Machine Learning-munkaterület alapértelmezett blobtárolójában jön létre az *exportálás/kakaó*mappájában. 

### <a name="azure-machine-learning-dataset"></a>Azure Machine Learning-adatkészlet

Az exportált Azure Machine Learning-adatkészlet az Azure Machine Learning-stúdió **Adatkészletek** szakaszában érhető el. Az adatkészlet **részletei** lap is biztosít mintakódot a címkék eléréséhez a Python.

![Exportált adatkészlet](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="explore-labeled-datasets"></a>Címkézett adatkészletek felfedezése

Töltse be a címkézett adatkészleteket egy pandas dataframe-be vagy Torchvision adatkészletbe, hogy kihasználja a népszerű nyílt forráskódú kódtárakat az adatok feltárásához, valamint a PyTorch könyvtárakat biztosított a képátalakításhoz és -betanításhoz.

### <a name="pandas-dataframe"></a>Pandák adatkerete

A címkézett adatkészleteket az [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) `azureml-contrib-dataset` osztály metódusával pandák adatkeretbe töltheti be. Telepítse az osztályt a következő parancsértelmezővel: 

```shell
pip install azureml-contrib-dataset
```

>[!NOTE]
>Az azureml.contrib névtér gyakran változik, miközben a szolgáltatás fejlesztésén dolgozunk. Mint ilyen, a névtérben lévő minden előnézetnek tekintendő, és a Microsoft nem támogatja teljes mértékben.

A következő fájlkezelési lehetőségeket kínáljuk a fájlfolyamok számára, amikor pandák adatkeretté konvertál.
* Letöltés: Töltse le az adatfájlokat egy helyi elérési útra.
* Csatlakoztatás: Csatlakoztassa az adatfájlokat egy csatlakoztatási ponthoz. A csatlakoztatás csak Linux-alapú számítási, beleértve az Azure Machine Learning notebook virtuális gép és az Azure Machine Learning Compute.

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

A címkézett adatkészleteket a Torchvision adatkészletbe is betöltheti `azureml-contrib-dataset` a [to_torchvision()](https://docs.microsoft.com/python/api/azureml-contrib-dataset/azureml.contrib.dataset.tabulardataset?view=azure-ml-py#to-torchvision--) metódussal. A módszer használatához telepítenie kell a [PyTorch-ot.](https://pytorch.org/) 

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

## <a name="next-steps"></a>További lépések

* Tekintse meg az [adatkészlet címkék et notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb) teljes betanítási minta.
