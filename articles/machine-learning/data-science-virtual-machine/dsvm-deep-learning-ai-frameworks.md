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
ms.openlocfilehash: fd38bf1f7741c4d610ef43a12d90533d4ac7b703
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802412"
---
# <a name="deep-learning-and-ai-frameworks-for-the-azure-data-science-vm"></a>Mély tanulási és AI-keretrendszerek az Azure Data Science VM
Az alábbi lista mély tanulási keretrendszerek a DSVM.

## <a name="caffehttpsgithubcombvlccaffe"></a>[Caffe](https://github.com/BVLC/caffe)

|    |           |
| ------------- | ------------- |
| Támogatott verzió (k) | |
| Támogatott DSVM-kiadások      | Linux (Ubuntu)     |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | Caffe telepítve van a `/opt/caffe`.   A minták a `/opt/caffe/examples`következők:.|
| Futtatás      | a X2Go használatával jelentkezzen be a virtuális gépre, majd indítson el egy új terminált, és adja meg a következőt:<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>Mintafüzetek megnyílik egy új böngészőablakban. Bináris fájljai vannak telepítve a /opt/caffe/build/install/bin.<br/><br/>A Cafe telepített verziójának a Python 2,7-es verziójára van szüksége, és a Python 3,5 nem működik, amely alapértelmezés szerint aktiválva van. A Python 2,7-re való váltáshoz futtassa a parancsot `source activate root` az anaconda-környezetre való váltáshoz.|    

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

## <a name="pytorchhttpspytorchorg"></a>[PyTorch](https://pytorch.org/)

|    |           |
| ------------- | ------------- |
| Támogatott verzió (k) | 1.2.0 |
| Támogatott DSVM-kiadások      | Linux |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | A [Python 3,5](dsvm-languages.md#python-linux-edition)-es verzióra van telepítve. A minta Jupyter jegyzetfüzeteket tartalmaz, és a minták a/dsvm/Samples/pytorch. |
| Futtatás      | Terminál Aktiválja a megfelelő környezetet, majd futtassa a Pythont.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine): Kapcsolódjon, majd nyissa meg a PyTorch könyvtárat a mintákhoz.  |

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