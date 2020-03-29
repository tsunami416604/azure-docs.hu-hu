---
title: 'K-eszközök fürtözés: modul hivatkozás'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a K-Means fürtözési modult az Azure Machine Learningben a fürtözési modellek betanításához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: 9606768288cc74afc24491149eb471944f45e2dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921164"
---
# <a name="module-k-means-clustering"></a>Modul: K-means klaszterek

Ez a cikk ismerteti, hogyan használhatja a *K-Means fürtözési* modul az Azure Machine Learning designer (előzetes verzió) egy képzetlen K-eszközök fürtözési modell létrehozásához. 
 
K-eszközök az egyik legegyszerűbb és a legismertebb *felügyelet nélküli* tanulási algoritmusok. Az algoritmus taszthat különböző gépi tanulási feladatokhoz, például a következőkre: 

* [Rendellenes adatok észlelése](https://msdn.microsoft.com/magazine/jj891054.aspx).
* Szöveges dokumentumok fürtözése.
* Az adatkészletek elemzése más besorolási vagy regressziós módszerek használata előtt. 

Fürthálózati modell létrehozásához:

* Adja hozzá ezt a modult a folyamathoz.
* Adatkészlet csatlakoztatása.
* Állítsa be a paramétereket, például a várható fürtök számát, a fürtök létrehozásához használandó távolságmérőszámot stb. 
  
Miután konfigurálta a modul hiperparamétereit, csatlakoztatja a képzetlen modellt a [vonatfürtözési modellhez.](train-clustering-model.md) Mivel a K-means algoritmus felügyelet nélküli tanulási módszer, a címkeoszlop nem kötelező. 

+ Ha az adatok tartalmaznak egy címkét, a címke értékek segítségével irányíthatja a fürtök kiválasztását, és optimalizálhatja a modellt. 

+ Ha az adatok nem rendelkeznek címkével, az algoritmus olyan fürtöket hoz létre, amelyek a lehetséges kategóriákat képviselik, kizárólag az adatok alapján.  

##  <a name="understand-k-means-clustering"></a>A K-eszközök fürtözésének megértése
 
A fürtözés általában iteratív technikákat használ az adatkészletben lévő esetek hasonló jellemzőkkel rendelkező fürtökbe történő csoportosításához. Ezek a csoportosítások hasznosak az adatok feltárásához, az adatok anomáliáinak azonosításához és végül az előrejelzések készítéséhez. A fürtözési modellek segítségével azonosíthatja az adatkészletben lévő olyan kapcsolatokat is, amelyeket előfordulhat, hogy nem logikailag levezethet a böngészéssel vagy az egyszerű megfigyeléssel. Ezen okok miatt a fürtözést gyakran használják a gépi tanulási feladatok korai fázisaiban az adatok feltárására és a váratlan korrelációk felfedezésére.  
  
 Ha egy fürtözési modellt a K-means metódussal konfigurál, meg kell adnia egy *k* célszámot, amely jelzi a modellben használni kívánt *centroidok* számát. A centroid egy olyan pont, amely minden egyes halmazt reprezentatot képvisel. A K-eszközök algoritmus hozzárendeli az egyes bejövő adatpont az egyik a klaszterek minimalizálásával a fürtön belüli összeg négyzetek. 
 
Amikor feldolgozza a betanítási adatokat, a K-means algoritmus véletlenszerűen kiválasztott centroidok kezdeti készletével kezdődik. A centroidok a fürtök kiindulópontjaként szolgálnak, és Lloyd algoritmusát alkalmazzák a helyük iteratív finomítására. A K-eszközök algoritmus leállítja a fürtök építését és finomítását, ha azok megfelelnek az alábbi feltételek közül egynek:  
  
-   A centroidok stabilizálódnak, ami azt jelenti, hogy az egyes pontok fürthozzárendelései már nem változnak, és az algoritmus egy megoldásra konvergált.  
  
-   Az algoritmus befejezte a megadott számú ismétlés futtatását.  
  
 Miután befejezte a betanítási fázist, az [Adatok hozzárendelése a fürtökhöz](assign-data-to-clusters.md) modul segítségével új eseteket rendelhet hozzá a K-means algoritmus sal talált fürtök egyikéhez. A fürthozzárendelést az új eset és az egyes fürtök centroidja közötti távolság kiszámításával hajthatja végre. Minden új eset a legközelebbi centroidtal rendelkező fürthöz van rendelve.  

## <a name="configure-the-k-means-clustering-module"></a>A K-Means fürtöző modul konfigurálása
  
1.  Adja hozzá a **K-Means fürtözési** modult a folyamathoz.  
  
2.  Ha meg szeretné adni, hogyan szeretné betanítani a modellt, válassza az **Oktatói mód létrehozása** lehetőséget.  
  
    -   **Egyetlen paraméter:** Ha ismeri a fürtözési modellben használni kívánt pontos paramétereket, argumentumként megadhat egy adott értékkészletet.  
  
3.  A **Centroidok száma**mezőbe írja be azalgoritmus kezdetét.  
  
     A modell nem garantált, hogy pontosan ennyi fürtöt hoz létre. Az algoritmus ezzel az adatponttal kezdődik, és az optimális konfiguráció megtalálásához iterálja.  
  
4.  A tulajdonságok **inicializálása** a kezdeti fürtkonfiguráció definiálására használt algoritmus megadására szolgál.  
  
    -   **Első N**: Néhány kezdeti adatpont az adatkészletből választódik ki, és kezdeti eszközként szolgál. 
    
         Ezt a módszert *Forgy módszernek is nevezik.*  
  
    -   **Véletlenszerű**: Az algoritmus véletlenszerűen elhelyez egy adatpontot egy fürtben, majd kiszámítja a kezdeti átlagot a fürt véletlenszerűen hozzárendelt pontjainak centroidjának. 

         Ezt a módszert *véletlenszerű partíciós* módszernek is nevezik.  
  
    -   **K-Means++**: Ez a fürtök inicializálásának alapértelmezett módszere.  
  
         A **K-means++** algoritmust David Arthur és Szergej Vassilvitskii javasolta 2007-ben, hogy elkerüljék a rossz klaszterek a szabványos K-eszközök algoritmus. **A K-means++** a szabványos K-eszközökhöz egy másik módszerrel javítja a kezdeti fürtközpontok kiválasztását.  
  
    
5.  A **véletlenszám-vetőmag**esetében tetszés szerint írjon be egy értéket, amelyet a fürt inicializálásának vetőmagjaként szeretne használni. Ez az érték jelentős hatással lehet a fürt kiválasztására.  
  
6.  A **Metric**területen válassza ki a fürtvektorok közötti távolság méréséhez, vagy az új adatpontok és a véletlenszerűen kiválasztott centroid közötti távolság méréséhez használt függvényt. Az Azure Machine Learning a következő fürttávolság-metrikákat támogatja:  
  
    -   **Euklideszi**: Az euklideszi távolság gyakran használják, mint egy intézkedés klaszter scatter a K-eszközök klaszterek. Ez a mérőszám azért előnyös, mert minimalizálja a pontok és a centroidok közötti átlagos távolságot.
  
7.  **Iterációk**esetén írja be, hogy az algoritmus hányszor kell a betanítási adatokat, mielőtt véglegesíti a centroidok kiválasztását.  
  
     Ezt a paramétert úgy módosíthatja, hogy egyensúlyba hozza a pontosságot az edzési idővel.  
  
8.  A **Címke hozzárendelése módban**válasszon egy beállítást, amely megadja, hogy az adatkészletben található címkeoszlophogyan legyen kezelve.  
  
     Mivel a K-means fürtözés egy felügyelet nélküli gépi tanulási módszer, a címkék nem kötelező. Ha azonban az adatkészlet már rendelkezik címkeoszloppal, ezekkel az értékekkel irányíthatja a fürtök kiválasztását, vagy megadhatja, hogy az értékeket figyelmen kívül hagyja.  
  
    -   **Címkeoszlop figyelmen kívül hagyása:** A címkeoszlop értékeit a program figyelmen kívül hagyja, és nem használja a modell létrehozásához.
  
    -   **Hiányzó értékek kitöltése**: A címkeoszlop értékei a fürtök létrehozásának elősegítésére szolgálnak. Ha valamelyik sorból hiányzik egy címke, az értéket más szolgáltatások kal imputálja a függvény.  
  
    -   **Felülírás a legközelebbitől a közepéig**: A feliratoszlop értékeit a program az aktuális centroidhoz legközelebb eső pont címkéjének használatával lecseréli a várt feliratértékekre.  

8.  Válassza a **Jellemzők normalizálása** lehetőséget, ha a betanítás előtt normalizálni szeretné a funkciókat.
  
     Ha normalizálást alkalmaz, a betanítás előtt `[0,1]` az adatpontokat a MinMaxNormalizer normalizálja.

10. A modell betanítása.  
  
    -   Ha az **Oktató létrehozása mód létrehozása** **beállítását egy paraméterre állítja**be, adjon hozzá egy címkézett adatkészletet, és tanítsa be a modellt a [Fürtözési modell betanítása](train-clustering-model.md) modul használatával.  
  
## <a name="results"></a>Results (Eredmények)

Miután befejezte a modell konfigurálását és betanítását, rendelkezik egy modellel, amely segítségével pontszámokat hozhat létre. A modell betanításának azonban többféle módja van, és többféle módon is megtekintheti és használhatja az eredményeket: 

### <a name="capture-a-snapshot-of-the-model-in-your-workspace"></a>Pillanatkép készítése a modellről a munkaterületen

Ha a [Train Clustering Model modult](train-clustering-model.md) használta:

1. Jelölje ki a **Vonatfürttípus modult,** és nyissa meg a jobb oldali panelt.

2. Válassza **a Kimenetek** **Register dataset** lapot.

A mentett modell a betanítási adatokat jelöli a modell mentésekor. Ha később frissíti a folyamatban használt betanítási adatokat, nem frissíti a mentett modellt. 

### <a name="see-the-clustering-result-dataset"></a>A fürtözés eredményadatkészletének megtekintése 

Ha a [Train Clustering Model modult](train-clustering-model.md) használta:

1. Kattintson a jobb gombbal a **Train Clustering Model** modulra.

2. Válassza a **Megjelenítés lehetőséget.**

### <a name="tips-for-generating-the-best-clustering-model"></a>Tippek a legjobb fürtözési modell létrehozásához  

Ismeretes, hogy a fürtözés során használt *vetési* folyamat jelentősen befolyásolhatja a modellt. A vetés a pontok kezdeti elhelyezését jelenti a potenciális centroidokba.
 
Ha például az adatkészlet sok kiugró értékkel rendelkezik, és a fürtvetéshez egy kiugró érték kerül kiválasztásra, más adatpont nem illeszkedne jól a fürthöz, és a fürt lehet egyton. Azaz, lehet, hogy csak egy pont.  
  
Ezt a problémát többféleképpen is elkerülheti:  
  
-   Változtassa meg a centroidok számát, és próbáljon ki több magértéket.  
  
-   Hozzon létre több modellt, a metrikát változóan, vagy több iterációt.  
  
Általában fürtözési modellek, lehetséges, hogy egy adott konfiguráció eredményez helyileg optimalizált fürtkészletét. Más szóval a modell által visszaadott fürtök készlete csak az aktuális adatpontoknak felel meg, és nem általánosítható más adatokkal. Ha egy másik kezdeti konfigurációt használ, a K-means metódus előfordulhat, hogy egy másik, jobb, konfiguráció. 

## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 
