---
title: Az Azure Machine Learning-környezetek
titleSuffix: Azure Machine Learning
description: Ebben a cikkben ismerje meg a gépi tanulási környezetek előnyeit, amelyek lehetővé teszik a reprodukálható, auditálható és hordozható gépi tanulási függőségi definíciókat a különböző számítási célok között.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: trbye
author: trevorbye
ms.date: 03/18/2020
ms.openlocfilehash: 50ddbffd00e0cbbd0641089613aaa40d03658c9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064191"
---
# <a name="what-are-azure-machine-learning-environments"></a>Mik azok az Azure Machine Learning-környezetek?
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Az Azure Machine Learning-környezetek határozzák meg a Python-csomagokat, a környezeti változókat és a szoftverbeállításokat a betanítási és pontozási parancsfájlok körül. Azt is megadják a futtatási idő (Python, Spark vagy Docker). A környezetek felügyelt és verziózott entitások a Machine Learning-munkaterületen belül, amelyek lehetővé teszik a reprodukálható, auditálható és hordozható gépi tanulási munkafolyamatok at a különböző számítási célok.

A helyi `Environment` számításon lévő objektumot a következőkre használhatja:
* Fejlessze a képzési szkriptet.
* Használja fel újra ugyanazt a környezetet az Azure Machine Learning Compute modellbetanítási méretekben.
* Telepítse a modellt ugyanazzal a környezettel.

Az alábbi ábra bemutatja, hogyan `Environment` használhatja egyetlen objektumot mind a futtatási konfigurációban, a betanításban, valamint a következtetés és a központi telepítés konfigurációjában a webszolgáltatás-telepítésekhez.

![Környezet diagramja gépi tanulási munkafolyamatban](./media/concept-environments/ml-environment.png)

## <a name="types-of-environments"></a>A környezet típusai

A környezetek nagyjából három kategóriába sorolhatók: *kurátor,* *felhasználó által felügyelt*és rendszer által *felügyelt*.

A válogatott környezeteket az Azure Machine Learning biztosítja, és alapértelmezés szerint elérhetők a munkaterületen. Python-csomagok és -beállítások gyűjteményeit tartalmazzák, amelyek segítenek a különböző gépi tanulási keretrendszerek megkezdésében. 

Felhasználó által felügyelt környezetben ön a felelős a környezet beállításáért és minden olyan csomag telepítéséért, amelyre a betanítási parancsfájlnak szüksége van a számítási célhoz. Conda nem ellenőrzi a környezetet, és nem telepít semmit az Ön számára. Ha saját környezetet definiál, pip-függőségként kell felsorolnia `azureml-defaults` a verziót. `>= 1.0.45` Ez a csomag tartalmazza a modell webszolgáltatásként való üzemeltetéséhez szükséges funkciókat.

