---
title: Nagy teljesítményű, platformfüggetlen ONNX-platform következtetésekhez
titleSuffix: Azure Machine Learning service
description: Ismerje meg az ONNX és a modellek felgyorsítása az ONNX modul
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 04/24/2019
ms.custom: seodec18
ms.openlocfilehash: 6bda1af8095e7026c79b7d8ffe45b39f6cbb3508
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028695"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX és az Azure Machine Learning: Hozzon létre és gépi Tanulási modelleket felgyorsítása

Ismerje meg, hogyan használatával a [nyílt Neurális hálózat Exchange](https://onnx.ai) (ONNX) segítségével optimalizálhatja a gépi tanulási modellek.

Optimalizálás machine learning-modellek következtetésekhez nehézséget jelent, mivel a modell és a következtetésekhez könyvtárat a legtöbbet hozhatja ki a hardveres képességeitől hangolása kell. A probléma akkor rendkívül nehéz, ha azt szeretné, különböző platformokra (felhőalapú és peremhálózati, Processzor és GPU, stb.) az optimális teljesítmény eléréséhez, minden egyes óta egy olyan különböző lehetőségeket és jellemzőit. Összetettségét növeli, ha modellek számos különféle keretrendszer, amely a különböző platformok futtatnia kell. Legyen nagyon sok időt vesz igénybe a különböző kombinációkkal, keretrendszerek és a hardver optimalizálása érdekében. Egy megoldás betanításához egyszer az előnyben részesített keretrendszerben, és bárhol futtathatók a felhőben vagy a peremhálózaton szükséges. Ez az, ahol az ONNX érhető el.

A Microsoft és partnerei Közössége létrehozott ONNX nyílt szabvány, amely a machine learning-modellek jelző. A modellek [számos keretrendszer](https://onnx.ai/supported-tools) TensorFlow, PyTorch, SciKit-ismerje meg, Keras, Chainer, MXNet és MATLAB exportálható vagy a standard ONNX-formátumra alakítja át. Miután a modellek az ONNX-formátumra, azokat a különböző platformokon és eszközökön futtatható.

[Az ONNX-futtatókörnyezet](https://github.com/Microsoft/onnxruntime) egy nagy teljesítményű következtetésekhez motor éles környezetben történő üzembe helyezéshez a ONNX-modellekkel. Felhő és a peremhálózat van optimalizálva, és a Linux, Windows és Mac rendszereken Írt C++, C, a Python, is rendelkezik, és C# API-k. ONNX-futtatókörnyezet támogatást nyújt az összes, az ONNX-ML-specifikáció, és együttműködik a megoldásgyorsítók például TensorRT NVidia gpu-kat a különböző hardveren.

Az ONNX-futtatókörnyezet nagy skálázást a Microsoft Services a Bing, az Office és a Cognitive Services például szolgál. Teljesítménynövekedést számos tényezőtől függ, de ezek a Microsoft a szolgáltatások látott egy __2 x teljesítmény nyereség processzor átlagos__. Az ONNX-modul is szolgál a több száz millió eszközre Windows ML részeként. A modul az Azure Machine Learning-szolgáltatások is használhatja. Az ONNX-modul segítségével élvezheti a széles körű termelési szintű optimalizálást, és a folyamatos fejlesztések.

[![Képzés, a feldolgozók és a központi telepítési ONNX folyamatábrája](media/concept-onnx/onnx.png) ](./media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>ONNX-modellekkel beolvasása

ONNX-modellekkel többféle módon szerezheti be:
+ Az Azure Machine Learning szolgáltatásban egy új ONNX-modell betanításához (lásd a példákat, ez a cikk alján)
+ Létező modell más formátumba való átalakítása ONNX (lásd a [oktatóanyagok](https://github.com/onnx/tutorials)) 
+ Az ONNX előre betanított modell beolvasása a [ONNX modell állatkert](https://github.com/onnx/models) (lásd a példákat, ez a cikk alján)
+ Hozzon létre egy testre szabott ONNX-modellt a [Azure Custom Vision service](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) 

Képek besorolása, objektumdetektálás és szöveges feldolgozási számos modellt is reprezentovat jako ONNX-modellekkel. Azonban egyes modellek nem lehet sikeresen konvertálni. Ha ez a helyzet, nyújtson be egy problémát a github, a megfelelő konverter használt. Továbbra is a meglévő formátum modell használatával, mindaddig, amíg a kérdést.

## <a name="deploy-onnx-models-in-azure"></a>Az Azure-ban az ONNX-modellek üzembe helyezése

Az Azure Machine Learning szolgáltatás üzembe helyezése, kezelése és figyelése az ONNX-modellekkel. A standard használatával [üzembe helyezést megvalósító munkafolyamat](concept-model-management-and-deployment.md) ONNX-futtatókörnyezet, hozhat létre a felhőben üzemeltetett REST-végponton. Lásd: a példában Jupyter notebookok próbálhatja ki saját maga számára ez a cikk végén található. 

### <a name="install-and-use-onnx-runtime-with-python"></a>Telepítése és használata az ONNX-modul Python

Az ONNX-modul Python-csomagok elérhetők az [PyPi.org](https://pypi.org) ([CPU](https://pypi.org/project/onnxruntime), [GPU](https://pypi.org/project/onnxruntime-gpu)). Kérjük, olvassa el [rendszerkövetelmények](https://github.com/Microsoft/onnxruntime#system-requirements) telepítés előtt. 

 Pythonhoz készült ONNX-modul telepítéséhez használja a következő parancsok egyikét: 
```python   
pip install onnxruntime       # CPU build
pip install onnxruntime-gpu   # GPU build
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

A teljes Python API-referencia, lásd: a [ONNX-futtatókörnyezet referenciadokumentumok](https://aka.ms/onnxruntime-python).    

## <a name="examples"></a>Példák

Lásd: [útmutatóval-to-használat-azureml/üzembe helyezés/onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx) létrehozásáért és ONNX-modellekkel például jegyzetfüzeteket.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>További információ

További információ az ONNX vagy járulnak hozzá a projekthez:
+ [Az ONNX-projekt webhelyének](https://onnx.ai)
+ [Az ONNX-kódját a Githubon](https://github.com/onnx/onnx)

További információ az ONNX-futtatókörnyezet, illetve járulnak hozzá a projekthez:
+ [Az ONNX-futtatókörnyezet GitHub-adattár](https://github.com/Microsoft/onnxruntime)


