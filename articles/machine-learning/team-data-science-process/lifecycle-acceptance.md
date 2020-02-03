---
title: Felhasználói elfogadás szakasza a csoportos adatelemzési folyamat életciklusa
description: A célok, feladatok és a felhasználói elfogadás szakaszra a data-adatelemzési projektek le
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 7224a7bb26ef491915df9fcb9b6b84ff171a9fc0
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720520"
---
# <a name="customer-acceptance-stage-of-the-team-data-science-process-lifecycle"></a>Felhasználói elfogadás szakasza a csoportos adatelemzési folyamat életciklusa

Ez a cikk ismerteti a célokat, feladatok és a felhasználói elfogadás szakaszban, a csoportos adatelemzési folyamat (TDSP) társított le. Ez a folyamat egy ajánlott életciklussal, amely használatával a data-adatelemzési projektek strukturálása biztosít. Az életciklus a fő szakaszai, projektek általában végrehajtható, iteratív gyakran ismerteti:

   1. **Üzleti ismeretek**
   2. **Adatgyűjtés és-megértés**
   3. **Modellezés**
   4. **Üzembe helyezés**
   5. **Ügyfél-elfogadás**

A TDSP életciklus ábrázolása a következő: 

![TDSP-életciklus](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Cél
**Projekt-végtermékek véglegesítése**: Ellenőrizze, hogy a folyamat, a modell és az üzemi környezetben való üzembe helyezése megfelel-e az ügyfél célkitűzéseinek.

## <a name="how-to-do-it"></a>Megtudhatja, hogyan teheti
Nincsenek az ebben a szakaszban tárgyalt két fő feladat:

   * **Rendszer-ellenőrzés**: Ellenőrizze, hogy az üzembe helyezett modell és a folyamat megfelel-e az ügyfél igényeinek.
   * **Projekt-kikapcsolás**: kapcsolja ki a projektet arra az entitásra, amelyen a rendszer éles környezetben fog futni.

Az ügyfél ellenőrizni kell, hogy a rendszer megfelel-e a saját üzleti igényeinek megfelelően, és a kérdések megválaszolja az elfogadható pontosságú üzembe helyezéséhez a rendszer az ügyfélalkalmazás által az éles környezetben való használatra. Teljes dokumentáció fejeződik be, és tekintse át. A projekt a gyorsítási értéknek, átadná-off műveletekért felelős az entitáshoz. Ehhez az entitáshoz lehet például az informatikai vagy ügyfél-adatelemzési csapatával, vagy az ügyfél felelős a rendszer az éles üzemben futó ügynök. 

## <a name="artifacts"></a>Összetevők
Az ebben az utolsó szakaszban létrehozott fő összetevő az **ügyfélre vonatkozó projekt kilépési jelentése**. Ez a műszaki jelentés tartalmazza a projekt összes a részleteit, amelyek hasznosak a rendszer működtetésének módját ismertető. A TDSP egy [kilépési jelentési](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md) sablont biztosít. A sablonnal, mert, vagy testre is szabhatja az adott ügyfél igényeinek megfelelően. 


## <a name="next-steps"></a>Következő lépések

Az alábbiakban a TDSP életciklusának minden lépése mutató hivatkozásokat:

   1. [Üzleti ismeretek](lifecycle-business-understanding.md)
   2. [Adatgyűjtés és-megértés](lifecycle-data.md)
   3. [Modellezés](lifecycle-modeling.md)
   4. [Üzembe helyezés](lifecycle-deployment.md)
   5. [Ügyfél-elfogadás](lifecycle-acceptance.md)

Teljes körű bemutatókat biztosítunk, amelyek bemutatják a folyamat összes lépését adott forgatókönyvek esetében. A [példákat](walkthroughs.md) bemutató cikk a hivatkozásokat és a miniatűr leírásait tartalmazza. A forgatókönyvek bemutatják, hogyan kombinálhatja a felhőbeli, helyszíni eszközöket és szolgáltatásokat, munkafolyamat vagy folyamat, intelligens alkalmazások létrehozására. 

A Azure Machine Learning Studiot használó TDSPs lépéseinek végrehajtásával kapcsolatos Példákért lásd: [a TDSP használata Azure Machine learning használatával](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).
