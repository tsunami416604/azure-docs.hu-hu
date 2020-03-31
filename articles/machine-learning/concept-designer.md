---
title: Ml-modellek készítése tervezővel
titleSuffix: Azure Machine Learning
description: Ismerje meg az Azure Machine Learning tervezőjét alkotó kifejezéseket, fogalmakat és munkafolyamatokat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: peterlu
author: peterclu
ms.date: 11/12/2019
ms.openlocfilehash: 78a6e7fa8d030185f537136a3a2124d8bc59d808
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79037629"
---
# <a name="what-is-azure-machine-learning-designer-preview"></a>Mi az az Azure Machine Learning Designer (előzetes verzió)? 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Az Azure Machine Learning tervezője lehetővé teszi, hogy vizuálisan csatlakoztassa [az adatkészleteket](#datasets) és [modulokat](#module) egy interaktív vásznon gépi tanulási modellek létrehozásához. Ha meg szeretné tudni, hogyan kezdheti el a tervezőt, olvassa el az [Oktatóanyag: Autóár előrejelzése a tervezővel című témakört.](tutorial-designer-automobile-price-train-score.md)

![Példa az Azure Machine Learning tervezőjének](./media/concept-designer/designer-drag-and-drop.gif)

A tervező az Azure Machine [Learning-munkaterületet](concept-workspace.md) használja a megosztott erőforrások rendszerezéséhez, például:

