---
title: "Felhasználói elfogadás szakasz Team adatok tudományos folyamat életciklus - Azure |} Microsoft Docs"
description: "A célok, feladatok és az adatok tudományos projektek felhasználói elfogadás állapotának a termékek esetében."
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
ms.openlocfilehash: 953f090f775da5e48b2f4ed36550a5624ae4596b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="customer-acceptance"></a>Felhasználói elfogadás

Ez a témakör bemutatja a célok, feladatok, és a társított termékek esetében a **felhasználói elfogadás szakasz** az Team tudományos folyamat. Ez a folyamat egy ajánlott életciklussal, amely segítségével az adatok tudományos projektek struktúra biztosítja. Az életciklus, projektek általában hajtható végre, gyakran ismételt fő szakaszait vázolja fel:

* **Üzleti ismertetése**
* **Adatok megszerzését és ismertetése**
* **Modellezési**
* **Üzembe helyezés**
* **Ügyfelek**

Íme egy vizuális ábrázolását a **Team adatok tudományos folyamat életciklus**. 

![TDSP-Lifecycle2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Cél
* **A projekt termékek véglegesítése**: Ellenőrizze, hogy a folyamat, a modell és a telepítés éles környezetben amelyek ügyfél céljait.

## <a name="how-to-do-it"></a>Menete
Ebben a szakaszban tárgyalt két fő feladatok van:

* **Rendszer érvényesítési**: Ellenőrizze, hogy a telepített modell és a kimenetátirányítási teljesítésének ügyfelek igényeinek megfelelően.
* **Az aktuális ki a projekt**: az entitás számára a rendszer éles környezetben való futtatásához.

Az ügyfél ellenőrizni kell, hogy a rendszer megfelel-e az üzleti igényeknek és a válaszok az ügyfélalkalmazás által használható a kérdéseket a rendszer telepítéséhez az üzemi környezetben elfogadható pontossága. A dokumentáció fejeződik be, és tekintse át. Egy az aktuális ki a projekt műveletek felelős entitásra befejeződött. Ehhez az entitáshoz lehet, például az informatikai vagy felhasználói adatok tudományos team vagy az ügyfél, amely felelős az éles környezetben a rendszert futtató ügynök. 

## <a name="artifacts"></a>Összetevők
A fő összetevő előállított végső ebben a szakaszban a **kilépési jelentés a projekt ügyfél**. Ez az a műszaki és a rendszert, amely hasznos a projekt összes részleteit tartalmazó jelentést. Egy [kilépési jelentés](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md) sablont használja, vagy az adott ügyfélnek kell testre szabott TDSP biztosítja. 


## <a name="next-steps"></a>Következő lépések

Az alábbiakban az életciklus az Team tudományos folyamat minden lépése mutató hivatkozásokat:

* [1. Üzleti ismertetése](lifecycle-business-understanding.md)
* [2. Adatok megszerzését és ismertetése](lifecycle-data.md)
* [3. Modellezési](lifecycle-modeling.md)
* [4. Központi telepítés](lifecycle-deployment.md)
* [5. Ügyfelek](lifecycle-acceptance.md)

Végpont forgatókönyvek, amelyek azt mutatják, a folyamat lépései teljes **meghatározott forgatókönyvek** is rendelkezésre állnak. Szerepel a listában, és kapcsolódik a miniatűr leírásokat a [példa forgatókönyvek](walkthroughs.md) témakör. Ezek bemutatják, hogyan lehet felhő, a helyszíni eszközök és szolgáltatások egyesítése munkafolyamat vagy csővezeték intelligens alkalmazás létrehozása. 

Az adatok tudományos folyamatban lépések végrehajtása, amelyek használják az Azure Machine Learning Studióban, tekintse meg a [Azure ML](http://aka.ms/datascienceprocess) képzési.