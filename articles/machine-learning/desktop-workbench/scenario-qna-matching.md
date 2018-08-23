---
title: A Q & a használatával az Azure Machine Learning Workbench alkalmazásban egy egyező |} A Microsoft Docs
description: Hogyan hatékony gépi tanulási módszerekkel különböző a megfelelő nyílt ért a már meglévő lekérdezéseket – gyakori kérdések kérdés/válasz párok.
services: machine-learning
documentationcenter: ''
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.openlocfilehash: 33d95e0c17e8b9b18313cb0854532337ec76cfd1
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2018
ms.locfileid: "42056400"
---
#  <a name="q--a-matching-using-azure-machine-learning-workbench"></a>A Q & A megfelelést kiváltó Azure Machine Learning workbench használatával
Nyissa meg befejeződött a kérdés megválaszolásával nehéz feladat, és gyakran az az érintett témakör szakértőit (kis-és középvállalkozások) manuális beavatkozást igényel. Belső KKV igény csökkentése érdekében vállalatok gyakran létrehozása gyakran ismételt kérdések (GYIK) listája, hogy segítséget nyújtson a felhasználóknak. Ez a példa bemutatja a különböző hatékony machine learning módszerek megfelelő, korábban létező – gyakori kérdések kérdés/válasz párt nyílt befejeződött a lekérdezéseket. Ez a példa bemutatja egy egyszerű fejlesztési folyamatot, az Azure Machine Learning Workbench használatával megoldás kiépítésének. 

