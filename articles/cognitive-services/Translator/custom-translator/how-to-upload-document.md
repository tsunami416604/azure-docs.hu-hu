---
title: Hogyan töltsünk fel egy dokumentumot - Egyéni fordító
titleSuffix: Azure Cognitive Services
description: A dokumentumfeltöltési funkció párhuzamos dokumentumokat tölt fel a szolgáltatásba (két dokumentum, ahol az egyik az eredet, a másik pedig a fordítás).
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: b2a249a40d8c782d54a12df43d33655f3409753c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647382"
---
# <a name="upload-a-document"></a>Dokumentum feltöltése

Az [Egyéni fordító](https://portal.customtranslator.azure.ai)ban párhuzamos dokumentumokat tölthet fel a fordítási modellek betanításához. [A párhuzamos dokumentumok](what-are-parallel-documents.md) olyan dokumentumok párjai, amelyek közül az egyik a másik fordítása. A pár egyik dokumentuma a forrásnyelven lévő mondatokat, a másik pedig ezeket a mondatokat a célnyelvre fordította.

A dokumentumok feltöltése előtt tekintse át a [dokumentumformátumokat és az elnevezési konvenciókra vonatkozó útmutatást,](document-formats-naming-convention.md) és győződjön meg arról, hogy a fájlformátum ot támogatja az Egyéni fordító.

## <a name="how-to-upload-document"></a>Hogyan lehet feltölteni a dokumentumot?

Az [Egyéni fordító](https://portal.customtranslator.azure.ai) portálon kattintson a "Dokumentumok" fülre a dokumentumok laphoz.

![Dokumentum feltöltési hivatkozása](media/how-to/how-to-upload-1.png)


1.  Kattintson a Dokumentumok oldal Fájlok feltöltése gombjára.

    ![Dokumentumoldal feltöltése](media/how-to/how-to-upload-2.png)

2.  A párbeszédpanelen töltse ki a következő adatokat:

    a.  Dokumentum típusa:

    -  Oktatás: Ezek a dokumentumok(ok) lesznek használva a képzési készlet.
    -  Hangolás: Ezek a dokumentum(oka)t fogja használni a hangolási készlet.
    -  Tesztelés: Ezek a dokumentumok a tesztkészlethez lesznek használva.
    -  Kifejezés szótár: Ezek a dokumentumok(ak) kifejezésszótárhoz lesznek használva.
    -  Mondatszótár: Ezek a dokumentumok a mondatszótárban lesznek használva

    b.  Nyelvi pár

    c.  Dokumentum felülírása, ha létezik: Jelölje be ezt a jelölőnégyzetet, ha felül szeretné írni az azonos nevű meglévő dokumentumokat.

    d.  Töltse ki a megfelelő szakaszt a párhuzamos adatok vagy a kombinált adatok szempontjából.

    -  Párhuzamos adatok:
        -  Forrásfájl: Válassza ki a forrásnyelvi fájlt a helyi számítógépről.
        -  Célfájl: Válassza ki a célnyelvi fájlt a helyi számítógépről.
        -  Dokumentumneve: Csak akkor használatos, ha párhuzamos fájlokat tölt fel.

    - Kombinált adatok:
        -  Kombinált fájl: Válassza ki a kombinált fájlt a helyi számítógépről. A kombinált fájl ban mind a forrás-, mind a célnyelvi mondatok vannak. [Az elnevezési konvenció](document-formats-naming-convention.md) fontos a kombinált fájlok esetében.

    e.  Kattintson a Feltöltés gombra

    ![Dokumentum feltöltése párbeszédpanel](media/how-to/how-to-upload-dialog.png)

3.  Ezen a ponton feldolgozzuk a dokumentumait, és megpróbáljuk kinyerni a mondatokat. A "Feltöltési folyamat megtekintése" gombra kattintva ellenőrizheti a dokumentumok állapotát a feldolgozás során.

    ![Dokumentumfeldolgozás feltöltése párbeszédpanel](media/how-to/how-to-upload-processing-dialog.png)

4.  Ezen az oldalon megjelenik az állapot, és az esetleges hibák minden fájlt a feltöltés. A feltöltési állapotot bármikor megtekintheti a "Feltöltési előzmények" fülre kattintva.

    ![Dokumentumelőzmények feltöltése párbeszédpanel](media/how-to/how-to-upload-document-history.png)


## <a name="view-upload-history"></a>Feltöltési előzmények megtekintése

A feltöltési előzmények oldalon megtekintheti az összes dokumentumfeltöltési adat előzményeit, például a dokumentum típusát, a nyelvpárt, a feltöltési állapotot stb.

1. Az [Egyéni fordító](https://portal.customtranslator.azure.ai) portálon kattintson az Előzmények feltöltése fülre az előzmények megtekintéséhez.

    ![Az előzmények feltöltése lap](media/how-to/how-to-upload-history-1.png)

2. Ezen az oldalon az összes korábbi feltöltés állapota látható. Megjeleníti a feltöltéseket a legutóbbitól a legkevésbé frissig. Minden feltöltésnél megjelenik a dokumentum neve, a feltöltési állapot, a feltöltés dátuma, a feltöltött fájlok száma, a feltöltött fájl típusa és a fájl nyelvi párja.

    ![Az előzmények feltöltése lap](media/how-to/how-to-document-history-2.png)

3. Kattints on any upload history record. A feltöltési előzmények részleteit oldalon megtekintheti a feltöltött fájlokat a feltöltés, a fájl feltöltött állapota, a fájl nyelve és a hibaüzenet részeként (ha hiba van a feltöltésben).

## <a name="next-steps"></a>További lépések

- A [dokumentum részletei lapon](how-to-view-document-details.md) áttekintheti a kibontott mondatok listáját.
- [Hogyan kell a vonat egy modell](how-to-train-model.md).
