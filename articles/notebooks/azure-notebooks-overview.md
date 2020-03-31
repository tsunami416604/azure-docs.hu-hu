---
title: Az Azure Notebookelőzetes verzió áttekintése
description: Futtassa a Jupyter-jegyzetfüzeteket a felhőben az ingyenes Azure Notebookelőzetes szolgáltatás használatával, ahol nincs szükség beállításra vagy konfigurációra.
ms.topic: overview
ms.date: 04/05/2019
ms.openlocfilehash: bd2355bdefcedca5026a25915dc1da55fdc33a36
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "75646330"
---
# <a name="overview-of-azure-notebooks-preview"></a>Az Azure Notebookelőzetes verzió áttekintése

Az Azure Notebooks egy ingyenes üzemeltetett szolgáltatás a Jupyter notebookok felhőben történő, telepítés nélküli fejlesztéséhez és futtatásához. [Jupyter](https://jupyter.org/) (korábban IPython) egy nyílt forráskódú projekt, amely lehetővé teszi, hogy könnyen kombinálni Markdown szöveg, futtatható kód, állandó adatok, grafikák, és képi megjelenítés rá egy, megosztható vászon, a *notebook* (kép jóvoltából jupyter.org):

[![Példák jupyter notebookokra](https://jupyter.org/assets/jupyterpreview.png)](https://jupyter.org/assets/jupyterpreview.png#lightbox)

A kód, a grafika és a magyarázó szöveg hatékony kombinációja miatt a Jupyter számos célra népszerűvé vált, beleértve az adatelemzési oktatást, az adattisztítást és -átalakítást, a numerikus szimulációt, a statisztikai modellezést és a gépi tanulási modellek.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="hassle-free-experience"></a>Gondtalan élmény

Az Azure Notebooks segítségével gyorsan elkezdheti a prototípuskészítést, az adattudományt, az akadémiai kutatást vagy a Python programozását:

- Egy adattudós azonnali hozzáféréssel rendelkezik egy teljes Anaconda környezethez telepítés nélkül.
- A tanárok gondtalan Python-környezetet biztosíthatnak a diákoknak.
- A műsorvezető adhat egy hasonló beszélgetés vagy webinar megkérdezése nélkül a résztvevők költeni 45 percet szoftver telepítése.
- A fejlesztő vagy hobbyist használhatja Notebooks, mint egy gyors kód scratchpad.

A jegyzetfüzetek még hatékonyabbá válnak, ha az emberek együttműködhetnek rajtuk egy böngészőhöz elérhető felhőszolgáltatáson, például az Azure Notebookokon keresztül (előzetes verzióban). A felhőben a felhasználóknak nem kell helyileg telepíteniük a Jupytert, és nem kell a környezet fenntartásával foglalkozniuk. A felhő is egyszerűvé teszi a jegyzetfüzetek (és a kapcsolódó adatfájlok) megosztását más jogosult felhasználókkal, elkerülve a jegyzetfüzetek külső eszközökkel, például a forrás-ellenőrző adattárakhasználatával történő megosztásának bonyodalmait. Az Azure Notebooks segítségével a felhasználók is másolhatnak (vagy "klónozhatnak") jegyzetfüzeteket a saját fiókjukba módosítás vagy kísérletezés céljából, ami különösen hasznos az utasítási célokra.

Mivel az Azure Notebooks egy általános kódszerzői, végrehajtási és megosztási platform, számos különböző forgatókönyvhöz használhatja:

- Új programozási nyelv elsajátítása – próbálja ki az egyik [címlapoktatót](https://notebooks.azure.com/Microsoft/projects/samples/html/Introduction%20to%20Python.ipynb)
- Ismerje meg az adattudományt – próbálja ki [Jake VanderPlas könyvét](https://notebooks.azure.com/jakevdp/projects/PythonDataScienceHandbook)
- Több száz diák nak [tanítegy tanfolyamot](https://notebooks.azure.com/garth-wells/projects/CUED-IA-Computing-Michaelmas)
- Webinárium online vagy konferencián való adása a telepítéssel töltött idő nélkül 
- A GitHub-felhasználók közvetlenbetöltésének és futtatásának engedélyezése a [GitHub indítási jelvényének létrehozásával](https://notebooks.azure.com/help/projects/sharing/create-a-github-badge)
- Adja meg a [PowerPoint, mint a diavetítések,](https://notebooks.azure.com/help/jupyter-notebooks/slides) ahol a kód diák futtatható!

Röviden, az Azure Notebooks segítségével hatékonyabban végezheti el munkáját, és így többet érhet el.

> [!Note]
> További információ a Jupyter magáról megtalálható [jupyter.org](https://jupyter.org/) és a [Jupyter dokumentáció](https://jupyter-notebook.readthedocs.io/en/latest/).

## <a name="pricing-and-quotas"></a>Árképzés és kvóták

Az Azure Notebooks egy ingyenes szolgáltatás, de minden projekt 4 GB memóriára és 1 GB-os adatokra korlátozódik a visszaélések megelőzése érdekében. A korlátozásokat túllépő legitim felhasználók egy Captcha-kihívást látnak a jegyzetfüzetek futtatásának folytatásához.

Az összes korlát felszabadításához jelentkezzen be az Azure Notebooks-ba egy Azure Active Directory (például vállalati fiók) használatával. Ha ez a fiók egy Azure-előfizetéshez van társítva, az adott előfizetésen belül bármely Azure Data Science virtuálisgép-példányhoz csatlakozhat. További információ: [Projektek kezelése és konfigurálása – Számítási szint.](configure-manage-azure-notebooks-projects.md#compute-tier)

Notebook szerverek garantáltan létezik legmost 8 óra. A legtöbb esetben a tárolóra nem vonatkozik ez a korlát, és ez az időszak túl is fut, de a hosszú élettartamú munkamenetek időnként leállhatnak a rendszer stabilitása érdekében.

## <a name="available-kernels-and-environments"></a>Elérhető kernelek és környezetek

Minden egyes jegyzetfüzethez ki kell választania azt a rendszermagot (azaz a futásidejű környezetet), amely a kódcellák futtatásához használatos. Az Azure Notebooks a következő kerneleket támogatja:

- Python 2.7 + Anaconda2-5.3.0
- Python 3.6 + Anaconda3-5.3.0
- Python 3.5 + Anaconda3-4.2.0 (elavult)
- R 3.4.1 + Microsoft R Open 3.4.1
- F#4.1.9

Az Azure Notebooks az alapdisztribúciókon túl további csomagokat is tartalmaz. A Python kernelek közé tartoznak például a numpy, pandák, scikit-learn, matplotlib és bokeh könyvtárak.

A projekt testreszabásával környezetet hozhat létre a projekt összes jegyzetfüzetéhez. További információt a [Rövid útmutató: Projekt létrehozása egyéni környezettel](quickstart-create-jupyter-notebook-project-environment.md)című témakörben talál.

Az alapszintű disztribúciók mellett az Azure Notebooks előre telepített, számos további csomaggal, amelyek hasznosak az adatszakértők számára. Saját csomagokat is telepíthet az egyes nyelvekre jellemző folyamat használatával.

## <a name="pre-configured-jupyter-extensions"></a>Előre konfigurált Jupyter-bővítmények

Az Azure Notebooks előre konfigurálva van a következő Jupyter-bővítményekkel:

- [RISE](https://github.com/damianavila/RISE): A Jupyter Diavetítés Extension (más néven live_reveal). További információt a [Jegyzetfüzet-diavetítés futtatása](present-jupyter-notebooks-slideshow.md)című témakörben talál.
- [JupyterLab](https://github.com/jupyterlab/jupyterlab): Teljes számítási környezet a Jupyter notebookok hoz.
- [Altair](https://github.com/ellisonbg/altair): Python deklaratív statisztikai vizualizációs könyvtára.
- [BQPlot](https://github.com/bloomberg/bqplot): A Jupyter notebookok interaktív nyomtatási keretrendszere.
- [IpyWidgets](https://github.com/jupyter-widgets/ipywidgets): Interaktív HTML kütyü Jupyter Notebooks.

## <a name="issues-and-getting-help"></a>Problémák és segítség szerzése

Mivel az Azure Notebooks még előzetes verzióban van, a szolgáltatás átmeneti kimaradásokat tapasztalhat, amelyek gyakoribbak vagy tartósabbak lehetnek, mint más Azure-szolgáltatások. Előfordulhat, hogy egyes funkciók hiányosak vagy hibákat tartalmaznak.

Jelenleg azt javasoljuk, hogy ne használja az Azure Notebooks Preview üzleti legkritikusabb alkalmazásokhoz, illetve a bizalmas jegyzetfüzetek és adatok használata ellen.

Az Azure-jegyzetfüzetekkel kapcsolatos kérdések megvitatásához nyújtson be egy problémát a [GitHub-tárházban.](https://github.com/Microsoft/AzureNotebooks/issues)

## <a name="next-steps"></a>További lépések  

- [Mintajegyzetfüzetek felfedezése](azure-notebooks-samples.md)

- Gyorsútmutatók:

  - [Notebook létrehozása és megosztása](quickstart-create-share-jupyter-notebook.md)
  - [Notebook klónozása](quickstart-clone-jupyter-notebook.md)
  - [Helyi Jupyter notebook migrálása](quickstart-migrate-local-jupyter-notebook.md)
  - [Egyéni környezet használata](quickstart-create-jupyter-notebook-project-environment.md)
  - [Bejelentkezés és felhasználói azonosító beállítása](quickstart-sign-in-azure-notebooks.md)

- Oktatóanyagok:

  - [Notebook létrehozása és futtatása](tutorial-create-run-jupyter-notebook.md  )

- Útmutató cikkek:
  
  - [Projektek létrehozása és klónozása](create-clone-jupyter-notebooks.md)
  - [Projektek konfigurálása és kezelése](configure-manage-azure-notebooks-projects.md)
  - [Csomagok telepítése jegyzetfüzetből](install-packages-jupyter-notebook.md)
  - [Diavetítés bemutatása](present-jupyter-notebooks-slideshow.md)
  - [Adatfájlok használata](work-with-project-data-files.md)
  - [Adatforrások elérése](access-data-resources-jupyter-notebooks.md)
  - [Az Azure Machine Learning használata](use-machine-learning-services-jupyter-notebooks.md)
