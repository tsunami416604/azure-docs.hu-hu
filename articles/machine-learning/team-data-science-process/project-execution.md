---
title: "Tudományos adatok - projektek Azure végrehajtásának |} Microsoft Docs"
description: "Hogyan egy adatok tudósok is végre lehet hajtani egy adatok tudományos projektet egy trackable szabályozott verzióját és együttműködési módot."
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
ms.date: 11/16/2017
ms.author: bradsev;
ms.openlocfilehash: 1015a9f24ca2c175ff367b1748f05bb3e464457f
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2017
---
# <a name="execution-of-data-science-projects"></a>A végrehajtási adatok tudományos projektek

Ez a dokumentum ismerteti, hogyan fejlesztők végrehajthat egy tudományos adatprojekthez egy rendszeres, a szabályozott verzióját és az együttműködési módot a projektcsapattal használatával a [Team adatok tudományos folyamat](overview.md) (TDSP). A TDSP egy felhőalapú, a prediktív elemzési megoldások hatékonyan végrehajtandó tevékenységek strukturált sorrendje biztosító Microsoft által kifejlesztett keretrendszert. A személyzet szerepkörök és a kapcsolódó feladatok szabványosításával egy adatok tudományos csapat a folyamatban végrehajtott áttekintését lásd: [Team adatok tudományos folyamat szerepköröket és feladatokat](roles-tasks.md). 

Ez a cikk útmutatást tartalmazza: 

