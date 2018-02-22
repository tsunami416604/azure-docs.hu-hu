---
title: "A részletes tanulási és AI keretrendszerek - Azure |} Microsoft Docs"
description: "Mély tanulás és mesterségesintelligencia-keretrendszerek"
keywords: "adatok tudományos eszközök, adatok tudományos virtuális gép, adattudomány, linux adattudomány eszközei"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: gokuma;bradsev
ms.openlocfilehash: 89a8cde0dbb7fe7ccfbb6d068411aaf5488c532f
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2018
---
# <a name="deep-learning-and-ai-frameworks"></a>Mély tanulás és mesterségesintelligencia-keretrendszerek
A [adatok tudományos virtuális gép](http://aka.ms/dsvm) (DSVM) és a [mély tanulási VM](http://aka.ms/dsvm/deeplearning) mély tanulási keretrendszerek mesterséges intelligencia (AI) alkalmazások a prediktív elemzés érdekében számos támogat, és kognitív képességei többek között a lemezkép és a nyelvi ismertetése. 

Az alábbiak a tanulási keretrendszerek a DSVM elérhető összes mély.

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

|    |           |
| ------------- | ------------- |
| Mi ez?   | A részletes oktatási keretrendszer      |
| Támogatott DSVM kiadás      | Windows, Linux     |
| Hogyan van azt konfigurált / a DSVM telepítve?  | A Microsoft kognitív Toolkit (CNTK) telepítve van a Python 2.7 esetén a _legfelső szintű_ környezet, valamint a Python 3.5, a a _py35_ környezetben.   |
| Minták mutató hivatkozások      | A minta Jupyter notebookok tartoznak.     |
| A DSVM a kapcsolódó eszközök      | Keras      |
| Hogyan használja az / futtatni?    | Nyissa meg a Jupyter, majd keresse meg a CNTK mappát  |

## <a name="tensorflow"></a>TensorFlow

|    |           |
| ------------- | ------------- |
| Mi ez?   | A részletes oktatási keretrendszer      |
| Támogatott DSVM kiadás      | Windows, Linux     |
| Hogyan van azt konfigurált / a DSVM telepítve?  | Linux, TensorFlow telepítve van a Python 2.7-es (_legfelső szintű_), valamint a Python 3.5 (_py35_) környezetben. A Windows, a Tensorflow telepítve van a Python 3.5 (_py35_) környezetben.  |
| Minták mutató hivatkozások      | A minta Jupyter notebookok tartoznak.     |
| A DSVM a kapcsolódó eszközök      | Keras      |
| Hogyan használja az / futtatni?    | Nyissa meg a Jupyter, majd keresse meg a TensorFlow mappát.  |

## <a name="keras"></a>Keras

|    |           |
| ------------- | ------------- |
| Mi ez?   | A részletes oktatási keretrendszer      |
| Támogatott DSVM kiadás      | Windows, Linux     |
| Hogyan van azt konfigurált / a DSVM telepítve?  | Keras telepítve van a Python 2.7-es (_legfelső szintű_), valamint a Python 3.5 (_py35_) környezetben.   |
| Minták mutató hivatkozások      | https://github.com/fchollet/keras/tree/master/examples      |
| A DSVM a kapcsolódó eszközök      | Microsoft kognitív eszközkészlet, TensorFlow, Theano      |
| Hogyan használja az / futtatni?    | Töltse le a minták a Githubon helyről, másolja azt a könyvtárat ~/notebooks, és nyissa meg a Jupyter   |




## <a name="caffe"></a>Caffe

|    |           |
| ------------- | ------------- |
| Mi ez?   | A részletes oktatási keretrendszer      |
| Támogatott DSVM kiadás      | Ubuntu     |
| Hogyan van azt konfigurált / a DSVM telepítve?  | Caffe telepítve van-e `/opt/caffe`.    |
| Minták mutató hivatkozások      | Minták szerepelnek `/opt/caffe/examples`.      |
| A DSVM a kapcsolódó eszközök      | Caffe2      |
### <a name="how-to-use--run-it"></a>Hogyan használja az / futtatni?  

Jelentkezzen be a virtuális Gépet, majd indítsa el egy új terminál, és írja be a X2Go segítségével

```
cd /opt/caffe/examples
jupyter notebook
```

Egy új böngészőablakban nyílik meg a minta notebookok.

## <a name="caffe2"></a>Caffe2

|    |           |
| ------------- | ------------- |
| Mi ez?   | A részletes oktatási keretrendszer      |
| Támogatott DSVM kiadás      | Ubuntu     |
| Hogyan van azt konfigurált / a DSVM telepítve?  | Caffe2 telepítve van-e `/opt/caffe2`. Python 2.7 is érhető el (_legfelső szintű_) conda környezetben.     |
| Minták mutató hivatkozások      | A minta Jupyter notebookok tartoznak.     |
| A DSVM a kapcsolódó eszközök      | Caffe      |
| Hogyan használja az / futtatni?    | Nyissa meg a Jupyter, majd keresse meg a minta notebookok található Caffe2 könyvtárát. Néhány notebookok igényelnek a Caffe2 legfelső szintű kell beállítani, a Python-kódban; Adja meg a /opt/caffe2.   |


## <a name="chainer"></a>Chainer

|    |           |
| ------------- | ------------- |
| Mi ez?   | A részletes oktatási keretrendszer      |
| Támogatott DSVM kiadás      | Windows, Linux     |
| Hogyan van azt konfigurált / a DSVM telepítve?  | Chainer telepítve van a Python 2.7-es (_legfelső szintű_), valamint a Python 3.5 (_py35_) környezetben. ChainerRL és ChainerCV is települnek.   |
| Minták mutató hivatkozások      | A minta Jupyter notebookok tartoznak.      |
| A DSVM a kapcsolódó eszközök      | Caffe      |

### <a name="how-to-use--run-it"></a>Hogyan használja az / futtatni?  

A terminálon aktiválja a Python kívánt verziót (_legfelső szintű_ vagy _py35_)- ben futtassa _python_, majd importálja a Chainer. A Jupyter válassza ki a Python 2.7 vagy 3.5-ös kernel, majd importálja a Chainer.


## <a name="deep-water"></a>A részletes vízjel

|    |           |
| ------------- | ------------- |
| Mi ez?   | A részletes tanulási keretrendszere, amely H2O      |
| Támogatott DSVM kiadás      | Ubuntu     |
| Hogyan van azt konfigurált / a DSVM telepítve?  | A részletes vízjel telepítve van-e `/dsvm/tools/deep_water`.   |
| Minták mutató hivatkozások      | Minták a mély vízjel kiszolgálón keresztül érhetők el.      |
| A DSVM a kapcsolódó eszközök      | H2O, készült vízjel      |

### <a name="how-to-use--run-it"></a>Hogyan használja az / futtatni?  

Csatlakoztassa a virtuális Gépet X2Go használatával. A terminálon indítsa el a részletes vízjel kiszolgálót:

    java -jar /dsvm/tools/deep_water/h2o.jar

Majd nyisson meg egy böngészőt, és kapcsolódjon `http://localhost:54321`.



## <a name="mxnet"></a>MXNet

|    |           |
| ------------- | ------------- |
| Mi ez?   | A részletes oktatási keretrendszer      |
| Támogatott DSVM kiadás      | Windows, Linux     |
| Hogyan van azt konfigurált / a DSVM telepítve?  | MXNet telepítve van-e `C:\dsvm\tools\mxnet` Windows és `/dsvm/tools/mxnet` Linux rendszeren. Python 2.7 Python kötések vannak telepítve (_legfelső szintű_), valamint Python 3.5 (_py35_) környezetben. R kötések is telepítve van.   |
| Minták mutató hivatkozások      | A minta Jupyter notebookok tartoznak.    |
| A DSVM a kapcsolódó eszközök      | Keras      |
| Hogyan használja az / futtatni?    | Nyissa meg a Jupyter, majd keresse meg a mxnet mappát  |

## <a name="nvidia-digits"></a>NVIDIA SZÁMJEGY

|    |           |
| ------------- | ------------- |
| Mi ez?   | A mélyhivatkozással NVIDIA rendszer learning a gyors a mély tanulási modellek betanítása      |
| Támogatott DSVM kiadás      | Ubuntu     |
| Hogyan van azt konfigurált / a DSVM telepítve?  | SZÁMJEGYEK telepítve van-e `/dsvm/tools/DIGITS` érhető el, és egy szolgáltatás nevű _számjegyek_.   |
### <a name="how-to-use--run-it"></a>Hogyan használja az / futtatni?  

Jelentkezzen be a virtuális Géphez a X2Go. A terminálon indítsa el a szolgáltatást:

    sudo systemctl start digits

A szolgáltatás elindításához körülbelül egy percig tart. Indítson el egy webböngészőt, és keresse meg `http://localhost:5000`.



## <a name="nvdia-smi"></a>nvdia-smi

|    |           |
| ------------- | ------------- |
| Mi ez?   | NVIDIA eszköz GPU tevékenység lekérdezése      |
| Támogatott DSVM kiadás      | Windows, Linux     |
| Hogyan van azt konfigurált / a DSVM telepítve?  | _NVIDIA-smi_ a rendszer útvonalon érhető el.   |
| Hogyan használja az / futtatni? | Indítsa el a parancssor (Windows) vagy a Terminálszolgáltatások (a Linux), majd futtassa _nvidia-smi_.



## <a name="theano"></a>Theano

|    |           |
| ------------- | ------------- |
| Mi ez?   | A részletes oktatási keretrendszer      |
| Támogatott DSVM kiadás      | Ubuntu     |
| Hogyan van azt konfigurált / a DSVM telepítve?  | Theano telepítve van a Python 2.7-es (_legfelső szintű_), valamint a Python 3.5 (_py35_) környezetben.   |
| A DSVM a kapcsolódó eszközök      | Keras      |
| Hogyan használja az / futtatni?    | A terminálon a Python-verzió (legfelső szintű vagy py35), futtassa a python, majd importálása theano aktiválása. A Jupyter válassza ki a Python 2.7 vagy 3.5-ös kernel, majd importálja a theano.  |



## <a name="torch"></a>Torch

|    |           |
| ------------- | ------------- |
| Mi ez?   | A részletes oktatási keretrendszer      |
| Támogatott DSVM kiadás      | Ubuntu     |
| Hogyan van azt konfigurált / a DSVM telepítve?  | Torch telepítve van-e `/dsvm/tools/torch`. PyTorch telepítve van a Python 2.7-es (_legfelső szintű_), valamint a Python 3.5 (_py35_) környezetben.   |
| Minták mutató hivatkozások      | Torch minták találhatók `/dsvm/samples/torch`. PyTorch minták találhatók `/dsvm/samples/pytorch`.      |


## <a name="pytorch"></a>PyTorch

|    |           |
| ------------- | ------------- |
| Mi ez?   | A részletes oktatási keretrendszer      |
| Támogatott DSVM kiadás      | Linux     |
| Hogyan van azt konfigurált / a DSVM telepítve?  | PyTorch telepítve van-e a Python 3.5-ös (_py35_) környezetben.   |
| Minták mutató hivatkozások      | Minta Jupyter notebookok tartoznak, és minták /dsvm/samples/pytorch is található.      |
| A DSVM a kapcsolódó eszközök      | Torch      |

### <a name="how-to-use--run-it"></a>Hogyan használja az / futtatni?  

Futtassa a terminálon _python_, majd importálja a torch. A Jupyter válassza ki a Python 3.5 kernel, majd importálja a torch.


## <a name="mxnet-model-server"></a>MXNet Model Server

|    |           |
| ------------- | ------------- |
| Mi ez?   | A kiszolgáló HTTP-végpontokról MXNet és ONNX modellek létrehozásához      |
| Támogatott DSVM kiadás      | Linux     |
| Hogyan van azt konfigurált / a DSVM telepítve?  | _mxnet-modell-kiszolgáló_ érhető el a terminálon.   |
| Minták mutató hivatkozások      | Naprakész mintákat keressen az [MXNet modell kiszolgáló lap](https://github.com/awslabs/mxnet-model-server).    |
| A DSVM a kapcsolódó eszközök      | MXNet      |

## <a name="tensorflow-serving"></a>TensorFlow szolgál

|    |           |
| ------------- | ------------- |
| Mi ez?   | A kiszolgáló TensorFlow modell inferencing futtatásához      |
| Támogatott DSVM kiadás      | Linux     |
| Hogyan van azt konfigurált / a DSVM telepítve?  | _tensorflow_model_server_ érhető el a terminálon.   |
| Minták mutató hivatkozások      | Minták érhetők el [online](https://www.tensorflow.org/serving/).      |
| A DSVM a kapcsolódó eszközök      | TensorFlow      |
