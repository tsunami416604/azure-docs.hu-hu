---
title: Mi történt a Machine Learning Workbench?
titleSuffix: Azure Machine Learning service
description: 'További tudnivalók: Mi történt a Machine Learning Workbench alkalmazást, mi változott, az Azure Machine Learning szolgáltatás és a támogatási idősor van.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
ms.reviewer: jmartens
author: j-martens
ms.author: jmartens
ms.date: 01/09/2019
ms.custom: seodec18
ms.openlocfilehash: 5ff065816e287e0d4e37cdea9a7770c9ff806520
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57890746"
---
# <a name="what-happened-to-azure-machine-learning-workbench"></a>Mi történt az Azure Machine Learning Workbench alkalmazással?

Az Azure Machine Learning Workbench alkalmazást és néhány más korai funkciókat is hozzárendelésénél a 2018 szeptember kiadásban, hogy egy továbbfejlesztett módon [architektúra](concept-azure-machine-learning-architecture.md). 

A felhasználói élmény javítása érdekében a kiadás számos jelentős frissítés kéri, a felhasználói visszajelzések tartalmazza. A modell üzembe helyezése a Kísérletezési futtatások fő funkció nem változott. Most már használhatja a nagy teljesítményű, de <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a> és a [Azure CLI-vel](reference-azure-machine-learning-cli.md) a gépi tanulási folyamatok és feladatok elvégzéséhez.  

Az Azure Machine Learning szolgáltatás korábbi verziójában létrehozott összetevők a legtöbb vannak tárolva a saját helyi vagy felhőbeli tárhelyén. Ezek a munkadarabok sohasem fognak eltűnni.

Ebben a cikkben megismerheti, mi változott, és hogyan érinti a már meglévő munkáját az Azure Machine Learning Workbench és annak API-k a kapcsolatban.

>[!Warning]
>Ez a cikk az Azure Machine Learning Studio-felhasználó nem tartozik. Az Azure Machine Learning szolgáltatás vásárlók a Workbench (előzetes verzió) alkalmazás telepítése és/vagy kísérleti és modellkezelési fiókok előzetes kell legyen.


## <a name="what-changed"></a>Mi változott?

Az Azure Machine Learning szolgáltatás legújabb kiadása a következő szolgáltatásokat tartalmazza:
+ A [egyszerűsített Azure-erőforrások modell](concept-azure-machine-learning-architecture.md).
+ A [új portál felhasználói Felületét](how-to-track-experiments.md) a kísérletek kezeléséhez és számítási céljainak.
+ Egy új, szélesebb körű Python <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a>.
+ Az új kibontva [Azure CLI-bővítmény](reference-azure-machine-learning-cli.md) a machine Learning szolgáltatáshoz.