1. Tegye **sprint tervezési** munkaelemekre részt a projektben.<br> Ha nem ismeri a sprint tervezést, adatokat és általános információk található [Itt](https://en.wikipedia.org/wiki/Sprint_(software_development) "Itt"). 
2. **Adja hozzá a munkaelemek** sprints számára.
3. **a tevékenységek kódolási munkaelemekben** git követi.
4. Tegye **felülvizsgálati code**. 

> [!NOTE]
> Visual Studio Team Services (VSTS) használatával TDSP csoportos környezetben beállításához szükséges lépéseket az utasítások a következő készletben eljárásokat. Akkor adja meg, hogyan hajthat végre ezeket a feladatokat a VSTS, mivel az a Microsoft TDSP megvalósításához.  Ha VSTS, (3) elemet, és (4) használt a fenti listán szereplő vannak a következő előnyöket természetes. Egy másik kódot platform üzemeltető csoport használata esetén a feladatokat, amelyeket teljesíteni a csapat az érdeklődési általában ne módosítsa. De ezek a feladatok úgy a különböző lesz. Például szakaszban hat, az elem **hivatkozásra a git ág munkaelem**, nem feltétlenül egyszerű, mint az VSTS.
>
>

Verziókövetési munkafolyamat végrehajtási adatok tudományos projekt szükséges, és a következő ábra azt mutatja be egy tipikus sprint tervezési, kódolási:

![1](./media/project-execution/project-execution-1-project-execute.png)


##  1. <a name='Terminology-1'></a>Terminológia 

Az a TDSP súlyadatainak nyomtatása tervezési keretrendszer, négy gyakran használt típusú **munkaelemek**: **szolgáltatás**, **felhasználói szövegegység**, **feladat**, és **Hiba**. Minden egyes csapatprojekt tart fenn minden munkaelemekre egyetlen hátralék. Nincs várakoztatás alatt egy csapatprojekt git tárház szinten. Az alábbiakban a definíciójukat:

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

Adatszakértőkön úgy érezhetik kényelmesebb gyors sablonnal, amely kifejezetten a TDSP életciklusának szakaszait igazodik. Vele szem előtt tervezősablon Agile származtatott sprint létrejött, ahol Epics, szöveg stb helyébe TDSP életciklusának szakaszait vagy substages. Ilyen egy gyors sablon létrehozásával dokumentáció itt található [Itt](https://msdata.visualstudio.com/AlgorithmsAndDataScience/TDSP/_git/TDSP?path=%2FDocs%2Fteam-data-science-process-agile-template.md&version=GBxibingao&_a=preview).


##  2. <a name='SprintPlanning-2'></a>Súlyadatainak nyomtatása tervezése 

Sprint tervezésre is hasznosak lehetnek a projekt rangsorolási és erőforrás-tervezés és lefoglalás. Sok adatszakértőkön részt vevő a hónap befejezéséhez is igénybe vehet, amelyek mindegyike több projektet. Projektek gyakran különböző szóközként, a folytatáshoz. A VSTS-kiszolgálón akkor is könnyen létrehozására, kezelésére, és a munkaelemek nyomon a csapatprojekt és sprint tervezi, győződjön meg arról, hogy a projektek helyezi előre a várt módon végezze el. 

Hajtsa végre a [Ez a hivatkozás](https://www.visualstudio.com/en-us/docs/work/scrum/sprint-planning) sprint VSTS a tervezés részletes útmutatást. 


##  3. <a name='AddFeature-3'></a>A funkció hozzáadása  

A csapatprojekt alatt a projekt tárház létrehozása után nyissa meg a csapat **áttekintése** lapot, és kattintson **munkáját**.

![2](./media/project-execution/project-execution-2-sprint-team-overview.png)

A várakozó fájlok számát a szolgáltatásnak a szolgáltatással, kattintson a **várakozási sorok** --> **szolgáltatások** --> **új**, írja be a szolgáltatás **cím**(általában a projekt neve), és kattintson a **Hozzáadás** .

![3](./media/project-execution/project-execution-3-sprint-team-add-work.png)

Kattintson duplán a létrehozott szolgáltatást. Töltse ki a leírásokat, rendelje hozzá a szolgáltatáshoz tartozó csoport tagjai és tervezési a szolgáltatáshoz tartozó paraméterek beállítása. 

Ez a funkció a projekt tárház is társíthatja. Kattintson a **hivatkozás hozzáadása** alatt a **fejlesztési** szakasz. Miután végzett a szolgáltatás szerkesztésével, kattintson **mentés és Bezárás** való kilépéshez.


##  4. <a name='AddStoryunderfeature-4'></a>A szolgáltatás szövegegység hozzáadása 

A funkció a szövegek megadásával írhatja le a (szolgáltatás) projekt befejezéséhez szükséges fő lépéseken lehet hozzáadni. Egy új szövegegység hozzáadásához kattintson a  **+**  jelre a szolgáltatás várakozó nézetben.  

![4](./media/project-execution/project-execution-4-sprint-add-story.png)

Szerkesztheti a szöveg, például a állapot, leírás, megjegyzések, tervezés és az előugró ablakban prioritású virtuális gép adatait.

![5](./media/project-execution/project-execution-5-sprint-edit-story.png)

Egy meglévő tárház Ez a cikk a kattintva csatolhatja **+ Hozzáadás hivatkozásra** alatt **fejlesztési**. 

![6](./media/project-execution/project-execution-6-sprint-link-existing-branch.png)


##  5. <a name='AddTaskunderstory-5'></a>Adja hozzá a feladatot egy története 

Feladatokat is, amely minden szövegegység szükséges részletes lépéseit. Miután egy szövegegység minden feladat befejeződött, a szöveg túl kell végrehajtani. 

Adja hozzá a feladatot egy története, kattintson a  **+**  mellett a Szövegegység elem, jelölje be a bejelentkezési **feladat**, majd adja meg a részletes információkat, ennek a feladatnak az előugró ablakban.

![7](./media/project-execution/project-execution-7-sprint-add-task.png)

A szolgáltatások, a szövegek és a feladatok létrehozását követően megtekintheti azokat a **várakozó** vagy **Board** nézetek azok állapotának nyomon követéséhez.

![8](./media/project-execution/project-execution-8-sprint-backlog-view.png)

![9](./media/project-execution/project-execution-9-link-to-a-new-branch.png)


##  6. <a name='Linkaworkitemwithagitbranch-6'></a>A git ág munkaelem hivatkozás 

VSTS (szövegegység vagy feladat) munkaelem csatlakozni kényelmes megoldást kínál a git ág. Ez lehetővé teszi, hogy a szövegegység vagy feladat közvetlen hivatkozás a vele társított kód. 

Munkaelem egy új fiókirodai való kapcsolódáshoz kattintson duplán egy munkaelemet, és az előugró ablakban kattintson **hozzon létre egy új fiókirodai** alatt **+ Hozzáadás hivatkozásra**.  

![10](./media/project-execution/project-execution-10-sprint-board-view.png)

Adja meg az új fiókirodai, például a fióknév, alap git-tárház és a fiókiroda az adatokat. A kiválasztott git-tárház a tárház, amely a munkaelem tartozik azonos csapatprojekt alatt kell lennie. Az alap ág a főágba vagy valamilyen más meglévő ág lehet.

![11](./media/project-execution/project-execution-11-create-a-branch.png)

Bevált gyakorlat, ha minden szövegegység munkatétel git ág. Ezután minden feladat munkatétel létrehoz egy ágat a szövegegység fiókirodai alapján. A hierarchikus módon, amely megfelel a szövegegység-kapcsolatokat a fiókirodák telephelyeire rendszerezéséhez akkor hasznos, ha több személy dolgozik a különböző szövegek az ugyanabban a projektben van, vagy több személy dolgozik a különböző feladatok a azonos szövegegység rendelkezik. Ütközések minimalizálható, ha minden csoport egy tagja egy másik ágat, és minden tag akkor működik a különböző kód vagy más összetevők, amikor egy ágat megosztása működik. 

Az alábbi képen a javasolt elágaztatási stratégiát a TDSP mutatja be. Előfordulhat, nem kell különböző elágazik itt látható, különösen ha csak rendelkezik egy vagy két dolgozó ugyanabban a projektben, vagy csak egy személy működik-e az összes feladat egy szövegegység. Azonban a fejlesztési fiókirodai mappától a főágba mindig célszerű. Ez segítséget nyújt a kiadási fiókirodai az éppen a fejlesztési tevékenységek miatt megszakadt. Git fiókirodai modell részletesebb leírását található [egy sikeres Git ugorhat modellt](http://nvie.com/posts/a-successful-git-branching-model/).

![12](./media/project-execution/project-execution-12-git-branches.png)

Váltson át a fiókiroda, amelyet szeretne dolgozni, a következő parancsot a rendszerhéj parancsban (Windows vagy Linux). 

    git checkout <branch name>

Módosítása a *< fióknév\>*  való **fő** biztonsági kapcsolók a **fő** ág. Miután a munkaágat váltani, indítsa el függ, hogy működjön, a cikk befejezéséhez szükséges kód vagy a dokumentációjával az összetevők fejleszt. 

Egy meglévő elágazási is hozzárendelhet egy munkaelemet. Az a **részletes** munkaelem, helyett kattintva oldalán **hozzon létre egy új fiókirodai**, kattint **+ Hozzáadás hivatkozásra**. Válassza ki a fiókiroda szeretne kapcsolni a munkaelemet. 

![13](./media/project-execution/project-execution-13-link-to-an-existing-branch.png)

Is létrehozhat egy új fiókirodai a git bash parancsokat. Ha < alap fióknevének\> hiányzik, a < új fiókirodai neve\> alapuló _fő_ ág. 
    
    git checkout -b <new branch name> <base branch name>


##  7. <a name='WorkonaBranchandCommittheChanges-7'></a>Működjenek az egy ágat, és véglegesítse a módosításokat 

Most tegyük fel, hogy néhány módosítást a *adatok\_adatfeldolgozást* ág a munkaelem, például a fiókiroda egy R fájl hozzáadása a helyi gépen. Az R fájl, a fiókiroda a munkaelem hozzá véglegesítheti, feltéve, hogy a fiókirodában lévő a Git rendszerhéj az alábbi Git-parancsok használatával:

    git status
    git add .
    git commit -m"added a R scripts"
    git push origin data_ingestion

![14](./media/project-execution/project-execution-14-sprint-push-to-branch.png)

##  8. <a name='CreateapullrequestonVSTS-8'></a>Hozzon létre egy lekérési kérelmet a VSTS 

Ha készen áll után néhány véglegesíti és leküldéses értesítések, az aktuális ág egyesíteni az alap fiókirodai elküldheti a **lekérési kérelmet** VSTS-kiszolgálón. 

Nyissa meg a csapatprojekt fő lapján, és kattintson a **kód**. Válassza ki a fiókiroda való egyesíthetők és az ág az egyesíteni kívánt git-tárház nevét. Kattintson a **lekérési kérelmek**, kattintson a **új lekérési kérelmet** előtt létre szeretne hozni egy lekérési kérelmet tekintse át a munkát az ág az alap elágazási egyesített.

![15](./media/project-execution/project-execution-15-spring-create-pull-request.png)

Töltse ki a lekérési kérelmet néhány leírása, vegye fel a felülvizsgálók és továbbíthatja.

![16](./media/project-execution/project-execution-16-spring-send-pull-request.png)

##  9. <a name='ReviewandMerge-9'></a>Tekintse át és egyesítése 

A lekérési kérelem létrehozásakor, akkor a felülvizsgálók áttekintheti a lekérési kérelmek e-mail értesítést kap. A felülvizsgálók ellenőrizze, hogy a módosítások dolgozik-e, és ha lehetséges tesztelése a módosításokat a kérelmező kell. Az értékelés alapján, a többi felhasználó jóváhagyhatja vagy elutasíthatja a lekérési kérelmet. 

![17](./media/project-execution/project-execution-17-add_comments.png)

![18](./media/project-execution/project-execution-18-spring-approve-pullrequest.png)

Miután elkészült a tekintse át, a munkaágat egyesített az alap elágazási kattintva a **Complete** gombra. Választhatja a munkaágat törlése után összevont rendelkezik. 

![19](./media/project-execution/project-execution-19-spring-complete-pullrequest.png)

Erősítse meg a bal felső sarkában, amely van megjelölve, a kérelem **BEFEJEZVE**. 

![20](./media/project-execution/project-execution-20-spring-merge-pullrequest.png)

Ha később visszatérhet a tárházba **kód**, megtudja, hogy a főágba lett átadva.

![21](./media/project-execution/project-execution-21-spring-branch-deleted.png)

A következő Git-parancsok segítségével a munkaágat az alap elágazási egyesíteni, és törölje a munkaágat egyesítése után:

    git checkout master
    git merge data_ingestion
    git branch -d data_ingestion

![22](./media/project-execution/project-execution-22-spring-branch-deleted-commandline.png)


##  10. <a name='DataQualityReportUtility-10'></a>Adatok interaktív áttekintését, elemzési és jelentéskészítési (IDEAR) segédprogram

Az R markdown-alapú segédprogram értékelje ki és felfedezés adatkészletek rugalmas és interaktív eszközt biztosít. Felhasználók gyorsan előállíthat olyan jelentéseket a minimális kódolási adatkészletből. Felhasználók rákattinthatnak gombok eszköz interaktív adatkutatási eredményez exportálhat egy végső jelentést, amely ügyfelek felé vagy használ a döntést arról, mely tartalmazza az ezt követő modellezési lépésben változókat.

Ilyenkor az eszközt csak akkor működik a memóriában adatkeretek. Az adatkészlet vizsgálják paramétereket egy .yaml fájl szükséges. További információkért lásd: [TDSP adatok tudományos segédprogramok IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils).


##  11. <a name='ModelingUtility-11'></a>Alapkonfiguráció modellezési és jelentéskészítési segédprogram

Ez a segédprogram hajtsa végre a modell létrehozása abszolút, a hyper-paraméterrel, és hasonlítsa össze ezeket a modell pontosságát testre szabható, félig automatizált eszközt biztosít. 

A modell létrehozása segédprogram egy R markdown-fájl is használható a különböző szakaszok egyszerű kezelhetőség tartalomjegyzék önálló html kimenet előállításához. Három algoritmusok végrehajtásának futtatásakor a markdown-fájl (knit): a glmnet használatával rendeződik regressziós csomag, a randomForest csomag használata, és a xgboost csomaggal fák kiemelése véletlenszerű erdő). Ezek az algoritmusok mindegyikének hoz létre a modell betanítását. Ezek a modellek pontosságát majd képest, és a szolgáltatás relatív fontosságát előkészítésére jelentik. Jelenleg nincsenek két segédprogramok: egyet az bináris osztályozási feladat és egy regressziós feladat. Egymás közötti elsődleges különbségek módon paramétereit, és pontossága metrikák tanulási feladatok is meg van adva. 

Egy Yam fájl használatával adja meg:

- a bemeneti adatokhoz (egy R-adatfájl vagy egy SQL-forrás) 
- az adatok milyen része képzési használja, és milyen arányban teszteléshez
- Futtassa mely algoritmusokat 
- a modell optimalizálásához paraméterek kiválasztása:
    - kereszt-ellenőrzési 
    - rendszerindítása
    - a kereszt-ellenőrzési modellrészt
- a hyper-paraméter állandóként állítja be, minden egyes algoritmushoz. 

Algoritmusok, optimális modellrészt számát, a hyper-paraméterek számát, és a hyper-paraméterkészletei keresztül továbbítsa számát is módosíthatja a modellek gyorsan futtatásához Yam-fájlban. Például azok is futtatható KtgE modellrészt, kevesebb paraméterkészletei kevesebb. Akkor is futtatható több átfogó KtgE modellrészt nagyobb számú vagy paraméterkészletei, nagyobb számú indokolt, ha.

További információkért lásd: [automatikus modellezési és segédprogram Reporting TDSP adatok tudományos segédprogramok](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling).


##  12. <a name='PowerBI-12'></a>Power BI-irányítópult-projektek állapotának nyomon követése

Adatok tudományos csoport kezelők, a csapat érdeklődők és a projekt érdeklődők kell a projektjeikhez előrehaladását úgy követheti nyomon, milyen munkahelyi őket, és ki végzett, és továbbra is megtalálható a feladatlisták. Ha VSTS használata esetén is a tevékenységeket és a Git-tárház társított munkaelemek követéséhez Power BI-irányítópult létrehozásához. Power BI összekapcsolása a Visual Studio Team Services kapcsolatos további információkért lásd: [össze a Power BI Team Services](https://www.visualstudio.com/en-us/docs/report/powerbi/connect-vso-pbi-vs). 

Power BI-irányítópult és jelentések megtekintésére az Git-tárház tevékenységeket és a munkaelemek követéséhez VSTS adatait a Power bi-bA csatlakoztatása után, lásd: [hozzon létre Power BI-irányítópult és jelentések](https://www.visualstudio.com/en-us/docs/report/powerbi/report-on-vso-with-power-bi-vs). 

Az alábbiakban a két egyszerű példát irányítópultok nyomon követhető a Git-tevékenységek és a munkaelemek készült. Az első példában irányítópult a git kötelezettségvállalás tevékenységek különböző felhasználók alapján vannak listázva különböző időpontokban, és a különböző tárházak találhatók. Akkor is könnyen részletekbe menően vizsgálhatja a gazdarendszerhez érdekli szűréséhez.

![23](./media/project-execution/project-execution-23-powerbi-git.png)

A második példában irányítópulton a munkaelemek (szövegek és feladatok) különböző ismétlési jelennek meg. Ezek engedményeseknek és prioritási szintek szerint csoportosítva, és színes állapot szerint.

![24](./media/project-execution/project-execution-24-powerbi-workitem.png)

 
## <a name="next-steps"></a>Következő lépések

Végpont forgatókönyvek, amelyek azt mutatják, a folyamat lépései teljes **meghatározott forgatókönyvek** is rendelkezésre állnak. Szerepel a listában, és kapcsolódik a miniatűr leírásokat a [példa forgatókönyvek](walkthroughs.md) cikk. Ezek bemutatják, hogyan lehet felhő, a helyszíni eszközök és szolgáltatások egyesítése munkafolyamat vagy csővezeték intelligens alkalmazás létrehozása. 

Az adatok tudományos folyamatban lépések végrehajtása, amelyek használják az Azure Machine Learning Studióban, tekintse meg a [Azure ML](http://aka.ms/datascienceprocess) képzési.