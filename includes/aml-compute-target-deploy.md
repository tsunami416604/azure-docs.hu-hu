---
title: fájl belefoglalása
description: fájl belefoglalása
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 11/02/2020
ms.openlocfilehash: 269242e61b1f20221ddb3ff3d251bf9cd5c7108a
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322276"
---
A modell üzemeltetéséhez használt számítási cél a telepített végpont költségeit és rendelkezésre állását is befolyásolja. A táblázat segítségével kiválaszthatja a megfelelő számítási célt.

| Számítási cél | Alkalmazási cél | GPU-támogatás | FPGA-támogatás | Leírás |
| ----- | ----- | ----- | ----- | ----- |
| [Helyi &nbsp; webszolgáltatás &nbsp;](../articles/machine-learning/how-to-deploy-local-container-notebook-vm.md) | Tesztelés/hibakeresés | &nbsp; | &nbsp; | Korlátozott teszteléshez és hibaelhárításhoz használható. A hardveres gyorsítás a helyi rendszer könyvtárainak használatából függ.
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/how-to-deploy-azure-kubernetes-service.md) | Valós idejű következtetés |  [Igen](../articles/machine-learning/how-to-deploy-inferencing-gpus.md) (webszolgáltatás üzembe helyezése) | [Igen](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |Nagy léptékű üzembe helyezések esetén használható. Gyors válaszidőt és automatikus skálázást biztosít a telepített szolgáltatás számára. A fürt automatikus skálázása nem támogatott a Azure Machine Learning SDK-n keresztül. Az AK-fürt csomópontjainak módosításához használja a Azure Portalban az AK-fürt felhasználói felületét. A tervező számára az egyazon lehetőség áll rendelkezésre. |
| [Azure Container Instances](../articles/machine-learning/how-to-deploy-azure-container-instance.md) | Tesztelés vagy fejlesztés | &nbsp;  | &nbsp; | Alacsony léptékű CPU-alapú számítási feladatokhoz használható, amelyek kevesebb mint 48 GB RAM memóriát igényelnek. |
| [Az Azure Machine Learning számítási fürtjei](../articles/machine-learning/how-to-use-parallel-run-step.md) | Batch- &nbsp; következtetés | [Igen](../articles/machine-learning/how-to-use-parallel-run-step.md) (gépi tanulási folyamat) | &nbsp;  | Kötegelt pontozás futtatása kiszolgáló nélküli számítási feladatokhoz. A a normál és az alacsony prioritású virtuális gépeket támogatja. |

> [!NOTE]
> Bár a számítási célok, például a helyi, Azure Machine Learning számítások, és a Azure Machine Learning számítási fürtök támogatják a GPU-t a betanításhoz és kísérletezéshez, GPU-t használ, _Ha webszolgáltatásként való üzembe helyezés esetén_ csak az AK-ban támogatott.
>
> _Ha a Machine learning-folyamattal való pontozást_ csak Azure Machine learning számításokban támogatja, GPU-t használ a következtetéshez.

> [!NOTE]
> * A Container instances csak az 1 GB-nál kisebb méretű kisméretű modellek esetében alkalmas.
> * Használjon egycsomópontos AK-fürtöket nagyobb modellek fejlesztéséhez és teszteléséhez.