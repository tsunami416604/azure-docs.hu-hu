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
ms.openlocfilehash: ed48ab9f85d28bec926120fea87a5ccf8bb6f9af
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/28/2019
ms.locfileid: "75526770"
---
# <a name="deploy-models-to-production-to-play-an-active-role-in-making-business-decisions"></a>Modellek üzembe helyezése éles környezetben az üzleti döntések meghozatala érdekében

Az éles környezetbe állítás lehetővé teszi, hogy egy modell aktív szerepet játsszon a vállalaton belül. Az üzembe helyezett modellből származó előrejelzések üzleti döntések meghozatalára is használhatók.

## <a name="production-platforms"></a>Üzemi platformok

A modellek éles környezetben való üzembe helyezéséhez különböző megközelítések és platformok tartoznak. Íme néhány lehetőség:

- [A modellek üzembe helyezésének helye Azure Machine Learning](../how-to-deploy-and-where.md)
- [Modell üzembe helyezése az SQL-Serverben](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

>[!NOTE]
>Az üzembe helyezés előtt az egyiknek biztosítania kell, hogy a modell pontozásának késése elég alacsony legyen az éles környezetben való használathoz.
>

>[!NOTE]
>A Azure Machine Learning Studio használatával történő üzembe helyezéssel kapcsolatban lásd: [Azure Machine learning webszolgáltatás üzembe helyezése](../studio/deploy-a-machine-learning-web-service.md).
>

## <a name="ab-testing"></a>A/B-tesztelés

Ha több modell van éles környezetben, hasznos lehet a [/B teszt](https://en.wikipedia.org/wiki/A/B_testing) végrehajtása a modellek teljesítményének összehasonlításához. 
 
## <a name="next-steps"></a>Következő lépések

Az **adott forgatókönyvek** folyamatának összes lépését bemutató útmutatókat is megadja. Ezek a példákban láthatók és a miniatűr leírásokkal vannak összekapcsolva a [példában szereplő forgatókönyvek](walkthroughs.md) című cikkben. Bemutatják, hogyan kombinálhatók a felhő, a helyszíni eszközök és a szolgáltatások egy munkafolyamatban vagy folyamatban egy intelligens alkalmazás létrehozásához. 
