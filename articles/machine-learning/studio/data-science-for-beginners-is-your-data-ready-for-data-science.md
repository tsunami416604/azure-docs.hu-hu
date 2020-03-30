---
title: Adatok értékelése
titleSuffix: ML Studio (classic) - Azure
description: Négy feltétel, amelynek az adatoknak meg kell felelniük ahhoz, hogy készen álljanak az adatelemzésre. Ez a videó konkrét példákat, hogy segítsen az alapvető adatok kiértékelése.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: ccc422dfb3105fd1e12569a84a4ebfd22182b225
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73837802"
---
# <a name="is-your-data-ready-for-data-science"></a>Készen állnak adatai az elemzésre?
## <a name="video-2-data-science-for-beginners-series"></a>2. videó: Adattudomány kezdőknek sorozat
Ismerje meg, hogyan értékelheti ki az adatokat, hogy megbizonyosodjon arról, hogy megfelel az alapvető feltételeknek, hogy készen áll az adatelemzésre.

Ahhoz, hogy a legtöbbet hozza ki a sorozat, nézd meg őket. [Ugrás a videók listájára](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/9/player]
>
>

## <a name="other-videos-in-this-series"></a>Egyéb videók ebben a sorozatban
*Data Science kezdőknek* egy gyors bevezetés az adattudomány öt rövid videók.

* 1. videó: [Az 5 kérdés adatelemzési válaszok](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 perc 14 mp)*
* 2. videó: Készen áll az adatok az adatelemzésre?
* 3. videó: [Olyan kérdés feltehet ő: adatokkal válaszolhat](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 perc 17 mp)*
* 4. videó: [Válasz előrejelzése egy egyszerű modellel](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 perc 42 mp)*
* 5. videó: [Mások munkájának másolása adatelemzéshez](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 perc 18 mp)*

## <a name="transcript-is-your-data-ready-for-data-science"></a>Átirat: Az adatok készen állnak az adatelemzésre?
Üdvözli a "Készen állnak az adatok az adatelemzésre?" című A második videó a sorozat *Data Science kezdőknek*.  

Mielőtt az adattudomány megadná a kívánt válaszokat, meg kell adnia néhány kiváló minőségű nyersanyagot, amelyekkel dolgozhat. Csakúgy, mint hogy egy pizza, annál jobb az összetevők kezdeni, annál jobb a végtermék. 

## <a name="criteria-for-data"></a>Az adatokra vonatkozó kritériumok
Az adattudomány, vannak bizonyos összetevők, amelyeket össze kell húzni, beleértve:

* Vonatkozó
* Csatlakozva
* Pontos
* Elég ahhoz, hogy

## <a name="is-your-data-relevant"></a>Relevánsak-e az adatai?
Tehát az első összetevő - olyan adatokra van szüksége, amelyek relevánsak.

![Releváns adatok és irreleváns adatok – adatok kiértékelése](./media/data-science-for-beginners-is-your-data-ready-for-data-science/relevant-and-irrelevant-data.png)

A bal oldalon, az asztal bemutatja a véralkohol szintje hét ember tesztelt kívül bostoni bárban, a Red Sox szemrebbenés átlag az utolsó játék, és az ár a tej a legközelebbi kisboltban.

Ezek mind teljesen jogos adatok. Az egyetlen hiba, hogy nem lényeges. Nincs nyilvánvaló kapcsolat a számok között. Ha valaki megadta neked a jelenlegi tejárat és a Red Sox ütőátlagot, kizárt, hogy kitaláld a véralkoholtartalmukat.

Most nézd meg a jobb oldali asztalt. Ezúttal minden egyes ember testtömegét, valamint az italok számát mérték.  Az egyes sorokban lévő számok most már relevánsak egymással szemben. Ha megadnám a testtömegemet és a Margarita-k számát, akkor megtudná tippelni a véralkoholtartalmamat.

## <a name="do-you-have-connected-data"></a>Vannak csatlakoztatott adatai?
A következő összetevő a csatlakoztatott adatok.

