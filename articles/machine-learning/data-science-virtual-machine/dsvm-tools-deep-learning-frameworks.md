---
title: Deep learning & AI-keretrendszerek
titleSuffix: Azure Data Science Virtual Machine
description: Elérhető mély tanulási keretrendszerek és eszközök az Azure Data Science Virtual Machineon.
keywords: adatelemzési eszközök, adatelemző virtuális gép, eszközök adatelemzéshez, linux adatelemzés
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: d8a5cf428f41b130e6faf68ac87a075c15211099
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587600"
---
# <a name="deep-learning-and-ai-frameworks-for-the-azure-data-science-vm"></a>Mély tanulási és AI-keretrendszerek az Azure Data Science VM
Az alábbi lista mély tanulási keretrendszerek a DSVM.

## <a name="caffe"></a>[Caffe](https://github.com/BVLC/caffe)

|    |           |
| ------------- | ------------- |
| Támogatott verzió (k) | |
| Támogatott DSVM-kiadások      | Linux (Ubuntu)     |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | A Cafe `/opt/caffe`van telepítve.   A minták `/opt/caffe/examples`.|
| Futtatás      | a X2Go használatával jelentkezzen be a virtuális gépre, majd indítson el egy új terminált, és adja meg a következőt:<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>Mintafüzetek megnyílik egy új böngészőablakban. Bináris fájljai vannak telepítve a /opt/caffe/build/install/bin.<br/><br/>A Cafe telepített verziójának a Python 2,7-es verziójára van szüksége, és a Python 3,5 nem működik, amely alapértelmezés szerint aktiválva van. A Python 2,7-re való váltáshoz futtassa a `source activate root` parancsot az anaconda-környezetre való váltáshoz.|    

## <a name="caffe2"></a>[Caffe2](https://github.com/caffe2/caffe2)