## <a name="link-to-the-gallery-github-repository"></a>A katalógus GitHub-adattárra mutató hivatkozás
[https://github.com/Azure/MachineLearningSamples-QnAMatching](https://github.com/Azure/MachineLearningSamples-QnAMatching)

## <a name="overview"></a>Áttekintés

Ebben a példában a problémára, felhasználói kérdések leképezése már meglévő kérdés és válasz (Q & A) párok általában biztosított gyakran ismételt kérdések (vagyis a GYIK) listája vagy a Q & a-e a webhelyek párok, például [verem Overflow](https://stackoverflow.com/). Nincsenek a helyes válasz, például keresés, a választ, amely a leginkább hasonló a kérdést a kérdés megfelelően számos különböző módszer. Azonban ebben a példában nyílt befejeződött a kérdések megfeleltetődnek szerint korábban feltett kérdésekre feltételezi, hogy minden egyes választ a gyakori kérdések a több szemantikailag egyenértékűnek kérdésekre képes választ adni.

A szükséges, hogy ez a megoldás fontos lépések az alábbiak szerint:

1. Tiszta és szöveges adatokat.
2. Ismerje meg, hogy informatív kifejezések, amelyek több hangzású szószekvenciák, amely további információval, mint ha külön kezeli-e feladatütemezési megtekintve.
3. Jellemzők kinyerése a szöveges adatokat.
4. Szövegbesorolási modellek betanítása és modellek teljesítményének elemzése.


## <a name="prerequisites"></a>Előfeltételek

Ez a példa futtatásához az Előfeltételek a következők:

1. Egy [Azure-fiók](https://azure.microsoft.com/free/) (az ingyenes próbaverziók érhető el).
2. Egy telepített példánya [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) következő a [rövid telepítési útmutatójában](../service/quickstart-installation.md) telepítse a programot, és hozzon létre egy munkaterületet.
3. Ebben a példában minden számítási környezetben is futhat. Azonban javasoljuk, hogy legalább futtatása a Többmagos gépen 16GB memória, és 5GB szabad lemezterület.

## <a name="create-a-new-workbench-project"></a>Egy új workbench-projekt létrehozása

Hozzon létre egy új projektet, ebben a példában egy sablon használatával:
1.  Nyissa meg az Azure Machine Learning Workbench alkalmazásban
2.  Az a **projektek** lap, kattintson a **+** aláírásához, és válassza ki **új projekt**
3.  Az a **új projekt létrehozása** panelen adja meg az információkat az új projekt
4.  Az a **projektsablonok keresése** keresőmezőbe, írja be a "Q & A megfelelő", és válassza ki a sablont
5.  Kattintson a **Create** (Létrehozás) gombra

## <a name="data-description"></a>Adatok leírása

Ebben a példában használt adatkészlet, a Stack Exchange adatok Dump tárolt [archive.org](https://archive.org/details/stackexchange). Ezeket az adatokat egy anonimizált memóriakép az összes felhasználó által biztosított tartalom kapcsolva a [Stack Exchange hálózati](https://stackexchange.com/). A hálózati helyekhez formázott XML-fájlok, amelyek egy külön archív zip-n keresztül 7-zip bzip2 tömörítéssel. Minden hely archív bejegyzések, a felhasználók, a szavazatok, a megjegyzéseket, a PostHistory és a PostLinks tartalmazza. 

### <a name="data-source"></a>Adatforrás

Ez a példa a [tesz közzé adatokat (10 GB)](https://archive.org/download/stackexchange/stackoverflow.com-Posts.7z) és [adatok (515 MB) PostLinks](https://archive.org/download/stackexchange/stackoverflow.com-PostLinks.7z) a Stack Overflow webhelyről. Teljes séma információkért lásd: a [readme.txt](https://ia800500.us.archive.org/22/items/stackexchange/readme.txt). 

A `PostTypeId` a hozzászólások adatait a mezőben azt jelzi, hogy egy bejegyzés egy `Question` vagy egy `Answer`. A `PostLinkTypeId` PostLinks adatok mező azt jelzi, hogy két bejegyzések van csatolva, vagy ismétlődő. Kérdés bejegyzések az egyes címkék, amelyek kategorizálásához és más hasonló/ismétlődő kérdések feltevése kulcsszavak általában tartalmaznak. Vannak az egyes címkék nagy gyakoriságú, például `javascript`, `java`, `c#`, `php` stb., kérdés bejegyzések nagyobb számú áll. Az ebben a példában, egy részét a Q & A párokat a `javascript` címke ki kell olvasni.

Additionionally, kérdés hozzászólás kapcsolatosak, több választ bejegyzések vagy ismétlődő kérdés bejegyzések. – Gyakori kérdések listáját a a két adatkészlet létrehozásához, néhány adat gyűjtemény feltétel minősülnek. A lefordított adatok három különböző ki van jelölve, egy SQL-parancsfájl, amely nem szerepel ebben a példában használatával. Az eredményül kapott adatok leírásának a következőképpen történik:

- `Original Questions (Q)`: Kérdés bejegyzések gyakori, és választ találhat a Stack overflow-weblapra.
- `Duplications (D)`: Kérdés bejegyzések ismétlődő további kérdések a már meglévő (`PostLinkTypeId = 3`), amely az eredeti kérdések. Adatismétlődések számít szemantikailag egyenértékűnek abban az értelemben, hogy a megadott az eredeti kérdésre adott válasz az új ismétlődő kérdés is ad választ az eredeti kérdésekre.
- `Answers (A)`: Minden egyes eredeti kérdést, és annak adatismétlődések egynél több válasz bejegyzések lehet társítani. Ebben a példában csak a válaszfájl-bejegyzés, vagy amelyet az eredeti szerző választja ki (alapján szűrt `AcceptedAnswerId`) vagy a legnagyobb pontszámot tartalmaz (szerint rangsorolva, amelyekről `Score`) az eredeti kérdéseket. 

Ezek az adatkészletek három kombinációja hoz létre, ahol választ (A) le van képezve egy eredeti kérdésre (Q) és több ismétlődő kérdések (D) leírtak szerint a következő adatok diagram a Q & A párok:

<table><tr><td><img id ="data_diagram" src="media/scenario-qna-matching/data_diagram.png"></td></tr></table>

### <a name="data-structure"></a>Adatszerkezet

A sémát és a közvetlen letöltési hivatkozások a három adatkészletek a következő táblázatban található:

| Adathalmaz | Mező | Típus | Leírás
| ----------|------------|------------|--------
| [Kérdések](https://bostondata.blob.core.windows.net/stackoverflow/orig-q.tsv.gz) | Azonosító | Sztring | A kérdés egyedi Azonosítóját (elsődleges kulcs)
|  | AnswerId | Sztring | Kérdés / válasz egyedi azonosítója
|  | Text0 | Sztring | A nyers szöveg adatait, beleértve a a kérdés címe és törzs
|  | CreationDate | Időbélyeg | Ha a kérdés rendszer kérte időbélyegzője
| [duplikátumok](https://bostondata.blob.core.windows.net/stackoverflow/dup-q.tsv.gz) | Azonosító | Sztring | A duplikáció egyedi Azonosítóját (elsődleges kulcs)
|  | AnswerId | Sztring | A válasz-Azonosítójának társítva a duplikálása
|  | Text0 | Sztring | A nyers szöveg adatait, beleértve a párhuzamos címe és törzs
|  | CreationDate | Időbélyeg | Amikor az ismétlődést rendszer kérte időbélyegzője
| [válaszok](https://bostondata.blob.core.windows.net/stackoverflow/ans.tsv.gz)  | Azonosító | Sztring | A válasz egyedi azonosítója (elsődleges kulcs)
|  | text0 | Sztring | A nyers szöveg adatok a válasz


## <a name="scenario-structure"></a>A forgatókönyv-struktúra

A Q & A megfelelő példa keresztül három típusú fájlok jelennek meg. Az első típus azt mutatják be, a teljes munkafolyamat részletes leírása a Jupyter Notebooks sorozata. A második pedig a Python-fájlokat tanulás és a szolgáltatás kulcsszókeresés egyéni Python-modulokat tartalmaz. Ezek a Python-modulok elég általános, nem csak az ebben a példában, de más használati eseteket kiszolgálása érdekében. A harmadik típus adhatja meg a hyper-paraméterek, és nyomon követheti az Azure Machine Learning Workbench használata modell teljesítményét, a Python-fájlokat.

Ebben a példában a fájlokat a következőképpen vannak rendszerezve.

| Fájlnév | Típus | Leírás
| ----------|------------|--------
| `Image` | Mappa | A rendszerképeket az információs fájl mentéséhez használt mappa
| `notebooks` | Mappa | A Jupyter notebookok mappa
| `modules` | Mappa | A Python modulok mappa
| `scripts` | Mappa | A Python-mappába
| `notebooks/Part_1_Data_Preparation.ipynb` | Jupyter notebook | Elérni a mintaadatokat, előzetes feldolgozása a szöveget, és készítse elő a képzés és adatkészletek
| `notebooks/Part_2_Phrase_Learning.ipynb` | Jupyter notebook | Informatív kifejezések bemutatása és a szöveg tokenize való alkalmasságával tulajdonságcsomag szavakat (BOWs)
| `notebooks/Part_3_Model_Training_and_Evaluation.ipynb` | Jupyter notebook | Bontsa ki a Funkciók, szövegbesorolási modellek és modellek teljesítményének kiértékelése
| `modules/__init__.py` | Soubor Pythonu | A Python Csomagkezelő init-fájlt
| `modules/phrase_learning.py` | Soubor Pythonu | A nyers adataiból, és ismerje meg az informatív kifejezéseket használni a Python-modulok
| `modules/feature_extractor.py` | Soubor Pythonu | A Python-modulok bontsa ki a modell betanítása és funkciók
| `scripts/naive_bayes.py` | Soubor Pythonu | A Python, a hyper-paraméterek a Naiv Bayes modell finomhangolása
| `scripts/random_forest.py` | Soubor Pythonu | A Python hiperparaméterekkel a véletlenszerű erdő modell finomhangolása
| `README.md` | Markdown-fájl | Az információs markdown-fájl

> [!NOTE]
> Notebookok sorozatát épül, a Python 3.5-ös verzióját.
> 

### <a name="data-ingestion-and-transformation"></a>Adatfeldolgozás és -átalakítási

A három lefordított adatkészletek egy Blob storage szolgáltatásban tárolódnak, és blobnevet `Part_1_Data_Preparation.ipynb` notebookot.  

A szövegbesorolási modellek betanítása, mielőtt a szöveget a kérdések tisztítani és kódrészletek kizárandó üzenetfájlrekordok. Az oktatóanyag további informatív több hangzású szószekvenciák keresztül egy felügyeletlen kifejezés tanulás alkalmazásával. Ezek a kifejezések jelentésekként jelennek meg az alsóbb rétegbeli tulajdonságcsomag szavakat (BOWs) featurization a szövegbesorolási modellek használják egységek összetett egyszavas.

Szöveg előfeldolgozása és kifejezés, tanulási részletes részletes leírása megtalálható a notebookok `Part_1_Data_Preparation.ipynb` és `Part_2_Phrase_Learning.ipynb`, illetve.

### <a name="modeling"></a>Modellezés

Ebben a példában úgy tervezték, hogy új kérdéseket a már meglévő Q & a-párok elleni pontozása képzési szöveg képbesorolási modellek, ahol minden meglévő Q & a két egy egyedi osztály és a egy részét az ismétlődő kérdések a Q & A párjaihoz érhetők el képzési anyagokat. A példában az eredeti kérdéseket és az ismétlődő kérdések 75 %-át a betanításhoz megmaradnak a legutóbb közzétett 25 %-a ismétlődő kérdések vannak tárolt megismerheti és értékelési adatokat.

A besorolási modell ensemble módszert használja a három alapvető osztályozó eszközökkel, beleértve a összesítenie **Naiv Bayes**, **Tartóvektor-gép**, és **véletlenszerű erdő**. Az egyes alap osztályozó a `AnswerId` osztály címke és a BOWs formátumban lesz az funkciók használatát.

A modell betanítási folyamat szemlélteti az `Part_3_Model_Training_and_Evaluation.ipynb`.

### <a name="evaluation"></a>Próbaverzió

Két különböző értékelési mérőszámok segítségével teljesítmény értékeléséhez. 
1. `Average Rank (AR)`: azt jelzi, hogy hol található a megfelelő választ (összesen: 103 válasz osztályok teljes készletét) lekérdezése a Q & A párok listáját átlagos pozíciója. 
2. `Top 3 Percentage`: azt jelzi, hogy a teszt aránya kérdésekre, hogy a helyes választ is olvassa be az első három lehetőségei által visszaadott rangsorolt listát. 

Az értékelés mutatják be `Part_3_Model_Training_and_Evaluation.ipynb`.


## <a name="conclusion"></a>Összegzés

Ebben a példában emeli ki, hogyan használható a Well-Known text analytics technikák, kifejezés tanulás és a szöveges besorolás, például egy robusztus modell létrehozásához. Azt is bemutatja, hogyan segíthet az Azure Machine Learning Workbench interaktív megoldás fejlesztési és nyomon követheti a modell teljesítményét. 

Néhány fontosabb az ebben a példában a következők:

- A kérdés és válasz egyező probléma hatékonyan megoldhatók a tanulás és a szöveges kifejezést képbesorolási modellek.
- Interaktív modell fejlesztését az Azure Machine Learning Workbench és a Jupyter Notebook bemutatására.
- Az Azure Machine Learning Workbench futtatási előzményeket és megismert modellek az összehasonlításhoz értékelési mérőszámok a naplózási kezeli. Ezek a funkciók lehetővé teszi, hogy gyors finomhangolással, és segít azonosítani a legjobban teljesítő modelljeit.


## <a name="references"></a>Referencia

Zak J. Hazen, Fred Richardson, [ _korlátozott kifejezések fája Multiword kifejezések modellezési továbbfejlesztett témakör modellezés, természetes nyelvi beszédfelismerési_](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf). Használja a beszélt nyelv technológia Workshop (SLT), 2012 IEEE. IEEE, 2012.

Zak J. Hazen, [ _MCE képzési módszereket témakör azonosításához használja a beszélt hang dokumentumok_ ](http://ieeexplore.ieee.org/abstract/document/5742980/) IEEE tranzakcióknak a hang, beszédfelismerési és nyelvi feldolgozás, 19-os aránya nem található. 8., oldal 2451-2460, 2011. november.
