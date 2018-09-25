---
title: A tároló rendszerképét az Azure ML-modellek üzembe helyezéséhez használt testreszabása |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan szabhatja testre a egy tárolórendszerképet az Azure Machine Learning-modellek
services: machine-learning
author: tedway
ms.author: tedway
manager: mwinkle
ms.reviewer: mldocs, raymondl
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 3/26/2018
ROBOTS: NOINDEX
ms.openlocfilehash: d4cfb7067510ec06df3319035dee5e2195cb2f9d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46997539"
---
# <a name="customize-the-container-image-used-for-azure-ml-models"></a>A tároló rendszerképét használja az Azure ML-modellek testreszabása

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 



Ez a cikk egy tárolórendszerképet az Azure Machine Learning-modellek testreszabásának módját ismerteti.  Az Azure ML Workbench tárolók használja a machine learning-modellek üzembe helyezéséhez. A modellek üzembe van helyezve, azok függőségeivel együtt, és az Azure ML összeállít egy képet a modellt, a függőségeket és társított fájlokat.

## <a name="how-to-customize-the-docker-image"></a>A Docker-rendszerkép testreszabása
Testre szabhatja a Docker-rendszerkép üzembe helyező Azure ML használatával:

1. A `dependencies.yml` fájl:, amelyek telepíthető a függőségek kezelése [PyPi]( https://pypi.python.org/pypi), használhatja a `conda_dependencies.yml` a Workbench-projekt fájlt, vagy létrehozhatja a sajátját. Ez az az ajánlott módszer Python pip telepíthető függőségek telepítéséhez.

   Példa CLI-parancsot:
   ```azurecli
   az ml image create -n <my Image Name> --manifest-id <my Manifest ID> -c amlconfig\conda_dependencies.yml
   ```

   Mintafájl conda_dependencies: 
   ```yaml
   name: project_environment
   dependencies:
      - python=3.5.2
      - scikit-learn
      - ipykernel=4.6.1
      
      - pip:
        - azure-ml-api-sdk==0.1.0a11
        - matplotlib
   ```
        
2. A Docker lépések fájlt: Ezzel a beállítással testre szabni a telepített lemezképhez függőségeit, amelyek nem telepíthető a PyPi telepítésével. 

   A fájl tartalmaznia kell egy docker-fájlban hasonló a Docker telepítési lépéseket. A fájl használata engedélyezett a következő parancsokat: 

    FUTTATÁS ENV, ARG, FELIRAT, ELÉRHETŐVÉ

   Példa CLI-parancsot:
   ```azurecli
   az ml image create -n <my Image Name> --manifest-id <my Manifest ID> --docker-file <myDockerStepsFileName> 
   ```

   Kép, a jegyzékfájlban és a szolgáltatás a parancsok elfogadják a docker-fájl jelző.

   A példában a Docker lépéseket fájl:
   ```docker
   # Install tools required to build the project
   RUN apt-get update && apt-get install -y git libxml2-dev
   # Install library dependencies
   RUN dep ensure -vendor-only
   ```

> [!NOTE]
> Az alap rendszerképet az Azure ML-tárolókhoz az Ubuntu, és nem módosítható. Ha megad egy eltérő alaprendszerképet, akkor figyelmen kívül.

## <a name="next-steps"></a>További lépések
Most, hogy testre szabta a tárolórendszerképet, telepíthet egy fürtön nagy léptékű használatra.  A webszolgáltatás üzembe egy fürtöt a beállítás a részletekért lásd: [modell Felügyeletkonfigurálási](deployment-setup-configuration.md). 
