---
title: A csapat az tudományos folyamata életciklus - Azure felhasználói elfogadását szakasza |} Microsoft Docs
description: A célok, feladatok és a adattudomány projektek felhasználói elfogadás állapotának a termékek esetében
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: bradsev
ms.openlocfilehash: 0030698bafc3d92a1c6daf944ba496421080912c
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="customer-acceptance"></a>Felhasználói elfogadás

Ez a cikk ismerteti a célokat, feladatok és a felhasználói elfogadás szakasza a csapat adatok tudományos folyamat (TDSP) társított termékek esetében. Ez a folyamat egy ajánlott életciklussal, amely segítségével a adattudomány projektek struktúra biztosítja. Az életciklus, projektek általában hajtható végre, gyakran ismételt fő szakaszait vázolja fel:

   1. **Üzleti ismertetése**
   2. **Adatok megszerzését és ismertetése**
   3. **Modellezési**
   4. **Üzembe helyezés**
   5. **Ügyfelek**

A TDSP életciklus vizuális ábrázolását itt található: 

![TDSP életciklusa](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Cél
**A projekt termékek véglegesítése**:, hogy a folyamat, a modell és a telepítés éles környezetben megfelelnek-e az ügyfél céljait.

## <a name="how-to-do-it"></a>Menete
Ebben a szakaszban tárgyalt két fő feladatok van:

   * **Rendszer érvényesítési**: Győződjön meg arról, hogy a telepített modell és a kimenetátirányítási megfelelnek-e az ügyfelek igényeinek megfelelően.
   * **Az aktuális ki a projekt**: a projekt kiosztják az entitásban, amelyet a rendszer éles környezetben való futtatásához.

Az ügyfél érdemes ellenőrizni, hogy a rendszer megfelel-e az üzleti igényeiknek és, hogy válaszolja meg a kérdéseket a rendszer az ügyfélalkalmazás által központi telepítését az üzemi használatra elfogadható pontossággal. A dokumentáció fejeződik be, és tekintse át. A projekt van átadni indító műveletek felelős az entitásban. Ehhez az entitáshoz lehet, például az informatikai vagy felhasználói adattudomány csoport vagy az ügyfél, amely felelős az éles környezetben a rendszert futtató ügynök. 

## <a name="artifacts"></a>Összetevők
A fő összetevő előállított végső ebben a szakaszban a **lépjen ki a jelentés a projekt az ügyfél**. Ez a műszaki jelentés tartalmazza az összes a projekt, amelyek hasznosak a rendszer működtetéséről megismerését. TDSP biztosít egy [lépjen ki a jelentés](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md) sablont. Használhatja a sablont, vagy testre is szabhatja az adott ügyfél igényeinek megfelelően. 


## <a name="next-steps"></a>További lépések

Az alábbiakban a TDSP életciklusát lépésre mutató hivatkozásokat:

   1. [Üzleti ismertetése](lifecycle-business-understanding.md)
   2. [Adatok megszerzését és ismertetése](lifecycle-data.md)
   3. [Modellezési](lifecycle-modeling.md)
   4. [Üzembe helyezés](lifecycle-deployment.md)
   5. [Ügyfelek](lifecycle-acceptance.md)

Teljes-végpontok forgatókönyvek, amelyek azt mutatják, meghatározott forgatókönyvek esetén a folyamat lépései a Microsoft biztosítja. A [példa forgatókönyvek](walkthroughs.md) a cikk ismerteti a miniatűr leírások és hivatkozások forgatókönyvek listája. A forgatókönyvek bemutatják, hogyan lehet a felhő, a helyszíni eszközök és szolgáltatások egyesítése munkafolyamat vagy csővezeték intelligens alkalmazás létrehozása. 

Hogyan hajthat végre a lépéseket az Azure Machine Learning Studio használó TDSPs példákért lásd [a TDSP használja az Azure Machine Learning segítségével](http://aka.ms/datascienceprocess).
