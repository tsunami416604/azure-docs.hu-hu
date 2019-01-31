---
title: Az adatelemzési projektek - csoportos adatelemzési folyamat rugalmas fejlesztés
description: Hogyan fejlesztők is végrehajthat egy a egy szisztematikus adatelemzési projektjéhez, a szabályozott verzió és a egy projektcsapattal remek a csoportos adatelemzési folyamat használatával.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/28/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: adf713fc3f875168f99b302b0a9affef88e8414f
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55457683"
---
# <a name="agile-development-of-data-science-projects"></a>Hatékony adatelemzési projektek fejlesztését

Ez a dokumentum ismerteti, hogyan fejlesztők végrehajthat egy a egy szisztematikus adatelemzési projektjéhez, a szabályozott verzió és a egy projektcsapattal remek használatával a [csoportos adatelemzési folyamat](overview.md) (TDSP). A TDSP egy keretrendszer, amely hatékony felhőalapú, prediktív elemzési megoldások végrehajtásához tevékenységek strukturált sorrendje biztosít a Microsoft által kifejlesztett. A személyzet szerepkörök és hozzájuk kapcsolódó részfeladatokat szabványosításával egy data science csapat ezt a folyamatot a kezelt áttekintését lásd: [csoportos adatelemzési folyamat szerepkörök és feladatok](roles-tasks.md). 

Ez a cikk útmutatást tartalmazza: 

