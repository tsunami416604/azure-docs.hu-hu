---
title: A csoportos adatelemzési folyamat életciklusa telepítési szakaszában
description: A célok, feladatok és a központi telepítési szakaszra a data-adatelemzési projektek le
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
ms.openlocfilehash: 1138c95274c769186a9a29aa4d35517e378baeae
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720486"
---
# <a name="deployment-stage-of-the-team-data-science-process-lifecycle"></a>A csoportos adatelemzési folyamat életciklusa telepítési szakaszában

Ez a cikk ismerteti a célokat, feladatok és az üzembe helyezés, a csoportos adatelemzési folyamat (TDSP) társított le. Ez a folyamat egy ajánlott életciklussal, amely használatával a data-adatelemzési projektek strukturálása biztosít. Az életciklus a fő szakaszai, projektek általában végrehajtható, iteratív gyakran ismerteti:

   1. **Üzleti ismeretek**
   2. **Adatgyűjtés és-megértés**
   3. **Modellezés**
   4. **Üzembe helyezés**
   5. **Ügyfél-elfogadás**

A TDSP életciklus ábrázolása a következő: 

![TDSP-életciklus](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Cél
Egy adatfolyamat a modellek üzembe helyezése éles, akár a végső felhasználói elfogadás éles-szerű környezete. 

## <a name="how-to-do-it"></a>Megtudhatja, hogyan teheti
Ebben a szakaszban tárgyalt fő feladat:

**Működővé tenni**: a modell és a folyamat üzembe helyezése éles vagy éles környezetben az alkalmazások felhasználásához.

### <a name="operationalize-a-model"></a>Modell üzembe helyezése
Miután egy jól teljesítő modell készletét, működésbe hozhat őket a más alkalmazások általi felhasználáshoz. Az üzleti követelményeitől függően előrejelzéseket valós időben vagy kötegelt alapon történik. Modellek üzembe helyezéséhez elérhetővé teszi azokat az open API felületet. A felület lehetővé teszi, hogy a modell egyszerűen fogják a különböző alkalmazások, például:

   * Online webhelyek
   * Számolótáblák 
   * Irányítópultok
   * Az üzletági alkalmazások 
   * Háttér-alkalmazások 

Azure Machine Learning webszolgáltatással történő operacionalizálási kapcsolatos példákért tekintse meg a [Azure Machine learning webszolgáltatás üzembe helyezése](../studio/deploy-a-machine-learning-web-service.md)című témakört. Ajánlott eljárás a telemetriai adatok és a figyelést az üzemi modell és az adatfolyamatok úgy, hogy legyen. Ez az eljárás későbbi rendszer állapota jelentéskészítési és hibaelhárítási segítséget.  

## <a name="artifacts"></a>Összetevők

* Egy állapotjelző irányítópult, amely megjeleníti a rendszer állapotának és a kulcs metrikák
* Üzembe helyezési adatok végleges modellezési jelentés
* A végső megoldás architektúrája dokumentum


## <a name="next-steps"></a>Következő lépések

Az alábbiakban a TDSP életciklusának minden lépése mutató hivatkozásokat:

   1. [Üzleti ismeretek](lifecycle-business-understanding.md)
   2. [Adatgyűjtés és-megértés](lifecycle-data.md)
   3. [Modellezés](lifecycle-modeling.md)
   4. [Üzembe helyezés](lifecycle-deployment.md)
   5. [Ügyfél-elfogadás](lifecycle-acceptance.md)

Teljes körű bemutatókat biztosítunk, amelyek bemutatják a folyamat összes lépését adott forgatókönyvek esetében. A [példákat](walkthroughs.md) bemutató cikk a hivatkozásokat és a miniatűr leírásait tartalmazza. A forgatókönyvek bemutatják, hogyan kombinálhatja a felhőbeli, helyszíni eszközöket és szolgáltatásokat, munkafolyamat vagy folyamat, intelligens alkalmazások létrehozására. 

A Azure Machine Learning Studiot használó TDSPs lépéseinek végrehajtásával kapcsolatos Példákért lásd: [a TDSP használata Azure Machine learning használatával](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).
