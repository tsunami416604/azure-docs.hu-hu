---
title: A csapatadat-elemzési folyamat életciklusának modellezési szakasza
description: Az adatelemzési projektek modellezési szakaszának céljai, feladatai és céljai
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 1d3cd61ea3da88c4c5231f22c0e127508591fb8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720469"
---
# <a name="modeling-stage-of-the-team-data-science-process-lifecycle"></a>A csapatadat-elemzési folyamat életciklusának modellezési szakasza

Ez a cikk ismerteti a célokat, feladatokat és a csapat adatelemzési folyamat (TDSP) modellezési szakaszához kapcsolódó célokat, feladatokat és eredményeket. Ez a folyamat egy ajánlott életciklust biztosít, amely az adatelemzési projektek strukturálásához használható. Az életciklus felvázolja azokat a főbb szakaszokat, amelyeket a projektek általában végrehajtanak, gyakran ismétlődően:

   1. **Üzleti ismertetés**
   2. **Adatgyűjtés és adatértelmezés**
   3. **Modellezés**
   4. **Környezet**
   5. **Felhasználói elfogadás**

Itt van egy vizuális ábrázolása a TDSP életciklus:

![TDSP életciklus](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Célok
* Határozza meg a gépi tanulási modell optimális adatfunkcióit.
* Hozzon létre egy informatív gépi tanulási modellt, amely a legpontosabban előrejelzi a célt.
* Hozzon létre egy gépi tanulási modellt, amely alkalmas az éles környezetben.

## <a name="how-to-do-it"></a>Hogyan kell csinálni
Ebben a szakaszban három fő feladatfoglalkozik:

  * **Funkciótervezés:** Hozzon létre adatfunkciókat a nyers adatokból a modellbetanítás megkönnyítése érdekében.
  * **Modellbetanítás:** Keresse meg azt a modellt, amely a legpontosabban válaszol a kérdésre a sikermérők összehasonlításával.
  * Határozza meg, hogy a modell **alkalmas-e a termelésre.**

### <a name="feature-engineering"></a>Jellemzőkiemelés
A szolgáltatástervezés magában foglalja a nyers változók felvételét, összesítését és átalakítását az elemzésben használt funkciók létrehozásához. Ha azt szeretné, hogy betekintést, hogy mi hajtja a modellt, akkor meg kell értenie, hogy a funkciók kapcsolódnak egymáshoz, és hogyan a gépi tanulási algoritmusok használni ezeket a funkciókat. 

Ez a lépés a tartomány szakértelem és az adatfeltárási lépésből nyert elemzések kreatív kombinációját igényli. A funkciótervezés a megtalálás és az informatív változók megtalálása és befoglalása, de ugyanakkor megpróbálja elkerülni a túl sok független változót. Informatív változók javítja az eredményt; a nem kapcsolódó változók szükségtelen zajt vezetnek be a modellbe. Ezeket a funkciókat a pontozás során kapott új adatokhoz is létre kell hoznia. Ennek eredményeképpen ezek a funkciók létrehozása csak a pontozás időpontjában rendelkezésre álló adatoktól függhet. 

A különböző Azure-adattechnológiák használata kori szolgáltatástervezéssel kapcsolatos technikai útmutatásért olvassa el [a Szolgáltatástervezés az adatelemzési folyamat funkciómérnöki témakört.](create-features.md) 

### <a name="model-training"></a>A modell betanítása
Attól függően, hogy milyen típusú kérdés, hogy próbál válaszolni, számos modellezési algoritmusok állnak rendelkezésre. Az algoritmusok kiválasztásával kapcsolatos útmutatásért olvassa el az Algoritmusok kiválasztása a Microsoft Azure Machine Learninghez című [témakört.](../studio/algorithm-choice.md) Bár ez a cikk az Azure Machine Learninget használja, az általa nyújtott útmutatás hasznos minden gépi tanulási projekt hez. 

A modellbetanítás folyamata a következő lépéseket tartalmazza: 

   * **A bemeneti adatok felosztása** véletlenszerűen egy betanítási adatkészlet és egy teszt adatkészlet modellezésére.
   * **A modellek a** betanítási adatkészlet használatával hozhat létre.
   * **Értékelje ki** a betanítást és a tesztadatkészletet. Használjon egymással versengő gépi tanulási algoritmusokat, valamint a különböző kapcsolódó hangolási paramétereket (más néven *paraméteres söprést),* amelyek az aktuális adatokkal való érdeklődési kérdés megválaszolására irányulnak.
   * **Határozza meg a "legjobb" megoldást** a kérdés megválaszolásához az alternatív módszerek sikerességi mutatóinak összehasonlításával.

> [!NOTE]
> **Szivárgás elkerülése:** Adatszivárgást okozhat, ha a betanítási adatkészleten kívülről származó adatokat tartalmaz, amelyek lehetővé teszik, hogy egy modell vagy gépi tanulási algoritmus irreálisan jó előrejelzéseket készítsen. Szivárgás gyakori oka annak, hogy az adatszakértők idegesek lesznek, amikor olyan prediktív eredményeket kapnak, amelyek túl szépnek tűnnek ahhoz, hogy igazak legyenek. Ezeket a függőségeket nehéz észlelni. A szivárgás elkerülése érdekében gyakran szükség van iteráció között épület egy elemzési adatkészlet, egy modell létrehozása, és az eredmények pontosságának kiértékelése között. 
> 
> 

Egy [automatizált modellezési és jelentéskészítő eszközt](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling) biztosítunk a TDSP-vel, amely képes több algoritmuson és paraméteres söprésen keresztül létrehozni egy alapmodellt. Emellett egy alapmodellezési jelentést is készít, amely összefoglalja az egyes modellek és paraméterkombinációk teljesítményét, beleértve a változó fontosságot is. Ez a folyamat is iteratív, mivel vezethet további funkció mérnöki. 

## <a name="artifacts"></a>Összetevők
Az ebben a szakaszban előállított leletek a következők:

   * [Szolgáltatáskészletek](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md): A modellezéshez kifejlesztett funkciókat az **Adatdefiníciós** jelentés **Funkciókészletek** szakasza ismerteti. A szolgáltatások létrehozásához a kódra mutató mutatókat és a szolgáltatás létrehozásának leírását tartalmazza.
   * [Modelljelentés:](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md)Minden kipróbált modellhez egy szabványos, sablonalapú jelentés készül, amely minden egyes kísérlet részleteit tartalmazza.
   * **Ellenőrzőpont-döntés:** Értékelje ki, hogy a modell megfelelően teljesít-e az éles környezetszámára. Néhány kulcsfontosságú kérdés a következő:
     * A modell kellő bizalommal válaszol a kérdésre a vizsgálati adatok nak megfelelően? 
     * Ha megpróbál bármilyen alternatív megközelítések? További adatokat kell gyűjtenie, több funkciótervezést kell végeznie, vagy más algoritmusokkal kell kísérleteznie?

## <a name="next-steps"></a>További lépések

A TDSP életciklusának egyes lépéseire mutató hivatkozások:

   1. [Üzleti ismertetés](lifecycle-business-understanding.md)
   2. [Adatgyűjtés és adatértelmezés](lifecycle-data.md)
   3. [Modellezés](lifecycle-modeling.md)
   4. [Környezet](lifecycle-deployment.md)
   5. [Felhasználói elfogadás](lifecycle-acceptance.md)

Teljes körű forgatókönyveket biztosítunk, amelyek bemutatják a folyamat összes lépését az adott forgatókönyvekhez. A [Példa forgatókönyvek](walkthroughs.md) cikk hivatkozásokat és miniatűr leírásokat tartalmazó forgatókönyvek listáját tartalmazza. A forgatókönyvek bemutatják, hogyan kombinálhatja a felhőt, a helyszíni eszközöket és szolgáltatásokat egy munkafolyamatban vagy folyamatban egy intelligens alkalmazás létrehozásához. 

Példák az Azure Machine Learning Studio tdsp-kben végrehajtott lépések végrehajtásáról: [Use the TDSP with Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/). 
