---
title: A gépi tanulási modellek tisztaságának felmérése és enyhítése
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogy a Fairlearn Python-csomag hogyan hozhat létre tisztességesebb modelleket, és hogyan segíthet a Fair-modellek kiépítésében.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 05/02/2020
ms.openlocfilehash: c21ec0329a7b5716a00262b7422296df3afe208b
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598604"
---
# <a name="fairness-in-machine-learning-models"></a>Méltányosság a gépi tanulási modellekben

Ismerje meg a tisztességes működést a gépi tanulásban, valamint azt, hogy a Fairlearn nyílt forráskódú Python-csomag segítségével hogyan hozhat létre igazságosabb modelleket.

## <a name="what-is-fairness-in-machine-learning-systems"></a>Mi a tisztességes a gépi tanulási rendszerekben?

A mesterséges intelligencia és a gépi tanulási rendszerek tisztességtelen viselkedést tudnak megjeleníteni. A tisztességtelen viselkedés meghatározásának egyik módja a sérülése, vagy a felhasználókra gyakorolt hatás. A mesterséges intelligencia-rendszerek számos fajta kárt okozhatnak. A mesterséges intelligencia által okozott károk két gyakori típusa:

- Kiosztási kár: egy AI-rendszer kiterjeszti vagy visszatartja a lehetőségeket, erőforrásokat vagy információkat. Ilyenek például a bérbeadás, az iskolai beléptetések és a hitelnyújtás, ahol a modell sokkal jobb lehet, ha a jó jelöltek kiválogatása egy adott csoportba tartozik, mint az egyéb csoportok között.

- A szolgáltatás minőségének károsodása: az AI-rendszerek nem működnek megfelelően egy csoport számára, mint egy másik személy. Előfordulhat például, hogy a hangfelismerő rendszer nem tud működni a nők számára, mint a férfiaknál.

Az AI-rendszerek tisztességtelen viselkedésének csökkentése érdekében fel kell mérnie és csökkentenie kell ezeket a károkat.

>[!NOTE]
> A méltányosság egy szocio-technikai kérdés. A méltányosság számos aspektusa, például az igazságszolgáltatás és a megfelelő folyamat nem rögzíti a mennyiségi korrektség mérőszámait. Emellett számos mennyiségi korrektség mérőszáma nem lehet egyszerre kielégítve. A cél az, hogy lehetővé tegye az emberek számára a különböző kockázatcsökkentő stratégiák kiértékelését, majd a forgatókönyvnek megfelelő kompromisszumok elvégzését.

## <a name="fairness-assessment-and-mitigation-with-fairlearn"></a>A méltányosság felmérése és enyhítése a Fairlearn

A Fairlearn egy nyílt forráskódú Python-csomag, amely lehetővé teszi, hogy a gépi tanulási rendszerek fejlesztői felmérjék a rendszereik tisztaságát, és enyhítsék a megfigyelt tisztességgel kapcsolatos problémákat.

A Fairlearn két összetevőből áll:

- Assessment irányítópult: egy Jupyter notebook widget annak értékeléséhez, hogy a modell előrejelzései hogyan befolyásolják a különböző csoportokat. Emellett lehetővé teszi több modell összehasonlítását a méltányosság és a teljesítmény mérőszámok használatával.
- Enyhítő algoritmusok: olyan algoritmusok összessége, amelyek a bináris besorolásban és a regresszióban csökkentik a méltánytalanság enyhítését.

Ezek az összetevők együtt lehetővé teszik az adatszakértők és az üzleti vezetők számára, hogy a méltányosság és a teljesítmény között bármilyen kompromisszumot keressenek, és kiválassza az igényeiknek leginkább megfelelő kockázatcsökkentő stratégiát.

## <a name="fairness-assessment"></a>Méltányosság felmérése

A Fairlearn-ben a méltányosság fogalma a **csoport méltányosnak**tekinthető, amely megkérdezi, hogy mely személyeknek van kockázata az ártalmak felderítésére?

A kapcsolódó csoportok, más néven alpopulációk, **bizalmas szolgáltatásokkal** vagy bizalmas attribútumokkal vannak meghatározva. A bizalmas funkciók átadása egy Fairlearn kalkulátorként, vagy egy nevű mátrixként történik `sensitive_features` . A kifejezés azt sugallja, hogy a rendszertervezőnek érzékenynek kell lennie ezekre a funkciókra a csoport tisztességének értékelésekor. Fontos szem előtt tartva, hogy ezek a funkciók személyes azonosításra alkalmas adatok miatt tartalmaznak-e adatvédelmi szempontokat. A "bizalmas" szó azonban nem jelenti azt, hogy ezek a funkciók nem használhatók előrejelzések készítésére.

Az értékelési fázisban a méltányosság mértékét a különbségek mérőszámai határozzák meg. A különbségi **mérőszámok** kiértékelik és összehasonlítják a modell viselkedését a különböző csoportok között, akár arányként, akár különbségként. A Fairlearn a különbségi mérőszámok két osztályát támogatja:


- A modell teljesítményének eltérése: ezek a mérőszámok a kiválasztott teljesítménymutató értékeit határozzák meg a különböző alcsoportokon belül. Néhány példa:

  - eltérés a pontossági arányban
  - eltérés a hibák arányában
  - különbség a precizitásban
  - eltérés a visszahívás során
  - a különbség a MAE-ben
  - sokan mások

