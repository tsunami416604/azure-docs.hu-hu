---
title: Minták &-forgatókönyvek
titleSuffix: Azure Data Science Virtual Machine
description: Ezekkel a mintákkal és útmutatókkal megismerheti, hogyan kezelheti a gyakori feladatokat és forgatókönyveket a Data Science Virtual Machine.
keywords: adatelemzési eszközök, adatelemző virtuális gép, eszközök adatelemzéshez, linux adatelemzés
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: ad58adb85077a27bce65595738ffdbd92bace9bd
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802453"
---
# <a name="samples-on-azure-data-science-virtual-machines"></a>Minták az Azure adatelemzési Virtual Machines

Az Azure adatelemzési Virtual Machines (Dsvm) tartalmaz egy átfogó mintakód-készletet. Ezek közé tartoznak a Jupyter notebookok és parancsfájlok, például a Python és az R.
> [!NOTE]
> A Jupyter-jegyzetfüzetek adatelemzési virtuális gépeken való futtatásával kapcsolatos további információkért tekintse meg a [hozzáférés Jupyter](#access-jupyter) című szakaszt.

## <a name="prerequisites"></a>Előfeltételek

A minták futtatásához Data Science Virtual Machine kell kiépíteni. Tekintse meg a [Windows](./provision-vm.md) és az [Ubuntu](./dsvm-ubuntu-intro.md)gyors útmutatóit.

## <a name="available-samples"></a>Elérhető minták
| A minták kategória | Leírás | Helyek |
| ------------- | ------------- | ------------- |
| Az R nyelv  | A minták olyan forgatókönyveket mutatnak be, mint például az Azure-alapú felhőalapú adattárakkal való kapcsolódás, valamint a nyílt forráskódú R-és Microsoft Machine Learning Server összevetése. Azt is ismertetik, hogyan működővé tenni modelleket Microsoft Machine Learning Server és SQL Server. <br/> [Az R nyelv](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Python nyelven  | A példák olyan forgatókönyveket mutatnak be, mint például az Azure-alapú felhőalapú adattárakkal való kapcsolódás, valamint a Azure Machine Learning használata.  <br/> [Python nyelven](#python-language) | <br/>`~notebooks` <br/><br/>|
| Julia nyelvhez  | Részletes leírást nyújt a Julia-ben való ábrázolásról és a mély tanulásról. Azt is ismerteti, hogyan hívható meg a C és a Python a Julia-től. <br/> [Julia nyelvhez](#julia-language) |<br/> Windows:<br/> `~notebooks/Julia_notebooks`<br/><br/> Linux:<br/> `~notebooks/julia`<br/><br/> |
| Azure Machine Learning  | Bemutatja, hogyan hozhat létre gépi tanulási és részletes tanulási modelleket Machine Learningokkal. Bárhol modellek üzembe helyezése. Használjon automatizált gépi tanulási és intelligens hiperparaméter finomhangolása. Modellkezelési és elosztott képzési is használható. <br/> [Machine Learning](#azure-machine-learning) | <br/>`~notebooks/AzureML`<br/> <br/>|
| PyTorch notebookok  | PyTorch-alapú neurális hálózatokat használó mélyreható tanulási minták. Notebookok tartomány kezdő speciális forgatókönyvekig.  <br/> [PyTorch notebookok](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| TensorFlow  |  Különféle neurális hálózati minták és technikák a TensorFlow-keretrendszer használatával. <br/> [TensorFlow](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| Microsoft Cognitive Toolkit <br/>   | A Cognitive Toolkit csapat által a Microsoftnál közzétett mélyreható tanulási minták.  <br/> [A cognitive Toolkit](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> Linux:<br/> `~notebooks/CNTK`<br/> <br/>|
| Caffe2 | Caffe2-alapú neurális hálózatokat használó mélyreható tanulási minták. Több jegyzetfüzet is megismerkedjen a felhasználókkal a Caffe2, és hogyan használhatja azt hatékonyan. Ilyenek például a képek előfeldolgozása és az adatkészlet létrehozása. Emellett a regressziót és az előképzetlen modellek használatát is tartalmazzák. <br/> [Caffe2](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| H2O   | Python-alapú minták, amelyek a H2O-t használják a valós problémás forgatókönyvekhez. <br/> [H2O](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| Könnyen használható nyelv  | Az Apache Spark MLLib Toolkit funkcióit használó minták a pySpark és a MMLSpark használatával: A Microsoft Machine Learning Apache Spark Apache Spark 2. x.  <br/> [Könnyen használható nyelv](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| XGBoost | Szabványos gépi tanulási minták a XGBoost olyan forgatókönyvekhez, mint például a besorolás és a regresszió. <br/> [XGBoost](#xgboost) | <br/>Windows:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Hozzáférés a Jupyter 

A Jupyter eléréséhez válassza a **Jupyter** ikont az asztal vagy az alkalmazás menüjében. A Jupyter-t egy DSVM linuxos kiadásán is elérheti. Ha távolról szeretne hozzáférni egy webböngészőből, lépjen a `https://<Full Domain Name or IP Address of the DSVM>:8000` Ubuntura.

A következő útmutatást követve adhat hozzá kivételeket, és elérhetővé teheti a Jupyter-hozzáférést böngészőn keresztül:


![A Jupyter kivétel engedélyezése](./media/ubuntu-jupyter-exception.png)


Jelentkezzen be ugyanazzal a jelszóval, amelyet a Data Science Virtual Machineba való bejelentkezéshez használ.
<br/>

**Jupyter-kezdőlap**
<br/>![Jupyter-kezdőlap](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>Az R nyelv 
<br/>![R-minták](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Python nyelven
<br/>![Python-példák](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Julia nyelvhez 
<br/>![Julia minták](./media/julia-samples.png)<br/>

## <a name="azure-machine-learning"></a>Azure Machine Learning 
<br/>![Azure Machine Learning minták](./media/azureml-samples.png)<br/>

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

