---
title: "Gyors fejlesztési tudományos adatok - projektek Azure Machine Learning |} Microsoft Docs"
description: "Hogyan fejlesztők hajthat végre egy tudományos adatprojekthez egy rendszeres, a szabályozott verzióját és az együttműködési módot a projektcsapattal az Team tudományos folyamat használatával."
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
ms.date: 11/28/2017
ms.author: bradsev;
ms.openlocfilehash: 686f751b241d49d116948711c683f4b504d5d5f9
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2017
---
# <a name="agile-development-of-data-science-projects"></a>Az adatok tudományos projektek gyors fejlesztés

Ez a dokumentum ismerteti, hogyan fejlesztők végrehajthat egy tudományos adatprojekthez egy rendszeres, a szabályozott verzióját és az együttműködési módot a projektcsapattal használatával a [Team adatok tudományos folyamat](overview.md) (TDSP). A TDSP egy felhőalapú, a prediktív elemzési megoldások hatékonyan végrehajtandó tevékenységek strukturált sorrendje biztosító Microsoft által kifejlesztett keretrendszert. A személyzet szerepkörök és a kapcsolódó feladatok szabványosításával egy adatok tudományos csapat a folyamatban végrehajtott áttekintését lásd: [Team adatok tudományos folyamat szerepköröket és feladatokat](roles-tasks.md). 

Ez a cikk útmutatást tartalmazza: 

