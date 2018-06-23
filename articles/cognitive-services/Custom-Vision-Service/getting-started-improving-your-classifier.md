---
title: Az egyéni stratégiai szolgáltatás - kognitív Azure-szolgáltatások használatával osztályozó javítása |} Microsoft Docs
description: Útmutató az egyéni stratégiai szolgáltatás osztályozó minőségének javítása érdekében.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 65b1424f259066c7d5bd6b2b508d2a4052ff0527
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347866"
---
# <a name="how-to-improve-your-classifier"></a>Hogyan lehet fokozni a osztályozó

Útmutató az egyéni stratégiai szolgáltatás osztályozó minőségének javítása érdekében. A besorolás minőségének a címkével ellátott adatok, majd azt való minőségének függ. 

## <a name="train-more-varied-images"></a>A vonat változatosabb lemezképek

Címkézett képek biztosítható a különböző szögek, háttér, objektumok mérete, fényképek csoportokat és más Variant típusú adatok javítja a osztályozó. Fényképek környezetben jobb, mint fényképek semleges hátterek elé. Például a lemezképbe, amelyek képviselője mit kell terjeszteni a osztályozó normál használat során.

Képek hozzáadásával kapcsolatos további információkért lásd: a [besorolás Build](getting-started-build-a-classifier.md) dokumentum.

> [!IMPORTANT]
> Ne felejtse el a osztályozó betanítása lemezképek hozzáadása után.

## <a name="use-images-submitted-for-prediction"></a>A lemezképeket előrejelzés elküldése megtörtént

Az egyéni stratégiai szolgáltatás tárolja a előrejelzés végpontnak küldött kép. Ezek a lemezképek használatával javíthatja az osztályozó által igénybe, használja az alábbi lépéseket:

1. Az osztályozó számára küldött kép megtekintéséhez nyissa meg a [egyéni stratégiai weblap](https://customvision.ai) válassza ki a __előrejelzéseket__ fülre.

    ![Az előrejelzés lap képe](./media/getting-started-improving-your-classifier/predictions-tab.png)

    > [!TIP]
    > Az alapértelmezett nézet lemezképet rögzíthet a jelenlegi iterációs jeleníti meg. Használhatja a __iterációs__ legördülő mező az előző lépés során küldött kép megtekintéséhez.

2. Egy képre az osztályozó által előrejelzett volt címkéket mutasson.

    > [!TIP]
    > Lemezképek rangsora, hogy a képek, amely a legtöbb előnyökre is kerüljön a osztályozó tetején. Válassza ki, eltérő rendezést, használja a __rendezési__ szakasz.

    Kép felvétele a betanítási adatok, válassza ki a lemezképet, jelölje ki a címke, és válassza ki __mentse és zárja be__. Eltávolítja a lemezképet __előrejelzéseket__ és a képzési lemezképekhez hozzá. Kiválasztásával megtekintheti a __képzési képek__ fülre.

    ![A címkézési lapjának képe](./media/getting-started-improving-your-classifier/tag-image.png)

3. Használja a __vonat__ gombra kattintva a osztályozó működik.

## <a name="visually-inspect-predictions"></a>Vizuálisan ellenőrizze az előrejelzés

Vizsgálja meg a lemezkép előrejelzéseket, válassza ki a __képzési képek__ fülre, majd jelölje __iterációs előzmények__. A vörös téglalappal vázolt képek helytelenül volt előre.

![Az iterációs előzmények képe](./media/getting-started-improving-your-classifier/iteration-history.png)

Néha visual hálózatfelügyeleti azonosíthatja a minta, majd javíthatja ki további betanítási adatok hozzáadásával. Például egy osztályozó rózsa daises és a lehetséges, hogy helytelenül címke minden fehér rózsa daises. Előfordulhat, hogy a probléma elhárításához hozzáadása, és fehér rózsa címkézett képeit tartalmazó betanítási adatok megadásával.

## <a name="unexpected-classification"></a>Váratlan besorolás

Egyes esetekben az osztályozó Tanulja meg, hogy a képek rendelkezik közös jellemzőkkel. Például szeretné egy rózsa tulips és a-osztályozó létrehozása. Lemezképek tulips mezőket, és a kék fali elé egy piros vase rózsa megadnia. Megadott ezeket az adatokat, a osztályozó előfordulhat, hogy betanítása mező és fali + vase és tulips rózsa helyett.

A probléma megoldásához útmutatás használja több képzési eltérőek a képek: Adja meg a lemezképek különböző szögek, háttér, objektumok mérete, csoportok és más Variant adattípusban.

## <a name="negative-image-handling"></a>Negatív kép kezelése

Az egyéni stratégiai szolgáltatás néhány automatikus negatív kép kezelését támogatja. Ha egy cat és kutya osztályozó fejleszt, és egy cipő előrejelzés kép küldése, az osztályozó kell pontszám, megközelíti a 0 % lemezkép cat és kutya is. 

> [!WARNING]
> Az automatikus módszer egyértelműen negatív lemezképek működik. Nem működnek jól olyan esetekben, ahol a negatív képek a képek képzési szerepel egy változata. 
>
> Például ha egy husky és corgi osztályozó, és Ön egy Pomeranian lemezképet a hírcsatorna, akkor előfordulhat, hogy pontszám a Pomeranian egy Husky. Ha a negatív képek ilyen jellegű, hozzon létre egy új címke (például az "egyéb"), és alkalmazza azt az a negatív képzési képek.

## <a name="next-steps"></a>További lépések

[Az előrejelzés API használata](use-prediction-api.md)