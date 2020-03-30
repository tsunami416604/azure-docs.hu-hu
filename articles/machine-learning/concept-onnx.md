---
title: 'ONNX: magas perf, platformfüggetlen következtetések'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan az Open Neural Network Exchange (ONNX) segítségével optimalizálja a következtetés a gépi tanulási modell.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 08/15/2019
ms.custom: seodec18
ms.openlocfilehash: 98aebb4733c2aa2a6d0b0217f1f437bcea1992e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270172"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX és Azure Machine Learning: Ml-modellek létrehozása és felgyorsítása

Ismerje meg, hogyan az [Open Neural Network Exchange](https://onnx.ai) (ONNX) segítségével optimalizálja a következtetés a gépi tanulási modell. A következtetés vagy a modell pontozása az a fázis, ahol az üzembe helyezett modell előrejelzési, leggyakrabban az éles adatok. 

A gépi tanulási modellek izálása a következtetés (vagy modellpontozás) érdekében nehéz, mivel a modell és a következtetési könyvtár finomhangolásával a legtöbbet kell kihoznia a hardverképességekből. A probléma rendkívül nehéz, ha azt szeretné, hogy optimális teljesítményt különböző platformokon (felhő / él, CPU / GPU, stb), mivel mindegyik különböző képességekkel és jellemzőkkel rendelkezik. A komplexitás növekszik, ha a modellek a különböző keretek, amelyek futtatásához kell futtatni a különböző platformokon. Nagyon időigényes a keretrendszerek és a hardverek különböző kombinációinak optimalizálása. A megoldás a vonat egyszer a kívánt keretrendszer, és fuss bárhol a felhőben vagy szélén van szükség. Itt jön a mi a be a mibe az ONNX.

A Microsoft és a partnerek közössége létrehozta az ONNX-et nyílt szabványként a gépi tanulási modellek képviseletére. Számos [keretrendszer modelljei,](https://onnx.ai/supported-tools) például a TensorFlow, a PyTorch, a SciKit-Learn, a Keras, a Chainer, az MXNet és a MATLAB exportálhatók vagy konvertálhatók szabványos ONNX formátumra. Miután a modellek ONNX formátumban vannak, különböző platformokon és eszközökön futtathatók.

[Az ONNX Runtime](https://github.com/Microsoft/onnxruntime) egy nagy teljesítményű következtetési motor az ONNX modellek éles környezetben történő üzembe helyezéséhez. Felhőre és élre is optimalizálva van, és Linuxon, Windowson és Macen is működik. C++-ban írott, c, Python és C# API-kis. Az ONNX Runtime támogatja az ÖSSZES ONNX-ML specifikációt, és integrálja a gyorsítókat különböző hardvereken, például a TensorRT-en az NVidia GPU-kon.

Az ONNX-futásidejű nagy méretű Microsoft-szolgáltatásokban, például a Bingben, az Office-ban és a Cognitive Servicesben használatos. A teljesítménynövekedés számos tényezőtől függ, de ezek a Microsoft-szolgáltatások __átlagosan 2-szeres teljesítménynövekedést értek el a CPU-n.__ Az ONNX Futásidő a Windows ML részeként is használható több száz millió eszközön. Használhatja a futásidejű Azure Machine Learning. Az ONNX runtime használatával élvezheti a széles körű gyártási szintű optimalizálást, tesztelést és folyamatos fejlesztéseket.

[![ONNX-folyamatábra az edzést, a konvertereket és a telepítést ábrázoló](./media/concept-onnx/onnx.png)](././media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>ONNX modellek beolvasása

Az ONNX modelleket többféleképpen szerezheti be:
+ Új ONNX-modell betanítása az Azure Machine Learningben (lásd a cikk alján található példákat)
+ Konvertálja a meglévő modellt egy másik formátumból ONNX formátumra (lásd az [oktatóanyagokat)](https://github.com/onnx/tutorials) 
+ Előre betanított ONNX-modell beolvasása az [ONNX modellállatkertből](https://github.com/onnx/models) (lásd a cikk alján található példákat)
+ Személyre szabott ONNX-modell létrehozása az [Azure Custom Vision szolgáltatásból](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) 

Számos modell, beleértve a képbesorolást, az objektumészlelést és a szövegfeldolgozást, ONNX modellként is ábrázolható. Előfordulhat azonban, hogy egyes modellek konvertálása nem lehetséges. Ha befut ebben a helyzetben, kérjük, fájl egy probléma a GitHub a megfelelő konverter, amit használt. A meglévő formátummodell tanévenként a probléma elnem hárította.

## <a name="deploy-onnx-models-in-azure"></a>ONNX-modellek telepítése az Azure-ban

Az Azure Machine Learning segítségével telepítheti, kezelheti és figyelheti AZ ONNX-modelleket. A szabványos [üzembe helyezési munkafolyamat](concept-model-management-and-deployment.md) és az ONNX-futásidő használatával létrehozhat egy, a felhőben üzemeltetett REST-végpontot. Lásd a cikk végén található Jupyter-jegyzetfüzetek példáját, hogy saját maga próbálja ki. 

### <a name="install-and-use-onnx-runtime-with-python"></a>Az ONNX Runtime telepítése és használata pythonnal

Python csomagok ONNX Runtime állnak rendelkezésre [PyPi.org](https://pypi.org) [(CPU](https://pypi.org/project/onnxruntime), [GPU](https://pypi.org/project/onnxruntime-gpu)). Kérjük, a telepítés előtt olvassa el [a rendszerkövetelményeket.](https://github.com/Microsoft/onnxruntime#system-requirements) 

 A Python ONNX-futási ideje telepítéséhez használja az alábbi parancsok egyikét: 
```python   
pip install onnxruntime       # CPU build
pip install onnxruntime-gpu   # GPU build
```

Az ONNX Runtime hívásához használja a következőt:    
```python
import onnxruntime
session = onnxruntime.InferenceSession("path to model")
```

A modellhez mellékelt dokumentáció általában a modell használatának bemeneteit és kimeneteit ismerteti. A modell megtekintéséhez használhat vizualizációs eszközt is, például [Netront.](https://github.com/lutzroeder/Netron) Az ONNX Futásidejű lehetővé teszi a modell metaadatainak, bemeneteinek és kimeneteinek lekérdezését is:    
```python
session.get_modelmeta()
first_input_name = session.get_inputs()[0].name
first_output_name = session.get_outputs()[0].name
```

A modell következtetéséhez használja `run` és adja át a visszaadandó kimenetek listáját (hagyja üresen, ha az összeset szeretné), valamint a bemeneti értékek térképét. Az eredmény a kimenetek listája.  
```python
results = session.run(["output1", "output2"], {
                      "input1": indata1, "input2": indata2})
results = session.run([], {"input1": indata1, "input2": indata2})
```

A teljes Python API-referencia, tekintse meg az [ONNX runtime referencia docs](https://aka.ms/onnxruntime-python).    

## <a name="examples"></a>Példák

Tekintse meg [a how-to-use-azureml/deployment/onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx) például az ONNX-modelleket létrehozó és üzembe helyező jegyzetfüzeteket.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>További információ

További információ az ONNX-ről vagy a projekthez való hozzájárulásról:
+ [Az ONNX projekt honlapja](https://onnx.ai)
+ [ONNX-kód a GitHubon](https://github.com/onnx/onnx)

További információ az ONNX futásidejéről vagy a projekthez való hozzájárulásról:
+ [ONNX futásidejű GitHub-tárcsa](https://github.com/Microsoft/onnxruntime)