Rendszer által felügyelt környezetek, ha azt szeretné, hogy a [Conda](https://conda.io/docs/) kezelje a Python-környezet és a parancsfájl-függőségek az Ön számára. A szolgáltatás alapértelmezés szerint feltételezi az ilyen típusú környezetet, mivel hasznos a távoli számítási célokon, amelyek nem manuálisan konfigurálhatók.

## <a name="create-and-manage-environments"></a>Környezetek létrehozása és kezelése

Környezeteket a következő konditeremben hozhat létre:

* Új `Environment` objektumok definiálása, akár válogatott környezet használatával, akár saját függőségek definiálásával.
* Meglévő `Environment` objektumok használata a munkaterületről. Ez a megközelítés lehetővé teszi a konzisztenciát és a függőségek reprodukálhatóságát.
* Importálás egy meglévő Anaconda környezetdefinícióból.
* Az Azure Machine Learning CLI használata

A konkrét kódmintákról a [Betanítási és üzembe helyezési környezetek újrafelhasználása](how-to-use-environments.md#create-an-environment)című szakaszban talál. A környezetek is könnyen kezelhetők a munkaterületen keresztül. Ezek a következő funkciókat tartalmazzák:

* A környezetek automatikusan regisztrálva lesznek a munkaterületre, amikor beküld egy kísérletet. Manuálisan is regisztrálhatók.
* A munkaterületről lehívhat környezeteket a betanításhoz vagy telepítéshez, vagy módosíthatja a környezetdefiníciót.
* A verziószámozás, láthatja a környezetek időbeli módosításait, ami biztosítja a reprodukálhatóságot.
* Docker-rendszerképeket automatikusan hozhat létre a környezetből.

A kódmintákat a [Betanítási és telepítési környezetek újrafelhasználása](how-to-use-environments.md#manage-environments)című szakasz "Környezetek kezelése" című szakaszában talál.

## <a name="environment-building-caching-and-reuse"></a>Környezetépítés, gyorsítótárazás és újrafelhasználás

Az Azure Machine Learning szolgáltatás környezetdefiníciókat hoz létre a Docker-rendszerképek és conda-környezetekbe. Emellett gyorsítótárazza a környezeteket, így azok a későbbi betanítási futtatások és a szolgáltatás végponti telepítések.

### <a name="building-environments-as-docker-images"></a>Környezetek létrehozása Docker-lemezképekként

Általában, amikor először küld el egy futtatást egy környezetben, az Azure Machine Learning szolgáltatás meghívja az [ACR build feladat](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview) az Azure Container Registry (ACR) a workspace társított. A beépített Docker-rendszerkép ezután gyorsítótárazva van a munkaterületi ACR-en. A futtatási végrehajtás kezdetén a rendszerképet a számítási cél lekéri.

A lemezkép összeállítása két lépésből áll:

 1. Alaprendszerkép letöltése és a Docker-lépések végrehajtása
 2. Conda-környezet létrehozása a környezetdefinícióban megadott conda-függőségek szerint.

A második lépés kimarad, ha [a felhasználó által felügyelt függőségeket](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py)adja meg. Ebben az esetben Ön felelős a Python-csomagok telepítéséért, az alaplemezképbe való felvételükbe való felvételükvel, vagy az egyéni Docker-lépések megadásával az első lépésben. Ön felelős a Python-fájl megfelelő helyének megadásáért is.

### <a name="image-caching-and-reuse"></a>Képgyorsítótárazás és újrafelhasználás

Ha ugyanazt a környezetdefiníciót használja egy másik futtatáshoz, az Azure Machine Learning szolgáltatás újrafelhasználja a gyorsítótárazott lemezképet a munkaterületi ACR-ből. 

A gyorsítótárazott lemezkép részleteinek megtekintéséhez használja [az Environment.get_image_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#get-image-details-workspace-) metódust.

Annak meghatározásához, hogy egy gyorsítótárazott lemezképet újra fel kell-e használni, vagy újat szeretne építeni, a szolgáltatás kiszámítja a környezeti definícióból a [kivonatértéket,](https://en.wikipedia.org/wiki/Hash_table) és összehasonlítja azt a meglévő környezetek kivonataival. A kivonat alapja:
 
 * Alapkép tulajdonságértéke
 * Az egyéni docker-lépés tulajdonságértéke
 * Python-csomagok listája a Conda definícióban
 * Csomagok listája a Spark-definícióban 

A kivonat nem függ a környezet nevétól vagy verziójától. A környezetdefiníció-módosítások, például egy Python-csomag hozzáadása vagy eltávolítása, vagy a csomagverzió módosítása, a kivonatérték megváltozását eredményezi, és elindítja a rendszerkép újraépítését. Ha azonban egyszerűen átnevezi a környezetet, vagy létrehoz egy új környezetet egy meglévő pontos tulajdonságaival és csomagjaival, akkor a kivonatértéke ugyanaz marad, és a gyorsítótárazott lemezkép használatos.

Lásd az alábbi ábrát, amely három környezeti definíciót mutat be. Kettő közülük különböző névvel és verzióval rendelkezik, de azonos alaplemezkép és Python-csomagok. Ugyanaz a kivonat, ezért ugyanannak a gyorsítótárazott lemezképnek felelnek meg. A harmadik környezet különböző Python-csomagok és -verziók, és ezért megfelel egy másik gyorsítótárazott lemezkép.

![A környezet gyorsítótárazásának diagramja Docker-lemezképekként](./media/concept-environments/environment-caching.png)

>[!IMPORTANT]
> Ha például ```numpy```nem rögzített csomagfüggőséggel rendelkező környezetet hoz létre, akkor az adott környezet a _környezet létrehozásakor_telepített csomagverziót fogja használni. Is, minden jövőbeli környezetben egyező felbontású továbbra is a régi verziót. 

A csomag frissítéséhez adjon meg egy verziószámot ```numpy==1.18.1```a kép újraépítésének kényszerítéséhez, például . Vegye figyelembe, hogy új függőségek, beleértve a beágyazott is lesz telepítve, amely megtörheti a korábban működő forgatókönyv.

> [!WARNING]
>  Az [Environment.build](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#build-workspace--image-build-compute-none-) metódus újraépíti a gyorsítótárazott lemezképet, a nem rögzített csomagok frissítésének lehetséges mellékhatásával, és megtörve a reprodukálhatóságot az adott gyorsítótárazott lemezképnek megfelelő összes környezeti definícióesetében.

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [hozhat létre és használhat környezeteket](how-to-use-environments.md) az Azure Machine Learningben.
* Tekintse meg a Python SDK referenciadokumentációját a [környezetosztályhoz.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py)
* Lásd az R SDK [dokumentációját a környezetekhez.](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments)