1. Tegye **sprint tervezési** munkaelemekre részt a projektben.<br> Ha nem ismeri a sprint tervezést, adatokat és általános információk található [Itt](https://en.wikipedia.org/wiki/Sprint_(software_development) "Itt"). 
2. **Adja hozzá a munkaelemek** sprints számára. 

> [!NOTE]
> Visual Studio Team Services (VSTS) használatával TDSP csoportos környezetben beállításához szükséges lépéseket az utasítások a következő készletben eljárásokat. Akkor adja meg, hogyan hajthat végre ezeket a feladatokat a VSTS, mivel az a Microsoft TDSP megvalósításához.  Ha VSTS, (3) elemet, és (4) használt a fenti listán szereplő vannak a következő előnyöket természetes. Egy másik kódot platform üzemeltető csoport használata esetén a feladatokat, amelyeket teljesíteni a csapat az érdeklődési általában ne módosítsa. De ezek a feladatok úgy a különböző lesz. Például szakaszban hat, az elem **hivatkozásra a Git ág munkaelem**, nem feltétlenül egyszerű, mint az VSTS.
>
>

Verziókövetési munkafolyamat végrehajtási adatok tudományos projekt szükséges, és a következő ábra azt mutatja be egy tipikus sprint tervezési, kódolási:

![1](./media/agile-development/1-project-execute.png)


##  1. <a name='Terminology-1'></a>Terminológia 

Az a TDSP súlyadatainak nyomtatása tervezési keretrendszer, négy gyakran használt típusú **munkaelemek**: **szolgáltatás**, **felhasználói szövegegység**, **feladat**, és **Hiba**. Minden egyes csapatprojekt tart fenn minden munkaelemekre egyetlen hátralék. Nincs várakoztatás alatt egy csapatprojekt Git tárház szinten. Az alábbiakban a definíciójukat:

- **A szolgáltatás**: A szolgáltatás felel meg a projekt engagement. Ügyfelek különböző bevonására minősülnek különböző szolgáltatások. Hasonlóképpen célszerű egy ügyféllel a projekt különböző fázisait tekinti különböző szolgáltatások. Ha úgy dönt, a séma, mint ***ClientName-EngagementName*** a szolgáltatások elnevezéséhez majd könnyen felismerheti a név a projekt/engagement környezetében.
- **Szövegegység**: szövegek, melyek szükségesek ahhoz, hogy végezze el a szolgáltatást (projekt)-végpontok különböző munkaelemek. Szövegegység közé:
    - Adatok beolvasása 
    - Adatok 
    - Szolgáltatások létrehozása
    - Épület modellek
    - Végrehajtott modellek 
    - Modellek átképezési
- **A feladat**: feladatokat is hozzárendelhető kód vagy a dokumentum munkaelemek vagy egyéb tevékenységeket, amelyek egy adott szövegegység befejezéséhez kell elvégezni. Például a szövegegység feladatok *első adatok* lehet:
    -  Az SQL Server hitelesítő adatok beolvasása 
    -  Az SQL Data Warehouse-adatok feltöltését. 
- **Hiba**: hibák általában javítások, a szükséges egy meglévő kód vagy a dokumentum történik, amikor a tevékenység hivatkozik. Ha a hiba okozta szakaszában vagy feladatok rendre hiányzik, akkor eszkaláció szövegegység vagy egy feladatot. 

> [!NOTE]
> Fogalmak vannak tárolt, közös funkciók, szövegek, feladatok és hibák a kód szoftverkezelés (SCM) adattudomány használhatók. Ezek némileg eltér a hagyományos SCM definíciójukat.
>
>

> [!NOTE]
> Adatszakértőkön úgy érezhetik kényelmesebb gyors sablonnal, amely kifejezetten a TDSP életciklusának szakaszait igazodik. Vele szem előtt tervezősablon Agile származtatott sprint létrejött, ahol Epics, szöveg stb helyébe TDSP életciklusának szakaszait vagy substages. Egy gyors sablon létrehozásával kapcsolatos utasításokért lásd: [gyors adatok tudományos folyamat a Visual Studio Online beállítása](agile-development.md#set-up-agile-dsp-6).
>
>

## 2. <a name='SprintPlanning-2'></a>Súlyadatainak nyomtatása tervezése 

Sprint tervezésre is hasznosak lehetnek a projekt rangsorolási és erőforrás-tervezés és lefoglalás. Sok adatszakértőkön részt vevő a hónap befejezéséhez is igénybe vehet, amelyek mindegyike több projektet. Projektek gyakran különböző szóközként, a folytatáshoz. A VSTS-kiszolgálón akkor is könnyen létrehozására, kezelésére, és a munkaelemek nyomon a csapatprojekt és sprint tervezi, győződjön meg arról, hogy a projektek helyezi előre a várt módon végezze el. 

Hajtsa végre a [Ez a hivatkozás](https://www.visualstudio.com/en-us/docs/work/scrum/sprint-planning) sprint VSTS a tervezés részletes útmutatást. 


## 3. <a name='AddFeature-3'></a>A funkció hozzáadása  

A csapatprojekt alatt a projekt tárház létrehozása után nyissa meg a csapat **áttekintése** lapot, és kattintson **munkáját**.

![2](./media/agile-development/2-sprint-team-overview.png)

A várakozó fájlok számát a szolgáltatásnak a szolgáltatással, kattintson a **várakozási sorok** --> **szolgáltatások** --> **új**, írja be a szolgáltatás **cím**(általában a projekt neve), és kattintson a **Hozzáadás** .

![3](./media/agile-development/3-sprint-team-add-work.png)

Kattintson duplán a létrehozott szolgáltatást. Töltse ki a leírásokat, rendelje hozzá a szolgáltatáshoz tartozó csoport tagjai és tervezési a szolgáltatáshoz tartozó paraméterek beállítása. 

Ez a funkció a projekt tárház is társíthatja. Kattintson a **hivatkozás hozzáadása** alatt a **fejlesztési** szakasz. Miután végzett a szolgáltatás szerkesztésével, kattintson **mentés és Bezárás** való kilépéshez.


## 4. <a name='AddStoryunderfeature-4'></a>A szolgáltatás szövegegység hozzáadása 

A funkció a szövegek megadásával írhatja le a (szolgáltatás) projekt befejezéséhez szükséges fő lépéseken lehet hozzáadni. Egy új szövegegység hozzáadásához kattintson a  **+**  jelre a szolgáltatás várakozó nézetben.  

![4](./media/agile-development/4-sprint-add-story.png)

Szerkesztheti a szöveg, például a állapot, leírás, megjegyzések, tervezés és az előugró ablakban prioritású virtuális gép adatait.

![5](./media/agile-development/5-sprint-edit-story.png)

Egy meglévő tárház Ez a cikk a kattintva csatolhatja **+ Hozzáadás hivatkozásra** alatt **fejlesztési**. 

![6](./media/agile-development/6-sprint-link-existing-branch.png)


## 5. <a name='AddTaskunderstory-5'></a>Adja hozzá a feladatot egy története 

Feladatokat is, amely minden szövegegység szükséges részletes lépéseit. Miután egy szövegegység minden feladat befejeződött, a szöveg túl kell végrehajtani. 

Adja hozzá a feladatot egy története, kattintson a  **+**  mellett a Szövegegység elem, jelölje be a bejelentkezési **feladat**, majd adja meg a részletes információkat, ennek a feladatnak az előugró ablakban.

![7](./media/agile-development/7-sprint-add-task.png)

A szolgáltatások, a szövegek és a feladatok létrehozását követően megtekintheti azokat a **várakozó** vagy **Board** nézetek azok állapotának nyomon követéséhez.

![8](./media/agile-development/8-sprint-backlog-view.png)

![9](./media/agile-development/9-link-to-a-new-branch.png)


## 6. <a name='set-up-agile-dsp-6'></a>Állítson be egy gyors TDSP munkahelyi sablont a Visual Studio Online-ban

Ez a cikk ismerteti, hogyan állíthat be egy gyors adatok tudományos folyamatsablont használ a TDSP adatok tudományos életciklusának szakaszait, és nyomon követi a Visual Studio Online (vso) munkaelemek. Az alábbi lépésein végighaladva beállítása az adatok tudományos-specifikus gyors példát lépéseket feldolgozni sablon *AgileDataScienceProcess* és tudományos munkaelemek a sablon alapján létrehozását mutatják be.

### <a name="agile-data-science-process-template-setup"></a>Agilis adatok tudományos folyamat sablon beállítása

1. Navigáljon a kiszolgálón-kezdőlap, **konfigurálása** -> **folyamat**.

    ![10](./media/agile-development/10-settings.png) 

2. Navigáljon a **minden folyamat** -> **folyamatok**a **Agile** , majd kattintson a **létrehozás örökölt folyamat**. Majd a folyamat neve "AgileDataScienceProcess" put, és kattintson az **folyamat létrehozása**.

    ![11](./media/agile-development/11-agileds.png)

3. Az a **AgileDataScienceProcess** -> **munkaelem-típusok** lap, tiltsa le a **Epic**, **szolgáltatás**,  **Felhasználói szövegegység**, és **feladat** munkaelem-típusok által **beállítása -> letiltása**

    ![12](./media/agile-development/12-disable.png)

4. Navigáljon a **AgileDataScienceProcess** -> **szintek tartalék** fülre. "Epics", "TDSP projektek" átnevezéséhez kattintson a a **konfigurálása** -> **szerkesztése/átnevezése**. A azonos párbeszédpanelen kattintson a **+ új munkaelem típusa** "Adatok tudományos projektben", és állítsa be a **alapértelmezett munkaelemtípus** "TDSP projekthez" 

    ![13](./media/agile-development/13-rename.png)  

5. Hasonlóképpen, várakozó neve "Szolgáltatások" "TDSP szakaszból" módosítsa, majd adja hozzá a következőt a **új munkahelyi konfigurációelem-típust**:

    - Az üzleti igények felmérése
    - Adatok beszerzése
    - Modellezés
    - Környezet

6. Nevezze át a "Felhasználói szövegegység" "TDSP Substages" alapértelmezett munkaelemtípus az újonnan létrehozott "TDSP segédalátét" típust adott meg.

7. Állítsa be a "tevékenységek" az újonnan létrehozott munkaelemtípus "TDSP feladat" 

8. Ezeket a lépéseket alatti a várakozó szintek kell kinéznie:

    ![14](./media/agile-development/14-template.png)  

 
### <a name="create-data-science-work-items"></a>Adatok tudományos munkaelemek létrehozása

Az adatok tudományos folyamat sablon létrehozása után hozhat létre és az adatok tudományos munkaelemek, amelyek megfelelnek a TDSP életciklus követik.

1. Amikor létrehoz egy új csapatprojektbe, jelölje ki "Agile\AgileDataScienceProcess" a **munkaelemet bekérő folyamat**:

    ![15](./media/agile-development/15-newproject.png)

2. Keresse meg az újonnan létrehozott csapatprojektben, és kattintson a **munkahelyi** -> **várakozási sorok**.

3. "TDSP projektek" parancsával láthatóvá **team beállításainak konfigurálása** , és ellenőrizze a "TDSP projektek"; mentse.

    ![16](./media/agile-development/16-enabledsprojects.png)

4. Most elindíthatja az adatok tudományos-specifikus munkaelemek létrehozása.

    ![17](./media/agile-development/17-dsworkitems.png)

5. Íme egy példa hogyan jelenjenek meg a tudományos projekt munkaelemek:

    ![18](./media/agile-development/18-workitems.png)


## <a name="next-steps"></a>Következő lépések

[A Git együttműködési kódolási](collaborative-coding-with-git.md) hajtsa végre az adatok tudományos projektek Git használatával, a megosztott kód fejlesztési keretrendszer együttműködési kód fejlesztésére és csatolása ezeket kódolás tevékenységekhez, hogy a gyors folyamattal tervezett munka ismerteti.

Az alábbiakban további, a gyors folyamatokon forrásanyagokra mutató hivatkozásokat tartalmaz.

- Agilis folyamat [https://www.visualstudio.com/en-us/docs/work/guidance/agile-process](https://www.visualstudio.com/en-us/docs/work/guidance/agile-process)
- Agilis folyamat munkaelem-típusok és a munkafolyamat [https://www.visualstudio.com/en-us/docs/work/guidance/agile-process-workflow](https://www.visualstudio.com/en-us/docs/work/guidance/agile-process-workflow)


Forgatókönyvek, amelyek azt mutatják, a folyamat lépései **meghatározott forgatókönyvek** is rendelkezésre állnak. Szerepel a listában, és kapcsolódik a miniatűr leírásokat a [példa forgatókönyvek](walkthroughs.md) cikk. Ezek bemutatják, hogyan lehet felhő, a helyszíni eszközök és szolgáltatások egyesítése munkafolyamat vagy csővezeték intelligens alkalmazás létrehozása. 
