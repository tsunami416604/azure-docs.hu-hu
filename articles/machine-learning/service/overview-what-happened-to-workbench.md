---
title: Mi történt Machine Learning Workbench-vel?
titleSuffix: Azure Machine Learning service
description: Ismerje meg, mi történt a Machine Learning Workbench alkalmazással, mi változott a Azure Machine Learning szolgáltatásban, és mi a támogatási ütemterv.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
ms.reviewer: jmartens
author: j-martens
ms.author: jmartens
ms.date: 05/14/2019
ms.custom: seodec18
ms.openlocfilehash: 1bac6a2ec5657f3304e52fb328895a4f5aa94529
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "69996779"
---
# <a name="what-happened-to-azure-machine-learning-workbench"></a>Mi történt az Azure Machine Learning Workbench alkalmazással?

A Azure Machine Learning Workbench alkalmazást és néhány más korai szolgáltatást elavultak, és lecserélték a szeptember 2018-es kiadásban, hogy a fejlettebb [architektúra](concept-azure-machine-learning-architecture.md)legyen. 

Az élmény javítása érdekében a kiadás számos jelentős frissítést tartalmaz, amelyeket az ügyfél visszajelzése kér. A kísérlet alapfunkciói a modell üzembe helyezése során nem változtak. Most azonban használhatja a robusztus <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a> -t és az [Azure CLI](reference-azure-machine-learning-cli.md) -t a gépi tanulási feladatok és folyamatok elvégzéséhez.  

A Azure Machine Learning szolgáltatás korábbi verziójában létrehozott összetevők többsége a saját helyi vagy Felhőbeli tárolójában van tárolva. Ezek a munkadarabok sohasem fognak eltűnni.

Ebből a cikkből megtudhatja, hogy mi változott, és hogyan befolyásolja a már meglévő munkáját a Azure Machine Learning Workbench és az API-kkal.

>[!Warning]
>Ez a cikk az Azure Machine Learning Studio-felhasználó nem tartozik. Az Azure Machine Learning szolgáltatás vásárlók a Workbench (előzetes verzió) alkalmazás telepítése és/vagy kísérleti és modellkezelési fiókok előzetes kell legyen.


## <a name="what-changed"></a>Mi változott?

Azure Machine Learning szolgáltatás legújabb kiadása a következő funkciókat tartalmazza:
+ Egy [egyszerűsített Azure-erőforrás modell](concept-azure-machine-learning-architecture.md).
+ [Új portál felhasználói felület](how-to-track-experiments.md) a kísérletek és számítási célok kezeléséhez.
+ Egy új, átfogóbb Python <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a>.
+ Az új bővített [Azure CLI-bővítmény](reference-azure-machine-learning-cli.md) a gépi tanuláshoz.

Az [architektúra](concept-azure-machine-learning-architecture.md) újratervezése egyszerű használat érdekében. Több Azure-erőforrás és -fiók helyett csak egy [Azure Machine Learning-munkaterület](concept-workspace.md) szükséges. Az [Azure Portalon](how-to-manage-workspace.md) gyorsan létrehozhat munkaterületeket. Munkaterületek használatával több felhasználó is tárolhatja a képzési és üzembe helyezési számítási célokat, a modellek kísérleteit, a Docker-rendszerképeket, a telepített modelleket stb.

