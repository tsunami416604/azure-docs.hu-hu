---
title: "A csapat adatok tudományos folyamatsablont-projektek szerkezeti |} Microsoft Docs"
description: "Hogyan hozható létre csoport adatok tudományos folyamat (TDSP) sablonok az Azure Machine Learning, amely az együttműködési projektek struktúra"
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
ms.date: 10/16/2017
ms.author: bradsev
ms.openlocfilehash: efb681b85d3d7434e3114b8576abc64d00891f03
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2017
---
# <a name="structure-projects-with-the-team-data-science-process-template"></a>A csapat adatok tudományos folyamatsablont struktúra-projektek

Ez a dokumentum útmutatás adatok tudományos projektek létrehozása az Azure Machine Learning Team adatok tudományos folyamat (TDSP) sablonokkal. Ezek a sablonok segítségével való együttműködés és reprodukálhatósági struktúra projektek. 


## <a name="what-is-the-team-data-science-process"></a>Mi az az Team tudományos folyamat?
A TDSP egy gyors, fokozatos, adatok tudományos folyamat végrehajtása és a speciális elemzési megoldásokat kézbesítéséhez. Úgy van kialakítva, együttműködés és adatok tudományos csapatok vállalati szervezetekben hatékonyságának javítása érdekében. A négy fő összetevőből ezen célkitűzések támogatja:

   * Szabványos [adatok tudományos életciklus](../team-data-science-process/lifecycle.md) definíciója.
   * Szabványos szerkezetének, [projektek dokumentációját, és a jelentési sablonok](https://github.com/Azure/Azure-TDSP-ProjectTemplate).
   * Az infrastruktúra és az erőforrások projekt végrehajtásra, mint például a, a számítási és tárolási infrastruktúra és kód tárházak találhatók.
   * [Eszközök és segédprogramok](https://github.com/Azure/Azure-TDSP-Utilities) adattudomány projekt feladatok, például:
      - Együttműködési verziókezelést
      - Kód áttekintése
      - Az adatok feltárása és modellezési
      - Munkahelyi tervezése

A TDSP bővebb leírását, tekintse meg a [Team adatok tudományos folyamat áttekintése](../team-data-science-process/overview.md).

## <a name="why-should-you-use-the-tdsp-structure-and-templates"></a>Miért kell használni a TDSP struktúra és sablonok?
A struktúra, életciklusának és az adatok tudományos projektek dokumentáció szabványosítás az adatok tudományos csoportok hatékony együttműködés elősegítése kulcsát. Hozzon létre a machine learning projektek ilyen keretet biztosít a koordinált alkották TDSP sablonnal.

Korábban megjelent egy [GitHub-tárházban TDSP szerkezetének és sablonok](https://github.com/Azure/Azure-TDSP-ProjectTemplate) e célok elérése érdekében. De nem volt lehetséges, eddig TDSP felépítését és sablonok belül a tudományos eszköz elindítását. Már lehetséges, hogy a TDSP struktúra és dokumentáció található sablonok példányosítja gépi tanulás projekt létrehozásához. 

## <a name="things-to-note-before-creating-a-new-project"></a>Új projekt létrehozása előtt ügyeljen a következőkre
Az alábbi elemek áttekintésével *előtt* új projekt létrehozása:
* Tekintse át a TDSP Machine Learning [sablon](https://aka.ms/tdspamlgithubrepo).
* (Kívül mit már jelen a "dokumentumok" mappában) tartalmát 25 MB-nál kevesebb lehet szükséges. Lásd az ebben a listában követő megjegyzést.
* A minta\_Adatmappa van csak a kisméretű fájlok (5 MB-nál kevesebb), amellyel tesztelheti a kódját vagy korai fejlesztési elindításához.
* Fájlok, például a Word és PowerPoint-fájlokat tárolja, növelheti a "dokumentumok" mappa méretét jelentősen. Javasoljuk, hogy egy közös Wiki megtalálhatja [SharePoint](https://products.office.com/en-us/sharepoint/collaboration), vagy más együttműködési erőforrás ilyen fájlok tárolásához.
* Nagy méretű fájlok és a gépi tanulás kimenetek kezelésének módját, olvassa el [módosítások megőrzése, és nagy méretű fájlok foglalkozó](http://aka.ms/aml-largefiles).

> [!NOTE]
> Minden dokumentációhoz kapcsolódó tartalom (text, markdowns, képek és egyéb fájlok), amely *nem* használt projekt végrehajtása közben, a readme.md fájl eltérő mappában kell lennie a "dokumentumok" (összes, kisbetű) nevű. A "dokumentumok" mappa nem a Machine Learning végrehajtási figyelmen kívül hagyja, hogy ez a mappa tartalmát nem másolódnak a célok feleslegesen kiszámításához speciális mappába. Ebben a mappában lévő objektumok is nem számítanak a projekt méretének 25 MB kap felé. A "dokumentumok" mappa például az a hely, a dokumentáció szükség nagy méretű kép fájlok tárolásához. Ezek a fájlok továbbra is kötetblokkok Git keresztül futtatási előzményeit. 

## <a name="instantiate-the-tdsp-structure-and-templates-from-the-machine-learning-template-gallery"></a>A TDSP struktúra és a gépi tanulás sablon gyűjteményből sablonok példányosítható
Új projekt létrehozása a TDSP struktúra és dokumentáció található sablonok, végezze el az alábbi eljárásokat.

### <a name="create-a-new-project"></a>Új projekt létrehozása
Hozzon létre egy új projektet, nyissa meg az Azure Machine Learning. A **projektek** a bal felső ablaktáblán jelölje ki a plusz jelre (**+**), majd válassza ki **új projekt**.

![Új projekt](./media/how-to-use-tdsp-in-azure-ml/instantiation-1.png)


### <a name="create-a-new-tdsp-structured-project"></a>Új TDSP strukturált projekt létrehozása
   1. Adja meg a paraméterek és az adatok a megfelelő jelölőnégyzet vagy listáját:

      - Projekt neve
      - Projekt könyvtárában
      - Projekt leírása
      - Egy üres Git-tárház elérési útja
      - Munkaterület neve

   2. Ezt a a **keresési** adja meg a **TDSP**. 
   3. Ha a **TDSP projekt szerkezeti** beállítás úgy jelenik meg, válassza ki, hogy a sablon. 
   4. Válassza ki a **létrehozása** TDSP struktúrával az új projekt létrehozása gombra. Ha megad egy üres Git-tárház (a megfelelő mezőbe) a projekt létrehozásakor, majd a tárház feltöltődik a projekt struktúra és a tartalmakat a projekt létrehozása után.

![TDSP-projekt létrehozása](./media/how-to-use-tdsp-in-azure-ml/instantiation-2.png)


## <a name="examine-the-tdsp-project-structure"></a>Vizsgálja meg a TDSP projekt struktúra
Az új projekt létrehozása után ellenőrizheti a struktúra (a bal oldali panelen az alábbi ábrán látható). Az üzleti ismertetése szabványosított dokumentációjában szempontjaival tartalmaz. Ezek az elemek közé tartozik a a TDSP életciklusát, az adatok helye, a definíciók és a dokumentációs sablon architektúrájának szakaszában. 

Bemutatott struktúra származik a közzétett TDSP struktúra [TDSP struktúra, a dokumentumok és az összetevő projektsablonjai](https://github.com/Azure/Azure-TDSP-ProjectTemplate), néhány módosításokkal. Például a dokumentum sablonok számos egyesülnek egy markdown, nevezetesen [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport). 

### <a name="project-folder-structure"></a>Projekt mappaszerkezet
A TDSP projektsablon tartalmazza a következő legfelső szintű mappák:
   - **kód**: olyan kódot tartalmaz.
   - **dokumentumok**: szükséges dokumentációjában, a projekt (például a markdown-fájlokat és a kapcsolódó media) tartalmazza.
   - **sample_data**: tartalmaz **minta (kicsi)** korai fejlesztési vagy tesztelési használható adatokat. Ezeket a kiszolgálócsoportokat általában nem több (5) nagyobb MB. Ez a mappa nem a teljes vagy nagy adatkészletek van.

![Mintaadatok](./media/how-to-use-tdsp-in-azure-ml/instantiation-3.png)


## <a name="use-the-tdsp-structure-and-templates"></a>A TDSP struktúra és sablonok
Kell hozzáadnia a projekt-specifikus adatait. a struktúra és a sablonokat. Ezek a kóddal és hajtható végre, és a projekt biztosításához szükséges adatok feltöltése van várt. A [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport) fájl egy sablont, módosítania kell a projekttel adatokkal. Az tartalmaz egy kérdést, amelyek segítenek az adatok az egyes négy szakaszainak a [TDSP életciklus](../team-data-science-process/lifecycle.md).

Egy szerkezetének néz végrehajtása közben vagy után a létrehozása után a bal oldali panelen az alábbi ábrán látható példát. Ez a projekt már a a [Team adatok tudományos folyamat mintaprojektet: az Azure Machine Learning USA nyilvántartásba adatokból bevételek besorolása](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) mintaprojektet.

![Példa projekt struktúra](./media/how-to-use-tdsp-in-azure-ml/instantiation-4.png)

## <a name="document-your-project"></a>Dokumentálja a projekthez
Tekintse meg a [TDSP dokumentáció sablonok](https://github.com/Azure/Azure-TDSP-ProjectTemplate) a projekt dokumentálása olvashat. A Machine Learning TDSP dokumentációs sablon azt javasoljuk, hogy tartalmazza-e az összes adatot a [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport) fájlt. Ez a sablon a projekt vonatkozó információkkal ki kell tölteni. 

Azt adja meg a [ProjectLearnings](https://aka.ms/tdspamlgithubrepoprojectlearnings) sablont. Ez a sablon használatával, amely nem szerepel az elsődleges projekt dokumentumot, de továbbra is hasznosak dokumentálása semmilyen információt tartalmaz. 

### <a name="example-project-report"></a>Példa a projekt jelentés
Kaphat egy [példa projekt jelentés](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md). Ez a projekt a jelentés a [USA bevétel besorolás mintaprojektet](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) bemutatja, hogyan hozható létre, és a TDSP sablon használata az adatok tudományos projekt.

## <a name="next-steps"></a>Következő lépések
A Machine Learning-projektek a TDSP struktúra és a sablonok használatával megértését elősegítő nyújtunk a Machine Learning dokumentációjában több befejezett projekt példa:

- Ez a minta bemutatja, hogyan TDSP-projekt létrehozása a Machine Learning, lásd: [Team adatok tudományos folyamat mintaprojektet: az Azure Machine Learning Velünk nyilvántartásba adatokból bevételek besorolása](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome).
- A Machine Learning TDSP-példányként létrehozott projektben (NLP) feldolgozási természetes nyelven mély tanulási használó mintaalkalmazás, lásd: [életrajza orvosi entitás felismerés mély tanulási feldolgozáson természetes nyelvezetet használva](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction).

