---
title: Mély tanulás és Mesterségesintelligencia-keretrendszerek – Azure |} A Microsoft Docs
description: Ismerje meg a deep learning-keretrendszerek és eszközök támogatott a Data Science virtuális gépen.
keywords: adatelemzési eszközök, adatelemző virtuális gép, eszközök adatelemzéshez, linux adatelemzés
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: 59f88d54d3542738f1a500c8c476995eb1535ecf
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58006497"
---
# <a name="deep-learning-and-ai-frameworks"></a>Mély tanulás és mesterségesintelligencia-keretrendszerek
A [adatelemző virtuális gép](https://aka.ms/dsvm) (DSVM) és a [Deep Learning virtuális gép](https://aka.ms/dsvm/deeplearning) segítségével prediktív elemzéssel mesterséges intelligencia (AI) alkalmazásokat hozhat létre deep learning-keretrendszerek számos támogat, és kognitív képességeket, például kép és a language understanding.

Az alábbiakban az összes a mélytanulási keretrendszerekkel a dsvm-hez elérhető.

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

|    |           |
| ------------- | ------------- |
| Mi ez?   | Deep learning-keretrendszerek      |
| Támogatott DSVM-kiadások      | Windows, Linux     |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | A Microsoft Cognitive Toolkit (CNTK) telepítve van a Python 3.5 [Linux és Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) és a Python 3.6-os [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition).   |
| A minták mutató hivatkozások      | Mintául szolgáló Jupyter notebookok szerepelnek.     |
| A dsvm-hez kapcsolódó eszközök      | Keras      |
| Hogyan futtathatja / használata?    | * A parancsot egy terminálban: aktiválja a megfelelő környezetet, majd futtassa a Python. <br/> * A Jupyter: Csatlakozás [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) vagy [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), majd nyissa meg a minták a CNTK-könyvtár. |

## <a name="tensorflow"></a>TensorFlow

|    |           |
| ------------- | ------------- |
| Mi ez?   | Deep learning-keretrendszerek      |
| Támogatott DSVM-kiadások      | Windows, Linux     |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | Tensorflow-hoz a Python 3.5 települ az [Linux és Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) és a Python 3.6-os [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition).  |
| A minták mutató hivatkozások      | Mintául szolgáló Jupyter notebookok szerepelnek.     |
| A dsvm-hez kapcsolódó eszközök      | Keras      |
| Hogyan futtathatja / használata?    | * A parancsot egy terminálban: aktiválja a megfelelő környezetet, majd futtassa a Python. <br/> * A Jupyter: Csatlakozás [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) vagy [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), majd nyissa meg a minták a TensorFlow-könyvtár.  |

## <a name="horovod"></a>Horovod

|    |           |
| ------------- | ------------- |
| Mi ez?   | Distribued deep learning keretrendszer, a tensorflow-hoz      |
| Támogatott DSVM-kiadások      | Ubuntu     |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | Horovod Python 3.5 települ az [Ubuntu](dsvm-languages.md#python-linux-and-windows-server-2012-edition).  |
| A minták mutató hivatkozások      | [https://github.com/uber/horovod/tree/master/examples](https://github.com/uber/horovod/tree/master/examples)     |
| A dsvm-hez kapcsolódó eszközök      | TensorFlow      |
| Hogyan futtathatja / használata?    | Egy terminálon: aktiválja a megfelelő környezetet, majd futtassa a Python. |

## <a name="keras"></a>Keras

|    |           |
| ------------- | ------------- |
| Mi ez?   | Magas szintű deep learning-API      |
| Támogatott DSVM-kiadások      | Windows, Linux     |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | Tensorflow-hoz a Python 3.5 települ az [Linux és Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) és a Python 3.6-os [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). |
| A minták mutató hivatkozások      | https://github.com/fchollet/keras/tree/master/examples      |
| A dsvm-hez kapcsolódó eszközök      | A Microsoft Cognitive Toolkit, TensorFlow, Theano      |
| Hogyan futtathatja / használata?    | * A parancsot egy terminálban: aktiválja a megfelelő környezetet, majd futtassa a Python. <br/> * A Jupyter: A minták GitHub töltheti le, csatlakozás [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) vagy [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), majd nyissa meg a minta címtárat. |

## <a name="caffe"></a>Caffe

|    |           |
| ------------- | ------------- |
| Mi ez?   | Deep learning-keretrendszerek      |
| Támogatott DSVM-kiadások      | Ubuntu     |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | Caffe telepítve van a `/opt/caffe`.    |
| Hogyan lehet váltani a Python 2.7-es | Futtassa a `source activate root` parancsot. |
| A minták mutató hivatkozások      | A minták szerepelnek `/opt/caffe/examples`.      |
| A dsvm-hez kapcsolódó eszközök      | Caffe2      |

### <a name="how-to-use--run-it"></a>Hogyan futtathatja / használata?

Jelentkezzen be a virtuális Gépre, majd indítsa el egy új terminálablakban, és adja meg a X2Go használatával

```
cd /opt/caffe/examples
source activate root
jupyter notebook
```

Mintafüzetek megnyílik egy új böngészőablakban.

Bináris fájljai vannak telepítve a /opt/caffe/build/install/bin.

Caffe telepített verziója van szükség a Python 2.7-t, és a Python 3.5 alapértelmezés szerint aktiválva nem fog működni. Futtatás `source activate root` Anaconda környezetre váltani.

## <a name="caffe2"></a>Caffe2

|    |           |
| ------------- | ------------- |
| Mi ez?   | Deep learning-keretrendszerek      |
| Támogatott DSVM-kiadások      | Ubuntu     |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | Caffe2 telepítve van a [környezet a Python 2.7-es (root) conda](dsvm-languages.md#python-linux-and-windows-server-2012-edition). A forrás, a `/opt/caffe2`. |
| A minták mutató hivatkozások      | Mintafüzetek JupyterHub szerepelnek. |
| A dsvm-hez kapcsolódó eszközök      | Caffe      |
| Hogyan futtathatja / használata?    | * A terminálon: aktiválja a [legfelső szintű Python-környezetet](dsvm-languages.md#python-linux-and-windows-server-2012-edition), indítsa el a Python és caffe2 importálása. <br/> * A JupyterHub: [JupyterHub csatlakozni](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), majd keresse meg a Caffe2 könyvtárát mintafüzetek található. Néhány notebookok megkövetelése a Caffe2 legfelső szintű kell beállítani, a Python-kódban; Adja meg a /opt/caffe2. |
| Jegyzetek létrehozása | Caffe2 a Linux rendszerű forrásból épül, és tartalmazza a CUDA, cuDNN és Intel MKL. A jelenlegi véglegesítés 0d9c0d48c6f20143d6404b99cc568efd29d5a4be, minden gpu-k és minták tesztelt stabilitását választott. |

## <a name="chainer"></a>Chainer

|    |           |
| ------------- | ------------- |
| Mi ez?   | Deep learning-keretrendszerek      |
| Támogatott DSVM-kiadások      | Windows, Linux     |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | Az telepítve van a chainer [Python 3.5-ös](dsvm-languages.md#python-linux-and-windows-server-2012-edition). ChainerRL és ChainerCV is telepítve lesz.   |
| A minták mutató hivatkozások      | Mintafüzetek JupyterHub szerepelnek. |
| A dsvm-hez kapcsolódó eszközök      | Caffe      |
| Hogyan futtathatja / használata?  | * A terminálon: aktiválja a [Python 3.5-ös](dsvm-languages.md#python-linux-and-windows-server-2012-edition) környezetben futtatni _python_, majd importálja a chainer. <br/> * A JupyterHub: [JupyterHub csatlakozni](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), majd keresse meg a Chainer könyvtárát mintafüzetek található.


## <a name="deep-water"></a>Mély víz

|    |           |
| ------------- | ------------- |
| Mi ez?   | A H2O deep learning keretrendszer      |
| Támogatott DSVM-kiadások      | Ubuntu     |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | Mély víz telepítve van a [Python 3.5-ös](dsvm-languages.md#python-linux-and-windows-server-2012-edition) és is elérhető `/dsvm/tools/deep_water`.   |
| A minták mutató hivatkozások      | Mintafüzetek JupyterHub szerepelnek.      |
| A dsvm-hez kapcsolódó eszközök      | A H2O Sparkling Water      |

### <a name="how-to-use--run-it"></a>Hogyan futtathatja / használata?

Mély víz CUDA 8 cuDNN 5.1-a van szükség. Ez a nem alapértelmezés szerint a könyvtár elérési úton egyéb deep learning-keretrendszerek használja a CUDA 9 és cuDNN 7. Mély víz CUDA 8 + cuDNN 5.1 használata:

```
export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:${LD_LIBRARY_PATH}
export CUDA_ROOT=/usr/local/cuda-8.0
```

Mély víz használata:
* A terminálon: aktiválja a [Python 3.5-ös](dsvm-languages.md#python-linux-and-windows-server-2012-edition) környezetre, majd futtassa a _python_. <br/>
* A JupyterHub: [JupyterHub csatlakozni](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), majd keresse meg a deep_water könyvtárát mintafüzetek található.

## <a name="mxnet"></a>MXNet

|    |           |
| ------------- | ------------- |
| Mi ez?   | Deep learning-keretrendszerek      |
| Támogatott DSVM-kiadások      | Windows, Linux     |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | MXNet telepítve van a `C:\dsvm\tools\mxnet` a Windows és `/dsvm/tools/mxnet` Linux rendszeren. Python-kötések vannak telepítve a Python 3.5 [Linux és Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) és a Python 3.6-os [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). R-kötések az ubuntu rendszeren is telepítve lesz.   |
| A minták mutató hivatkozások      | Mintául szolgáló Jupyter notebookok szerepelnek.    |
| A dsvm-hez kapcsolódó eszközök      | Keras      |
| Hogyan futtathatja / használata?    | * A parancsot egy terminálban: aktiválja a megfelelő környezetet, majd futtassa a Python. <br/> * A Jupyter: Csatlakozás [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) vagy [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), majd nyissa meg a minták mxnet könyvtárat.  |
 | Jegyzetek létrehozása | MXNet Linux épülő forrásból. A build CUDA, cuDNN, NCCL és MKL tartalmazza. |

## <a name="nvidia-digits"></a>NVIDIA SZÁMJEGY

|    |           |
| ------------- | ------------- |
| Mi ez?   | Deep learning-NVIDIA rendszer rövid idő alatt a deep learning-modellek betanításához      |
| Támogatott DSVM-kiadások      | Ubuntu     |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | SZÁMJEGYEK telepítve van a `/dsvm/tools/DIGITS` érhető el, és egy szolgáltatás nevű _számjegyek_.   |

### <a name="how-to-use--run-it"></a>Hogyan futtathatja / használata?

Jelentkezzen be a virtuális Gépet a X2Go. A terminálon a szolgáltatás elindítása:

    sudo systemctl start digits

A szolgáltatás indítása körülbelül egy percig tart. Indítson el egy webböngészőt, és keresse meg a `http://localhost:5000`. Vegye figyelembe, hogy számjegyek nem biztosít egy biztonságos bejelentkezés előtt nem szerencsés felfedni a virtuális gép kívül.



## <a name="nvidia-smi"></a>NVIDIA-smi

|    |           |
| ------------- | ------------- |
| Mi ez?   | Eszköz NVIDIA GPU-tevékenység lekérdezése      |
| Támogatott DSVM-kiadások      | Windows, Linux     |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | _NVIDIA-smi_ érhető el a rendszerbeli elérési úton.   |
| Hogyan futtathatja / használata? | Indítsa el a parancssort (Windows) vagy (a Linux rendszeren) parancsot egy terminálban, majd futtassa _nvidia-smi_.



## <a name="theano"></a>Theano

|    |           |
| ------------- | ------------- |
| Mi ez?   | Deep learning-keretrendszerek      |
| Támogatott DSVM-kiadások      | Ubuntu     |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | Theano telepítve van a Python 2.7-es (_legfelső szintű_), valamint a Python 3.5-ös (_py35_) környezetben.   |
| A dsvm-hez kapcsolódó eszközök      | Keras      |
| Hogyan futtathatja / használata?    | * A terminálon a Python-verzió (legfelső szintű vagy py35) kívánja, futtassa a python, majd importálja a theano aktiválása. <br/> * A Jupyter válassza ki a Python 2.7-es vagy a 3.5-ös kernel, majd importálása theano.  <br/>Egy közelmúltbeli MKL hiba elkerüléséhez először állítsa a réteg threading MKL kell:<br/><br/>_export MKL_THREADING_LAYER=GNU_|



## <a name="torch"></a>Torch

|    |           |
| ------------- | ------------- |
| Mi ez?   | Deep learning-keretrendszerek      |
| Támogatott DSVM-kiadások      | Ubuntu     |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | Torch telepítve van a `/dsvm/tools/torch`. PyTorch telepítve van a Python 2.7-es (_legfelső szintű_), valamint a Python 3.5-ös (_py35_) környezetben.   |
| A minták mutató hivatkozások      | Torch mintát a következő helyen találhatók `/dsvm/samples/torch`. PyTorch mintát a következő helyen találhatók `/dsvm/samples/pytorch`.      |


## <a name="pytorch"></a>PyTorch

|    |           |
| ------------- | ------------- |
| Mi ez?   | Deep learning-keretrendszerek      |
| Támogatott DSVM-kiadások      | Linux     |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | PyTorch telepítve van a [Python 3.5-ös](dsvm-languages.md#python-linux-and-windows-server-2012-edition).  |
| A minták mutató hivatkozások      | Mintául szolgáló Jupyter notebookok megtalálhatók, és minták /dsvm/samples/pytorch is található.      |
| A dsvm-hez kapcsolódó eszközök      | Torch      |
| Hogyan futtathatja / használata |* A parancsot egy terminálban: aktiválja a megfelelő környezetet, majd futtassa a Python. <br/> * A Jupyter: Csatlakozás [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), majd nyissa meg a mintákat a PyTorch könyvtárban.  |

## <a name="mxnet-model-server"></a>MXNet Model Server

|    |           |
| ------------- | ------------- |
| Mi ez?   | A kiszolgáló HTTP-végpontokat MXNet és ONNX-modellek létrehozása      |
| Támogatott DSVM-kiadások      | Linux     |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | _mxnet-modell-server_ érhető el a terminálon.   |
| A minták mutató hivatkozások      | Naprakész mintákat keressen az [MXNet modell kiszolgáló lap](https://github.com/awslabs/mxnet-model-server).    |
| A dsvm-hez kapcsolódó eszközök      | MXNet      |

## <a name="tensorflow-serving"></a>TensorFlow-kiszolgáló

|    |           |
| ------------- | ------------- |
| Mi ez?   | A kiszolgáló, a TensorFlow-modellen futhat következtetési      |
| Támogatott DSVM-kiadások      | Linux     |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | _tensorflow_model_server_ érhető el a terminálon.   |
| A minták mutató hivatkozások      | A minták elérhetők [online](https://www.tensorflow.org/serving/).      |
| A dsvm-hez kapcsolódó eszközök      | TensorFlow      |

## <a name="tensorrt"></a>TensorRT

|    |           |
| ------------- | ------------- |
| Mi ez?   | Deep learning-NVIDIA következtetésekhez kiszolgálót. |
| Támogatott DSVM-kiadások      | Ubuntu     |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | TensorRT van telepítve egy _apt_ csomagot.   |
| A minták mutató hivatkozások      | A minták elérhetők [online](https://docs.nvidia.com/deeplearning/sdk/tensorrt-developer-guide/index.html#samples).      |
| A dsvm-hez kapcsolódó eszközök      | Szolgáltató, MXNet modell kiszolgáló tensorflow-hoz  |



