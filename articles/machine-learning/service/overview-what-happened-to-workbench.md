---
title: Mi történik az Azure Machine Learning Workbench alkalmazással? | Microsoft Docs
description: A cikkből megtudhatja, mi történik a Workbench alkalmazással, mi változott az Azure Machine Learningben, és megismerheti a támogatási ütemtervet.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: overview
ms.reviewer: jmartens
author: j-martens
ms.author: jmartens
ms.date: 09/24/2018
ms.openlocfilehash: d90f1791a05d575caedfdd5c696bb1e6d6c29376
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50091916"
---
# <a name="what-is-happening-to-workbench-in-azure-machine-learning-preview"></a>Mi történik a Workbench alkalmazással az Azure Machine Learning (előzetes verzió) szolgáltatásban?

A 2018. szeptemberi kiadásban a Workbench alkalmazás és néhány egyéb korai funkció elavulttá vált, hogy lehetővé tegye a továbbfejlesztett [architektúra](concept-azure-machine-learning-architecture.md) bevezetését. A kiadás számos, ügyfélvisszajelzésen alapuló jelentős frissítést tartalmaz a felhasználói élmény javítása érdekében. Az kísérleti futtatásoktól a modell-üzembehelyezésig az alapvető funkciók nem változtak, de most már használhatja a robusztus <a href="http://aka.ms/aml-sdk" target="_blank">SDK</a> és [CLI](reference-azure-machine-learning-cli.md) eszközöket a gépi tanulási feladatok és folyamatok végrehajtására.  

Ebből a cikkből elsajátíthatja, mi változott, és hogyan érinti ez a már meglévő munkáját az Azure Machine Learning szolgáltatásban.

## <a name="what-changed"></a>Mi változott?

Az Azure Machine Learning szolgáltatás legújabb kiadása a következőket tartalmazza:
+ Egy [egyszerűsített Azure-erőforrásmodell](concept-azure-machine-learning-architecture.md)
+ [Új portál felhasználói felület](how-to-track-experiments.md) a kísérletek és számítási célok kezeléséhez
+ Egy új, szélesebb körű Python <a href="http://aka.ms/aml-sdk" target="_blank">SDK</a>
+ Egy új, kibővített [Azure CLI-bővítmény](reference-azure-machine-learning-cli.md) a gépi tanuláshoz

