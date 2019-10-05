---
title: Deep learning & AI-keretrendszerek
titleSuffix: Azure Data Science Virtual Machine
description: Elérhető mély tanulási keretrendszerek és eszközök az Azure Data Science Virtual Machineon.
keywords: adatelemzési eszközök, adatelemző virtuális gép, eszközök adatelemzéshez, linux adatelemzés
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: conceptual
ms.date: 10/1/2019
ms.openlocfilehash: db95788b0f2c041157bdc16000d0328c042e86d5
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973693"
---
# <a name="deep-learning-and-ai-frameworks-for-the-azure-data-science-vm"></a>Mély tanulási és AI-keretrendszerek az Azure Data Science VM
Az alábbi lista mély tanulási keretrendszerek a DSVM.

## <a name="caffehttpsgithubcombvlccaffe"></a>[Caffe](https://github.com/BVLC/caffe)

|    |           |
| ------------- | ------------- |
| Támogatott verzió (k) | |
| Támogatott DSVM-kiadások      | Linux (Ubuntu)     |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | Caffe telepítve van a `/opt/caffe`.   A minták @no__t – 0.|
| Futtatás      | a X2Go használatával jelentkezzen be a virtuális gépre, majd indítson el egy új terminált, és adja meg a következőt:<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>Mintafüzetek megnyílik egy új böngészőablakban. Bináris fájljai vannak telepítve a /opt/caffe/build/install/bin.<br/><br/>A Cafe telepített verziójának a Python 2,7-es verziójára van szüksége, és a Python 3,5 nem működik, amely alapértelmezés szerint aktiválva van. A Python 2,7-re való váltáshoz futtassa a `source activate root` parancsot az anaconda-környezetre való váltáshoz.|    

## <a name="caffe2httpsgithubcomcaffe2caffe2"></a>[Caffe2](https://github.com/caffe2/caffe2)