1. Tegye **sprint tervezési** a munkaelemek a projektben szereplő.<br> Ha ismeri a sprint tervezést, Észreveheti, hogy adatait és általános [Itt](https://en.wikipedia.org/wiki/Sprint_(software_development) "Itt"). 
2. **munkaelemek hozzáadása** sprintekben való. 

> [!NOTE]
> Az Azure fejlesztési és üzemeltetési szolgáltatásokat használó TDSP csoportos környezetben beállításához szükséges lépéseket a következő utasításkészlet rendszerkövetelményeknek. Akkor adja meg, hogyan végezheti el ezeket a feladatokat az Azure DevOps-szolgáltatásokkal, mert a Microsoft TDSP megvalósítása.  Ha úgy dönt, hogy az Azure DevOps-szolgáltatásokkal, (3) elemet, és (4) használja a fenti listán szereplő előnyökkel, amely természetesen kap. Egy másik, a üzemeltetési platform kód a csoport használható, ha a feladatokat kell végrehajtani a csapatvezetők általában, ne módosítsa. Azonban ezeket a feladatokat a módja eltérő lesz. Például szakaszban hat, az elem **hivatkozás egy munkaelemet a Git-ágak**, lehetséges, hogy nem olyan egyszerű, mint az Azure DevOps-szolgáltatásokkal.
>
>

Az alábbi ábra egy tipikus sprint megtervezése, kódolási és source-control munkafolyamat vesz részt az adatelemzési projektjéhez megvalósításához:

![1](./media/agile-development/1-project-execute.png)


##  1. <a name='Terminology-1'></a>Terminológia 

A TDSP a tervezési keretrendszer sprint, négy gyakran használt típusú **munkaelemek**: **A szolgáltatás**, **felhasználói történetet blokkol**, **feladat**, és **Bug**. Minden projekt fenntart egy egyetlen mappájában várakozó fájlok számát az összes munkaelem. Git-tárház szintjén egy projekt keretében nincs várakoztatás van. Az alábbiakban a definíciójukat:

- **A szolgáltatás**: Egy projekt engagement felel meg egyik funkciója. Az ügyfél másik marketingmódszerek minősülnek különböző funkcióit. Hasonlóképpen érdemes másik lehetőségként fontolja meg egy projektet az egyes fázisokat futtatni az ügyfél. Ha úgy dönt, a séma például ***ClientName-EngagementName*** , nevezze el a szolgáltatásokat, majd könnyen felismerheti a projekt/engagement magukat a paraméterkészletben kontextusában.
- **Történetet**: Történetek, melyek szükségesek ahhoz, hogy végezze el a szolgáltatást (projekt)-végpontok különböző munkaelemek. Történetek közé:
    - Adatok beolvasása 
    - Adatok feltárása 
    - Funkciók létrehozása
    - Modellek létrehozása
    - Modellek modellezést 
    - Megőrzési modellek
- **A feladat**: Feladatok a következők: rendelhető hozzá a kódot, vagy dokumentum munkaelemek vagy egyéb tevékenységeket, amelyek egy adott történetet elvégzéséhez szükséges. Például a történetet feladatainak *adatok beolvasása* lehet:
    -  Bevezetés az SQL Server hitelesítő adatok 
    -  Az SQL Data warehouse-ba való feltöltéséhez. 
- **Programhiba**: Hibák általában javításcsomagot tartalmaz, amely szükséges ahhoz, hogy egy meglévő kódot vagy a dokumentum, amely minden kész, amikor egy tevékenység befejezése hivatkoznak. Ha a hibát okozta szakaszában vagy feladatok rendre hiányzik, a is eszkalálása egy történetet vagy a feladat indításához. 

> [!NOTE]
> Fogalmak funkciók, történetek, feladatok és-hibák szoftver kód forráskezelési (SCM) használhatók az adatelemzés a hitelt. Ezek némileg eltér a hagyományos SCM definíciójukat.
>
>

> [!NOTE]
> Az adatszakértők úgy otthonosabban egy Agilis sablonnal, amely kifejezetten illeszkedik a TDSP életciklusának szakaszait. Vegye figyelembe, hogy a sablon tervezési Agile-származtatott sprint létrejött, ahol Epics stb. történetek helyébe TDSP életciklusának szakaszai vagy substages. Egy Agilis sablon létrehozásával kapcsolatos útmutatásért lásd: [állítsa be a Visual Studio Online hatékony adatelemzési folyamat](agile-development.md#set-up-agile-dsp-6).
>
>

## 2. <a name='SprintPlanning-2'></a>Sprint tervezése 

Sprint tervezési hasznos projekt rangsorolási, és az erőforrás-tervezés és lefoglalás. Számos adatszakértők több projektet, hónapok végrehajtásához is igénybe vehet, amelyek mindegyike a foglalkoznak. Projektek gyakran különböző szóközként, folytatható. Az Azure DevOps-szolgáltatásokkal, a egyszerűen létrehozása, kezelése, és nyomon követheti a munkaelemek a projekt és viselkedési szabályzattal sprint tervezi, győződjön meg arról, hogy a projektek előre a várt módon helyez át. 

Hajtsa végre a [ezt a hivatkozást](https://www.visualstudio.com/en-us/docs/work/scrum/sprint-planning) sprint tervezése az Azure DevOps-szolgáltatásokkal részletes útmutatást. 


## 3. <a name='AddFeature-3'></a>Funkció hozzáadása  

A csapat lépjen a projektadattárat a projekt létrehozása után **áttekintése** lapot, és kattintson **végzett munka kezeléséhez**.

![2](./media/agile-development/2-sprint-team-overview.png)

Egy szolgáltatás belefoglalni a várakozó fájlok számát, kattintson a **várakozási sorok** --> **funkciók** --> **új**, írja be a szolgáltatás **cím**(általában a projekt neve), és kattintson a **Hozzáadás** .

![3](./media/agile-development/3-sprint-team-add-work.png)

Kattintson duplán a szolgáltatás hozott létre. Töltse ki a leírások, rendelje hozzá a szolgáltatás csapattagok és tervezési ennek a funkciónak a paraméterek beállítása. 

Ez a funkció a projektadattárat is csatolható. Kattintson a **hivatkozás hozzáadása** alatt a **fejlesztési** szakaszban. Miután ezzel végzett, a szolgáltatás szerkesztését, kattintson a **mentés és Bezárás** való kilépéshez.


## 4. <a name='AddStoryunderfeature-4'></a>A szolgáltatás alatt adja hozzá a történetet 

A szolgáltatás alatt történetek is hozzáadhatók (szolgáltatás) projekt létrehozásához szükséges főbb lépéseket ismertetik. Adjon hozzá egy új történetet, kattintson a **+** jelre a funkció a várakozó fájlok számát a nézetben.  

![4](./media/agile-development/4-sprint-add-story.png)

Szerkesztheti a történetet, például állapot, leírás, megjegyzések, tervezési és prioritás a felugró ablakban részleteit.

![5](./media/agile-development/5-sprint-edit-story.png)

Egy meglévő adattárhoz Ez a cikk a kattintva csatolhatja **+ Hozzáadás hivatkozás** alatt **fejlesztési**. 

![6](./media/agile-development/6-sprint-link-existing-branch.png)


## 5. <a name='AddTaskunderstory-5'></a>Feladat hozzáadása egy történetet 

Feladatok olyan egyes történetet végrehajtásához szükséges részletes lépéseit. Miután egy történetet a minden feladat befejeződött, a szöveg túl kell elvégezni. 

Feladat hozzáadása egy történetet, kattintson a **+** mellett a történetet elemre, majd válassza a bejelentkezés **feladat**, és a részletes információkat, ennek a feladatnak az előugró ablakban töltse ki.

![7](./media/agile-development/7-sprint-add-task.png)

A Funkciók, történeteket és feladatok létrehozását követően megtekintheti őket a **várakozó** vagy **tábla** nézetek azok állapotának nyomon követéséhez.

![8](./media/agile-development/8-sprint-backlog-view.png)

![9](./media/agile-development/9-link-to-a-new-branch.png)


## 6. <a name='set-up-agile-dsp-6'></a> Állítson be egy Agilis TDSP munkahelyi sablont a Visual Studio online-ban

Ez a cikk bemutatja, hogyan állítható be egy Agilis data science folyamatsablon TDSP data science életciklusának szakaszai használ, és nyomon követi a munkatételek a Visual Studio Online (vso). Az útmutató egy példa a data science-specifikus Agilis beállítását az alábbi lépéseket a folyamatsablon *AgileDataScienceProcess* és bemutatják, hogyan hozhat létre data science munkaelemek a sablon alapján.

### <a name="agile-data-science-process-template-setup"></a>Agilis adatok adatelemzési folyamat sablon beállítása

1. Keresse meg a kiszolgáló kezdőlap elérését, **konfigurálása** -> **folyamat**.

    ![10](./media/agile-development/10-settings.png) 

2. Navigáljon a **összes folyamat** -> **folyamatok**alatt **Agile** , majd kattintson a **létrehozás örökölt folyamat**. Ezután "AgileDataScienceProcess" folyamat nevét, és kattintson a **létrehozni folyamatot**.

    ![11](./media/agile-development/11-agileds.png)

3. Alatt a **AgileDataScienceProcess** -> **munkaelem-típusok** lapon, tiltsa le a **Epic**, **funkció**,  **Felhasználói történetet blokkol**, és **feladat** munkaelem-típusok szerint **konfigurálása -> letiltása**

    ![12](./media/agile-development/12-disable.png)

4. Navigáljon a **AgileDataScienceProcess** -> **várakozó fájlok a szintek** fülre. Nevezze át a "Epics", "TDSP projekt" parancsával a **konfigurálása** -> **Szerkesztés/átnevezése**. Azonos párbeszédpanelen kattintson a **+ új munkaelem típusa** "Adatelemzési projektjéhez" a, és állítsa az értékét **alapértelmezett munkaelemtípus** "TDSP projekt" 

    ![13](./media/agile-development/13-rename.png)  

5. Ehhez hasonlóan várakozó fájlok számát a neve "Szolgáltatások" módosítsa "TDSP szakaszok", majd adja hozzá a következőt a **új munkahelyi konfigurációelem-típust**:

    - Az üzleti igények felmérése
    - Adatgyűjtés
    - Modellezés
    - Környezet

6. Nevezze át a "Felhasználói történetet blokkol" "TDSP Substages" alapértelmezett munkaelemtípus az újonnan létrehozott "TDSP segédalátét" típusú értékre.

7. Állítsa be a "tevékenységek" az újonnan létrehozott "TDSP feladat" munkaelem-típusok 

8. Ezeket a lépéseket a várakozó szintek kell kinéznie:

    ![14](./media/agile-development/14-template.png)  

 
### <a name="create-data-science-work-items"></a>Data Science munkaelem létrehozása

A data science folyamat sablon létrehozása után hozzon létre, és a data science munkaelemek, amelyek megfelelnek a TDSP életciklusának nyomon követésére.

1. Amikor egy új projektet hoz létre, válassza a "Agile\AgileDataScienceProcess" a **munkaelemet bekérő folyamat**:

    ![15](./media/agile-development/15-newproject.png)

2. Keresse meg az újonnan létrehozott projektre, és kattintson a **munkahelyi** -> **sorait**.

3. Kattintson a "TDSP projekt" láthatóvá **team beállításainak konfigurálása** és ellenőrzése "TDSP projekt", majd mentse.

    ![16](./media/agile-development/16-enabledsprojects.png)

4. Most már megkezdheti a data science-specifikus munkaelemek létrehozása.

    ![17](./media/agile-development/17-dsworkitems.png)

5. Íme egy példa hogyan jelenjenek meg a data science projekt munkaelemek:

    ![18](./media/agile-development/18-workitems.png)


## <a name="next-steps"></a>További lépések

[Együttműködésen alapuló kódolás a Gittel](collaborative-coding-with-git.md) hajtsa végre az adatelemzési projektek készítése a Git használatával, a megosztott kód fejlesztési keretrendszer által biztosított együttműködési környezettel kód fejlesztési és összekapcsolhatók a munkát, az Agilis folyamattal tervezett tevékenységek kódolási ismerteti.

Az alábbiakban Agilis folyamatok erőforrásokhoz további hivatkozásokat tartalmaz.

- Agilis   [https://www.visualstudio.com/en-us/docs/work/guidance/agile-process](https://www.visualstudio.com/en-us/docs/work/guidance/agile-process)
- Agilis munkaelem-típusok és munkafolyamat   [https://www.visualstudio.com/en-us/docs/work/guidance/agile-process-workflow](https://www.visualstudio.com/en-us/docs/work/guidance/agile-process-workflow)


Forgatókönyvek, amelyek bemutatják, a folyamat összes lépését **meghatározott forgatókönyvek** is rendelkezésre állnak. Felsorolt, és a miniatűr leírásokat tartalmazó társított a [példa forgatókönyvek](walkthroughs.md) cikk. Ezek bemutatják, hogyan kombinálhatja a felhőbeli, helyszíni eszközöket és szolgáltatásokat, munkafolyamat vagy folyamat, intelligens alkalmazások létrehozására. 
