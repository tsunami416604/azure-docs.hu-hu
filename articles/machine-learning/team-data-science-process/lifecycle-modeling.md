---
title: A csoport az tudományos folyamata életciklus - Azure szakasza modellezési |} Microsoft Docs
description: A célok, feladatok és a adattudomány projektek modellezési szakaszának termékek esetében
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: bradsev
ms.openlocfilehash: 8ef76e32e0a78f4ca492e6ac6d5755fdf19e734c
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="modeling"></a>Modellezés

Ez a cikk ismerteti a célokat, feladatok és a modellezési szakasza a csapat adatok tudományos folyamat (TDSP) társított termékek esetében. Ez a folyamat egy ajánlott életciklussal, amely segítségével a adattudomány projektek struktúra biztosítja. Az életciklus, projektek általában hajtható végre, gyakran ismételt fő szakaszait vázolja fel:

   1. **Üzleti ismertetése**
   2. **Adatok megszerzését és ismertetése**
   3. **Modellezési**
   4. **Üzembe helyezés**
   5. **Ügyfelek**

A TDSP életciklus vizuális ábrázolását itt található:

![TDSP életciklusa](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Célok
* Határozza meg az optimális adatok szolgáltatások gépi tanulásra modell.
* Hozzon létre egy informatív gépi tanulásra modell, amely a leginkább előrejelzésre a cél.
* Hozzon létre egy gépi tanulásra modell, amely lehetővé teszi a termelési környezetben.

## <a name="how-to-do-it"></a>Menete
Az ebben a szakaszban leírt három fő feladat van:

  * **Jellemzőkiemelés**: adatok funkciók létrehozása a modell betanítási megkönnyítése érdekében a nyers adatoktól.
  * **A modell betanítási**: keresse meg az, hogy felveszi a kérdés legpontosabban a sikerkritériumokat összehasonlítva.
  * Annak megállapítása, hogy a modell **éles alkalmas.**

### <a name="feature-engineering"></a>Jellemzőkiemelés
Szolgáltatás mérnöki magában foglalja a befoglalási, összesítési és az elemzés során használt funkciók létrehozása nyers változók átalakítása. Ha azt szeretné, hogy a modell Előfeltételek betekintést, akkor szüksége megtudhatja, hogy hogyan a szolgáltatások kapcsolódnak egymáshoz, és hogyan a gépi tanulásra algoritmusok ezen szolgáltatások használatát. 

Ezt a lépést igényel, és az adatok feltárása lépésben beszerzett insights tartomány szakértői kreatív kombinációját. A szolgáltatás a Keresés és informatív változóval együtt, de túl sok független változók elkerülése érdekében tett kísérlet egy időben egyensúlyt igényel szó. Informatív változók javítása az eredmény; független változók bevezetéséhez a modell nélküli "zajt". Is kell ezeket a szolgáltatásokat bármely pontozási során kapott új adatok létrehozásához. Ezek a funkciók generációját emiatt csak pontozási időpontjában rendelkezésre álló adatok függ. 

Szolgáltatás műszaki útmutatót mérnöki mikor ellenőrizze az Azure data különböző technológiák használata, hogy [Jellemzőkiemelés az adatok tudományos folyamatban](create-features.md). 

### <a name="model-training"></a>modell betanítási
Attól függően, hogy a kérdés megválaszolásához kezelni kívánt típusú érhetők el számos modellezési algoritmus. Az algoritmusok kiválasztásáról útmutatóért lásd: [algoritmusok kiválasztása a Microsoft Azure Machine Learning](../studio/algorithm-choice.md). Bár ebben a cikkben az Azure Machine Learning, a nyújt útmutatást esetén bármely gépi tanulásra projektek hasznos. 

A modell betanítási folyamata a következő lépésekből áll: 

   * **A bemeneti adatok** véletlenszerűen a modellezési a tanítási adathalmazt és a vizsgálati adatkészletet.
   * **A modellek létrehozása** a tanítási adathalmazt használatával.
   * **Értékelje ki** a képzési és a teszt adatkészletében. Használható együtt a különböző társított hangolási paraméterek versengő gépi tanulásra algoritmusok sorozata (néven egy *paraméter ismétlés*), amely az aktuális adatok érdeklő a kérdés megválaszolásához célközönségre.
   * **Határozza meg a "ajánlott" megoldás** a kérdés megválaszolásához összehasonlítva a sikerkritériumokat alternatív módszerek között.

> [!NOTE]
> **Adatszivárgás megakadályozásához**: adatszivárgás okozhat, ha a tanítási adathalmazt, amely lehetővé teszi, hogy a modell vagy a gépi tanulásra algoritmus irreálisan jó előrejelzéseket készítsen kívül adatait. Adatszivárgás oka egy közös miért kutatók beolvasása ideg, ha csak prediktív eredmények, amelyek adatokat úgy tűnik, túl alacsony. Ezek a függőségi észleléséhez nehéz lehet. Gyakran az adatszivárgás megakadályozásához között egy elemző adatkészlet létrehozása, a modell létrehozása, és az eredmények pontosságának értékelése léptetés igényel. 
> 
> 

Azt adja meg egy [modellezéséhez és jelentéskészítési eszköz automatikus](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling) TDSP, amely képes több algoritmusok és paraméter segítségével futtassa a modell létrehozásához el a. Minden modell és paraméter kombináció, többek között a változó fontosság teljesítményét összesítő jelentést modellezési alapterv is képes. Ez a folyamat esetén is iteratív, mert azt is meghajtó további szolgáltatás mérnöki csapathoz. 

## <a name="artifacts"></a>Összetevők
Az ebben a szakaszban létrehozott összetevőket tartalmazza:

   * [Szolgáltatáskészletek](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#feature-sets): A funkciók a modellezési kifejlesztett ismerteti a **szolgáltatáskészletek** szakasza a **adatainak definíciója** jelentés. A kód a szolgáltatások és a szolgáltatás létrehozásáról leírása mutatók tartalmaz.
   * [A jelentés a modell](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md): minden modell, amely próbálkozik, egy szabványos, amely adatokat minden egyes kísérlet a sablon-alapú jelentés hozott létre.
   * **Ellenőrzőpont döntési**: mérlegelje, hogy telepíteni kell egy éles rendszer elégséges kezeli a modell. Néhány kulcsfontosságú megválaszolandó kérdések a következők:
     * A modell nem elegendő a vizsgálati adatok megadott megbízhatósággal válaszolja a kérdést? 
     * Kell-e próbálja bármely alternatív módszerek? Kell, további adatokat gyűjtsön, hajtsa végre a további szolgáltatás mérnöki csapathoz, vagy más algoritmusok kísérletezhet?

## <a name="next-steps"></a>További lépések

Az alábbiakban a TDSP életciklusát lépésre mutató hivatkozásokat:

   1. [Üzleti ismertetése](lifecycle-business-understanding.md)
   2. [Adatok megszerzését és ismertetése](lifecycle-data.md)
   3. [Modellezési](lifecycle-modeling.md)
   4. [Üzembe helyezés](lifecycle-deployment.md)
   5. [Ügyfelek](lifecycle-acceptance.md)

Teljes-végpontok forgatókönyvek, amelyek azt mutatják, meghatározott forgatókönyvek esetén a folyamat lépései a Microsoft biztosítja. A [példa forgatókönyvek](walkthroughs.md) a cikk ismerteti a miniatűr leírások és hivatkozások forgatókönyvek listája. A forgatókönyvek bemutatják, hogyan lehet a felhő, a helyszíni eszközök és szolgáltatások egyesítése munkafolyamat vagy csővezeték intelligens alkalmazás létrehozása. 

Hogyan hajthat végre a lépéseket az Azure Machine Learning Studio használó TDSPs példákért lásd [a TDSP használja az Azure Machine Learning segítségével](http://aka.ms/datascienceprocess). 
