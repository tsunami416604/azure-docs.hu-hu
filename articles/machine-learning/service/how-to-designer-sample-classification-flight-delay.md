---
title: 'Tervező: példa a repülési késleltetés előrejelzésére'
titleSuffix: Azure Machine Learning
description: Osztályozó létrehozása és egyéni R-kód használata a repülési késések előrejelzésére Azure Machine Learning Designer használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 11/04/2019
ms.openlocfilehash: 23b763a69fc0ea3191150c6255cf358d69bc4b73
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74213989"
---
# <a name="build-a-classifier--use-r-to-predict-flight-delays-with-azure-machine-learning-designer"></a>Osztályozó & létrehozása az R használatával a repülési késések előrejelzéséhez Azure Machine Learning Designerben

**Designer (előzetes verzió) 6. minta**

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Ez a folyamat a korábbi repülési és időjárási adatszolgáltatások alapján Jósolja meg, hogy egy ütemezett utasszállító járat késése több mint 15 percet vesz igénybe. Ez a probléma besorolási problémaként is megközelíthető, amely két osztályt jósol: késleltetve vagy időben.

A minta utolsó folyamat gráfja a következő:

[a folyamat ![gráfja](media/how-to-designer-sample-classification-predict-flight-delay/pipeline-graph.png)](media/how-to-designer-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. A megnyitásához kattintson a 6. minta elemre.

## <a name="get-the-data"></a>Az adatok lekérése

Ez a példa a **repülési késések** adatkészletét használja. Része az Egyesült államokbeli Közlekedési Minisztérium TranStats-adatgyűjtési szolgáltatásának. Az adatkészlet az áprilistól október 2013-ig terjedő repülési késleltetési adatokat tartalmaz. Az adatkészlet a következőképpen lett előre feldolgozva:

* Szűrve, hogy tartalmazza a 70 legforgalmasabb repülőteret a kontinentális Egyesült Államok.
* A megszakított járatok újracímkézve 15 percnél hosszabb ideig késleltetve.
* Kiszűrt fordított járatok.
* Kiválasztott 14 oszlop.

A repülési adatokat a rendszer az **időjárási adatkészlet** használatával egészíti ki. Az időjárási adatok óránként, szárazföldön alapuló időjárási megfigyeléseket tartalmaznak a NOAA-tól, és a repülőtéri meteorológiai állomások észrevételeit jelölik, amelyek a járatok adatkészletével megegyező időszakra vonatkoznak. A következőképpen lett előre feldolgozva:

* A meteorológiai állomás azonosítói a megfelelő repülőtéri azonosítóra vannak leképezve.
* Az 70-es legforgalmasabb repülőtérhez nem társított meteorológiai állomások el lettek távolítva.
* A Date (dátum) oszlopot különálló oszlopokra osztottuk: év, hónap és nap.
* Kiválasztott 26 oszlop.

## <a name="pre-process-the-data"></a>Az adatfeldolgozás előkezelése

Az adatkészletek általában valamilyen előzetes feldolgozást igényelnek, mielőtt elemezni lehetne őket.

![adatfeldolgozás](media/how-to-designer-sample-classification-predict-flight-delay/data-process.png)

### <a name="flight-data"></a>Repülési adatcsatorna

A **Carrier**, a **OriginAirportID**és a **DestAirportID** oszlopok egész számként lesznek mentve. Azonban kategorikus attribútumok, a **metaadatok szerkesztése** modullal alakítsa át azokat a kategorikusba.

![metaadatok szerkesztése](media/how-to-designer-sample-classification-predict-flight-delay/edit-metadata.png)

Ezután az adathalmazok **kijelölése** az adatkészlet modulban lehetőséggel kizárhatja azokat az adatkészlet-oszlopokat, amelyek lehetséges célja a szivárgás: **DepDelay**, **DepDel15**, **ArrDelay**, **megszakított**, **év**. 

Ha a repülési rekordokat az óránkénti időjárási adatokkal szeretné csatlakoztatni, használja az ütemezett indulási időt az illesztési kulcsok egyikének megfelelően. Az illesztés végrehajtásához a CSRDepTime oszlopot a legközelebbi órára kell kerekíteni, amelyet az **R-parancsfájl végrehajtása modul hajt** végre. 

### <a name="weather-data"></a>Időjárási adatszolgáltatások

A hiányzó értékek nagy hányadát tartalmazó oszlopok ki vannak zárva a **Project Columns** modul használatával. Ezek az oszlopok az összes karakterlánc értékű oszlopot tartalmazzák: **ValueForWindCharacter**, **WetBulbFarenheit**, **WetBulbCelsius**, **PressureTendency**, **PressureChange**, **SeaLevelPressure**és **StationPressure** .

Ekkor a rendszer a hiányzó oszlopokra alkalmazza a **tiszta hiányzó** adatmodult, hogy eltávolítsa a hiányzó adatsorokat.

Az időjárási megfigyelési idők a legközelebbi teljes órára vannak kerekítve. Az ütemezett repülési időpontok és az időjárási megfigyelések időpontját az ellenkező irányba kerekítjük, így biztosítva, hogy a modell csak a repülési idő előtti időjárási időt használja. 

Mivel az időjárási adatok helyi időben kerülnek jelentésre, az időzóna-különbségek az ütemezett indulási idő és az időjárási megfigyelési idő kivonásával vannak elszámolva. Ezek a műveletek az **R szkript végrehajtása** modul használatával hajthatók végre.

### <a name="joining-datasets"></a>Adatkészletek csatlakoztatása

A repülési rekordok a beléptetési **adatok** modul használatával a Flight (**OriginAirportID**) által használt időjárási adatokkal vannak csatlakoztatva.

 ![Csatlakozás a repüléshez és az időjáráshoz forrás szerint](media/how-to-designer-sample-classification-predict-flight-delay/join-origin.png)


A repülési rekordok időjárási adatokkal vannak összekapcsolva a repülési cél (**DestAirportID**) használatával.

 ![Csatlakozás a repüléshez és az időjáráshoz célhoz](media/how-to-designer-sample-classification-predict-flight-delay/join-destination.png)

### <a name="preparing-training-and-test-samples"></a>Képzések és tesztelési minták előkészítése

Az **adatok felosztása** modul a betanítási és a tesztelési feladatokra vonatkozó októberi rekordokra osztja fel az adatokat.

 ![A képzési és tesztelési célú megosztások](media/how-to-designer-sample-classification-predict-flight-delay/split.png)

Az év, hónap és timezone oszlop törlődik a betanítási adatkészletből az Oszlopok kiválasztása modul használatával.

## <a name="define-features"></a>Funkciók definiálása

A gépi tanulásban a funkciók a érdeklik egyes, mérhető tulajdonságai. Erős funkciók megkeresése kísérletezést és tartományi ismereteket igényel. Bizonyos jellemzők ugyanis hasznosabbak a cél előrejelzéséhez, mint mások. Emellett előfordulhat, hogy egyes funkciók erős korrelációt mutatnak más funkciókkal, és nem vesznek fel új adatokat a modellbe. Ezek a funkciók eltávolíthatók.

Modell létrehozásához használhatja az összes elérhető funkciót, vagy kiválaszthatja a szolgáltatások egy részhalmazát.

## <a name="choose-and-apply-a-learning-algorithm"></a>Tanulási algoritmus kiválasztása és alkalmazása

Hozzon létre egy modellt a **kétosztályos logisztikai regressziós** modullal, és tanítsa be azt a betanítási adatkészleten. 

A **betanítási modell** modul eredménye egy betanított besorolási modell, amely az előrejelzések készítéséhez használható új minták kiértékelésére. A test (teszt) beállítással pontokat hozhat létre a betanított modellből. Ezután használja a **modell kiértékelése** modult a modellek minőségének elemzéséhez és összehasonlításához.
a folyamat futtatása után a folyamat után megtekintheti a **pontszám modell** modul kimenetét. ehhez kattintson a kimeneti portra, és válassza a **Megjelenítés**lehetőséget. A kimenet tartalmazza a pontszámmal ellátható címkéket és a címkék valószínűségét.

Végül az eredmények minőségének teszteléséhez adja hozzá a **modell kiértékelése** modult a folyamat vászonhoz, és a bal oldali bemeneti portot a pontszám modell modul kimenetéhez kapcsolja. Futtassa a folyamatot, és tekintse meg a **modell kiértékelése** modul kimenetét a kimeneti portra kattintva, és válassza a **Megjelenítés**lehetőséget.

## <a name="evaluate"></a>Értékelés
A logisztikai regressziós modell 0,631 AUC rendelkezik a tesztelési készleten.

 ![értékelés](media/how-to-designer-sample-classification-predict-flight-delay/evaluate.png)

## <a name="next-steps"></a>Következő lépések

Ismerje meg a tervező számára elérhető egyéb mintákat:

- [1. példa – regresszió: az autó árának előrejelzése](how-to-designer-sample-regression-automobile-price-basic.md)
- [2. minta – regresszió: algoritmusok összehasonlítása az autó árának előrejelzéséhez](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [3. minta – besorolás a szolgáltatás kiválasztásával: bevétel előrejelzése](how-to-designer-sample-classification-predict-income.md)
- [4. minta – besorolás: a hitelkockázat előrejelzése (a Cost szenzitív)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [5. példa – besorolás: forgalom előrejelzése](how-to-designer-sample-classification-churn.md)
- [7. minta – szöveges besorolás: wikipedia SP 500 adatkészlet](how-to-designer-sample-text-classification.md)