A [architektúra](concept-azure-machine-learning-architecture.md) át lett alakítva a könnyű használhatóság. Több Azure-erőforrás és -fiók helyett csak egy [Azure Machine Learning-munkaterület](concept-azure-machine-learning-architecture.md#workspace) szükséges. Az [Azure Portalon](quickstart-get-started.md) gyorsan létrehozhat munkaterületeket. Munkaterület használatával több felhasználó tud tárolni a képzés és számítási célokat, modell kísérletek, Docker-rendszerképek, üzembe helyezett modellnél és így tovább.

Bár a jelenlegi kiadásban új továbbfejlesztett CLI és az SDK ügyfelek, a workbench asztali alkalmazás maga visszavontuk. Kísérletek kezelhetők a [munkaterület irányítópultot az Azure Portalon](how-to-track-experiments.md#view-the-experiment-in-the-azure-portal). Használja az irányítópultot a kísérlet előzményeinek beolvasására, a munkaterülethez rendelt számítási célok, a modellek és a Docker-rendszerképek kezelésére, vagy akár webszolgáltatások üzembe helyezésére.

<a name="timeline"></a>

## <a name="support-timeline"></a>Támogatási idővonal

A 2019. január 9. támogatja a Machine Learning Workbench, az Azure Machine Learning-kísérletezés és a Modellkezelési fiókok, és azok kapcsolódó SDK-t és a CLI véget ért. 

Ennek használatával érhető el a legújabb funkciókat <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a>, a [CLI](reference-azure-machine-learning-cli.md), és a [portál](quickstart-get-started.md).

## <a name="what-about-run-histories"></a>Mi a helyzet a futtatási előzményekkel?

Régebbi futtatási előzményeket már nem érhetőek el, hogyan továbbra is megtekintheti a futtatások a legújabb verzió. 

Futtatási előzményeket viselő **kísérletek**. A modell kísérletek összegyűjtheti, és ismerje meg azokat az SDK-t, a parancssori felület vagy az Azure portal használatával.

A portál munkaterület irányítópult csak a Microsoft Edge, Chrome és a Firefox böngésző esetében támogatott:

[![Online portálon](./media/overview-what-happened-to-workbench/image001.png)](./media/overview-what-happened-to-workbench/image001.png#lightbox)

Indítsa el a modellek betanítása és nyomon követése a futtatási előzményeket az új parancssori felület és SDK-val. Megtudhatja, hogyan az a [oktatóanyag: modellek Azure Machine Learning szolgáltatással](tutorial-train-models-with-aml.md).

## <a name="can-i-still-prep-data"></a>Továbbra is előkészíthetem az adatokat?

A már meglévő adatfájlok előkészítése nem a legújabb verziót a hordozható, mert a Machine Learning Workbench már nincs. De továbbra is előkészítheti bármilyen méretű készlet a modellezési.   

A méretüktől adatkészletekhez a segítségével a [az Azure Machine Learning Data Prep SDK](https://aka.ms/data-prep-sdk) gyorsan előkészítheti az adatokat a modellezési Python-kód írásával. 

Követheti [ebben az oktatóanyagban](tutorial-data-prep.md) tudhat meg többet az Azure Machine Learning Data Prep SDK használatával.

## <a name="will-projects-persist"></a>Megmaradnak a projektek?

Nem fog elveszni sem kód, sem munka. A régebbi verzióban, a projektek helyi könyvtárral rendelkező felhőbeli entitások. A legújabb verzió csatolása helyi címtárak az Azure Machine Learning szolgáltatáshoz munkaterület a helyi konfigurációs fájl használatával. Tekintse meg a [a legújabb architektúra diagramja](concept-azure-machine-learning-architecture.md).

A projekt tartalmának nagy részét már a helyi gépen. Így egyszerűen hozzon létre egy konfigurációs fájl ebben a könyvtárban, és hivatkozzon arra a kód a munkaterülethez való kapcsolódáshoz. A helyi könyvtárban, amely tartalmazza a fájlok és parancsfájlok használatával. a folytatáshoz adja meg a könyvtár neve a a ["experiment.submit"](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) Python-parancsot, vagy a parancssori felület az ml-projekt csatlakoztatása parancsot.  Példa:
```python
run = exp.submit(source_directory = script_folder, script = 'train.py', run_config = run_config_system_managed)
```

Ismerje meg, hogyan kezdheti el [Python és a fő SDK](quickstart-create-workspace-with-python.md) vagy [az Azure portal](quickstart-get-started.md).

## <a name="what-about-my-registered-models-and-images"></a>Mi a helyzet a regisztrált modellek és lemezképek?

Ha azt szeretné, hogy továbbra is használhatja őket a régi modellben beállításjegyzék regisztrált modellek kell áttelepíteni az új munkaterülethez. A modellek migrálása, töltse le a modelleket, és újból regisztrálja őket az új munkaterületet. 

A rendszerképeket, amelyet a régi rendszerkép-regisztrációs adatbázisban hozott létre, újra létre kell hozni az új munkaterületen, hogy továbbra is használhassa őket. Ezek a lemezképek a következő újra létrehozhatja a [konfigurálása és a rendszerkép létrehozása](how-to-deploy-and-where.md#configureimage) szakaszok. 

## <a name="what-about-deployed-web-services"></a>Mi a helyzet az üzembe helyezett webszolgáltatásokkal?

Most, hogy a régi parancssori Felületet támogatása véget ért, már nem ismételt üzembe helyezése a modellek vagy az eredetileg a Modellkezelési fiók a telepített webszolgáltatások kezelése. Azonban ezeket a szolgáltatásokat továbbra is működni fog a mindaddig, amíg az Azure Container Service (ACS) továbbra is támogatott.

A legújabb verzióra, a modellek webszolgáltatásként az Azure Container Instances (ACI) vagy az Azure Kubernetes Service (AKS)-fürtöket üzembe helyezve. FPGA-kban és az Azure IoT Edge-ben is telepítheti. 

További tudnivalók az alábbi cikkeket:
+ [Hol és hogyan helyezhet üzembe modelleket](how-to-deploy-and-where.md)
+ [Oktatóanyag: Az Azure Machine Learning szolgáltatás modellek üzembe helyezése](tutorial-deploy-models-with-aml.md)

## <a name="what-about-the-old-sdk-and-cli"></a>Mi a helyzet a régi SDK és parancssori felület?

Igen, ezek továbbra is működnek, amíg a január. Lásd az előző [ütemterv](#timeline). Javasoljuk, hogy indítsa el a legújabb SDK vagy parancssori felület az új kísérletek és modellek létrehozása.

A legújabb verziót az új Python SDK használatával bármilyen Python-környezetet az Azure Machine Learning szolgáltatás is dolgozhat. Ismerje meg, hogyan telepítheti a legújabb <a href="https://aka.ms/aml-sdk" target="_blank">SDK-t</a>. Is használhatja a frissített [Azure Machine Learning parancssori bővítmény](reference-azure-machine-learning-cli.md) a számos `az ml` használják a szolgáltatást minden parancssori környezetben, beleértve az Azure Cloud Shell-parancsokat.

## <a name="what-about-visual-studio-code-tools-for-ai"></a>Mi a helyzet a Visual Studio Code Tools for AI?

Ebben a legújabb kiadásban a bővítményt a Visual Studio Code az Azure Machine Learning átnevezte kibontva és továbbfejlesztve, hogy a fenti új funkciók.

[![A Visual Studio Code az Azure Machine Learning](./media/overview-what-happened-to-workbench/vscode.png)](./media/overview-what-happened-to-workbench/vscode-big.png#lightbox)

## <a name="what-about-domain-packages"></a>Mi a helyzet a tartománycsomagokkal?

A számítógépes látástechnológiai, szövegelemzési és előrejelzési tartomány csomagjai nem használható az Azure Machine Learning a legújabb verzióra. Azonban továbbra is hozhat létre és számítógépes látástechnológiai, szöveg és előrejelzési a legújabb Azure Machine Learning Python-modellek betanításához <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a>. Megtudhatja, hogyan telepítheti át a már meglévő modellek a számítógépes látástechnológiai, szövegelemzési és előrejelzési csomagok, forduljon a [ AML-Packages@microsoft.com ](mailto:AML-Packages@microsoft.com).

## <a name="next-steps"></a>További lépések

További információ a [legújabb architektúra az Azure Machine Learning szolgáltatás](concept-azure-machine-learning-architecture.md). 

A szolgáltatás áttekintéséhez olvassa el a [Mi az Azure Machine Learning szolgáltatás?](overview-what-is-azure-ml.md)

A rövid útmutató bemutatja, hogyan hozzon létre egy munkaterületet, hozzon létre egy projektet, futtassa a szkriptet és Fedezze fel az Azure Machine Learning szolgáltatás legújabb verzióját a szkript futtatási előzményeit, próbálja [Ismerkedés az Azure Machine Learning szolgáltatás](quickstart-get-started.md).

A munkafolyamat részletesebb élményt, kövesse a [részletes oktatóanyag](tutorial-train-models-with-aml.md) , amely részletes útmutatásokat tartalmaz az képzés és az Azure Machine Learning szolgáltatás modellek üzembe helyezéséhez. 
