---
title: Minták és útmutatók a az adatelemző virtuális gép – Azure |} A Microsoft Docs
description: Minták és útmutatók a Data Science virtuális gép esetében.
keywords: adatelemzési eszközök, adatelemző virtuális gép, eszközök adatelemzéshez, linux adatelemzés
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: gokuma
ms.openlocfilehash: a1f15b805d2f27152d9ba85608ce0dc1d1aac21e
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47392568"
---
# <a name="samples-on-the-data-science-virtual-machines-dsvm"></a>Példák a az adatelemző virtuális gépek (DSVM)

A Dsvm mintakód mindkét átfogó készletét tartalmazza a Jupyter notebookok csakúgy, mint például a Python és az r nyelvű parancsprogramok a képernyő    
> [!NOTE]
> Tekintse meg [hozzáférés Jupyter](#access-jupyter) szakaszban olvashat a Jupyter notebookok futó a dsvm-hez.

## <a name="quick-reference-of-samples"></a>A minták rövid összefoglalása
| A minták kategória | Leírás | Helyek |
| ------------- | ------------- | ------------- |
| **R** nyelv  | A minták **R** helyzetek, például az Azure-felhőbeli adatokhoz való csatlakoztatásának elmagyarázza tárolja, nyílt forráskódú R összehasonlítása és a Microsoft R & modellek modellezést, a Microsoft R Server vagy SQL Server. <br/> [Képernyőfelvétel](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| **Python** nyelv  | A minták **Python** helyzetek, például az Azure felhőalapú adattárak csatlakoztatása és használatának elmagyarázza **Azure Machine Learning**.  <br/> [Képernyőfelvétel](#python-language) | <br/>`~notebooks` <br/><br/>|
| **Julia** nyelv  | A minta **Julia** , amely részletesen Plotting a Julia, a mély tanulási Julia, C és Python megismernie Julia stb. <br/> [Képernyőfelvétel](#julia-language) |<br/> **Windows**:<br/> `~notebooks/Julia_notebooks`<br/><br/> **Linux**:<br/> `~notebooks/julia`<br/><br/> |
| **Az Azure Machine Learning** AzureML  | Gépi Tanulási és deep learning-modellek létrehozása **az Azure Machine Learning** szolgáltatás, és bárhol üzembe modelleket. Használja ki szolgáltatásokat, például a gépi tanulás automatikus finomhangolási, modellkezelés, intelligens hyper paraméter képzési terjesztése. <br/> [Képernyőfelvétel](#azureml) | <br/>`~notebooks/AzureML`<br/> <br/>|
| **PyTorch** notebookok  | Deep Learning felhasználásával minták **PyTorch** Neurális hálózatok alapján. Nincsenek speciális forgatókönyvek és a kezdő notebookok különböző.  <br/> [Képernyőfelvétel](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| **TensorFlow**  | Több különböző Neurális hálózat-minták és eljárások használatával implementált a **TensorFlow** keretrendszer. <br/> [Képernyőfelvétel](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| **CNTK** <br/> (A Microsoft Cognitive Toolkit)  | A deep learning-mintákat a Microsoft Cognitive Toolkit csapata által közzétett.  <br/> [Képernyőfelvétel](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> **Linux**:<br/> `~notebooks/CNTK`<br/> <br/>|
| **Caffe2** | Deep Learning felhasználásával minták **caffe2** Neurális hálózatok alapján. Nincsenek, a lemezkép előzetes feldolgozás adatkészlet létrehozását, regressziós, és használatával előre betanított modelleket, beleértve a példákat és ismerje meg a felhasználók caffe2 és hogyan használható hatékonyan, tervezett több notebookok. <br/> [Képernyőfelvétel](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| **H2O**   | Python-alapú minták használatával **H2O** a való életből vett forgatókönyv számos olyan problémákat. <br/> [Képernyőfelvétel](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| **Könnyen használható** nyelv  | Kész üzletifolyamat-funkciók és a Spark képességeire minta **MLlib** keresztül eszközkészlet **pySpark** és **MMLSpark – Microsoft Machine Learning, az Apache Spark** a **Apache Spark-2.x**.  <br/> [Képernyőfelvétel](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| **XGBoost** | Standard szintű Machine Learning-minták az **XGBoost** például a besorolás, regressziós stb. <br/> [Képernyőfelvétel](#xgboost) | <br/>**Windows**:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Hozzáférés a Jupyter 

Jupyter kattintva elérheti a `Jupyter` ikonjára az asztal vagy alkalmazás menü. Is elérhető Jupyter távolról egy webböngészőből a DSVM Linux kiadásában funkcionáló **`https://<Full Domain Name or IP Address of the DSVM>:8000`** Ubuntu rendszeren.

Tekintse meg a kivétel hozzáadása és Jupyter-hozzáférés engedélyezése a böngészőben képernyőképet.


![A Jupyter kivétel engedélyezése](./media/ubuntu-jupyter-exception.png)


Jelentkezzen be a bejelentkezési jelszót a dsvm-hez.
<br/>

**Jupyter-kezdőlap**
<br/>![Jupyter-kezdőlap](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>Az R nyelv 
<br/>![R-minták](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Python nyelven
<br/>![Példák Python programnyelvhez](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Julia nyelvhez 
<br/>![Julia minták](./media/julia-samples.png)<br/>

## <a name="azureml"></a>AzureML 
<br/>![AzurekML minták](./media/azureml-samples.png)<br/>

## <a name="pytorch"></a>PyTorch
<br/>![PyTorch minták](./media/pytorch-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![TensorFlow-minták](./media/tensorflow-samples.png)<br/>


## <a name="cntk"></a>CNTK 
<br/>![CNTK-minták](./media/cntk-samples.png)<br/>


## <a name="caffe2"></a>Caffe2 
<br/>![caffe2 minták](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![H2O minták](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>Könnyen használható 
<br/>![Könnyen használható minták](./media/sparkml-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![XGBoost minták](./media/xgboost-samples.png)<br/>

