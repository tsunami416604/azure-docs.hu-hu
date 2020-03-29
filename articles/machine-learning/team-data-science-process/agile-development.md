---
title: Adatelemzési projektek agilis fejlesztése - Team Data Science Process
description: Egy adatelemzési projekt végrehajtása egy szisztematikus, verzióvezérelt és együttműködésen belül a projekt csapat segítségével a team data science folyamat.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c097c14406349d973e905fadb806cc159d9b16d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722101"
---
# <a name="agile-development-of-data-science-projects"></a>Adatelemzési projektek agilis fejlesztése

Ez a dokumentum leírja, hogy a fejlesztők hogyan hajthatnak végre egy adatelemzési projektet egy szisztematikus, verzióvezérelt és együttműködésen alapuló módon egy projektcsapaton belül a [Team Data Science Process](overview.md) (TDSP) használatával. A TDSP a Microsoft által kifejlesztett keretrendszer, amely strukturált tevékenységsorozatot biztosít a felhőalapú, prediktív elemzési megoldások hatékony végrehajtásához. A TDSP-re szabványosító adatelemzési csapat által kezelt szerepkörök és feladatok vázlatát a [Csapatadat-elemzési folyamat szerepkörei és feladatai című témakörben található.](roles-tasks.md) 

Ez a cikk a következőkre vonatkozó utasításokat tartalmazza: 

- Sprint *tervezés* a projektben részt vevő munkaelemekhez.
- *Munkaelemek* hozzáadása a sprintekhez.
- Hozzon létre és használjon *olyan agilis eredetű munkaelem-sablont,* amely kifejezetten igazodik a TDSP életciklus-szakaszaihoz.

Az alábbi utasítások ismertetik a TDSP-csapatkörnyezet beállításához szükséges lépéseket az Azure Boards és az Azure Repos azure DevOps használatával. A utasítások az Azure DevOps-t használják, mert így valósíthatja meg a TDSP-t a Microsoftnál. Ha a csoport más kódüzemeltetési platformot használ, a csapatvezető feladatok általában nem változnak, de a feladatok elvégzésének módja eltérő. Például egy munkaelem összekapcsolása egy Git-ág nem lehet ugyanaz a GitHub, mint az Azure-repos.

Az alábbi ábra egy adatelemzési projekt tipikus sprinttervezési, kódolási és forrásellenőrzési munkafolyamatát mutatja be:

![Csoportos adatelemzési folyamat](./media/agile-development/1-project-execute.png)

##  <a name="work-item-types"></a><a name='Terminology-1'></a>Munkaelem-típusok

A TDSP sprint tervezési keretrendszerében négy gyakran használt *munkaelem-típus* létezik: *Funkciók*, *Felhasználói történetek*, *Feladatok*és *Hibák.* Az összes munkaelem hátraléka a projekt szintjén van, nem pedig a Git-tárház szintjén. 

A munkaelemtípusok definíciói:

- **Funkció:** A funkció projektelköteleződésnek felel meg. Az ügyféllel való különböző elköteleződések különböző funkciók, és a legjobb, ha a projekt különböző fázisait különböző funkcióknak tekinti. Ha olyan sémát választ, mint * \<\<például clientname>- EngagementName>* a szolgáltatások elnevezéséhez, könnyen felismerheti a projekt környezetét és az elköteleződést magukból a nevekből.
  
- **Felhasználói történet:** A felhasználói történetek olyan munkaelemek, amelyek a funkció teljes körű befejezéséhez szükségesek. Példák a felhasználói történetekre:
  - Adatok lekérése 
  - Adatok megismerése 
  - Szolgáltatások létrehozása
  - Modellek létrehozása
  - Modellek üzembe helyezése 
  - Modellek újratanítása
  
- **Feladat:** A feladatok olyan hozzárendelhető munkaelemek, amelyeket el kell végezni egy adott felhasználói történet befejezéséhez. A Felhasználói történet ben lévő feladatok *például* a következők lehetnek:
  - SQL Server hitelesítő adatok beszerezése
  - Adatok feltöltése az SQL Data Warehouse-ba
  
- **Hiba**: A hibák olyan problémák a meglévő kódban vagy dokumentumokban, amelyeket rögzíteni kell egy feladat elvégzéséhez. Ha a hibákat hiányzó munkaelemek okozzák, akkor felhasználói történetek vagy feladatok kábulhatnak ki. 