|    |           |
| ------------- | ------------- |
| Támogatott verzió (k) | |
| Támogatott DSVM-kiadások      | Linux (Ubuntu)     |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | A Caffe2 a [Python 2,7 (root) Conda-környezetben van telepítve. |
| Futtatás      | Terminál Indítsa el a Pythont, és importálja a Caffe2. <br/> * JupyterHub: [Kapcsolódjon a JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)-hez, majd nyissa meg a Caffe2 könyvtárat a minta jegyzetfüzetek kereséséhez. Néhány notebookok megkövetelése a Caffe2 legfelső szintű kell beállítani, a Python-kódban; Adja meg a /opt/caffe2. |

## <a name="chainerhttpschainerorg"></a>[Chainert](https://chainer.org/)

|    |           |
| ------------- | ------------- |
| Támogatott verzió (k) | 5.2 |
| Támogatott DSVM-kiadások      | Linux (Ubuntu)     |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | A chainer a Python 3,5-es verzióra van telepítve. |
| Futtatás      | Terminál Aktiválja a Python 3,5 környezetet, futtassa `python`, majd `import chainer`. <br/> * JupyterHub: [Kapcsolódjon a JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), majd nyissa meg a láncolt könyvtárat a minta jegyzetfüzetek kereséséhez.| 

## <a name="cuda-cudnn-nvidia-driverhttpsdevelopernvidiacomcuda-toolkit"></a>[CUDA, cuDNN, NVIDIA-illesztőprogram](https://developer.nvidia.com/cuda-toolkit)

|    |           |
| ------------- | ------------- |
| Támogatott verzió (k) | 10.0.130|
| Támogatott DSVM-kiadások      | Windows és Linux   |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  |_NVIDIA-smi_ érhető el a rendszerbeli elérési úton.  |
| Futtatás      | Nyisson meg egy parancssort (Windows rendszeren) vagy egy terminált (Linuxon), majd futtassa az _NVIDIA-SMI_parancsot. |


## <a name="horovodhttpsgithubcomuberhorovod"></a>[Horovod](https://github.com/uber/horovod)

|    |           |
| ------------- | ------------- |
| Támogatott verzió (k) | 0.16.1|
| Támogatott DSVM-kiadások      | Linux (Ubuntu)   |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | A Horovod a Python 3,5-es verziójában van telepítve |
| Futtatás      | Aktiválja a megfelelő környezetet a terminálon, majd futtassa a Pythont. |

## <a name="kerashttpskerasio"></a>[Kerasz](https://keras.io/)

|    |           |
| ------------- | ------------- |
| Támogatott verzió (k) | 2.2.4 |
| Támogatott DSVM-kiadások      | Windows és Linux   |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | A kerasz a Windows Python 3,6-es verziójában, Linuxon pedig Python 3,5-ben van telepítve |
| Futtatás      | Aktiválja a megfelelő környezetet a terminálon, majd futtassa a Pythont. |

## <a name="microsoft-cognitive-toolkit-cntkhttpsdocsmicrosoftcomcognitive-toolkit"></a>[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/)

|    |           |
| ------------- | ------------- |
| Támogatott verzió (k) | 2.5.1 |
| Támogatott DSVM-kiadások      | Windows és Linux   |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | A CNTK a Python 3,6-es verzióban van telepítve a [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition) -ben és a Python 3,5-on [Linux](./dsvm-languages.md#python-linux-edition)rendszeren) |
| Futtatás      | Terminál Aktiválja a megfelelő környezetet, és futtassa a Pythont. <br/>Jupyter Kapcsolódjon a [Jupyter](provision-vm.md) vagy a [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), majd nyissa meg a CNTK könyvtárat a mintákhoz. |

## <a name="mxnethttpsmxnetapacheorg"></a>[MXNet](https://mxnet.apache.org/)
|    |           |
| ------------- | ------------- |
| Támogatott verzió (k) | 1.3.0 |
| Támogatott DSVM-kiadások      | Windows és Linux   |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | A MXNet `C:\dsvm\tools\mxnet`-ra van telepítve a Windows rendszeren, és `/dsvm/tools/mxnet` az Ubuntu rendszeren. A Python-kötések a [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition) -es Python 3,6-es verziójában, a [Linuxon](./dsvm-languages.md#python-linux-edition)pedig a Python 3,5-ban vannak telepítve, az R-kötések pedig az Ubuntu DSVM is szerepelnek. |
| Futtatás      | Terminál Aktiválja a megfelelő Conda-környezetet, majd futtassa a `import mxnet` parancsot. <br/>Jupyter Kapcsolódjon a [Jupyter](provision-vm.md#access-the-dsvm) vagy a [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), majd nyissa meg a mintákhoz tartozó `mxnet` könyvtárat. |

## <a name="mxnet-model-serverhttpsgithubcomawslabsmxnet-model-serverquick-start"></a>[MXNet-modell kiszolgálója](https://github.com/awslabs/mxnet-model-server#quick-start)

|    |           |
| ------------- | ------------- |
| Támogatott verzió (k) | 1.0.1 |
| Támogatott DSVM-kiadások      | Windows és Linux   |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | A MXNet Model Server a Python 3,6-es verzióra van telepítve a [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition) és a Python 3,5 [Linux](./dsvm-languages.md#python-linux-edition)rendszeren) |
| Futtatás      | Terminál Futtassa a `sudo systemctl stop jupyterhub` parancsot a JupyterHub szolgáltatás leállításához, mert mindkettő ugyanazt a portot figyeli. Ezután aktiválja a megfelelő Conda-környezetet, és futtassa `mxnet-model-server --start --models squeezenet=https://s3.amazonaws.com/model-server/model_archive_1.0/squeezenet_v1.1.mar` |

## <a name="nvidia-system-management-interface-nvidia-smihttpsdevelopernvidiacomnvidia-system-management-interface"></a>[NVidia rendszerkezelési felület (NVIDIA-SMI)](https://developer.nvidia.com/nvidia-system-management-interface)

|    |           |
| ------------- | ------------- |
| Támogatott verzió (k) |  |
| Támogatott DSVM-kiadások      | Windows és Linux   |
| Mi ez? | Eszköz NVIDIA GPU-tevékenység lekérdezése |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | @no__t – 0 a rendszer elérési útjában van. |
| Futtatás      | Egy **GPU-val rendelkező**virtuális gépen nyisson meg egy parancssort (Windows rendszeren) vagy egy terminált (Linuxon), majd futtassa a `nvidia-smi` parancsot. |

## <a name="pytorchhttpspytorchorg"></a>[PyTorch](https://pytorch.org/)

|    |           |
| ------------- | ------------- |
| Támogatott verzió (k) | 1.2.0 |
| Támogatott DSVM-kiadások      | Linux |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | A [Python 3,5](dsvm-languages.md#python-linux-edition)-es verzióra van telepítve. A minta Jupyter jegyzetfüzeteket tartalmaz, és a minták a/dsvm/Samples/pytorch. |
| Futtatás      | Terminál Aktiválja a megfelelő környezetet, majd futtassa a Pythont.<br/>@no__t – 0[JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine): Kapcsolódjon, majd nyissa meg a PyTorch könyvtárat a mintákhoz.  |

## <a name="tensorflowhttpswwwtensorfloworg"></a>[TensorFlow](https://www.tensorflow.org/)

|    |           |
| ------------- | ------------- |
| Támogatott verzió (k) | 1,13 |
| Támogatott DSVM-kiadások      | Windows, Linux |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | Telepítve a Python 3,5-es verziójában [Linux](dsvm-languages.md#python-linux-edition) és Python 3,6 rendszeren [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition) |
| Futtatás      | Terminál Aktiválja a megfelelő környezetet, majd futtassa a Pythont. <br/> Jupyter Kapcsolódjon a [Jupyter](provision-vm.md) vagy a [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), majd nyissa meg a TensorFlow könyvtárat a mintákhoz.   |

## <a name="tensorflow-servinghttpswwwtensorfloworgserving"></a>[TensorFlow-kiszolgáló](https://www.tensorflow.org/serving/)

|    |           |
| ------------- | ------------- |
| Támogatott verzió (k) | 1,12 |
| Támogatott DSVM-kiadások      | Linux |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | a tensorflow_model_server a terminálon érhető el. |
| Futtatás      |  A minták elérhetők [online](https://www.tensorflow.org/serving/).   |


## <a name="theanohttpsgithubcomtheanotheano"></a>[Theano](https://github.com/Theano/Theano)

|    |           |
| ------------- | ------------- |
| Támogatott verzió (k) | 1.0.3 |
| Támogatott DSVM-kiadások      | Linux |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  |A theano a Python 2,7 (_root_) és a Python 3,5 (_py35_) környezetben van telepítve. |
| Futtatás      |  Terminál Aktiválja a kívánt Python-verziót (root vagy py35), futtassa a Pythont, majd importálja a theano.<br/>Jupyter Válassza ki a Python 2,7 vagy a 3,5 kernelt, majd importálja a theano.  <br/>A legújabb matematikai kernel-függvénytári (MKL-) hibák megkerüléséhez először a következőképpen kell beállítania a MKL-szálat:<br/><br/>`export MKL_THREADING_LAYER=GNU`  |