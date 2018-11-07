---
title: Felhasználói elfogadás szakasza a csoportos adatelemzési folyamat életciklusa – Azure |} A Microsoft Docs
description: A célok, feladatok és a felhasználói elfogadás szakaszra a data-adatelemzési projektek le
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: 9dd3ab8c9451ecfe6b095b52f5af083a7c7e9550
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51232759"
---
# <a name="customer-acceptance"></a>Felhasználói elfogadás

Ez a cikk ismerteti a célokat, feladatok és a felhasználói elfogadás szakaszban, a csoportos adatelemzési folyamat (TDSP) társított le. Ez a folyamat egy ajánlott életciklussal, amely használatával a data-adatelemzési projektek strukturálása biztosít. Az életciklus a fő szakaszai, projektek általában végrehajtható, iteratív gyakran ismerteti:

   1. **Az üzleti igények felmérése**
   2. **Adatgyűjtés és adatértelmezés**
   3. **Modellezés**
   4. **Üzembe helyezés**
   5. **Felhasználói elfogadás**

A TDSP életciklus ábrázolása a következő: 

![TDSP-életciklus](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Cél
**A projekt le véglegesítése**: Győződjön meg arról, hogy a folyamat, a modell és az éles környezetben üzembe helyezésükben megfelelnek-e az ügyfél célkitűzéseket.

## <a name="how-to-do-it"></a>Megtudhatja, hogyan teheti
Nincsenek az ebben a szakaszban tárgyalt két fő feladat:

   * **Rendszer érvényesítése**: Győződjön meg arról, hogy az üzembe helyezett modell és a folyamat megfelelnek-e az ügyfelek igényeinek.
   * **Az aktuális ki a projekt**: a projekt kiosztják tartalmazó éles környezetben futtatni kívánja a rendszer.

Az ügyfél ellenőrizni kell, hogy a rendszer megfelel-e a saját üzleti igényeinek megfelelően, és a kérdések megválaszolja az elfogadható pontosságú üzembe helyezéséhez a rendszer az ügyfélalkalmazás által az éles környezetben való használatra. Teljes dokumentáció fejeződik be, és tekintse át. A projekt a gyorsítási értéknek, átadná-off műveletekért felelős az entitáshoz. Ehhez az entitáshoz lehet például az informatikai vagy ügyfél-adatelemzési csapatával, vagy az ügyfél felelős a rendszer az éles üzemben futó ügynök. 

## <a name="artifacts"></a>Összetevők
A fő összetevő, az utolsó szakaszban létrehozott a **lépjen ki a jelentés a projekt az ügyfél**. Ez a műszaki jelentés tartalmazza a projekt összes a részleteit, amelyek hasznosak a rendszer működtetésének módját ismertető. TDSP biztosít egy [lépjen ki a jelentés](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md) sablont. A sablonnal, mert, vagy testre is szabhatja az adott ügyfél igényeinek megfelelően. 


## <a name="next-steps"></a>További lépések

Az alábbiakban a TDSP életciklusának minden lépése mutató hivatkozásokat:

   1. [Az üzleti igények felmérése](lifecycle-business-understanding.md)
   2. [Adatgyűjtés és adatértelmezés](lifecycle-data.md)
   3. [Modellezés](lifecycle-modeling.md)
   4. [Üzembe helyezés](lifecycle-deployment.md)
   5. [Felhasználói elfogadás](lifecycle-acceptance.md)

Azt adja meg a teljes, végpontok közötti forgatókönyvek, amelyek bemutatják, bizonyos forgatókönyvek esetén a folyamat összes lépését. A [példa forgatókönyvek](walkthroughs.md) a cikk a forgatókönyvek miniatűr leírások és hivatkozások listáját tartalmazza. A forgatókönyvek bemutatják, hogyan kombinálhatja a felhőbeli, helyszíni eszközöket és szolgáltatásokat, munkafolyamat vagy folyamat, intelligens alkalmazások létrehozására. 

Hogyan hajtható végre a lépéseket az Azure Machine Learning Studio használó TDSPs példákért lásd [a TSDP használata az Azure Machine Learning](https://aka.ms/datascienceprocess).
