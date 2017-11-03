---
title: "Központi telepítési fázis Team adatok tudományos folyamat életciklus - Azure |} Microsoft Docs"
description: "A célok, feladatok és az adatok tudományos projektek központi telepítési állapotának a termékek esetében."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/02/2017
ms.author: bradsev;
ms.openlocfilehash: b49b3ab696c22928c5f5a4566e059345fd810588
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="deployment"></a>Környezet

Ez a témakör bemutatja a célok, feladatok, és a társított termékek esetében a **telepítési** az Team tudományos folyamat. Ez a folyamat egy ajánlott életciklussal, amely segítségével az adatok tudományos projektek struktúra biztosítja. Az életciklus, projektek általában hajtható végre, gyakran ismételt fő szakaszait vázolja fel:

* **Üzleti ismertetése**
* **Adatok megszerzését és ismertetése**
* **Modellezési**
* **Üzembe helyezés**
* **Ügyfelek**

Íme egy vizuális ábrázolását a **Team adatok tudományos folyamat életciklus**. 

![TDSP-Lifecycle2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Cél
* Adatok adatcsatorna modellek egy éles vagy hasonló környezetet a végfelhasználó elfogadási is települ. 

## <a name="how-to-do-it"></a>Menete
Ebben a szakaszban tárgyalt fő feladat:

* **Azok a modell**: a modell és a kimenetátirányítási telepítése egy éles vagy hasonló környezet alkalmazás felhasználásra.

### <a name="41-operationalize-a-model"></a>4.1-es vagy azok egy modell
Miután egy modellt a hatékony készletét, azokat is kell operationalized más alkalmazások felhasználását. Attól függően, hogy az üzleti igények előrejelzéseket valós időben vagy kötegelt alapon történik. Modellek jelentkezik, mintha egy megnyitott API felülettel vannak telepítve. A felület lehetővé teszi, hogy könnyen használható a különböző alkalmazások, például online webhelyek, táblázatkezelő programok, irányítópultok vagy üzleti és a háttérkiszolgáló alkalmazások át a modellt. Az Azure Machine Learning webszolgáltatás a modell operationalization példákért lásd [központi telepítése az Azure Machine Learning webszolgáltatás](../studio/publish-a-machine-learning-web-service.md). Akkor is célszerű hozhat létre telemetriai adatok és az üzemi modell és a központilag telepített adatok feldolgozási folyamat figyelést. Ez az eljárás segítségével a jelentések és hibaelhárítás későbbi rendszer állapotú.  

## <a name="artifacts"></a>Összetevők
* A rendszer állapotának és a kulcs mérőszámok irányítópult állapotát.
* Végső modellezési központi telepítésének részletei jelentés.
* Végső megoldás architektúrája dokumentum.


## <a name="next-steps"></a>Következő lépések

Az alábbiakban az életciklus az Team tudományos folyamat minden lépése mutató hivatkozásokat:

* [1. Üzleti ismertetése](lifecycle-business-understanding.md)
* [2. Adatok megszerzését és ismertetése](lifecycle-data.md)
* [3. Modellezési](lifecycle-modeling.md)
* [4. Központi telepítés](lifecycle-deployment.md)
* [5. Ügyfelek](lifecycle-acceptance.md)

Végpont forgatókönyvek, amelyek azt mutatják, a folyamat lépései teljes **meghatározott forgatókönyvek** is rendelkezésre állnak. Szerepel a listában, és kapcsolódik a miniatűr leírásokat a [példa forgatókönyvek](walkthroughs.md) témakör. Ezek bemutatják, hogyan lehet felhő, a helyszíni eszközök és szolgáltatások egyesítése munkafolyamat vagy csővezeték intelligens alkalmazás létrehozása. 

Az adatok tudományos folyamatban lépések végrehajtása, amelyek használják az Azure Machine Learning Studióban, tekintse meg a [Azure ML](http://aka.ms/datascienceprocess) képzési.