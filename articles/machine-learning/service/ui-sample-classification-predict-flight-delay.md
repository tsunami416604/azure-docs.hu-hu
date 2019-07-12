---
title: 'Besorolás: Repülőjáratok késéseinek előrejelzése'
titleSuffix: Azure Machine Learning service
description: Ez a cikk bemutatja, hogyan hozhat létre a machine learning-modell előre jelezni a járatok késésének a vizuális fogd és vidd felület és az egyéni R-kód használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 07/02/2019
ms.openlocfilehash: 773e55fe4b5ca5acf27ba1765e5a16075f625187
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607636"
---
# <a name="sample-6---classification-predict-flight-delays-using-r"></a>6 – besorolási. példa: Az R használata a járatok késésének előrejelzése

Ezzel a kísérlettel előre jelezni, ha repülőjáratok késni fog több mint 15 perces korábbi repülési és időjárási adatait használja.

Ez a probléma is megközelíthető, osztályozási probléma, előrejelzésére két osztály – késleltetett, vagy időben. A besorolás, ez a modell példákat a korábbi repülőjáratok adatainak nagy számú használatával hozhat létre.

Az elkészült kísérletnek a következőképpen graph itt látható:

[![A kísérlet diagram](media/ui-sample-classification-predict-flight-delay/experiment-graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Válassza ki a **nyílt** gomb a minta 6 kísérlet:

    ![Nyissa meg a kísérlet](media/ui-sample-classification-predict-flight-delay/open-sample6.png)

## <a name="get-the-data"></a>Az adatok lekérése

Ez kísérletezhet, használja a **repülőjáratok késések adatainak** adatkészlet. Az Egyesült Államok TranStats adatgyűjtést része Minisztériumának. Az adatkészlet repülési késleltetés információkat tartalmaz az április 2013. októberi. Az adatok feltöltése a vizuális felhasználói felületet, mielőtt azt előkészítette a következő:

* Szűrt 70 legforgalmasabb repülőterek Egyesült Államok kontinentális területén tároljuk.
* A járatok relabeled késnek-e több mint 15 perc, megszakadt.
* Kiszűrte forgalomelterelés repülőjáratok.
* A kijelölt 14 oszlopok.

A flight data, kiegészítve a **időjárási adatkészlet** szolgál. Az időjárási adatok óránkénti szárazföldi időjárási megfigyelések a NOAA tartalmaz, és repülőtér időjárási állomások kiterjedő azonos 2013. április október időtartammal a megfigyelések jelöli. Mielőtt feltöltené az Azure ML vizuális felhasználói felületet, hogy előkészítette a következő:

* Megfelelő repülőtér azonosítók időjárásjelző azonosítók hozzá lettek rendelve.
* Időjárás-állomások 70 legforgalmasabb repülőterek hozzá nem rendelt el lettek távolítva.
* A dátum oszlop lett felosztva külön oszlopban: Év, hónap és nap.
* A kijelölt 26 oszlopok.

## <a name="pre-process-the-data"></a>Az adatok előzetes feldolgozása

Egy adatkészlet általában néhány előre feldolgozását, mielőtt azok elemezhetők igényel.

![adat-folyamata](media/ui-sample-classification-predict-flight-delay/data-process.png)

### <a name="flight-data"></a>Repülőjáratok adatainak

Az oszlopok **szolgáltatója**, **OriginAirportID**, és **DestAirportID** lesznek mentve az egész számok. Azonban azok kategorikus attribútumok, használja a **metaadatainak szerkesztése** történő váltás kategorikus modul.

![edit-metadata](media/ui-sample-classification-predict-flight-delay/edit-metadata.png)

Ezután a **Select Columns** adatkészlet modul lehetséges cél leakers az adatkészlet oszlopok kizárása: **DepDelay**, **DepDel15**, **ArrDelay**, **meg lett szakítva**, **év**. 

Csatlakozzon a flight rekordok óránkénti időjárási rekordjait, használja az ütemezett indulási idő, az illesztési kulcsok egyikét. A csatlakozás ehhez a CSRDepTime oszlop kell kerekíti a legközelebbi órára, amely a végzi el a **R-szkript végrehajtása** modul. 

### <a name="weather-data"></a>Időjárási adatok

Hiányzó értékek nagy részét rendelkező oszlopok ki legyenek zárva, használja a **Projektoszlopok** modul. Ezekben az oszlopokban összes karakterláncos oszlopokat tartalmazza: **ValueForWindCharacter**, **WetBulbFarenheit**, **WetBulbCelsius**, **PressureTendency**, **PressureChange**, **SeaLevelPressure**, és **StationPressure**.

A **Clean Missing Data** modul tüntetjük fel a többi oszlopot eltávolítja a hiányzó sorokat.

Időjárási megfigyelési többször vannak kerekítve a legközelebbi teljes órára. Járatok ideje és az időjárási megfigyelési időpontok annak érdekében, hogy a modell csak időjárási használ a flight időpont előtt ellentétes irányú kerekíti. 

Mivel az időjárási adatok, jelentett helyi idő időzóna különbségek számolják el az ütemezett indulási idő és az időjárási megfigyelési idő időzóna oszlopai kivonásával történik. Ezeket a műveleteket hajthatja végre használatával a **R-szkript végrehajtása** modul.

### <a name="joining-datasets"></a>Az adatkészletek csatlakoztatása

Repülési rekordok csatlakoznak az időjárási adatok származási félóránként rögzíti helyen (**OriginAirportID**) használatával a **csatlakozzon adatok** modul.

 ![Csatlakoztassa a flight és időjárási származási hely](media/ui-sample-classification-predict-flight-delay/join-origin.png)


Időjárási adatok és a flight segítségével a csatlakoztatott repülési rekordok (**DestAirportID**).

 ![Csatlakozzon a flight és weather cél szerint](media/ui-sample-classification-predict-flight-delay/join-destination.png)

### <a name="preparing-training-and-test-samples"></a>Betanítási és vizsgálati minták előkészítése

A **Split Data** modul bontja az április adatokat képzéshez szeptember rekordok, és október tesztelési rögzíti.

 ![Felosztása a betanítási és vizsgálati adatok](media/ui-sample-classification-predict-flight-delay/split.png)

Év, hónap és időzóna-oszlopok a betanítási adatkészletet az oszlopok kiválasztása modullal törlődnek.

## <a name="define-features"></a>A jellemzők meghatározása

A Machine learning a szolgáltatások olyan egyéni mérhető tulajdonságok, érdekli. Rengeteg funkciók megkeresése Kísérletezési és a tartomány ismeretek szükségesek. Bizonyos jellemzők ugyanis hasznosabbak a cél előrejelzéséhez, mint mások. Néhány funkció is, előfordulhat, hogy az egyéb szolgáltatásokkal erős korreláció, és nem fog új információk hozzáadása a modellhez. Ezek a funkciók távolíthatja el.

A modell létrehozása minden funkcióját használja, vagy válassza ki a funkciók egy részét.

## <a name="choose-and-apply-a-learning-algorithm"></a>A tanulási algoritmus kiválasztása és alkalmazása

Hozzon létre egy modellt a **Two-Class logisztikai regressziós** modul, és azt a betanítási adatkészletet. 

Eredményét a **tanítási modell** modul egy betanított osztályozási modell használható pontszámot rendelni az új mintát, hogy előrejelzéseket végezzen. A pontszámok generálhatnak a betanított modellek beállítása teszt használja. Ezután a **Evaluate Model** modul elemzéséhez, és hasonlítsa össze a modell minőségét.

Után futtassa a kísérletet, megtekintheti a kimenete a **Score Model** modult. Ehhez kattintson a kimeneti portra, majd válasszon **Visualize**. A kimenet tartalmazza a pontozott címkék és a feliratok a valószínűségek.

Végül az eredmények minőségének ellenőrzéséhez adja hozzá a **Evaluate Model** modult a kísérletvászonra vászonra, és csatlakozzon a bal oldali bemeneti portját a Score Model-modul kimenete. A kísérlet futtatásához és megtekintéséhez a **Evaluate Model** modult, kattintson a kimeneti portra, majd válasszon **Visualize**.

## <a name="evaluate"></a>Értékelés
A logisztikai regressziós modellt állítsa be a teszteléshez használt 0.631 AUC rendelkezik.

 ![értékelés](media/ui-sample-classification-predict-flight-delay/evaluate.png)

## <a name="next-steps"></a>További lépések

Ismerje meg a vizuális felületen érhető el a más minták:

- [1 – regressziós. példa: Egy autó árát előrejelzése](ui-sample-regression-predict-automobile-price-basic.md)
- [2 – regressziós. példa: Hasonlítsa össze az autó árának előrejelzése algoritmusok](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Mintául szolgáló 3 - besorolás: Hitelkockázat előrejelzése](ui-sample-classification-predict-credit-risk-basic.md)
- [4 – besorolási. példa: Hitelkockázatot (költség-és nagybetűket)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [5 – besorolási. példa: Forgalom előrejelzése](ui-sample-classification-predict-churn.md)
