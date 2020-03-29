---
title: Mi történt a Workbenchcsel?
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogy mi lehetséges a Workbench-alkalmazás nélkül, és mi a támogatási idővonal.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: j-martens
ms.author: jmartens
ms.date: 03/05/2020
ms.openlocfilehash: 9b49eb6237346a76903202a118331383c5a8f623
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944257"
---
# <a name="what-happened-to-azure-machine-learning-workbench"></a>Mi történt az Azure Machine Learning Workbench alkalmazással?

Az Azure Machine Learning Workbench alkalmazás és néhány más korai funkció elavult, és a **2018 szeptemberi** kiadásban lecserélték őket, hogy helyet adjanak a továbbfejlesztett [architektúrának.](concept-azure-machine-learning-architecture.md)

A felhasználói élmény javítása érdekében a kiadás számos, az ügyfelek visszajelzései által ösztönzött jelentős frissítést tartalmaz. A kísérletfuttatásoktól a modellüzembe helyezésig az alapvető funkciók nem változtak. De most már használhatja a robusztus <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">Python SDK,</a>R SDK, és az [Azure CLI](reference-azure-machine-learning-cli.md) a gépi tanulási feladatok és folyamatok elvégzéséhez.

Az Azure Machine Learning korábbi verziójában létrehozott összetevők többsége a saját helyi vagy felhőbeli tárolójában van tárolva. Ezek a munkadarabok sohasem fognak eltűnni.

Ebben a cikkben megtudhatja, hogy mi változott, és hogyan befolyásolja a már meglévő munkát az Azure Machine Learning Workbench és api-jai.

>[!Warning]
>Ez a cikk nem az Azure Machine Learning Studio-felhasználók számára. Az Azure Machine Learning-ügyfelek számára, akik telepítették a Workbench (előzetes verzió) alkalmazást, és/vagy kísérletezési és modellkezelési előzetes fiókkal rendelkeznek.


## <a name="what-changed"></a>Mi változott?

Az Azure Machine Learning legújabb kiadása a következő funkciókat tartalmazza:
+ [Egyszerűsített Azure-erőforrás-modell.](concept-azure-machine-learning-architecture.md)
+ Új [portál felhasználói felülete](how-to-track-experiments.md) a kísérletek és a számítási célok kezeléséhez.
+ Egy új, átfogóbb Python <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">SDK</a>.
+ Az új kibővített [Azure CLI-bővítmény](reference-azure-machine-learning-cli.md) gépi tanuláshoz.

Az [architektúrát](concept-azure-machine-learning-architecture.md) újratervezték a könnyű használat érdekében. Több Azure-erőforrás és -fiók helyett csak egy [Azure Machine Learning-munkaterület](concept-workspace.md) szükséges. Az [Azure Portalon](how-to-manage-workspace.md) gyorsan létrehozhat munkaterületeket. Egy munkaterület használatával több felhasználó tárolhatja a betanítási és üzembe helyezési számítási célokat, modellkísérleteket, Docker-rendszerképeket, üzembe helyezett modelleket és így tovább.

