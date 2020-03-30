---
title: 'Oszlopok kijelölése az adatkészletben: Modulhivatkozás'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja az Oszlopok kiválasztása az Azure Machine Learning adatkészletmoduljában az oszlopok egy részhalmazának kiválasztásához az alsóbb rétegbeli műveletekben való használatra.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 714148815fa4ff543ecbf86ab21e8d51fe775543
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77153774"
---
# <a name="select-columns-in-dataset-module"></a>Oszlopok kijelölése az Adatkészlet modulban

Ez a cikk ismerteti a modul az Azure Machine Learning designer (előzetes verzió).

Ezzel a modullal kiválaszthatja az oszlopok egy részhalmazát, amelyet az alsóbb rétegbeli műveletekben kell használnia. A modul fizikailag nem távolítja el az oszlopokat a forrásadatkészletből; ehelyett az oszlopok egy részhalmazát hozza létre, hasonlóan az adatbázis *nézethez* vagy *a vetítéshez.*

Ez a modul akkor hasznos, ha korlátoznia kell az alsóbb rétegbeli művelethez rendelkezésre álló oszlopokat, vagy ha csökkenteni szeretné az adatkészlet méretét a szükségtelen oszlopok eltávolításával.

Az adatkészlet oszlopai az eredeti adatokkal megegyező sorrendben kerülnek ki, még akkor is, ha más sorrendben adja meg őket.

## <a name="how-to-use"></a>A használat módja

Ennek a modulnak nincsenek paraméterei. Az oszlopkijelölő vel kiválaszthatja a felvenni vagy kizárni kívánt oszlopokat.

### <a name="choose-columns-by-name"></a>Oszlopok kiválasztása név szerint

A modulban több lehetőség is van az oszlopok név szerint való kiválasztásához: 

+ Szűrés és keresés

    Kattintson a **NÉV SZERINT** beállításra.

    Ha már feltöltött adatkészletet csatlakoztatott, meg kell jelennie az elérhető oszlopok listájának. Ha nem jelennek meg oszlopok, előfordulhat, hogy az oszloplista megtekintéséhez felfelé irányuló modulokat kell futtatnia.

    A lista szűréséhez írja be a keresőmezőbe. Ha például beírja `w` a betűt a keresőmezőbe, a program szűri a `w`listát, hogy megjelenjenek a betűt tartalmazó oszlopnevek.

    Jelölje ki az oszlopokat, és kattintson a jobb nyílgombra, ha a kijelölt oszlopokat a jobb oldali ablaktáblában lévő listára szeretné helyezni.

    + Az oszlopnevek folyamatos tartományának kijelöléséhez nyomja le a **Shift + Click billentyűkombinációt.**
    + Ha egyes oszlopokat szeretne hozzáadni a kijelöléshez, nyomja le a **Ctrl + Click billentyűkombinációt.**

    A mentéshez és a bezáráshoz kattintson a pipagombra.

+ Nevek használata más szabályokkal együtt

    Kattintson a **SZABÁLYOKKAL** lehetőségre.
    
    Válasszon egy szabályt, például egy adott adattípus oszlopait.

    Ezután kattintson az adott típusú egyes oszlopokra név szerint, és vegye fel őket a kijelölési listára.

+ Vesszővel tagolt oszlopnevek beírása vagy beillesztése

    Ha az adatkészlet széles, egyszerűbb lehet az indexek vagy a létrehozott névlisták használata, ahelyett, hogy külön-külön választaná ki az oszlopokat. Feltételezve, hogy előre elkészítette a listát:

    1. Kattintson a **SZABÁLYOKKAL** lehetőségre. 
    2. Jelölje be **a Nincs oszlop**, jelölje be a **Belefoglalás**lehetőséget, majd kattintson a piros felkiáltójellel ellátott szövegdobozba. 
    3. Illessze be vagy írja be a korábban érvényesített oszlopnevek vesszővel tagolt listáját. A modul nem menthető, ha valamelyik oszlop nak érvénytelen neve van, ezért előzetesen ellenőrizze a neveket.
    
    Ezzel a módszerrel indexértékekkel is megadhatja az oszlopok listáját. 

### <a name="choose-by-type"></a>Választás típus szerint

Ha a **WITH RULES** beállítást használja, több feltételt is alkalmazhat az oszlopbeállításokra. Előfordulhat például, hogy csak numerikus adattípusú jellemzőoszlopokat kell beszereznie.

A **BEGIN WITH** beállítás határozza meg a kiindulási pontot, és fontos az eredmények megértéséhez. 

+ Ha az ÖSSZES OSZLOP lehetőséget **választja,** az összes oszlop hozzáadódik a listához. Ezután a Kizárás lehetőséget kell **használnia** bizonyos feltételeknek megfelelő oszlopok *eltávolításához.* 

    Előfordulhat például, hogy az összes oszloppal kezdi, majd név vagy típus szerint eltávolítja az oszlopokat.

+ Ha a NINCS OSZLOP lehetőséget **választja,** az oszlopok listája üresen indul. Ezután megadhatja az oszlopok listához *való hozzáadásához* kívánt feltételeket. 

    Ha több szabályt alkalmaz, minden feltétel **adalékanyag**. Tegyük fel például, hogy oszlop nélkül kezdi, majd az összes numerikus oszlop lekéri a szabályt. Az Automobile áradatkészletben, amely 16 oszlopot eredményez. Ezután a **+** jelre kattintva új feltételt szeretne hozzáadni, és válassza **az Összes szolgáltatás felvétele**lehetőséget. Az eredményül kapott adatkészlet tartalmazza az összes numerikus oszlopot, valamint az összes jellemzőoszlopot, beleértve néhány karakterlánc-jellemzőoszlopot is.

### <a name="choose-by-column-index"></a>Választás oszlopindex szerint

Az oszlopindex az eredeti adatkészleten belüli oszlop sorrendjére utal.

+ Az oszlopok számozása 1-től kezdődően egymás után történik.  
+ Oszlopok tartományának beszerezéséhez használjon kötőjelet. 
+ Nyílt végű specifikációk, `-3` például vagy `1-` nem engedélyezettek.
+ A duplikált indexértékek (vagy oszlopnevek) nem engedélyezettek, és hibát okozhatnak.

Ha például az adatkészlet legalább nyolc oszlopot tartalmaz, az alábbi példák bármelyikébe illeszthető be, hogy több, nem összefüggő oszlopot ad vissza: 

+ `8,1-4,6`
+ `1,3-8`
+ `1,3-6,4` 

a végső példa nem eredményez hibát; azonban egyetlen oszloppéldányt `4`ad vissza.



### <a name="change-order-of-columns"></a>Oszlopok sorrendjének módosítása

Az **Ismétlődések engedélyezése és az oszlopsorrend megőrzése a kijelölésben** beállítás üres listával kezdődik, és név vagy index szerint megadott oszlopokat ad hozzá. Más beállításokkal ellentétben, amelyek mindig a "természetes sorrendjükben" adják vissza az oszlopokat, ez a beállítás az oszlopokat a név vagy a lista sorrendjében adja ki. 

A Col1, Col2, Col3 és Col4 oszlopokkal rendelkező adatkészletben például megfordíthatja az oszlopok sorrendjét, és kihagyhatja a 2.

+ `Col4, Col3, Col1`
+ `4,3,1`


## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 