![Csatlakoztatott adatok és leválasztott adatok - adatfeltételek, adatkész](./media/data-science-for-beginners-is-your-data-ready-for-data-science/connected-vs-disconnected-data.png)

Itt van néhány releváns adat a hamburgerek minőségéről: grill hőmérséklet, patty súly és értékelés a helyi élelmiszer magazinban. De figyeljük meg a hiányosságokat a táblázatban a bal oldalon.

A legtöbb adatkészletből hiányzik néhány érték. Ez a közös, hogy lyukak, mint ez, és vannak olyan módon, hogy a munka körül őket. De ha túl sok hiányzik, az adatok kezd kinézni, mint a svájci sajt.

Ha megnézzük az asztalt a bal oldalon, annyi hiányzó adat van, hogy nehéz bármilyen kapcsolattal előállni a grill hőmérséklete és a patty súlya között. Ez a példa a leválasztott adatokat mutatja.

A jobb oldali táblázat azonban teljes és teljes - egy példa a csatlakoztatott adatokra.

## <a name="is-your-data-accurate"></a>Pontosak az adatai?
A következő összetevő a pontosság. Itt van négy célpont, amit el kell találni.

![Pontos adatok és pontatlan adatok – adatkritériumok](./media/data-science-for-beginners-is-your-data-ready-for-data-science/inaccurate-vs-accurate-data.png)

Nézd meg a célpontot a jobb felső sarokban. Van egy szűk csoportosítás a bika szeme körül. Ez természetesen pontos. Furcsa módon az adattudomány nyelvén a cél alatti teljesítmény is pontosnak tekinthető.

Ha feltérképezi a nyilak közepét, látná, hogy nagyon közel van a bikák szeméhez. A nyilak szétszóródtak a célpont körül, így pontatlannak számítanak, de a bikák szeme köré vannak fordítva, így pontosnak tekinthetők.

Most nézd meg a bal felső célpontot. Itt a nyilak hit nagyon közel egymáshoz, egy szűk csoportosítás. Pontosak, de pontatlanok, mert a középpont messze van a bikák szemétől. A bal alsó célpontnyilak pontatlanok és pontatlanok. Ennek az íjásznak több gyakorlásra van szüksége.

## <a name="do-you-have-enough-data-to-work-with"></a>Van elég adata, amivel dolgozhat?
Végül az összetevő #4 elegendő adat.

![Van elég adataz elemzéshez? Adatok értékelése](./media/data-science-for-beginners-is-your-data-ready-for-data-science/barely-enough-data.png)

A táblázat egyes adatpontjaira úgy gondoljon, mint egy ecsetvonásra egy festményen. Ha csak néhány van belőlük, a festmény homályos lehet - nehéz megmondani, hogy mi az.

Ha még több ecsetvonást ad hozzá, akkor a festmény egy kicsit élesebb lesz.

Ha alig van annyi stroke, akkor csak látni ahhoz, hogy néhány széles körű döntéseket. Talán meg akarom látogatni? Úgy néz ki, fényes, hogy úgy néz ki, mint a tiszta víz - igen, ez az, ahol megyek nyaralni.

Ahogy további adatokat ad hozzá, a kép világosabb lesz, és részletesebb döntéseket hozhat. Most megnézheted a bal parton lévő három szállodát. Az előtérben lévő építészeti jellemzőit is láthatja. Lehet, hogy még úgy dönt, hogy felfüggeszti a harmadik emeleten, mert a kilátás.

Releváns, összekapcsolt, pontos és elegendő adatokkal rendelkezik a kiváló minőségű adatelemzéshez szükséges összes összetevővel.

Ügyeljen arra, hogy tekintse meg a másik négy videó *adattudomány kezdőknek a* Microsoft Azure Machine Learning Studio (klasszikus).

## <a name="next-steps"></a>További lépések
* [Próbálja ki az első adatelemzési kísérletet a Machine Learning Studio-val (klasszikus)](create-experiment.md)
* [Bevezetés a Microsoft Azure-alapú gépi tanulásba](/azure/machine-learning/preview/overview-what-is-azure-ml)
