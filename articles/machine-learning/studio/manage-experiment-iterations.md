---
title: Kísérletismétlések kezelése
titleSuffix: ML Studio (classic) - Azure
description: A kísérletismétlések kezelése az Azure Machine Learning Studio-ban (klasszikus). A kísérletek korábbi futtatásait bármikor áttekintheti, hogy megtámadhassa, újra meglátogathassa, és végül megerősíthesse vagy finomíthassa a korábbi feltételezéseket.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/20/2017
ms.openlocfilehash: 5c8a278f09fdb3b605020e4c2fcf7aa2776906e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204307"
---
# <a name="manage-experiment-iterations-in-azure-machine-learning-studio-classic"></a>Kísérletismétlések kezelése az Azure Machine Learning Studio-ban (klasszikus)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

A prediktív elemzési modell kidolgozása egy iteratív folyamat - a kísérlet különböző funkcióinak és paramétereinek módosításakor az eredmények addig konvergálnak, amíg meg nem győződik arról, hogy betanított, hatékony modellel rendelkezik. A folyamat kulcsa a kísérlet paramétereinek és konfigurációinak különböző iterációinak nyomon követése.



A kísérletek korábbi futtatásait bármikor áttekintheti, hogy megtámadhassa, újra meglátogathassa, és végül megerősíthesse vagy finomíthassa a korábbi feltételezéseket. Kísérlet futtatásakor a Machine Learning Studio (klasszikus) megőrzi a futtatás előzményeit, beleértve az adatkészletet, a modult és a portkapcsolatokat és paramétereket. Ez az előzmények is rögzíti az eredményeket, futásidejű információk, például a kezdési és leállítási idő, naplóüzenetek és végrehajtási állapot. Bármikor visszatekinthet ezek bármelyik ére, hogy áttekintse a kísérlet és a köztes eredmények időrendjét. Akár a kísérlet egy korábbi sorozatával is elindulhat a lekérdezés és felfedezés új fázisába az egyszerű, összetett vagy akár együttesmodellezési megoldások létrehozásához vezető úton.

> [!NOTE]
> Amikor egy kísérlet egy korábbi sorozatát tekinti meg, a kísérlet adott verziója zárolva van, és nem szerkeszthető. A másolatot azonban mentheti, ha a **MENTÉS MÁSKÉNT** gombra kattint, és új nevet ad a másolatnak. A Machine Learning Studio (klasszikus) megnyitja az új példányt, amelyet ezután szerkeszthet és futtathat. A kísérlet ezen példánya elérhető a **KÍSÉRLETEK** listában az összes többi kísérlettel együtt.
> 
> 

## <a name="viewing-the-prior-run"></a>Az előző futtatás megtekintése
Ha egy olyan kísérlet van megnyitva, amelyet legalább egyszer futtatott, megtekintheti a kísérlet előző futtatását a Tulajdonságok ablaktáblában a **Futtatás előtt** elemre kattintva.

Tegyük fel például, hogy létrehoz egy kísérletet, és 11:23, 11:42 és 11:55-kor futtatja annak verzióit. Ha megnyitja a kísérlet utolsó sorozatát (11:55), és a **Futtatás előtt**gombra kattint, a 11:42-kor futtatott verzió megnyílik.

## <a name="viewing-the-run-history"></a>A futtatási előzmények megtekintése
A kísérlet összes korábbi futtatását megtekintheti, ha egy megnyitott kísérletben a **Futtatási előzmények megtekintése** elemre kattint.

Tegyük fel például, hogy létrehoz egy kísérletet a [lineáris regressziós][linear-regression] modullal, és meg szeretné figyelni, hogy a **tanulási sebesség** értékének módosítása milyen hatással van a kísérlet eredményeire. A kísérletet többször is futtatja a paraméter különböző értékeivel, az alábbiak szerint:

| Tanulási arány értéke | Futtatás kezdési időpontja |
| --- | --- |
| 0,1 |2014.09.11. 16:18:58 |
| 0,2 |2014.09.11. 16:24:33 |
| 0,4 |2014.09.11. 16:28:36 |
| 0,5 |2014.09.11. 15:33:31 |

Ha a **FUTTATÁSI ELŐZMÉNYEK MEGTEKINTÉSE**gombra kattint, megjelenik az összes futtatás listája:

![Példa futtatási előzményekre](./media/manage-experiment-iterations/viewrunhistory.jpg)

Kattintson ezek bármelyik futtatása megtekintéséhez pillanatképet a kísérlet a futtatás időpontjában. A konfiguráció, a paraméterértékek, a megjegyzések és az eredmények mind megmaradnak, hogy teljes feljegyzést adjon a kísérlet adott futtatásáról.

> [!TIP]
> A kísérlet ismétléseinek dokumentálásához minden futtatáskor módosíthatja a címet, frissítheti a kísérlet **összegzését** a tulajdonságok ablaktáblában, és megjegyzéseket fűzhet vagy frissíthet az egyes modulokhoz a módosítások rögzítéséhez. A cím, az összegzés és a modul megjegyzései a kísérlet minden egyes futtatásával mentésre kerülnek.
> 
> 

A Machine Learning Studio (klasszikus) **kísérletek** lapján végzett kísérletek listája mindig a kísérlet legújabb verzióját jeleníti meg. Ha a kísérlet egy korábbi sorozatát nyitja meg (a **Prior Run** vagy **VIEW RUN HISTORY**használatával), akkor a **futtatási előzmények megtekintése** gombra kattintva visszatérhet a vázlatverzióhoz, és kiválasztja a **szerkeszthető** **állapotú** iterációt.

## <a name="iterating-on-a-previous-run"></a>Előző futtatás iterálása
Ha a **Korábbi futtatás** vagy a **FUTTATÁSi előzmények megtekintése** elemre kattint, és megnyit egy korábbi futtatást, megtekintheti a befejezett kísérletet írásvédett módban.

Ha a kísérlet ismétlését az előző futtatáshoz való konfigurálásával szeretné kezdeni, ezt a futtatás megnyitásával és a **MENTÉS MÁSKÉNT**gombra kattintva teheti meg. Ez létrehoz egy új kísérletet, egy új címmel, egy üres futtatási előzményekkel, valamint az előző futtatás összes összetevőjével és paraméterértékével. Ez az új kísérlet a Machine Learning Studio (klasszikus) **kezdőlapján** található KÍSÉRLETEK lapon található, és módosíthatja és futtathatja azt, és új futtatási előzményeket hozhat meg a kísérlet ezen ismétléséhez. 

Tegyük fel például, hogy az előző szakaszban látható a kísérlet futtatási előzménye. Meg szeretné figyelni, hogy mi történik, ha a **Tanulási sebesség** paramétert 0,4-re állítja, és különböző értékeket próbál meg a **Képzési korszakok száma** paraméterhez.

1. Kattintson **a RUN HISTORY MEGTEKINTÉSE** gombra, és nyissa meg a 16:28:36-kor futtatott kísérlet ismétlését (amelyben a paraméter értékét 0,4-re állította be).
2. Kattintson **a MENTÉS MÁSKÉNT gombra.**
3. Írjon be egy új címet, és kattintson az **OK** gombra. Létrejön a kísérlet új másolata.
4. A **Betanítási korszakok száma** paraméter módosítása.
5. Kattintson **a FUTTATÁS gombra.**

Most már módosíthatja és futtathatja a kísérlet ezen verzióját, és új futtatási előzményeket hozhat össze a munka rögzítéséhez.

<!-- Module References -->
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
