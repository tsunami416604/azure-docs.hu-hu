---
title: Modellek üzembe helyezése éles környezetben – Csapatadat-elemzési folyamat
description: Modellek üzembe helyezése éles környezetben, amely lehetővé teszi számukra, hogy aktív szerepet játszanak az üzleti döntések meghozatalában.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722237"
---
# <a name="deploy-models-to-production-to-play-an-active-role-in-making-business-decisions"></a>Modellek üzembe helyezése éles környezetbe, hogy aktív szerepet játszhasson az üzleti döntések meghozatalában

Éles környezetben lehetővé teszi, hogy a modell aktív szerepet játsszon egy vállalkozásban. Az üzembe helyezett modellből származó előrejelzések üzleti döntésekhez használhatók.

## <a name="production-platforms"></a>Termelési platformok

Vannak különböző megközelítések és platformok, hogy terjesszen modellek termelésbe. Íme néhány lehetőség:

- [A modellek üzembe helyezése az Azure Machine Learning segítségével](../how-to-deploy-and-where.md)
- [Modell telepítése az SQL-kiszolgálón](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

>[!NOTE]
>A telepítés előtt biztosítani kell, hogy a modell pontozási késése elég alacsony ahhoz, hogy éles környezetben használható.
>

>[!NOTE]
>Az Azure Machine Learning Studio használatával történő telepítésről [az Azure Machine Learning webszolgáltatás telepítése.](../studio/deploy-a-machine-learning-web-service.md)
>

## <a name="ab-testing"></a>A/B vizsgálat

Ha több modell van élesben, [A/B teszteléssel](https://en.wikipedia.org/wiki/A/B_testing) összehasonlítható a modell teljesítménye. 
 
## <a name="next-steps"></a>További lépések

Forgatókönyvek, amelyek bemutatják a folyamat összes lépését az **adott forgatókönyvek** is rendelkezésre állnak. Ezek a példa forgatókönyvek cikkben szerepelnek a miniatűrleírásokkal, és azokhoz [kapcsolódnak.](walkthroughs.md) Bemutatják, hogyan kombinálhatja a felhőt, a helyszíni eszközöket és szolgáltatásokat egy munkafolyamatba vagy folyamatba egy intelligens alkalmazás létrehozásához. 