Az adatszakértők jobban érezhetik magukat egy agilis sablon használatával, amely a Funkciókat, a Felhasználói történeteket és a Feladatokat a TDSP életciklus-szakaszaival és alfázisaival helyettesíti. Ha olyan agilis eredetű sablont szeretne létrehozni, amely kifejezetten a TDSP életciklus-szakaszaihoz igazodik, olvassa el az [Agilis TDSP munkasablon használata című témakört.](#set-up-agile-dsp-6)

> [!NOTE]
> A TDSP a szolgáltatások, a felhasználói történetek, a feladatok és a hibák fogalmait kölcsönzi a szoftverkód-kezelésből (SCM). A TDSP fogalmak némileg eltérhetnek a hagyományos SCM-definícióktól.

## <a name="plan-sprints"></a><a name='SprintPlanning-2'></a>Sprintek tervezése

Számos adattudós több projekttel foglalkozik, amelyek végrehajtásához hónapokba telhet, és különböző ütemben halad. A sprinttervezés a projekt rangsorolásához, valamint az erőforrás-tervezéshez és -elosztáshoz hasznos. Az Azure Boardsban egyszerűen létrehozhat, kezelhet és nyomon követheti a projektek munkaelemeit, és sprinttervezést végezhet annak biztosítása érdekében, hogy a projektek a várt módon haladjanak előre.

A sprinttervezésről további információt a Scrum sprintek című [témakörben talál.](https://en.wikipedia.org/wiki/Scrum_(software_development)#Sprint) 

A sprinttervezésről az Azure Boardsban a [Hátralévő elemek hozzárendelése sprinthez című](/azure/devops/boards/sprints/assign-work-sprint)témakörben talál további információt. 

## <a name="add-a-feature-to-the-backlog"></a><a name='AddFeature-3'></a>Szolgáltatás hozzáadása a hátralékhoz 

A projekt- és projektkódtár létrehozása után hozzáadhat egy funkciót a hátralékhoz, hogy képviselje a projekt munkáját.

1. A projektlapon válassza a Bal oldali navigációs sáv > **OnBoardBacklogs** **lehetőséget.** 
   
1. Ha a **Backlog** lapon a munkaelem típusa a felső sávon **a Szövegegységek**, legördülő menü legördülő menü **legördülő menüJének A Funkciók**lehetőséget válassza. Ezután válassza az **Új munkaelem lehetőséget.**
   
   ![Új munkaelem kiválasztása](./media/agile-development/2-sprint-team-overview.png)
   
1. Adja meg a szolgáltatás címét, általában a projekt nevét, majd válassza **a Hozzáadás a lap tetejére**lehetőséget. 
   
   ![Írjon be egy címet, és válassza a Hozzáadás a lap tetejére lehetőséget.](./media/agile-development/3-sprint-team-add-work.png)
   
1. A **Backlog** listából jelölje ki és nyissa meg az új funkciót. Töltse ki a leírást, rendeljen hozzá egy csapattagot, és állítsa be a tervezési paramétereket. 
   
   A funkciót a projekt Azure Repos kódtárházához is csatolhatja, ha a Fejlesztés szakaszban a **Hivatkozás hozzáadása** lehetőséget **választja.** 
   
   A funkció szerkesztése után válassza a **Mentés & bezárás**a lehetőséget.
   
   ![Szolgáltatás szerkesztése és a Bezárás & mentése](./media/agile-development/3a-add-link-repo.png)

## <a name="add-a-user-story-to-the-feature"></a><a name='AddStoryunderfeature-4'></a>Felhasználói szövegegység hozzáadása a szolgáltatáshoz 

A Funkció alatt hozzáadhat felhasználói történeteket a projekt befejezéséhez szükséges főbb lépések leírásához. 

Új felhasználói történet hozzáadása egy funkcióhoz:

1. A **Backlog** lapon válassza **+** a Funkció tól balra lévő elemet. 
   
   ![Új felhasználói szövegegység hozzáadása a funkció alatt](./media/agile-development/4-sprint-add-story.png)
   
1. Adjon címet a felhasználói történetnek, és szerkesztse az olyan részleteket, mint a hozzárendelés, az állapot, a leírás, a megjegyzések, a tervezés és a prioritás. 
   
   A felhasználói történet a projekt Azure Repos-kódtárának egyik ágához is csatolhatja, ha a Fejlesztés szakaszban a **Hivatkozás hozzáadása** lehetőséget **választja.** Jelölje ki azt a tárházat és elágazást, amelyhez a munkaelemet csatolni szeretné, majd kattintson az **OK gombra.**
   
   ![Hivatkozás hozzáadása](./media/agile-development/5-sprint-edit-story.png)
   
1. Ha befejezte a Felhasználói szövegegység szerkesztését, válassza **a Mentés & bezárás lehetőséget.** 

## <a name="add-a-task-to-a-user-story"></a><a name='AddTaskunderstory-5'></a>Feladat hozzáadása felhasználói szövegegységhez 

A feladatok az egyes felhasználói történetek végrehajtásához szükséges részletes lépések. Miután egy felhasználói történet összes feladata befejeződött, a felhasználói történetet is be kell fejezni. 

Ha feladatot szeretne hozzáadni egy felhasználói **+** szövegegységhez, jelölje ki a Felhasználói szövegegység elem melletti elemet, és válassza a **Feladat lehetőséget.** Töltse ki a címet és egyéb információkat a Feladatban.

![Feladat hozzáadása felhasználói szövegegységhez](./media/agile-development/7-sprint-add-task.png)

Miután létrehozta a Funkciókat, a Felhasználói történeteket és a Feladatokat, megtekintheti őket a **Backlogs** vagy a **Boards** nézetben, hogy nyomon követhesse az állapotukat.

![Hátralékok nézet](./media/agile-development/8-sprint-backlog-view.png)

![Táblák nézet](./media/agile-development/8a-sprint-board-view.png)

## <a name="use-an-agile-tdsp-work-template"></a><a name='set-up-agile-dsp-6'></a>Agilis TDSP munkasablon használata

Az adatszakértők jobban érezhetik magukat egy agilis sablon használatával, amely a Funkciókat, a Felhasználói történeteket és a Feladatokat a TDSP életciklus-szakaszaival és alfázisaival helyettesíti. Az Azure Boardsban létrehozhat egy agilis származtatott sablont, amely a TDSP életciklus-szakaszait használja a munkaelemek létrehozásához és nyomon követéséhez. A következő lépések séta egy adattudomány-specifikus agilis folyamatsablon beállítása és a sablon on alapuló adatelemzési munkaelemek létrehozása.

### <a name="set-up-an-agile-data-science-process-template"></a>Agilis adatelemzési folyamatsablon beállítása

1. Az Azure DevOps szervezeti főlapján válassza **a Szervezet beállításait** a bal oldali navigációs sávon. 
   
1. A **Szervezeti beállítások** bal oldali navigációs lapon a **Táblák csoportban**válassza a **Folyamat lehetőséget.** 
   
1. A **Minden folyamat** ablaktáblán jelölje ki az **Agilis**melletti **...** elemet, majd az Örökölt folyamat **létrehozása lehetőséget.**
   
   ![Örökölt folyamat létrehozása az Agile-ból](./media/agile-development/10-settings.png) 
   
1. Az Örökölt folyamat létrehozása az **Agilis párbeszédpanelen** írja be az *AgileDataScienceProcess*nevet, és válassza a **Folyamat létrehozása lehetőséget.**
   
   ![AgileDataScienceProcess folyamat létrehozása](./media/agile-development/11-agileds.png)
   
1. A **Minden folyamatban**válassza ki az új **AgileDataScienceProcess -t.** 
   
1. A **Munka elemtípusok** lapon tiltsa le az **Epic**, **Feature**, **User Story**és **Task elemet,** ha kiválasztja a **...** elemet az egyes elemek mellett, majd a **Letiltás lehetőséget.** 
   
   ![Munkaelemtípusok letiltása](./media/agile-development/12-disable.png)
   
1. A **Minden folyamatban**válassza a **Backlog szintek (Backlog levels) (Visszamaradási szintek)** lapot. A **Portfóliók elmaradása**csoportban válassza a **...** elemet az **Epic (letiltva)** mellett, majd a **Szerkesztés/átnevezés lehetőséget.** 
   
1. A **Hátralékszint szerkesztése** párbeszédpanelen:
   1. **Név**alatt cserélje le **az Epic-et** a *TDSP-projektekre.* 
   1. A **Hátralékszint Munka cikktípusai csoportban**válassza az **Új munkaelemtípus**lehetőséget, írja be a *TDSP Project*értéket, és válassza a **Hozzáadás**lehetőséget. 
   1. Az **Alapértelmezett munkaelemtípus csoportban**válassza a **TDSP Project**lehetőséget. 
   1. Kattintson a **Mentés** gombra.
   
   ![Portfólióhátralék szintjének beállítása](./media/agile-development/13-rename.png)  
   
1. Ugyanezeket a lépéseket követve nevezze át a **szolgáltatásokat** *TDSP-szakaszokra,* és adja hozzá a következő új munkaelemtípusokat:
   
   - *Üzleti megértés*
   - *Adatgyűjtés*
   - *Modellezés*
   - *Környezet*
   
1. A **Követelmény hátralék csoportban**nevezze át a **Stories-t** *TDSP alszakaszokra*, adja hozzá a *TDSP alszakasz*új munkaelemtípusát , és állítsa az alapértelmezett munkaelem-típust **TDSP alszakaszra**.
   
1. Az **Iteration hátralék**területen vegyen fel egy új munkaelemtípust *TDSP-feladat*ként, és állítsa be alapértelmezett munkaelem-típusként. 
   
A lépések elvégzése után a hátralékszinteknek a következőkre kell kitűnniük:
   
 ![TDSP-sablon hátralékszintjei](./media/agile-development/14-template.png)  

### <a name="create-agile-data-science-process-work-items"></a>Agilis adatelemzési folyamat munkaelemeinek létrehozása

Az adatelemzési folyamat sablon nal TDSP-projekteket hozhat létre, és nyomon követheti a TDSP életciklus-szakaszainak megfelelő munkaelemeket.

1. Az Azure DevOps szervezeti főlapján válassza az **Új projekt**lehetőséget. 
   
1. Az **Új projekt létrehozása** párbeszédpanelen adjon nevet a projektnek, majd válassza a **Speciális lehetőséget.** 
   
1. A **Munkaelem folyamata csoportban**válassza az **AgileDataScienceProcess**lehetőséget, majd válassza a **Létrehozás**lehetőséget.
   
   ![TDSP-projekt létrehozása](./media/agile-development/15-newproject.png)
   
1. Az újonnan létrehozott projektben válassza a Bal oldali navigációs sávon a > **Visszamaradások** **tábla**lehetőséget.
   
1. Ha láthatóvá szeretné tenni a TDSP-projekteket, kattintson a **Csapatbeállítások konfigurálása** ikonra. A **Beállítások** képernyőn jelölje be a **TDSP-projektek** jelölőnégyzetet, majd kattintson a **Mentés és bezárás gombra.**
   
   ![A TDSP-projektek jelölőnégyzet bejelölése](./media/agile-development/16-enabledsprojects1.png)
   
1. Adatelemzési adatelemzési tdsp-projekt létrehozásához válassza a felső sáv **TDSP-projektje** lehetőséget, majd az **Új munkaelem**lehetőséget. 
   
1. Az előugró ablakban adjon nevet a TDSP Project munkaelemnek, és válassza **a Hozzáadás a lap tetejére**lehetőséget.
   
   ![Adatelemzési projekt munkaelemének létrehozása](./media/agile-development/17-dsworkitems0.png)
   
1. Ha munkaelemet szeretne hozzáadni a TDSP-projekt alá, jelölje ki a **+** projekt melletti elemet, majd válassza ki a létrehozandó munkaelem típusát. 
   
   ![Adatelemzési munkaelemtípus kiválasztása](./media/agile-development/17-dsworkitems1.png)
   
1. Töltse ki a részleteket az új munkaelemben, és válassza a Mentés & bezárás a **gombot.**
   
1. Folytassa a **+** munkaelemek melletti szimbólumok kiválasztását új TDSP-szakaszok, alszakaszok és feladatok hozzáadásához. 
   
Íme egy példa arra, hogyan jelenjenek meg az adatelemzési projekt munkaelemei a **Backlogs** nézetben:

![18](./media/agile-development/18-workitems1.png)


## <a name="next-steps"></a>További lépések

[A Git-tel végzett együttműködésen alapuló kódolás](collaborative-coding-with-git.md) leírja, hogyan lehet együttműködésen alapuló kódfejlesztést végezni az adatelemzési projektekhez a Git használatával a megosztott kódfejlesztési keretrendszer ként, és hogyan kapcsolható össze ezek a kódolási tevékenységek az agilis folyamattal tervezett munkával.

[Példa forgatókönyvek](walkthroughs.md) listák forgatókönyvek, a hivatkozások és a miniatűr leírások. A kapcsolódó forgatókönyvek bemutatják, hogyan kombinálhatja a felhőbeli és helyszíni eszközöket és szolgáltatásokat munkafolyamatokban vagy folyamatokban intelligens alkalmazások létrehozásához.
  
További források az agilis folyamatokhoz:

- [Agilis folyamat](/azure/devops/boards/work-items/guidance/agile-process)
  
- [Agilis folyamat munkaelem-típusok és munkafolyamat](/azure/devops/boards/work-items/guidance/agile-process-workflow)

