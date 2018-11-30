---
title: Üzembe helyezési fázis, a csoportos adatelemzési folyamat életciklusa – Azure |} A Microsoft Docs
description: A célok, feladatok és a központi telepítési szakaszra a data-adatelemzési projektek le
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: 30d54b1475188fa886a811b22460d88e53aa564e
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52446585"
---
# <a name="deployment"></a>Környezet

Ez a cikk ismerteti a célokat, feladatok és az üzembe helyezés, a csoportos adatelemzési folyamat (TDSP) társított le. Ez a folyamat egy ajánlott életciklussal, amely használatával a data-adatelemzési projektek strukturálása biztosít. Az életciklus a fő szakaszai, projektek általában végrehajtható, iteratív gyakran ismerteti:

   1. **Az üzleti igények felmérése**
   2. **Adatgyűjtés és adatértelmezés**
   3. **Modellezés**
   4. **Üzembe helyezés**
   5. **Felhasználói elfogadás**

A TDSP életciklus ábrázolása a következő: 

![TDSP-életciklus](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Cél
Egy adatfolyamat a modellek üzembe helyezése éles, akár a végső felhasználói elfogadás éles-szerű környezete. 

## <a name="how-to-do-it"></a>Megtudhatja, hogyan teheti
Ebben a szakaszban tárgyalt fő feladat:

**A modell üzembe helyezése**: a modell és a folyamat üzembe egy éles, akár az alkalmazás használati éles-szerű környezete.

### <a name="operationalize-a-model"></a>Modell üzembe helyezése
Miután egy jól teljesítő modell készletét, működésbe hozhat őket a más alkalmazások általi felhasználáshoz. Az üzleti követelményeitől függően előrejelzéseket valós időben vagy kötegelt alapon történik. Modellek üzembe helyezéséhez elérhetővé teszi azokat az open API felületet. A felület lehetővé teszi, hogy a modell egyszerűen fogják a különböző alkalmazások, például:

   * Online webhelyek
   * Számolótáblák 
   * Irányítópultok
   * Az üzletági alkalmazások 
   * Háttér-alkalmazások 

Az Azure Machine Learning webszolgáltatás-modell operacionalizálás példákért lásd [egy Azure Machine Learning webszolgáltatás üzembe helyezése](../studio/publish-a-machine-learning-web-service.md). Ajánlott eljárás a telemetriai adatok és a figyelést az üzemi modell és az adatfolyamatok úgy, hogy legyen. Ez az eljárás későbbi rendszer állapota jelentéskészítési és hibaelhárítási segítséget.  

## <a name="artifacts"></a>Összetevők

* Egy állapotjelző irányítópult, amely megjeleníti a rendszer állapotának és a kulcs metrikák
* Üzembe helyezési adatok végleges modellezési jelentés
* A végső megoldás architektúrája dokumentum


## <a name="next-steps"></a>További lépések

Az alábbiakban a TDSP életciklusának minden lépése mutató hivatkozásokat:

   1. [Az üzleti igények felmérése](lifecycle-business-understanding.md)
   2. [Adatgyűjtés és adatértelmezés](lifecycle-data.md)
   3. [Modellezés](lifecycle-modeling.md)
   4. [Üzembe helyezés](lifecycle-deployment.md)
   5. [Felhasználói elfogadás](lifecycle-acceptance.md)

Azt adja meg a teljes, végpontok közötti forgatókönyvek, amelyek bemutatják, bizonyos forgatókönyvek esetén a folyamat összes lépését. A [példa forgatókönyvek](walkthroughs.md) a cikk a forgatókönyvek miniatűr leírások és hivatkozások listáját tartalmazza. A forgatókönyvek bemutatják, hogyan kombinálhatja a felhőbeli, helyszíni eszközöket és szolgáltatásokat, munkafolyamat vagy folyamat, intelligens alkalmazások létrehozására. 

Hogyan hajtható végre a lépéseket az Azure Machine Learning Studio használó TDSPs példákért lásd [a TSDP használata az Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).
