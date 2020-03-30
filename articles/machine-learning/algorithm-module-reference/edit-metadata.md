---
title: 'Metaadatok szerkesztése: Modulhivatkozás'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a Metaadat-szerkesztés modult az Azure Machine Learningben az adatkészlet oszlopaihoz társított metaadatok módosításához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 9853a3decc8d145fee58d1da526926e224ee2030
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064243"
---
# <a name="edit-metadata-module"></a>Metaadat modul szerkesztése

Ez a cikk az Azure Machine Learning designerben található modult ismerteti (előzetes verzió).

A Metaadatok szerkesztése modullal módosíthatja az adatkészlet oszlopaihoz társított metaadatokat. Az adatkészlet értéke és adattípusa a Metaadat szerkesztése modul használata után megváltozik.

A tipikus metaadat-változások a következők lehetnek:
  
+ Logikai vagy numerikus oszlopok kategorikus értékekként való kezelése.
  
+ Annak jelzése, **class** hogy melyik oszlop tartalmazza az osztálycímkét, vagy tartalmazza a kategorizálni vagy előrejelezni kívánt értékeket.
  
+ Oszlopok megjelölése szolgáltatásként.
  
+ Dátum-idő értékek módosítása numerikus értékekre vagy fordítva.
  
+ Oszlopok átnevezése.
  
 A Metaadatok szerkesztése bármikor használható, amikor módosítania kell egy oszlop definícióját, általában úgy, hogy megfeleljen a későbbi modul követelményeinek. Egyes modulok például csak meghatározott adattípusokkal működnek, vagy `IsFeature` jelzőket igényelnek az oszlopokon, például vagy `IsCategorical`.  
  
 A szükséges művelet végrehajtása után visszaállíthatja a metaadatokat az eredeti állapotába.
  
## <a name="configure-edit-metadata"></a>Metaadatok szerkesztése
  
1. Az Azure Machine Learning tervezőjében adja hozzá a Metaadat szerkesztése modult a folyamathoz, és csatlakoztassa a frissíteni kívánt adatkészletet. A modul az **Adattranszformáció** kategóriában található.
  
1. Kattintson a modul jobb oldali paneljén található **Oszlop szerkesztése** elemre, és válassza ki a dolgozni kívánt oszlopot vagy oszlopkészletet. Az oszlopokat külön-külön is választhatja név vagy tárgymutató szerint, vagy típus szerint is választhatja az oszlopok csoportját.  
  
1. Válassza az **Adattípus** lehetőséget, ha más adattípust kell hozzárendelnie a kijelölt oszlopokhoz. Előfordulhat, hogy bizonyos műveletek esetében módosítania kell az adattípust. Ha például a forrásadatkészlet számait szövegként kezelik, a matematikai műveletek használata előtt számtípusra kell módosítani őket.

    + A támogatott adattípusok a következők: **Karakterlánc**, **Egész,** **Dupla**, **Logikai**és **DateTime**.

    + Ha több oszlopot jelöl ki, a metaadatok módosításait az *összes* kijelölt oszlopra alkalmaznia kell. Tegyük fel például, hogy két vagy három numerikus oszlopot választ. Az összeset karakterlánc-adattípusra módosíthatja, és átnevezheti őket egy műveletben. Az egyik oszlop azonban nem módosítható karakterlánc-adattípusra, egy másik oszlop pedig úszószámból egész számra.
  
    + Ha nem ad meg új adattípust, az oszlop metaadatai változatlanok maradnak.

    + Az oszlop típusa és értékei a Metaadat szerkesztése művelet végrehajtása után megváltoznak. Az eredeti adattípust bármikor helyreállíthatja az Oszlop adattípusának visszaállításához a Metaadatok szerkesztése segítségével.  

    > [!NOTE]
    > Ha bármilyen típusú számot **DateTime** típusra változtat, hagyja üresen a **DateTime Formátum mezőt.** Jelenleg nem lehet megadni a céladat-formátumot.  

