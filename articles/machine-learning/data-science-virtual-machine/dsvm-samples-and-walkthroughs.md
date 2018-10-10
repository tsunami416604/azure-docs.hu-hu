---
title: Minták és útmutatók az adatelemző virtuális gépek – Azure |} A Microsoft Docs
description: Minták és útmutatók az adatelemző virtuális gépek.
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
ms.openlocfilehash: 74d5893a377fb67dbec7b185525e74ac248deede
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902756"
---
# <a name="samples-on-data-science-virtual-machines"></a>Az adatelemző virtuális gépek minták

Az Azure Data Science Virtual Machines mintakód átfogó készletét tartalmazza. A mintakód neve a Jupyter notebookok és parancsfájlok nyelveken, például a Python és az r segítségével 
> [!NOTE]
> A Jupyter notebookok futtatása a data science virtuális gépeken további információkért lásd: a [hozzáférés Jupyter](#access-jupyter) szakaszban.

## <a name="quick-reference-of-samples"></a>A minták rövid összefoglalása
| A minták kategória | Leírás | Helyek |
| ------------- | ------------- | ------------- |
| Az R nyelv  | Minták az R azt ismertetik, hogyan csatlakozhat az Azure felhőalapú adattárak – egyéb felhasználási helyzetek. Akkor is bemutatják, hogyan hasonlítsa össze a nyílt forráskódú R és a Microsoft R. És azok azt ismertetik, hogyan modelleket a Microsoft R Server vagy SQL Server. <br/> [Az R nyelv](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Python nyelven  | Példák Python azt ismertetik, hogyan csatlakozhat az Azure felhőalapú adattárak, és működnek az Azure Machine Learning – egyéb felhasználási helyzetek.  <br/> [Python nyelven](#python-language) | <br/>`~notebooks` <br/><br/>|
| Julia nyelvhez  | Julia, amely részletesen küldik az ábrázolást és deep learning-Julia a mintát. Azt is bemutatja hívó C és Python, Julia. <br/> [Julia nyelvhez](#julia-language) |<br/> Windows:<br/> `~notebooks/Julia_notebooks`<br/><br/> Linux:<br/> `~notebooks/julia`<br/><br/> |
| Azure Machine Learning  | Gépi tanulási és deep learning-modellek a Machine Learning hozhat létre. Bárhol modellek üzembe helyezése. Használjon automatizált gépi tanulási és intelligens hiperparaméter finomhangolása. Modellkezelési és elosztott képzési is használható. <br/> [Machine Learning](#azureml) | <br/>`~notebooks/AzureML`<br/> <br/>|
| PyTorch notebookok  | A deep learning-PyTorch-alapú Neurális hálózatokat használó mintákat. Notebookok tartomány kezdő speciális forgatókönyvekig.  <br/> [PyTorch notebookok](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| TensorFlow  |  Különböző Neurális hálózat minták és technikák, a TensorFlow-keretrendszer használatával. <br/> [TensorFlow](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| Microsoft Cognitive Toolkit <br/>   | A deep learning-mintákat a Microsoft Cognitive Toolkit csapata által közzétett.  <br/> [A cognitive Toolkit](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> Linux:<br/> `~notebooks/CNTK`<br/> <br/>|
| Caffe2 | A deep learning-caffe2-alapú Neurális hálózatokat használó mintákat. Több notebookok megismerni a felhasználók caffe2 és a hatékony használata. Ilyenek például a lemezkép előzetes feldolgozása és adatkészlet létrehozását. Ezek tartalmazzák a regressziós és előre betanított modellek használata. <br/> [Caffe2](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| H2O   | Python-alapú minták, a valós életből vett helyzet problémákat H2O használó. <br/> [H2O](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| Könnyen használható nyelv  | A minták azt használja a Spark MLLib eszközkészlet pySpark és MMLSpark – Apache Spark on Apache Spark for Microsoft Machine Learning funkcióit 2.x.  <br/> [Könnyen használható nyelv](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| XGBoost | Standard szintű machine learning-minták az XGBoost besorolási és regressziós hasonló forgatókönyvek esetén. <br/> [XGBoost](#xgboost) | <br/>Windows:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Hozzáférés a Jupyter 

Jupyter eléréséhez, válassza a `Jupyter` ikonjára az asztal vagy alkalmazás menü. Jupyter Linux kiadásaiban az adatelemző virtuális gépek is elérheti. Érheti el távolról egy webes böngésző funkcionáló `https://<Full Domain Name or IP Address of the DSVM>:8000` Ubuntu rendszeren.

Vegyen fel kivételeket és Jupyter eléréséhez egy böngészőben keresztül elérhetővé: az alábbi képernyőfelvételen látható.


![A Jupyter kivétel engedélyezése](./media/ubuntu-jupyter-exception.png)


Jelentkezzen be ugyanazt a jelszót, mint a bejelentkezési adatok adatelemző virtuális gépeket.
<br/>

**Jupyter-kezdőlap**
<br/>![Jupyter-kezdőlap](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>Az R nyelv 
<br/>![R-minták](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Python nyelven
<br/>![Python-példák](./media/python-language-samples.png)<br/>

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

