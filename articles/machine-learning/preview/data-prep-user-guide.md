---
title: "Részletes útmutató az Azure Machine Learning adatok előkészített használata |} Microsoft Docs"
description: "Ez a dokumentum nyújt áttekintést és az Azure Machine Learning adatok előkészített adatok problémáinak megoldásával kapcsolatos részletek"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: 9bcdd539c199086e0f48c1172853ff00cc1617f8
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="data-preparations-user-guide"></a>Adatok előkészített felhasználói útmutatója 
Az Azure Machine Learning adatok előkészített élmény nagy mennyiségű funkciók széles körét biztosítja. Ez a cikk a felhasználói élmény a legalsó szintű részeit dokumentumokat.

### <a name="step-execution-history-and-caching"></a>Lépés végrehajtása, előzményei és gyorsítótárazása 
Adatok előkészített lépés előzmények fenntartja a teljesítményre vonatkozó megfontolásból gyorsítótárak sorozata. Ha egy lépés, és találatok száma a gyorsítótárban, akkor nem hajtsa végre újból. Ha egy írási blokkot a lépés előzmények végén, és oda-vissza tükrözése a lépéseket, de nem végez módosítást, a írási után először nincs elindítva. Egy új írási következik be, és a felváltja azt, ha Ön:

- Módosíthatja a írási blokkot.
- Adja hozzá egy új átalakító blokkot, és helyezze a írási blokkot, amely állít elő, a gyorsítótár érvénytelenítési fent.
- Az írási blokk, amely állít elő, a gyorsítótár érvénytelenítési fent egy blokk tulajdonságainak módosításához.
- Válassza ki a frissítési (így érvénytelenítése a gyorsítótárak) mintán.

### <a name="error-values"></a>Hiba értékek

Az adatátalakításokat egy bemeneti érték meghiúsulhat, mert ez az érték nem megfelelő kezelését. Például típusú kényszerítési műveletek esetében a kényszerítési sikertelen lesz, ha a bemeneti karakterlánc nem konvertálható a megadott cél típusát. A típus kényszerítési művelet lehet, hogy egy numerikus vagy logikai típusú karakterlánc típusú oszlop átalakítása vagy megpróbálja ismétlődő egy oszlopot, amely nem létezik. (Ez a hiba akkor fordul elő, áthelyezése miatt a *oszlop X törlése* műveleteket, mielőtt a *ismétlődő oszlopneveket X* műveletet.)

Ezekben az esetekben az előkészített hibaértéket kimeneteként hoz létre. Hiba értékek azt jelzik, hogy egy korábbi művelet sikertelen a megadott érték. Belsőleg ezeket még kezelni első osztályú értéktípusként, de ezek jelenléte nem módosítsa egy olyan oszloptól, alapul szolgáló típusa, akkor is, ha egy oszlop teljes egészében hiba értékeket tartalmaz.

Hiba értékei könnyedén azonosíthatja. Most pirossal kiemelt és olvassa el az "Error". A hiba okának megállapításához, a leírás a sikertelen megjelenítéséhez hibaértéket mutasson.

Hibaértékek propagálása. Hiba után érték esetén, azt a legtöbb esetben lejjebb legtöbb műveletek révén hibát. Cserélje le vagy távolítsa el őket három módja van:

* Csere
    -  Kattintson a jobb gombbal az oszlop, és válassza ki **hibaértékek cseréje**. Ezután válasszon ki egy helyettesítő értéket az egyes hibák oszlopában.

* Eltávolítás
    - Előkészített adatok megőrzése, vagy távolítsa el a hibaértéket interaktív szűrőket tartalmaz.
    - Kattintson a jobb gombbal az oszlop, és válassza ki **Szűrőoszlopokat**. Megőrizheti a, vagy távolítsa el a hibaértéket, hozzon létre egy feltételes azzal a feltétellel *"hiba az"* vagy *"nincs hiba."*

* A Python kifejezés használatával hibaértékek feltételesen működik. További információkért lásd: a [szakaszt, Python bővítmények](data-prep-python-extensibility-overview.md).

### <a name="sampling"></a>Mintavételezés
A nyers adatokat adatforrások fájl egy vagy több forrásból, vagy a helyi fájlrendszer vagy egy távoli helyről vesz igénybe. A minta blokk adja meg, hogy az adatok egy részét együttműködve minták létrehozásával teszi lehetővé. Nagy adatbázisból, hanem az adatok mintán gyakran működő vezet jobb teljesítményt amikor végrehajtja a későbbi lépésekben szereplő műveleteket.

Minden egyes adatforrások fájl több mintához jön létre és tárolja. Azonban csak egy minta az aktív minta jelzőként állíthatók be. Létrehozása, szerkesztése vagy törlése a mintákat az adatforrás varázsló vagy a minta blokk szerkesztésével. Egy adatforrásra hivatkoznak eredendően adatok előkészített fájlok használata a minta az adatforrások fájlban megadott.

Számos mintavételi stratégiák érhető el, minden más konfigurálható paraméterekkel.

#### <a name="top"></a>Az első
Ezt a stratégiát helyi vagy távoli fájlok alkalmazhatja. Az első N sorát (száma által meghatározott) az adatforrás vesz igénybe.