- Eltérés a kiválasztási arányban: Ez a metrika a különböző alcsoportok közötti különbségeket tartalmazza a kiválasztási arányban. Erre példa a hitelek jóváhagyási arányának eltérése. A kiválasztási arány azt jelenti, hogy az egyes osztályok datapoints frakciója 1 (bináris besorolás) vagy előrejelzési értékek eloszlása (regresszióban).

## <a name="unfairness-mitigation"></a>Méltánytalanság enyhítése

### <a name="parity-constraints"></a>Paritásos megkötések

A Fairlearn számos tisztességtelen megoldási algoritmust tartalmaz. Ezek az algoritmusok több korlátozást is támogatnak a prediktív viselkedés **paritásos megkötések** vagy feltételek szerint. A paritásos megkötések esetében a prediktív viselkedés bizonyos aspektusait össze kell hasonlítani az érzékeny funkciók által definiált csoportok között (például különböző fajok). A Fairlearn enyhítő algoritmusai az ilyen paritásos korlátozásokkal csökkentik a megfigyelt tisztességgel kapcsolatos problémákat.

A Fairlearn a következő típusú paritásos korlátozásokat támogatja:

|Paritásos megkötés  | Cél  |Gépi tanulási feladat  |
|---------|---------|---------|
|Demográfiai paritás     |  A foglalási károsodások enyhítése | Bináris besorolás, regresszió |
|Equaled odds  | Kiosztási és szolgáltatásminőség-károsodások diagnosztizálása | Bináris besorolás        |
|Kötött csoport elvesztése     |  A szolgáltatás minőségi károsodásának enyhítése | Regresszió |

### <a name="mitigation-algorithms"></a>Enyhítő algoritmusok

A Fairlearn biztosítja a utófeldolgozó, és csökkenti a méltánytalanság enyhítésének algoritmusait:

- Csökkentés: ezek az algoritmusok standard Black Box ML-es kalkulátort (pl. LightGBM modellt) hoznak létre, és újraképzésen alapuló modelleket állítanak elő, amelyek újrasúlyozott betanítási adatkészletek sorozatával rendelkeznek. Előfordulhat például, hogy egy bizonyos nemhez tartozó pályázók súlyozása vagy csökkentése a modellek újratanítása és a különböző nemi csoportok közötti egyenlőtlenségek csökkentése érdekében. A felhasználók ezután olyan modellt választhatnak, amely a lehető legjobb kompromisszumot biztosítja a pontosság (vagy más teljesítmény mérőszáma) és a különbség között, ami általában üzleti szabályokon és költségszámításokon alapul.  
- Utómunka: ezek az algoritmusok egy meglévő besorolást és az érzékeny funkciót is felhasználják bemenetként. Ezután származtatják az osztályozó előrejelzését, hogy kikényszerítsék a megadott méltányos korlátozásokat. A küszöbérték-optimalizálás legnagyobb előnye az egyszerűség és a rugalmasság, mivel nem kell átképeznie a modellt. 

| Algoritmus | Leírás | Gépi tanulási feladat | Bizalmas funkciók | Támogatott paritásos megkötések | Algoritmus típusa |
| --- | --- | --- | --- | --- | --- |
| `ExponentiatedGradient` | A Fair besorolásra vonatkozó, a [méltányos](https://arxiv.org/abs/1803.02453) besoroláshoz | Bináris besorolás | Kategorikus | [Demográfiai paritás](#parity-constraints), [equald odds](#parity-constraints) | Csökkentése |
| `GridSearch` | A [valós besorolás csökkentési megközelítésében](https://arxiv.org/abs/1803.02453) leírt fekete négyzetes megközelítés| Bináris besorolás | Bináris | [Demográfiai paritás](#parity-constraints), [equald odds](#parity-constraints) | Csökkentése |
| `GridSearch` | A fekete Box megközelítése, amely a valós regressziós teljesítmény-keresés változatát valósítja meg a [valós regresszió: mennyiségi definíciók és a redukáló algoritmusok](https://arxiv.org/abs/1905.12843) által leírt, kötött csoportok elvesztésének algoritmusával. | Regresszió | Bináris | [Kötött csoport elvesztése](#parity-constraints) | Csökkentése |
| `ThresholdOptimizer` | Utófeldolgozó algoritmus a [felügyelt tanulásban az esélyegyenlőségi lehetőség](https://arxiv.org/abs/1610.02413)alapján. Ez a módszer egy meglévő osztályozó és a bizalmas szolgáltatás bemenetét veszi figyelembe, és az osztályozó előrejelzésének monoton átalakítását származtatja a megadott paritásos megkötések érvénybe léptetéséhez. | Bináris besorolás | Kategorikus | [Demográfiai paritás](#parity-constraints), [equald odds](#parity-constraints) | Feldolgozás utáni |

## <a name="next-steps"></a>További lépések

- A különböző összetevők használatának megismeréséhez tekintse meg a [Fairlearn GitHub-tárházat](https://github.com/fairlearn/fairlearn/) és a [minta jegyzetfüzeteket](https://github.com/fairlearn/fairlearn/tree/master/notebooks).
- További információ az adatvédelem megőrzéséről [a differenciált adatvédelem és a WhisperNoise-csomag](concept-differential-privacy.md)használatával.