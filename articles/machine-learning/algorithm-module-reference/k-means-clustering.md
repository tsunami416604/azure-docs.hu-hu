---
title: 'Fürtszolgáltatás K-közép: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan használható a K-közép-fürtszolgáltatás modul az Azure Machine Learning szolgáltatásban fürtözési modelleket taníthat be.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 7e9b7c8f2cf86245322679198b84b50d2c5edce8
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65464668"
---
# <a name="module-k-means-clustering"></a>A modul: K-közép csoportosítás

Ez a cikk ismerteti, hogyan használható a *K-közép-Fürtszolgáltatás* modul az Azure Machine Learning Studio egy kellő K-közép-fürtözési modell létrehozásához. 
 
K-közép, a legegyszerűbb egyik és a legjobb ismert *felügyeletlen* tanulási algoritmus. Az algoritmus a machine learning-tevékenységek, például különböző használhatja: 

* [Észlelni a rendellenes adatok](https://msdn.microsoft.com/magazine/jj891054.aspx).
* Szöveges dokumentumok fürtszolgáltatás.
* Adatkészletek elemzése, mielőtt más osztályozási vagy regressziós módszert használ. 

A fürtözési modell létrehozásához meg:

* Ez a modul adja hozzá a kísérlet során.
* Csatlakozzon egy adatkészletet.
* Állítsa be a paramétereket, például a fürtök várható, a distance metrikát a fürtök létrehozásához használandó, és így tovább. 
  
Miután konfigurálta a modul hiperparaméterek, csatlakoztatja a kellő modellt a [Train-csoportosítási modellben](train-clustering-model.md). Mivel a K-közép-algoritmus egy felügyeletlen tanulási módszert, egy címke oszlopot nem kötelező. 

+ Ha az adatok egy címkét is tartalmaz, használhatja a címke értékek és a fürt kiválasztása a modell optimalizálása. 

+ Ha az adatok nem címke, az algoritmus fürtök jelölő lehetséges kategóriák, kizárólag az adatok alapján hoz létre.  

##  <a name="understand-k-means-clustering"></a>Megismerheti a K-közép-fürtözés
 
Általában fürtözéshez hasonló jellemzőkkel rendelkező fürtök csoport esetekre egy adatkészlet iteratív módszerekkel. Ezek a Csoportosítások adatok azonosítása a rendellenességeket, az adatok feltárására és végül végzett előrejelzések hasznosak. Fürtszolgáltatás modellek is segít azonosítani egy adatkészletet, amely, előfordulhat, hogy nem logikailag a Származtatás böngészési vagy egyszerű megfigyelési kapcsolatokat. Ebből kifolyólag fürtszolgáltatás gyakran használják a korai szakaszában, a gépi tanulási feladatok, feltárhatja az adatait, és nem várt összefüggéseket felderíteni.  
  
 A fürtözési modell a K-közép módszer használatával való konfigurálásakor meg kell adnia egy célszámát *k* számát jelöli, amely *centroids* szeretné a modellben. A középpont – az pont, amely minden egyes fürt. A K-közép-algoritmust rendeli hozzá, csökkenthetik a fürtön belüli négyzetes összeg minden bejövő adatok pont a fürt egyik. 
 
Amikor feldolgozza a betanítási adatok, a K-közép-algoritmus egy véletlenszerűen kiválasztott centroids kezdeti készleteinek kezdődik. Centroids a fürtök kiindulópontként szolgálnak, és alkalmazza őket a Lloyd algoritmus iteratív finomíthatja a helyüket. A K-közép-algoritmus leállítja, létrehozását és a fürtök finomítása egy vagy több feltétel teljesülésekor:  
  
-   A centroids stabilizálódhatnak, ami azt jelenti, hogy a fürt hozzárendelések egyes pontok már nem módosíthatja, és az algoritmus a megoldás egy konvergált.  
  
-   Az algoritmus befejeződött, a megadott számú ismétlések futó.  
  
 Miután végrehajtotta a betanítási fázis, használja a [adatok hozzárendelése fürtökhöz](assign-data-to-clusters.md) modul új esetek hozzárendelése egy, a K-közép-algoritmus segítségével talált-fürtjétől. Fürt hozzárendelés végez számítástechnika az új eset és minden egyes fürt az középpontját közötti távolság. A legközelebbi középpontját a fürt minden egyes új eset hozzá van rendelve.  

## <a name="configure-the-k-means-clustering-module"></a>A K-közép-fürtszolgáltatás modul konfigurálása
  
1.  Adja hozzá a **K-közép-Fürtszolgáltatás** modult a kísérletvászonra.  
  
2.  Adja meg, hogyan kell betanítani a modellt, jelölje be a **létrehozási trainer módban** lehetőséget.  
  
    -   **Egyetlen paraméter**: Ha ismeri a pontos paraméterek a fürtözési modell a használni kívánt, megadhat egy adott értékhalmazt argumentumként.  
  
3.  A **számát, Centroids**, írja be a kívánt kezdődik, az algoritmus a fürtök számának.  
  
     A modell létrehozásához pontosan fürtök száma nem garantált. Az algoritmus az adatpontok száma kezdődik, és az optimális konfigurációt található végiglépkedve.  
  
4.  A Tulajdonságok **inicializálási** meghatározza az algoritmus, amely a kezdeti fürtkonfigurációtól meghatározására szolgál.  
  
    -   **Első N**: Bizonyos adatpontok kezdeti száma az adatkészletből választott és kezdeti eszközét. 
    
         Ez a módszer néven is ismert a *Forgy metódus*.  
  
    -   **Véletlenszerű**: Az algoritmus véletlenszerűen elhelyezi egy fürt egy adatpont és majd kiszámítja a kezdeti középérték véletlenszerűen kell a fürt a középpontját hozzárendelt pontokat. 

         Ez a módszer néven is ismert a *véletlenszerű partíció* metódust.  
  
    -   **K-Means++**: Ez az alapértelmezett módszere fürtök inicializálása.  
  
         A **K-azt jelenti, hogy ++** algoritmus 2007-ben, David Arthur Sergei Vassilvitskii, így elkerülheti a gyenge, fürtözés, a standard szintű K-közép-algoritmus által javasolt. **K – azt jelenti, hogy ++** standard K-közép javítja a fürtcsomópontok kezdeti központok kiválasztására vonatkozó egy másik módszer segítségével.  
  
    
5.  A **véletlenszerű szám kezdőérték**, igény szerint adjon meg egy értéket a fürt inicializálása a kezdőérték használja. Ez az érték egy jelentős hatással lehet a fürt kiválasztása.  
  
6.  A **metrika**, válassza ki a fürt vektorok, vagy új adatpontok és a véletlenszerűen kiválasztott középpontját között távolság mérésére szolgáló használni kívánt függvény. Az Azure Machine Learning a következő távolságskála fürtmetrikák támogatja:  
  
    -   **Euclidean**: Euclidean távolság általánosan használt fürt pontdiagram mértékegysége a K-közép-fürtszolgáltatáshoz. Ez a metrika használata ajánlott, mert minimalizálja mean pontok és a centroids közötti távolság.
  
7.  A **ismétléseinek**, írja be, hogy hányszor előtt, a választott centroids finalizes megismételheti a betanítási adatok az algoritmus kell.  
  
     Beállíthatja, hogy ez a paraméter egyenleg pontossága képzési idő függvényében.  
  
8.  A **hozzárendelése címke mód**, válasszon egy lehetőséget, amely meghatározza a felirat oszlop, ha a az adatkészlet kezelésének módját.  
  
     Mivel a K-közép-fürtszolgáltatás egy felügyeletlen machine learning-metódus, a címkék nem kötelező. Azonban ha az adatkészlet már rendelkezik egy olyan címke oszlop, használhatja ezeket az értékeket a fürtök kiválasztását, vagy megadhatja, hogy az értékek figyelmen kívül lesz hagyva.  
  
    -   **Címke oszlop figyelmen kívül**: A címke oszlopban szereplő értékek figyelmen kívül hagyja, és nem szerepelnek a modell létrehozásához.
  
    -   **Adja meg a hiányzó értékeket**: A címke oszlopértékek segítségével lehetőségként a fürtöket hozhat létre. Azokat a sorokat hiányzik egy címkét, ha az érték van imputált egyéb funkciók használatával.  
  
    -   **Írja felül a legközelebbi center**: A címke oszlopértékek cserélése címke előre jelzett értékek, a pont, a jelenlegi középpontját legközelebb eső címke használata.  

8.  Válassza ki a **funkciók normalizálása** beállítást, ha azt szeretné, mielőtt képzési szolgáltatásai optimalizálására.
  
     Ha normalizálási, mielőtt képzés, alkalmazza az adatpontok normalizálva vannak `[0,1]` MinMaxNormalizer szerint.

10. A modell betanításához.  
  
    -   Ha **létrehozási trainer módban** való **egyetlen paramétert**adja hozzá a címkézett adatkészlet és a modell betanításához használja a [fürtözési modell betanításához](train-clustering-model.md) modul.  
  
### <a name="results"></a>Results (Eredmények)

Végzett konfigurálása és a modell tanítása, után egy olyan modell, pontszámokat létrehozásához használhatja. Vannak azonban több lehetőség is a modell betanítását, és több lehetőség is megtekinthetik és használhatják az eredmények: 

#### <a name="capture-a-snapshot-of-the-model-in-your-workspace"></a>A modell a munkaterületen pillanatkép rögzítése

Ha használta a [Train-csoportosítási modellben](train-clustering-model.md) modul:

1. Kattintson a jobb gombbal a **Train-csoportosítási modellben** modul.

2. Válassza ki **Trained model**, majd válassza ki **Mentés másként betanított modell**.

A mentett modell a betanítási adatok a modell a mentésekor jelöli. Ha később frissíti a betanítási adatok, a kísérletben használt, azt nem frissíti a mentett modell. 

#### <a name="see-the-clustering-result-dataset"></a>Tekintse meg a fürtözési eredmény adatkészlet 

Ha használta a [Train-csoportosítási modellben](train-clustering-model.md) modul:

1. Kattintson a jobb gombbal a **Train-csoportosítási modellben** modul.

2. Válassza ki **eredmények adatkészlet**, majd válassza ki **Visualize**.

### <a name="tips-for-generating-the-best-clustering-model"></a>Tippek a legjobb fürtözési modell létrehozása  

Ismert, hogy a *beültetés* csoportosítás során használt jelentős hatással lehet a modellt. Az esetleges centroids pontok kezdeti elhelyezését beültetés jelenti.
 
Például ha az adatkészlet tartalmaz számos kiugró adatokat, és a egy rendkívüli akkor kell kiválasztani, hogy a fürtök, egyéb adatpont nem fér el a fürt, és a fürt egypéldányos lehet. Előfordulhat, hogy azt csak egy pont.  
  
A probléma több módon is elkerülése érdekében:  
  
-   Módosíthatja a centroids számát, és próbálkozzon több seed értéket.  
  
-   Hozzon létre több modell, a metrika változó vagy léptetés több.  
  
Általánosságban véve a fürtözési modell, lehetőség, hogy minden megadott konfigurációt eredményez fürtök helyileg optimalizált készletét. Más szóval a fürtök a modell által visszaadott csak az aktuális adatpontok megfelelő és nem általánosítható más adatokhoz. Ha egy másik kezdeti konfigurációt használ, a K-közép metódus egy másik, például kiváló konfigurációban előfordulhat, hogy találja. 
