---
title: Kapcsolat kiértékelése – Personalizer
titleSuffix: Azure Cognitive Services
description: A learning ciklus egy offline próbaverziójának elemzése
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: b719e6e693471415350007a4f4fabed917b8e12d
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722315"
---
# <a name="how-to-analyze-your-learning-loop-with-an-offline-evaluation"></a>A learning ciklus egy offline próbaverziójának elemzése


Ismerje meg, hogyan hajtsa végre a kapcsolat nélküli próbaverzióját, és az eredmények értelmezésében.

Kapcsolat nélküli értékelések engedélyezése mérhető hatékony Personalizer a rendszer összehasonlítja az alkalmazás alapértelmezett viselkedését, ismerje meg, milyen szolgáltatások legtöbb személyre szabás működik közre, és Fedezze fel az új machine learning beállítások automatikusan.

További információ [Offline értékelések](concepts-offline-evaluation.md) további.


## <a name="prerequisites"></a>Előfeltételek

1. Rendelkeznie kell konfigurált Personalizer hurok
1. A Personalizer hurok kell rendelkeznie legalább 50 000 események jelentéssel bíró kiértékelésének eredménye a naplókat.

Szükség esetén előfordulhat, hogy is korábban exportált _házirend tanulási_ fájlok ugyanazon kiértékelésekor tesztelése és összehasonlítása.

## <a name="steps-to-start-a-new-offline-evaluation"></a>A lépéseket egy új kapcsolat nélküli értékelés indítása

1. Az Azure Portalon keresse meg a személyre szabás hurok erőforrás.
1. Keresse meg az "Értékelés" szakaszban.
1. Kattintson az új értékelés
1. Válassza ki az offline értékelés egy kezdő és záró dátumát. Ezek a múltbeli időpont, adja meg az adatokat, a tartományát kiértékelésekor dátumokat. Ezeket az adatokat a naplókban, a jelen kell lennie a [adatmegőrzés](how-to-settings.md) beállítás.
1. Igény szerint tölthet fel a saját learning házirend. 
1. Adja meg e Personalizer a megfigyelt ebben az időszakban a felhasználói viselkedés alapján optimalizált Learning szabályzatot kell létrehozni.
1. Az értékelés indítása

## <a name="results"></a>Results (Eredmények)

Értékelések szeretne futtatni, az adatok feldolgozásához, tanulási összehasonlítására, a szabályzatok száma az igényelt kreditmennyiség függvényében hosszú időt vehet igénybe, és a egy optimalizálási kért e.

Ha befejeződött, a következő eredményeket tekintheti meg:

1. Összehasonlítások Learning házirendek, többek között:
    * **Online-szabályzat**: Az aktuális Personalizer használt Learning házirend
    * **Alapkonfiguráció**: Az alkalmazás alapértelmezett (határoz meg az első művelet rangsorolják hívásokat küld),
    * **Véletlenszerű házirend**: Egy képzeletbeli rangsorolják viselkedés, amely mindig visszaadja véletlenszerű művelet közül választhat a megadott állók közül.
    * **Egyéni szabályzatok**: További tanulási házirendek töltött fel, az értékelés indítása során.
    * **Optimalizált házirend**: Ha az értékelés felderíteni egy optimalizált házirend kapcsolóval lett elindítva, azt is lesz összehasonlítva, és lesz letöltheti, vagy adja meg az online tanulási szabályzatot, és cserélje le a jelenlegivel.

1. Hatékonyságát [funkciók](concepts-features.md) műveletek és a környezet számára.


## <a name="more-information"></a>További információ

* Ismerje meg, [offline hogyan működnek a értékelések](concepts-offline-evaluation.md).