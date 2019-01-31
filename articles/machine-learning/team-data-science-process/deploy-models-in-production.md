---
title: Az éles környezet – a csoportos adatelemzési folyamat modellek üzembe helyezése
description: Hogyan helyezhet üzembe modelleket az éles környezetbe, így lehetővé válik az üzleti döntések elősegítése érdekében aktív szerepet játszanak.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/30/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 1a75c842989cfbaf7bb1880831fda2bc6994622b
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55458602"
---
# <a name="deploy-models-to-production-to-play-an-active-role-in-making-business-decisions"></a>Üzleti döntések elősegítése érdekében az aktív szerepet játszanak az üzemi modellek üzembe helyezése

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
