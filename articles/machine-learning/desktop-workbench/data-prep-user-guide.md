---
title: Részletes útmutató, hogyan használható az Azure Machine Learning adat előkészített |} A Microsoft Docs
description: Ez a dokumentum nyújt áttekintést, és részletes tájékoztatást az Azure Machine Learning adat előkészített adatok problémák megoldásához
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 7536e67d0ae4973008c8acc91a99a7d0d286f9b8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46988733"
---
# <a name="data-preparations-user-guide"></a>Adatok előkészített használati útmutatója 

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Az Azure Machine Learning adat előkészített élményt nagy mennyiségű gazdag funkcionalitást biztosít. Ez a cikk a tapasztalatok legmélyebb részeit dokumentumok.

### <a name="step-execution-history-and-caching"></a>Lépés végrehajtási előzményeinek és gyorsítótárazás 
Adatok előkészített lépés előzményei gyorsítótárak teljesítmény javítása érdekében egy sorozatát tárolja. Ha egy lépés, és azt egy gyorsítótárat rákeres, akkor nem újra lesz végrehajtva. Ha egy írási letiltása a lépés előzmények végén, és oda-vissza tükrözése a lépéseket, de nem végez módosítást, az írási után először nem aktiválódik. Egy új írási történik, és felülírja az előzőt, ha Ön:

- Az írási blokk módosításokat.
- Átalakítás új blokkot ad hozzá, és helyezze az írási blokk, amely létrehoz egy gyorsítótárának érvénytelenítésére felett.
- A blokkolás az írási blokk, amely létrehoz egy gyorsítótárának érvénytelenítésére fent tulajdonságainak módosításához.
- Válassza ki a frissítési (így érvénytelenítése a gyorsítótárait) mintán.

### <a name="error-values"></a>Hibás értékek

Adatátalakítások egy bemeneti érték meghiúsulhat, mert ez az érték nem megfelelő kezelését. Ha például esetén típusú kényszert műveletek, a kényszert sikertelen lesz, ha a bemeneti karakterlánc nem konvertálható a megadott céltípust. A típus kényszert művelet előfordulhat, hogy egy karakterlánc típusú oszlop konvertálása a numerikus vagy logikai típusú vagy próbál ismétlődő egy oszlop, amely nem létezik. (Ez a hiba akkor fordul elő, áthelyezése eredményezi a *X oszlop törlése* műveleteket, mielőtt a *ismétlődő oszlopnév X* műveletet.)

Ezekben az esetekben az adat-Előkészítések kimenetként, hibát eredményez. Hiba értékek azt jelzik, hogy egy korábbi művelet sikertelen volt a megadott érték. Belső használatra van egy első osztályú érték típusú kezeli azokat, de ezek jelenléte nem alter típuson alapul egy oszlop, akkor is, ha egy oszlop teljes egészében hiba értékeket tartalmaz.

Hiba értékei könnyedén azonosíthatja. Ezek vörös színnel, és olvassa el az "Error". A hiba, vigye a kurzort hibaértéket szöveges leírása a hiba okának megállapításához.

Hibaértékek propagálása. Hiba után érték esetén, azt a legtöbb esetben propagálás keresztül legtöbb művelet hibát. Cserélje le vagy távolítsa el őket három módja van:

* Csere
    -  Jobb gombbal az oszlopra, és válassza ki **hibaértékek cseréje**. Kiválaszthatja, hogy egy érték az oszlopban található hiba az értékekhez.

* Eltávolítás
    - Adat-Előkészítések megőrzése, vagy távolítsa el a hibaértéket az interaktív szűrőket tartalmaz.
    - Jobb gombbal az oszlopra, és válassza ki **oszlop szűrése**. Megőrzése, vagy távolítsa el a hibaértéket, hozzon létre egy feltételes azzal a feltétellel *"is hiba"* vagy *"nem hiba."*

* Egy Python-kifejezés használatával hibaértékek feltételesen művelethez. További információkért lásd: a [Python bővítményekre vonatkozó szakaszt](data-prep-python-extensibility-overview.md).

### <a name="sampling"></a>Mintavételezés
Egy adatforrás fájlt nyers adatok egy vagy több forrásból, vagy a helyi fájlrendszer vagy egy távoli helyről származó vesz igénybe. A minta blokk lehetővé teszi, hogy adja meg, hogy az adatok egy részhalmazának használata minták létrehozásával. Egy minta, hanem a nagy méretű adathalmazt a gyakran működő vezet jobb teljesítményt hajt végre műveleteket a későbbi lépések során.

Egyes adatforrások fájl több minták előállított és tárolt. Azonban csak egy minta beállítható az aktív mintaként. Létrehozása, szerkesztése vagy törlése az adatforrás varázsló vagy a minta blokk szerkesztésével mintákat. Minden olyan adat-Előkészítések fájlok, amelyek eredendően hivatkozhatnak egy adatforrásra a minta használatát az adatforrások fájlban megadott.

Nincsenek konfigurálható eltérő paraméterekkel rendelkező mintavételezési stratégia elérhető, számos.

#### <a name="top"></a>felső
Ez a stratégia helyi vagy távoli fájlokat is alkalmazható. Az első N sorát (száma által meghatározott) vesz igénybe az adatforrásba.

