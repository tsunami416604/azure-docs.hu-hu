---
title: Kísérletismétlések kezelése
titleSuffix: ML Studio (classic) - Azure
description: A kísérletek közelítésének kezelése Azure Machine Learning Studio (klasszikus). A kísérletek az előző futtatásokat annak érdekében, hogy az ellenőrző kérdés, nyissa meg újra, és végső soron erősítse meg vagy finomíthatja a korábbi feltételezések bármikor tekintheti meg.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: seodec18
ms.date: 03/20/2017
ms.openlocfilehash: 71a20f0de9907b04b9dcfd63c0003f2540a2e370
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153383"
---
# <a name="manage-experiment-iterations-in-azure-machine-learning-studio-classic"></a>Kísérletezési ismétlések kezelése Azure Machine Learning Studio (klasszikus)
Prediktív elemzési modellek fejlesztése iteratív folyamat - módosítása a különböző függvényeket és paramétereket a kísérlet, az eredmények közelítjük, amíg olyan nem egy betanított, hatékony modellel rendelkezünk. Ez a folyamat kulcs a kísérlet paraméterek és konfigurációkkal különböző ismétlésének nyomon követéséhez.



A kísérletek az előző futtatásokat annak érdekében, hogy az ellenőrző kérdés, nyissa meg újra, és végső soron erősítse meg vagy finomíthatja a korábbi feltételezések bármikor tekintheti meg. Kísérlet futtatásakor Machine Learning Studio (klasszikus) megtartja a Futtatás előzményeit, beleértve az adatkészletet, a modult, valamint a portok kapcsolatait és paramétereit. Az előzményekben is eredményeket, például indítási és leállási idő, naplóüzenetek és végrehajtási állapot runtime adatait rögzíti. Megtekintheti vissza bármely ezek fut, és tekintse át a kísérletet, és a köztes eredményeket kronológiát biztosíthatnak. A kísérlet egy korábbi futtatás segítségével még be egy új lekérdezést és a felderítési fázisa az elérési útra, egyszerű, bonyolult vagy akár ensemble modellezési megoldások létrehozásához indítsa el.

> [!NOTE]
> Egy kísérlet egy korábbi futtatás megtekintésekor az adott verziójával a kísérletvászonra zárolva van, ezért nem szerkeszthető. A másolatát azonban mentheti, ha a **Mentés másként** lehetőségre kattint, és megadja a másolás új nevét. Machine Learning Studio (klasszikus) megnyitja az új példányt, amelyet aztán szerkeszthet és futtathat. A kísérlet ezen példánya az összes többi kísérlettel együtt elérhető a **kísérletek** listáján.
> 
> 

## <a name="viewing-the-prior-run"></a>A korábbi futtatás megtekintése
Ha olyan kísérletet nyit meg, amelyet legalább egyszer futtatott, a Tulajdonságok ablaktáblán a **korábbi Futtatás** lehetőségre kattintva megtekintheti a kísérlet előző futtatását.

Tegyük fel például, a kísérlet létrehozása és futtatása a verziókat, 11:23., 11:42 és 11:55. Ha megnyitja a kísérlet utolsó futtatását (11:55), és a **korábbi Futtatás**gombra kattint, megnyílik a 11:42-on futtatott verzió.

## <a name="viewing-the-run-history"></a>A futtatási előzmények megtekintése
A kísérletek összes korábbi futtatását megtekintheti, ha a **futtatási előzmények megtekintése** lehetőségre kattint egy nyitott kísérletben.

Tegyük fel például, hogy létrehoz egy kísérletet a [lineáris regressziós][linear-regression] modullal, és szeretné megfigyelni, hogy milyen hatással van a **tanulási ráta** értékének módosítására a kísérlet eredményein. Akkor futtathatja a kísérletet többször eltérő értékek ezt a paramétert, a következőképpen:

| Tanulási ráta érték | Futtatás kezdő időpontja |
| --- | --- |
| 0,1 |9/11/2014 du. 4:18:58 |
| 0.2 |9/11/2014 du. 4:24:33 |
| 0,4 |9/11/2014 du. 4:28:36 |
| 0,5 |9/11/2014 du. 4:33:31 |

Ha a **futtatási előzmények megtekintése**elemre kattint, megjelenik a következő futtatások listája:

![A példában a futtatási előzmények](./media/manage-experiment-iterations/viewrunhistory.jpg)

Kattintson bármelyik ezek futtatások megtekintése a kísérlet azt futtatta időben pillanatképét. A konfiguráció, paraméterértékeket, megjegyzések és eredmények összes megmaradnak, hogy a Futtatás a kísérlet egy teljes rekord.

> [!TIP]
> A kísérlet ismétlésének dokumentálása érdekében minden egyes futtatásakor módosíthatja a címet, a Tulajdonságok ablaktáblán frissítheti a kísérlet **összegzését** , és hozzáadhat vagy frissíthet megjegyzéseket az egyes modulokhoz a módosítások rögzítéséhez. A cím, Összegzés és modul megjegyzések minden egyes a kísérlet futtatásához együtt lesznek mentve.
> 
> 

Machine Learning Studio (klasszikus) **kísérletek** lapján a kísérletek listája mindig megjeleníti a kísérlet legújabb verzióját. Ha a kísérlet előző futtatását ( **korábbi Futtatás** vagy **MEGTEKINTÉS futtatási előzmények**használatával) nyitja meg, akkor a Piszkozat verzióra való VISSZATÉRÉShez kattintson a **futtatási előzmények megtekintése** elemre, és válassza ki a **szerkeszthető** **állapotú** iterációt.

## <a name="iterating-on-a-previous-run"></a>A korábbi futtatása léptetés
Ha a korábbi **Futtatás** vagy a **futtatási előzmények megtekintése** elemre kattint, és megnyit egy előző futtatást, megtekintheti a befejezett kísérletet írásvédett módban.

Ha meg szeretné kezdeni a kísérlet iterációját, ahogy azt az előző futtatáshoz konfigurálta, akkor nyissa meg a futtatást, és kattintson a **Mentés másként**lehetőségre. Ez egy új kísérlet hoz létre egy új címmel, egy üres futtatási előzmények, és az összetevők és a paraméterértékek az előző futtatható. Ez az új kísérlet a Machine Learning Studio (klasszikus) Kezdőlap **kísérletek** lapján található, és módosíthatja és futtathatja a kísérlethez szükséges új futtatási előzményeket. 

Tegyük fel például, a futtatási előzmények az előző szakaszban látható kísérletet. Szeretné megfigyelni, mi történik, ha a képzési **sebesség** paraméterét 0,4-ra állítja be, és különböző értékeket próbál meg használni a **Training epochs** paraméter számára.

1. Kattintson a **futtatási előzmények megtekintése** elemre, és nyissa meg az 4:28:36 órakor futtatott kísérlet iterációját (amelyben a paraméter értékét 0,4-re állítja).
2. Kattintson **a Mentés másként**elemre.
3. Adjon meg egy új címet, és kattintson az **OK pipa gombra** . A kísérlet egy új példányát jön létre.
4. A **képzési korszakok paraméter számának** módosítása.
5. Kattintson a **Futtatás**gombra.

Most már továbbra is módosíthatja, és ez a kísérlet, a verzió fut egy új futtatási előzmények jegyezze fel a munkahelyi létrehozásához.

<!-- Module References -->
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
