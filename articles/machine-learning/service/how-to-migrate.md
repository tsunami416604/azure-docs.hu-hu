---
title: Az Azure Machine Learning-szolgáltatás áttelepítése
description: Megtudhatja, hogyan frissítheti, vagy egy korábbi verziójáról az Azure Machine Learning szolgáltatás késői verziójára való áttérést.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: haining
author: haining
ms.date: 09/24/2018
ms.openlocfilehash: 8517b469e622068b37bb0fde34f5cfcd8703c7ee
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52877270"
---
# <a name="migrate-to-the-latest-version-of-azure-machine-learning-service"></a>Az Azure Machine Learning szolgáltatás legújabb verziójára való áttérést 

**Ha telepítette a Workbench alkalmazást, illetve kísérleti és modellkezelési fiókok előzetes rendelkezik, ez a cikk segítségével áttelepítheti az a legújabb verzióra.**  Ha az előzetes verziójú Workbench telepítve, vagy egy Kísérletezési, illetve a modellkezelési fiók nem rendelkezik, nem kell áttelepíteni semmit.

## <a name="what-can-i-migrate"></a>Milyen telepíthet át?
Az első Azure Machine Learning szolgáltatás előzetes verzióban létrehozott legtöbb összetevők tárolására is szolgál a saját helyi vagy felhőbeli tárhelyén. Ezek az összetevők nem eltűnik. A migráláshoz az összetevők való újbóli regisztráláshoz a frissített Azure Machine Learning szolgáltatásban. 

