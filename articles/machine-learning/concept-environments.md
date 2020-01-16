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
ms.date: 01/06/2020
ms.openlocfilehash: 8906299cc9e2c000dab2ac9d2a345d9aaf238260
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045858"
---
# <a name="what-are-azure-machine-learning-environments"></a>Mik azok a Azure Machine Learning környezetek?
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning környezetek megadja a Python-csomagokat, a környezeti változókat és a szoftver beállításait a képzés és a pontozási szkriptek köré. A futtatási időpontokat (Python, Spark vagy Docker) is megadják. Ezek a Machine Learning munkaterületen lévő felügyelt és verzióval rendelkező entitások, amelyek lehetővé teszik a reprodukálható, auditálható és hordozható gépi tanulási munkafolyamatok különböző számítási célokból való kezelését.

A helyi számítási `Environment` objektum a következőre használható:
* Fejlessze a betanítási szkriptet.
* Használja ugyanazt a környezetet Azure Machine Learning számítási feladatokhoz a Modelles képzések esetében.
* Telepítse a modellt ugyanazzal a környezettel.

Az alábbi ábra azt szemlélteti, hogyan használható egyetlen `Environment` objektum a futtatási konfigurációban, a képzéshez, valamint a következtetésekhez és a központi telepítési konfigurációhoz a webszolgáltatás üzembe helyezéséhez.

![A Machine learning-munkafolyamatban található környezet ábrája](./media/concept-environments/ml-environment.png)

## <a name="types-of-environments"></a>A környezetek típusai

A környezetek széles körben három kategóriára oszthatók: a *kurátor*, a *felhasználó által felügyelt*és a *rendszer által felügyelt*.

A Azure Machine Learning által biztosított, és a munkaterületen alapértelmezés szerint elérhetővé tett környezetek. Python-csomagok és-beállítások gyűjteményeit tartalmazzák, amelyek segítségével megkezdheti a különböző gépi tanulási keretrendszerek használatának megkezdését. 

A felhasználó által felügyelt környezetekben Ön felelős a környezet beállításához és minden olyan csomag telepítéséhez, amelyet a képzési parancsfájlnak szüksége van a számítási célra. A Conda nem vizsgálja a környezetet, vagy semmit sem telepít Önnek. Ha a saját környezetét határozza meg, a `>= 1.0.45`, hogy a verzió pip-függőségként `azureml-defaults`. Ez a csomag tartalmazza a modell webszolgáltatásként való üzemeltetéséhez szükséges funkciókat.

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

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan [hozhat létre és használhat környezeteket](how-to-use-environments.md) a Azure Machine Learningban.
* A [környezeti osztályhoz](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py)tartozó Python SDK dokumentációjában talál.
* Tekintse meg az R SDK dokumentációját a [környezetekhez](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments).
