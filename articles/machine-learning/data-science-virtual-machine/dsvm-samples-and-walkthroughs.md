---
title: Mintaprogramok & ML forgatókönyvekkel
titleSuffix: Azure Data Science Virtual Machine
description: Ezeken a mintákon és forgatókönyveken keresztül megtudhatja, hogyan kezelhetők a gyakori feladatok és forgatókönyvek az adatelemzési virtuális gép.
keywords: adatelemzési eszközök, adatelemző virtuális gép, eszközök adatelemzéshez, linux adatelemzés
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: d064e940997d13e51be4fd38e9f678b778ce31f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900047"
---
# <a name="samples-on-azure-data-science-virtual-machines"></a>Minták az Azure Data Science virtuális gépeken

Az Azure Data Science virtuális gépek (DSVM-ek) egy átfogó mintakódkészletet tartalmaznak. Ezek a minták jupyter notebookok és parancsfájlok nyelveken, például a Python és az R.
> [!NOTE]
> A Jupyter-jegyzetfüzetek adatelemzési virtuális gépeken való futtatásáról az [Access Jupyter](#access-jupyter) című szakaszban talál további információt.

## <a name="prerequisites"></a>Előfeltételek

A minták futtatásához ki kell építeni egy adatelemzési virtuális gép. Tekintse meg a [Windows](./provision-vm.md) és az [Ubuntu](./dsvm-ubuntu-intro.md)rövid útmutatóit.

## <a name="available-samples"></a>Rendelkezésre álló minták
| Minták kategóriája | Leírás | Helyek |
| ------------- | ------------- | ------------- |
| R nyelv  | A minták bemutatják az olyan forgatókönyveket, mint például az Azure-alapú felhőalapú adattárakhoz való csatlakozás, valamint a nyílt forráskódú R és a Microsoft Machine Learning Server összehasonlítása. Azt is ismertetik, hogyan lehet a microsoft machine learning server és az SQL Server modelleket működőképessé tenni. <br/> [R nyelv](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Python nyelv  | A minták ismertetik az olyan forgatókönyveket, mint például az Azure-alapú felhőalapú adattárakhoz való csatlakozás és az Azure Machine Learning használatával való kapcsolat.  <br/> [Python nyelv](#python-language) | <br/>`~notebooks` <br/><br/>|
| Julia nyelv  | Részletes leírást ad a juliai ábrázolásról és mély tanulásról. Azt is elmagyarázza, hogyan hívja a C és python Julia. <br/> [Julia nyelv](#julia-language) |<br/> Windows:<br/> `~notebooks/Julia_notebooks`<br/><br/> Linux:<br/> `~notebooks/julia`<br/><br/> |
| Azure Machine Learning  | Bemutatja, hogyan hozhat létre gépi tanulási és mélytanulási modelleket a Machine Learning segítségével. Modellek üzembe helyezése bárhol. Automatikus gépi tanulás és intelligens hiperparaméter-hangolás használata. Is használja a modell kezelése és az elosztott képzés. <br/> [Machine Learning](#azure-machine-learning) | <br/>`~notebooks/AzureML`<br/> <br/>|
| PyTorch notebookok  | Deep-learning mintákat használó PyTorch-alapú neurális hálózatok. A jegyzetfüzetek a kezdőtől a haladó forgatókönyvig terjednek.  <br/> [PyTorch notebookok](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| TensorFlow  |  A TensorFlow keretrendszer használatával megvalósított különböző neurális hálózati minták és technikák. <br/> [TensorFlow](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| Microsoft Cognitive Toolkit <br/>   | A Cognitive Toolkit csapata által közzétett mélytanulási minták a Microsoftnál.  <br/> [Cognitive Toolkit](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> Linux:<br/> `~notebooks/CNTK`<br/> <br/>|
| Caffe2 | Caffe2 alapú neurális hálózatokat használó mélytanulási minták. Számos notebook ismeri a felhasználókcaffe2 és hogyan kell használni hatékonyan. Ilyenek például a lemezkép-előfeldolgozás és az adatkészletek létrehozása. Ezek közé tartozik a regresszió és hogyan kell használni az előre betanított modellek. <br/> [Caffe2](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| H2o   | Python-alapú minták, amelyek h2O valós probléma forgatókönyvek. <br/> [H2o](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| SparkML nyelv  | Az Apache Spark MLLib eszközkészlet funkcióit használó minták a pySpark és az MMLSpark segítségével: Microsoft Machine Learning apache sparkhoz az Apache Spark 2.x-en.  <br/> [SparkML nyelv](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| Xgboost | Szabványos gépi tanulási minták az XGBoost-ban olyan forgatókönyvekhez, mint a besorolás és a regresszió. <br/> [XGBoost](#xgboost) | <br/>Windows:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Hozzáférés Jupyter 

A Jupyter eléréséhez válassza a **Jupyter** ikont az asztal vagy az alkalmazás menüjében. A Jupyter egy DSVM Linux-kiadásában is elérhető. A webböngészőből távolról való `https://<Full Domain Name or IP Address of the DSVM>:8000` hozzáféréshez nyissa meg az Ubuntut.

Kivételek hozzáadásához és a Jupyter-hozzáférés böngészőn keresztüli elérhetővé válásához kövesse az alábbi útmutatást:


![Jupyter-kivétel engedélyezése](./media/ubuntu-jupyter-exception.png)


Jelentkezzen be ugyanazzal a jelszóval, amelyet az adatelemzési virtuális gépbe való bejelentkezéshez használ.
<br/>

**Jupyter otthon**
<br/>![Jupyter otthon](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>R nyelv 
<br/>![R-minták](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Python nyelv
<br/>![Python-példák](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Julia nyelv 
<br/>![Julia minták](./media/julia-samples.png)<br/>

## <a name="azure-machine-learning"></a>Azure Machine Learning 
<br/>![Azure Machine Learning-minták](./media/azureml-samples.png)<br/>

## <a name="pytorch"></a>PyTorch
<br/>![PyTorch-minták](./media/pytorch-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![TensorFlow-minták](./media/tensorflow-samples.png)<br/>


## <a name="cntk"></a>CNTK 
<br/>![CNTK-minták](./media/cntk-samples.png)<br/>


## <a name="caffe2"></a>Caffe2 
<br/>![caffe2 minták](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2o 
<br/>![H2O-minták](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![SparkML-minták](./media/sparkml-samples.png)<br/>

## <a name="xgboost"></a>Xgboost 
<br/>![XGBoost minták](./media/xgboost-samples.png)<br/>

