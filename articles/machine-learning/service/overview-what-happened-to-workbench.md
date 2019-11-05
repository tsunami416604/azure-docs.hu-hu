---
title: Mi történt Machine Learning Workbench-vel?
titleSuffix: Azure Machine Learning
description: Ismerje meg, mi történt a Machine Learning Workbench alkalmazással, mi változott a Azure Machine Learningban, és mi a támogatási ütemterv.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
ms.reviewer: jmartens
author: j-martens
ms.author: jmartens
ms.date: 05/14/2019
ms.custom: seodec18
ms.openlocfilehash: 3c98da50d0c50a7fdb94f579444f3c09c1096d18
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73476415"
---
# <a name="what-happened-to-azure-machine-learning-workbench"></a>Mi történt az Azure Machine Learning Workbench alkalmazással?

A Azure Machine Learning Workbench alkalmazást és néhány más korai szolgáltatást elavultak, és lecserélték a **szeptember 2018** -es kiadásban, hogy a fejlettebb [architektúra](concept-azure-machine-learning-architecture.md)legyen.

Az élmény javítása érdekében a kiadás számos jelentős frissítést tartalmaz, amelyeket az ügyfél visszajelzése kér. A kísérlet alapfunkciói a modell üzembe helyezése során nem változtak. Most azonban használhatja a robusztus <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">PYTHON SDK</a>-t, az R SDK-t és az [Azure CLI](reference-azure-machine-learning-cli.md) -t a gépi tanulási feladatok és folyamatok elvégzéséhez.

A Azure Machine Learning korábbi verziójában létrehozott összetevők többsége a saját helyi vagy Felhőbeli tárolójában tárolódik. Ezek a munkadarabok sohasem fognak eltűnni.

Ebből a cikkből megtudhatja, hogy mi változott, és hogyan befolyásolja a már meglévő munkáját a Azure Machine Learning Workbench és az API-kkal.

>[!Warning]
>Ez a cikk nem Azure Machine Learning Studio felhasználók számára készült. Olyan Azure Machine Learning ügyfelek számára készült, akik telepítették a Workbench (előzetes verzió) alkalmazást, és/vagy kísérletezési és modell-felügyeleti előzetes fiókkal rendelkeznek.


## <a name="what-changed"></a>Mi változott?

A Azure Machine Learning legújabb kiadása a következő funkciókat tartalmazza:
+ Egy [egyszerűsített Azure-erőforrás modell](concept-azure-machine-learning-architecture.md).
+ [Új portál felhasználói felület](how-to-track-experiments.md) a kísérletek és számítási célok kezeléséhez.
+ Egy új, átfogóbb Python <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">SDK</a>.
+ Az új bővített [Azure CLI-bővítmény](reference-azure-machine-learning-cli.md) a gépi tanuláshoz.

Az [architektúra](concept-azure-machine-learning-architecture.md) újratervezése egyszerű használat érdekében. Több Azure-erőforrás és -fiók helyett csak egy [Azure Machine Learning-munkaterület](concept-workspace.md) szükséges. Az [Azure Portalon](how-to-manage-workspace.md) gyorsan létrehozhat munkaterületeket. Munkaterületek használatával több felhasználó is tárolhatja a képzési és üzembe helyezési számítási célokat, a modellek kísérleteit, a Docker-rendszerképeket, a telepített modelleket stb.

