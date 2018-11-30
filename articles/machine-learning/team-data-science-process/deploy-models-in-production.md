---
title: Az éles környezet – az Azure Machine Learning modellek üzembe helyezése |} A Microsoft Docs
description: Hogyan helyezhet üzembe modelleket az éles környezetbe, így lehetővé válik az üzleti döntések elősegítése érdekében aktív szerepet játszanak.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/30/2017
ms.author: tdsp
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: 5b1614f92f7633e008f4f7176723002dc7730b15
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52495973"
---
# <a name="deploy-models-in-production"></a>Modellek üzembe helyezése éles környezetben

Éles környezet lehetővé teszi, hogy egy aktív szerepet játszanak egy üzleti modell. A telepített modell előrejelzéses használható üzleti döntéseket hozhasson.

## <a name="production-platforms"></a>Éles rendszerek

Nincsenek különböző módszerek és platformok modellek éles üzembe helyezhető. Az alábbiakban néhány lehetőséget:

- [Az üzembe helyezés modellek Azure Machine Learning szolgáltatással](../service/how-to-deploy-and-where.md)
- [Egy modellt az SQL-kiszolgáló telepítése](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

>[!NOTE]
>Telepítés előtt egy rendelkezik elég alacsony-e az éles környezetben használt modell pontozása késését biztosítja.
>

>[!NOTE]
>Az Azure Machine Learning Studio használatával olyan központi telepítésre, [egy Azure Machine Learning webszolgáltatás üzembe helyezése](../studio/publish-a-machine-learning-web-service.md).
>

## <a name="ab-testing"></a>A / B tesztelés

Ha több modell éles környezetben, végrehajtásához hasznos lehet [A / B tesztelés](https://en.wikipedia.org/wiki/A/B_testing) a modellek teljesítményének összehasonlítását. 
 
## <a name="next-steps"></a>További lépések

Forgatókönyvek, amelyek bemutatják, a folyamat összes lépését **meghatározott forgatókönyvek** is rendelkezésre állnak. Felsorolt, és a miniatűr leírásokat tartalmazó társított a [példa forgatókönyvek](walkthroughs.md) cikk. Ezek bemutatják, hogyan kombinálhatja a felhőbeli, helyszíni eszközöket és szolgáltatásokat, munkafolyamat vagy folyamat, intelligens alkalmazások létrehozására. 
