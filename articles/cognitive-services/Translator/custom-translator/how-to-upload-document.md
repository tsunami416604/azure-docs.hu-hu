---
title: A dokumentum - egyéni a fordítót feltöltése
titleSuffix: Azure Cognitive Services
description: A dokumentum a feltöltési funkcióval a betanítások a párhuzamos dokumentum is feltölthet. Párhuzamos dokumentumok párjai dokumentumok ahol egyik, a másik a fordítás. A pár egyik dokumentum tartalmaz a Forrásnyelv mondatok és a többi dokumentum tartalmazza ezeket a Célnyelv fordítja mondatokat.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: bb84a3ddfba54c47cdf91aa72889a960722cdbd3
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51627431"
---
# <a name="upload-a-document"></a>Dokumentum feltöltése

A [egyéni a fordítót](https://portal.customtranslator.azure.ai), a fordítási modelleket taníthat be párhuzamos dokumentumokat feltölthet. [Dokumentumok párhuzamos](what-are-parallel-documents.md) párjai dokumentumok egy fordítását, a másik helyére. A pár egyik dokumentum tartalmaz a Forrásnyelv mondatok és a többi dokumentum tartalmazza ezeket a Célnyelv fordítja mondatokat.

## <a name="how-to-upload-document"></a>Dokumentum feltöltése hogyan?

A [egyéni a fordítót](https://portal.customtranslator.azure.ai) portálon kattintson a "Dokumentumok" lapon dokumentumok lap megnyitásához.

![Dokumentum feltöltése hivatkozása](media/how-to/how-to-upload-1.png)


1.  Kattintson a feltöltés fájlok gombra a dokumentumok oldalon.

    ![Töltse fel a dokumentum lap](media/how-to/how-to-upload-2.png)

2.  Töltse ki az alábbi adatokat a párbeszédpanelen:

    a.  A dokumentum típusa:

    -  Képzés: Ezek dokumentum(ok) gyakorlókészlethez használandó.
    -  Hangolási: Ezek dokumentum(ok) használható set finomhangoláshoz.
    -  Az ellenőrzési: Ezek dokumentum(ok) tesztelési set lesz.
    -  A kifejezés szótár: Ezek dokumentum(ok) kifejezés szótár lesz.
    -  Mondat szótár: Ezek dokumentum(ok) jelzi a mondat szótár

    b.  Nyelvi pár

    c.  Dokumentum felülírása, ha létezik: válassza ezt a jelölőnégyzetet, ha felülírja a meglévő dokumentumok ugyanazzal a névvel.

    d.  Töltse ki a megfelelő szakaszban párhuzamos vagy kombinált adatok.

    -  Párhuzamos adatok:
        -  Forrásfájl: Select Forrásnyelv fájlt a helyi számítógépről.
        -  Célfájl: válassza ki a célnyelvet fájlt a helyi számítógépről.
        -  Dokumentum neve: csak akkor, ha a feltöltendő párhuzamos fájlokat használja.

    - Kombinált adatok:
        -  Kombinált fájl: Jelölje ki a kombinált fájlt a helyi számítógépen. A kombinált fájlban vannak, mind a forrás és cél nyelvi mondatokat. [Elnevezési szabályainak](document-formats-naming-convention.md) kombinált fájlok számára fontos.

    e.  Kattintson a feltöltés

    ![Töltse fel a dokumentum párbeszédpanel](media/how-to/how-to-upload-dialog.png)

3.  Ezen a ponton folyamatban van a dokumentumok feldolgozása és kinyerése a mondatok tett kísérlet. "View feltöltése folyamatban" gombra kattint, azok feldolgozni a dokumentumok állapotának ellenőrzéséhez.

    ![Töltse fel a dokumentum feldolgozási párbeszédpanel](media/how-to/how-to-upload-processing-dialog.png)

4.  Ezt oldal jelenik meg, az állapota, és az esetleges hibákat a feltöltés belül minden fájlhoz. Megtekintheti korábbi feltöltési állapotot bármikor a "Feltöltése előzmények" fülre kattintva.

    ![Töltse fel a dokumentum korábbi párbeszédpanel](media/how-to/how-to-upload-document-history.png)


## <a name="view-upload-history"></a>Feltöltés előzményeinek megtekintése

Feltöltés Előzmények lapon megtekintheti a részleteket, például az összes dokumentum feltöltések előzményeit a dokumentum típusa, nyelvi pár, feltöltése stb. állapotát.

1. Az a [egyéni a fordítót](https://portal.customtranslator.azure.ai) portálon feltöltése Előzmények lapon előzményeinek megtekintése.

    ![Töltse fel az Előzmények lapon](media/how-to/how-to-upload-history-1.png)

2. Ezen a lapon minden az elmúlt feltöltések állapotát jeleníti meg. Feltöltések megjeleníti a legutóbbi, a legrégebbi. Minden feltöltött azt mutatja be, a dokumentum neve, feltöltési állapotot, a feltöltés dátuma, a feltöltött fájlok száma, feltöltött fájl típusa és a fájl a nyelvi pár.

    ![Töltse fel az Előzmények lapon](media/how-to/how-to-document-history-2.png)

3. Kattintson bármely feltöltési előzményrekordot. Feltöltés előzmények részletei lapon megtekintheti a fájlok feltöltése a feltöltés, a fájl, a fájl- és hiba üzenet (ha hiba történik a feltöltés) nyelvi feltöltött állapotát részeként.

## <a name="next-steps"></a>További lépések

- Használja a [dokumentum Részletek lap](how-to-view-document-details.md) kinyert mondatokat listájának áttekintéséhez.
- [A modell betanításához hogyan](how-to-train-model.md).
