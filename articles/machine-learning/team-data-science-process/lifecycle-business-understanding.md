---
title: "Team adatok tudományos folyamat életciklus - Azure szakasza üzleti ismertetése |} Microsoft Docs"
description: "A célok, feladatok és az adatok tudományos projektek üzleti ismertetése szakaszának termékek esetében."
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
ms.openlocfilehash: 2adce61f8185bd86a6a870bb5752fe936701b0af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="business-understanding"></a>Üzleti ismertetés

Ez a témakör bemutatja a célok, feladatok, és a társított termékek esetében a **üzleti ismertetése szakasz** az Team tudományos folyamat. Ez a folyamat egy ajánlott életciklussal, amely segítségével az adatok tudományos projektek struktúra biztosítja. Az életciklus, projektek általában hajtható végre, gyakran ismételt fő szakaszait vázolja fel:

* **Üzleti ismertetése**
* **Adatok megszerzését és ismertetése**
* **Modellezési**
* **Üzembe helyezés**
* **Ügyfelek**

Íme egy vizuális ábrázolását a **Team adatok tudományos folyamat életciklus**. 

![TDSP-Lifecycle2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Célok
* A **változók kulcs** megadott szolgál, amelyek a **célok modell** , és amelynek a kapcsolódó metrikák használnak a projekt sikerességének meghatározásához.
* A megfelelő **adatforrások** azonosítja, hogy az üzleti hozzáféréssel rendelkezik, vagy kell beszereznie.

## <a name="how-to-do-it"></a>Menete
Ebben a szakaszban tárgyalt két fő feladatok van: 

* **Adja meg a célkitűzés**: az ügyfél és az egyéb érintett felek megértéséhez, valamint az üzleti problémák azonosításához. Állítson össze, amelyek az üzleti célokat határozza meg, és adatok tudományos technikák célzó kérdésekre.
* **Adatforrások azonosítása**: a Keresés a vonatkozó adatokat tartalmaz, amelyek megkönnyítik a kérdésekre, amelyek meghatározzák a projekt céljainak.

### <a name="11-define-objectives"></a>1.1 a célok meghatározása

1. Egy központi e lépés célja, hogy azonosítsa a kulcs **üzleti változók** , amelyet az elemzés előre jelezni. Ezek a változók nevezzük a **célok modell** és a hozzájuk társított mérőszámok segítségével meghatározhatja a projekt sikeréhez. Ilyen célok két többek között az értékesítési vagy éppen a csalárd rendelés valószínűségét.

2. Adja meg a **célok projekt** kérése és a "éles" kérdésekre vonatkozó és adott és egyértelműen szűkebbé tenni. Adattudomány ilyen kérdések megválaszolása nevek és számok használata során a rendszer. Az éles kérdések további információkért lásd: [Adattudomány módjáról](https://blogs.technet.microsoft.com/machinelearning/2016/03/28/how-to-do-data-science/) blog. Adattudomány / gépi tanulás általában használt öt típus kérdések megválaszolásához:
 
   * Mennyi vagy hány? (regresszió)
   * Mely kategóriát? (besorolási)
   * Melyik csoportot? (fürtökkel)
   * Az Ez furcsának? (anomáliadetektálás)
   * Melyik lehetőség kell tenni? (ajánlott)

    Határozza meg, amely ezeket a kérdéseket arra utasítja, és hogyan fogadó Ez a funkció az üzleti céljaihoz.

3. Adja meg a **projekt csapata** a szerepkörök és felelősségek meghatározása, a tagjai megadásával. Amely akkor többször további információt ismertté mérföldkő magas szintű tervének kialakításához.  

4. **Adja meg a sikerkritériumok metrikái**. Példa: ügyfél forgalom előrejelzés pontosságát X % elérése a 3 hónapos projekt végén, úgy, hogy azt kínálhat előléptetések forgalom csökkentése érdekében. A metrikák kell **INTELLIGENS**: 
   * **S**ütemezésből 
   * **M**easurable
   * **A**chievable 
   * **R**elevant 
   * **T**ime adathoz kötött 

### <a name="12-identify-data-sources"></a>1.2 az adatforrások azonosítása
A kérdésekre adott válaszok az éles ismert példák tartalmazó adatforrások azonosítása. Keresse meg a következő adatokat:

* Adatok **érintett** a kérdésre. A cél és a cél kapcsolódó szolgáltatások rendelkezünk?
* Adatok egy **pontos** a modell cél és a lehetőségeket.

Nincs ritka, például, hogy a meglévő rendszerek összegyűjtése és oldja meg a problémát, és a projekt céljainak eléréséhez további adatfajták naplózása kell kereséséhez. Ebben az esetben érdemes lehet külső adatforrások keres, vagy frissítse a rendszer új adatok gyűjtéséért felelős ügyfélfeladatot.

## <a name="artifacts"></a>Összetevők
Ebben a szakaszban az alábbiakban a termékek esetében:

* [**A dokumentum charter**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md): szabványos sablon megtalálható a TDSP projekt struktúra definíciójának. Ez az egy élő dokumentumot folyamatosan frissített, új felderítések válnak, és üzleti követelmények változnak. A fontos, hogy ez a dokumentum részletesen, hozzáadása során a felderítési folyamat során többször. Megőrzése az ügyfél és egyéb érintett felek részt vesz a módosítások elvégzése, és egyértelműen kommunikálnak a okok miatt a módosítások.  
* [**Adatforrások**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#raw-data-sources): Ez a **nyers adatforrások** szakasza a **Adatmeghatározások** jelentést, amely megtalálható a TDSP projekt **jelentés** mappát. Meghatározza a nyers adatok eredeti és a rendeltetési helyét. Későbbi szakaszaiban ki további adatait, például az adatok áthelyezése az analitikus környezet parancsfájlok.  
* [**Adatok szótárak**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/DataDictionaries): Ez a dokumentum ismerteti az ügyfél által meghatározott adatok. A leírások információkat tartalmaznak a séma (adattípusok, információkat a ellenőrzési szabályok, ha van ilyen) és az entitás-kapcsolat diagramokat, ha elérhető.

## <a name="next-steps"></a>Következő lépések

Az alábbiakban az életciklus az Team tudományos folyamat minden lépése mutató hivatkozásokat:

* [1. Üzleti ismertetése](lifecycle-business-understanding.md)
* [2. Adatok megszerzését és ismertetése](lifecycle-data.md)
* [3. Modellezési](lifecycle-modeling.md)
* [4. Központi telepítés](lifecycle-deployment.md)
* [5. Ügyfelek](lifecycle-acceptance.md)

Végpont forgatókönyvek, amelyek azt mutatják, a folyamat lépései teljes **meghatározott forgatókönyvek** is rendelkezésre állnak. Szerepel a listában, és kapcsolódik a miniatűr leírásokat a [példa forgatókönyvek](walkthroughs.md) témakör. Ezek bemutatják, hogyan lehet felhő, a helyszíni eszközök és szolgáltatások egyesítése munkafolyamat vagy csővezeték intelligens alkalmazás létrehozása. 

Az adatok tudományos folyamatban lépések végrehajtása, amelyek használják az Azure Machine Learning Studióban, tekintse meg a [Azure ML](http://aka.ms/datascienceprocess) képzési.