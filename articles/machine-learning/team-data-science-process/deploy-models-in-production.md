---
title: Az éles környezet – a csoportos adatelemzési folyamat modellek üzembe helyezése
description: Hogyan helyezhet üzembe modelleket az éles környezetbe, így lehetővé válik az üzleti döntések elősegítése érdekében aktív szerepet játszanak.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e3e2ef3340ca836f56176c21cf3d221f0bf172b9
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722237"
---
# <a name="deploy-models-to-production-to-play-an-active-role-in-making-business-decisions"></a>Üzleti döntések elősegítése érdekében az aktív szerepet játszanak az üzemi modellek üzembe helyezése

Éles környezet lehetővé teszi, hogy egy aktív szerepet játszanak egy üzleti modell. A telepített modell előrejelzéses használható üzleti döntéseket hozhasson.

## <a name="production-platforms"></a>Éles rendszerek

Nincsenek különböző módszerek és platformok modellek éles üzembe helyezhető. Az alábbiakban néhány lehetőséget:

- [A modellek üzembe helyezésének helye Azure Machine Learning](../how-to-deploy-and-where.md)
- [Modell üzembe helyezése az SQL-Serverben](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

>[!NOTE]
>Telepítés előtt egy rendelkezik elég alacsony-e az éles környezetben használt modell pontozása késését biztosítja.
>

>[!NOTE]
>A Azure Machine Learning Studio használatával történő üzembe helyezéssel kapcsolatban lásd: [Azure Machine learning webszolgáltatás üzembe helyezése](../studio/deploy-a-machine-learning-web-service.md).
>

## <a name="ab-testing"></a>A / B tesztelés

Ha több modell is éles üzemben van, [a/B tesztelést](https://en.wikipedia.org/wiki/A/B_testing) is használhatja a modell teljesítményének összehasonlítására. 
 
## <a name="next-steps"></a>Következő lépések

Az **adott forgatókönyvek** folyamatának összes lépését bemutató útmutatókat is megadja. Ezek a példákban láthatók és a miniatűr leírásokkal vannak összekapcsolva a [példában szereplő forgatókönyvek](walkthroughs.md) című cikkben. Ezek bemutatják, hogyan kombinálhatja a felhőbeli, helyszíni eszközöket és szolgáltatásokat, munkafolyamat vagy folyamat, intelligens alkalmazások létrehozására. 
