---
title: Tudnivalók Azure Machine Learning környezetekről
titleSuffix: Azure Machine Learning
description: Ebből a cikkből megtudhatja, milyen előnyökkel jár a gépi tanulási környezet, amely lehetővé teszi a reprodukálható, auditálható és hordozható gépi tanulási függőségek meghatározását számos számítási cél esetében.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: trbye
author: trevorbye
ms.date: 03/18/2020
ms.openlocfilehash: 50ddbffd00e0cbbd0641089613aaa40d03658c9e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80064191"
---
# <a name="what-are-azure-machine-learning-environments"></a>Mik azok a Azure Machine Learning környezetek?
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning környezetek megadja a Python-csomagokat, a környezeti változókat és a szoftver beállításait a képzés és a pontozási szkriptek köré. A futtatási időpontokat (Python, Spark vagy Docker) is megadják. A környezetek a Machine Learning munkaterületen belül felügyelt és verzióval rendelkező entitások, amelyek lehetővé teszik a reprodukálható, auditálható és hordozható gépi tanulási munkafolyamatok különböző számítási célokból való átirányítását.

A helyi számítási feladatokhoz a következőket használhatja `Environment` :
* Fejlessze a betanítási szkriptet.
* Használja ugyanazt a környezetet Azure Machine Learning számítási feladatokhoz a Modelles képzések esetében.
* Telepítse a modellt ugyanazzal a környezettel.

A következő ábra azt szemlélteti, hogyan használható egyetlen `Environment` objektum a futtatási konfigurációban, a képzéshez, valamint a következtetésekhez és a központi telepítési konfigurációhoz a webszolgáltatás üzembe helyezéséhez.

![A Machine learning-munkafolyamatban található környezet ábrája](./media/concept-environments/ml-environment.png)

## <a name="types-of-environments"></a>A környezetek típusai

A környezetek széles körben három kategóriára oszthatók: a *kurátor*, a *felhasználó által felügyelt*és a *rendszer által felügyelt*.

A Azure Machine Learning által biztosított, és a munkaterületen alapértelmezés szerint elérhetővé tett környezetek. Python-csomagok és-beállítások gyűjteményeit tartalmazzák, amelyek segítségével megkezdheti a különböző gépi tanulási keretrendszerek használatának megkezdését. 

A felhasználó által felügyelt környezetekben Ön felelős a környezet beállításához és minden olyan csomag telepítéséhez, amelyet a képzési parancsfájlnak szüksége van a számítási célra. A Conda nem vizsgálja a környezetet, vagy semmit sem telepít Önnek. Ha saját környezetét határozza meg, akkor a verziót `azureml-defaults` `>= 1.0.45` pip-függőségként kell listáznia. Ez a csomag tartalmazza a modell webszolgáltatásként való üzemeltetéséhez szükséges funkciókat.

