---
title: Kódminták és az adatok tudományos virtuálisgép - Azure forgatókönyvek |} Microsoft Docs
description: Kódminták és forgatókönyvek esetében az adatok tudományos virtuális gép.
keywords: adatok tudományos eszközök, adatok tudományos virtuális gép, adattudomány, linux adattudomány eszközei
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
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: 3e3ee232b6342601e44d728148d32e70e6f3f00b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31419976"
---
# <a name="samples-on-the-data-science-virtual-machines-dsvm"></a>Az adatok tudományos virtuális gépeken (DSVM) minták

A DSVMs belefoglalt teljesen működőképes kibővített minták Jupyter Notebookokból és az egyes nem a Jupyter alapuló formájában kapja. Jupyter kattintva végezheti el a `Jupyter` ikonjára az asztal vagy alkalmazás menü.  
> [!NOTE]
> Tekintse meg [hozzáférés Jupyter](#access-jupyter) szakaszt, hogy a DSVM a Jupyter notebookok.

## <a name="quick-reference-of-samples"></a>Gyors referenciaként a minták
| Kategória – minták | Leírás | Helyek |
| ------------- | ------------- | ------------- |
| **R** nyelv  | A minták **R** foglalja össze a helyzetekben, például az Azure-felhőbe adatok összekapcsolása tárolja, nyissa meg forrás R összehasonlításával és Microsoft R & végrehajtott modellek Microsoft R Server vagy SQL Server. <br/> [Képernyőfelvétel](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| **Python** nyelv  | A minták **Python** foglalja össze a helyzetekben, például az Azure felhőalapú adattároló összekapcsolása és az azokkal **Azure Machine Learning**.  <br/> [Képernyőfelvétel](#python-language) | <br/>`~notebooks` <br/><br/>|
| **Ágnes** nyelv  | A minta **Ágnes** , amely részletesen Plotting a Ágnes, mély Ágnes tanulás, C és Python előhívásának Ágnes stb. <br/> [Képernyőfelvétel](#julia-language) |<br/> **Windows**:<br/> `~notebooks/Julia_notebooks`<br/><br/>`~notebooks`<br/><br/> **Linux**:<br/> `~notebooks/julia`<br/><br/> |
| **CNTK** <br/> (A Microsoft kognitív eszközkészlet)  | A Microsoft kognitív eszközkészlet csapata által közzétett minták tanulási mély.  <br/> [Képernyőfelvétel](#cntk) | <br/>**Windows**:<br/> `~notebooks/CNTK/Tutorials`<br/><br/>`~/samples/CNTK-Samples-2-0/Examples`<br/><br/> **Linux**:<br/> `~notebooks/CNTK`<br/> <br/>|
| **MXNet** notebookok  | Részletes tanulási okhoz minták **MXNet** alapú Neurális hálózatokat. Nincsenek speciális forgatókönyvek kezdő kezdve notebookok különböző.  <br/> [Képernyőfelvétel](#mxnet) | <br/>`~notebooks/mxnet`<br/> <br/>|
| **Az Azure gépi tanulás** AzureML  | Interakció **Azure Machine Learning** Studio és a webszolgáltatás végpontok helyileg betanított modellek pontozási munkafolyamatokat felhőalapú történő létrehozását. <br/> [Képernyőfelvétel](#azureml) | <br/>`~notebooks/azureml`<br/> <br/>|
| **Caffe2** | Részletes tanulási okhoz minták **caffe2** alapú Neurális hálózatokat. Például a lemezkép előzetes feldolgozás adatkészlet létrehozását, regresszióját, és segítségével előre modellek betanítása, beleértve a példákat úgy tervezték, hogy a felhasználók ismerkedjen caffe2 és hatékonyan, használatával több jegyzetfüzetet van. <br/> [Képernyőfelvétel](#caffe2) | <br/>`~notebooks/caffe2`<br/><br/> |
| **H2O**   | Python-alapú minták okhoz **H2O** számos valós forgatókönyvvel problémák. <br/> [Képernyőfelvétel](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| **SparkML** nyelv  | A Spark az funkcióira és képességeire okhoz minta **MLlib** keresztül eszközkészlet **pySpark 2.0** a **Apache Spark 2.0**.  <br/> [Képernyőfelvétel](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/><br/> |
| **MMLSpark** nyelv  | Okhoz minták számos **MMLSpark - Microsoft Machine Learning az Apache Spark**, amely, amely számos mély tanulási és adatok tudományos eszközök a keretet **Apache Spark**. <br/> [Képernyőfelvétel](#sparkml) | <br/>`~notebooks/MMLSpark`<br/><br/> |
| **TensorFlow**  | Több különböző Neurális hálózat mintákat és technikák használatával megvalósított a **TensorFlow** keretrendszer. <br/> [Képernyőfelvétel](#tensorflow) | <br/>`~notebooks/tensorflow`<br/><br/> |
| **XGBoost** | Standard Machine Learning mintákkal **XGBoost** forgatókönyvek például a besorolás, regressziós stb. <br/> [Képernyőfelvétel](#xgboost) | <br/>`~samples/xgboost/demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Hozzáférés Jupyter 

Látogasson el Jupyter kezdőlapja a **`https://localhost:9999`** Windows vagy **`https://localhost:8000`** az Ubuntu.


### <a name="enabling-jupyter-access-from-browser"></a>A böngésző Jupyter hozzáférés engedélyezése

**Windows DSVM**

Futtatás **`Jupyter SetPassword`** az asztali parancsikonjára, és kövesse a jelenít meg a jelszó beállítása/reset a Jupyter, és indítsa el a Jupyter feldolgozni. 
<br/>![Jupyter kivétel engedélyezése](./media/jupyter-setpassword.png)<br/>
Van-e hozzáférési Jupyter kezdőlap után a Jupyter folyamat sikeresen elindult a virtuális gépen látogasson el **`https://localhost:9999`** a böngésző. Lásd: a kivétel hozzáadása és Jupyter hozzáférés engedélyezése a böngészőben képernyőképe
<br/>![Jupyter kivétel engedélyezése](./media/windows-jupyter-exception.png)<br/>
Jelentkezzen be az imént beállított új jelszót.
<br/>
**Linux DSVM**

Látogasson el a virtuális Gépet a Jupyter otthoni végezheti el **`https://localhost:8000`** a böngésző. Tekintse meg a kivétel hozzáadása és Jupyter hozzáférés engedélyezése a böngészőben képernyőképet.
<br/>![Jupyter kivétel engedélyezése](./media/ubuntu-jupyter-exception.png)<br/>
Jelentkezzen be ugyanazt a jelszót, mint a bejelentkezési adatait a DSVM számára.
<br/>

**Jupyter kezdőlapja**
<br/>![Jupyter kezdőlapja](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>R nyelv 
<br/>![R-minták](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Python nyelven
<br/>![Python-minták](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Ágnes nyelv 
<br/>![Ágnes minták](./media/julia-samples.png)<br/>

## <a name="cntk"></a>CNTK 
<br/>![CNTK minták](./media/cntk-samples2.png)<br/>
<br/>![CNTK minták](./media/cntk-samples.png)<br/>

## <a name="mxnet"></a>MXNet
<br/>![MXnet minták](./media/mxnet-samples.png)<br/>

## <a name="azureml"></a>AzureML 
<br/>![AzurekML minták](./media/azureml-samples.png)<br/>

## <a name="caffe2"></a>Caffe2 
<br/>![caffe2 minták](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![H2O minták](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![SparkML minták](./media/sparkml-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![TensorFlow minták](./media/tensorflow-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![XGBoost minták](./media/xgboost-samples.png)<br/>