Bár új továbbfejlesztett CLI-és SDK-ügyfelek találhatók a jelenlegi kiadásban, maga a Desktop Workbench alkalmazás is ki lett vonva. A kísérletek a [Azure Machine learning Studio munkaterület-irányítópultján](how-to-track-experiments.md#view-the-experiment-in-the-web-portal)kezelhetők. Használja az irányítópultot a kísérlet előzményeinek beolvasására, a munkaterülethez rendelt számítási célok, a modellek és a Docker-rendszerképek kezelésére, vagy akár webszolgáltatások üzembe helyezésére.

<a name="timeline"></a>

## <a name="support-timeline"></a>Támogatási idővonal

Január 9 2019-én a Machine Learning Workbench, Azure Machine Learning-kísérletezés és modellkezelés fiókok, valamint a hozzájuk tartozó SDK-és CLI-támogatás megszűnt.

Az <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">SDK</a>, a [CLI](reference-azure-machine-learning-cli.md)és a [portál](how-to-manage-workspace.md)használatával minden legújabb funkció elérhető.

## <a name="what-about-run-histories"></a>Mi a helyzet a futtatási előzményekkel?

A régebbi futtatási előzmények már nem érhetők el, így továbbra is megtekintheti a futtatásokat a legújabb verzióban.

A futtatási előzmények mostantól **kísérletek**. A modell kísérleteit összegyűjtheti, és az SDK, a CLI vagy a Azure Machine Learning Studio használatával is megismerheti azokat.

A portál munkaterület-irányítópultja csak a Microsoft Edge, a Chrome és a Firefox böngészőkben támogatott:

[![online portál](./media/overview-what-happened-to-workbench/image001.png)](./media/overview-what-happened-to-workbench/image001.png#lightbox)

Az új parancssori felület és az SDK használatával megkezdheti a modellek betanítását és a futtatási előzmények nyomon követését. Megtudhatja, hogyan használható az [oktatóanyag: modellek betanítása Azure Machine learning](tutorial-train-models-with-aml.md)használatával.

## <a name="can-i-still-prep-data"></a>Továbbra is előkészíthetem az adatokat?

A meglévő adatelőkészítési fájlok nem a legújabb kiadásba kerülnek, mert már nem rendelkezünk Machine Learning Workbench szolgáltatással. Azonban továbbra is előkészítheti a modellezéshez szükséges bármilyen méretű adatkészletet.

Ha bármilyen méretű adatkészletet használ, a [Azure Machine learning adatelőkészítési csomag](https://aka.ms/data-prep-sdk) használatával gyorsan elő tudja készíteni az adatait a modellezés előtt a Python-kód írásával.

## <a name="will-projects-persist"></a>Megmaradnak a projektek?

Nem fog elveszni sem kód, sem munka. A régebbi verzióban, a projektek helyi könyvtárral rendelkező felhőbeli entitások. A legújabb verzióban helyi konfigurációs fájllal csatolja a helyi címtárakat a Azure Machine Learning munkaterülethez. Tekintse meg a [legújabb architektúra diagramját](concept-azure-machine-learning-architecture.md).

A projekt tartalmának nagy része már a helyi gépen van. Ezért csak létre kell hoznia egy konfigurációs fájlt a könyvtárban, és hivatkoznia kell a kódban a munkaterülethez való kapcsolódásra. Ha továbbra is szeretné használni a fájlokat és parancsfájlokat tartalmazó helyi könyvtárat, adja meg a könyvtár nevét a ["Experiment. submit"](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) Python-parancsban, vagy használja a `az ml project attach` CLI-parancsot.  Példa:
```python
run = exp.submit(source_directory=script_folder,
                 script='train.py', run_config=run_config_system_managed)
```

[Hozzon létre egy munkaterületet](how-to-manage-workspace.md) az első lépésekhez.

## <a name="what-about-my-registered-models-and-images"></a>Mi a helyzet a regisztrált modellekkel és képekkel?

A régi modell beállításjegyzékében regisztrált modelleket át kell telepíteni az új munkaterületre, ha továbbra is használni szeretné őket. A modellek áttelepítéséhez töltse le a modelleket, majd regisztrálja újra az új munkaterületen.

A régi rendszerkép beállításjegyzékében létrehozott rendszerképeket nem lehet közvetlenül áttelepíteni az új munkaterületre. A legtöbb esetben a modell lemezkép létrehozása nélkül is üzembe helyezhető. Ha szükséges, létrehozhat egy rendszerképet a modellhez az új munkaterületen. További információkért lásd: [gépi tanulási modellek kezelése, regisztrálása, üzembe helyezése és figyelése](concept-model-management-and-deployment.md).

## <a name="what-about-deployed-web-services"></a>Mi a helyzet az üzembe helyezett webszolgáltatásokkal?

A régi CLI támogatásának befejezése után már nem telepíthet újra modelleket, és nem felügyelheti a modellkezelés-fiókkal eredetileg telepített webszolgáltatásokat. Ezek a webszolgáltatások azonban továbbra is működni fognak, amíg a Azure Container Service (ACS) továbbra is támogatott.

A legújabb verzióban a modellek webszolgáltatásként lesznek telepítve Azure Container Instances (ACI) vagy Azure Kubernetes Service (ak) fürtökhöz. A FPGA és a Azure IoT Edge is üzembe helyezhető.

További információt a következő cikkekben talál:
+ [A modellek üzembe helyezésének helye és módja](how-to-deploy-and-where.md)
+ [Oktatóanyag: modellek üzembe helyezése Azure Machine Learning](tutorial-deploy-models-with-aml.md)

## <a name="what-about-the-old-sdk-and-cli"></a>Mi a helyzet a régi SDK-val és a parancssori felülettel?

Lásd az előző [idővonalat](#timeline). Javasoljuk, hogy kezdje el létrehozni az új kísérleteket és modelleket a legújabb SDK-val vagy parancssori felülettel.

Ha az új Python SDK-t használja a legújabb kiadásban, a Azure Machine Learning bármely Python-környezetben dolgozhat. Ismerje meg, hogyan telepítheti a legújabb <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">SDK-t</a>. A frissített [Azure Machine learning CLI-bővítmény](reference-azure-machine-learning-cli.md) a `az ml` parancsainak gazdag készletével is használható a szolgáltatással való interakcióhoz bármely parancssori környezetben, beleértve a Azure Cloud shellt is.

## <a name="what-about-visual-studio-code-tools-for-ai"></a>Mi a helyzet Visual Studio Code Tools for AI?

Ebben a legújabb kiadásban a bővítmény átnevezve lett a Visual Studio Code Azure Machine Learningra, és a rendszer kibővítette és továbbfejlesztette az előző új funkciókkal való együttműködéshez.

[![Azure Machine Learning a Visual Studio Code-hoz](./media/overview-what-happened-to-workbench/vscode.png)](./media/overview-what-happened-to-workbench/vscode-big.png#lightbox)

## <a name="what-about-domain-packages"></a>Mi a helyzet a tartománycsomagokkal?

A számítógép-és a szöveges Analitika tartományi csomagjai nem használhatók a Azure Machine Learning legújabb verziójára. A legújabb Azure Machine Learning Python <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">SDK</a>-val azonban továbbra is létrehozhat és betaníthat számítógépes jövőképet, szöveges és előrejelzési modelleket.

## <a name="next-steps"></a>További lépések

Ismerkedjen meg a [Azure Machine learning legújabb architektúrával](concept-azure-machine-learning-architecture.md).

A szolgáltatás áttekintéséhez olvassa el a [Mi az Azure Machine learning?](overview-what-is-azure-ml.md)című témakört.

Hozza létre első kísérletét a kívánt módszerrel:
  + [Python-jegyzetfüzetek használata & ML-modellek üzembe helyezéséhez](tutorial-1st-experiment-sdk-setup.md)
  + [Az R Markdown használata & ML-modellek üzembe helyezéséhez]( tutorial-1st-r-experiment.md) 
  + [Az automatizált gépi tanulás használata & ML-modellek üzembe helyezéséhez](ui-tutorial-automobile-price-train-score.md) 
  + [A tervező drag & drop funkciói a & üzembe helyezéséhez](tutorial-first-experiment-automated-ml.md) 
  + [Modellek betanítása és üzembe helyezése a Machine learning parancssori felület használatával](tutorial-train-deploy-model-cli.md)