---
title: Mi történik az Azure Batch AI? | Microsoft Docs
description: További tudnivalók az Azure Batch AI, mi történik, és az Azure Machine Learning szolgáltatás számítási lehetőségek.
services: batch-ai
author: garyericson
ms.service: batch-ai
ms.topic: overview
ms.date: 12/14/2018
ms.author: garye
ms.openlocfilehash: 6803a47ae77c072eff05df65e37156c90aabf3e6
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53436874"
---
# <a name="whats-happening-to-azure-batch-ai"></a>Mi történik az Azure Batch AI?

**Az Azure Batch AI szolgáltatás márciusi kivonás alatt áll.** Az ipari méretekben tanítási és pontozási a Batch AI képességeit már elérhetők [Azure Machine Learning szolgáltatás](../machine-learning/service/overview-what-is-azure-ml.md), amely óta általánosan elérhető 2018. December 4.

Sok más gépi tanulási funkciókat, valamint az Azure Machine Learning szolgáltatás tartalmaz egy felhőalapú, felügyelt számítási célt képzés, üzembe helyezése és pontozás a machine learning-modellek. A számítási célnak nevezzük [Azure Machine Learning Compute](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute). [Indítsa el a migrálást, és jelenleg is használó](#migrate). Az Azure Machine Learning szolgáltatás segítségével kezelheti a [Python SDK-k](../machine-learning/service/quickstart-create-workspace-with-python.md), parancssori felületet, és a [az Azure portal](../machine-learning/service/quickstart-get-started.md).

## <a name="support-timeline"></a>Támogatási idővonal

| Dátum | A Batch AI szolgáltatás részletei |
| ---- |-----------------|
| December&nbsp;14&#x2c;&nbsp;2018.| Továbbra is a meglévő Azure Batch AI előfizetéseket, mielőtt használja. Azonban regisztrálása **új előfizetések** nem hosszabb lehetséges nem, és ez a szolgáltatás nincs új befektetések fog érkezni.|
| Március&nbsp;31&#x2c;&nbsp;2019 | Ezt a dátumot követően a meglévő Batch AI-előfizetések nem fog működni. |

<a name="migrate"></a>
## <a name="how-do-i-migrate"></a>Hogyan kell áttelepíteni az adatokat?

Az alkalmazások a fennakadások elkerülése érdekében, és kihasználhatják a legújabb funkciók, a következő lépésekkel 2019. március 31. előtt:

1. Hozzon létre egy Azure Machine Learning szolgáltatás munkaterületet, és első lépések:
    + [Python-alapú a rövid útmutató](../machine-learning/service/quickstart-create-workspace-with-python.md)
    + [Az Azure portal alapján rövid útmutatójában](../machine-learning/service/quickstart-get-started.md)

1. Állítsa be egy [Azure Machine Learning Compute](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute) modell betanítása.

1. A parancsfájlok használata az Azure Machine Learning Compute frissítése.

## <a name="support"></a>Támogatás

Támogatás érhető el, annál több átfogó át meglévő képtalálatokat [Azure Machine Learning szolgáltatás](https://aka.ms/aml-docs).

Ha az Azure Machine Learning szolgáltatáshoz nem felel meg az igényeknek, ahol egy támogatott funkciót tartalmaz a Batch AI szolgáltatásban nyissa meg a Batch AI támogatási kérést az ügyfélszolgálathoz, az engedélyezési listára hogy az előfizetése használja a Batch AI szolgáltatás kivonása amíg.

## <a name="next-steps"></a>További lépések

+ Olvassa el a [Azure Machine Learning szolgáltatás áttekintése](../machine-learning/service/overview-what-is-azure-ml.md).

+ [Konfigurálja a modell betanítása és számítási célt](../machine-learning/service/how-to-set-up-training-targets.md) Azure Machine Learning szolgáltatással.

+ Tekintse át a [Azure ütemterve](https://azure.microsoft.com/updates/) további más Azure-szolgáltatás frissítések.
