---
title: Mi az a ML-környezet?
titleSuffix: Azure Machine Learning
description: Ebben a cikkben megismerheti a gépi tanulási környezetek előnyeit, amelyek lehetővé teszik a reprodukálható, auditálható és hordozható gépi tanulási függőségi definíciókat a különböző számítási célok között.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: trbye
author: trevorbye
ms.date: 01/06/2020
ms.openlocfilehash: ad520c7e6503f28de0bd5538662c223575a078fa
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2020
ms.locfileid: "75692731"
---
# <a name="what-are-azure-machine-learning-environments"></a>Mik azok a Azure Machine Learning környezetek?
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

A környezetek megadják a Python-csomagokat, a környezeti változókat és a szoftver beállításait a képzés és a pontozási szkriptek, valamint a futtatási idők (Python, Spark vagy Docker) köré. Ezek a Azure Machine Learning munkaterületen található felügyelt és verzióval rendelkező entitások, amelyek lehetővé teszik a reprodukálható, naplózható és hordozható gépi tanulási munkafolyamatokat a különböző számítási célok között.

A helyi számítási feladatokhoz környezeti objektumokat is használhat, hogy kifejlessze a betanítási parancsfájlt, újrahasznosítsa ugyanezt a környezetet Azure Machine Learning számítási modelleken a Modelles képzések esetében, és még a modellt is üzembe helyezheti ugyanazzal a környezettel.

A következő ábra azt szemlélteti, hogy ugyanaz a környezeti objektum használható a futtatási konfigurációjában a betanításhoz, valamint a webszolgáltatások központi telepítésére vonatkozó következtetési és telepítési konfigurációban is.

![A Machine learning-munkafolyamatban található környezet ábrája](./media/concept-environments/ml-environment.png)

## <a name="types-of-environments"></a>A környezetek típusai

A környezetek széles körben három kategóriára oszthatók: a **kurátor**, a **felhasználó által felügyelt** és a **rendszer által felügyelt**.

A Azure Machine Learning által biztosított, és a munkaterületen alapértelmezés szerint elérhetővé tett környezetek. Python-csomagok és-beállítások gyűjteményeit tartalmazzák, amelyek segítségével megkezdheti a különböző gépi tanulási keretrendszerek megkezdését. 

A felhasználó által felügyelt környezetek esetében Ön felelős a környezet beállításában és minden csomag telepítéséhez, amelyet a képzési szkriptnek szüksége van a számítási célra. A Conda nem fogja megtekinteni a környezetet, vagy semmit sem telepít Önnek. Vegye figyelembe, hogy ha a saját környezetét határozza meg, akkor a `>= 1.0.45`, ha pip-függőségként `azureml-defaults`. Ez a csomag tartalmazza a modell webszolgáltatásként való üzemeltetéséhez szükséges funkciókat.

A rendszer által felügyelt környezetek akkor használhatók, ha azt szeretné, hogy a [Conda](https://conda.io/docs/) kezelje a Python-környezetet és a parancsfájlok függőségeit. A szolgáltatás alapértelmezés szerint ezt a típusú környezetet feltételezi, mivel a távoli számítási célok nem manuálisan konfigurálhatók.

## <a name="creating-and-managing-environments"></a>Környezetek létrehozása és kezelése

A környezetek a segítségével hozhatók létre:

* Új `Environment` objektumok definiálása egy kurátori környezet használatával vagy a saját függőségeinek definiálásával
* Meglévő `Environment` objektumok használata a munkaterületről. Ez lehetővé teszi az egységességet és a reprodukálhatóságot a függőségeivel
* Importálás meglévő anaconda-környezet definícióból.

Tekintse meg az [útmutató](how-to-use-environments.md#create-an-environment) adott kódokra vonatkozó példáit. A környezeteket a munkaterületen is könnyedén kezelheti, és a következő funkciókat veheti fel:

* A környezetek automatikusan regisztrálva lesznek a munkaterületen, amikor elküld egy kísérletet. Manuálisan is regisztrálhatók
* A környezetek lekérése a munkaterületről, és azok betanítása, üzembe helyezése vagy szerkesztése a környezeti definícióban
* A verziószámozás lehetővé teszi a környezetek változásainak időbeli változását, és biztosítja a reprodukálhatóságot
* Docker-rendszerképek automatikus létrehozása a környezetből

Lásd az útmutató a [környezetek kezelése](how-to-use-environments.md#manage-environments) című szakaszát.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan [hozhat létre és használhat környezeteket](how-to-use-environments.md) Azure Machine learning
* Tekintse meg a [környezet osztály](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py)Python SDK-dokumentációját.
* Tekintse meg a [környezetekhez](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments)készült R SDK dokumentációs dokumentációját.