---
title: Az ONNX- és az Azure Machine Learning |} Hozzon létre és helyezhet üzembe modelleket
description: Ismerje meg az ONNX és létrehozása és üzembe helyezése az ONNX-modellek Azure Machine Learning használata
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 09/24/2018
ms.openlocfilehash: 190b7fff24c9d6b3dee86471b56ad68c962e51ce
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49116878"
---
# <a name="onnx-and-azure-machine-learning-create-and-deploy-interoperable-ai-models"></a>ONNX és az Azure Machine Learning: hozzon létre és együttműködésre AI-modellek üzembe helyezése

A [nyissa meg a Neurális hálózat Exchange](http://onnx.ai) (ONNX) formátuma nyílt szabvány, amely a machine learning-modellek jelző. ONNX által támogatott egy [partnerek közösségi](http://onnx.ai/supported-tools), beleértve a Microsoft, akik kompatibilis keretrendszerek és eszközök létrehozása. Microsoft elkötelezett a nyílt forráskódú és együttműködésre AI, így az adatelemzők és fejlesztők is:

+ A választott eszközükön keretrendszer használatával modellek létrehozása és betanítása
+ Modellek platformok közötti integráció minimális munkahelyi üzembe helyezése

A Microsoft támogatja az ONNX között a termékek, köztük az Azure és a Windows segítséget ezen célok eléréséhez.  

## <a name="why-choose-onnx"></a>Miért érdemes az ONNX választani?
Az ONNX-kap együttműködést nagyszerű ötleteket meríthet éles gyorsabban lehetővé teszi. Az ONNX az adatszakértők választhat az előnyben részesített keretrendszer, a feladat. Hasonlóképpen, a fejlesztők Felkészülés a modellek éles kevesebb időt, és üzembe helyezése a felhőben és a peremhálózati.  

ONNX-modellekkel számos keretrendszereket PyTorch, Chainer, a Microsoft Cognitive Toolkit (CNTK), MXNet, ML.Net, TensorFlow, Keras, SciKit-ismerje meg, és további is létrehozhat.

Jelenítenek meg, és felgyorsítja az ONNX-modellekkel eszközök ökoszisztémája is van. Előre betanított ONNX-modellekkel számos gyakori szituációhoz kínál is elérhetők.

[ONNX-modellekkel is üzembe helyezhetők](#deploy) a felhőbe az Azure Machine Learning és az ONNX-futtatókörnyezet. A Windows 10-eszközökön is telepíthető [Windows ML](https://docs.microsoft.com/windows/ai/). Akkor is telepíthetők legyenek más platformokon elérhető az ONNX-Közösségből származó átalakítók használatával. 

[ ![Képzés, konverterek és telepítési folyamatábrája ONNX](media/concept-onnx/onnx.png) ] (. / media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>ONNX-modellekkel beolvasása

ONNX-modellekkel többféle módon szerezheti be:
+ Az ONNX előre betanított modell beolvasása a [ONNX modell állatkert](https://github.com/onnx/models) (lásd: a példában ez a cikk alján)
+ Hozzon létre egy testre szabott ONNX-modellt a [Azure Custom Vision service](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) 
+ Létező modell más formátumba való átalakítása ONNX (lásd: a példában ez a cikk alján) 
+ Az Azure Machine Learning szolgáltatásban egy új ONNX-modell betanításához (lásd: a példában ez a cikk alján)

## <a name="saveconvert-your-models-to-onnx"></a>Az ONNX-modellek Save/convert

Meglévő modellek átalakíthatók ONNX, vagy a tanítási végén ONNX mentheti őket.

|Modell-keretrendszer|Átalakítás például vagy eszköz|
|-----|-------|
|PyTorch|[Jupyter notebook](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb)|
|A Microsoft&nbsp;Cognitive&nbsp;eszközkészlet&nbsp;(CNTK)|[Jupyter notebook](https://github.com/onnx/tutorials/blob/master/tutorials/CntkOnnxExport.ipynb)|
|TensorFlow|[tensorflow-onnx-átalakító](https://github.com/onnx/tensorflow-onnx)|
|Chainer|[Jupyter notebook](https://github.com/onnx/tutorials/blob/master/tutorials/ChainerOnnxExport.ipynb)|
|MXNet|[Jupyter notebook](https://github.com/onnx/tutorials/blob/master/tutorials/MXNetONNXExport.ipynb)|
|Keras, ScitKit további CoreML<br/>XGBoost és libSVM|[WinMLTools](https://docs.microsoft.com/windows/ai/convert-model-winmltools)|

A támogatott keretrendszerek és található konverterek legújabb listáját megtalálhatja az [ONNX-oktatóanyagok hely](https://github.com/onnx/tutorials).

<a name="deploy"></a>

## <a name="deploy-onnx-models-in-azure"></a>Az Azure-ban az ONNX-modellek üzembe helyezése

Az Azure Machine Learning szolgáltatás üzembe helyezése, kezelése és figyelése az ONNX-modellekkel. A standard használatával [üzembe helyezést megvalósító munkafolyamat](concept-model-management-and-deployment.md) az ONNX-futtatókörnyezet, hozhat létre a felhőben üzemeltetett REST-végponton. Egy teljes példa Jupyter notebookot, próbálja ki saját maga számára ez a cikk végén talál. 

### <a name="install-and-configure-the-onnx-runtime"></a>Telepítse és konfigurálja az ONNX-futtatókörnyezet

Az ONNX-modul az egy nagy teljesítményű következtetésekhez motor az ONNX-modellekkel. Tartalmaz egy Python API-t és a hardveres gyorsítás Ez a Processzor és GPU is. Jelenleg támogatja az 1.2-es ONNX-modellek és Ubuntu 16.04 linuxon futtatja. Mindkét [CPU](https://pypi.org/project/onnxruntime) és [GPU](https://pypi.org/project/onnxruntime-gpu) csomagok elérhetők az [PyPi.org](https://pypi.org).

Az ONNX-modul telepítéséhez használja:
```python
pip install onnxruntime
```

Az ONNX-futtatókörnyezet hívja meg a Python-szkript, használja:
```python
import onnxruntime

session = onnxruntime.InferenceSession("path to model")
```

A dokumentáció a modell általában kísérő kiderül, hogy a bemeneteit és kimeneteit a modell használatával. Például egy vizualizációs eszköz is használható [Netron](https://github.com/lutzroeder/Netron) a modell megtekintéséhez. Az ONNX-modul is lehetővé teszi a modell metaadatainak lekérdezés, bemenetek és kimenetek:
```python
session.get_modelmeta()
first_input_name = session.get_inputs()[0].name
first_output_name = session.get_outputs()[0].name
```

A következtetésekhez a modellt, a használat `run` és kimenetei visszaadott (hagyja üres, ha azt szeretné, hogy ezek mindegyike) és a egy térképet, a bemeneti értékek listáját adja át. Ez a kimenetek listáját.
```python
results = session.run(["output1", "output2"], {"input1": indata1, "input2": indata2})
results = session.run([], {"input1": indata1, "input2": indata2})
```

A teljes körű API-referencia, lásd: a [ONNX-futtatókörnyezet referenciadokumentumok](https://aka.ms/onnxruntime-python).

### <a name="example-deployment-steps"></a>A példában az üzembe helyezés lépései

Íme egy példa az ONNX-modell üzembe helyezéséhez:

1. Az Azure Machine Learning szolgáltatás munkaterület inicializálása. Ha Ön még nem rendelkezik, ismerje meg, hogyan hozhat létre egy munkaterületet a [ebben a rövid útmutatóban](quickstart-get-started.md).

   ```python
   from azureml.core import Workspace

   ws = Workspace.from_config()
   print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')
   ```

2. Regisztrálja a modellt az Azure Machine Learning.

   ```python
   from azureml.core.model import Model

   model = Model.register(model_path = "model.onnx",
                          model_name = "MyONNXmodel",
                          tags = ["onnx"],
                          description = "test",
                          workspace = ws)
   ```

3. Rendszerkép létrehozása a modell és a függőségek.

   ```python
   from azureml.core.image import ContainerImage
   
   image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                     runtime = "python",
                                                     conda_file = "myenv.yml",
                                                     description = "test",
                                                     tags = ["onnx"]
                                                    )

   image = ContainerImage.create(name = "myonnxmodelimage",
                                 # this is the model object
                                 models = [model],
                                 image_config = image_config,
                                 workspace = ws)

   image.wait_for_creation(show_output = True)
   ```

   A fájl `score.py` a pontozási logikát tartalmaz, és fel kell venni a képen. Ez a fájl a lemezkép a modell futtatására szolgál. Ez [oktatóanyag](tutorial-deploy-models-with-aml.md#create-scoring-script) való hozzon létre egy pontozó szkript esetében. Alább látható egy példa fájlt az ONNX-modell:

   ```python
   import onnxruntime
   import json
   import numpy as np
   import sys
   from azureml.core.model import Model

   def init():
       global model_path
       model_path = Model.get_model_path(model_name = 'MyONNXmodel')

   def run(raw_data):
       try:
           data = json.loads(raw_data)['data']
           data = np.array(data)
        
           sess = onnxruntime.InferenceSession(model_path)
           result = sess.run(["outY"], {"inX": data})
        
           return json.dumps({"result": result.tolist()})
       except Exception as e:
           result = str(e)
           return json.dumps({"error": result})
   ```

   A fájl `myenv.yml` a lemezkép szükséges függőségeit ismerteti. Ez [oktatóanyag](tutorial-deploy-models-with-aml.md#create-environment-file) hozzon létre egy környezetben fájlt például ezt a mintafájlt útmutatást:

   ```python
   from azureml.core.conda_dependencies import CondaDependencies 

   myenv = CondaDependencies()
   myenv.add_pip_package("numpy")
   myenv.add_pip_package("azureml-core")
   myenv.add_pip_package("onnxruntime")

   with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())
   ```

4. Az Azure Machine Learning, a ONNX modell üzembe helyezése:
   + Az Azure Container Instances (aci Szolgáltatásban): [megtudhatja, hogyan...](how-to-deploy-to-aci.md)

   + Az Azure Kubernetes Service (AKS): [megtudhatja, hogyan...](how-to-deploy-to-aks.md)


## <a name="examples"></a>Példák
 
A következő notebookok ONNX-modellek létrehozása és üzembe helyezhetik azokat az Azure Machine Learning bemutatása: 
+ `/onnx/onnx-modelzoo-aml-deploy-resnet50.ipynb` 
+ `/onnx/onnx-convert-aml-deploy-tinyyolo.ipynb`
+ `/onnx/onnx-train-pytorch-aml-deploy-mnist.ipynb`

A következő notebookok bemutatják, hogyan lehet az Azure Machine Learning meglévő ONNX-modellek üzembe helyezése: 
+ [onnx/onnx-következtetésekhez-mnist.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/onnx/onnx-inference-mnist.ipynb) 
+ [onnx/onnx-következtetésekhez – emotion-recognition.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/onnx/onnx-inference-emotion-recognition.ipynb)
 
Ezeket a notebookokat lekérése:
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>További információ

További információ az ONNX vagy járulnak hozzá a projekthez:
+ [Az ONNX-projekt webhelyének](http://onnx.ai)

+ [Az ONNX-kódját a Githubon](https://github.com/onnx/onnx)
