---
title: Deep learning & AI-keretrendszerek
titleSuffix: Azure Data Science Virtual Machines
description: Mély tanulási keretrendszerek és eszközök az Azure Data Science Virtual Machine, beleértve a TensorFlow, a PyTorch, a kerasz, a Cafe, a MXNet, a Horovod, a theano, a Chainer és más eszközöket.
keywords: adatelemzési eszközök, adatelemző virtuális gép, eszközök adatelemzéshez, linux adatelemzés
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/11/2019
ms.author: vijetaj
ms.openlocfilehash: adf6a68d6da4236874dce403bb4d7043087049b2
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69970366"
---
# <a name="deep-learning-and-ai-frameworks"></a>Mély tanulás és mesterségesintelligencia-keretrendszerek
A [Data Science Virtual Machine](https://aka.ms/dsvm) (DSVM) számos mély tanulási keretrendszert támogat, amelyek segítségével mesterséges intelligencia (AI) alkalmazásokat hozhat létre prediktív elemzési és kognitív képességekkel, például a képekkel és a nyelvekkel kapcsolatos ismeretekkel.

A DSVM elérhető mély tanulási keretrendszerek a következők:

+ TensorFlow
+ PyTorch
+ Keras
+ Caffe
+ Caffe2
+ Torch
+ MXNet Model Server
+ MXNet
+ Horovod
+ Theano
+ Chainer
+ Mély víz
+ NVIDIA SZÁMJEGY
+ NVIDIA-smi
+ TensorFlow-kiszolgáló
+ TensorRT
+ Microsoft Cognitive Toolkit

|DL&nbsp;-&nbsp;eszközöka&nbsp;DSVM|Windows|Linux|Használati&nbsp;megjegyzések|
|---------|:-------------------:|:------------------:|-----|
|[TensorFlow](https://www.tensorflow.org/) | Igen (Windows 2016) | Igen |A Python 3,5-es verzióra van telepítve a [Linux és a windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) , valamint a Python 3,6 [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition)rendszeren. A DSVM-ben a minta Jupyter jegyzetfüzetek is elérhetők.<br/><br/>**Futtatás**:<br/>* Terminál: aktiválja a megfelelő környezetet, majd futtassa a Pythont. <br/> Jupyter Kapcsolódjon a [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) vagy a [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), majd nyissa meg a TensorFlow könyvtárat a mintákhoz.  |
|[PyTorch](https://pytorch.org/)| Nem | Igen |A [Python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition)-es verzióra van telepítve. A minta Jupyter jegyzetfüzeteket tartalmaz, és a minták a/dsvm/Samples/pytorch.    <br/><br/>**Futtatás**<br/>* Terminál: aktiválja a megfelelő környezetet, majd futtassa a Pythont.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux): Kapcsolódjon, majd nyissa meg a PyTorch könyvtárat a mintákhoz.  |
|[Kerasz](https://keras.io/)| Igen | Igen |Az API-t a Python 3,5-es verziója telepíti a [Linux és a windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) és a Python 3,6 [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition)rendszeren. [Példák](https://github.com/fchollet/keras/tree/master/examples)<br/><br/>**Futtatás**:<br/>* Terminál: aktiválja a megfelelő környezetet, majd futtassa a Pythont. <br/> Jupyter Töltse le a mintákat a GitHub-helyről, kapcsolódjon a [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) vagy a [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), majd nyissa meg a minta könyvtárat. |
|[Caffe](https://github.com/caffe2/caffe2) | Nem |Igen (Ubuntu)|Caffe telepítve van a `/opt/caffe`.   A minták a `/opt/caffe/examples`következők:. <br/><br/>**A futtatásához**a X2Go használatával jelentkezzen be a virtuális gépre, majd indítson el egy új terminált, és írja be a következőt:<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>Mintafüzetek megnyílik egy új böngészőablakban. Bináris fájljai vannak telepítve a /opt/caffe/build/install/bin.<br/><br/>Caffe telepített verziója van szükség a Python 2.7-t, és a Python 3.5 alapértelmezés szerint aktiválva nem fog működni. A Python 2,7-re való váltáshoz futtassa a parancsot `source activate root` az anaconda-környezet váltásához.|
|[Caffe2](https://github.com/caffe2/caffe2) | Nem |Igen (Ubuntu)|Caffe2 telepítve van a [környezet a Python 2.7-es (root) conda](dsvm-languages.md#python-linux-and-windows-server-2012-edition). A forrás, a `/opt/caffe2`.<br/>Mintafüzetek JupyterHub szerepelnek.<br/><br/>**Futtatás**:<br/>* A terminálon: aktiválja a [legfelső szintű Python-környezetet](dsvm-languages.md#python-linux-and-windows-server-2012-edition), indítsa el a Python és caffe2 importálása. <br/> * A JupyterHub: [JupyterHub csatlakozni](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), majd keresse meg a Caffe2 könyvtárát mintafüzetek található. Néhány notebookok megkövetelése a Caffe2 legfelső szintű kell beállítani, a Python-kódban; Adja meg a /opt/caffe2. |
|[Fáklya](http://torch.ch/) | Nem |Igen (Ubuntu)|Torch telepítve van a `/dsvm/tools/torch`. PyTorch telepítve van a Python 2.7-es (_legfelső szintű_), valamint a Python 3.5-ös (_py35_) környezetben. A Torch-minták `/dsvm/samples/torch` a-ben és a `/dsvm/samples/pytorch`PyTorch-mintákban találhatók. |
|[MXNet](https://mxnet.io/) | Igen (Windows 2016) | Igen|MXNet telepítve van a `C:\dsvm\tools\mxnet` a Windows és `/dsvm/tools/mxnet` Linux rendszeren. Python-kötések vannak telepítve a Python 3.5 [Linux és Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) és a Python 3.6-os [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). R-kötések az ubuntu rendszeren is telepítve lesz.<br/><br/>Mintául szolgáló Jupyter notebookok szerepelnek. <br/><br/>**Futtatás**:<br/>Terminál Aktiválja a megfelelő környezetet, majd futtassa a Pythont. <br/> Jupyter Kapcsolódjon a [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) vagy a [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), majd nyissa meg a mxnet könyvtárat a mintákhoz.|
|[MXNet-modell kiszolgálója](https://github.com/awslabs/mxnet-model-server) | Nem | Igen |Egy kiszolgáló, amely HTTP-végpontokat hoz létre a MXNet és a ONNX modellekhez. _mxnet-modell-server_ érhető el a terminálon. Minták a [MXNet Model Server oldalon](https://github.com/awslabs/mxnet-model-server).|
|[Horovod](https://github.com/uber/horovod) | Nem | Igen (Ubuntu) |Elosztott mély tanulási keretrendszer a TensorFlow-hez. Horovod Python 3.5 települ az [Ubuntu](dsvm-languages.md#python-linux-and-windows-server-2012-edition).  [Minták megtekintése](https://github.com/uber/horovod/tree/master/examples)<br/><br/>A futtatásához aktiválja a megfelelő környezetet a terminálon, majd futtassa a Pythont. |
|[Theano](https://github.com/Theano/Theano) | Nem | Igen (Ubuntu) |Theano telepítve van a Python 2.7-es (_legfelső szintű_), valamint a Python 3.5-ös (_py35_) környezetben.<br/><br/>**Futtatás**: <br/>Terminál Aktiválja a kívánt Python-verziót (root vagy py35), futtassa a Pythont, majd importálja a theano.<br/>Jupyter Válassza ki a Python 2,7 vagy a 3,5 kernelt, majd importálja a theano.  <br/>Egy közelmúltbeli MKL hiba elkerüléséhez először állítsa a réteg threading MKL kell:<br/><br/>_export MKL_THREADING_LAYER=GNU_|
|[Chainert](https://chainer.org/) |Nem | Igen |Az telepítve van a chainer [Python 3.5-ös](dsvm-languages.md#python-linux-and-windows-server-2012-edition). ChainerRL és ChainerCV is telepítve lesz. <br/><br/>Mintafüzetek JupyterHub szerepelnek.<br/><br/>**Futtatás**: <br/>Terminál Aktiválja a [python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) környezetet, futtassaa Pythont, majd importálja a láncot. <br/> * JupyterHub: [Kapcsolódjon a JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), majd keresse meg a láncolt könyvtárat a minta jegyzetfüzetek kereséséhez.|
|[NVidia-számjegyek](https://github.com/NVIDIA/DIGITS) | Nem | Igen (Ubuntu) |Mély tanulási rendszer az NVIDIA-tól a mély tanulási modellek gyors betanításához. SZÁMJEGYEK telepítve van a `/dsvm/tools/DIGITS` érhető el, és egy szolgáltatás nevű _számjegyek_.  <br/><br/>**Futtatás**: <br/>Jelentkezzen be a virtuális Gépet a X2Go. A terminálon indítsa el a szolgáltatást ```sudo systemctl start digits```. <br/><br/>A szolgáltatás indítása körülbelül egy percig tart. Indítson el egy webböngészőt, és keresse meg a `http://localhost:5000`. Vegye figyelembe, hogy számjegyek nem biztosít egy biztonságos bejelentkezés előtt nem szerencsés felfedni a virtuális gép kívül.|
|[CUDA, cuDNN, NVIDIA-illesztőprogram](https://developer.nvidia.com/cuda-toolkit) |Igen | Igen | |
|NVIDIA-smi|Igen | Igen |NVIDIA-eszköz a GPU-tevékenységek lekérdezéséhez. _NVIDIA-smi_ érhető el a rendszerbeli elérési úton. <br/><br/>Indítsa el a parancssort (Windows) vagy (a Linux rendszeren) parancsot egy terminálban, majd futtassa _nvidia-smi_.|
|[TensorFlow-kiszolgáló](https://www.tensorflow.org/serving/) | Nem | Igen |Egy kiszolgáló, amely egy TensorFlow-modellen alapul. _tensorflow_model_server_ érhető el a terminálon. A minták elérhetők [online](https://www.tensorflow.org/serving/).|
|[TensorRT](https://developer.nvidia.com/tensorrt) |  Nem | Igen (Ubuntu) |Deep learning-NVIDIA következtetésekhez kiszolgálót. TensorRT van telepítve egy _apt_ csomagot. A minták elérhetők [online](https://docs.nvidia.com/deeplearning/sdk/tensorrt-developer-guide/index.html#samples).|
|[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/)|Igen | Igen | A Python 3,5-es verzióra van telepítve a [Linux és a windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) , valamint a Python 3,6 [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition)rendszeren. A DSVM-ben a minta Jupyter jegyzetfüzetek is elérhetők. <br/><br/>**Futtatás**: <br/>Terminál Aktiválja a megfelelő környezetet, és futtassa a Pythont. <br/>Jupyter Kapcsolódjon a [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) vagy a [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), majd nyissa meg a CNTK könyvtárat a mintákhoz. |
|Mély víz|Nem | Igen (Ubuntu) |Mély tanulási keretrendszer a H2O-hez, a Deep Water a [Python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) -es verzióban `/dsvm/tools/deep_water`érhető el, és a-ben is elérhető. Mintafüzetek JupyterHub szerepelnek. Mély víz CUDA 8 cuDNN 5.1-a van szükség. Ez a nem alapértelmezés szerint a könyvtár elérési úton egyéb deep learning-keretrendszerek használja a CUDA 9 és cuDNN 7. Mély víz CUDA 8 + cuDNN 5.1 használata:<br/><br/>```export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:${LD_LIBRARY_PATH}```<br/>```export CUDA_ROOT=/usr/local/cuda-8.0```<br/><br/>Mély víz használata:<br/>* Terminál: aktiválja a [python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) környezetet, majd futtassaa Pythont. <br/>* JupyterHub: [kapcsolódjon a JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), majd keresse meg a deep_water könyvtárat, és keresse meg a minta jegyzetfüzeteket.|
