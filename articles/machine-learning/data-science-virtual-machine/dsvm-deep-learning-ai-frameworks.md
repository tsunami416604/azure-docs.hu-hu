---
title: Deep learning & AI-keretrendszerek
titleSuffix: Azure Data Science Virtual Machine
description: Az Azure Data Science Virtual Machine-ban elérhető mély tanulási keretrendszerek és eszközök, beleértve a TensorFlow, a PyTorch, a kerasz, a Cafe, a MXNet, a Horovod, a theano, a Chainer és más eszközöket.
keywords: adatelemzési eszközök, adatelemző virtuális gép, eszközök adatelemzéshez, linux adatelemzés
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 08/11/2019
ms.openlocfilehash: f71a2cc5c0a430037b429aac4f3ec4dc5f5f933d
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208103"
---
# <a name="deep-learning-and-ai-frameworks-for-azure-data-science-vm"></a>Deep learning-és AI-keretrendszerek az Azure Data Science VM
A [Data Science Virtual Machine](https://aka.ms/dsvm) (DSVM) számos mélyreható tanulási keretrendszert támogat, amelyek segítségével mesterséges intelligencia (AI) alkalmazásokat hozhat létre prediktív elemzési és kognitív képességekkel, például a képekkel és a nyelvekkel kapcsolatos ismeretekkel.

A DSVM-en keresztül elérhető mély tanulási keretrendszerek többek között a következők:

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

|A&nbsp;&nbsp;DSVMmélyrehatótanulási&nbsp;eszközei|Windows|Linux|Használati&nbsp;megjegyzések|
|---------|-------------------|------------------|-----|
|[TensorFlow](https://www.tensorflow.org/) | Igen (Windows 2016) | Igen |A Python 3,5-es verzióra van telepítve a [Linux és a windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) , valamint a Python 3,6 [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition)rendszeren. A DSVM-ben a minta Jupyter jegyzetfüzetek is elérhetők.<br/><br/>**Futtatás**:<br/>Terminál Aktiválja a megfelelő környezetet, majd futtassa a Pythont. <br/> Jupyter Kapcsolódjon a [Jupyter](provision-vm.md) vagy a [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), majd nyissa meg a TensorFlow könyvtárat a mintákhoz.  |
|[PyTorch](https://pytorch.org/)| Nem | Igen |A [Python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition)-es verzióra van telepítve. A minta Jupyter jegyzetfüzeteket tartalmaz, és a minták a/dsvm/Samples/pytorch.    <br/><br/>**Futtatás**:<br/>Terminál Aktiválja a megfelelő környezetet, majd futtassa a Pythont.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux): Kapcsolódjon, majd nyissa meg a PyTorch könyvtárat a mintákhoz.  |
|[Kerasz](https://keras.io/)| Igen | Igen |Az API a Python 3,5-es verzióra van telepítve a [Linux és a windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) rendszeren, valamint a Python 3,6-ben [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition)rendszeren. [Lásd: minták](https://github.com/fchollet/keras/tree/master/examples).<br/><br/>**Futtatás**:<br/>Terminál Aktiválja a megfelelő környezetet, majd futtassa a Pythont. <br/> Jupyter Töltse le a mintákat a GitHub-helyről, kapcsolódjon a [Jupyter](provision-vm.md) vagy a [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), majd nyissa meg a minta könyvtárat. |
|[Caffe](https://github.com/caffe2/caffe2) | Nem |Igen (Ubuntu)|Caffe telepítve van a `/opt/caffe`.   A minták a `/opt/caffe/examples`következők:. <br/><br/>A futtatásához a X2Go használatával jelentkezzen be a virtuális gépre, majd indítson el egy új terminált, és adja meg a következőt:<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>Mintafüzetek megnyílik egy új böngészőablakban. Bináris fájljai vannak telepítve a /opt/caffe/build/install/bin.<br/><br/>A Cafe telepített verziójának a Python 2,7-es verziójára van szüksége, és a Python 3,5 nem működik, amely alapértelmezés szerint aktiválva van. A Python 2,7-re való váltáshoz futtassa a parancsot `source activate root` az anaconda-környezetre való váltáshoz.|
|[Caffe2](https://github.com/caffe2/caffe2) | Nem |Igen (Ubuntu)|Caffe2 telepítve van a [környezet a Python 2.7-es (root) conda](dsvm-languages.md#python-linux-and-windows-server-2012-edition). A forrás, a `/opt/caffe2`.<br/>Mintafüzetek JupyterHub szerepelnek.<br/><br/>**Futtatás**:<br/>Terminál Aktiválja a [legfelső szintű Python-környezetet](dsvm-languages.md#python-linux-and-windows-server-2012-edition), indítsa el a Pythont, és importálja a Caffe2. <br/> * JupyterHub: [Kapcsolódjon a JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)-hez, majd nyissa meg a Caffe2 könyvtárat a minta jegyzetfüzetek kereséséhez. Néhány notebookok megkövetelése a Caffe2 legfelső szintű kell beállítani, a Python-kódban; Adja meg a /opt/caffe2. |
|[Fáklya](http://torch.ch/) | Nem |Igen (Ubuntu)|Torch telepítve van a `/dsvm/tools/torch`. PyTorch telepítve van a Python 2.7-es (_legfelső szintű_), valamint a Python 3.5-ös (_py35_) környezetben. A Torch-minták `/dsvm/samples/torch` a-ben és a `/dsvm/samples/pytorch`PyTorch-mintákban találhatók. |
|[MXNet](https://mxnet.io/) | Igen (Windows 2016) | Igen|MXNet telepítve van a `C:\dsvm\tools\mxnet` a Windows és `/dsvm/tools/mxnet` Linux rendszeren. Python-kötések vannak telepítve a Python 3.5 [Linux és Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) és a Python 3.6-os [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). R-kötések az ubuntu rendszeren is telepítve lesz.<br/><br/>Mintául szolgáló Jupyter notebookok szerepelnek. <br/><br/>**Futtatás**:<br/>Terminál Aktiválja a megfelelő környezetet, majd futtassa a Pythont. <br/> Jupyter Kapcsolódjon a [Jupyter](provision-vm.md) vagy a [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), majd nyissa meg a mxnet könyvtárat a mintákhoz.|
|[MXNet-modell kiszolgálója](https://github.com/awslabs/mxnet-model-server) | Nem | Igen |Egy kiszolgáló, amely HTTP-végpontokat hoz létre a MXNet és a ONNX modellekhez. A _Mxnet-Model-Server_ elérhető a terminálon. Minták a [MXNet Model Server oldalon](https://github.com/awslabs/mxnet-model-server).|
|[Horovod](https://github.com/uber/horovod) | Nem | Igen (Ubuntu) |Elosztott TensorFlow-keretrendszer. Horovod Python 3.5 települ az [Ubuntu](dsvm-languages.md#python-linux-and-windows-server-2012-edition).  [Lásd: minták](https://github.com/uber/horovod/tree/master/examples).<br/><br/>A futtatásához aktiválja a megfelelő környezetet a terminálon, majd futtassa a Pythont. |
|[Theano](https://github.com/Theano/Theano) | Nem | Igen (Ubuntu) |A theano a Python 2,7 (_root_) és a Python 3,5 (_py35_) környezetben van telepítve.<br/><br/>**Futtatás**: <br/>Terminál Aktiválja a kívánt Python-verziót (root vagy py35), futtassa a Pythont, majd importálja a theano.<br/>Jupyter Válassza ki a Python 2,7 vagy a 3,5 kernelt, majd importálja a theano.  <br/>A legújabb matematikai kernel-függvénytári (MKL-) hibák megkerüléséhez először a következőképpen kell beállítania a MKL-szálat:<br/><br/>`export MKL_THREADING_LAYER=GNU`|
|[Chainert](https://chainer.org/) |Nem | Igen |Az telepítve van a chainer [Python 3.5-ös](dsvm-languages.md#python-linux-and-windows-server-2012-edition). ChainerRL és ChainerCV is telepítve lesz. <br/><br/>Mintafüzetek JupyterHub szerepelnek.<br/><br/>**Futtatás**: <br/>Terminál Aktiválja a [python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) környezetet, futtassaa Pythont, majd importálja a láncot. <br/> * JupyterHub: [Kapcsolódjon a JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), majd nyissa meg a láncolt könyvtárat a minta jegyzetfüzetek kereséséhez.|
|[NVidia-számjegyek](https://github.com/NVIDIA/DIGITS) | Nem | Igen (Ubuntu) |Mélyreható tanulási rendszer az NVIDIA-tól a mélyreható tanulási modellek gyors betanításához. A (z) `/dsvm/tools/DIGITS` és a (z) számjegyeka (z) rendszerbe vannak telepítve, és elérhetők  <br/><br/>**Futtatás**: <br/>Jelentkezzen be a virtuális gépre a X2Go használatával. A terminálon indítsa el a szolgáltatást a futtatásával ```sudo systemctl start digits```. <br/><br/>A szolgáltatás indítása körülbelül egy percig tart. Nyisson meg egy webböngészőt, `http://localhost:5000`és lépjen a következőre:. Vegye figyelembe, hogy számjegyek nem biztosít egy biztonságos bejelentkezés előtt nem szerencsés felfedni a virtuális gép kívül.|
|[CUDA, cuDNN, NVIDIA-illesztőprogram](https://developer.nvidia.com/cuda-toolkit) |Igen | Igen | |
|NVIDIA-smi|Igen | Igen |NVIDIA-eszköz a GPU-tevékenységek lekérdezéséhez; az _NVIDIA-SMI_ a rendszer elérési útján érhető el. <br/><br/>Nyisson meg egy parancssort (Windows rendszeren) vagy egy terminált (Linuxon), majd futtassa az _NVIDIA-SMI_parancsot.|
|[TensorFlow-kiszolgáló](https://www.tensorflow.org/serving/) | Nem | Igen |Egy TensorFlow-modellen következtetni kívánt kiszolgáló; a tensorflow_model_server a terminálon érhető el. A minták elérhetők [online](https://www.tensorflow.org/serving/).|
|[TensorRT](https://developer.nvidia.com/tensorrt) |  Nem | Igen (Ubuntu) |Mély tanulási következtetési kiszolgáló az NVIDIA-ból. TensorRT van telepítve egy _apt_ csomagot. A minták elérhetők [online](https://docs.nvidia.com/deeplearning/sdk/tensorrt-developer-guide/index.html#samples).|
|[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/)|Igen | Igen | A Python 3,5-es verzióra van telepítve a [Linux és a windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) , valamint a Python 3,6 [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition)rendszeren. A DSVM-ben a minta Jupyter jegyzetfüzetek is elérhetők. <br/><br/>**Futtatás**: <br/>Terminál Aktiválja a megfelelő környezetet, és futtassa a Pythont. <br/>Jupyter Kapcsolódjon a [Jupyter](provision-vm.md) vagy a [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), majd nyissa meg a CNTK könyvtárat a mintákhoz. |
|Mély víz|Nem | Igen (Ubuntu) |Mély tanulási keretrendszer a H2O-hez, a Deep Water a [Python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) -es verzióban érhető `/dsvm/tools/deep_water`el, és a-ben is elérhető. Mintafüzetek JupyterHub szerepelnek. Mély víz CUDA 8 cuDNN 5.1-a van szükség. Alapértelmezés szerint ez nem a könyvtár elérési útjában van, mert más mély tanulási keretrendszerek a CUDA 9 és a cuDNN 7 használatát használják. A használatához telepítse a CUDA 8 + cuDNN 5,1-et a Deep Water számára:<br/><br/>```export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:${LD_LIBRARY_PATH}```<br/>```export CUDA_ROOT=/usr/local/cuda-8.0```<br/><br/>Mély víz használata:<br/>Terminál Aktiválja a [python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) környezetet, majd futtassa a _Pythont_. <br/>* JupyterHub: [Kapcsolódjon a JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)-hez, majd nyissa meg a deep_water könyvtárat a minta jegyzetfüzetek kereséséhez.|