#### <a name="random-n"></a>Véletlenszerű N 
Ezt a stratégiát csak helyi fájlok alkalmazhatja. Véletlenszerű N sorát (száma által meghatározott) az adatforrás vesz igénybe. Egy adott kezdőérték, annak érdekében, hogy az azonos minta létrehozása történik, feltéve, hogy a száma megegyezik is biztosíthat.

#### <a name="random-"></a>Véletlenszerű % 
Ezt a stratégiát helyi vagy távoli fájlok alkalmazhatja. Mindkét esetben valószínűség és az egyik kezdőérték legyen megadva, a véletlenszerű N stratégia hasonló.

A távoli fájlok mintákat további paramétereket kell biztosítani:

- A minta-készítő 
  - Válasszon ki egy Spark-fürt, vagy távoli Docker számítási cél-és a minta létrehozásánál használható. A projekt előzetesen ahhoz, hogy a listában jelennek meg a számítási cél kell létrehozni. Hajtsa végre az a szakaszban található lépéseket "létre új számítási cél" [GPU használata az Azure Machine Learning](how-to-use-gpu.md) számítási célok létrehozásához.
- A minta-tároló 
  - Adja meg a távoli minta tárolási egy köztes tárolási helyét. Ennek az elérési útnak kell lennie egy másik címtárat a bemeneti fájl helyről.

#### <a name="full-file"></a>Teljes fájl 
Ezt a stratégiát csak helyi fájlok, a teljes fájl véve az adatforrás alkalmazhatja. Ha a fájl túl nagy, ez a beállítás lelassíthatják a jövőbeli műveleteket az alkalmazásban. Bizonyára hasznosnak találja azt egy másik mintavételi stratégia felhasználását.


### <a name="fork-merge-and-append"></a>Forkolhatja, egyesítése és hozzáfűzése

Alkalmazásakor a szűrő egy adatkészlet keresztül a művelet az adatok felosztja a két eredményhalmazt: egy képviseli azt jelzi, hogy a szűrő sikeres, és a rekordok eleget nem tevő van egy másik készlet. Mindkét esetben a felhasználó választhatja ki a megjelenítendő eredményhalmaz. A felhasználó elveti a DataSet adatkészlet és helyezheti el egy új adatfolyam. Az utóbbi lehetőséget nevezzük elágazó.

Oszthatja ketté: 
1. Válasszon egy oszlopot, kattintson a jobb gombbal, majd válassza a **szűrő** oszlop.

2. A **I szeretné, hogy**, jelölje be **sorok megtartása** az eredményhalmazban, amelyet továbbküld a szűrő megjelenítése.

3. Válassza ki **sorok eltávolítása** szeretne megjeleníteni a sikertelen.

4. Után **feltételek**, jelölje be **szűrt kimenő sorokat tartalmazó létrehozása Adatfolyamblokk** oszthatja ketté beállítani egy új adatfolyama nem megjelenítési eredménye.


Ez az eljárás további előkészítést igénylő adatok elkülönítésére gyakran használják. Miután wrangle villás adatkészlet, általában egyesítheti az adatokat az eredeti adatfolyam eredményhalmazt. Az egyesítés (elágazás művelet fordítva), végrehajtásához használja a következő műveletek egyikét:

- **Sor hozzáfűzése**. Két vagy több adatfolyamok függőleges egyesítési (row-wise). 
- **Oszlopok hozzáfűzése**. Két vagy több adatfolyamok vízszintesen egyesítési (column-wise).


>[!NOTE]
>Hozzáfűzendő oszlopok sikertelen oszlop ütközés esetén.


A partícióegyesítési művelet után egy vagy több adatfolyamok forrás az adatfolyam hivatkoznak. Adatok előkészített értesíti a jobb alsó sarkában található lépések listája alatt, az alkalmazás értesítést.


A hivatkozott adatfolyam minden művelethez a szülő adatfolyam a hivatkozásban szereplő adatfolyamban felhasznált minta frissítéséhez. A művelet megerősítését kérő párbeszédpanelen ebben az esetben az adatok folyamata hivatkozás értesítés jobb alsó sarokban váltja fel. Ezen a párbeszédpanelen megerősíti, hogy a kell az bármely függőségi adatfolyamok módosításainak szinkronizálása az adatfolyam.

### <a name="list-of-appendices"></a>Mellékletek listája 
* [Támogatott adatforrások](data-prep-appendix2-supported-data-sources.md)  
* [Támogatott átalakítások](data-prep-appendix3-supported-transforms.md)  
* [Támogatott ellenőrök](data-prep-appendix4-supported-inspectors.md)  
* [Támogatott célok](data-prep-appendix5-supported-destinations.md)  
* [A Python minta szűrőkifejezéseket](data-prep-appendix6-sample-filter-expressions-python.md)  
* [A minta átalakítási adatok folyamata kifejezések Python](data-prep-appendix7-sample-transform-data-flow-python.md)  
* [A Python minta adatforrások](data-prep-appendix8-sample-source-connections-python.md)  
* [A minta cél kapcsolatok a Python](data-prep-appendix9-sample-destination-connections-python.md)  
* [A minta oszlop átalakítja a Python](data-prep-appendix10-sample-custom-column-transforms-python.md)  