1. A **Kategorikus** beállítás sal megadhatja, hogy a kijelölt oszlopokban lévő értékeket kategóriákként kell kezelni.

    Előfordulhat például, hogy van egy oszlopa, amely a 0, 1 és 2 számokat tartalmazza, de tudja, hogy a számok valójában "dohányos", "Nem dohányzó" és "Ismeretlen" számokat jelentenek. Ebben az esetben az oszlop kategorikusként való megjelölésével biztosíthatja, hogy az értékek csak az adatok csoportosítására legyenek használva, numerikus számításokban nem.
  
1. Használja a **Mezők** beállítást, ha módosítani szeretné, hogy az Azure Machine Learning hogyan használja fel az adatokat egy modellben.

    + **Funkció:** Ezzel a beállítással megjelölheti az oszlopokat szolgáltatásként olyan modulokban, amelyek csak jellemzőoszlopokon működnek. Alapértelmezés szerint az összes oszlop ot kezdetben szolgáltatásként kezeli a rendszer.  
  
    + **Címke**: Ezzel a beállítással jelölje meg a címkét, amelyet kiszámítható attribútumnak vagy célváltozónak is neveznek. Sok modul megköveteli, hogy pontosan egy címkeoszlop jelen van az adatkészletben.

        Sok esetben az Azure Machine Learning arra következtethet, hogy egy oszlop tartalmaz egy osztály címkét. A metaadatok beállításával biztosíthatja, hogy az oszlop azonosítása megfelelő legyen. A beállítás nem módosítja az adatértékeket. Csak azt módosítja, ahogyan egyes gépi tanulási algoritmusok kezelik az adatokat.
  
    > [!TIP]
    > Vannak olyan adatai, amelyek nem illeszkednek ezekbe a kategóriákba? Az adatkészlet például tartalmazhat olyan értékeket, például egyedi azonosítókat, amelyek változóként nem hasznosak. Néha az ilyen azonosítók problémákat okozhatnak, ha egy modellben használják.
    >
    > Szerencsére az Azure Machine Learning megőrzi az összes adatot, így nem kell törölnie ezeket az oszlopokat az adatkészletből. Ha bizonyos speciális oszlopokon műveleteket kell végrehajtania, csak ideiglenesen távolítsa el az összes többi oszlopot az [Adatkészlet oszlopok kijelölése](select-columns-in-dataset.md) modul használatával. Később az [Oszlopok](add-columns.md) hozzáadása modul segítségével egyesítheti az oszlopokat az adatkészletbe.  
  
1. Az alábbi beállításokkal törölheti az előző kijelöléseket, és visszaállíthatja a metaadatokat az alapértelmezett értékekre.  
  
    + **Törlési funkció**: Ezzel a beállítással eltávolíthatja a szolgáltatásjelzőt.  
  
         Kezdetben az összes oszlopot szolgáltatásként kezeli a funkció. A matematikai műveleteket végző modulok esetében előfordulhat, hogy ezt a beállítást kell használnia ahhoz, hogy megakadályozza a numerikus oszlopok változóként való kezelését.
  
    + **Címke törlése:** Ezzel a beállítással eltávolíthatja a **címke** metaadatait a megadott oszlopból.  
  
    + **Pont törlése:** Ezzel a beállítással eltávolíthatja a **pontszám** metaadatait a megadott oszlopból.  
  
         Jelenleg nem jelölhet meg explicit módon egy oszlopot pontszámként az Azure Machine Learningben. Egyes műveletek azonban azt eredményezik, hogy egy oszlop ot belső pontszámként jelölnek meg. Emellett egy egyéni R-modul kimeneti pontszám értékeket is kimenetele lehet.

1. Az **Új oszlopnevek mezőbe**írja be a kijelölt oszlop vagy oszlopok új nevét.  
  
    + Az oszlopnevek csak az UTF-8 kódolás által támogatott karaktereket használhatják. A teljes egészében szóközökből álló üres karakterláncok, nullértékek vagy nevek nem engedélyezettek.  
  
    + Több oszlop átnevezéséhez írja be a neveket vesszővel tagolt listaként az oszlopindexek sorrendjében.  
  
    + Az összes kijelölt oszlopot át kell nevezni. Az oszlopokat nem lehet kihagyni vagy kihagyni.  
  
1. Küldje el a folyamatot.  

## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md)
