---
title: "Párhuzamossági és munkaterhelés-kezelés az SQL Data Warehouse |} Microsoft Docs"
description: "Ismerje meg a feldolgozási és munkaterhelés-kezelés az Azure SQL Data Warehouse adattárházzal történő, megoldások."
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: ef170f39-ae24-4b04-af76-53bb4c4d16d3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 08/23/2017
ms.author: joeyong;barbkess;kavithaj
ms.openlocfilehash: eaf2d43286dbaa52ada1430fbb7ce1e37f41c0d4
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="concurrency-and-workload-management-in-sql-data-warehouse"></a>Párhuzamossági és munkaterhelés-kezelés az SQL Data Warehouse
Kiszámítható teljesítményt biztosítanak a Microsoft Azure SQL Data Warehouse segítséget nyújt a nagyságrendnél párhuzamossági szintek és erőforrás-hozzárendelések például memória és CPU rangsorolási ellenőrzése. Ez a cikk bemutatja a feldolgozási és munkaterhelés-kezelés, elmagyarázza, hogyan mindkét szolgáltatásokat nyújt, és hogy miként szabályozható őket az adatraktár elveit. SQL Data Warehouse munkaterhelés felügyeleti Többfelhasználós környezetek támogatására segítenek célja. Nem célja a több-bérlős munkaterhelésekhez.

## <a name="concurrency-limits"></a>Feldolgozási korlátok
Az SQL Data Warehouse lehetővé teszi, hogy legfeljebb 1024 egyidejű kapcsolatok. Az összes 1024 kapcsolat egyidejű lekérdezések küldje el. Azonban a teljesítmény optimalizálása érdekében az SQL Data Warehouse is várólista néhány lekérdezést biztosítja, hogy minden egyes lekérdezés megkapják-e a minimális memória biztosítása. A lekérdezés-végrehajtás során Queuing következik be. Üzenetsor-kezelés lekérdezések feldolgozási korlátok elérésekor, az SQL Data Warehouse növelheti teljes átviteli sebesség győződjön meg arról, hogy aktív lekérdezések kritikusan szükséges memória-erőforrások eléréséhez.  

Feldolgozási korlátok vonatkoznak két fogalom: *párhuzamos lekérdezések* és *egyidejűségi üzembe helyezési ponti*. A lekérdezés végrehajtásához azt végre kell hajtani a lekérdezés feldolgozási korlát és a párhuzamosság tárolóhely lefoglalása belül.