|    |           |
| ------------- | ------------- |
| Támogatott verzió (k) | |
| Támogatott DSVM-kiadások      | Linux (Ubuntu)     |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | A Caffe2 a [Python 2,7 (root) Conda-környezetben van telepítve. |
| Futtatás      | Terminal: a Python elindítása és az Caffe2 importálása. <br/> * JupyterHub: [kapcsolódjon a JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), majd nyissa meg a Caffe2 könyvtárat, és keresse meg a minta jegyzetfüzeteket. Néhány notebookok megkövetelése a Caffe2 legfelső szintű kell beállítani, a Python-kódban; Adja meg a /opt/caffe2. |

## <a name="chainer"></a>[Chainert](https://chainer.org/)

|    |           |
| ------------- | ------------- |
| Támogatott verzió (k) | 5.2 |
| Támogatott DSVM-kiadások      | Linux (Ubuntu)     |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | A chainer a Python 3,5-es verzióra van telepítve. |
| Futtatás      | Terminál: aktiválja a Python 3,5 környezetet, futtassa `python`, majd `import chainer`. <br/> * JupyterHub: [kapcsolódjon a JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), majd nyissa meg a láncolt könyvtárat a minta jegyzetfüzetek kereséséhez.| 

## <a name="cuda-cudnn-nvidia-driver"></a>[CUDA, cuDNN, NVIDIA-illesztőprogram](https://developer.nvidia.com/cuda-toolkit)

|    |           |
| ------------- | ------------- |
| Támogatott verzió (k) | 10.0.130|
| Támogatott DSVM-kiadások      | Windows és Linux   |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  |az _NVIDIA-SMI_ a rendszer elérési útján érhető el.  |
| Futtatás      | Nyisson meg egy parancssort (Windows rendszeren) vagy egy terminált (Linuxon), majd futtassa az _NVIDIA-SMI_parancsot. |


## <a name="horovod"></a>[Horovod](https://github.com/uber/horovod)

|    |           |
| ------------- | ------------- |
| Támogatott verzió (k) | 0.16.1|
| Támogatott DSVM-kiadások      | Linux (Ubuntu)   |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | A Horovod a Python 3,5-es verziójában van telepítve |
| Futtatás      | Aktiválja a megfelelő környezetet a terminálon, majd futtassa a Pythont. |

## <a name="keras"></a>[Kerasz](https://keras.io/)

|    |           |
| ------------- | ------------- |
| Támogatott verzió (k) | 2.2.4 |
| Támogatott DSVM-kiadások      | Windows és Linux   |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | A kerasz a Windows Python 3,6-es verziójában, Linuxon pedig Python 3,5-ben van telepítve |
| Futtatás      | Aktiválja a megfelelő környezetet a terminálon, majd futtassa a Pythont. |

## <a name="microsoft-cognitive-toolkit-cntk"></a>[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/)

|    |           |
| ------------- | ------------- |
| Támogatott verzió (k) | 2.5.1 |
| Támogatott DSVM-kiadások      | Windows és Linux   |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | A CNTK a Python 3,6-es verzióban van telepítve a [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) -ben és a Python 3,5-on [Linux](./dsvm-tools-languages.md#python-linux-edition)rendszeren) |
| Futtatás      | Terminál: aktiválja a megfelelő környezetet, és futtassa a Pythont. <br/>Jupyter: kapcsolódjon a [Jupyter](provision-vm.md) vagy a [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)-hez, majd nyissa meg a CNTK könyvtárat a mintákhoz. |

## <a name="mxnet"></a>[MXNet](https://mxnet.apache.org/)
|    |           |
| ------------- | ------------- |
| Támogatott verzió (k) | 1.3.0 |
| Támogatott DSVM-kiadások      | Windows és Linux   |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | A MXNet `C:\dsvm\tools\mxnet` Windows rendszeren és Ubuntu rendszeren `/dsvm/tools/mxnet` van telepítve. A Python-kötések a [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) -es Python 3,6-es verziójában, a [Linuxon](./dsvm-tools-languages.md#python-linux-edition)pedig a Python 3,5-ban vannak telepítve, az R-kötések pedig az Ubuntu DSVM is szerepelnek. |
| Futtatás      | Terminál: aktiválja a megfelelő Conda-környezetet, majd futtassa `import mxnet`. <br/>Jupyter: kapcsolódjon a [Jupyter](provision-vm.md#access-the-dsvm) vagy a [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)-hez, majd nyissa meg a mintákhoz tartozó `mxnet` könyvtárat. |

## <a name="mxnet-model-server"></a>[MXNet-modell kiszolgálója](https://github.com/awslabs/mxnet-model-server#quick-start)

|    |           |
| ------------- | ------------- |
| Támogatott verzió (k) | 1.0.1 |
| Támogatott DSVM-kiadások      | Windows és Linux   |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | A MXNet Model Server a Python 3,6-es verzióra van telepítve a [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) és a Python 3,5 [Linux](./dsvm-tools-languages.md#python-linux-edition)rendszeren) |
| Futtatás      | Terminál: `sudo systemctl stop jupyterhub` futtatásával állítsa le a JupyterHub szolgáltatást, mert mindkettő ugyanazt a portot figyeli. Ezután aktiválja a megfelelő Conda-környezetet, és futtassa `mxnet-model-server --start --models squeezenet=https://s3.amazonaws.com/model-server/model_archive_1.0/squeezenet_v1.1.mar` |

## <a name="nvidia-system-management-interface-nvidia-smi"></a>[NVidia rendszerkezelési felület (NVIDIA-SMI)](https://developer.nvidia.com/nvidia-system-management-interface)

|    |           |
| ------------- | ------------- |
| Támogatott verzió (k) |  |
| Támogatott DSVM-kiadások      | Windows és Linux   |
| Mi ez? | Eszköz NVIDIA GPU-tevékenység lekérdezése |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | `nvidia-smi` a rendszer elérési útjában van. |
| Futtatás      | Egy **GPU-val rendelkező**virtuális gépen nyisson meg egy parancssort (Windows rendszeren) vagy egy terminált (Linuxon), majd futtassa `nvidia-smi`. |

## <a name="pytorch"></a>[PyTorch](https://pytorch.org/)

|    |           |
| ------------- | ------------- |
| Támogatott verzió (k) | 1.2.0 (Ubuntu 16,04, Windows 2016), 1.4.0 (Ubuntu 18,04, Windows 2019) |
| Támogatott DSVM-kiadások      | Linux |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | A [Python 3,5](dsvm-tools-languages.md#python-linux-edition)-es verzióra van telepítve. A minta Jupyter jegyzetfüzeteket tartalmaz, és a minták a/dsvm/Samples/pytorch. |
| Futtatás      | Terminál: aktiválja a megfelelő környezetet, majd futtassa a Pythont.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine): kapcsolódjon, majd nyissa meg a PyTorch könyvtárat a mintákhoz.  |

## <a name="tensorflow"></a>[TensorFlow](https://www.tensorflow.org/)

|    |           |
| ------------- | ------------- |
| Támogatott verzió (k) | 1,13 |
| Támogatott DSVM-kiadások      | Windows, Linux |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | Telepítve a Python 3,5-es verziójában [Linux](dsvm-tools-languages.md#python-linux-edition) és Python 3,6 rendszeren [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) |
| Futtatás      | Terminál: aktiválja a megfelelő környezetet, majd futtassa a Pythont. <br/> * Jupyter: kapcsolódjon a [Jupyter](provision-vm.md) vagy a [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), majd nyissa meg a TensorFlow könyvtárat a mintákhoz.   |

## <a name="tensorflow-serving"></a>[TensorFlow-kiszolgáló](https://www.tensorflow.org/serving/)

|    |           |
| ------------- | ------------- |
| Támogatott verzió (k) | 1,12 |
| Támogatott DSVM-kiadások      | Linux |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | tensorflow_model_server a terminálon érhető el. |
| Futtatás      |  A minták [online](https://www.tensorflow.org/serving/)elérhetők.   |


## <a name="theano"></a>[Theano](https://github.com/Theano/Theano)

|    |           |
| ------------- | ------------- |
| Támogatott verzió (k) | 1.0.3 |
| Támogatott DSVM-kiadások      | Linux |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  |A theano a Python 2,7 (_root_) és a Python 3,5 (_py35_) környezetben van telepítve. |
| Futtatás      |  Terminál: aktiválja a kívánt Python-verziót (root vagy py35), futtassa a Pythont, majd importálja a theano.<br/>* Jupyter: válassza ki a Python 2,7 vagy a 3,5 kernelt, majd importálja a theano.  <br/>A legújabb matematikai kernel-függvénytári (MKL-) hibák megkerüléséhez először a következőképpen kell beállítania a MKL-szálat:<br/><br/>`export MKL_THREADING_LAYER=GNU`  |