Az [architektúra](concept-azure-machine-learning-architecture.md) a könnyű használat szem előtt tartásával lett újratervezve. Több Azure-erőforrás és -fiók helyett csak egy [Azure Machine Learning-munkaterület](concept-azure-machine-learning-architecture.md#workspace) szükséges.  Az [Azure Portalon](quickstart-get-started.md) gyorsan létrehozhat munkaterületeket.  A munkaterületet több felhasználó is használhatja tanulási és üzembe helyezési számítási célok, modellkísérletek, Docker-lemezképek, üzembe helyezett modellek stb. tárolására.

Noha a jelenlegi kiadásban új, továbbfejlesztett CLI és SDK ügyfelek vannak, maga az asztali Workbench alkalmazás elavult. Már figyelheti a kísérleteket az [Azure webportál munkaterület irányítópultján](how-to-track-experiments.md#view-the-experiment-in-the-azure-portal). Használja az irányítópultot a kísérlet előzményeinek beolvasására, a munkaterülethez rendelt számítási célok, a modellek és a Docker-rendszerképek kezelésére, vagy akár webszolgáltatások üzembe helyezésére.

## <a name="how-do-i-migrate"></a>Hogyan kell áttelepíteni az adatokat?

Az Azure Machine Learning szolgáltatás korábbi verziójában létrehozott legtöbb munkadarab tárolása a saját helyi vagy felhőbeli tárában történik. Ezek a munkadarabok sohasem fognak eltűnni. Az áttelepítéshez újra kell regisztrálnia az összetevőket a frissített Azure Machine Learning szolgáltatásban. Azt, hogy mit és hogyan telepíthet át, ebből az [áttelepítésről szóló cikkből](how-to-migrate.md) ismerheti meg.

<a name="timeline"></a>

## <a name="support-timeline"></a>Támogatási idővonal

Kísérletezési és modellkezelési fiókjait, valamint a Workbench alkalmazást is használhatja még egy ideig 2018 szeptembere után. Az alábbi erőforrások támogatása fokozatosan szűnik meg a kiadást követő 3-4 hónap folyamán. A régi funkciók dokumentációja továbbra is megtalálható a tartalomjegyzék alján, az [Erőforrások szakaszban](../desktop-workbench/tutorial-classifying-iris-part-1.md).

|Fázis|Korábbi funkciók támogatásának részletezése|
|:---:|----------------|
|1|Megszűnik az _Azure Machine Learning-kísérletezési fiók_ és _modellkezelési fiók_ Azure Portalon és a CLI-ből való létrehozásának lehetősége. A Machine Learning Compute-környezetek CLI-ből való létrehozásának lehetősége is megszűnik. Meglévő fiók esetén a CLI és az asztali Workbench ebben a fázisban továbbra is működni fog.|
|2|Ebben a fázisban minden más, így a fennmaradó API-k és az asztali Workbench támogatása megszűnik.|

[Kezdje meg a migrálást](how-to-migrate.md) még ma. A legújabb funkciók mindegyike elérhető az új <a href="http://aka.ms/aml-sdk" target="_blank">SDK</a>, [CLI](reference-azure-machine-learning-cli.md) és [portál](quickstart-get-started.md) használatával.

## <a name="what-about-run-histories"></a>Mi a helyzet a futtatási előzményekkel?

A futtatási előzmények egy ideig elérhetők maradnak. Ha készen áll az Azure Machine Learning szolgáltatás frissített verziójába történő migrálásra, exportálhatja a futtatási előzményeket, ha szeretné egy példányukat megőrizni.

A jelenlegi kiadásban a futtatási előzmények neve _kísérletek_. Összegyűjtheti a modellkísérleteket, és feltárhatja azokat az SDK, a CLI vagy a webportál használatával.

A portál munkaterület irányítópultjának használata csak Edge, Chrome és Firefox böngészőkkel támogatott.

[ ![Online portál](./media/overview-what-happened-to-workbench/image001.png) ] (./media/overview-what-happened-to-workbench/image001.png#lightbox)


## <a name="can-i-still-prep-data"></a>Továbbra is előkészíthetem az adatokat?

A már meglévő adat-előkészítési fájlok nem hordozhatók a legújabb verzióba, mivel többé már nincs Workbench. Azonban továbbra is előkészítheti az adatokat modellezésre.  

Kisebb adatkészletekkel használhatja az <a href="http://aka.ms/aml-sdk" target="_blank">Azure Machine Learning adat-előkészítési SDK-t</a> az adatok modellezés előtti gyors előkészítésére. 

Használhatja ugyanezt az <a href="http://aka.ms/aml-sdk" target="_blank">SDK</a> készletet nagyobb adatkészletekhez, vagy nagy adatkészletek előkészítésére használhatja az Azure Databricks szolgáltatást. 

## <a name="will-projects-persist"></a>Megmaradnak a projektek?

Nem fog elveszni sem kód, sem munka. A régebbi verzióban, a projektek helyi könyvtárral rendelkező felhőbeli entitások. A legújabb verzióban egy helyi konfigurációs fájllal csatlakoztathat helyi könyvtárakat az Azure Machine Learning-munkaterülethez. [Tekintse meg a legújabb architektúra diagramját](concept-azure-machine-learning-architecture.md).

Mivel a projekt tartalmának nagy része már a helyi gépén volt, egyszerűen csak létre kell hoznia egy konfigurációs fájlt ugyanabban a könyvtárban, és hivatkoznia kell rá a kódban a munkaterülettel való összekapcsoláshoz. [Ismerje meg, hogyan telepítheti át a meglévő projekteket.](how-to-migrate.md#projects)

Ismerje meg, hogyan kezdheti el [a Python használatát a fő SDK-val](quickstart-get-started.md).

## <a name="what-about-my-registers-models-and-images"></a>Mi a helyzet a regisztrált modellekkel és rendszerképekkel?
 
A modelleket, amelyeket a régi modellregisztrációs adatbázisban regisztrált, át kell áttelepítenie az új munkaterületre, ha továbbra is használni szeretné őket. Ezt megteheti, ha [letölti a modelleket, és újból regisztrálja őket](how-to-migrate.md) az új munkaterületen. 

A rendszerképeket, amelyet a régi rendszerkép-regisztrációs adatbázisban hozott létre, újra létre kell hozni az új munkaterületen, hogy továbbra is használhassa őket. Ezt megteheti a [Docker-rendszerkép létrehozása](how-to-deploy-to-aci.md#configure-an-image) szakasz útmutatását követve. 

## <a name="what-about-deployed-web-services"></a>Mi a helyzet az üzembe helyezett webszolgáltatásokkal?

A Machine Learning Modellkezelés-fiókban webszolgáltatásként üzembe helyezett modellek továbbra is működni fognak, amíg az Azure Container Service (ACS) támogatott. Ezek a webszolgáltatások még azután is működni fognak, miután megszűnt a Machine Learning Modellkezelés-fiókok támogatása. Azonban, amikor véget ér a régi CLI támogatása, többé nem fogja tudni kezelni ezeket a webszolgáltatásokat.

Az újabb verzióban a modellek webszolgáltatásként vannak telepítve az [Azure Container Instances](how-to-deploy-to-aci.md) (ACI) szolgáltatásban vagy az [Azure Kubernetes Service](how-to-deploy-to-aks.md) (AKS) fürtjeiben. Emellett [üzembe is helyezheti az FPGA-kban és az IoT Edge-ben](how-to-deploy-and-where.md). Az új SDK vagy CLI használatával újratelepítheti a modelleket, anélkül, hogy módosítania kellene bármelyik pontozási fájlt, függőséget vagy sémát. 

## <a name="what-about-the-old-sdk--cli"></a>Mi a helyzet a régi SDK-val és CLI-vel?

Igen, ezek egy ideig továbbra is működni fognak (lásd a fenti [ütemtervet](#timeline)). Javasoljuk, hogy az új kísérletek és modellek létrehozást a legújabb SDK-val és/vagy CLI-vel kezdje el.

A legújabb kiadásban az új Python SDK használatával bármely Python környezetben kommunikálhat az Azure Machine Learning szolgáltatással. Ismerje meg, hogyan telepítheti a legújabb <a href="http://aka.ms/aml-sdk" target="_blank">SDK-t</a>.  A [frissített Azure CLI gépi tanulási bővítményt](reference-azure-machine-learning-cli.md) is használhatja `az ml` parancsok széles választékával, hogy bármely parancssori felületről interakcióba léphessen a szolgáltatással, többek között az Azure Portal Cloud Shellel.

## <a name="what-about-vs-code-tools-for-ai"></a>Mi a helyzet a VS Code Tools for AI bővítménnyel?

Ezzel a legújabb kiadással a Visual Studio (VS) Code Tools for AI bővítmény szolgáltatásai kibővültek és javultak, hogy együttműködjenek a fenti új szolgáltatásokkal.

[ ![Visual Studio Code Tools for AI](./media/overview-what-happened-to-workbench/vscode.png) ] (./media/overview-what-happened-to-workbench/vscode-big.png#lightbox)

## <a name="what-about-domain-packages"></a>Mi a helyzet a tartománycsomagokkal?

A [Computer Vision, a Text Analytics és a Forecasting](../desktop-workbench/reference-python-package-overview.md) tartománycsomagjai az Azure Machine Learning legújabb verziójával nem használhatók. Azonban az Azure Machine Learning Python legújabb <a href="http://aka.ms/aml-sdk" target="_blank">SDK</a> csomagjával továbbra is létrehozhat és taníthat Computer Vision, Text Analytics és Forcasting modelleket. Ha szeretné megtudni, hogyan telepítse át a meglévő, Computer Vision, Text Analytics és Forecasting csomagokkal felépített modelleket, lépjen velünk kapcsolatba a következő címen: [AML-Packages@microsoft.com](mailto:AML-Packages@microsoft.com).

## <a name="next-steps"></a>További lépések

Ismerje meg [az Azure Machine Learning szolgáltatás legújabb architektúráját](concept-azure-machine-learning-architecture.md), és próbálja ki az egyik rövid útmutatót vagy oktatóanyagot:

* [Az Azure Machine Learning szolgáltatás ismertetése](overview-what-is-azure-ml.md)
* [Rövid útmutató: Munkaterület létrehozása Python használatával](quickstart-get-started.md)
* [Oktatóanyag: Modell betanítása](tutorial-train-models-with-aml.md)
