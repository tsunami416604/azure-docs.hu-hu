---
title: Modellek üzembe helyezése élesben – csoportos adatelemzési folyamat
description: Modellek üzembe helyezése az éles környezetben, amely lehetővé teszi, hogy aktív szerepet játsszon az üzleti döntések meghozatalában.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/30/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 94843bfc30b0d0d44284d533c715ff9632e6a6bd
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492362"
---
# <a name="deploy-models-to-production-to-play-an-active-role-in-making-business-decisions"></a>Modellek üzembe helyezése éles környezetben az üzleti döntések meghozatala érdekében

Az éles környezetbe állítás lehetővé teszi, hogy egy modell aktív szerepet játsszon a vállalaton belül. Az üzembe helyezett modellből származó előrejelzések üzleti döntések meghozatalára is használhatók.

## <a name="production-platforms"></a>Üzemi platformok

A modellek éles környezetben való üzembe helyezéséhez különböző megközelítések és platformok tartoznak. Íme néhány lehetőség:

- [A modellek üzembe helyezésének helye Azure Machine Learning](../service/how-to-deploy-and-where.md)
- [Modell üzembe helyezése az SQL-Serverben](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

>[!NOTE]
>Az üzembe helyezés előtt az egyiknek biztosítania kell, hogy a modell pontozásának késése elég alacsony legyen az éles környezetben való használathoz.
>

>[!NOTE]
>A Azure Machine Learning Studio használatával történő üzembe helyezéssel kapcsolatban lásd: [Azure Machine learning webszolgáltatás üzembe helyezése](../studio/publish-a-machine-learning-web-service.md).
>

## <a name="ab-testing"></a>A/B tesztelés

Ha több modell van éles környezetben, hasznos lehet a [/B teszt](https://en.wikipedia.org/wiki/A/B_testing) végrehajtása a modellek teljesítményének összehasonlításához. 
 
## <a name="next-steps"></a>További lépések

Az **adott forgatókönyvek** folyamatának összes lépését bemutató útmutatókat is megadja. Ezek a példákban láthatók és a miniatűr leírásokkal vannak összekapcsolva a [példában szereplő forgatókönyvek](walkthroughs.md) című cikkben. Bemutatják, hogyan kombinálhatók a felhő, a helyszíni eszközök és a szolgáltatások egy munkafolyamatban vagy folyamatban egy intelligens alkalmazás létrehozásához. 
