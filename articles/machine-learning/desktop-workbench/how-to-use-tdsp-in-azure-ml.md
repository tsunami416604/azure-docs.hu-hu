---
title: A csoportos adatelemzési folyamat sablon a projektek strukturálása |} A Microsoft Docs
description: Hogyan hozza létre a csoportos adatelemzési folyamat (TDSP) sablonok az Azure Machine Learning, amely struktúra-projektek, az együttműködéshez
services: machine-learning
documentationcenter: ''
author: deguhath
ms.author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 8e13ba0bcbd24dcc3cd08a0d2886f1da9d85de79
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51249914"
---
# <a name="structure-projects-with-the-team-data-science-process-template"></a>Projektek strukturálása a csoportos adatelemzési folyamat sablon

Jelen dokumentum útmutatásokat nyújt adatelemzési projektek létrehozása az Azure Machine Learning egy korábbi verzióját a csoportos adatelemzési folyamat (TDSP) sablonok segítségével. Ezek a sablonok segítségével az együttműködéshez és megismételhetőség projektek. 

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 

## <a name="what-is-the-team-data-science-process"></a>Mi a csoportos adatelemzési folyamat?
A TDSP az egy rugalmas és iteratív, adatelemzési folyamat végrehajtása és a fejlett analitikai megoldások kidolgozását. Feladata, hogy az együttműködés és a vállalati szervezetek adatok adattudománnyal foglalkozó csapatai hatékonyságát javítása. A négy fő összetevőből e célok támogatja:

   * Standard [adattudományi életciklus](../team-data-science-process/lifecycle.md) definíciója.
   * Szabványos projektstruktúra [dokumentáció, és a jelentési sablonok](https://github.com/Azure/Azure-TDSP-ProjectTemplate).
   * Az infrastruktúra és a projekt végrehajtása, például, egy számítási és tárolási infrastruktúra és kód tárházak erőforrásait.
   * [Eszközök és segédprogramok](https://github.com/Azure/Azure-TDSP-Utilities) adatelemzési projekt feladatok, például:
      - Együttműködésen alapuló verziókövetés
      - Kód áttekintése
      - Adatáttekintés és modellezés
      - Munkahelyi tervezése

A TDSP teljes tárgyalását lásd: a [csoportos adatelemzési folyamat áttekintése](../team-data-science-process/overview.md).

## <a name="why-should-you-use-the-tdsp-structure-and-templates"></a>Miért használjam a TDSP szerkezetét és a sablonok?
Szabványügyi szervezet szerkezetét, életciklusát és adatelemzési projektek dokumentációját, fontos a azoknak a hatékony együttműködés megkönnyítése. Machine learning-projektek létrehozása a TDSP-sablonnal koordinált csapatmunkát ilyen keretrendszert biztosít.

Korábban kiadott egy [GitHub-tárházat a TDSP projekt felépítése és sablonok](https://github.com/Azure/Azure-TDSP-ProjectTemplate) e célok elérése érdekében. De nem volt lehetséges, eddig a TDSP szerkezetét és a egy adattudományi eszközt belül sablonok elindítását. Most is létrehozhat egy machine learning-projektet, amely a TDSP struktúra és a dokumentáció sablonok példányosítja. 

## <a name="things-to-note-before-creating-a-new-project"></a>Új projekt létrehozása előtt érdemes megjegyezni
Az alábbi elemek áttekintésével *előtt* egy új projektet hoz létre:
* Tekintse át a TDSP Machine Learning [sablon](https://aka.ms/tdspamlgithubrepo).
* (Nem a Mi már szerepel a "dokumentumok" mappában) tartalma 25 MB-nál kevesebb lehet szükséges. Tekintse meg az alábbi ebben a listában.
* A minta\_Adatmappa van csak a kis méretű adatfájlokat (5 MB-nál kevesebb), amellyel tesztelheti a kódját vagy indítsa el a korai fejlesztési.
* Fájlok, például a Word és PowerPoint-fájlokat tárolja, növelheti a "dokumentumok" mappában mérete közel teljes értékesítése miatt. Azt javasoljuk, hogy egy együttműködésen alapuló Wiki megtalálhatók [SharePoint](https://products.office.com/sharepoint/collaboration), vagy más együttműködésen alapuló erőforrás az ilyen fájlok tárolására.
* Nagy méretű fájlok és a Machine Learning kimenetek kezelésének módját, olvassa el [módosítások megőrzése, és a nagy méretű fájlok foglalkozó](https://aka.ms/aml-largefiles).

> [!NOTE]
> Minden dokumentáció kapcsolatos tartalom (szöveg, markdowns, képek és egyéb fájlok), amely *nem* projekt végrehajtása során használt, más, mint a readme.md fájl kell lennie, a "docs" (csak kisbetűkkel) nevű mappát. A "dokumentumok" mappában, a Machine Learning végrehajtási figyelmen kívül hagyja, hogy ez a mappa tartalma nem másolódik feleslegesen számítási céljainak speciális mappába. Ebben a mappában lévő objektumok is nem beleszámítanak a 25 MB-ot kap a projekt méretét. A "dokumentumok" mappában, például az a hely, a dokumentáció a szükséges nagy méretű kép fájlok tárolására. Ezek a fájlok továbbra is a futtatási előzmények keresztül Git követi. 

## <a name="instantiate-the-tdsp-structure-and-templates-from-the-machine-learning-template-gallery"></a>Hozza létre a TDSP szerkezetét és a Machine Learning sablongyűjteményből sablonok
A TDSP struktúra és a dokumentáció sablonokkal új projekt létrehozásához hajtsa végre az alábbi eljárásokat.

### <a name="create-a-new-project"></a>Új projekt létrehozása
Hozzon létre egy új projektet, nyissa meg az Azure Machine Learning. A **projektek** a bal felső ablaktáblán válassza a pluszjelet (**+**), majd válassza ki **új projekt**.

![Új projekt](./media/how-to-use-tdsp-in-azure-ml/instantiation-1.png)


### <a name="create-a-new-tdsp-structured-project"></a>Hozzon létre egy új strukturált TDSP projekt
   1. Adja meg a paraméterek és az adatok a megfelelő jelölőnégyzetet, vagy a listán:

      - Projektnév
      - Projektkönyvtár
      - Projekt leírása
      - Egy üres Git-tárház elérési útja
      - Munkaterület neve

   2. Ezt a a **keresési** mezőbe írja be **TDSP**. 
   3. Ha a **projekt strukturálása TDSP** lehetőség jelenik meg, válassza ki a sablont. 
   4. Válassza ki a **létrehozás** TDSP struktúra az új projekt létrehozása gombra. Ha egy üres Git-tárház meg kell adnia, amikor létrehozza a projektet (a megfelelő szövegmezőbe), majd adott tárházba feltöltődik a projektstruktúra és annak tartalmát a projekt létrehozása után.

![TDSP projekt létrehozása](./media/how-to-use-tdsp-in-azure-ml/instantiation-2.png)


## <a name="examine-the-tdsp-project-structure"></a>A TDSP projektstruktúra vizsgálata
Az új projekt létrehozása után ellenőrizheti a struktúra (lásd az alábbi ábrán a bal oldali panelen). Az üzleti igények felmérése szabványos dokumentációját minden szempontját tartalmazza. Ezek az elemek közé tartozik a TDSP életciklusa, az adatok helye, definícióit és az architektúra a dokumentációs sablon fázisa. 

A struktúra látható a TDSP struktúra, a közzétett van származtatva [TDSP projekt struktúra, a dokumentumok és az összetevő sablonok](https://github.com/Azure/Azure-TDSP-ProjectTemplate), az néhány módosítást kell végrehajtani. Például a dokumentum sablonok számos egyesítve egy markdown, nevezetesen, [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport). 

### <a name="project-folder-structure"></a>Projekt mappastruktúra
A TDSP projekt sablon tartalmaz a következő legfelső szintű mappák:
   - **kód**: kódját tartalmazza.
   - **Docs**: a projekt (például a markdown-fájlok és a kapcsolódó media) vonatkozó dokumentációt tartalmazza.
   - **sample_data**: tartalmaz **minta (kicsi)** adatok korai fejlesztési vagy tesztelési célokra használható. Ezen készletek általában nem nagyobb, mint számos (5) MB. Ez a mappa teljes vagy nagy méretű adatkészletek nem tartozik.

![Mintaadatok](./media/how-to-use-tdsp-in-azure-ml/instantiation-3.png)


## <a name="use-the-tdsp-structure-and-templates"></a>A TDSP struktúra és sablonok
A szerkezeti és sablonok projektinformációk hozzá kell. Feltölti ezeket a kódot, és hajtsa végre, és továbbítja a projekthez szükséges információkat az Ön várt. A [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport) fájl egy sablont, amely a projekthez kapcsolódó adatokkal módosítani kell. Együttműködik a kérdésekre, amelyek segítségével adja meg az adatokat az egyes négy fázisát a [TDSP életciklus](../team-data-science-process/lifecycle.md).

Egy olyan projektstruktúra néz végrehajtása során vagy után befejezését a bal oldali panelen az alábbi ábrán látható példát. A projekt származik-e a [csoportos adatelemzési folyamat mintaprojektet: USA népszámlálási adatok az Azure Machine Learning jövedelmek besorolása](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) mintaprojektet.

![Példa projektstruktúra](./media/how-to-use-tdsp-in-azure-ml/instantiation-4.png)

## <a name="document-your-project"></a>Dokumentálja a projekthez
Tekintse meg a [TDSP dokumentáció sablonok](https://github.com/Azure/Azure-TDSP-ProjectTemplate) információk dokumentálni a projekthez. A Machine Learning TDSP dokumentációs sablon az ajánlott az összes adatait a [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport) fájlt. Ez a sablon olyan információkkal, amelyek kifejezetten a projekthez, ki kell tölteni. 

Is biztosítunk egy [ProjectLearnings](https://aka.ms/tdspamlgithubrepoprojectlearnings) sablont. Ez a sablon használatával adjon meg, amely nem szerepel az elsődleges projekt dokumentumot, de ez továbbra is hasznos, ha szeretné, hogy a dokumentum információkat. 

### <a name="example-project-report"></a>A példában a projekt jelentés
Megjelenik egy [példa Projektjelentés](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md). A projekt jelentés a [Egyesült Államokbeli bevételek besorolása mintaprojektet](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) bemutatja, hogyan hozható létre, és a TDSP-sablont használ egy adatelemzési projektjéhez.

## <a name="next-steps"></a>További lépések
Megkönnyítése érdekében a megértése a Machine Learning-projektek TDSP szerkezetét és sablonok használatával, a Machine Learning dokumentációja több befejezett projekt példái kínálunk:

- Ez a minta azt mutatja be TDSP projekt létrehozása a Machine Learning, lásd: [csoportos adatelemzési folyamat mintaprojektet: USA népszámlálási adatok az Azure Machine Learning jövedelmek besorolása](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome).
- A természetes nyelvi feldolgozási (NLP) egy TDSP példányosítása projekt a Machine Learning deep learninget használó minta: [önéletrajz orvosi entitások felismerése természetes nyelvi feldolgozási használatával deep learninget használó](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction).

