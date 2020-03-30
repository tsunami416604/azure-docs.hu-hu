---
title: Deep Learning & AI keretrendszerek
titleSuffix: Azure Data Science Virtual Machine
description: Elérhető deep learning-keretrendszerek és eszközök az Azure Data Science virtuális gép.
keywords: adatelemzési eszközök, adatelemző virtuális gép, eszközök adatelemzéshez, linux adatelemzés
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: d8a5cf428f41b130e6faf68ac87a075c15211099
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270068"
---
# <a name="deep-learning-and-ai-frameworks-for-the-azure-data-science-vm"></a>Deep learning és AI keretrendszerek az Azure Data Science vm
A DSVM mélytanulási keretei az alábbiakban találhatók.

## <a name="caffe"></a>[Caffe](https://github.com/BVLC/caffe)

|    |           |
| ------------- | ------------- |
| Támogatott verzió(k) | |
| Támogatott DSVM-kiadások      | Linux (Ubuntu)     |
| Hogyan van beállítva / telepítve van a DSVM?  | Caffe telepítve `/opt/caffe`van a.   A minták `/opt/caffe/examples`a.|
| Hogyan kell futtatni      | az X2Go segítségével jelentkezzen be a virtuális gépre, majd indítson el egy új terminált, és írja be a következőket:<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>Megnyílik egy új böngészőablak mintajegyzetfüzetekkel. A bináris fájlok a /opt/caffe/build/install/bin kapcsolóba vannak telepítve.<br/><br/>A Caffe telepített verziója Python 2.7-es verziója szükséges, és nem működik a Python 3.5-tel, amely alapértelmezés szerint aktiválva van. A Python 2.7-re `source activate root` való váltáshoz futtassa az Anaconda-környezetre való váltáshoz.|    

## <a name="caffe2"></a>[Caffe2](https://github.com/caffe2/caffe2)

