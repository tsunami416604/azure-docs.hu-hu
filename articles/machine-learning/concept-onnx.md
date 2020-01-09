---
title: 'ONNX: nagy teljesítményű, platformfüggetlen következtetés'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használható a nyílt neurális hálózati Exchange (ONNX) a gépi tanulási modell következtetésének optimalizálásához.
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
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/28/2019
ms.locfileid: "75541061"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX és Azure Machine Learning: ML modellek létrehozása és felgyorsítása

Ismerje meg, hogyan használható a [nyílt neurális hálózati Exchange](https://onnx.ai) (ONNX) a gépi tanulási modell következtetésének optimalizálásához. A következtetés vagy a modell pontozása az a fázis, ahol az üzembe helyezett modellt az előrejelzéshez használják, leggyakrabban a termelési adatforgalomban. 

A gépi tanulási modellek a következtetésekhez való optimalizálása (vagy a modell pontozása) nehéz, mivel a modell és a következtetési könyvtár összehangolása szükséges a legtöbb hardveres funkció kihasználásához. A probléma rendkívül nehéz lesz, ha a különböző típusú platformokon (felhő/Edge, CPU/GPU stb.) optimális teljesítményt szeretne kapni, mivel mindegyik különböző képességekkel és jellemzőkkel rendelkezik. A bonyolultság akkor növekszik, ha számos különböző keretrendszerből származó modellekkel rendelkezik, amelyek számos különböző platformon futnak. Nagyon sok időt kell igénybe venni a keretrendszerek és a hardverek különböző kombinációinak optimalizálására. Egy olyan megoldás, amelyet egyszer kell betanítani az előnyben részesített keretrendszerben, és a felhőben vagy az Edge-ben bárhol futnia kell. Itt jön a ONNX.

A Microsoft és a partnerek egyik közössége nyílt szabványként hozta létre a gépi tanulási modelleket képviselő ONNX. A [számos keretrendszerből](https://onnx.ai/supported-tools) származó modellek, például a TensorFlow, a PyTorch, a SciKit, a kerasz, a chainer, a MXNet és a MATLAB, a szabványos ONNX formátumba exportálhatók vagy konvertálhatók. Ha a modellek ONNX formátumúak, különböző platformokon és eszközökön is futtathatók.

A [ONNX Runtime](https://github.com/Microsoft/onnxruntime) egy nagy teljesítményű, a ONNX-modellek éles környezetben történő üzembe helyezéséhez használható. A felhő és a peremhálózat számára egyaránt optimalizált, és Linux, Windows és Mac rendszereken is működik. A C++szolgáltatásban C, Python és C# API-k is szerepelnek. Az ONNX Runtime támogatást nyújt az összes ONNX-ML specifikációhoz, és a különböző hardverek, például az NVidia GPU-k TensorRT is integrálható a gyorssegédekkel.

A ONNX futtatókörnyezet nagy léptékű Microsoft-szolgáltatásokban, például a Bing, az Office és a Cognitive Servicesban használatos. A teljesítménybeli nyereség számos tényezőtől függ, de ezek a Microsoft-szolgáltatások __átlagosan 2x teljesítménybeli nyereséget észleltek a CPU__-ban. A ONNX Runtime a Windows ML részeként is használatos több száz millió eszközön. A futtatókörnyezetet Azure Machine Learning használatával használhatja. A ONNX Runtime használatával kihasználhatja a nagy teljesítményű optimalizálási, tesztelési és folyamatos fejlesztéseket.

[![ONNX-folyamatábra, amely bemutatja a képzést, a konvertereket és az üzembe helyezést](./media/concept-onnx/onnx.png)](././media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>ONNX-modellek beolvasása

A ONNX-modelleket többféleképpen is beszerezheti:
+ Új ONNX-modell betanítása Azure Machine Learningban (lásd a cikk alján található példákat)
+ Meglévő modell átalakítása más formátumból ONNX (lásd az [oktatóanyagokat](https://github.com/onnx/tutorials)) 
+ Egy előre betanított ONNX-modell beszerzése a [ONNX Model Zoo](https://github.com/onnx/models) -ból (lásd a cikk alján található példákat)
+ Testreszabott ONNX-modell létrehozása az [Azure Custom Vision szolgáltatásból](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) 

Számos modell, beleértve a képbesorolást, az objektumok észlelését és a ONNX modellként is megjeleníthető. Előfordulhat azonban, hogy egyes modellek nem alakíthatók át sikeresen. Ha ebben a helyzetben fut, a megfelelő konverter GitHubján egy problémát kell megadnia. A probléma megoldása előtt továbbra is használhatja a meglévő Format modellt.

## <a name="deploy-onnx-models-in-azure"></a>ONNX-modellek üzembe helyezése az Azure-ban

A Azure Machine Learning segítségével üzembe helyezheti, kezelheti és figyelheti a ONNX-modelljeit. A normál [üzembe helyezési munkafolyamat](concept-model-management-and-deployment.md) és a ONNX futtatókörnyezet használatával létrehozhat egy felhőben üzemeltetett Rest-végpontot. Tekintse meg a cikk végén található példa Jupyter jegyzetfüzeteket a kipróbáláshoz. 

### <a name="install-and-use-onnx-runtime-with-python"></a>A ONNX Runtime telepítése és használata a Python használatával

A ONNX Runtime Python-csomagjai a [PyPi.org](https://pypi.org) ([CPU](https://pypi.org/project/onnxruntime), [GPU](https://pypi.org/project/onnxruntime-gpu)) szolgáltatásban érhetők el. A telepítés előtt olvassa el a [rendszerkövetelményeket](https://github.com/Microsoft/onnxruntime#system-requirements) . 

 A Pythonhoz készült ONNX Runtime telepítéséhez használja az alábbi parancsok egyikét: 
```python   
pip install onnxruntime       # CPU build
pip install onnxruntime-gpu   # GPU build
```

A ONNX Runtime a Python-szkriptben való meghívásához használja a következőt:    
```python
import onnxruntime
session = onnxruntime.InferenceSession("path to model")
```

A modellt kísérő dokumentáció általában megadja a modell használatának bemeneteit és kimeneteit. A modell megjelenítéséhez olyan vizualizációs eszközt is használhat, mint például a [Netron](https://github.com/lutzroeder/Netron) . A ONNX Runtime lehetővé teszi a modell metaadatainak, bemenetének és kimenetének lekérdezését is:    
```python
session.get_modelmeta()
first_input_name = session.get_inputs()[0].name
first_output_name = session.get_outputs()[0].name
```

A modell kiszámításához használja a `run` és a pass értéket a visszaadott kimenetek listájában (hagyja üresen, ha az összeset meg szeretné jeleníteni) és a bemeneti értékek egy térképét. Az eredmény a kimenetek listája.  
```python
results = session.run(["output1", "output2"], {
                      "input1": indata1, "input2": indata2})
results = session.run([], {"input1": indata1, "input2": indata2})
```

A Python API teljes leírását a [ONNX futásidejű](https://aka.ms/onnxruntime-python)dokumentációjában találhatja meg.    

## <a name="examples"></a>Példák

Lásd: [útmutató – azureml/Deployment/Onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx) , például Onnx-modelleket létrehozó és telepítő jegyzetfüzetek.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>További információ

További információ a ONNX vagy a projekthez való hozzájárulásról:
+ [ONNX projekt webhelye](https://onnx.ai)
+ [ONNX-kód a GitHubon](https://github.com/onnx/onnx)

További információ a ONNX Futtatókörnyezetről vagy a projekthez való hozzájárulásról:
+ [ONNX futásidejű GitHub-tárház](https://github.com/Microsoft/onnxruntime)


