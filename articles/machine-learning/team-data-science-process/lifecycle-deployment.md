---
title: Központi telepítési fázis a csapat az tudományos folyamata életciklus - Azure |} Microsoft Docs
description: A célok, feladatok és a telepítési fázis a adattudomány projektek a termékek esetében
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
ms.openlocfilehash: 5cb6361ed674ffaaf776adafd6f3ff87272c73eb
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="deployment"></a>Környezet

Ez a cikk ismerteti a célokat, feladatok és a központi telepítés a csapat adatok tudományos folyamat (TDSP) társított termékek esetében. Ez a folyamat egy ajánlott életciklussal, amely segítségével a adattudomány projektek struktúra biztosítja. Az életciklus, projektek általában hajtható végre, gyakran ismételt fő szakaszait vázolja fel:

   1. **Üzleti ismertetése**
   2. **Adatok megszerzését és ismertetése**
   3. **Modellezési**
   4. **Üzembe helyezés**
   5. **Ügyfelek**

A TDSP életciklus vizuális ábrázolását itt található: 

![TDSP életciklusa](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Cél
Adatok adatcsatorna modellek telepítése egy éles vagy a végső felhasználói elfogadás hasonló környezetet. 

## <a name="how-to-do-it"></a>Menete
Ebben a szakaszban tárgyalt fő feladat:

**Azok a modell**: a modell és a kimenetátirányítási telepítése egy éles vagy hasonló környezet alkalmazás felhasználásra.

### <a name="operationalize-a-model"></a>Modell üzembe helyezése
Miután egy modellt a hatékony készletét, üzembe azokat más alkalmazások felhasználását. Attól függően, hogy az üzleti igények előrejelzéseket valós időben vagy kötegelt alapon történik. Modellek telepíteni, akkor tegye őket elérhetővé egy megnyitott API felületen. A felület lehetővé teszi, hogy a modell könnyen használható a különböző alkalmazások, például:

   * Online webhelyek
   * Számolótáblák 
   * Irányítópultok
   * Az üzletági alkalmazások 
   * Háttér-alkalmazások 

Az Azure Machine Learning webszolgáltatás a modell operationalization példákért lásd [központi telepítése az Azure Machine Learning webszolgáltatás](../studio/publish-a-machine-learning-web-service.md). Akkor célszerű hozhat létre telemetriai adatok és az üzemi modell és a központilag telepített adatok feldolgozási folyamat figyelést. Ez az eljárás segítségével a jelentések és hibaelhárítás későbbi rendszer állapotú.  

## <a name="artifacts"></a>Összetevők

* A rendszer állapotának és a kulcs metrikák megjelenítő állapota irányítópult
* A végső modellezési központi telepítésének részletei jelentés
* A végső megoldás architektúrája dokumentum


## <a name="next-steps"></a>További lépések

Az alábbiakban a TDSP életciklusát lépésre mutató hivatkozásokat:

   1. [Üzleti ismertetése](lifecycle-business-understanding.md)
   2. [Adatok megszerzését és ismertetése](lifecycle-data.md)
   3. [Modellezési](lifecycle-modeling.md)
   4. [Üzembe helyezés](lifecycle-deployment.md)
   5. [Ügyfelek](lifecycle-acceptance.md)

Teljes-végpontok forgatókönyvek, amelyek azt mutatják, meghatározott forgatókönyvek esetén a folyamat lépései a Microsoft biztosítja. A [példa forgatókönyvek](walkthroughs.md) a cikk ismerteti a miniatűr leírások és hivatkozások forgatókönyvek listája. A forgatókönyvek bemutatják, hogyan lehet a felhő, a helyszíni eszközök és szolgáltatások egyesítése munkafolyamat vagy csővezeték intelligens alkalmazás létrehozása. 

Hogyan hajthat végre a lépéseket az Azure Machine Learning Studio használó TDSPs példákért lásd [a TDSP használja az Azure Machine Learning segítségével](http://aka.ms/datascienceprocess).
