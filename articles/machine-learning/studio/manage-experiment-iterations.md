---
title: Kísérletismétlések kezelése
titleSuffix: Azure Machine Learning Studio (classic)
description: A kísérletek közelítésének kezelése Azure Machine Learning Studio (klasszikus). A kísérletek korábbi futtatásait bármikor áttekintheti, hogy megtámadja, újra felkeresse és végső soron megerősítse vagy pontosítsa a korábbi feltételezéseket.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 03/20/2017
ms.openlocfilehash: d2c1f31db93d2caecc5576de18eb15e01cc94461
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492845"
---
# <a name="manage-experiment-iterations-in-azure-machine-learning-studio-classic"></a>Kísérletezési ismétlések kezelése Azure Machine Learning Studio (klasszikus)
A prediktív elemzési modell kialakítása egy iterációs folyamat – ahogy módosítja a kísérlet különböző funkcióit és paramétereit, az eredmények konvergálnak, amíg nem meggyőződött arról, hogy Ön rendelkezik egy betanított, hatékony modellel. A folyamat kulcsa a kísérlet paramétereinek és konfigurációinak különböző ismétléseit követi nyomon.



A kísérletek korábbi futtatásait bármikor áttekintheti, hogy megtámadja, újra felkeresse és végső soron megerősítse vagy pontosítsa a korábbi feltételezéseket. Kísérlet futtatásakor Machine Learning Studio (klasszikus) megtartja a Futtatás előzményeit, beleértve az adatkészletet, a modult, valamint a portok kapcsolatait és paramétereit. Ez az előzmények rögzíti az eredményeket, a futásidejű adatokat, például az indítási és leállítási időpontokat, a naplózási üzeneteket és a végrehajtási állapotot. Bármikor visszatekintheti ezen futtatások bármelyikét, hogy áttekintse a kísérlet és a köztes eredmények időrendjét. A kísérlet korábbi futtatásával is elindíthat egy új, a lekérdezési és felderítési fázist az egyszerű, összetett vagy akár Ensemble modellezési megoldások létrehozásához.

> [!NOTE]
> Amikor egy kísérlet korábbi futtatását tekinti meg, a kísérlet ezen verziója zárolva van, és nem szerkeszthető. A másolatát azonban mentheti, ha a **Mentés másként** lehetőségre kattint, és megadja a másolás új nevét. Machine Learning Studio (klasszikus) megnyitja az új példányt, amelyet aztán szerkeszthet és futtathat. A kísérlet ezen példánya az összes többi kísérlettel együtt elérhető a **kísérletek** listáján.
> 
> 

## <a name="viewing-the-prior-run"></a>A korábbi Futtatás megtekintése
Ha olyan kísérletet nyit meg, amelyet legalább egyszer futtatott, a Tulajdonságok ablaktáblán a **korábbi Futtatás** lehetőségre kattintva megtekintheti a kísérlet előző futtatását.

Tegyük fel például, hogy létrehoz egy kísérletet, és futtatja azt a 11:23-es, 11:42-as és 11:55-es verzióban. Ha megnyitja a kísérlet utolsó futtatását (11:55), és a **korábbi Futtatás**gombra kattint, megnyílik a 11:42-on futtatott verzió.

## <a name="viewing-the-run-history"></a>A futtatási előzmények megtekintése
A kísérletek összes korábbi futtatását megtekintheti, ha a **futtatási előzmények megtekintése** lehetőségre kattint egy nyitott kísérletben.

Tegyük fel például, hogy létrehoz egy kísérletet a [lineáris regressziós][linear-regression] modullal, és szeretné megfigyelni, hogy milyen hatással van a **tanulási ráta** értékének módosítására a kísérlet eredményein. A kísérletet többször is futtathatja a paraméter különböző értékeivel, a következőképpen:

| Tanulási arány értéke | Futtatás kezdő időpontja |
| --- | --- |
| 0,1 |9/11/2014 4:18:58 PM |
| 0,2 |9/11/2014 4:24:33 PM |
| 0,4 |9/11/2014 4:28:36 PM |
| 0,5 |9/11/2014 4:33:31 PM |

Ha a **futtatási előzmények megtekintése**elemre kattint, megjelenik a következő futtatások listája:

![Példa a futtatási előzményekre](./media/manage-experiment-iterations/viewrunhistory.jpg)

A futtatások bármelyikére kattintva megtekintheti a kísérlet pillanatképét, amikor futtatja azt. A konfiguráció, a paraméterek értékei, a megjegyzések és az eredmények megmaradnak, hogy teljes körűen jegyezze fel a kísérlet futtatását.

> [!TIP]
> A kísérlet ismétlésének dokumentálása érdekében minden egyes futtatásakor módosíthatja a címet, a Tulajdonságok ablaktáblán frissítheti a kísérlet **összegzését** , és hozzáadhat vagy frissíthet megjegyzéseket az egyes modulokhoz a módosítások rögzítéséhez. A rendszer menti a cím, az összefoglalás és a modul megjegyzéseit a kísérlet minden egyes futtatásával.
> 
> 

Machine Learning Studio (klasszikus) **kísérletek** lapján a kísérletek listája mindig megjeleníti a kísérlet legújabb verzióját. Ha a kísérlet előző futtatását ( **korábbi Futtatás** vagy **MEGTEKINTÉS futtatási előzmények**használatával) nyitja meg, akkor a Piszkozat verzióra való VISSZATÉRÉShez kattintson a **futtatási előzmények megtekintése** elemre, és válassza ki a **szerkeszthető** **állapotú** iterációt.

## <a name="iterating-on-a-previous-run"></a>Iteráció egy korábbi futtatáskor
Ha a korábbi **Futtatás** vagy a **futtatási előzmények megtekintése** elemre kattint, és megnyit egy előző futtatást, megtekintheti a befejezett kísérletet írásvédett módban.

Ha meg szeretné kezdeni a kísérlet iterációját, ahogy azt az előző futtatáshoz konfigurálta, akkor nyissa meg a futtatást, és kattintson a **Mentés másként**lehetőségre. Ez létrehoz egy új kísérletet, új címmel, üres futtatási előzményekkel, valamint az előző Futtatás összes összetevőjét és paraméterét. Ez az új kísérlet a Machine Learning Studio (klasszikus) Kezdőlap **kísérletek** lapján található, és módosíthatja és futtathatja a kísérlethez szükséges új futtatási előzményeket. 

Tegyük fel például, hogy a kísérlet futtatási előzményei az előző szakaszban láthatók. Szeretné megfigyelni, mi történik, ha a képzési **sebesség** paraméterét 0,4-ra állítja be, és különböző értékeket próbál meg használni a **Training epochs** paraméter számára.

1. Kattintson a **futtatási előzmények megtekintése** elemre, és nyissa meg az 4:28:36 órakor futtatott kísérlet iterációját (amelyben a paraméter értékét 0,4-re állítja).
2. Kattintson **a Mentés másként**elemre.
3. Adjon meg egy új címet, és kattintson az **OK pipa gombra** . Létrejön a kísérlet új példánya.
4. A **képzési korszakok paraméter számának** módosítása.
5. Kattintson a **Futtatás**gombra.

Most már továbbra is módosíthatja és futtathatja a kísérlet ezen verzióját, és felépítheti az új futtatási előzményeket a munka rögzítéséhez.

<!-- Module References -->
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