Bár új továbbfejlesztett CLI-és SDK-ügyfelek találhatók a jelenlegi kiadásban, maga a Desktop Workbench alkalmazás is ki lett vonva. A kísérletek a [Azure Portal munkaterület irányítópultján](how-to-track-experiments.md#view-the-experiment-in-the-azure-portal)kezelhetők. Használja az irányítópultot a kísérlet előzményeinek beolvasására, a munkaterülethez rendelt számítási célok, a modellek és a Docker-rendszerképek kezelésére, vagy akár webszolgáltatások üzembe helyezésére.

<a name="timeline"></a>

## <a name="support-timeline"></a>Támogatási idővonal

Január 9 2019-én a Machine Learning Workbench, Azure Machine Learning-kísérletezés és modellkezelés fiókok, valamint a hozzájuk tartozó SDK-és CLI-támogatás megszűnt. 

Az <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a>, a [CLI](reference-azure-machine-learning-cli.md)és a [portál](how-to-manage-workspace.md)használatával minden legújabb funkció elérhető.

## <a name="what-about-run-histories"></a>Mi a helyzet a futtatási előzményekkel?

A régebbi futtatási előzmények már nem érhetők el, így továbbra is megtekintheti a futtatásokat a legújabb verzióban. 

A futtatási előzmények mostantól **kísérletek**. A modell kísérleteit összegyűjtheti, és az SDK, a CLI vagy a Azure Portal használatával is megismerheti azokat.

A portál munkaterület-irányítópultja csak a Microsoft Edge, a Chrome és a Firefox böngészőkben támogatott:

[![Online portál](./media/overview-what-happened-to-workbench/image001.png)](./media/overview-what-happened-to-workbench/image001.png#lightbox)

Indítsa el a modellek betanítása és nyomon követése a futtatási előzményeket az új parancssori felület és SDK-val. Megtudhatja, hogyan az a [oktatóanyag: modellek Azure Machine Learning szolgáltatással](tutorial-train-models-with-aml.md).

## <a name="can-i-still-prep-data"></a>Továbbra is előkészíthetem az adatokat?

A meglévő adatelőkészítési fájlok nem a legújabb kiadásba kerülnek, mert már nem rendelkezünk Machine Learning Workbench szolgáltatással. Azonban továbbra is előkészítheti a modellezéshez szükséges bármilyen méretű adatkészletet.   

Ha bármilyen méretű adatkészletet használ, a [Azure Machine learning adatelőkészítési csomag](https://aka.ms/data-prep-sdk) használatával gyorsan elő tudja készíteni az adatait a modellezés előtt a Python-kód írásával. 

[Ezt](tutorial-data-prep.md) az oktatóanyagot követve többet is megtudhat a Azure Machine learning adat-előkészítési SDK használatáról.

## <a name="will-projects-persist"></a>Megmaradnak a projektek?

Nem fog elveszni sem kód, sem munka. A régebbi verzióban, a projektek helyi könyvtárral rendelkező felhőbeli entitások. A legújabb verzióban helyi konfigurációs fájl használatával csatlakoztatja a helyi címtárakat a Azure Machine Learning szolgáltatás munkaterülethez. Tekintse meg a [legújabb architektúra diagramját](concept-azure-machine-learning-architecture.md).

A projekt tartalmának nagy része már a helyi gépen van. Ezért csak létre kell hoznia egy konfigurációs fájlt a könyvtárban, és hivatkoznia kell a kódban a munkaterülethez való kapcsolódásra. A fájlokat és parancsfájlokat tartalmazó helyi könyvtár használatának folytatásához adja meg a könyvtár nevét a ["Experiment. submit"](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) Python-parancsban vagy a `az ml project attach` CLI-parancs használatával.  Példa:
```python
run = exp.submit(source_directory=script_folder,
                 script='train.py', run_config=run_config_system_managed)
```

[Hozzon létre egy](how-to-manage-workspace.md) munkaterületet az első lépésekhez.

## <a name="what-about-my-registered-models-and-images"></a>Mi a helyzet a regisztrált modellekkel és képekkel?

Ha azt szeretné, hogy továbbra is használhatja őket a régi modellben beállításjegyzék regisztrált modellek kell áttelepíteni az új munkaterülethez. A modellek áttelepítéséhez töltse le a modelleket, majd regisztrálja újra az új munkaterületen. 

A régi rendszerkép beállításjegyzékében létrehozott rendszerképeket nem lehet közvetlenül áttelepíteni az új munkaterületre. A legtöbb esetben a modell lemezkép létrehozása nélkül is üzembe helyezhető. Ha szükséges, létrehozhat egy rendszerképet a modellhez az új munkaterületen. További információkért lásd: [gépi tanulási modellek kezelése, regisztrálása, üzembe helyezése és figyelése](concept-model-management-and-deployment.md).

## <a name="what-about-deployed-web-services"></a>Mi a helyzet az üzembe helyezett webszolgáltatásokkal?

A régi CLI támogatásának befejezése után már nem telepíthet újra modelleket, és nem felügyelheti a modellkezelés-fiókkal eredetileg telepített webszolgáltatásokat. Azonban ezeket a szolgáltatásokat továbbra is működni fog a mindaddig, amíg az Azure Container Service (ACS) továbbra is támogatott.

A legújabb verzióra, a modellek webszolgáltatásként az Azure Container Instances (ACI) vagy az Azure Kubernetes Service (AKS)-fürtöket üzembe helyezve. A FPGA és a Azure IoT Edge is üzembe helyezhető. 

További tudnivalók az alábbi cikkeket:
+ [A modellek üzembe helyezésének helye és módja](how-to-deploy-and-where.md)
+ [Oktatóanyag: Modellek üzembe helyezése Azure Machine Learning szolgáltatással](tutorial-deploy-models-with-aml.md)

## <a name="what-about-the-old-sdk-and-cli"></a>Mi a helyzet a régi SDK-val és a parancssori felülettel?

Igen, januárig továbbra is működni fognak. Lásd az előző [](#timeline)idővonalat. Javasoljuk, hogy kezdje el létrehozni az új kísérleteket és modelleket a legújabb SDK-val vagy parancssori felülettel.

A legújabb kiadásban az új Python SDK használatával bármilyen Python-környezetben használhatja a Azure Machine Learning szolgáltatást. Ismerje meg, hogyan telepítheti a legújabb <a href="https://aka.ms/aml-sdk" target="_blank">SDK-t</a>. A frissített [Azure Machine learning CLI](reference-azure-machine-learning-cli.md) -bővítményt a `az ml` parancsok gazdag készletével is használhatja a szolgáltatással való interakcióhoz bármely parancssori környezetben, beleértve a Azure Cloud Shell is.

## <a name="what-about-visual-studio-code-tools-for-ai"></a>Mi a helyzet Visual Studio Code Tools for AI?

Ebben a legújabb kiadásban a bővítmény átnevezve lett a Visual Studio Code Azure Machine Learningra, és a rendszer kibővítette és továbbfejlesztette az előző új funkciókkal való együttműködéshez.

[![Azure Machine Learning a Visual Studio Code-hoz](./media/overview-what-happened-to-workbench/vscode.png)](./media/overview-what-happened-to-workbench/vscode-big.png#lightbox)

## <a name="what-about-domain-packages"></a>Mi a helyzet a tartománycsomagokkal?

A számítógép-és a szöveges Analitika tartományi csomagjai nem használhatók a Azure Machine Learning legújabb verziójára. A legújabb Azure Machine Learning Python <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a>-val azonban továbbra is létrehozhat és betaníthat számítógépes jövőképet, szöveges és előrejelzési modelleket. A következő témakörből megtudhatja, hogyan telepítheti át a Computer vízió, a Text Analytics és az előrejelzési csomagok [AML-Packages@microsoft.com](mailto:AML-Packages@microsoft.com)használatával létrehozott, már meglévő modelleket.

## <a name="next-steps"></a>További lépések

Ismerje meg [Azure Machine learning szolgáltatás legújabb architektúráját](concept-azure-machine-learning-architecture.md). 

A szolgáltatás áttekintéséhez olvassa el a [Mi az Azure Machine learning Service?](overview-what-is-azure-ml.md)című témakört.

Hozza létre első kísérletét a kétrészes oktatóanyaggal a [környezet és a munkaterület beállításához](tutorial-1st-experiment-sdk-setup.md) és [az első modell](tutorial-1st-experiment-sdk-train.md) betanításához

A munkafolyamat részletesebb megismeréséhez kövesse a [teljes hosszúságú oktatóanyagot](tutorial-train-models-with-aml.md) , amely részletesen ismerteti a modellek betanítását és üzembe helyezését Azure Machine learning szolgáltatással. 