Bár az aktuális kiadásban új, továbbfejlesztett CLI- és SDK-ügyfelek találhatók, maga az asztali munkapad-alkalmazás is kilett vonva. A kísérletek az [Azure Machine Learning stúdió munkaterületi irányítópultján kezelhetők.](how-to-track-experiments.md#view-the-experiment-in-the-web-portal) Használja az irányítópultot a kísérlet előzményeinek beolvasására, a munkaterülethez rendelt számítási célok, a modellek és a Docker-rendszerképek kezelésére, vagy akár webszolgáltatások üzembe helyezésére.

<a name="timeline"></a>

## <a name="support-timeline"></a>Támogatási idővonal

2019. január 9-én a Machine Learning Workbench, az Azure Machine Learning Experimentation és a Model Management fiókok, valamint a hozzájuk tartozó SDK- és CLI-fiókok támogatása véget ért.

A legújabb funkciók az <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">SDK</a>, a [CLI](reference-azure-machine-learning-cli.md)és a [portál](how-to-manage-workspace.md)használatával érhetők el .

## <a name="what-about-run-histories"></a>Mi a helyzet a futtatási előzményekkel?

A régebbi futtatási előzmények már nem érhetők el, így továbbra is láthatja a futtatásokat a legújabb verzióban.

A futtatási előzményeket **mostantól kísérleteknek nevezik.** A modell kísérleteit az SDK, a CLI vagy az Azure Machine Learning stúdió használatával gyűjtheti össze, és fedezheti fel azokat.

A portál munkaterületi irányítópultja csak a Microsoft Edge, a Chrome és a Firefox böngészőkben támogatott:

[![Online portál](./media/overview-what-happened-to-workbench/image001.png)](./media/overview-what-happened-to-workbench/image001.png#lightbox)

Kezdje el betanítása a modellek és nyomon követi a futtatási előzmények segítségével az új CLI és SDK. Megtudhatja, hogyan az [oktatóanyag: a vonat modellek az Azure Machine Learning](tutorial-train-models-with-aml.md).

## <a name="will-projects-persist"></a>Megmaradnak a projektek?

Nem fog elveszni sem kód, sem munka. A régebbi verzióban, a projektek helyi könyvtárral rendelkező felhőbeli entitások. A legújabb verzióban helyi könyvtárakat csatolhat az Azure Machine Learning-munkaterülethez egy helyi konfigurációs fájl használatával. Lásd [a legújabb architektúra ábráját.](concept-azure-machine-learning-architecture.md)

A projekt tartalmának nagy része már a helyi számítógépen volt. Tehát csak létre kell hoznia egy konfigurációs fájlt a könyvtárban, és hivatkoznia kell rá a kódban, hogy csatlakozzon a munkaterülethez. A fájlokat és parancsfájlokat tartalmazó helyi könyvtár használatának folytatásához adja meg a könyvtár nevét `az ml project attach` a ["experiment.submit"](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) Python parancsban vagy a CLI parancs használatával.  Példa:
```python
run = exp.submit(source_directory=script_folder,
                 script='train.py', run_config=run_config_system_managed)
```

[Hozzon létre egy munkaterületet](how-to-manage-workspace.md) a kezdéshez.

## <a name="what-about-my-registered-models-and-images"></a>Mi a helyzet a regisztrált modellekkel és képekkel?

A régi modell beállításjegyzékében regisztrált modelleket át kell telepíteni az új munkaterületre, ha továbbra is használni szeretné őket. A modellek áttelepítéséhez töltse le a modelleket, és regisztrálja őket újra az új munkaterületen.

A régi lemezkép-beállításjegyzékben létrehozott lemezképek nem telepíthetők át közvetlenül az új munkaterületre. A legtöbb esetben a modell lemezkép létrehozása nélkül is telepíthető. Szükség esetén létrehozhat egy lemezképet a modellhez az új munkaterületen. További információ: [Gépi tanulási modellek kezelése, regisztrálása, üzembe helyezése és figyelése.](concept-model-management-and-deployment.md)

## <a name="what-about-deployed-web-services"></a>Mi a helyzet az üzembe helyezett webszolgáltatásokkal?

Most, hogy a régi CLI támogatása véget ért, már nem telepítheti újra a modelleket, és nem kezelheti az eredetileg a Modellkezelő-fiókkal telepített webszolgáltatásokat. Ezek a webszolgáltatások azonban mindaddig működni fognak, amíg az Azure Container Service (ACS) továbbra is támogatott.

A legújabb verzióban a modellek webszolgáltatásokként vannak üzembe helyezve az Azure Container Instances (ACI) vagy az Azure Kubernetes Service (AKS) fürtök. FPGA-kra és az Azure IoT Edge-re is üzembe helyezhet.

További információ az alábbi cikkekben:
+ [A modellek üzembe helyezése hol és hogyan](how-to-deploy-and-where.md)
+ [Oktatóanyag: Modellek üzembe helyezése az Azure Machine Learning segítségével](tutorial-deploy-models-with-aml.md)

## <a name="next-steps"></a>További lépések

Ismerje meg az [Azure Machine Learning legújabb architektúráját.](concept-azure-machine-learning-architecture.md)

A szolgáltatás áttekintéséhez olvassa el [a Mi az Azure Machine Learning?](overview-what-is-azure-ml.md).

Hozza létre az első kísérletet a kívánt módszerrel:
  + [Python-jegyzetfüzetek használata](tutorial-1st-experiment-sdk-setup.md)
  + [Az R Markdown használata](tutorial-1st-r-experiment.md) 
  + [Automatikus gépi tanulás használata](tutorial-designer-automobile-price-train-score.md) 
  + [A tervező húzási & lehúzási képességeinek használata](tutorial-first-experiment-automated-ml.md) 
  + [A CLI ML-bővítményének használata](tutorial-train-deploy-model-cli.md)
