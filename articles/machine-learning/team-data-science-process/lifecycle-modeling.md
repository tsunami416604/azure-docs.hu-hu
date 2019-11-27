---
title: A csoportos adatelemzési folyamat életciklusa modellezési szakaszában
description: A célok, feladatok és a modellezés szakaszra a data-adatelemzési projektek le
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d72d39a2a59e06954c36473083af2d2b4689a7b6
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74538225"
---
# <a name="modeling-stage-of-the-team-data-science-process-lifecycle"></a>A csoportos adatelemzési folyamat életciklusa modellezési szakaszában

Ez a cikk ismerteti a célokat, feladatok és a modellezés szakaszban, a csoportos adatelemzési folyamat (TDSP) társított le. Ez a folyamat egy ajánlott életciklussal, amely használatával a data-adatelemzési projektek strukturálása biztosít. Az életciklus a fő szakaszai, projektek általában végrehajtható, iteratív gyakran ismerteti:

   1. **Üzleti ismeretek**
   2. **Adatgyűjtés és-megértés**
   3. **Modellezés**
   4. **Üzembe helyezés**
   5. **Ügyfél-elfogadás**

A TDSP életciklus ábrázolása a következő:

![TDSP-életciklus](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Célok
* Határozza meg, hogy az optimális funkciók gépi tanulási modell.
* Hozzon létre egy informatív gépi tanulási modellt az a cél legpontosabban.
* Hozzon létre egy gépi tanulási modellt, amely lehetővé teszi az éles üzemi környezetek részei.

## <a name="how-to-do-it"></a>Megtudhatja, hogyan teheti
Nincsenek az ebben a szakaszban leírt három fő feladat:

  * **Szolgáltatás-mérnöki**: adatszolgáltatások létrehozása a nyers adatokból a modell betanításának elősegítése érdekében.
  * **Modell betanítása**: keresse meg a modellt, amely a lehető legpontosabban válaszol a sikerességi mérőszámok összehasonlításával.
  * Állapítsa meg, hogy a modell **alkalmas-e a gyártásra.**

### <a name="feature-engineering"></a>Jellemzőkiemelés
Funkciófejlesztési magában foglalja a belefoglalási, aggregációs és átalakítási nyers változókat hozhat létre az a Funkciók, az elemzés során használt. Ha azt szeretné, hogy betekintést modell előfeltételeit, majd meg kell tudni, hogyan kapcsolódik egymáshoz a funkciók egymáshoz, és hogyan a gépi tanulási algoritmusok ezek a funkciók használatához. 

Ebben a lépésben a tartományi szakértelmet creative kombinációját és az adatok feltárására lépésben insights szükséges. A szolgáltatás keresése és informatív változóval együtt, de egyszerre túl sok független változók kerülni próbál egyensúlyt szó. Informatív változók javíthatja az eredmény; független változók szükségtelen zaj bevezetni a modellbe. Létre kell hoznia ezeket a funkciókat, a kiértékelés során kapott új adatokat is. Ezeket a funkciókat a generációját ennek eredményeképpen csak függ a kiértékelés idején elérhető adatokat. 

A különböző Azure-adattechnológiákat használó funkciók mérnöki műszaki útmutatója: [az adatelemzési folyamat funkcióinak](create-features.md)fejlesztése. 

### <a name="model-training"></a>A modell betanítása
Kérdést megválaszolni kívánt típusától függően érhetők el számos modellezési algoritmusokat. Az algoritmusok kiválasztásával kapcsolatos útmutatásért lásd: [algoritmusok kiválasztása Microsoft Azure Machine Learninghoz](../studio/algorithm-choice.md). Bár ez a cikk az Azure Machine Learning, az útmutatót biztosít hasznos a machine-learning projektekkel. 

A modell betanítása folyamat a következő lépésekből áll: 

   * **A bemeneti adatok véletlenszerűen való felosztása** modellezési adatkészletbe és tesztelési adatkészletbe.
   * Hozza **létre a modelleket** a betanítási adatkészlet használatával.
   * **Értékelje** ki a betanítást és a tesztelési adatkészletet. A különböző társított hangolási paraméterekkel, valamint az aktuális adatokkal kapcsolatos kérdések megválaszolásával megválaszolvaa versengő gépi tanulási algoritmusok sorozatát is használhatja.
   * **Határozza meg a "legjobb" megoldást** a kérdés megválaszolásához az alternatív módszerek közötti sikerességi mérőszámok összehasonlításával.

> [!NOTE]
> A **szivárgás elkerülése**: adatszivárgást okozhat, ha a betanítási adatkészleten kívülről származó adatokkal is rendelkezik, ami lehetővé teszi, hogy a modell vagy a gépi tanulási algoritmus reálisan jó előrejelzéseket készítsen. Kiszivárgását miért adatszakértők első ideg eljuthatnak prediktív eredményeket, amely adatokat úgy tűnik, hogy igaz legyen túlságosan is jó gyakori indoka. Ezeket a függőségeket is nehéz észlelni. Gyakran kiszivárgásának elkerülésére között egy analysis adatkészlet létrehozását, modellek létrehozása és az eredmények pontosságának kiértékelése léptetés igényel. 
> 
> 

Egy [automatizált modellezési és jelentéskészítési eszközt](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling) biztosítunk olyan TDSP, amely több algoritmussal és paraméterrel is futtatható egy alapmodell létrehozásához. Minden modell és paraméter kombináció, beleértve a változó fontosság teljesítményét összegző jelentés modellezési alapterv is küld. Ez a folyamat akkor is iteratív, azt is meghajtóként, hogy további funkciófejlesztési. 

## <a name="artifacts"></a>Összetevők
Az ebben a szakaszban létrehozott összetevők a következők:

   * [Szolgáltatások készletei](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md): a modellezéshez kifejlesztett funkciók leírása az **adatdefiníciós** jelentés **szolgáltatáskészlet-készletek** szakaszában található. Mutatók azokra a kódot létrehozni, az funkciók és hogyan jött létre a funkció leírása tartalmazza.
   * [Modell jelentés](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md): minden olyan modell esetében, amely minden egyes kísérlethez meg van határozva, egy standard, sablon alapú jelentés készül, amely részletesen ismerteti az egyes kísérletek részleteit.
   * **Ellenőrzőpont-döntés**: annak kiértékelése, hogy a modell megfelelően működik-e az éles környezetben való üzembe helyezéshez. Néhány kulcsfontosságú megválaszolandó kérdések a következők:
     * A modell választ a kérdésre adott a Tesztadatok elegendő magabiztosan? 
     * Lehetőleg bármely alternatív módszerek? Kell, további adatokat gyűjtsön, hajtsa végre a szolgáltatás további műszaki vagy egyéb algoritmusokkal kísérletezhet?

## <a name="next-steps"></a>Következő lépések

Az alábbiakban a TDSP életciklusának minden lépése mutató hivatkozásokat:

   1. [Üzleti ismeretek](lifecycle-business-understanding.md)
   2. [Adatgyűjtés és-megértés](lifecycle-data.md)
   3. [Modellezés](lifecycle-modeling.md)
   4. [Üzembe helyezés](lifecycle-deployment.md)
   5. [Ügyfél-elfogadás](lifecycle-acceptance.md)

Azt adja meg a teljes, végpontok közötti forgatókönyvek, amelyek bemutatják, bizonyos forgatókönyvek esetén a folyamat összes lépését. A [példákat](walkthroughs.md) bemutató cikk a hivatkozásokat és a miniatűr leírásait tartalmazza. A forgatókönyvek bemutatják, hogyan kombinálhatja a felhőbeli, helyszíni eszközöket és szolgáltatásokat, munkafolyamat vagy folyamat, intelligens alkalmazások létrehozására. 

A Azure Machine Learning Studiot használó TDSPs lépéseinek végrehajtásával kapcsolatos Példákért lásd: [a TDSP használata Azure Machine learning használatával](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/). 