* Egyidejű lekérdezések a lekérdezések végrehajtása egy időben. SQL Data Warehouse legfeljebb 32 egyidejű lekérdezések a DWU nagyobb méretű használatát teszi lehetővé.
* Párhuzamossági üzembe helyezési ponti DWU alapján foglal le. Minden 100 DWU 4 párhuzamossági üzembe helyezési ponti biztosít. Például egy DW100 4 párhuzamossági üzembe helyezési ponti foglal le, és DW1000 40 foglal le. Minden egyes lekérdezés használ egy vagy több egyidejű tárhelyek, függ a [erőforrásosztály](#resource-classes) a lekérdezés. A smallrc erőforrásosztály futó lekérdezések egy feldolgozási tárolóhely felhasználását. Egy magasabb erőforrásosztály futó lekérdezések több egyidejű üzembe helyezési ponti felhasználását.

A következő táblázat ismerteti a korlátozhatja a párhuzamos lekérdezések és a feldolgozási tárhelyek a különböző DWU méretben.

### <a name="concurrency-limits"></a>Feldolgozási korlátok
| DWU | Maximális párhuzamos lekérdezések | Lefoglalt párhuzamossági tárhelyek |
|:--- |:---:|:---:|
| DW100 |4 |4 |
| DW200 |8 |8 |
| DW300 |12 |12 |
| DW400 |16 |16 |
| DW500 |20 |20 |
| DW600 |24 |24 |
| DW1000 |32 |40 |
| DW1200 |32 |48 |
| DW1500 |32 |60 |
| DW2000 |32 |80 |
| DW3000 |32 |120 |
| DW6000 |32 |240 |

E küszöbértékek valamelyike teljesül, amikor új lekérdezések helyezi várólistára, és végre érkezési sorrendben történő kiküldési alapon.  A lekérdezések befejeződése és a lekérdezések és üzembe helyezési ponti süllyed korlátokat, várólistára helyezett lekérdezések kiadásakor. 

> [!NOTE]  
> *Válassza ki* lekérdezések végrehajtása kizárólag a dinamikus felügyeleti nézetek (dinamikus felügyeleti nézetek) vagy a katalógus nézetek nem szabályozza a feldolgozási korlátok bármelyikét. A rendszer függetlenül lekérdezések végrehajtása a figyelheti.
> 
> 

## <a name="resource-classes"></a>Erőforrás-osztályok
Az erőforrásosztályok segítségével szabályozhatja a lekérdezésekhez rendelt memóriakiosztást és CPU-ciklusokat. Kétféle típusú erőforrás osztályok rendelhet egy felhasználói adatbázis-szerepkörök formájában. A két típusú erőforrás-osztályok a következők:
1. Dinamikus erőforrás-osztályok (**smallrc, mediumrc, largerc, xlargerc**) foglaljon le a változó méretű memória, attól függően, hogy az aktuális DWU. Ez azt jelenti, hogy ha egy nagyobb adattárházegységre növelheti a lekérdezések automatikusan beolvasása több memóriát. 
2. Statikus erőforrás-osztály (**staticrc10, staticrc20, staticrc30, staticrc40, staticrc50, staticrc60, staticrc70, staticrc80**) lefoglalni az aktuális DWU függetlenül azonos memóriamennyiség (feltéve, hogy a DWU maga elegendő memória). Ez azt jelenti, hogy nagyobb dwu-k, a lekérdezéseket is futtathat további erőforrás az osztályok egyidejűleg.

A felhasználók **smallrc** és **staticrc10** kap a kisebb méretű memória, és magasabb szintű párhuzamosság előnyeit. Ezzel szemben rendelt felhasználók **xlargerc** vagy **staticrc80** kap nagy mennyiségű memóriát, és ezért a lekérdezések kevesebb egyidejűleg is futtathatók.

Alapértelmezés szerint minden felhasználó tagja a kis erőforrásosztály **smallrc**. Az eljárás `sp_addrolemember` használatával növelheti a erőforrásosztály és `sp_droprolemember` csökkenti a erőforrásosztály szolgál. Például a parancs a loaduser erőforrásosztály nőni fog **largerc**:

```sql
EXEC sp_addrolemember 'largerc', 'loaduser'
```


### <a name="queries-that-do-not-honor-resource-classes"></a>Lekérdezések, amelyek nem fogadják el erőforrás-osztályok

A lekérdezések, amelyek nem tudják igénybe venni egy nagyobb memóriafoglalás néhány típusa van. A rendszer figyelmen kívül hagyja az erőforrás-osztály terhelése, és mindig futni ezeket a lekérdezéseket a kis erőforrás osztály. Ha kis erőforrásosztály mindig fut, ezeket a lekérdezéseket, ha párhuzamossági üzembe helyezési ponti nyomás alatt, és nem használja a tárolóhelyek több, mint amennyi szükséges futtathatják. Lásd: [erőforrás osztály kivételek](#query-exceptions-to-concurrency-limits) további információt.

## <a name="details-on-resource-class-assignment"></a>Az erőforrás osztály-hozzárendelés részletei


Néhány további részleteket a erőforrásosztály:

* *Az ALTER szerepkör* engedély szükséges egy olyan felhasználó erőforrásosztály módosítása.
* Bár hozzáadhat egy felhasználó egy vagy több, a magasabb szintű erőforrás-osztályok, akkor dinamikus erőforrás-osztályok élveznek statikus erőforrás osztályok. Ez azt jelenti, hogy ha egy felhasználó mindkét rendelve **mediumrc**(dinamikus) és **staticrc80**(statikus), **mediumrc** az erőforrás-osztály, amely van figyelembe véve.
 * Ha a felhasználó egynél több erőforrás osztály egy adott osztály erőforrástípusra (egynél több dinamikus erőforrásosztály vagy több statikus erőforrásosztály) van rendelve, akkor a legmagasabb erőforrásosztály figyelembe véve. Ez azt jelenti, hogy ha egy felhasználó mediumrc és a largerc van rendelve, a magasabb szintű erőforrás-osztály (largerc) van figyelembe véve. És ha egy felhasználó mindkét rendelve **staticrc20** és **statirc80**, **staticrc80** kell figyelembe venni.
* A rendszer rendszergazda felhasználó erőforrásosztály nem módosítható.

Részletes példákat lásd: [módosul felhasználói erőforrás osztály példa](#changing-user-resource-class-example).

## <a name="memory-allocation"></a>A memóriafoglalás
Vannak előnyei és hátrányai a növekvő számú egy felhasználó erőforrásosztály. Egy felhasználó egy erőforrásosztály növekszik, a lekérdezések hozzáférést ad további memória, amely azt jelentheti, lekérdezések gyorsabban hajtható végre.  Azonban a magasabb erőforrás osztályok is csökkentheti a futtatható egyidejű lekérdezések száma. Ez egy nagy mennyiségű egyetlen lekérdezést memória lefoglalásakor vagy más lekérdezések, memória-hozzárendelések egyidejű futtatását is igénylő így között kompromisszum. Ha egy felhasználó kap a lekérdezés számára magas foglalásokat, más felhasználók nem hozzáférhetnek, hogy ugyanazt a memória-lekérdezés futtatható.

A következő táblázat a DWU- és erőforrás-osztály minden egyes terjesztési kiosztott memória képezi le.

### <a name="memory-allocations-per-distribution-for-dynamic-resource-classes-mb"></a>Memória kiosztásokat eloszlása dinamikus erőforrás osztályok (MB)
| DWU | smallrc | mediumrc | largerc | xlargerc |
|:--- |:---:|:---:|:---:|:---:|
| DW100 |100 |100 |200 |400 |
| DW200 |100 |200 |400 |800 |
| DW300 |100 |200 |400 |800 |
| DW400 |100 |400 |800 |1,600 |
| DW500 |100 |400 |800 |1,600 |
| DW600 |100 |400 |800 |1,600 |
| DW1000 |100 |800 |1,600 |3,200 |
| DW1200 |100 |800 |1,600 |3,200 |
| DW1500 |100 |800 |1,600 |3,200 |
| DW2000 |100 |1,600 |3,200 |6,400 |
| DW3000 |100 |1,600 |3,200 |6,400 |
| DW6000 |100 |3,200 |6,400 |12,800 |

A következő táblázat minden egyes terjesztési DWU és statikus erőforrásosztály által kiosztott memória képezi le. Ne feledje, hogy a magasabb szintű erőforrás-osztályok a memória, a globális DWU korlátok tiszteletben csökken.

### <a name="memory-allocations-per-distribution-for-static-resource-classes-mb"></a>Memória kiosztásokat eloszlása statikus erőforrás osztályok (MB)
| DWU | staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:--- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| DW100 |100 |200 |400 |400 |400 |400 |400 |400 |
| DW200 |100 |200 |400 |800 |800 |800 |800 |800 |
| DW300 |100 |200 |400 |800 |800 |800 |800 |800 |
| DW400 |100 |200 |400 |800 |1,600 |1,600 |1,600 |1,600 |
| DW500 |100 |200 |400 |800 |1,600 |1,600 |1,600 |1,600 |
| DW600 |100 |200 |400 |800 |1,600 |1,600 |1,600 |1,600 |
| DW1000 |100 |200 |400 |800 |1,600 |3,200 |3,200 |3,200 |
| DW1200 |100 |200 |400 |800 |1,600 |3,200 |3,200 |3,200 |
| DW1500 |100 |200 |400 |800 |1,600 |3,200 |3,200 |3,200 |
| DW2000 |100 |200 |400 |800 |1,600 |3,200 |6,400 |6,400 |
| DW3000 |100 |200 |400 |800 |1,600 |3,200 |6,400 |6,400 |
| DW6000 |100 |200 |400 |800 |1,600 |3,200 |6,400 |12,800 |

Az előző táblázatban láthatja, hogy fut egy DW2000 a lekérdezés a **xlargerc** erőforrásosztály kellene 6400 MB memória belül 60 elosztott adatbázis elérésére.  Az SQL Data Warehouse nincsenek 60 terjesztéseket. Ezért a teljes memória-foglalás a megadott erőforrásosztály lekérdezés kiszámításához, a fenti értékeket be kell szorozni 60.

### <a name="memory-allocations-system-wide-gb"></a>Memória kiosztásokat rendszerszintű (GB)
| DWU | smallrc | mediumrc | largerc | xlargerc |
|:--- |:---:|:---:|:---:|:---:|
| DW100 |6 |6 |12 |23 |
| DW200 |6 |12 |23 |47 |
| DW300 |6 |12 |23 |47 |
| DW400 |6 |23 |47 |94 |
| DW500 |6 |23 |47 |94 |
| DW600 |6 |23 |47 |94 |
| DW1000 |6 |47 |94 |188 |
| DW1200 |6 |47 |94 |188 |
| DW1500 |6 |47 |94 |188 |
| DW2000 |6 |94 |188 |375 |
| DW3000 |6 |94 |188 |375 |
| DW6000 |6 |188 |375 |750 |

Ebből a táblázatból tartozó rendszerszintű memória-hozzárendelések, láthatja, hogy a xlargerc erőforrás osztály egy DW2000 futó lekérdezés lefoglalt 375 GB memória összesen (6400 MB * 60 azokat a terjesztéseket / 1024 GB átalakítása) az SQL Data Warehouse a a teljes keresztül.

Ugyanazt a számítást vonatkozik statikus erőforrás.
 
## <a name="concurrency-slot-consumption"></a>Párhuzamossági tárolóhely-használat  
Az SQL Data Warehouse biztosít több memóriát az magasabb erőforrás osztályok futó lekérdezésekhez. Memória mérete rögzített erőforrás.  Ezért további fenntartott memória mérete lekérdezésenként, kevesebb párhuzamos lekérdezések hajthat végre. A következő táblázat ismételten kifejezi összes, a korábbi fogalmakat, amelyek a feldolgozási bővítőhelyek DWU által elérhető és a tárolóhelyek minden erőforrásosztály által felhasznált számát jeleníti meg egyetlen nézetben.  

### <a name="allocation-and-consumption-of-concurrency-slots-for-dynamic-resource-classes"></a>Foglalási és felhasználási párhuzamossági tárolóhelyek dinamikus erőforrás-osztályok  
| DWU | Maximális párhuzamos lekérdezések | Lefoglalt párhuzamossági tárhelyek | Üzembe helyezési ponti smallrc által használt | Üzembe helyezési ponti mediumrc által használt | Üzembe helyezési ponti largerc által használt | Üzembe helyezési ponti xlargerc által használt |
|:--- |:---:|:---:|:---:|:---:|:---:|:---:|
| DW100 |4 |4 |1 |1 |2 |4 |
| DW200 |8 |8 |1 |2 |4 |8 |
| DW300 |12 |12 |1 |2 |4 |8 |
| DW400 |16 |16 |1 |4 |8 |16 |
| DW500 |20 |20 |1 |4 |8 |16 |
| DW600 |24 |24 |1 |4 |8 |16 |
| DW1000 |32 |40 |1 |8 |16 |32 |
| DW1200 |32 |48 |1 |8 |16 |32 |
| DW1500 |32 |60 |1 |8 |16 |32 |
| DW2000 |32 |80 |1 |16 |32 |64 |
| DW3000 |32 |120 |1 |16 |32 |64 |
| DW6000 |32 |240 |1 |32 |64 |128 |

### <a name="allocation-and-consumption-of-concurrency-slots-for-static-resource-classes"></a>Foglalási és felhasználási párhuzamossági tárolóhelyek statikus erőforrás osztályok  
| DWU | Maximális párhuzamos lekérdezések | Lefoglalt párhuzamossági tárhelyek |staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:--- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| DW100 |4 |4 |1 |2 |4 |4 |4 |4 |4 |4 |
| DW200 |8 |8 |1 |2 |4 |8 |8 |8 |8 |8 |
| DW300 |12 |12 |1 |2 |4 |8 |8 |8 |8 |8 |
| DW400 |16 |16 |1 |2 |4 |8 |16 |16 |16 |16 |
| DW500 | 20| 20| 1| 2| 4| 8| 16| 16| 16| 16|
| DW600 | 24| 24| 1| 2| 4| 8| 16| 16| 16| 16|
| DW1000 | 32| 40| 1| 2| 4| 8| 16| 32| 32| 32|
| DW1200 | 32| 48| 1| 2| 4| 8| 16| 32| 32| 32|
| DW1500 | 32| 60| 1| 2| 4| 8| 16| 32| 32| 32|
| DW2000 | 32| 80| 1| 2| 4| 8| 16| 32| 64| 64|
| DW3000 | 32| 120| 1| 2| 4| 8| 16| 32| 64| 64|
| DW6000 | 32| 240| 1| 2| 4| 8| 16| 32| 64| 128|

Ezek a táblázatok a láthatja, hogy az SQL Data Warehouse fut, DW1000 foglal le, mely legfeljebb 32 egyidejű lekérdezéseket és 40 párhuzamossági tárolóhelyek összesen. Minden felhasználó smallrc fut, ha 32 egyidejű lekérdezések volna kell engedélyezett, mert minden egyes lekérdezés 1 párhuzamossági tárolóhely volna felhasználását. Ha minden felhasználó egy DW1000 mediumrc a futtatást, minden egyes lekérdezés szeretné kiosztani 800 MB eloszlása lekérdezésenként 47 GB memória összesen kiosztható és feldolgozási korlátozódik, 5 felhasználók (40 párhuzamossági üzembe helyezési ponti / 8 tárolóhely mediumrc felhasználónként).

## <a name="selecting-proper-resource-class"></a>Megfelelő erőforrásosztály kiválasztása  
Egy jó gyakorlat az, hogy véglegesen felhasználók hozzárendelése az erőforrás-osztályok módosítása helyett egy erőforrásosztály. Fürtözött oszloptárindexű táblákat terhelések hozzon létre például a következő: jobb minőségű indexeket, amikor több memóriát foglal le. Győződjön meg arról, hogy terhelések hozzáférhetnek a nagyobb memóriába, kifejezetten az adatok betöltése a felhasználó létrehozása, és a felhasználó véglegesen hozzárendelése egy magasabb erőforrásosztály.
Nincsenek ajánlott eljárásokat Itt néhány. Fent említett SQL DW támogatja-e a kétféle típusú erőforrások osztály: erőforrás statikus és dinamikus erőforrás-osztályok.
### <a name="loading-best-practices"></a>Gyakorlati tanácsok betöltése
1.  Ha az elvárások betölti a rendszeres adatmennyiséget, statikus erőforrásosztály érdemes használni. Később Ha vertikális felskálázásával eléréséhez további számítási teljesítménnyel rendelkezik, az adatraktár tudnak futni több egyidejű lekérdezéseket, a-kész, a betöltés felhasználó nem több memóriát igényel.
2.  Ha az elvárások nagyobb terhelés egyes esetekben, dinamikus erőforrásosztály érdemes használni. Később, ha vertikális felskálázásával eléréséhez további számítási teljesítménnyel rendelkezik, a betöltés felhasználó kap további memória az a-kész, így lehetővé téve a gyorsabb végrehajtásához a terhelés.

A terhelés hatékonyan feldolgozásához szükséges memória betöltve a tábla és a feldolgozott adatok mennyisége jellegétől függ. Például adatok közösségi koordináló intézet táblába töltéséhez ahhoz, hogy a közösségi koordináló intézet rowgroups optimalizálási elérni bizonyos memóriát igényel. További részletekért tekintse meg az Oszlopcentrikus indexek - Adatbetöltési útmutatást.

Ajánlott eljárásként javasoljuk terhelések legalább 200MB memória használata.

### <a name="querying-best-practices"></a>Gyakorlati tanácsok lekérdezése
Lekérdezések összetettségük függően különböző követelményekkel rendelkezik. Növelje a lekérdezésenként memóriát, vagy a feldolgozási növelését módon egyaránt érvényes lekérdezés igényeitől függően a teljes átviteli sebesség révén.
1.  Az elvárások rendszeres, összetett lekérdezések esetén (például napi és heti jelentések készítéséhez), és nem kell párhuzamossági előnyeit, dinamikus erőforrásosztály, akkor a. Ha a rendszer további adatokat feldolgozni, az adatraktár vertikális felskálázásával ezért automatikusan nyújtják több memóriát a lekérdezés futtatása a felhasználók számára.
2.  Ha elvárásainak változó vagy diurnal egyidejűségi minták (például ha az adatbázis lekérik a webes felhasználói felület széles körben elérhető keresztül), egy statikus erőforrásosztály, akkor a. Később Ha vertikális felskálázásával adatraktár, a felhasználó statikus erőforrásosztály társított automatikusan tudnak több egyidejű lekérdezések futtatásához.

Ez számos tényezőtől függ, például a lekérdezett, adatok mennyiségét a táblasémákat és különböző csatlakozást, kiválasztása és a csoport predikátumok jellege nem nem triviális, attól függően, hogy a lekérdezés kellene kiválasztásával megfelelő memóriaengedély. Egy általános tükrözik, rendeljen több memóriát lehetővé teszi a lekérdezéseket, amelyekkel gyorsabban befejeződjenek, de a teljes feldolgozási csökkenne. Párhuzamossági darabolása nem okoz problémát, ha nem árt túlzott memória lefoglalásakor. Átviteli sebesség finomhangolása, különböző változataira jellemző az erőforrás-osztályok tett kísérlet lehet szükség.

A következő tárolt eljárás segítségével mérje fel, egyidejűségi és a memória grant / erőforrás egy adott slo-t, és a legközelebbi legjobb erőforrás osztály memória intenzív közösségi koordináló intézet műveletekhez közösségi koordináló intézet tábla particionálva adott erőforrás osztályra:

#### <a name="description"></a>Leírás:  
A tárolt eljárás célja van:  
1. Segítségével mérje fel, felhasználói egyidejűségi és a memória adja meg egy erőforrás osztály egy adott SLO. Felhasználói kell megadni. null értékű séma és a tablename ehhez az alábbi példában látható módon.  
2. Felhasználói segítségével mérje fel, a memória intensed legközelebbi legjobb erőforrásosztály közösségi koordináló intézet műveleteket (terhelésétől, a másolási tábla rebuild index stb.) a megadott erőforrásosztály nem particionált közösségi koordináló intézet táblázat. A tárolt eljárás tudja meg a szükséges memória biztosítása a következő tábla sémáját használja.

#### <a name="dependencies--restrictions"></a>Függőségek és korlátozások:
- A tárolt eljárás nem célja, hogy a tábla particionálva közösségi koordináló intézet memóriakövetelményét kiszámításához.    
- A tárolt eljárás nem memóriakövetelményét CTAS/INSERT-VÁLASZTANI SELECT részében figyelembe veszi, és feltételezi, hogy lehet egy egyszerű jelöljön ki.
- A tárolt eljárás egy ideiglenes táblát használ, így használható a munkamenet hol jött létre a tárolt eljáráshoz.    
- A tárolt eljárás attól függ, az aktuális offerings (pl. hardverkonfiguráció, DMS config), és ha bármelyik, amely módosítja majd a tárolt eljárás nem megfelelően fog működni.  
- A tárolt eljárás attól függ, meglévő felajánlott feldolgozási korlátja, és ha megváltozik, majd a tárolt eljárás nem megfelelően fog működni.  
- A tárolt eljárás attól függ, meglévő erőforrás osztály ajánlatokat, és ha, amely módosítja majd ez tárolása megfelelő proc wuold nem működik.  

>  [!NOTE]  
>  Ha nem kap kimeneti megadva, előfordulhat, hogy két esetben paraméterekkel tárolt eljárás végrehajtása után. <br />1. Vagy DW paraméter érvénytelen SLO értéket tartalmazza: <br />2. VAGY nem megfelelő erőforrásosztály közösségi koordináló intézet művelet Ha megadva tábla neve. <br />Például: DW100, legmagasabb rendelkezésre memóriaengedély 400MB, és ha táblaséma elég széles kereszt-követelmény 400MB.
      
#### <a name="usage-example"></a>Példa:
Szintaxis:  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`  
1. @DWU:Adja meg az aktuális DWU kinyerése az Adatraktár-adatbázisban, vagy bármely támogatott DWU "DW100" formájában adja meg egy NULL értékű paramétert vagy
2. @SCHEMA_NAME:Adja meg a tábla a séma neve
3. @TABLE_NAME:Adjon meg egy tábla nevét, a fontos

A tárolt eljárás végrehajtása példák:  
```sql  
EXEC dbo.prc_workload_management_by_DWU 'DW2000', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000', NULL, NULL;  
EXEC dbo.prc_workload_management_by_DWU NULL, NULL, NULL;  
```

A fenti példákban használt Table1 sikerült létrehozni a következő:  
`CREATE TABLE Table1 (a int, b varchar(50), c decimal (18,10), d char(10), e varbinary(15), f float, g datetime, h date);`

#### <a name="heres-the-stored-procedure-definition"></a>Ez a tárolt eljárás definíciója:
```sql  
-------------------------------------------------------------------------------
-- Dropping prc_workload_management_by_DWU procedure if it exists.
-------------------------------------------------------------------------------
IF EXISTS (SELECT * FROM sys.objects WHERE type = 'P' AND name = 'prc_workload_management_by_DWU')
DROP PROCEDURE dbo.prc_workload_management_by_DWU
GO

-------------------------------------------------------------------------------
-- Creating prc_workload_management_by_DWU.
-------------------------------------------------------------------------------
CREATE PROCEDURE dbo.prc_workload_management_by_DWU
(   @DWU VARCHAR(7),
      @SCHEMA_NAME VARCHAR(128),
       @TABLE_NAME VARCHAR(128)
)
AS
IF @DWU IS NULL
BEGIN
-- Selecting proper DWU for the current DB if not specified.
SET @DWU = (
  SELECT 'DW'+CAST(COUNT(*)*100 AS VARCHAR(10))
  FROM sys.dm_pdw_nodes
  WHERE type = 'COMPUTE')
END

DECLARE @DWU_NUM INT
SET @DWU_NUM = CAST (SUBSTRING(@DWU, 3, LEN(@DWU)-2) AS INT)

-- Raise error if either schema name or table name is supplied but not both them supplied
--IF ((@SCHEMA_NAME IS NOT NULL AND @TABLE_NAME IS NULL) OR (@TABLE_NAME IS NULL AND @SCHEMA_NAME IS NOT NULL))
--     RAISEERROR('User need to supply either both Schema Name and Table Name or none of them')
       
-- Dropping temp table if exists.
IF OBJECT_ID('tempdb..#ref') IS NOT NULL
BEGIN
  DROP TABLE #ref;
END

-- Creating ref. temptable (CTAS) to hold mapping info.
-- CREATE TABLE #ref
CREATE TABLE #ref
WITH (DISTRIBUTION = ROUND_ROBIN)
AS 
WITH
-- Creating concurrency slots mapping for various DWUs.
alloc
AS
(
  SELECT 'DW100' AS DWU, 4 AS max_queries, 4 AS max_slots, 1 AS slots_used_smallrc, 1 AS slots_used_mediumrc,
        2 AS slots_used_largerc, 4 AS slots_used_xlargerc, 1 AS slots_used_staticrc10, 2 AS slots_used_staticrc20,
        4 AS slots_used_staticrc30, 4 AS slots_used_staticrc40, 4 AS slots_used_staticrc50,
        4 AS slots_used_staticrc60, 4 AS slots_used_staticrc70, 4 AS slots_used_staticrc80
  UNION ALL
    SELECT 'DW200', 8, 8, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW300', 12, 12, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW400', 16, 16, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
     SELECT 'DW500', 20, 20, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW600', 24, 24, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW1000', 32, 40, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1200', 32, 48, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1500', 32, 60, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW2000', 32, 80, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
   SELECT 'DW3000', 32, 120, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW6000', 32, 240, 1, 32, 64, 128, 1, 2, 4, 8, 16, 32, 64, 128
)
-- Creating workload mapping to their corresponding slot consumption and default memory grant.
,map
AS
(
  SELECT 'SloDWGroupC00' AS wg_name,1 AS slots_used,100 AS tgt_mem_grant_MB
  UNION ALL
    SELECT 'SloDWGroupC01',2,200
  UNION ALL
    SELECT 'SloDWGroupC02',4,400
  UNION ALL
    SELECT 'SloDWGroupC03',8,800
  UNION ALL
    SELECT 'SloDWGroupC04',16,1600
  UNION ALL
    SELECT 'SloDWGroupC05',32,3200
  UNION ALL
    SELECT 'SloDWGroupC06',64,6400
  UNION ALL
    SELECT 'SloDWGroupC07',128,12800
)
-- Creating ref based on current / asked DWU.
, ref
AS
(
  SELECT  a1.*
  ,       m1.wg_name          AS wg_name_smallrc
  ,       m1.tgt_mem_grant_MB AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.tgt_mem_grant_MB AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.tgt_mem_grant_MB AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.tgt_mem_grant_MB AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc80
  FROM alloc a1
  JOIN map   m1  ON a1.slots_used_smallrc     = m1.slots_used
  JOIN map   m2  ON a1.slots_used_mediumrc    = m2.slots_used
  JOIN map   m3  ON a1.slots_used_largerc     = m3.slots_used
  JOIN map   m4  ON a1.slots_used_xlargerc    = m4.slots_used
  JOIN map   m5  ON a1.slots_used_staticrc10    = m5.slots_used
  JOIN map   m6  ON a1.slots_used_staticrc20    = m6.slots_used
  JOIN map   m7  ON a1.slots_used_staticrc30    = m7.slots_used
  JOIN map   m8  ON a1.slots_used_staticrc40    = m8.slots_used
  JOIN map   m9  ON a1.slots_used_staticrc50    = m9.slots_used
  JOIN map   m10  ON a1.slots_used_staticrc60    = m10.slots_used
  JOIN map   m11  ON a1.slots_used_staticrc70    = m11.slots_used
  JOIN map   m12  ON a1.slots_used_staticrc80    = m12.slots_used
-- WHERE   a1.DWU = @DWU
  WHERE   a1.DWU = UPPER(@DWU)
)
SELECT  DWU
,       max_queries
,       max_slots
,       slots_used
,       wg_name
,       tgt_mem_grant_MB
,       up1 as rc
,       (ROW_NUMBER() OVER(PARTITION BY DWU ORDER BY DWU)) as rc_id
FROM
(
    SELECT  DWU
    ,       max_queries
    ,       max_slots
    ,       slots_used
    ,       wg_name
    ,       tgt_mem_grant_MB
    ,       REVERSE(SUBSTRING(REVERSE(wg_names),1,CHARINDEX('_',REVERSE(wg_names),1)-1)) as up1
    ,       REVERSE(SUBSTRING(REVERSE(tgt_mem_grant_MBs),1,CHARINDEX('_',REVERSE(tgt_mem_grant_MBs),1)-1)) as up2
    ,       REVERSE(SUBSTRING(REVERSE(slots_used_all),1,CHARINDEX('_',REVERSE(slots_used_all),1)-1)) as up3
    FROM    ref AS r1
    UNPIVOT
    (
        wg_name FOR wg_names IN (wg_name_smallrc,wg_name_mediumrc,wg_name_largerc,wg_name_xlargerc,
        wg_name_staticrc10, wg_name_staticrc20, wg_name_staticrc30, wg_name_staticrc40, wg_name_staticrc50,
        wg_name_staticrc60, wg_name_staticrc70, wg_name_staticrc80)
    ) AS r2
    UNPIVOT
    (
        tgt_mem_grant_MB FOR tgt_mem_grant_MBs IN (tgt_mem_grant_MB_smallrc,tgt_mem_grant_MB_mediumrc,
        tgt_mem_grant_MB_largerc,tgt_mem_grant_MB_xlargerc, tgt_mem_grant_MB_staticrc10, tgt_mem_grant_MB_staticrc20,
        tgt_mem_grant_MB_staticrc30, tgt_mem_grant_MB_staticrc40, tgt_mem_grant_MB_staticrc50,
        tgt_mem_grant_MB_staticrc60, tgt_mem_grant_MB_staticrc70, tgt_mem_grant_MB_staticrc80)
    ) AS r3
    UNPIVOT
    (
        slots_used FOR slots_used_all IN (slots_used_smallrc,slots_used_mediumrc,slots_used_largerc,
        slots_used_xlargerc, slots_used_staticrc10, slots_used_staticrc20, slots_used_staticrc30,
        slots_used_staticrc40, slots_used_staticrc50, slots_used_staticrc60, slots_used_staticrc70,
        slots_used_staticrc80)
    ) AS r4
) a
WHERE   up1 = up2
AND     up1 = up3
;
-- Getting current info about workload groups.
WITH  
dmv  
AS  
(
  SELECT
          rp.name                                           AS rp_name
  ,       rp.max_memory_kb*1.0/1048576                      AS rp_max_mem_GB
  ,       (rp.max_memory_kb*1.0/1024)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_MB
  ,       (rp.max_memory_kb*1.0/1048576)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_GB
  ,       wg.name                                           AS wg_name
  ,       wg.importance                                     AS importance
  ,       wg.request_max_memory_grant_percent               AS request_max_memory_grant_percent
  FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
  JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
                                                                  AND wg.pool_id      = rp.pool_id
  WHERE   rp.name = 'SloDWPool'
  GROUP BY
          rp.name
  ,       rp.max_memory_kb
  ,       wg.name
  ,       wg.importance
  ,       wg.request_max_memory_grant_percent
)
-- Creating resource class name mapping.
,names
AS
(
  SELECT 'smallrc' as resource_class, 1 as rc_id
  UNION ALL
    SELECT 'mediumrc', 2
  UNION ALL
    SELECT 'largerc', 3
  UNION ALL
    SELECT 'xlargerc', 4
  UNION ALL
    SELECT 'staticrc10', 5
  UNION ALL
    SELECT 'staticrc20', 6
  UNION ALL
    SELECT 'staticrc30', 7
  UNION ALL
    SELECT 'staticrc40', 8
  UNION ALL
    SELECT 'staticrc50', 9
  UNION ALL
    SELECT 'staticrc60', 10
  UNION ALL
    SELECT 'staticrc70', 11
  UNION ALL
    SELECT 'staticrc80', 12
)
,base AS
(   SELECT  schema_name
    ,       table_name
    ,       SUM(column_count)                   AS column_count
    ,       ISNULL(SUM(short_string_column_count),0)   AS short_string_column_count
    ,       ISNULL(SUM(long_string_column_count),0)    AS long_string_column_count
    FROM    (   SELECT  sm.name                                             AS schema_name
                ,       tb.name                                             AS table_name
                ,       COUNT(co.column_id)                                 AS column_count
                           ,       CASE    WHEN co.system_type_id IN (36,43,106,108,165,167,173,175,231,239) 
                                AND  co.max_length <= 32 
                                THEN COUNT(co.column_id) 
                        END                                                 AS short_string_column_count
                ,       CASE    WHEN co.system_type_id IN (165,167,173,175,231,239) 
                                AND  co.max_length > 32 and co.max_length <=8000
                                THEN COUNT(co.column_id) 
                        END                                                 AS long_string_column_count
                FROM    sys.schemas AS sm
                JOIN    sys.tables  AS tb   on sm.[schema_id] = tb.[schema_id]
                JOIN    sys.columns AS co   ON tb.[object_id] = co.[object_id]
                           WHERE tb.name = @TABLE_NAME AND sm.name = @SCHEMA_NAME
                GROUP BY sm.name
                ,        tb.name
                ,        co.system_type_id
                ,        co.max_length            ) a
GROUP BY schema_name
,        table_name
)
, size AS
(
SELECT  schema_name
,       table_name
,       75497472                                            AS table_overhead

,       column_count*1048576*8                              AS column_size
,       short_string_column_count*1048576*32                       AS short_string_size,       (long_string_column_count*16777216) AS long_string_size
FROM    base
UNION
SELECT CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as schema_name
         ,CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as table_name
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as table_overhead
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as column_size
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as short_string_size

,CASE WHEN COUNT(*) = 0 THEN 0 END as long_string_size
FROM   base
)
, load_multiplier as 
(
SELECT  CASE 
                     WHEN FLOOR(8 * (CAST (@DWU_NUM AS FLOAT)/6000)) > 0 THEN FLOOR(8 * (CAST (@DWU_NUM AS FLOAT)/6000)) 
                     ELSE 1 
              END AS multipliplication_factor
) 
       SELECT  r1.DWU
       , schema_name
       , table_name
       , rc.resource_class as closest_rc_in_increasing_order
       , max_queries_at_this_rc = CASE
             WHEN (r1.max_slots / r1.slots_used > r1.max_queries)
                  THEN r1.max_queries
             ELSE r1.max_slots / r1.slots_used
                  END
       , r1.max_slots as max_concurrency_slots
       , r1.slots_used as required_slots_for_the_rc
       , r1.tgt_mem_grant_MB  as rc_mem_grant_MB
       , CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) AS est_mem_grant_required_for_cci_operation_MB       
       FROM    size, load_multiplier, #ref r1, names  rc
       WHERE r1.rc_id=rc.rc_id
                     AND CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) < r1.tgt_mem_grant_MB
       ORDER BY ABS(CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) - r1.tgt_mem_grant_MB)