A következő táblázat és a cikk elmagyarázza, mit tehet a meglévő eszközeivel és erőforrásaival előtt vagy után az Azure Machine Learning szolgáltatás legújabb verziójára történő fölé helyezi. Az előző verzió és az eszközök használatához egy kis ideig folytathatja ([tekintse meg a támogatási idősor átmenet](overview-what-happened-to-workbench.md#timeline)).

|Eszköz vagy az erőforrás régi verzió|Telepíthetek át?|Műveletek|
|-----------------|:-------------:|-------------|
|Gépi tanulási modelleket (a helyi fájlok)|Igen|Nincs. Ahogy korábban is működik.|
|A modellekre függőségek & sémák (a helyi fájlok)|Igen|Nincs. Ahogy korábban is működik.|
|Projektek|Igen|[Új munkaterület a helyi mappát csatolni](#projects).|
|Futtatási előzmények|Nem|[Letölthető](#history) egy ideig.|
|Adat-előkészítési fájlok|Nem|[Bármilyen méretű adatkészlet előkészítése](#dataprep) a modellezési az új Azure Machine Learning Data Prep SDK használatával, vagy használhatja az Azure Databricks.|
|Számítási céljainak|Nem|Regisztrálja őket az új munkaterületet.|
|A regisztrált modellek|Nem|Regisztrálja újra a modell alapján egy új munkaterületet.|
|Regisztrált jegyzékek|Nem|Nincs. Jegyzékek már nem létezik, az új munkaterület egy fogalom megértéséhez.|
|Regisztrált képek|Nem|Hozza létre újra a központi telepítés Docker-rendszerkép alapján egy új munkaterületet.|
|Üzembe helyezett webszolgáltatások|Nem|Nincs. Fogja is működnek, mint-akkor <br/>vagy [üzembe helyezhetik azokat újra legújabb verzió](#services).|
|Kísérletezési és <br/>Modellkezelési fiókok|Nem|[Hozzon létre egy munkaterületet](#resources) helyette.|
|A Machine learning CLI & SDK|Nem|Az új [CLI](reference-azure-machine-learning-cli.md) és [SDK](https://aka.ms/aml-sdk) az új feladatok.|


Tudjon meg többet [mi változott, ebben a kiadásban](overview-what-happened-to-workbench.md)?

>[!Warning]
>Ez a cikk az Azure Machine Learning Studio-felhasználó nem tartozik. Az Azure Machine Learning szolgáltatás vásárlók a Workbench (előzetes verzió) alkalmazás telepítése és/vagy kísérleti és modellkezelési fiókok előzetes kell legyen.

<a name="resources"></a>

## <a name="azure-resources"></a>Azure-erőforrások

Erőforrások, például a kísérletezés-fiókokat, a modellkezelési fiókok és a machine learning számítási környezetek nem át az Azure Machine Learning szolgáltatás legújabb verziójára. Tekintse meg a [ütemterv](overview-what-happened-to-workbench.md#timeline) a mennyi az eszközök továbbra is működni fog.

Hozzon létre egy Azure Machine Learning szolgáltatás munkaterületén az első lépések a legújabb verzióra a [az Azure portal](quickstart-get-started.md). A portál munkaterület irányítópultjának használata csak Microsoft Edge, Chrome és Firefox böngészőkkel támogatott.

Ehhez az új munkaterülethez a legfelső szintű szolgáltatás-erőforrás, és lehetővé teszi, hogy az összes az Azure Machine Learning szolgáltatás legújabb funkcióit használja. További tudnivalók ezzel [munkaterületet és architektúra](concept-azure-machine-learning-architecture.md).

<a name="projects"></a>

## <a name="projects"></a>Projektek

A projektek nem a felhőben munkaterülete, projektek immár könyvtárak legújabb kiadásának a helyi gépen. Tekintse meg a diagram a [legújabb architektúra](concept-azure-machine-learning-architecture.md). 

A helyi könyvtárban, amely tartalmazza a fájlok és parancsfájlok használatával. a folytatáshoz adja meg a könyvtár neve a a ["experiment.submit"](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) Python-parancsot, vagy a parancssori felület az ml-projekt csatlakoztatása parancsot.

Példa:
```python
run = exp.submit(source_directory = script_folder, script = 'train.py', run_config = run_config_system_managed)
```

<a name="services"></a>

## <a name="deployed-web-services"></a>Üzembe helyezett webszolgáltatások

Webszolgáltatások át, telepítse újra a modellek az új központi telepítési célok, az új SDK-t vagy a parancssori felület használatával. Hiba esetén nem kell az eredeti pontozófájl, a modell fájl függőségek fájlok, a környezet fájl és a sémafájlok módosítása. 

A legújabb verzióra, a modellek webszolgáltatásként való telepített [Azure Container Instances](how-to-deploy-to-aci.md) (ACI) vagy [Azure Kubernetes Service](how-to-deploy-to-aks.md) (AKS)-fürtöket. 

További tudnivalók az alábbi cikkeket:
+ [Az aci Szolgáltatásban üzembe helyezése](how-to-deploy-to-aci.md)
+ [Az aks üzembe helyezése](how-to-deploy-to-aks.md)
+ [Oktatóanyag: Azure Machine Learning szolgáltatás a modellek üzembe helyezése](tutorial-deploy-models-with-aml.md)

Amikor [támogatása a korábbi CLI véget ér](overview-what-happened-to-workbench.md#timeline), akkor nem fogja tudni felügyelni az eredetileg a Modellkezelési fiók a telepített webszolgáltatások. Azonban ezeket a szolgáltatásokat továbbra is működni fog a mindaddig, amíg az Azure Container Service (ACS) továbbra is támogatott.

<a name="history"></a>

## <a name="run-history-records"></a>Futtassa a replikálásielőzmény-rekord

Amíg nem ad hozzá a meglévő futtatási előzményeket a régi munkaterület alatt továbbra is, exportálhatja az előzményeket az előző parancssori felülettel rendelkezik. Amikor [támogatása a korábbi CLI véget ér](overview-what-happened-to-workbench.md#timeline), nem lesz többé exportálhatja ezeket futtatási előzményeket.

Indítsa el a modellek betanítása és nyomon követése a futtatási előzményeket az új parancssori felület és SDK-val. Megtudhatja, hogyan az a [oktatóanyag: modellek Azure Machine Learning szolgáltatással](tutorial-train-models-with-aml.md).

Az előző CLI-vel a futtatási előzmények exportálása:

```azurecli
#list all runs
az ml history list

#get details about a particular run
az ml history info

# download all artifacts of a run
az ml history download
```

<a name="dataprep"></a>

## <a name="data-preparation-files"></a>Adatfájlok előkészítése
Adatok előkészítése fájlok nem hordozható, a Workbench nélkül. De továbbra is modellezését, ahol az új Azure Machine Learning Data Prep SDK használatával bármilyen méretű adatkészlet előkészítése vagy az Azure Databricks használata big data jellegű adatkészletek.  [Ismerje meg, hogyan tehet szert az adatelőkészítés SDK](how-to-data-prep.md). 

## <a name="next-steps"></a>További lépések

A rövid útmutató bemutatja, hogyan hozzon létre egy munkaterületet, hozzon létre egy projektet, futtassa a szkriptet és Fedezze fel az Azure Machine Learning szolgáltatás legújabb verzióját a szkript futtatási előzményeit, próbálja [Ismerkedés az Azure Machine Learning szolgáltatás](quickstart-get-started.md).

A munkafolyamat részletesebb élményt kövesse a részletes oktatóanyag, amely részletes útmutatásokat tartalmaz az képzés és az Azure Machine Learning szolgáltatás a modellek üzembe helyezéséhez. 

> [!div class="nextstepaction"]
> [Oktatóanyag: Betanítása és a modellek üzembe helyezése](tutorial-train-models-with-aml.md)
