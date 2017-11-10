---
title: "A csoport az tudományos folyamata életciklus - Azure szakasza üzleti ismertetése |} Microsoft Docs"
description: "A célok, feladatok és az üzleti ismertetése szakasza a adattudomány projektek a termékek esetében"
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
ms.date: 11/04/2017
ms.author: bradsev;
ms.openlocfilehash: 82784cbfd5f12297c376a395b54a817a2ae915a5
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2017
---
# <a name="business-understanding"></a>Üzleti ismertetés

Ez a cikk ismerteti a célokat, feladatok és az üzleti ismertetése szakasza a csapat adatok tudományos folyamat (TDSP) társított termékek esetében. Ez a folyamat egy ajánlott életciklussal, amely segítségével a adattudomány projektek struktúra biztosítja. Az életciklus, projektek általában hajtható végre, gyakran ismételt fő szakaszait vázolja fel:

   1. **Üzleti ismertetése**
   2. **Adatok megszerzését és ismertetése**
   3. **Modellezési**
   4. **Üzembe helyezés**
   5. **Ügyfelek**

A TDSP életciklus vizuális ábrázolását itt található: 

![TDSP életciklusa](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Célok
* Adja meg, amelyek a modell célokat szolgál, és amelynek kapcsolódó metrikák használt kulcs változók a projekt sikerességének meghatározásához.
* Azonosítsa a megfelelő adatforrás az üzleti hozzáféréssel rendelkezik, vagy kell beszereznie.

## <a name="how-to-do-it"></a>Menete
Ebben a szakaszban tárgyalt két fő feladatok van: 

   * **Adja meg a célkitűzés**: az ügyfél és az egyéb érintett felek megértéséhez, valamint az üzleti problémák azonosításához. Állítson össze, amelyek meghatározzák az üzleti célokat, amelyek az adatok tudományos technikák kérdéseket.
   * **Adatforrások azonosítása**: a Keresés a vonatkozó adatokat tartalmaz, amelyek megkönnyítik a kérdésekre, amelyek meghatározzák a projekt céljainak.

### <a name="define-objectives"></a>Célok meghatározása
1. Egy központi ebben a lépésben célja a fő üzleti változókat, amelyek az elemzési előre jelezni kell azonosításához. Azt a változókat, tekintse meg a *célok modell*, és a hozzájuk társított metrikák használatával a projekt sikerességének meghatározásához. Ilyen célok két többek között az értékesítési előrejelzések vagy alatt csalárd rendelés valószínűségét.

2. Adja meg a projekt célok kérése és a "éles" kérdésekre vonatkozó, adott és egyértelműen szűkebbé tenni. Adattudomány használ a nevek és számok ilyen kérdések megválaszolása. Az éles kérdések további információkért lásd: a [adattudomány módjáról](https://blogs.technet.microsoft.com/machinelearning/2016/03/28/how-to-do-data-science/) blog. Általában használhatja adattudomány vagy gépi tanulási öt típus kérdések megválaszolásához:
 
   * Mennyi vagy hány? (regresszió)
   * Mely kategóriát? (besorolási)
   * Melyik csoportot? (fürtökkel)
   * Az Ez furcsának? (anomáliadetektálás)
   * Melyik lehetőség kell tenni? (ajánlott)

   Határozza meg, amely ezeket a kérdéseket most kéri, és hogyan fogadó Ez a funkció az üzleti céljaihoz.

3. A projektcsapat megadása megadását követően a szerepkörök és felelősségek meghatározása, a tagjai. Mivel azt tapasztalja, hogy további információt az a következőnek mérföldkő magas szintű tervének kialakításához. 

4. Adja meg a sikerkritériumokat. Például érdemes olyan ügyfél-forgalom előrejelzésre eléréséhez. A három hónapos projekt végéig pontossága "x" százalék van szüksége. Ezekkel az adatokkal kínálhat kavarog egyidejűleg ügyfél előléptetések csökkentése érdekében. A metrikák kell **INTELLIGENS**: 

   * **S**ütemezésből 
   * **M**easurable
   * **A**chievable 
   * **R**elevant 
   * **T**ime adathoz kötött 

### <a name="identify-data-sources"></a>Adatforrások azonosítása
A kérdésekre adott válaszok az éles ismert példák tartalmazó adatforrások azonosítása. Keresse meg a következő adatokat:

* A kérdésre vonatkozó adatokat. Rendelkezik a cél és a cél kapcsolódó szolgáltatások?
* A modell célként pontos felmérését és a szolgáltatások fontos adatokról.

Előfordulhat például, hogy a meglévő rendszerek kell gyűjteni, és oldja meg a problémát, és a projekt céljainak eléréséhez további adatfajták naplózása. Ebben a helyzetben érdemes keresse meg a külső adatforrásokhoz, vagy frissítse a rendszer új adatok gyűjtéséért felelős ügyfélfeladatot.

## <a name="artifacts"></a>Összetevők
Ebben a szakaszban az alábbiakban a termékek esetében:

   * [Okleveles dokumentum](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md): szabványos sablon megtalálható a TDSP projekt struktúra definíciójának. A bérleti dokumentum egy élő dokumentumot. A sablon a projekt teljes frissíti, akkor ellenőrizze, hogy új felderítések és üzleti követelmények változnak. A fontos, hogy ez a dokumentum részletesen, hozzáadása során a felderítési folyamat során többször. Megőrzése az ügyfél és egyéb érintett felek részt vesz a módosítások elvégzése, és egyértelműen kommunikálnak a okok miatt a módosítások.  
   * [Adatforrások](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#raw-data-sources): A **nyers adatforrások** szakasza a **adatmeghatározások** jelentést, amely megtalálható a TDSP projekt **jelentés** mappa tartalmazza a adatforrások. Ez a szakasz a nyers adatok eredeti és a rendeltetési helyét adja meg. Későbbi szakaszaiban ki további adatait, például a parancsfájlok az adatok áthelyezése az analitikus környezethez.  
   * [Adatok szótárak](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Data_Dictionaries): Ez a dokumentum ismerteti az ügyfél által meghatározott adatok. A leírások információkat tartalmaznak a séma (a az adattípusokat és az információkat az ellenőrzési szabályok, ha van ilyen) és az entitás-kapcsolat diagramokat, ha elérhető.

## <a name="next-steps"></a>Következő lépések

Az alábbiakban a TDSP életciklusát lépésre mutató hivatkozásokat:

   1. [Üzleti ismertetése](lifecycle-business-understanding.md)
   2. [Adatok megszerzését és ismertetése](lifecycle-data.md)
   3. [Modellezési](lifecycle-modeling.md)
   4. [Üzembe helyezés](lifecycle-deployment.md)
   5. [Ügyfelek](lifecycle-acceptance.md)

Teljes-végpontok forgatókönyvek, amelyek azt mutatják, meghatározott forgatókönyvek esetén a folyamat lépései a Microsoft biztosítja. A [példa forgatókönyvek](walkthroughs.md) a cikk ismerteti a miniatűr leírások és hivatkozások forgatókönyvek listája. A forgatókönyvek bemutatják, hogyan lehet a felhő, a helyszíni eszközök és szolgáltatások egyesítése munkafolyamat vagy csővezeték intelligens alkalmazás létrehozása. 

Hogyan hajthat végre a lépéseket az Azure Machine Learning Studio használó TDSPs példákért lásd [a TDSP használja az Azure Machine Learning segítségével](http://aka.ms/datascienceprocess).
