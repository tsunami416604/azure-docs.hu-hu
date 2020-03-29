---
title: A csapatadat-elemzési folyamat életciklusa
description: A csapatadat-elemzési folyamat (TDSP) egy ajánlott életciklust biztosít, amely segítségével strukturálhatja az adatelemzési projektek.
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
ms.openlocfilehash: a043a1655950f3ed7688e59352f8a912146e12c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720452"
---
# <a name="the-team-data-science-process-lifecycle"></a>A csapatadat-elemzési folyamat életciklusa

A csapatadat-elemzési folyamat (TDSP) egy ajánlott életciklust biztosít, amely segítségével strukturálhatja az adatelemzési projektek. Az életciklus ismerteti a sikeres projektek által követett teljes lépéseket. Ha egy másik adatelemzési életciklust használ, például az iparágközi szabványos adatbányászati [folyamatot (CRISP-DM),](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining)az adatbázisokban található tudásfelderítést [(KDD)](https://wikipedia.org/wiki/Data_mining#Process)vagy a szervezet saját egyéni folyamatát, továbbra is használhatja a feladatalapú TDSP-t. 

Ez az életciklus olyan adatelemzési projektekhez készült, amelyek az intelligens alkalmazások részeként történő szállításra szolgálnak. Ezek az alkalmazások gépi tanulási vagy mesterséges intelligencia modelleket telepítenek a prediktív elemzéshez. A feltáró adatelemzési projektek és a rögtönzött elemzési projektek is profitálhatnak e folyamat használatából. De ezekhez a projektekhez az itt leírt lépések némelyikére nincs szükség. 

## <a name="five-lifecycle-stages"></a>Öt életciklus-szakasz

A TDSP életciklusöt öt fő szakaszból áll, amelyekit iteratív módon hajtják végre. Ezek a szakaszok a következők:

   1. [Üzleti ismertetés](lifecycle-business-understanding.md)
   2. [Adatgyűjtés és adatértelmezés](lifecycle-data.md)
   3. [Modellezés](lifecycle-modeling.md)
   4. [Környezet](lifecycle-deployment.md)
   5. [Felhasználói elfogadás](lifecycle-acceptance.md)

Itt van egy vizuális ábrázolása a TDSP életciklus: 

![TDSP életciklus](./media/lifecycle/tdsp-lifecycle2.png) 


A TDSP életciklus a prediktív modellek használatához szükséges feladatokhoz vezető, iterációs lépések sorozataként van modellezve. A prediktív modelleket az éles környezetben telepíti, amelyet az intelligens alkalmazások létrehozásához kíván használni. A folyamat életciklusának célja, hogy továbbra is egy adatelemzési projektet egy egyértelmű elköteleződési végpont felé helyezze át. Az adattudomány a kutatás és a felfedezés gyakorlata. A feladatok nak a csapattal és az ügyfelekkel való közlésének lehetősége a szabványosított sablonokat alkalmazó összetevők jól meghatározott készletének használatával segít elkerülni a félreértéseket. Ezek a sablonok használata növeli egy összetett adatelemzési projekt sikeres befejezésének esélyét is.

Minden szakaszhoz a következő információkat adjuk meg:

   * **Célok**: A konkrét célkitűzések.
   * **Hogyan kell csinálni:** A konkrét feladatok vázlata és útmutatás a befejezésükhöz.
   * **Leletek**: A termékek és a támogatás, hogy azok előállításához.

## <a name="next-steps"></a>További lépések

Teljes körű forgatókönyveket biztosítunk, amelyek bemutatják a folyamat összes lépését az adott forgatókönyvekhez. A [Példa forgatókönyvek](walkthroughs.md) cikk hivatkozásokat és miniatűr leírásokat tartalmazó forgatókönyvek listáját tartalmazza. A forgatókönyvek bemutatják, hogyan kombinálhatja a felhőt, a helyszíni eszközöket és szolgáltatásokat egy munkafolyamatban vagy folyamatban egy intelligens alkalmazás létrehozásához. 

Példák az Azure Machine Learning Studio tdsp-kben végrehajtott lépések végrehajtásáról: [Use the TDSP with Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).
