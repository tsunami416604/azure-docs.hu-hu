---
title: A Q & A egyező Azure Machine Learning-munkaterület használatával |} Microsoft Docs
description: Hogyan egyeztetésére kell használni különböző életbelépési gépi tanulás módszer nyitott befejeződött a lekérdezéseket, amelyekkel már meglévő gyakran ismételt kérdések kérdés/válaszok párokat.
services: machine-learning
documentationcenter: ''
author: mezmicrosoft
editor: mezmicrosoft
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: mez
manager: tihazen
ms.openlocfilehash: 3695657d819f52ef04468a0f1a44828403c65373
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835100"
---
#  <a name="q--a-matching-using-azure-machine-learning-workbench"></a>A Q & A egyező Azure Machine Learning-munkaterület használatával
Nyissa meg befejeződött a kérdések megválaszolásával nehéz feladat, és gyakran van szükség manuális adatfrissítésre az érintett témakör szakértőit (SME szól). Belső KKV iránti igény csökkentése érdekében a vállalatok gyakran létrehozása gyakran ismételt kérdések (GYIK) listája, amelyek segítik a felhasználókat. Ebben a példában a különböző hatékony machine learning módszer nyitott befejeződött a lekérdezéseket, amelyekkel korábban létező gyakran ismételt kérdések kérdés/válaszok párok megfelelően bővíthető. Ez a példa azt mutatja be, egy egyszerű fejlesztési megoldás az Azure Machine Learning-munkaterület használatával kiépítése során. 