#### <a name="random-n"></a>Véletlenszerű N 
Ez a stratégia csak helyi fájlok is alkalmazható. Véletlenszerű N sorát (száma által meghatározott) vesz igénybe az adatforrásba. Megadhat egy konkrét kezdőérték annak érdekében, hogy egyazon mintából jön létre, feltéve, hogy a számláló értéke is azonos.

#### <a name="random-"></a>Véletlenszerű % 
Ez a stratégia helyi vagy távoli fájlokat is alkalmazható. Mindkét esetben valószínűség és az egyik kezdőérték kell megadott, hasonlóan ahhoz, hogy a véletlenszerű N stratégiát.

A távoli fájlok minták további paramétereket kell megadni:

- Minta-generátor 
  - Válasszon ki egy Spark-fürtöt, vagy a távoli Docker számítási célt szolgáló a minta létrehozása. A projekt előre ahhoz, hogy ez a lista jelenik meg a számítási célnak kell létrehozni. Kövesse a a szakaszban ismertetett lépések "hozzon létre egy új számítási célnak" a [GPU használata az Azure Machine Learning](how-to-use-gpu.md) hozhat létre a számítási célokhoz.
- Minta storage 
  - Adjon meg egy köztes tárolási helye a távoli minta tárolásához. Ezt az elérési utat a bemeneti fájl helyről egy másik címtárban kell lennie.

#### <a name="full-file"></a>A teljes fájl 
Ez a stratégia csak a helyi fájlokhoz, figyelembe véve az adatforrás a teljes fájl alkalmazhatók. Ha a fájl túl nagy, ezzel a beállítással lassíthatja a későbbi műveletek az alkalmazásban. Előfordulhat, hogy használni egy másik mintavételezési stratégia.


### <a name="fork-merge-and-append"></a>Elágaztatását, egyesítése és hozzáfűzése

Ha szűrőt alkalmaz egy adatkészlet keresztül, a művelet felosztja a az adatokat két eredményhalmazt: egy jelöli, hogy a szűrő a sikeres rekordok, és a egy másik be van kapcsolva a rekordokat, amelyek nem. Mindkét esetben a felhasználó kiválaszthatja a megjelenítendő eredményhalmaz. A felhasználó elveti az egyéb adatkészletet, vagy helyezze egy új adatfolyamot. Az utóbbi lehetőséget nevezzük elágaztatási.

Elágazásra: 
1. Válassza ki egy oszlopot, kattintson a jobb gombbal, és válassza a **szűrő** oszlop.

2. Alatt **cél**válassza **sorok megtartása** az eredményhalmaz, amelyet átad a szűrő megjelenítéséhez.

3. Válassza ki **sorok eltávolítása** szeretne megjeleníteni a sikertelen.

4. Után **feltételek**válassza **szűri ki sorokat tartalmazó létrehozás adatfolyamot** elágazásra a nem megjelenített eredményhalmazt bemásolja egy új adatfolyamot.


Ez az eljárás egy adatkészletet, amely további előkészítést igényel különválasztása gyakran használják. Az elágaztatott adatkészlet előkészítése után szokás egyesítheti az adatokat a eredményhalmazt, az eredeti adatfolyam. (Egy elágazás művelet fordítva) az egyesítési művelet végrehajtásával, használja az alábbi műveletek egyikét:

- **Sorok hozzáfűzése**. Függőleges egyesíteni a két vagy több adatfolyam-gyűjteményre (row-wise). 
- **Oszlop hozzáfűzése**. Vízszintes egyesíteni a két vagy több adatfolyam-gyűjteményre (column-wise).


>[!NOTE]
>Oszlopok sikertelen hozzáfűzése a oszlop ütközés esetén.


Egy merge művelet után legalább egy adatfolyam-gyűjteményre hivatkozik egy forrás adatfolyamot. Adat-Előkészítések értesíti az alkalmazást, a lépések listája alatt a jobb alsó sarokban értesítést.


A hivatkozott adatfolyam minden művelethez a szülő adatfolyam a mintát használni a hivatkozásban szereplő folyamatból frissítéséhez. Ebben az esetben a művelet megerősítését kérő párbeszédpanelen váltja fel az adatokat a folyamat referencia értesítés jobb alsó sarokban. Adott párbeszédpanel megerősíti, hogy akkor frissítenie kell az adatok az adatfolyam minden olyan függőséget adatfolyamok módosítások szinkronizálása.

### <a name="list-of-appendices"></a>Mellékletek listája 
* [Támogatott adatforrások](data-prep-appendix2-supported-data-sources.md)  
* [Támogatott átalakítások](data-prep-appendix3-supported-transforms.md)  
* [Támogatott vizsgálók](data-prep-appendix4-supported-inspectors.md)  
* [Támogatott célhelyek](data-prep-appendix5-supported-destinations.md)  
* [Minta szűrőkifejezésekben a Pythonban](data-prep-appendix6-sample-filter-expressions-python.md)  
* [Minta átalakítási adatok folyamat kifejezések a Pythonban](data-prep-appendix7-sample-transform-data-flow-python.md)  
* [Minta az adatforrásokat a Pythonban](data-prep-appendix8-sample-source-connections-python.md)  
* [Minta cél kapcsolatok a Pythonban](data-prep-appendix9-sample-destination-connections-python.md)  
* [Minta oszlop-átalakításokra a Pythonban](data-prep-appendix10-sample-custom-column-transforms-python.md)  
