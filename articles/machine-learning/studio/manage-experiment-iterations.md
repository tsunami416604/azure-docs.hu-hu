---
title: A Machine Learning Studióban kísérletismétlések kezelése
titleSuffix: Azure Machine Learning Studio
description: Hogyan kísérlet ismétléseinek kezelése az Azure Machine Learning Studióban. A kísérletek az előző futtatásokat annak érdekében, hogy az ellenőrző kérdés, nyissa meg újra, és végső soron erősítse meg vagy finomíthatja a korábbi feltételezések bármikor tekintheti meg.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 03/20/2017
ms.openlocfilehash: 503a93064b81e0db35c0477a11f2ee525680c854
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55488434"
---
# <a name="manage-experiment-iterations-in-azure-machine-learning-studio"></a>Kísérlet ismétléseinek kezelése az Azure Machine Learning Studióban
Prediktív elemzési modellek fejlesztése iteratív folyamat - módosítása a különböző függvényeket és paramétereket a kísérlet, az eredmények közelítjük, amíg olyan nem egy betanított, hatékony modellel rendelkezünk. Ez a folyamat kulcs a kísérlet paraméterek és konfigurációkkal különböző ismétlésének nyomon követéséhez.



A kísérletek az előző futtatásokat annak érdekében, hogy az ellenőrző kérdés, nyissa meg újra, és végső soron erősítse meg vagy finomíthatja a korábbi feltételezések bármikor tekintheti meg. Amikor kísérletet futtat, a Machine Learning Studio előzményeket a futtatást, beleértve az adatkészlet, a modult, és a port-kapcsolatok és a paraméterek. Az előzményekben is eredményeket, például indítási és leállási idő, naplóüzenetek és végrehajtási állapot runtime adatait rögzíti. Megtekintheti vissza bármely ezek fut, és tekintse át a kísérletet, és a köztes eredményeket kronológiát biztosíthatnak. A kísérlet egy korábbi futtatás segítségével még be egy új lekérdezést és a felderítési fázisa az elérési útra, egyszerű, bonyolult vagy akár ensemble modellezési megoldások létrehozásához indítsa el.

> [!NOTE]
> Egy kísérlet egy korábbi futtatás megtekintésekor az adott verziójával a kísérletvászonra zárolva van, ezért nem szerkeszthető. Azonban mentheti a másolatát kattintva **SAVE AS** és a egy új nevet a másolat. A Machine Learning Studio megnyílik az új példányt, amely majd szerkesztheti, és futtassa. Ez a kísérlet másolat érhető el a **kísérletek** együtt a többi kísérletek a listában.
> 
> 

## <a name="viewing-the-prior-run"></a>A korábbi futtatás megtekintése
Kísérlet nyitott, amelyek legalább egyszer futtatnia kell, ha a fenti Futtatás a kísérlet kattintva megtekintheti **előzetes futtatása** a Tulajdonságok panelen.

Tegyük fel például, a kísérlet létrehozása és futtatása a verziókat, 11:23., 11:42 és 11:55. Ha nyissa meg a legutóbbi futtatás a kísérlet (11:55), és kattintson a **előzetes futtatása**, megnyílik a verzió, 11:42 futtatta.

## <a name="viewing-the-run-history"></a>A futtatási előzmények megtekintése
Kattintva megtekintheti az előző fut, egy kísérlet **futtatási előzmények megtekintése** egy megnyitott kísérlet során.

Tegyük fel például, egy kísérlet létrehozása a [lineáris regressziós] [ linear-regression] modul, és szeretné látni fogja a változó értékét a hatás **tanulási ráta** a a kísérlet eredményeit. Akkor futtathatja a kísérletet többször eltérő értékek ezt a paramétert, a következőképpen:

| Tanulási ráta érték | Futtatás kezdő időpontja |
| --- | --- |
| 0,1 |9/11/2014 du. 4:18:58 |
| 0.2 |9/11/2014 du. 4:24:33 |
| 0.4 |9/11/2014 du. 4:28:36 |
| 0,5 |9/11/2014 du. 4:33:31 |

Ha rákattint **FUTTATÁSI előzmények megtekintése**, minden Futtatás listájának megtekintéséhez:

![A példában a futtatási előzmények][runhistory]

Kattintson bármelyik ezek futtatások megtekintése a kísérlet azt futtatta időben pillanatképét. A konfiguráció, paraméterértékeket, megjegyzések és eredmények összes megmaradnak, hogy a Futtatás a kísérlet egy teljes rekord.

> [!TIP]
> A kísérlet ismétléseinek dokumentálása, módosíthatja a cím minden alkalommal, amikor futtatja, frissítheti a **összegzése** tulajdonságai a kísérlet panelre, és hozzáadhat vagy frissíthet rögzítse az egyes modulok fűzött megjegyzések a módosítások. A cím, Összegzés és modul megjegyzések minden egyes a kísérlet futtatásához együtt lesznek mentve.
> 
> 

A kísérletek a listában a **kísérletek** lapon, a Machine Learning Studióban mindig a legújabb verzióra, egy kísérlet jeleníti meg. Ha megnyitja a kísérlet egy korábbi futtatás (használatával **előzetes futtatása** vagy **FUTTATÁSI előzmények megtekintése**), a vázlatként megjelölt verziót kattintva visszatérhet **FUTTATÁSI előzmények megtekintése** , és válassza a Az iteráció, amely rendelkezik egy **állapot** , **szerkeszthető**.

## <a name="iterating-on-a-previous-run"></a>A korábbi futtatása léptetés
Amikor rákattint **előzetes futtassa** vagy **FUTTATÁSI előzmények megtekintése** és a egy korábbi futtatás nyissa meg, akkor az elkészült kísérlet csak olvasható módban.

Ha azt szeretné, az ismétlések kísérletbe kezdve a korábbi futtatása a konfigurált módon megkezdéséhez, ehhez nyissa meg a Futtatás, és kattintson a **SAVE AS**. Ez egy új kísérlet hoz létre egy új címmel, egy üres futtatási előzmények, és az összetevők és a paraméterértékek az előző futtatható. Ez a kísérlet szerepel-e a **KÍSÉRLETEKET** lap a Machine Learning Studio kezdőlapjára, és módosíthatja, és futtassa, kezdeményezése egy új futtatási előzmények az ismétlések kísérletbe számára. 

Tegyük fel például, a futtatási előzmények az előző szakaszban látható kísérletet. Figyelje meg, mi történik, ha meg szeretné a **tanulási ráta** 0,4, és próbálja meg eltérő értékeket paramétert a **hány betanítási alapidőszakkal** paraméter.

1. Kattintson a **FUTTATÁSI előzmények megtekintése** , és nyissa meg az iteráció a kísérlet, amely futtatta a du. 4:28:36 (amely beállította a paraméter értéke 0,4).
2. Kattintson a **SAVE AS**.
3. Adjon meg egy új címet, majd kattintson a **OK** pipára. A kísérlet egy új példányát jön létre.
4. Módosítsa a **hány betanítási alapidőszakkal** paraméter.
5. Kattintson a **futtatása**.

Most már továbbra is módosíthatja, és ez a kísérlet, a verzió fut egy új futtatási előzmények jegyezze fel a munkahelyi létrehozásához.

<!-- Images -->
[runhistory]:./media/manage-experiment-iterations/viewrunhistory.jpg


<!-- Module References -->
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