## <a name="link-to-the-gallery-github-repository"></a>A gyűjtemény GitHub-tárházban csatolása
[https://github.com/Azure/MachineLearningSamples-QnAMatching](https://github.com/Azure/MachineLearningSamples-QnAMatching)

## <a name="overview"></a>Áttekintés

Ez a példa kezeli a problémát a felhasználói kérdések leképezése a már meglévő kérdés és válasz (kérdések és válaszok) párok általában biztosított gyakran ismételt kérdések (Ez azt jelenti, hogy GYIK) listája vagy a Q & A webhelyek a jelenlegi párok, például [verem Túlcsordulás](https://stackoverflow.com/). Nincsenek egyező egy kérdést, hogy a helyes választ, például a legtöbb hasonló a kérdésre adott válasz keresése számos különböző módszer. Azonban ebben a példában nyitott befejeződött a kérdések egyeztetését által korábban kérdések feltételezve, hogy minden választ a gyakran ismételt kérdések is több szemantikailag kérdések megválaszolása.

Annak érdekében, ez a megoldás a fontos lépések a következők:

1. Tiszta és folyamatok szöveges adatok.
2. Ismerje meg, hogy informatív kifejezések, amelyek több word feladatütemezések, amelyek további információval jelennek meg a sorozatban, mint amikor függetlenül kezelni.
3. Bontsa ki a szolgáltatásokat a szöveges adatokat.
4. Szöveg besorolási modell betanítását, és értékelje a modell teljesítményét.


## <a name="prerequisites"></a>Előfeltételek

Ez a példa futtatásához az Előfeltételek a következők:

1. Egy [Azure-fiók](https://azure.microsoft.com/free/) (az ingyenes próbaverzió érhetők el).
2. Egy telepített példánya [Azure Machine Learning-munkaterület](../service/overview-what-is-azure-ml.md) következő a [– első lépések a telepítési útmutató](../service/quickstart-installation.md) telepíteni a programot, és hozzon létre egy munkaterület.
3. Ebben a példában minden számítási környezetben is futhat. Azonban javasoljuk, hogy azt futtatnia a Többmagos virtuális gép legalább 16GB memória, és 5GB szabad lemezterület.

## <a name="create-a-new-workbench-project"></a>Új munkaterület-projekt létrehozása

Hozzon létre egy új projektet, ebben a példában egy sablon használatával:
1.  Nyissa meg az Azure Machine Learning-munkaterület
2.  Az a **projektek** lapján kattintson a **+** aláírásához, és válassza ki **új projekt**
3.  Az a **új projekt létrehozása** ablaktáblán, töltse ki az adatokat az új projekt
4.  Az a **keresési Projektsablonjai** keresési mezőbe, írja be a "Q & A megfelelő", és válassza ki a sablont
5.  Kattintson a **Create** (Létrehozás) gombra

## <a name="data-description"></a>Adatok leírása

Az ebben a példában használt adatkészlet a verem Exchange adatok Dump mentve [archive.org](https://archive.org/details/stackexchange). Ezek az adatok megtalálható az összes felhasználó hozzájárult tartalom egy anonimizált memóriakép a [verem Exchange hálózati](https://stackexchange.com/). A hálózat egyes helye formázott egy külön archív XML-fájlokat tartalmazó zip keresztül 7 zip az bzip2 tömörítésével lehetett. Minden hely archív bejegyzéseket, a felhasználók, a szavazatok, a megjegyzéseket, a PostHistory és a PostLinks tartalmazza. 

### <a name="data-source"></a>Adatforrás

Ez a példa a [visszaküldés adatforgalom (10 GB-os)](https://archive.org/download/stackexchange/stackoverflow.com-Posts.7z) és [adatok (515 MB) PostLinks](https://archive.org/download/stackexchange/stackoverflow.com-PostLinks.7z) a Stack Overflow helyről. Teljes séma információkért lásd: a [readme.txt](https://ia800500.us.archive.org/22/items/stackexchange/readme.txt). 

A `PostTypeId` a POST data mező azt jelzi, hogy a feladás egy vagy több olyan `Question` vagy egy `Answer`. A `PostLinkTypeId` PostLinks adatok mező azt jelzi, hogy két kapcsolódó vagy ismétlődő. Kérdés bejegyzéseket rendszerint néhány címkéket, amelyek kulcsszavakat, amelyekre egy kérdést, más hasonló/duplikált kérdésekkel kategorizálását. Nincsenek néhány címkék nagy gyakorisággal, például a `javascript`, `java`, `c#`, `php` stb., nagyobb számú kérdés bejegyzéseket. Az ebben a példában, egy részhalmazát Q & A párokat az a `javascript` címke ki kell olvasni.

Additionionally, kérdés post összefügghet több válasz POST vagy ismétlődő kérdés bejegyzéseket. Gyakran ismételt kérdések listája az alábbi két adatkészletet összeállításához, néhány adat gyűjtemény feltétel minősülnek. A lefordított adatok három különböző ki van jelölve, egy SQL-parancsfájl, amely nem szerepel ebben a példában használatával. Az eredményül kapott adatok leírása a következőképpen történik:

- `Original Questions (Q)`: Kérdés bejegyzéseket kéri, és a Stack Overflow helyen válaszolni.
- `Duplications (D)`: Kérdés visszaküldés ismétlődő további kérdésekre, korábban létező (`PostLinkTypeId = 3`), amelyeket az eredeti kérdéseket. Adatismétlődések számít szemantikailag, abban az értelemben, hogy a megadott az eredeti kérdésre adott válasz is válaszol-e az új ismétlődő kérdés eredeti kérdésekre.
- `Answers (A)`: Minden eredeti kérdés és a adatismétlődések egynél több válasz bejegyzéseket lehet csatlakoztatni. Ebben a példában csak választja ki, amelyet az elfogad az eredeti szerző vagy válasz utáni (alapján szűrt `AcceptedAnswerId`) vagy a legmagasabb pontszám (szerinti sorrendben `Score`) az eredeti kérdéseket. 

Ezen három adatkészletek kombinációja hoz létre, ahol választ (A) van rendelve egy eredeti kérdésre (K) és a több ismétlődő kérdések (D) a következő adatok diagram módon kérdések és válaszok párok:

<table><tr><td><img id ="data_diagram" src="media/scenario-qna-matching/data_diagram.png"></td></tr></table>

### <a name="data-structure"></a>Adatszerkezet

Az adatok séma- és a három adatkészletek közvetlen letöltési hivatkozásokat a következő táblázatban található:

| Adathalmaz | Mező | Típus | Leírás
| ----------|------------|------------|--------
| [Kérdések](https://bostondata.blob.core.windows.net/stackoverflow/orig-q.tsv.gz) | Azonosító | Karakterlánc | Az egyedi kérdést azonosítója (elsődleges kulcs)
|  | AnswerId | Karakterlánc | A kérdésre egyedi választ azonosítója
|  | text0 | Karakterlánc | A nyers szöveg adatokat, többek között a kérdés cím és törzsét.
|  | CreationDate | Időbélyeg | Ha a kérdés kérte időbélyegzője
| [duplikátumok](https://bostondata.blob.core.windows.net/stackoverflow/dup-q.tsv.gz) | Azonosító | Karakterlánc | Az egyedi ismétlődést azonosítója (elsődleges kulcs)
|  | AnswerId | Karakterlánc | A válasz ismétlődést azonosítója
|  | text0 | Karakterlánc | A nyers szöveg adatokat, többek között az ismétlődést cím és törzsét.
|  | CreationDate | Időbélyeg | Ha az ismétlődést kérte időbélyegzője
| [válaszok](https://bostondata.blob.core.windows.net/stackoverflow/ans.tsv.gz)  | Azonosító | Karakterlánc | A válasz egyedi azonosító (elsődleges kulcs)
|  | text0 | Karakterlánc | A válasz a nyers adatokat


## <a name="scenario-structure"></a>A forgatókönyv struktúra

A kérdések és válaszok egyező példában három típusú fájlok áll rendelkezésre. Az első típus több Jupyter notebookok, amelyek megjelenítik a teljes munkafolyamat részletes leírását. A második típus Python fájlokat tartalmazhatnak egyéni Python modult kifejezés learning és a szolgáltatás kiolvasásához. Ezek a Python-modulok általánosak elég csak nem kiszolgálására ebben a példában, de más használati eseteket. A harmadik típus a Python fájlokat finomhangolhatják a hyper-paraméterek, és nyomon követését az Azure Machine Learning-munkaterület modell teljesítményét.

Ebben a példában a fájlokat az alábbiak szerint vannak rendszerezve.

| Fájlnév | Típus | Leírás
| ----------|------------|--------
| `Image` | Mappa | A lemezképek az információs fájl mentéséhez használt mappa
| `notebooks` | Mappa | A Jupyter notebookok mappa
| `modules` | Mappa | A Python-modulok mappa
| `scripts` | Mappa | A Python files mappában
| `notebooks/Part_1_Data_Preparation.ipynb` | Jupyter notebook | A mintaadatok eléri, előre feldolgozzák a szöveg, és készítse elő a képzés és az adatkészletek tesztelése
| `notebooks/Part_2_Phrase_Learning.ipynb` | Jupyter notebook | További informatív kifejezések és a tulajdonságcsomag szavak (BOWs) formában megadott szöveg tokenize
| `notebooks/Part_3_Model_Training_and_Evaluation.ipynb` | Jupyter notebook | Bontsa ki a szolgáltatásokat, a szöveg a besorolási modell és értékelési modell teljesítményének betanítása
| `modules/__init__.py` | Python-fájl | A Python-csomag init fájl
| `modules/phrase_learning.py` | Python-fájl | A nyers adatok átalakítása és informatív kifejezések további Python-modulok
| `modules/feature_extractor.py` | Python-fájl | A Python-modulok bontsa ki a modell betanítási funkciói
| `scripts/naive_bayes.py` | Python-fájl | A hyper-paraméterek a Naiv Bayes modell hangolásához Python
| `scripts/random_forest.py` | Python-fájl | A Python finomhangolhatják a hyper-paraméterek a véletlenszerű erdőmodell
| `README.md` | Markdown-fájlként | Az információs markdown-fájl

> [!NOTE]
> Notebookok sorozata alapján Python 3.5 épül.
> 

### <a name="data-ingestion-and-transformation"></a>Adatfeldolgozást és átalakítás

A három lefordított adatkészletek egy Blob Storage tárolóban tárolják, és a rendszer beolvassa `Part_1_Data_Preparation.ipynb` notebookot.  

A szöveg a besorolási modell betanítása, mielőtt a szöveget a kérdések tisztítani és kódtöredékek kizárandó Előfeldolgozott. Egy felügyeletlen kifejezés tanulási keresztül az oktatóanyag informatív több word sorozatok további lesz alkalmazva. Ezek a kifejezések jelentésekként jelennek meg az a szöveg a besorolási modell által használt alárendelt tulajdonságcsomag szavak (BOWs) featurization összetett egyszavas egység.

Szöveg előfeldolgozása és kifejezés tanulási részletes részletes leírása itt található: a notebookok `Part_1_Data_Preparation.ipynb` és `Part_2_Phrase_Learning.ipynb`, illetve.

### <a name="modeling"></a>Modellezés

Ez a példa arra tervezték, hogy ahol minden meglévő kérdések és válaszok párból egyedi osztály és egy részét a ismétlődő kérdések minden kérdések és válaszok pár elérhető, a besorolási modell pontozása új kérdéseket a már meglévő Q & A párok elleni képzési szöveg oktatóanyag. A példában képzési megőrzi az eredeti kérdéseket és az ismétlődő kérdések 75 %-át és a legutóbb elküldött 25 %-át ismétlődő kérdések vannak tárolt kiszolgálók, az adatok kiértékelése.

A besorolási modell egy ensemble metódus használatával három alapvető osztályozó, beleértve az összesítő **Naiv Bayes**, **támogatási vektoros gép**, és **véletlenszerű erdő**. Az egyes alap osztályozó a `AnswerId` a osztály címke és a BOWs formátumban szolgál a funkciók használatos.

A modell képzési folyamat mutatja be `Part_3_Model_Training_and_Evaluation.ipynb`.

### <a name="evaluation"></a>Próbaverzió

Két különböző értékelési-metrikák segítségével mérheti fel teljesítményét. 
1. `Average Rank (AR)`: azt jelzi, ha helyes választ a (kívül 103 válasz osztályok teljes készletét) lekérdezése a Q & A párok listája megtalálható az átlagos pozícióját. 
2. `Top 3 Percentage`: azt jelzi, hogy teszt százaléka kérdésekre, hogy a helyes választ lehet beolvasni a három legfontosabb választási lehetőségek a visszaadott a sorrend. 

A kiértékelés mutatják be `Part_3_Model_Training_and_Evaluation.ipynb`.


## <a name="conclusion"></a>Összegzés

Ez a példa kiemeli a jól ismert szöveg analytics módszereivel, mint a kifejezés learning és a szöveges besorolás, egy robusztus modell létrehozásához. Azt is bővíthető, hogyan Azure Machine Learning-munkaterület interaktív megoldás fejlesztési és nyomon követése a modell teljesítménnyel segítik. 

Néhány kulcsfontosságú emeli ki ebben a példában a következők:

- A kérdés és válasz egyező probléma hatékonyan megoldhatók a kifejezés tanulási és a szöveg a besorolási modell.
- Az Azure Machine Learning-munkaterület és a Jupyter Notebook interaktív modell fejlesztési, amely tartalmazza.
- Az Azure Machine Learning-munkaterület futtatási előzményei és összehasonlításra értékelési metrikáját naplózás megismert modellek kezeli. Ezek a funkciók lehetővé teszi, hogy gyors hangolása hyper paraméter, és segít azonosítani a legjobb teljesítményt modellek.


## <a name="references"></a>Referencia

Komócsin J. Hazen, János Richardson [ _korlátozott kifejezések fája Multiword kifejezések modellezési továbbfejlesztett témakör modellezési Conversational beszéd_](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf). Nyelvi technológia Workshop (SLT), 2012 IEEE szóbeli. IEEE, 2012.

Komócsin J. Hazen, [ _MCE képzési módszereket szóbeli hang dokumentumok témakör azonosításához_ ](http://ieeexplore.ieee.org/abstract/document/5742980/) az IEEE tranzakció a hang, beszéd átalakítás, és feldolgozás 19.,-os aránya nem. 8, oldal 2451-2460, november 2011.
