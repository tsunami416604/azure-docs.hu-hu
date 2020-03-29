---
title: Üzleti megértés a csapatadat-elemzési folyamatában
description: A célok, feladatok és a szállítások az üzleti ismeretek szakaszában az adat-tudományos projektek a csapat adatelemzési folyamat.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76710332"
---
# <a name="the-business-understanding-stage-of-the-team-data-science-process-lifecycle"></a>A csapatadat-elemzési folyamat életciklusának üzleti ismeretekkel kapcsolatos szakasza

Ez a cikk ismerteti a célokat, feladatokat és a csapatadat-elemzési folyamat (TDSP) üzleti ismeretekhez kapcsolódó szakaszához kapcsolódó célokat, feladatokat és eredményeket. Ez a folyamat egy ajánlott életciklust biztosít, amely az adatelemzési projektek strukturálásához használható. Az életciklus felvázolja azokat a főbb szakaszokat, amelyeket a projektek általában végrehajtanak, gyakran ismétlődően:

   1. **Üzleti ismertetés**
   2. **Adatgyűjtés és adatértelmezés**
   3. **Modellezés**
   4. **Környezet**
   5. **Felhasználói elfogadás**

Itt van egy vizuális ábrázolása a TDSP életciklus: 

![TDSP életciklus](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Célok
* Adja meg a modellcélként szolgáló legfontosabb változókat, amelyek kapcsolódó metrikákat használnak, határozzák meg a projekt sikerét.
* Azonosítsa azokat a releváns adatforrásokat, amelyekhez a vállalkozás nak hozzáférése van, vagy amelyekhez hozzá kell jutnia.

## <a name="how-to-do-it"></a>Hogyan kell csinálni
Ebben a szakaszban két fő feladatfoglalkozik: 

   * **Célok meghatározása:** Az üzleti problémák megértése és azonosítása érdekében működjön együtt az ügyféllel és más érdekelt felekkel. Fogalmazzon meg olyan kérdéseket, amelyek meghatározzák az adatelemzési technikák által megcélozható üzleti célokat.
   * **Adatforrások azonosítása**: Keresse meg azokat a releváns adatokat, amelyek segítenek megválaszolni a projekt célkitűzéseit meghatározó kérdéseket.

### <a name="define-objectives"></a>Célkitűzések meghatározása
1. Ennek a lépésnek a központi célja az elemzés által előre jelzett kulcsfontosságú üzleti változók azonosítása. Ezeket a változókat *modellcélokként*hivatkozzuk, és a projekt sikerének meghatározásához a hozzájuk társított mutatókat használjuk. Két példa ilyen célok értékesítési előrejelzések vagy annak valószínűsége, hogy egy rendelés csalárd.

2. A projekt céljait releváns, konkrét és egyértelmű "éles" kérdések feltevésével és finomításával határozhatja meg. Az adattudomány olyan folyamat, amely neveket és számokat használ az ilyen kérdések megválaszolására. Általában adatelemzési vagy gépi tanulást használ öt féle kérdés megválaszolására:
 
   * Mennyit vagy mennyi? (regresszió)
   * Melyik kategóriában? (osztályozás)
   * Melyik csoport? (fürtözés)
   * Nem furcsa ez? (anomáliadetektálás)
   * Melyik lehetőséget kell figyelembe venni? (ajánlás)

   Határozza meg, hogy a következő kérdések közül melyiket teszi fel, és hogyan éri el az üzleti célokat.

3. A projektcsapat definiálása a tagok szerepköreinek és felelősségi körének megadásával. Dolgozzon ki egy magas szintű mérföldkő tervet, amelyet további információk felfedezése után is megkell tasznia. 

4. Határozza meg a sikermutatókat. Például előfordulhat, hogy szeretne elérni egy ügyfél lemorzsolódás előrejelzést. A három hónapos projekt végére "x" százalékos pontossági arányra van szükség. Ezekkel az adatokkal, akkor az ügyfelek promóciók csökkentése lemorzsolódás. A mérőszámoknak **SMART-nak**kell lenniük: 

   * **S**pecific 
   * **M**m mbiztosítable
   * **Egy**chievable 
   * **R**elevant 
   * **T**ime-kötött 

### <a name="identify-data-sources"></a>Adatforrások azonosítása
Azonosítsa azokat az adatforrásokat, amelyek ismert példákat tartalmaznak az éles kérdésekre adott válaszokra. Keresse meg a következő adatokat:

* A kérdésszempontjából releváns adatok. Vannak-e olyan mérőszámai a célnak és a célhoz kapcsolódó funkcióknak?
* Olyan adatok, amelyek pontosan mérik a modell célját és az érdeklődési funkciókat.

Előfordulhat például, hogy a meglévő rendszereknek további típusú adatokat kell gyűjteniük és naplózniuk a probléma megoldásához és a projekt célok eléréséhez. Ebben az esetben érdemes lehet külső adatforrásokat keresni, vagy frissíteni a rendszereket az új adatok gyűjtéséhez.

## <a name="artifacts"></a>Összetevők
Itt vannak a deliverables ebben a szakaszban:

   * [Charter dokumentum](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md): A TDSP projektszerkezet-definíciója szabványos sablont tartalmaz. A charta dokumentum egy élő dokumentum. A sablont az egész projekt során új felderítések és az üzleti követelmények változása során frissítheti. A legfontosabb az, hogy iterálni a dokumentumot, további részleteket, ahogy halad a felderítési folyamat. Az ügyfél és más érdekelt felek bevonása a változtatásokba, és egyértelműen közöljük velük a változások okait.  
   * [Adatforrások](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md#raw-data-sources): A TDSP **projektadat-jelentés** mappájában található **Adatdefiníciók** jelentés **Nyers adatforrások** szakasza tartalmazza az adatforrásokat. Ez a szakasz a nyers adatok eredeti és célhelyeit határozza meg. A későbbi szakaszokban további részleteket, például a parancsfájlokat is kitöltheti az adatok analitikus környezetbe való áthelyezéséhez.  
   * [Adatszótárak](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Data_Dictionaries): Ez a dokumentum az ügyfél által megadott adatok leírását tartalmazza. Ezek a leírások információkat tartalmaznak a sémáról (az adattípusokról és az érvényesítési szabályokról, ha vannak ilyenek) és az entitáskapcsolati diagramokról, ha rendelkezésre állnak.

## <a name="next-steps"></a>További lépések

A TDSP életciklusának egyes lépéseire mutató hivatkozások:

   1. [Üzleti ismertetés](lifecycle-business-understanding.md)
   2. [Adatgyűjtés és adatértelmezés](lifecycle-data.md)
   3. [Modellezés](lifecycle-modeling.md)
   4. [Környezet](lifecycle-deployment.md)
   5. [Felhasználói elfogadás](lifecycle-acceptance.md)

Teljes forgatókönyveket biztosítunk, amelyek bemutatják a folyamat összes lépését az adott forgatókönyvekhez. A [Példa forgatókönyvek](walkthroughs.md) cikk hivatkozásokat és miniatűr leírásokat tartalmazó forgatókönyvek listáját tartalmazza. A forgatókönyvek bemutatják, hogyan kombinálhatja a felhőt, a helyszíni eszközöket és szolgáltatásokat egy munkafolyamatban vagy folyamatban egy intelligens alkalmazás létrehozásához. 