|    |           |
| ------------- | ------------- |
| Támogatott verzió(k) | |
| Támogatott DSVM-kiadások      | Linux (Ubuntu)     |
| Hogyan van beállítva / telepítve van a DSVM?  | Caffe2 telepítve van a [Python 2.7 (root) conda környezetben. |
| Hogyan kell futtatni      | Terminál: Indítsa el a Pythont, és importálja a Caffe2-t. <br/> * JupyterHub: [Csatlakozzon a JupyterHubhoz,](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)majd a Caffe2 könyvtárba lépjen a mintajegyzetfüzetek megkereséséhez. Egyes jegyzetfüzetek megkövetelik, hogy a Caffe2 gyökér a Python-kódban legyen beállítva; írja be a /opt/caffe2 kapcsolót. |

## <a name="chainer"></a>[Chainer](https://chainer.org/)

|    |           |
| ------------- | ------------- |
| Támogatott verzió(k) | 5.2 |
| Támogatott DSVM-kiadások      | Linux (Ubuntu)     |
| Hogyan van beállítva / telepítve van a DSVM?  | Chainer telepítve van python 3.5. |
| Hogyan kell futtatni      | Terminál: Aktiválja a Python 3.5-ös környezetet, futtassa `python`a és a . `import chainer` <br/> * JupyterHub: [Csatlakozzon a JupyterHubhoz,](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)majd lépjen a Chainer könyvtárba a mintajegyzetfüzetek megkereséséhez.| 

## <a name="cuda-cudnn-nvidia-driver"></a>[CUDA, cuDNN, NVIDIA illesztőprogram](https://developer.nvidia.com/cuda-toolkit)

|    |           |
| ------------- | ------------- |
| Támogatott verzió(k) | 10.0.130|
| Támogatott DSVM-kiadások      | Windows és Linux   |
| Hogyan van beállítva / telepítve van a DSVM?  |_nvidia-smi_ elérhető a rendszer útját.  |
| Hogyan kell futtatni      | Nyisson meg egy parancssort (Windows rendszeren) vagy egy terminált (Linux rendszeren), majd futtassa _az nvidia-smi parancsot._ |


## <a name="horovod"></a>[Horovod](https://github.com/uber/horovod)

|    |           |
| ------------- | ------------- |
| Támogatott verzió(k) | 0.16.1|
| Támogatott DSVM-kiadások      | Linux (Ubuntu)   |
| Hogyan van beállítva / telepítve van a DSVM?  | Horovod telepítve van python 3.5 |
| Hogyan kell futtatni      | Aktiválja a megfelelő környezetet a terminálon, majd futtassa a Pythont. |

## <a name="keras"></a>[Keras](https://keras.io/)

|    |           |
| ------------- | ------------- |
| Támogatott verzió(k) | 2.2.4 |
| Támogatott DSVM-kiadások      | Windows és Linux   |
| Hogyan van beállítva / telepítve van a DSVM?  | A Keras telepítve van a Python 3.6-ban Windows rendszeren és Python 3.5-ben Linux alatt |
| Hogyan kell futtatni      | Aktiválja a megfelelő környezetet a terminálon, majd futtassa a Pythont. |

## <a name="microsoft-cognitive-toolkit-cntk"></a>[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/)

|    |           |
| ------------- | ------------- |
| Támogatott verzió(k) | 2.5.1 |
| Támogatott DSVM-kiadások      | Windows és Linux   |
| Hogyan van beállítva / telepítve van a DSVM?  | CNTK telepítve van a Python 3.6 [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) és python 3.5 [Linux](./dsvm-tools-languages.md#python-linux-edition)) |
| Hogyan kell futtatni      | Terminál: Aktiválja a megfelelő környezetet, és futtassa a Pythont. <br/>Jupyter: Csatlakozzon a [Jupyter](provision-vm.md) hez vagy a [JupyterHubhoz,](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)majd nyissa meg a CNTK könyvtárat mintákhoz. |

## <a name="mxnet"></a>[MXNet](https://mxnet.apache.org/)
|    |           |
| ------------- | ------------- |
| Támogatott verzió(k) | 1.3.0 |
| Támogatott DSVM-kiadások      | Windows és Linux   |
| Hogyan van beállítva / telepítve van a DSVM?  | MXNet telepítve `C:\dsvm\tools\mxnet` van a `/dsvm/tools/mxnet` Windows és az Ubuntu. Python kötések vannak telepítve python 3.6 [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) és python 3.5 [Linux](./dsvm-tools-languages.md#python-linux-edition)) R kötések is szerepelnek az Ubuntu DSVM. |
| Hogyan kell futtatni      | terminál: aktiválja a megfelelő conda környezetben, majd fuss `import mxnet`. <br/>Jupyter: Csatlakozzon a [Jupyter](provision-vm.md#access-the-dsvm) hez vagy a `mxnet` [JupyterHubhoz,](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)majd nyissa meg a könyvtárat a mintákhoz. |

## <a name="mxnet-model-server"></a>[MXNet Model Server](https://github.com/awslabs/mxnet-model-server#quick-start)

|    |           |
| ------------- | ------------- |
| Támogatott verzió(k) | 1.0.1 |
| Támogatott DSVM-kiadások      | Windows és Linux   |
| Hogyan van beállítva / telepítve van a DSVM?  | Az MXNet Model Server telepítve van a Python 3.6-ban [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) rendszeren és Python 3.5-ben [Linuxon](./dsvm-tools-languages.md#python-linux-edition)) |
| Hogyan kell futtatni      | Terminál: `sudo systemctl stop jupyterhub` Futtassa először a JupyterHub szolgáltatás leállításához, mert mindkettő ugyanazon a porton figyel. Ezután aktiválja a megfelelő conda környezetet, és futtassa`mxnet-model-server --start --models squeezenet=https://s3.amazonaws.com/model-server/model_archive_1.0/squeezenet_v1.1.mar` |

## <a name="nvidia-system-management-interface-nvidia-smi"></a>[NVidia rendszerkezelő felület (nvidia-smi)](https://developer.nvidia.com/nvidia-system-management-interface)

|    |           |
| ------------- | ------------- |
| Támogatott verzió(k) |  |
| Támogatott DSVM-kiadások      | Windows és Linux   |
| Mire kell? | NVIDIA eszköz a GPU-tevékenység lekérdezéséhez |
| Hogyan van beállítva / telepítve van a DSVM?  | `nvidia-smi`a rendszer elérési útján van. |
| Hogyan kell futtatni      | A **GPU-val rendelkező**virtuális gépen nyisson meg egy parancssort (Windows rendszeren) `nvidia-smi`vagy egy terminált (Linux on), majd futtassa a parancsot. |

## <a name="pytorch"></a>[PyTorch](https://pytorch.org/)

|    |           |
| ------------- | ------------- |
| Támogatott verzió(k) | 1.2.0 (Ubuntu 16.04, Windows 2016), 1.4.0 (Ubuntu 18.04, Windows 2019) |
| Támogatott DSVM-kiadások      | Linux |
| Hogyan van beállítva / telepítve van a DSVM?  | Telepítve python [3.5](dsvm-tools-languages.md#python-linux-edition). A minta Jupyter notebookok szerepelnek, és a minták /dsvm/samples/pytorch.Sample Jupyter notebooks are included, and samples are in /dsvm/samples/pytorch. |
| Hogyan kell futtatni      | Terminál: Aktiválja a megfelelő környezetet, majd futtassa a Pythont.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine): Csatlakozás, majd nyissa meg a PyTorch könyvtárat a mintákat.  |

## <a name="tensorflow"></a>[TensorFlow](https://www.tensorflow.org/)

|    |           |
| ------------- | ------------- |
| Támogatott verzió(k) | 1.13 |
| Támogatott DSVM-kiadások      | Windows, Linux |
| Hogyan van beállítva / telepítve van a DSVM?  | Telepítve python [3.5-ben Linux](dsvm-tools-languages.md#python-linux-edition) és Python 3.6 [rendszeren Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) rendszeren |
| Hogyan kell futtatni      | Terminál: Aktiválja a megfelelő környezetet, majd futtassa a Pythont. <br/> * Jupyter: Csatlakozzon [a Jupyter](provision-vm.md) hez vagy a [JupyterHubhoz,](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)majd nyissa meg a TensorFlow könyvtárat a mintákhoz.   |

## <a name="tensorflow-serving"></a>[TensorFlow Serving](https://www.tensorflow.org/serving/)

|    |           |
| ------------- | ------------- |
| Támogatott verzió(k) | 1.12 |
| Támogatott DSVM-kiadások      | Linux |
| Hogyan van beállítva / telepítve van a DSVM?  | tensorflow_model_server a terminálon kapható. |
| Hogyan kell futtatni      |  A minták [elérhetők az interneten.](https://www.tensorflow.org/serving/)   |


## <a name="theano"></a>[Theano](https://github.com/Theano/Theano)

|    |           |
| ------------- | ------------- |
| Támogatott verzió(k) | 1.0.3 |
| Támogatott DSVM-kiadások      | Linux |
| Hogyan van beállítva / telepítve van a DSVM?  |Theano telepítve van a Python 2.7 _(root_), és a Python 3.5 _(py35_) környezetben. |
| Hogyan kell futtatni      |  Terminál: Aktiválja a kívánt Python-verziót (root vagy py35), futtassa a Pythont, majd importálja theano-t.<br/>* Jupyter: Válassza ki a Python 2.7 vagy 3.5 kernel, majd importálja Theano.  <br/>Egy legutóbbi matematikai rendszertár (MKL) hiba kerüléséhez először be kell állítania az MKL-szálréteget az alábbiak szerint:<br/><br/>`export MKL_THREADING_LAYER=GNU`  |