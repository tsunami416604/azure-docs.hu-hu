---
title: A csoportos adatelemzési folyamat az üzleti igények felmérése
description: A célok, feladatok és a data-adatelemzési projektek a csoportos adatelemzési folyamat az üzleti ismertetése szakaszának le.
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
ms.openlocfilehash: a7aaed519f8f97a9be77a263568aeed5257c16d6
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76710332"
---
# <a name="the-business-understanding-stage-of-the-team-data-science-process-lifecycle"></a>Az üzleti ismertetése szakaszban a csoportos adatelemzési folyamat életciklusa

Ez a cikk ismerteti a célokat, feladatok és az üzleti ismertetése szakaszban, a csoportos adatelemzési folyamat (TDSP) társított le. Ez a folyamat egy ajánlott életciklussal, amely használatával a data-adatelemzési projektek strukturálása biztosít. Az életciklus a fő szakaszai, projektek általában végrehajtható, iteratív gyakran ismerteti:

   1. **Üzleti ismeretek**
   2. **Adatgyűjtés és-megértés**
   3. **Modellezés**
   4. **Üzembe helyezés**
   5. **Ügyfél-elfogadás**

A TDSP életciklus ábrázolása a következő: 

![TDSP-életciklus](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Célok
* Adja meg a kulcs változókat, amelyek a modell célokat szolgál, és amelynek kapcsolódó metrikák használt határozza meg a projekt sikerét.
* A releváns adatforrások, amelyek az üzleti férhet hozzá, vagy szerezze be kell azonosítani.

## <a name="how-to-do-it"></a>Megtudhatja, hogyan teheti
Nincsenek az ebben a szakaszban tárgyalt két fő feladat: 

   * **Célkitűzések meghatározása**: az üzleti problémák megismeréséhez és azonosításához használja az ügyfelet és más érdekelteket. Állítson össze, amelyek meghatározzák az üzleti célokat, a data science technikák célzó kérdéseket.
   * **Adatforrások azonosítása**: keresse meg a releváns adatokat, amelyek segítségével választ kaphat a projekt céljait meghatározó kérdésekre.

### <a name="define-objectives"></a>Célok meghatározása
1. Egy központi Ez a lépés célja, hogy azonosítsa a fő üzleti változókat, amelyek előre jelezni kell az elemzés. Ezeket a változókat a *modell céljaként*tekintjük át, és a hozzájuk társított metrikákat használjuk a projekt sikerességének meghatározásához. Az ilyen tárolók két példák értékesítési előrejelzések vagy egy folyamatban a csalárd jellegű megrendelés valószínűségét.

2. A projekt célok meghatározása szerint kéri, és az "éles" kérdések, amelyek a kapcsolódó, meghatározott és egyértelmű finomítása. Adatelemzési módszer az, amely a nevek és számok használ ilyen kérdésre. Általában használhatja adatelemzési és machine learning-öt típusú kérdések megválaszolásához:
 
   * Mekkora vagy hány? (regresszió)
   * Mely kategóriában jelentkezik? (osztályozás)
   * Melyik csoporthoz? (fürtökkel)
   * Az Ez furcsának? (az anomáliadetektálás)
   * Melyik lehetőség kell tenni? (javaslat)

   Határozza meg, amely ezeket a kérdéseket Ön kéri, és hogyan fogadó hatékonyabb üzleti céljaihoz.

3. Adja meg a projektcsapat és a feladataikat a tagok megadásával. Fejlesztés, ismételt futtatásával további információk felderítésére, magas szintű mérföldkő csomagot. 

4. Adja meg a sikerkritériumok metrikái. Például érdemes a vásárlók lemorzsolódásának prognosztizálása eléréséhez. A három hónapos projekt végén pontossága "x" %-os van szüksége. Ezeket az adatokat, és elérhetővé teheti churn ügyfél promóciókkal csökkentése érdekében. A metrikáknak **intelligensnek**kell lenniük: 

   * **S**pecific 
   * **M**easurable
   * **Egy**chievable 
   * **R**-elevant 
   * **T**IME – kötve 

### <a name="identify-data-sources"></a>Adatforrások azonosítása
Azonosítsa az adatforrásokat, amelyek tartalmazzák az éles kérdésekre adott válaszok ismert példák. Keresse meg a következő adatokat:

* A kérdés vonatkozó adatokat. A cél és a cél kapcsolódó funkciók mértékek van?
* A modell cél pontos felmérését és a szolgáltatások fontos adatokat.

Előfordulhat például, hogy a meglévő rendszerek kell összegyűjtése és oldja meg a problémát, és a projekt célok eléréséhez további adattípus naplózása. Ebben a helyzetben érdemes, keresse meg a külső adatforrásokhoz, vagy frissíteni a rendszerek az új adatokat gyűjteni.

## <a name="artifacts"></a>Összetevők
Ebben a szakaszban az alábbiakban a le:

   * [Charter-dokumentum](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md): a TDSP-projekt szerkezetének definíciójában szabványos sablon szerepel. Bérleti Ez egy élő dokumentumot. Frissítse a projekt során a sablon, új felderítések győződjön meg arról, és üzleti követelmények változnak. A kulcs a újrafuttathatja esetén ez a dokumentum részletesen, hozzáadás, a felderítési folyamat során. Tartsa meg az ügyfél, és más érdekelt felek részt vesz a változtatásokat, és világosan a okok miatt a módosítások.  
   * [Adatforrások](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md#raw-data-sources): a TDSP Project **ADATJELENTÉSI** mappájában található **adatdefiníciós** jelentés **nyers adatforrások** szakasza tartalmazza az adatforrásokat. Ez a szakasz meghatározza a nyers adatok az eredeti, illetve a célhelyeket. A későbbi szakaszokra, töltse ki például a szkripteket az adatok áthelyezése az elemzési környezet további részleteket.  
   * [Adatszótárak](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Data_Dictionaries): Ez a dokumentum az ügyfél által biztosított adatmennyiség leírását tartalmazza. Ezeket a leírásokat a séma (az adattípusokat és az információkat az ellenőrzési szabályok, ha van ilyen) és az entitás-kapcsolat ábrák információkat tartalmaznak, ha elérhető.

## <a name="next-steps"></a>Következő lépések

Az alábbiakban a TDSP életciklusának minden lépése mutató hivatkozásokat:

   1. [Üzleti ismeretek](lifecycle-business-understanding.md)
   2. [Adatgyűjtés és-megértés](lifecycle-data.md)
   3. [Modellezés](lifecycle-modeling.md)
   4. [Üzembe helyezés](lifecycle-deployment.md)
   5. [Ügyfél-elfogadás](lifecycle-acceptance.md)

Teljes körű bemutatókat biztosítunk, amelyek bemutatják a folyamat összes lépését adott forgatókönyvek esetében. A [példákat](walkthroughs.md) bemutató cikk a hivatkozásokat és a miniatűr leírásait tartalmazza. A forgatókönyvek bemutatják, hogyan kombinálhatja a felhőbeli, helyszíni eszközöket és szolgáltatásokat, munkafolyamat vagy folyamat, intelligens alkalmazások létrehozására. 
