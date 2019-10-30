---
title: A csoportos adatelemzési folyamat életciklusa
description: A csoportos adatelemzési folyamat (TDSP) egy ajánlott életciklust biztosít, amelyet az adatelemzési projektek felépítéséhez használhat.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 00efe89314d4a1a5c3302e820b8609adf194aa59
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053246"
---
# <a name="the-team-data-science-process-lifecycle"></a>A csoportos adatelemzési folyamat életciklusa

A csoportos adatelemzési folyamat (TDSP) egy ajánlott életciklust biztosít, amelyet az adatelemzési projektek felépítéséhez használhat. Az életciklus az elejétől a végéig terjedő lépéseket ismerteti, amelyeket a projektek általában a végrehajtásuk után követnek. Ha más adattudományi életciklust használ, mint például az adatbányászat [(éles DM)](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), az adatbázisok [(KDD](https://wikipedia.org/wiki/Data_mining#Process)-k) és a szervezet saját egyéni folyamatának az iparági szabványos folyamata, továbbra is használhatja a feladat-alapú TDSP. 

Ez az életciklus olyan adattudományi projektekhez készült, amelyek intelligens alkalmazások részeként való szállításra szolgálnak. Ezek az alkalmazások gépi tanulási vagy mesterséges intelligencia-modelleket helyeznek üzembe a prediktív elemzésekhez. A felderítő adatelemzési projektek és az alkalmi elemzési projektek is hasznosak lehetnek a folyamat használatából. Ezen projektek esetében azonban előfordulhat, hogy az itt ismertetett lépések némelyike nem szükséges. 

## <a name="five-lifecycle-stages"></a>Öt életciklus szakasz

A TDSP életciklusa öt fő szakaszból áll, amelyek a iteratív-t futtatják. Ezek a szakaszok a következők:

   1. [Üzleti ismeretek](lifecycle-business-understanding.md)
   2. [Adatgyűjtés és-megértés](lifecycle-data.md)
   3. [Modellezés](lifecycle-modeling.md)
   4. [Üzembe helyezés](lifecycle-deployment.md)
   5. [Ügyfél-elfogadás](lifecycle-acceptance.md)

Itt látható a TDSP életciklus vizuális ábrázolása: 

![TDSP életciklusa](./media/lifecycle/tdsp-lifecycle2.png) 


A TDSP életciklusa ismétlődő lépések sorozatából áll, amely útmutatást nyújt a prediktív modellek használatához szükséges feladatokhoz. A prediktív modelleket olyan éles környezetben helyezheti üzembe, amelyet az intelligens alkalmazások létrehozásához tervez használni. A folyamat életciklusának célja, hogy továbbra is egy adattudományi projektet helyezzen át egy egyértelmű engagement-végpont irányába. Az adatelemzés a kutatás és a felderítés terén folytatott gyakorlat. A feladatoknak a csapatával és ügyfeleivel való kommunikációjának lehetősége a szabványosított sablonokat használó, jól definiált összetevők használatával a félreértések elkerülése érdekében. Ezen sablonok használatával a komplex adattudományi projektek sikeres befejezésének esélye is növekszik.

Az egyes fázisok esetében a következő információkat adjuk meg:

   * **Célok**: az adott célkitűzések.
   * **Útmutató**: a feladatok végrehajtásának részletes ismertetése és útmutatása.
   * Összetevők **: a**termékek és a hozzájuk tartozó támogatás.

## <a name="next-steps"></a>Következő lépések

Teljes körű útmutatót biztosítunk, amely bemutatja a folyamat összes lépését adott forgatókönyvek esetén. A [példákat](walkthroughs.md) bemutató cikk a hivatkozásokat és a miniatűr leírásait tartalmazza. Az útmutató bemutatja, hogyan egyesítheti a felhőt, a helyszíni eszközöket és a szolgáltatásokat egy munkafolyamatban vagy folyamatban egy intelligens alkalmazás létrehozásához. 

A Azure Machine Learning Studiot használó TDSPs lépéseinek végrehajtásával kapcsolatos Példákért lásd: [a TDSP használata Azure Machine learning használatával](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).
