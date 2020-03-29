---
title: A csapatadat-elemzési folyamat életciklusának ügyfélelfogadási szakasza
description: Az adatelemzési projektek ügyfélelfogadási szakaszának céljai, feladatai és szállítói
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720520"
---
# <a name="customer-acceptance-stage-of-the-team-data-science-process-lifecycle"></a>A csapatadat-elemzési folyamat életciklusának ügyfélelfogadási szakasza

Ez a cikk ismerteti a célokat, feladatokat és a csapatadat-elemzési folyamat (TDSP) ügyfél-elfogadási szakaszához kapcsolódó célokat, feladatokat és eredményeket. Ez a folyamat egy ajánlott életciklust biztosít, amely az adatelemzési projektek strukturálásához használható. Az életciklus felvázolja azokat a főbb szakaszokat, amelyeket a projektek általában végrehajtanak, gyakran ismétlődően:

   1. **Üzleti ismertetés**
   2. **Adatgyűjtés és adatértelmezés**
   3. **Modellezés**
   4. **Környezet**
   5. **Felhasználói elfogadás**

Itt van egy vizuális ábrázolása a TDSP életciklus: 

![TDSP életciklus](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Cél
**A projektvégtermék véglegesítése**: Győződjön meg arról, hogy a folyamat, a modell és azok éles környezetben való üzembe helyezése megfelel-e az ügyfél céljainak.

## <a name="how-to-do-it"></a>Hogyan kell csinálni
Ebben a szakaszban két fő feladatfoglalkozik:

   * **Rendszerérvényesítés:** Győződjön meg arról, hogy az üzembe helyezett modell és folyamat megfelel-e az ügyfél igényeinek.
   * **Projekt átadása:** Adja át a projektet annak az entitásnak, amely a rendszert éles környezetben fogja futtatni.

Az ügyfélnek ellenőriznie kell, hogy a rendszer megfelel-e az üzleti igényeiknek, és hogy elfogadható pontossággal válaszol-e a kérdésekre, hogy a rendszert éles környezetben telepítsék az ügyfél alkalmazásáltali használatra. Minden dokumentáció véglegesítésre és felülvizsgálatra kerül. A projektet átadják a műveletekért felelős entitásnak. Ez az entitás lehet például egy informatikai vagy ügyfél-adatelemzési csapat, vagy az ügyfél ügynöke, aki felelős a rendszer éles környezetben történő futtatásáért. 

## <a name="artifacts"></a>Összetevők
A fő műtárgy, amelyet ebben a végső szakaszban hoztak létre, a **projekt kilépési jelentése az ügyfél számára**. Ez a technikai jelentés tartalmazza a projekt minden olyan részletét, amely hasznos a rendszer működtetésének megismeréséhez. A TDSP [kilépési jelentéssablont](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md) biztosít. Használhatja a sablont úgy, ahogy van, vagy testreszabhatja az adott ügyféligényeknek megfelelően. 


## <a name="next-steps"></a>További lépések

A TDSP életciklusának egyes lépéseire mutató hivatkozások:

   1. [Üzleti ismertetés](lifecycle-business-understanding.md)
   2. [Adatgyűjtés és adatértelmezés](lifecycle-data.md)
   3. [Modellezés](lifecycle-modeling.md)
   4. [Környezet](lifecycle-deployment.md)
   5. [Felhasználói elfogadás](lifecycle-acceptance.md)

Teljes forgatókönyveket biztosítunk, amelyek bemutatják a folyamat összes lépését az adott forgatókönyvekhez. A [Példa forgatókönyvek](walkthroughs.md) cikk hivatkozásokat és miniatűr leírásokat tartalmazó forgatókönyvek listáját tartalmazza. A forgatókönyvek bemutatják, hogyan kombinálhatja a felhőt, a helyszíni eszközöket és szolgáltatásokat egy munkafolyamatban vagy folyamatban egy intelligens alkalmazás létrehozásához. 

Példák az Azure Machine Learning Studio tdsp-kben végrehajtott lépések végrehajtásáról: [Use the TDSP with Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).