+ [Folyamatok](#pipeline)
+ [Adathalmazok](#datasets)
+ [Számítási erőforrások](#compute)
+ [Regisztrált modellek](concept-azure-machine-learning-architecture.md#models)
+ [Közzétett folyamatok](#publish)
+ [Valós idejű végpontok](#deploy)

## <a name="model-training-and-deployment"></a>Modellképzés és üzembe helyezés

A tervező egy vizuális vásznat biztosít a gépi tanulási modellek létrehozásához, teszteléséhez és üzembe helyezéséhez. A tervező vel a következőket teheti:

+ Drag-and-drop [adatkészletek](#datasets) és [modulok](#module) a vászonra.
+ A modulok összekapcsolásával hozzon létre egy [folyamattervezetet.](#pipeline-draft)
+ Küldjön be egy [folyamatfuttatást](#pipeline-run) az Azure Machine Learning-munkaterületen lévő számítási erőforrások használatával.
+ A **betanítási folyamatok átalakítása** **következtetési folyamatokká.**
+ [Tegye közzé](#publish) a folyamatokat egy **REST-folyamat végpontján,** hogy új folyamatfuttatásokat küldjön különböző paraméterekkel és adatkészletekkel.
    + Közzétételegy **betanítási folyamat** újra egy folyamat betanítása több modell paraméterek és adatkészletek módosítása közben.
    + Közzétehet egy **kötegkövetkeztetési folyamatot,** hogy egy korábban betanított modell használatával előrejelzéseket készítsen az új adatokról.
+ [Valós](#deploy) **idejű következtetési folyamat** üzembe helyezése egy valós idejű végpontra, hogy valós idejű előrejelzéseket készítsen az új adatokról.

![Munkafolyamat-diagram a betanításhoz, a kötegkövetkeztetésekhez és a valós idejű következtetésekhez a tervezőben](./media/concept-designer/designer-workflow-diagram.png)

## <a name="pipeline"></a>Folyamat

A [folyamat](concept-azure-machine-learning-architecture.md#ml-pipelines) adatkészletekből és analitikus modulokból áll, amelyeket összeköt. A csővezetékek számos felhasználási célra szolgálnak: létrehozhat egy olyan csővezetéket, amely egyetlen modellt képez be, vagy amely több modellt képez be. Létrehozhat egy folyamatot, amely valós időben vagy kötegelt előrejelzéseket készít, vagy olyan folyamatot hozhat létre, amely csak az adatokat tisztítja. A folyamatok lehetővé teszik a munka újrafelhasználását és a projektek rendszerezését.

### <a name="pipeline-draft"></a>Csővezeték-tervezet

Amikor szerkeszt egy folyamatot a tervezőben, a rendszer a **folyamatvázlatot menti.** A folyamatvázlatokat bármikor szerkesztheti modulok hozzáadásával vagy eltávolításával, számítási célok konfigurálásával, paraméterek létrehozásával és így tovább.

Egy érvényes csővezeték a következő jellemzőkkel rendelkezik:

* Az adatkészletek csak modulokhoz tudnak csatlakozni.
* A modulok csak adatkészletekhez vagy más modulokhoz tudnak csatlakozni.
* A modulok minden bemeneti portjának rendelkeznie kell valamilyen kapcsolattal az adatfolyammal.
* Az egyes modulokhoz szükséges összes paramétert be kell állítani.

Ha készen áll a folyamatvázlat futtatására, beküld egy folyamatfuttatást.

### <a name="pipeline-run"></a>Folyamat futtatása

Minden alkalommal, amikor egy folyamatot futtat, a folyamat konfigurációja és annak eredményei a munkaterületen **egy folyamatfuttatásként**tárolódnak. Visszatérhet bármelyik folyamatfuttatáshoz, hogy hibaelhárítási vagy naplózási célokra megvizsgálhassa. **Klónozza** a folyamatfuttatást egy új folyamatpiszkozat létrehozásához, amelyet szerkesztheti.

A folyamatfuttatások [kísérletekbe](concept-azure-machine-learning-architecture.md#experiments) vannak csoportosítva a futtatási előzmények rendszerezéséhez. Beállíthatja a kísérletet minden folyamat futtatása. 

## <a name="datasets"></a>Adathalmazok

A gépi tanulási adatkészlet megkönnyíti az adatok elérését és az okkal való munkát. A tervező számos mintaadatkészletet tartalmaz, amelyekkel kísérletezhet. Szükség szerint további adatkészleteket [regisztrálhat.](how-to-create-register-datasets.md)

## <a name="module"></a>Modul

A modulok az adatokon végezhető algoritmusok. A tervező számos modullal rendelkezik, az adatbe- és betanítási funkcióktól a betanítási, pontozási és érvényesítési folyamatokig.

A modul rendelkezhet a belső algoritmusok konfigurálásához használható paraméterek készletével. Amikor kiválaszt egy modult a vásznon, a modul paraméterei megjelennek a Tulajdonságok panelen a vászontól jobbra. A modell hangolásához módosíthatja a paramétereket a panelen. Beállíthatja az egyes modulok számítási erőforrásait a tervezőben. 

![Modul tulajdonságai](./media/concept-designer/properties.png)

A rendelkezésre álló gépi tanulási algoritmusok könyvtárában való navigáláshoz [lásd: Algoritmus & modulhivatkozás áttekintése](algorithm-module-reference/module-reference.md)

## <a name="compute-resources"></a><a name="compute"></a>Számítási erőforrások

A munkaterületi számítási erőforrások használatával futtathatja a folyamatot, és valós idejű végpontként vagy folyamatvégpontként üzemeltetheti az üzembe helyezett modelleket (kötegelt következtetésesetén). A támogatott számítási célok a következők:

| Számítási cél | Képzés | Környezet |
| ---- |:----:|:----:|
| Azure Machine Learning-számítás | ✓ | |
| Azure Kubernetes Service | | ✓ |

A számítási célok az [Azure Machine Learning-munkaterülethez](concept-workspace.md)vannak csatolva. A számítási célokat a munkaterületen kezelheti az [Azure Machine Learning Studio (klasszikus) környezetben.](https://ml.azure.com)

## <a name="deploy"></a>Üzembe helyezés

Valós idejű következtetés végrehajtásához egy folyamatot kell **üzembe**helyeznie valós idejű végpontként. A valós idejű végpont egy külső alkalmazás és a pontozási modell közötti felületet hoz létre. A valós idejű végpontra irányuló hívás valós időben adja vissza az előrejelzési eredményeket az alkalmazásnak. Egy valós idejű végpont hívásához adja át az API-kulcsot, amely a végpont üzembe helyezésekor jött létre. A végpont a REST-en alapul, amely egy népszerű architektúra választás a webes programozási projektekhez.

Valós idejű végpontok kell telepíteni egy Azure Kubernetes service-fürt.

A modell üzembe helyezéséről az [Oktatóanyag: Gépi tanulási modell üzembe helyezése a tervezővel](tutorial-designer-automobile-price-deploy.md)című témakörben olvashat.

## <a name="publish"></a>Közzététel

Folyamatot is közzétehet egy **folyamatvégponton.** A valós idejű végponthoz hasonlóan a folyamatvégpont lehetővé teszi, hogy új folyamatfuttatásokat küldjön külső alkalmazásokból REST-hívások használatával. Azonban nem küldhet és fogadhat adatokat valós időben egy folyamatvégpont használatával.

A közzétett folyamatok rugalmasak, modellek betanítására vagy újratanítására, [kötegelt következtetések végrehajtására,](how-to-run-batch-predictions-designer.md)új adatok feldolgozására és még sok más ra használhatók. Több folyamatot is közzétehet egyetlen folyamatvégponton, és megadhatja, hogy melyik folyamatverziót kell futtatni.

A közzétett folyamat fut a számítási erőforrások at határozza meg a folyamat tervezetét az egyes modulok.

A tervező ugyanazt a [Közzétett pipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline?view=azure-ml-py) objektumot hozza létre, mint az SDK.


## <a name="moving-from-the-visual-interface-to-the-designer"></a>Áttérés a vizuális felületről a tervezőre

A vizuális felület (előzetes verzió) frissült, és most az Azure Machine Learning tervezője (előzetes verzió). A tervező újralett tervezve, hogy egy folyamatalapú háttérszolgáltatást használjon, amely teljes mértékben integrálható az Azure Machine Learning többi funkciójával. 

A frissítések eredményeképpen a vizuális felület egyes fogalmait és feltételeit megváltoztatták vagy átnevezték. A legfontosabb fogalmi változásokat lásd az alábbi táblázatban. 

| Koncepció a tervezőben | Korábban a vizuális felületen |
| ---- |:----:|
| Csővezeték-tervezet | Experiment |
| Valós idejű végpont | Webszolgáltatás |

### <a name="migrating-to-the-designer"></a>Áttelepítés a tervezőre

A meglévő vizuális felületi kísérleteket és webszolgáltatásokat folyamatokká és valós idejű végpontokká alakíthatja a tervezőben. A következő lépésekkel telepítheti át a vizuális felület eszközeit:

[!INCLUDE [migrate from the visual interface](../../includes/aml-vi-designer-migration.md)]


## <a name="next-steps"></a>További lépések

* Ismerje meg a prediktív elemzés és a gépi tanulás alapjait [az Oktatóanyaggal: Az autó árának előrejelzése a tervezővel](tutorial-designer-automobile-price-train-score.md)
* Ismerje meg, hogyan módosíthatja a meglévő [tervezőmintákat,](samples-designer.md) hogy azok az ön igényeihez igazodjanak.

