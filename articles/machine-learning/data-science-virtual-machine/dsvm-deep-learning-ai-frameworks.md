---
title: A részletes tanulási és AI keretrendszerek - Azure |} Microsoft Docs
description: Mély tanulás és mesterségesintelligencia-keretrendszerek
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
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: cd8579fe29282f3875ecfddf67a34444e3de9ef5
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="deep-learning-and-ai-frameworks"></a>Mély tanulás és mesterségesintelligencia-keretrendszerek
A [adatok tudományos virtuális gép](http://aka.ms/dsvm) (DSVM) és a [mély tanulási VM](http://aka.ms/dsvm/deeplearning) mély tanulási keretrendszerek mesterséges intelligencia (AI) alkalmazások a prediktív elemzés érdekében számos támogat, és kognitív képességei többek között a lemezkép és a nyelvi ismertetése. 

Az alábbiak a tanulási keretrendszerek a DSVM elérhető összes mély.

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

|    |           |
| ------------- | ------------- |
| Mi ez?   | A részletes oktatási keretrendszer      |
| Támogatott DSVM kiadás      | Windows, Linux     |
| Hogyan van azt konfigurált / a DSVM telepítve?  | A Microsoft kognitív Toolkit (CNTK) telepítve van a Python 3.5 [Linux és a Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) és a Python 3.6 [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition).   |
| Minták mutató hivatkozások      | A minta Jupyter notebookok tartoznak.     |
| A DSVM a kapcsolódó eszközök      | Keras      |
| Hogyan használja az / futtatni?    | * A terminálon egy: a megfelelő környezet aktiválja, majd futtassa a Python. <br/>
 * A Jupyter: Csatlakozás [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) vagy [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), majd nyissa meg a minták CNTK könyvtára. |

## <a name="tensorflow"></a>TensorFlow

|    |           |
| ------------- | ------------- |
| Mi ez?   | A részletes oktatási keretrendszer      |
| Támogatott DSVM kiadás      | Windows, Linux     |
| Hogyan van azt konfigurált / a DSVM telepítve?  | TensorFlow telepítve van a Python 3.5 [Linux és a Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) és a Python 3.6 [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition).  |
| Minták mutató hivatkozások      | A minta Jupyter notebookok tartoznak.     |
| A DSVM a kapcsolódó eszközök      | Keras      |
| Hogyan használja az / futtatni?    | * A terminálon egy: a megfelelő környezet aktiválja, majd futtassa a Python. <br/>
 * A Jupyter: Csatlakozás [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) vagy [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), majd nyissa meg a minták TensorFlow könyvtára.  |

## <a name="horovod"></a>Horovod

|    |           |
| ------------- | ------------- |
| Mi ez?   | Distribued mély tanulási keretrendszere, amely TensorFlow      |
| Támogatott DSVM kiadás      | Ubuntu     |
| Hogyan van azt konfigurált / a DSVM telepítve?  | Horovod telepítve van a Python 3.5 [Ubuntu](dsvm-languages.md#python-linux-and-windows-server-2012-edition).  |
| Minták mutató hivatkozások      | [https://github.com/uber/horovod/tree/master/examples](https://github.com/uber/horovod/tree/master/examples)     |
| A DSVM a kapcsolódó eszközök      | TensorFlow      |
| Hogyan használja az / futtatni?    | A terminálon: a megfelelő környezet aktiválja, majd futtassa a Python. |

## <a name="keras"></a>Keras

|    |           |
| ------------- | ------------- |
| Mi ez?   | A részletes oktatási keretrendszer      |
| Támogatott DSVM kiadás      | Windows, Linux     |
| Hogyan van azt konfigurált / a DSVM telepítve?  | TensorFlow telepítve van a Python 3.5 [Linux és a Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) és a Python 3.6 [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). |
| Minták mutató hivatkozások      | https://github.com/fchollet/keras/tree/master/examples      |
| A DSVM a kapcsolódó eszközök      | Microsoft kognitív eszközkészlet, TensorFlow, Theano      |
| Hogyan használja az / futtatni?    | * A terminálon egy: a megfelelő környezet aktiválja, majd futtassa a Python. <br/>
 * A Jupyter: Letöltése a minták a Githubon, csatlakozás [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) vagy [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), majd nyissa meg a minta címtárat. |

## <a name="caffe"></a>Caffe

|    |           |
| ------------- | ------------- |
| Mi ez?   | A részletes oktatási keretrendszer      |
| Támogatott DSVM kiadás      | Ubuntu     |
| Hogyan van azt konfigurált / a DSVM telepítve?  | Caffe telepítve van-e `/opt/caffe`.    |
| Hogyan lehet váltani a Python 2.7-es | Futtassa a `source activate root` parancsot. |
| Minták mutató hivatkozások      | Minták szerepelnek `/opt/caffe/examples`.      |
| A DSVM a kapcsolódó eszközök      | Caffe2      |
### <a name="how-to-use--run-it"></a>Hogyan használja az / futtatni?  

Jelentkezzen be a virtuális Gépet, majd indítsa el egy új terminál, és írja be a X2Go segítségével

```
cd /opt/caffe/examples
source activate root
jupyter notebook
```

Egy új böngészőablakban nyílik meg a minta notebookok.

/Opt/caffe/build/install/bin bináris fájljai vannak telepítve.

Caffe telepített verziója szükséges Python 2.7, és nem működik együtt a Python 3.5 alapértelmezés szerint aktív. Futtatás `source activate root` Anaconda környezetre váltson. 

## <a name="caffe2"></a>Caffe2

|    |           |
| ------------- | ------------- |
| Mi ez?   | A részletes oktatási keretrendszer      |
| Támogatott DSVM kiadás      | Ubuntu     |
| Hogyan van azt konfigurált / a DSVM telepítve?  | Caffe2 telepítve van-e a [Python 2.7-es (root) conda környezet](dsvm-languages.md#python-linux-and-windows-server-2012-edition). A forrás álljon a `/opt/caffe2`. |
| Minták mutató hivatkozások      | A minta notebookok JupyterHub szerepelnek. |
| A DSVM a kapcsolódó eszközök      | Caffe      |
| Hogyan használja az / futtatni?    | * A terminálon: aktiválja a [legfelső szintű Python-környezetben](dsvm-languages.md#python-linux-and-windows-server-2012-edition), indítsa el a Python és caffe2 importálása. <br/> * A JupyterHub: [JupyterHub csatlakozni](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), majd keresse meg a Caffe2 könyvtárát minta notebookok kereséséhez. Néhány notebookok igényelnek a Caffe2 legfelső szintű kell beállítani, a Python-kódban; Adja meg a /opt/caffe2. |
| Megjegyzések létrehozása | Caffe2 Linux forrásból épül, és CUDA cuDNN és Intel MKL tartalmazza. A jelenlegi lefoglalás 0d9c0d48c6f20143d6404b99cc568efd29d5a4be, minden Feldolgozóegységekkel és tesztelt minták stabilitását választott. |

## <a name="chainer"></a>Chainer

|    |           |
| ------------- | ------------- |
| Mi ez?   | A részletes oktatási keretrendszer      |
| Támogatott DSVM kiadás      | Windows, Linux     |
| Hogyan van azt konfigurált / a DSVM telepítve?  | Chainer telepítve van-e [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition). ChainerRL és ChainerCV is települnek.   |
| Minták mutató hivatkozások      | A minta notebookok JupyterHub szerepelnek. |
| A DSVM a kapcsolódó eszközök      | Caffe      |
| Hogyan használja az / futtatni?  | * A terminálon: aktiválja a [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) környezetet, futtassa _python_, majd importálja a Chainer. <br/>
* A JupyterHub: [JupyterHub csatlakozni](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), majd keresse meg a Chainer könyvtárát minta notebookok kereséséhez.


## <a name="deep-water"></a>A részletes vízjel

|    |           |
| ------------- | ------------- |
| Mi ez?   | A részletes tanulási keretrendszere, amely H2O      |
| Támogatott DSVM kiadás      | Ubuntu     |
| Hogyan van azt konfigurált / a DSVM telepítve?  | A részletes vízjel telepítve van-e [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) és található `/dsvm/tools/deep_water`.   |
| Minták mutató hivatkozások      | A minta notebookok JupyterHub szerepelnek.      |
| A DSVM a kapcsolódó eszközök      | H2O, készült vízjel      |

### <a name="how-to-use--run-it"></a>Hogyan használja az / futtatni?  

A részletes vízjel CUDA 8 cuDNN 5.1 a van szükség. Ez nincs alapértelmezés szerint a könyvtár útvonala más mély tanulási keretrendszerek CUDA 9 és cuDNN 7 használja. A részletes vízjel CUDA 8 + cuDNN 5.1 használata:

```
export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:${LD_LIBRARY_PATH}
export CUDA_ROOT=/usr/local/cuda-8.0
```

A részletes vízjel használata:
* A terminálon: aktiválja a [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) környezetre, majd futtassa a _python_. <br/>
* A JupyterHub: [JupyterHub csatlakozni](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), majd keresse meg a deep_water könyvtárát minta notebookok kereséséhez.

## <a name="mxnet"></a>MXNet

|    |           |
| ------------- | ------------- |
| Mi ez?   | A részletes oktatási keretrendszer      |
| Támogatott DSVM kiadás      | Windows, Linux     |
| Hogyan van azt konfigurált / a DSVM telepítve?  | MXNet telepítve van-e `C:\dsvm\tools\mxnet` Windows és `/dsvm/tools/mxnet` Linux rendszeren. Python kötések telepített Python 3.5 [Linux és a Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) és a Python 3.6 [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). R kötések Ubuntu is telepítve vannak.   |
| Minták mutató hivatkozások      | A minta Jupyter notebookok tartoznak.    |
| A DSVM a kapcsolódó eszközök      | Keras      |
| Hogyan használja az / futtatni?    | * A terminálon egy: a megfelelő környezet aktiválja, majd futtassa a Python. <br/>
 * A Jupyter: Csatlakozás [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) vagy [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), majd nyissa meg a minták mxnet könyvtára.  |
 | Megjegyzések létrehozása | MXNet Linux épülő forrásból. A build CUDA, cuDNN, NCCL és MKL tartalmazza. |

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
| Hogyan használja az / futtatni?    | * A terminálon egy a Python-verzió (legfelső szintű vagy py35), futtassa a python, majd importálása theano aktiválása. <br/> 
* A Jupyter válassza ki a Python 2.7 vagy 3.5-ös kernel, majd importálja a theano.  
<br/>
A legutóbbi MKL hiba megoldása érdekében először állítsa a réteg szálkezelési MKL kell:<br/><br/>
_Exportálás MKL_THREADING_LAYER = GNU_
|



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
| Hogyan van azt konfigurált / a DSVM telepítve?  | PyTorch telepítve van-e [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition).  |
| Minták mutató hivatkozások      | Minta Jupyter notebookok tartoznak, és minták /dsvm/samples/pytorch is található.      |
| A DSVM a kapcsolódó eszközök      | Torch      |
| Hogyan használja az / futtatható | 
* A terminálon: a megfelelő környezet aktiválja, majd futtassa a Python. <br/>
 * A Jupyter: Csatlakozás [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), majd nyissa meg a minták PyTorch könyvtára.  |

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

## <a name="tensorrt"></a>TensorRT

|    |           |
| ------------- | ------------- |
| Mi ez?   | A megállapítás kiszolgálót NVIDIA a tanulási mély. |
| Támogatott DSVM kiadás      | Ubuntu     |
| Hogyan van azt konfigurált / a DSVM telepítve?  | TensorRT van telepítve egy _apt_ csomag.   |
| Minták mutató hivatkozások      | Minták érhetők el [online](https://docs.nvidia.com/deeplearning/sdk/tensorrt-developer-guide/index.html#samples).      |
| A DSVM a kapcsolódó eszközök      | Szolgáltató, MXNet modell Server TensorFlow  |



