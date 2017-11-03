---
title: "Team adatok tudományos folyamat életciklus - Azure szakasza modellezési |} Microsoft Docs"
description: "A célok, feladatok és az adatok tudományos projektek modellezési szakaszának termékek esetében."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/02/2017
ms.author: bradsev;
ms.openlocfilehash: 0c855faa96d7feb9f762ecaed7654669a5a8a5b7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="modeling"></a>Modellezés

Ez a témakör bemutatja a célok, feladatok, és a társított termékek esetében a **szakasz modellezési** az Team tudományos folyamat. Ez a folyamat egy ajánlott életciklussal, amely segítségével az adatok tudományos projektek struktúra biztosítja. Az életciklus, projektek általában hajtható végre, gyakran ismételt fő szakaszait vázolja fel:

* **Üzleti ismertetése**
* **Adatok megszerzését és ismertetése**
* **Modellezési**
* **Üzembe helyezés**
* **Ügyfelek**

Íme egy vizuális ábrázolását a **Team adatok tudományos folyamat életciklus**. 

![TDSP-Lifecycle2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Célok
* A gépi tanulási modell optimális adatok funkciói.
* Olyan informatív ML-modell, amely a leginkább előrejelzésre a cél.
* Olyan ML-modell, amely a termelési alkalmas.

## <a name="how-to-do-it"></a>Menete
Az ebben a szakaszban leírt három fő feladat van:

* **Jellemzőkiemelés**: adatok funkciók létrehozása a modell betanítási megkönnyítése érdekében a nyers adatoktól.
* **A modell betanítási**: keresse meg az, hogy felveszi a kérdés legpontosabban a sikerkritériumokat összehasonlítva.
* Annak megállapítása, hogy a modell **éles alkalmas**.

### <a name="31-feature-engineering"></a>3.1 jellemzőkiemelés
A szolgáltatás műszaki osztály magában foglalja a, összesítési és az átalakítási nyers változók létrehozása az elemzés során használt szolgáltatásait. Ha azt szeretné, hogy a modell Előfeltételek betekintést, akkor szüksége tudni, hogyan szolgáltatások kapcsolódnak egymáshoz, és hogyan a gépi tanulási algoritmusok ezen szolgáltatások használatát. Ez a lépés szükséges tartomány szakértelmét és az adatok feltárása lépésben beszerzett insights kreatív kombinációja. Ez a Keresés és informatív változóval együtt túl sok független változók elkerülésével egy egyensúlyt igényel. Informatív változók javítása az eredmény; független változók bevezetéséhez a modell nélküli "zajt". Is kell ezeket a szolgáltatásokat bármely pontozási során kapott új adatok létrehozásához. Ezek a funkciók generációját úgy csak adatok pontozása időpontjában függ. A szolgáltatás műszaki osztály különböző az Azure data technológiákhoz műszaki útmutató: [Jellemzőkiemelés az adatok tudományos folyamatban](create-features.md). 

### <a name="32-model-training"></a>3.2 a modellek betanítása
Kérdés-válasz próbált típusától függően érhetők el számos modellezési algoritmus. Az algoritmusok kiválasztásáról útmutatóért lásd: [algoritmusok kiválasztása a Microsoft Azure Machine Learning](../studio/algorithm-choice.md). Bár ez a cikk az Azure Machine Learning, a nyújt útmutatást esetén gépi tanulási projektek hasznos. 

A modell betanítási folyamata a következő lépésekből áll: 

* **A bemeneti adatok** véletlenszerűen a modellezési a tanítási adathalmazt és a vizsgálati adatkészletet.
* **A modellek létrehozása** a tanítási adathalmazt használatával.
* **Értékelje ki** (tanítási és tesztelési dataset) együtt a különböző versengő gépi tanulási algoritmusok több kapcsolódó paraméterek (más néven paraméter ismétlés), amely az aktuális adatok érdeklő a kérdés megválaszolásához célközönségre hangolása.
* **Határozza meg a "ajánlott" megoldás** a kérdés megválaszolásához összehasonlítva a sikeres metrika alternatív módszerek között.

> [!NOTE]
> **Adatszivárgás megakadályozásához**: adatszivárgás oka lehet az adatok a kívül a tanítási adathalmazt, amelyet lehetővé teszi, hogy a modell vagy a gépi tanulási algoritmus irreálisan jó előrejelzéseket készítsen a felvételét. Adatszivárgás oka egy közös miért kutatók beolvasása ideg, ha csak prediktív eredmények, amelyek adatokat úgy tűnik, túl alacsony. Ezek a függőségi észleléséhez nehéz lehet. Gyakran az adatszivárgás megakadályozásához között egy elemző adatkészlet létrehozása, a modell létrehozása és pontosságának kiértékelése léptetés igényel. 
> 
> 

Azt adja meg egy [automatikus modellezéséhez és jelentéskészítési eszköz](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling) TDSP, amely képes több algoritmusok és paraméter segítségével futtassa a modell létrehozásához el a. A jelentés minden modell és paraméter kombináció, többek között a változó fontosság teljesítményének összefoglalójához modellezési alapterv is képes. Ez a folyamat esetén is iteratív, mert azt is meghajtó további szolgáltatás mérnöki csapathoz. 

## <a name="artifacts"></a>Összetevők
Az ebben a szakaszban létrehozott összetevőket tartalmazza:

* [**Szolgáltatáskészletek**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#feature-sets): A funkciók a modellezési kifejlesztett ismerteti a **szolgáltatáskészletek** szakasza a **adatainak definíciója** jelentés. A kód a szolgáltatások és a leírást a szolgáltatás létrehozásáról a mutatók tartalmaz.
* [**A jelentés a modell**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md): minden modell, amely próbálkozik, egy szabványos, amely adatokat minden egyes kísérlet a sablon-alapú jelentés hozott létre.
* **Ellenőrzőpont döntési**: mérlegelje, hogy a modell teljesítménye elegendő telepíteni kell egy éles rendszer. Néhány kulcsfontosságú megválaszolandó kérdések a következők:
  * A modell nem elegendő a vizsgálati adatok megadott megbízhatósággal válaszolja a kérdést? 
  * Érdemes kipróbálni! bármely alternatív módszerek: további adatokat gyűjtsön, ne további szolgáltatás mérnöki csapathoz, vagy más algoritmusok kísérletezhet?

## <a name="next-steps"></a>Következő lépések

Az alábbiakban az életciklus az Team tudományos folyamat minden lépése mutató hivatkozásokat:

* [1. Üzleti ismertetése](lifecycle-business-understanding.md)
* [2. Adatok megszerzését és ismertetése](lifecycle-data.md)
* [3. Modellezési](lifecycle-modeling.md)
* [4. Központi telepítés](lifecycle-deployment.md)
* [5. Ügyfelek](lifecycle-acceptance.md)

Végpont forgatókönyvek, amelyek azt mutatják, a folyamat lépései teljes **meghatározott forgatókönyvek** is rendelkezésre állnak. Szerepel a listában, és kapcsolódik a miniatűr leírásokat a [példa forgatókönyvek](walkthroughs.md) témakör. Ezek bemutatják, hogyan lehet felhő, a helyszíni eszközök és szolgáltatások egyesítése munkafolyamat vagy csővezeték intelligens alkalmazás létrehozása. 

Az adatok tudományos folyamatban lépések végrehajtása, amelyek használják az Azure Machine Learning Studióban, tekintse meg a [Azure ML](http://aka.ms/datascienceprocess) képzési. 