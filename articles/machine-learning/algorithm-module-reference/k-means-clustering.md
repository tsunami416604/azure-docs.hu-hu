---
title: 'Fürtszolgáltatás K-közép: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan használható a K-közép-fürtözési modul az Azure Machine Learning szolgáltatás fürtözési modelleket taníthat be.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 29aeb460581655190b7c3f4256647059f4642d3e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029700"
---
# <a name="k-means-clustering"></a>K-közép-fürtözés

Ez a cikk ismerteti, hogyan használható a **K-közép-Fürtszolgáltatás** modul az Azure Machine Learning Studio egy kellő K-közép-fürtözési modell létrehozásához. 
 
K-közép, a legegyszerűbb egyik és a legjobb ismert *felügyeletlen* tanulási algoritmusok és is használható különböző gépi tanulási feladatok, például [észlelni a rendellenes adatok](https://msdn.microsoft.com/magazine/jj891054.aspx), fürtözés, a szöveg dokumentumok, és a egy adatkészletet, mielőtt más osztályozási vagy regressziós módszerrel elemzése. Egy fürtözési modell létrehozásához, ez a modul hozzáadása a kísérlethez, csatlakozzon egy adatkészletet, és set paraméterek, például a fürtök várható, a distance metrikát a fürtök létrehozásához használandó, és így tovább. 
  
Miután konfigurálta a modul hiperparaméterek, csatlakozzon a kellő modellt a [Train-csoportosítási modellben](train-clustering-model.md) nem kötelező, mivel a K-közép-algoritmus egy felügyeletlen tanulási módszert, egy címke oszlopot. 

+ Ha az adatok egy címkét is tartalmaz, használhatja a címke értékek és a fürt kiválasztása a modell optimalizálása. 

+ Ha az adatok nem címke, az algoritmus fürtök jelölő lehetséges kategóriák, kizárólag az adatok alapján hoz létre.  
  

  
##  <a name="understanding-k-means-clustering"></a>Understanding k-közép fürtszolgáltatás
 
Általában fürtözéshez hasonló jellemzőket tartalmazó fürtök csoport esetekre egy adatkészlet iteratív módszerekkel. Ezek a Csoportosítások adatok azonosítása a rendellenességeket, az adatok feltárására és végül végzett előrejelzések hasznosak. Fürtszolgáltatás modellek is segít azonosítani egy adatkészletet, amely, előfordulhat, hogy nem logikailag a Származtatás böngészési vagy egyszerű megfigyelési kapcsolatokat. Ebből kifolyólag fürtszolgáltatás gyakran használják a korai szakaszában, a gépi tanulási feladatok, feltárhatja az adatait, és nem várt összefüggéseket felderíteni.  
  
 A k-közép metódussal csoportosító modell konfigurálásakor meg kell adnia egy célszámát *k* számát jelző *centroids* szeretné a modellben. A középpont – az pont, amely minden egyes fürt. A K-közép-algoritmust rendeli hozzá, csökkenthetik a fürtön belüli négyzetes összeg minden bejövő adatok pont a fürt egyik. 
 
A betanítási adatok feldolgozásakor a K-közép-algoritmus egy kezdeti készleteinek centroids, amely mindegyik fürthöz kiindulópontként szolgálnak, és alkalmazza a Lloyd algoritmus iteratív finomíthatja a centroids helyeinek kiválasztott randomnly kezdődik. A K-közép-algoritmus leállítja, létrehozását és a fürtök finomítása egy vagy több feltétel teljesülésekor:  
  
-   A centroids stabilizálódhatnak, ami azt jelenti, hogy a fürt-hozzárendelését az egyes pontok már nem módosíthatja, és az algoritmus a megoldás egy konvergált.  
  
-   Az algoritmus befejeződött, a megadott számú ismétlések futó.  
  
 A képzési fázis végrehajtása után az a [adatok hozzárendelése fürtökhöz](assign-data-to-clusters.md) modul új esetek hozzárendelése a fürtök a k-közép-algoritmus által megtalált egyikét. Az új eset és minden egyes fürt az középpontját közötti távolságot számítási fürt hozzárendelés hajt végre. A legközelebbi középpontját a fürt minden egyes új eset hozzá van rendelve.  

## <a name="how-to-configure-k-means-clustering"></a>K-közép-fürtözés konfigurálása
  
1.  Adja hozzá a **K-közép-Fürtszolgáltatás** modult a kísérletvászonra.  
  
2.  Adja meg, hogyan a modellt úgy vélik a **létrehozási trainer módban** lehetőséget.  
  
    -   **Egyetlen paraméter**: Ha ismeri a pontos paraméterek a fürtözési modell a használni kívánt, megadhat egy adott értékhalmazt argumentumként.  
  
   
  
3.  A **számát, Centroids**, írja be a kívánt kezdődik, az algoritmus a fürtök számának.  
  
     A modell nem garantált, hogy pontosan a fürtök számának előállításához. A algorithn adatpontok száma kezdődik, és az optimális konfigurációt található végiglépkedve.  
  
     
  
4.  A Tulajdonságok **inicializálási** meghatározza az algoritmus, amely a kezdeti fürtkonfigurációtól meghatározására szolgál.  
  
    -   **Első N**: Bizonyos adatpontok kezdeti száma az adatkészletből származó kiválasztott és kezdeti eszközét.  
  
         Más néven a *Forgy metódus*.  
  
    -   **Véletlenszerű**: Az algoritmus véletlenszerűen elhelyezi egy fürt egy adatpont és majd kiszámítja a kezdeti középérték véletlenszerűen kell a fürt a középpontját hozzárendelt pontokat.  
  
         Más néven a *véletlenszerű partíció* metódust.  
  
    -   **K-Means++**: Ez az alapértelmezett módszere fürtök inicializálása.  
  
         A **K-közép ++** algoritmus 2007-ben, David Arthur által javasolt és a standard gyenge fürtözését elkerülése érdekében Sergei Vassilvitskii k-közép algoritmus. **K-közép ++** standard K-közép javítja a fürtcsomópontok kezdeti központok kiválasztására vonatkozó egy másik módszer segítségével.  
  
    
5.  A **véletlenszerű szám kezdőérték**, igény szerint adjon meg egy értéket a fürt inicializálása a kezdőérték használja. Ez az érték egy jelentős hatással lehet a fürt kiválasztása.  
  
    
  
6.  A **metrika**, válassza ki a fürt vektorok, vagy új adatpontok és a véletlenszerűen kiválasztott középpontját között távolság mérésére szolgáló használni kívánt függvény. Az Azure Machine Learning a következő távolságskála fürtmetrikák támogatja:  
  
    -   **Euclidean**: Euclidean távolság általánosan használt fürt pontdiagram mértékegysége a K-közép-fürtszolgáltatáshoz. Ez a metrika használata ajánlott, mert minimalizálja mean pontok és a centroids közötti távolság.
  

  
7.  A **ismétléseinek**, írja be, hogy hányszor az algoritmus a választott centroids véglegesítése előtt kell megismételheti a betanítási adatok.  
  
     Ez a paraméter egyenleg pontossága képzési időalapú módosíthatja.  
  
8.  A **hozzárendelése címke mód**, válasszon egy lehetőséget, amely meghatározza a felirat oszlop, ha az adatkészletben található kezelésének módját.  
  
     Mivel a K-közép-fürtszolgáltatás egy felügyeletlen machine learning-metódus, a címkék nem kötelező. Azonban ha az adatkészlet már rendelkezik egy olyan címke oszlop, használhatja ezeket az értékeket is a fürtök kiválasztását, vagy megadhatja, hogy az értékek figyelmen kívül lesz hagyva.  
  
    -   **Címke oszlop figyelmen kívül**: A címke oszlopban szereplő értékek figyelmen kívül hagyja, és nem szerepelnek a modell létrehozásához.
  
    -   **Adja meg a hiányzó értékeket**: A címke oszlopértékek segítségével lehetőségként a fürtöket hozhat létre. Azokat a sorokat hiányzik egy címkét, ha az érték van imputált egyéb funkciók használatával.  
  
    -   **Írja felül a legközelebbi center**: A címke oszlopértékek cserélése címke előre jelzett értékek, a pont, a jelenlegi középpontját legközelebb eső címke használata.  

8.  A beállítást, **funkciók normalizálása**, ha azt szeretné, mielőtt képzési szolgáltatásai optimalizálására.
  
     Ha normalizálási, mielőtt képzés, alkalmazza az adatpontok normalizálva vannak `[0,1]` MinMaxNormalizer szerint.

10. A modell betanításához.  
  
    -   Ha **létrehozási trainer módban** való **egyetlen paramétert**adja hozzá a címkézett adatkészlet és a modell betanításához használja a [fürtözési modell betanításához](train-clustering-model.md) modul.  
  

### <a name="results"></a>Results (Eredmények)

Befejezte a modell tanítása és konfigurálása, után egy olyan modell, pontszámokat létrehozásához használhatja. Vannak azonban több lehetőség is a modell betanítását, és több lehetőség is megtekinthetik és használhatják az eredmények: 

#### <a name="capture-a-snapshot-of-the-model-in-your-workspace"></a>A modell a munkaterületen pillanatkép rögzítése

+ Ha használta a [Train-csoportosítási modellben](train-clustering-model.md) modul
    1. Kattintson a jobb gombbal a [Train-csoportosítási modellben](train-clustering-model.md) modul.
    2. Válassza ki **Trained model** majd **Mentés másként betanított modell**.


A mentett modell a betanítási adatok képviseli a modell a mentésekor. Ha később frissíti a betanítási adatok, a kísérletben használt, nem frissíti a mentett modell. 



#### <a name="see-the-clustering-result-dataset"></a>Tekintse meg a fürtözési eredmény adatkészlet 

+ Ha használta a [Train-csoportosítási modellben](train-clustering-model.md) modul
    1. Kattintson a jobb gombbal a [Train-csoportosítási modellben](train-clustering-model.md) modul.
    2. Válassza ki **eredmények adatkészlet** majd **Visualize**.


### <a name="tips-for-generating-the-best-clustering-model"></a>Tippek a legjobb fürtözési modell létrehozása  

Ismert, hogy a **beültetés** csoportosítás során használt folyamat jelentős hatással lehet a modell. A kezdeti elhelyezését pontok potental centroids be beültetés jelenti.
 
Például az adatkészlet tartalmaz számos kiugró adatokat, és a egy rendkívüli akkor kell kiválasztani, hogy a fürtök, ha nincs egyéb adatpont nem fér el a fürt és a fürt lehet egypéldányos: azt jelenti, egy fürtbe, csak egyetlen pont.  
  
A probléma elkerülése érdekében különböző módja van:  
  
-   Módosíthatja a centroids számát, és próbálkozzon több seed értéket.  
  
-   Hozzon létre több modell, a metrika változó vagy léptetés több.  
  
  
Általánosságban véve a fürtözési modell, lehetőség, hogy minden megadott konfigurációt eredményez fürtök helyileg optimalizált készletét. Más szóval a set-fürtök a modell által visszaadott csak az aktuális adatpontok leginkább megfelelő, és nem általánosítható más adatokhoz. Ha egy másik kezdeti konfigurációt használta, a K-közép metódus egy másik, például kiváló konfigurációban előfordulhat, hogy találja. 
