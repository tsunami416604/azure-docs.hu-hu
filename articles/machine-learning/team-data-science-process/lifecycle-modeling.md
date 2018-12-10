---
title: A csoportos adatelemzési folyamat életciklusa modellezési szakaszában
description: A célok, feladatok és a modellezés szakaszra a data-adatelemzési projektek le
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: dac686878ff128e3d556c0dbd7e9a2d51ac1756d
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53139952"
---
# <a name="modeling-stage-of-the-team-data-science-process-lifecycle"></a>A csoportos adatelemzési folyamat életciklusa modellezési szakaszában

Ez a cikk ismerteti a célokat, feladatok és a modellezés szakaszban, a csoportos adatelemzési folyamat (TDSP) társított le. Ez a folyamat egy ajánlott életciklussal, amely használatával a data-adatelemzési projektek strukturálása biztosít. Az életciklus a fő szakaszai, projektek általában végrehajtható, iteratív gyakran ismerteti:

   1. **Az üzleti igények felmérése**
   2. **Adatgyűjtés és adatértelmezés**
   3. **Modellezés**
   4. **Üzembe helyezés**
   5. **Felhasználói elfogadás**

A TDSP életciklus ábrázolása a következő:

![TDSP-életciklus](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Célok
* Határozza meg, hogy az optimális funkciók gépi tanulási modell.
* Hozzon létre egy informatív gépi tanulási modellt az a cél legpontosabban.
* Hozzon létre egy gépi tanulási modellt, amely lehetővé teszi az éles üzemi környezetek részei.

## <a name="how-to-do-it"></a>Megtudhatja, hogyan teheti
Nincsenek az ebben a szakaszban leírt három fő feladat:

  * **Jellemzőkiemelés**: adatok funkciók létrehozása a nyers adatoktól a modell betanítása megkönnyítése érdekében.
  * **Modell a betanítási**: keresse meg a modellt, amely összehasonlítja a sikerkritériumokat leginkább megfelelő választ ad a kérdést.
  * Megvizsgálja, hogy a modell **megfelelő az éles környezetben.**

### <a name="feature-engineering"></a>Jellemzőkiemelés
Funkciófejlesztési magában foglalja a belefoglalási, aggregációs és átalakítási nyers változókat hozhat létre az a Funkciók, az elemzés során használt. Ha azt szeretné, hogy betekintést modell előfeltételeit, majd meg kell tudni, hogyan kapcsolódik egymáshoz a funkciók egymáshoz, és hogyan a gépi tanulási algoritmusok ezek a funkciók használatához. 

Ebben a lépésben a tartományi szakértelmet creative kombinációját és az adatok feltárására lépésben insights szükséges. A szolgáltatás keresése és informatív változóval együtt, de egyszerre túl sok független változók kerülni próbál egyensúlyt szó. Informatív változók javíthatja az eredmény; független változók szükségtelen zaj bevezetni a modellbe. Létre kell hoznia ezeket a funkciókat, a kiértékelés során kapott új adatokat is. Ezeket a funkciókat a generációját ennek eredményeképpen csak függ a kiértékelés idején elérhető adatokat. 

A szolgáltatás műszaki útmutatást mérnöki mikor győződjön meg arról, a különböző Azure-beli adat-technológiák használata, hogy [funkciók tervezése az adatelemzési folyamat a](create-features.md). 

### <a name="model-training"></a>Modell betanítása
Kérdést megválaszolni kívánt típusától függően érhetők el számos modellezési algoritmusokat. Az algoritmusok kiválasztásáról, tekintse át [algoritmusok kiválasztása a Microsoft Azure Machine Learning](../studio/algorithm-choice.md). Bár ez a cikk az Azure Machine Learning, az útmutatót biztosít hasznos a machine-learning projektekkel. 

A modell betanítása folyamat a következő lépésekből áll: 

   * **A bemeneti adatok** véletlenszerűen modellezési tanítási adathalmazt és a egy teszt adatkészlet esetében.
   * **A modellek készítése** a tanítási adathalmazt használatával.
   * **Kiértékelése** a tanítási és tesztelési adatkészlet. Versengő gépi tanulási algoritmusokkal és a különböző társított hangolási paraméterek sorozatán használja (más néven egy *paraméteres*), amely stíluskalauz a lényeges az aktuális adatokkal a kérdés megválaszolásához.
   * **A "ajánlott" megoldást** a választ a kérdésre összehasonlítja a sikerkritériumokat, alternatív módszerek között.

> [!NOTE]
> **Kiszivárgásának elkerülésére**: adatszivárgás okozhat, ha adja meg az adatait a tanítási adathalmazt, amely lehetővé teszi egy modellt vagy gépi tanulási algoritmus irreálisan jó adatelemzésre kívül. Kiszivárgását miért adatszakértők első ideg eljuthatnak prediktív eredményeket, amely adatokat úgy tűnik, hogy igaz legyen túlságosan is jó gyakori indoka. Ezeket a függőségeket is nehéz észlelni. Gyakran kiszivárgásának elkerülésére között egy analysis adatkészlet létrehozását, modellek létrehozása és az eredmények pontosságának kiértékelése léptetés igényel. 
> 
> 

Biztosítunk egy [modellezési és jelentéskészítési eszköz automatikus](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling) strukturálása tdsp-vel, amely képes több algoritmusok és paraméter segítségével futtassa el egy alapkonfiguráció modell létrehozásához. Minden modell és paraméter kombináció, beleértve a változó fontosság teljesítményét összegző jelentés modellezési alapterv is küld. Ez a folyamat akkor is iteratív, azt is meghajtóként, hogy további funkciófejlesztési. 

## <a name="artifacts"></a>Összetevők
Az ebben a szakaszban létrehozott összetevők a következők:

   * [Szolgáltatáskészletek](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#feature-sets): a modellezési fejlesztette ki az új funkciókat ismertetjük a **szolgáltatáskészletek** szakaszában a **adatdefiníció** jelentést. Mutatók azokra a kódot létrehozni, az funkciók és hogyan jött létre a funkció leírása tartalmazza.
   * [Jelentés modell](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md): minden egyes modell, amely a rendszer megpróbálkozik, egy standard sablon-alapú jelentés, amely részletesen minden kísérlet jön létre.
   * **Ellenőrzőpont döntési**: kiértékelheti, hogy a modell elég is telepíteni kell egy éles rendszer végez-e. Néhány kulcsfontosságú megválaszolandó kérdések a következők:
     * A modell választ a kérdésre adott a Tesztadatok elegendő magabiztosan? 
     * Lehetőleg bármely alternatív módszerek? Kell, további adatokat gyűjtsön, hajtsa végre a szolgáltatás további műszaki vagy egyéb algoritmusokkal kísérletezhet?

## <a name="next-steps"></a>További lépések

Az alábbiakban a TDSP életciklusának minden lépése mutató hivatkozásokat:

   1. [Az üzleti igények felmérése](lifecycle-business-understanding.md)
   2. [Adatgyűjtés és adatértelmezés](lifecycle-data.md)
   3. [Modellezés](lifecycle-modeling.md)
   4. [Üzembe helyezés](lifecycle-deployment.md)
   5. [Felhasználói elfogadás](lifecycle-acceptance.md)

Azt adja meg a teljes, végpontok közötti forgatókönyvek, amelyek bemutatják, bizonyos forgatókönyvek esetén a folyamat összes lépését. A [példa forgatókönyvek](walkthroughs.md) a cikk a forgatókönyvek miniatűr leírások és hivatkozások listáját tartalmazza. A forgatókönyvek bemutatják, hogyan kombinálhatja a felhőbeli, helyszíni eszközöket és szolgáltatásokat, munkafolyamat vagy folyamat, intelligens alkalmazások létrehozására. 

Hogyan hajtható végre a lépéseket az Azure Machine Learning Studio használó TDSPs példákért lásd [a TSDP használata az Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/). 