A rendszer által felügyelt környezeteket akkor használja, ha azt szeretné, hogy a [Conda](https://conda.io/docs/) kezelje a Python-környezetet és a parancsfájlok függőségeit. A szolgáltatás alapértelmezés szerint ezt a típusú környezetet feltételezi, mert a távoli számítási célok nem manuálisan konfigurálhatók.

## <a name="create-and-manage-environments"></a>Környezetek létrehozása és kezelése

Környezeteket a használatával hozhat létre:

* Új `Environment` objektumok definiálása egy kurátori környezet használatával vagy a saját függőségeinek definiálásával.
* Meglévő `Environment` objektumok használata a munkaterületről. Ez a megközelítés lehetővé teszi az egységességet és a reprodukálhatóságot a függőségekkel.
* Importálás meglévő anaconda-környezet definícióból.
* A Azure Machine Learning parancssori felület használata

Adott kódok esetében tekintse meg az [újrahasznosítási környezetek](how-to-use-environments.md#create-an-environment)használatát ismertető témakör "környezet létrehozása" című szakaszát. A környezeteket a munkaterületen is könnyedén kezelheti. Ezek a következő funkciókat tartalmazzák:

* A környezetek automatikusan regisztrálva lesznek a munkaterületen, amikor elküld egy kísérletet. Manuálisan is regisztrálhatók.
* A munkaterületről beolvashat környezeteket a képzéshez vagy az üzembe helyezéshez, vagy szerkesztheti a környezet definícióját.
* A verziószámozással időben megtekintheti a környezetek változásait, ami biztosítja a reprodukálhatóságot.
* Docker-rendszerképeket automatikusan építhet ki a környezetből.

A kód minták esetében tekintse meg az újrahasznosítási környezetek (környezetek kezelése) című szakaszt a [betanításhoz és az üzembe helyezéshez](how-to-use-environments.md#manage-environments).

## <a name="environment-building-caching-and-reuse"></a>Környezetek kiépítése, gyorsítótárazása és újrafelhasználása

A Azure Machine Learning szolgáltatás környezeti definíciókat épít be a Docker-rendszerképekbe és a Conda környezetbe. Emellett gyorsítótárazza a környezeteket, hogy újra felhasználhatók legyenek a következő képzések futtatásakor és a szolgáltatás végpontjának telepítése során.

### <a name="building-environments-as-docker-images"></a>Környezetek kiépítése Docker-rendszerképekként

Amikor először küld el egy futtatást egy környezettel, a Azure Machine Learning szolgáltatás egy ACR- [felépítési feladatot](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview) hív meg a munkaterülethez társított Azure Container Registry (ACR) használatával. Ezután a rendszer gyorsítótárazza a beépített Docker-rendszerképet a munkaterület ACR-ben. A Futtatás végrehajtásának megkezdése után a rendszer lekéri a rendszerképet a számítási cél alapján.

A rendszerkép létrehozása két lépésből áll:

 1. Alaprendszerkép letöltése és a Docker-lépések végrehajtása
 2. Conda-környezet létrehozása a környezeti definícióban megadott Conda-függőségek alapján.

A második lépés kimarad, ha [felhasználó által felügyelt függőségeket](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py)ad meg. Ebben az esetben Ön felelős a Python-csomagok telepítéséhez, az alaprendszerképbe való belefoglalásával, vagy az első lépésben az egyéni Docker-lépések megadásával. Emellett a Python végrehajtható fájl megfelelő helyének megadására is a felelős.

### <a name="image-caching-and-reuse"></a>Képek gyorsítótárazása és újrafelhasználása

Ha ugyanezt a környezeti definíciót használja egy másik futtatáshoz, a Azure Machine Learning szolgáltatás újrahasználja a gyorsítótárazott rendszerképet a munkaterület ACR-ből. 

A gyorsítótárazott képek részleteinek megtekintéséhez használja a [Environment. get_image_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#get-image-details-workspace-) metódust.

Annak megállapításához, hogy fel kell-e használni a gyorsítótárazott képet, vagy újat kell létrehoznia, a szolgáltatás kiszámítja a környezeti definícióból származó [kivonatot](https://en.wikipedia.org/wiki/Hash_table) , és összehasonlítja a meglévő környezetek kivonatával. A kivonat a következőn alapul:
 
 * Kiinduló rendszerkép tulajdonságának értéke
 * Egyéni Docker-lépések tulajdonságának értéke
 * Python-csomagok listája a Conda-definícióban
 * A Spark-definícióban található csomagok listája 

A kivonat nem függ a környezet nevétől vagy verziójától. A környezeti definíció módosításait, például a Python-csomagok hozzáadását vagy eltávolítását, illetve a csomag verziószámának módosítását eredményezi, hogy a kivonatoló érték módosul, és elindítja a rendszerkép újraépítését. Ha azonban egyszerűen átnevezi a környezetet, vagy létrehoz egy új környezetet egy meglévő pontos tulajdonságaival és csomagjaival, akkor a kivonatoló érték ugyanaz marad, és a rendszer a gyorsítótárazott képet használja.

Tekintse meg a következő ábrát, amely három környezeti definíciót mutat be. Közülük kettő eltérő névvel és verzióval rendelkezik, de az alap rendszerképek és a Python-csomagok is megegyeznek. Ugyanazzal a kivonattal rendelkeznek, ezért ugyanaz a gyorsítótárazott rendszerképnek felel meg. A harmadik környezet különböző Python-csomagokat és-verziókat tartalmaz, ezért egy másik gyorsítótárazott rendszerképnek felel meg.

![A környezeti gyorsítótárazás diagramja Docker-rendszerképekként](./media/concept-environments/environment-caching.png)

>[!IMPORTANT]
> Ha például olyan környezetet hoz létre, amely nem rögzített csomag-függőséggel rendelkezik ```numpy```, például a környezet a _környezet létrehozásakor_telepített csomag verziószámát fogja használni. Emellett a megfelelő definícióval rendelkező jövőbeli környezetek továbbra is a régi verziót használják. 

A csomag frissítéséhez meg kell adnia egy verziószámot a rendszerkép újraépítésének kényszerítéséhez ```numpy==1.18.1```, például:. Vegye figyelembe, hogy az új függőségek, beleértve a beágyazottkat is, telepítve lesznek, amelyek megszakítják a korábban működő forgatókönyvet.

> [!WARNING]
>  A [környezet. a Build](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#build-workspace--image-build-compute-none-) metódus újraépíti a gyorsítótárazott rendszerképet, amely a nem rögzített csomagok frissítésének lehetséges mellékhatása, valamint az adott gyorsítótárazott rendszerképnek megfelelő összes környezeti definíció esetén a reprodukálhatóság megszakítása.

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [hozhat létre és használhat környezeteket](how-to-use-environments.md) a Azure Machine Learningban.
* A [környezeti osztályhoz](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py)tartozó Python SDK dokumentációjában talál.
* Tekintse meg az R SDK dokumentációját a [környezetekhez](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments).