GO
```

## <a name="query-importance"></a>Lekérdezés fontossága
Az SQL Data Warehouse erőforrás osztályok munkaterhelés-csoport használatával valósít meg. Nincsenek összesen nyolc munkaterhelés-csoport, amely felügyeli az erőforrás osztályok között a különböző DWU méretű. A DWU az SQL Data Warehouse csak közül négyet használ a nyolc munkaterhelés-csoport. Ez teljesen logikus, mert egyes tevékenységprofil-csoport van rendelve egy négy erőforrás osztályok: smallrc, mediumrc, largerc, vagy xlargerc. A munkaterhelés-csoport ismertetése fontosságát az, hogy a munkaterhelés csoportok vannak beállítva a magasabb *fontossági*. Fontos használt CPU ütemezés. A nagyon fontos futtatása lekérdezések háromszor több, mint a közepes fontos CPU-ciklusok fogja kapni. Ezért a feldolgozási tárolóhely hozzárendelések is CPU prioritásának meghatározása. Lekérdezés 16 vagy több üzembe helyezési ponti használ fel, ha fut, nagyon fontos.

Az alábbi táblázat az egyes tevékenységprofil-csoport fontosság leképezéseit.

### <a name="workload-group-mappings-to-concurrency-slots-and-importance"></a>Párhuzamossági tárhelyek és a fontosság munkaterhelési csoport leképezései
| Munkaterhelés-csoport | Párhuzamossági tárolóhely leképezése | MB / terjesztési | Fontos leképezése |
|:--- |:---:|:---:|:--- |
| SloDWGroupC00 |1 |100 |Közepes |
| SloDWGroupC01 |2 |200 |Közepes |
| SloDWGroupC02 |4 |400 |Közepes |
| SloDWGroupC03 |8 |800 |Közepes |
| SloDWGroupC04 |16 |1,600 |Magas |
| SloDWGroupC05 |32 |3,200 |Magas |
| SloDWGroupC06 |64 |6,400 |Magas |
| SloDWGroupC07 |128 |12,800 |Magas |

Az a **foglalási és felhasználási párhuzamossági tárolóhelyek** diagram, ellenőrizheti, hogy egy DW500 használ 1, 4, 8, vagy 16 párhuzamossági üzembe helyezési ponti smallrc, mediumrc, largerc és xlargerc, illetve. Megtekintheti ezeket az értékeket az előző táblázat az egyes erőforrás fontossága kereséséhez.

### <a name="dw500-mapping-of-resource-classes-to-importance"></a>Az erőforrás fontossága osztályok DW500 leképezése
| Erőforrásosztály | A tevékenységprofil-csoport | Párhuzamossági tárhelyek használt | MB / terjesztési | Fontos |
|:--- |:--- |:---:|:---:|:--- |
| smallrc |SloDWGroupC00 |1 |100 |Közepes |
| mediumrc |SloDWGroupC02 |4 |400 |Közepes |
| largerc |SloDWGroupC03 |8 |800 |Közepes |
| xlargerc |SloDWGroupC04 |16 |1,600 |Magas |
| staticrc10 |SloDWGroupC00 |1 |100 |Közepes |
| staticrc20 |SloDWGroupC01 |2 |200 |Közepes |
| staticrc30 |SloDWGroupC02 |4 |400 |Közepes |
| staticrc40 |SloDWGroupC03 |8 |800 |Közepes |
| staticrc50 |SloDWGroupC03 |16 |1,600 |Magas |
| staticrc60 |SloDWGroupC03 |16 |1,600 |Magas |
| staticrc70 |SloDWGroupC03 |16 |1,600 |Magas |
| staticrc80 |SloDWGroupC03 |16 |1,600 |Magas |

A következő DMV-lekérdezés is használhatja, nézze meg a memória erőforrás-elosztás részletesen különbségek szempontjából az erőforrás-vezérlő, illetve elemzése a munkaterhelés-csoport aktív és előzménynaplók használata esetén végzett hibaelhárításhoz.

```sql
WITH rg
AS
(   SELECT  
     pn.name                        AS node_name
    ,pn.[type]                        AS node_type
    ,pn.pdw_node_id                    AS node_id
    ,rp.name                        AS pool_name
    ,rp.max_memory_kb*1.0/1024                AS pool_max_mem_MB
    ,wg.name                        AS group_name
    ,wg.importance                    AS group_importance
    ,wg.request_max_memory_grant_percent        AS group_request_max_memory_grant_pcnt
    ,wg.max_dop                        AS group_max_dop
    ,wg.effective_max_dop                AS group_effective_max_dop
    ,wg.total_request_count                AS group_total_request_count
    ,wg.total_queued_request_count            AS group_total_queued_request_count
    ,wg.active_request_count                AS group_active_request_count
    ,wg.queued_request_count                AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    
            ON  wg.pdw_node_id  = rp.pdw_node_id
            AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn
            ON    wg.pdw_node_id    = pn.pdw_node_id
    WHERE   wg.name like 'SloDWGroup%'
        AND     rp.name = 'SloDWPool'
)
SELECT    pool_name
,        pool_max_mem_MB
,        group_name
,        group_importance
,        (pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,        node_name
,        node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM    rg
ORDER BY
    node_name
,    group_request_max_memory_grant_pcnt
,    group_importance
;
```

## <a name="queries-that-honor-concurrency-limits"></a>Feldolgozási korlátok tiszteletben lekérdezések
A legtöbb lekérdezések erőforrás osztályok vonatkozik. Ezeket a lekérdezéseket is a párhuzamos lekérdezés és feldolgozási tárolóhely küszöbértékek kell férnie. A felhasználó nem választható, egy lekérdezés kizárását a párhuzamossági tárolóhely modell.

A következő utasításokat megújítja, tiszteletben az erőforrás-osztályok:

* INSERT-KIVÁLASZTÁSA
* FRISSÍTÉS
* TÖRLÉSE
* Válassza ki a (felhasználói táblák lekérdezésekor)
* ALTER INDEX REBUILD
* ALTER INDEX ÁTSZERVEZ
* ALTER TABLE REBUILD
* INDEX LÉTREHOZÁSA
* HOZZON LÉTRE FÜRTÖZÖTT OSZLOPCENTRIKUS INDEXET
* TABLE AS SELECT (CTAS) LÉTREHOZÁSA
* Az adatok betöltése
* Az adatátviteli műveletek elvégzése az adatok adatátviteli szolgáltatás (DMS)

## <a name="query-exceptions-to-concurrency-limits"></a>Lekérdezés kivételek feldolgozási korlátok
Egyes lekérdezések nem fogadják el a erőforrásosztály, amelyhez a felhasználó hozzá van rendelve. Ezeket a kivételeket a feldolgozási korlátok válnak, amikor egy adott parancshoz szükséges memória-erőforrások terhelése alacsony, gyakran mivel a parancs a metaadat-művelet. Az ilyen kivételek célja lekérdezések, amelyek soha nem kell őket nagyobb memória-foglalásának elkerülése érdekében. A felhasználóhoz rendelt tényleges erőforrásosztály függetlenül mindig használatra a ezekben az esetekben az alapértelmezett kis erőforrásosztály (smallrc). Például `CREATE LOGIN` mindig smallrc fog futni. Ez a művelet teljesítéséhez szükséges erőforrások nagyon alacsony, így nem célszerű a lekérdezés tartalmazza a feldolgozási tárolóhely modellben.  Ezeket a lekérdezéseket is nem korlátozza a 32 felhasználói feldolgozási korlát, korlátlan számú ezeket a lekérdezéseket is futtathat a munkamenet legfeljebb 1024 munkamenetek.

A következő utasítás nem fogadják el az erőforrás-osztályok:

* DROP TABLE vagy létrehozása
* AZ ALTER TABLE... KAPCSOLÓ, a megosztott vagy a partíció EGYESÍTÉSE
* AZ ALTER INDEX LETILTÁSA
* A DROP INDEX
* LÉTREHOZÁSI, frissítési vagy a DROP STATISTICS
* A TRUNCATE TABLE
* AZ ALTER ENGEDÉLYEZÉSI
* BEJELENTKEZÉS LÉTREHOZÁSA
* CREATE, ALTER és a DROP USER
* CREATE, ALTER vagy DROP ELJÁRÁST
* Vagy DROP NÉZET létrehozása
* ÉRTÉKEK BESZÚRÁSA
* Válassza ki a rendszer nézetek és dinamikus felügyeleti nézetek
* MAGYARÁZÓ
* DBCC

<!--
Removed as these two are not confirmed / supported under SQLDW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

##  <a name="changing-user-resource-class-example"></a>A felhasználói erőforrás osztály példa módosítása
1. **Hozzon létre bejelentkezési:** kapcsolatot létesíteni a **fő** adatbázis az SQL Data Warehouse-adatbázist futtató SQL-kiszolgálón, és a következő parancsok.
   
    ```sql
    CREATE LOGIN newperson WITH PASSWORD = 'mypassword';
    CREATE USER newperson for LOGIN newperson;
    ```
   
   > [!NOTE]
   > Célszerű egy felhasználó létrehozása az Azure SQL Data Warehouse-felhasználók számára a fő adatbázist. A felhasználó létrehozása a fő lehetővé teszi a felhasználóknak például az SSMS használatával adatbázis nevének megadása nélkül.  Emellett lehetővé teszi az object explorer segítségével megtekintheti az összes adatbázis SQL-kiszolgálón.  További létrehozásával és felhasználók kezelésével kapcsolatos további információkért lásd: [az SQL Data Warehouse adatbázis védelme][Secure a database in SQL Data Warehouse].
   > 
   > 
2. **Az SQL Data Warehouse-felhasználó létrehozása:** kapcsolatot létesíteni a **SQL Data Warehouse** adatbázis, és hajtsa végre a következő parancsot.
   
    ```sql
    CREATE USER newperson FOR LOGIN newperson;
    ```
3. **Engedélyek:** a következő példa engedélyezi a `CONTROL` a a **SQL Data Warehouse** adatbázis. `CONTROL`az adatbázist szintje megegyezik a db_owner az SQL Server kiszolgálón.
   
    ```sql
    GRANT CONTROL ON DATABASE::MySQLDW to newperson;
    ```
4. **Erőforrásosztály növelése:** használja a következő lekérdezést a felhasználó egy nagyobb munkaterhelés felügyeleti szerepkörbe való felvételre.
   
    ```sql
    EXEC sp_addrolemember 'largerc', 'newperson'
    ```
5. **Erőforrásosztály csökkentése:** felhasználó eltávolítása egy munkaterhelés felügyeleti szerepkört a következő lekérdezés segítségével.
   
    ```sql
    EXEC sp_droprolemember 'largerc', 'newperson';
    ```
   
   > [!NOTE]
   > Nincs lehetőség a felhasználó eltávolítása smallrc.
   > 
   > 

## <a name="queued-query-detection-and-other-dmvs"></a>Várólistára helyezett lekérdezés felderítését és egyéb dinamikus felügyeleti nézetek
Használhatja a `sys.dm_pdw_exec_requests` DMV lekérdezések egy feldolgozási sorban várakozó azonosításához. Lekérdezi a feldolgozási tárhely állapottal fog rendelkezni várakozással **felfüggesztve**.

```sql
SELECT      r.[request_id]                 AS Request_ID
        ,r.[status]                 AS Request_Status
        ,r.[submit_time]             AS Request_SubmitTime
        ,r.[start_time]                 AS Request_StartTime
        ,DATEDIFF(ms,[submit_time],[start_time]) AS Request_InitiateDuration_ms
        ,r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r;
```

Alkalmazások és szolgáltatások felügyeleti szerepköre megtekinthetők az `sys.database_principals`.

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0;
```

A következő lekérdezés jeleníti meg, hogy melyik szerepkört minden felhasználóhoz hozzá van rendelve.

```sql
SELECT     r.name AS role_principal_name
        ,m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id        = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE    r.name IN ('mediumrc','largerc', 'xlargerc');
```

Az SQL Data Warehouse rendelkezik várjon típusok a következők:

* **LocalQueriesConcurrencyResourceType**: lekérdezések, amelyek a feldolgozási tárolóhely keretrendszer kívül elhelyezkedik. DMV lekérdezések és a rendszer funkciókkal, mint például `SELECT @@VERSION` példák a helyi lekérdezések.
* **UserConcurrencyResourceType**: egyidejűségi tárolóhely keretein belül elhelyezkedik lekérdezések. Végfelhasználói táblák lekérdezéseket képviselő példák, amelyek szeretné használni az erőforrástípus.
* **DmsConcurrencyResourceType**: megvárja-e az adatátviteli műveletek eredő.
* **BackupConcurrencyResourceType**: A várakozás azt jelzi, hogy egy adatbázis biztonsági mentése van folyamatban. Az erőforrástípus maximális értéke 1. Ha több biztonsági mentés kért egy időben, a többi várólistájára.

A `sys.dm_pdw_waits` DMV kérelmet arra vár, hogy milyen erőforrásokat is használható.

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]            AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,    SESSION_ID()            AS Current_session
,    s.[status]            AS Session_status
,    s.[login_name]
,    s.[query_count]
,    s.[client_id]
,    s.[sql_spid]
,    r.[command]            AS Request_command
,    r.[label]
,    r.[status]            AS Request_status
,    r.[submit_time]
,    r.[start_time]
,    r.[end_compile_time]
,    r.[end_time]
,    DATEDIFF(ms,r.[submit_time],r.[start_time])        AS Request_queue_time_ms
,    DATEDIFF(ms,r.[start_time],r.[end_compile_time])    AS Request_compile_time_ms
,    DATEDIFF(ms,r.[end_compile_time],r.[end_time])        AS Request_execution_time_ms
,    r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE    w.[session_id] <> SESSION_ID();
```

A `sys.dm_pdw_resource_waits` DMV csak egy adott lekérdezésre által felhasznált erőforrások vár jeleníti meg. Erőforrás várakozási idő csak meg kell adni, szemben a jel várakozási időt, ami az alapul szolgáló SQL-kiszolgálók ütemezni a CPU, a lekérdezés szükséges idő erőforrások Várakozás időt méri.

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE    [session_id] <> SESSION_ID();
```

A `sys.dm_pdw_wait_stats` történelmi trendelemzés vár a DMV is használható.

```sql
SELECT    w.[pdw_node_id]
,        w.[wait_name]
,        w.[max_wait_time]
,        w.[request_count]
,        w.[signal_time]
,        w.[completed_count]
,        w.[wait_time]
FROM    sys.dm_pdw_wait_stats w;
```

## <a name="next-steps"></a>Következő lépések
Adatbázis-felhasználók és biztonsági kezelésével kapcsolatos további információkért lásd: [az SQL Data Warehouse adatbázis védelme][Secure a database in SQL Data Warehouse]. További információ a hogyan nagyobb erőforrás osztályok javíthatja a fürtözött oszlopcentrikus index minőségének, lásd: [szegmens minőségének javítására indexek újraépítése].

<!--Image references-->

<!--Article references-->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[szegmens minőségének javítására indexek újraépítése]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->
