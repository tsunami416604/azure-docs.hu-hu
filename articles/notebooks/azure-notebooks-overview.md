---
title: Az Azure Notebooks előzetes verziójának áttekintése
description: Futtasson Jupyter jegyzetfüzeteket a felhőben az ingyenes Azure Notebooks előzetes verzió szolgáltatásával, ahol nincs szükség beállításra vagy konfigurációra.
ms.topic: overview
ms.date: 04/05/2019
ms.openlocfilehash: d229e48e5c49a9a672c533fb24231e9329e524c0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "85831403"
---
# <a name="overview-of-azure-notebooks-preview"></a>Az Azure Notebooks előzetes verziójának áttekintése

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Az Azure Notebooks egy ingyenes üzemeltetett szolgáltatás a Jupyter notebookok felhőben történő, telepítés nélküli fejlesztéséhez és futtatásához. A [Jupyter](https://jupyter.org/) (korábbi nevén IPython) egy nyílt forráskódú projekt, amely lehetővé teszi a Markdown-szöveg *, a végrehajtható* kód, az állandó adatok, a grafikák és a vizualizációk egyszerű egyesítését egyetlen, megosztható vászonra (a jupyter.org lemezkép jóvoltából):

[![Példák Jupyter notebookokra](https://jupyter.org/assets/jupyterpreview.png)](https://jupyter.org/assets/jupyterpreview.png#lightbox)

A kód, a grafika és a magyarázó szöveg hatékony kombinációja miatt a Jupyter számos felhasználási célra népszerűvé vált, beleértve az adatelemzési utasításokat, az adattisztítást és-átalakítást, a numerikus szimulációt, a statisztikai modellezést és a gépi tanulási modellek fejlesztését.

## <a name="hassle-free-experience"></a>Problémamentes élmény

Azure Notebooks segít gyorsan megkezdeni a prototípus-készítést, az adatelemzést, az akadémiai kutatást vagy a Python programba való tanulást:

- Az adattudós azonnali hozzáférést biztosít egy teljes anaconda-környezethez, telepítés nélkül.
- Egy tanár egy problémamentes Python-környezetet biztosíthat a tanulók számára.
- Az előadók olyan előadásokat adhatnak, mint például a Talk vagy a Webinar, és a résztvevőknek nem kell eltölteniük a szoftvert a 45 perc
- Egy fejlesztő vagy amatőr a jegyzetfüzeteket gyors kódú firkatömb használhatja.

A jegyzetfüzetek még hatékonyabbá válnak, ha a felhasználók egy böngészővel elérhető felhőalapú szolgáltatással, például az Azure Notebooks (előzetes verzióban) tudnak együttműködni rajtuk. A felhőben a felhasználóknak nem kell helyileg telepíteniük a Jupyter, vagy a környezet fenntartásával kellene foglalkoznia. A felhő lehetővé teszi a jegyzetfüzetek (és a kapcsolódó adatfájlok) megosztását is a más jogosultságokkal rendelkező felhasználókkal, elkerülve a jegyzetfüzetek külső módon, például a forrás-ellenőrzési adattárakban való megosztásának szövődményeit. A Azure Notebooks segítségével a felhasználók a saját fiókba másolhatják (vagy "klónozott") jegyzetfüzeteket a módosításra vagy kísérletezésre, ami különösen hasznos az útmutatáshoz.

Mivel a Azure Notebooks egy általános programkód-létrehozási,-végrehajtási és-megosztási platform, számos különböző forgatókönyvhöz használható:

- Ismerkedjen meg az új programozási nyelvvel – próbálja ki a [FrontPage oktatóanyagok](https://notebooks.azure.com/Microsoft/projects/samples/html/Introduction%20to%20Python.ipynb) egyikét
- Adatelemzés – a [Jake VanderPlas](https://notebooks.azure.com/jakevdp/projects/PythonDataScienceHandbook) kipróbálása
- Több száz diák [képzésének tanítása](https://notebooks.azure.com/garth-wells/projects/CUED-IA-Computing-Michaelmas)
- Szemináriumok Online vagy konferencián való üzembe helyezése a telepítés ideje nélkül 
- A GitHub-felhasználók közvetlen betöltését és futtatását teszi lehetővé a [GitHub indítási jelvényének létrehozásával](https://notebooks.azure.com/help/projects/sharing/create-a-github-badge)
- A [PowerPointhoz hasonló diavetítéseket](https://notebooks.azure.com/help/jupyter-notebooks/slides) adhat meg, ahol a diák kódja végrehajtható!

Röviden, Azure Notebooks segíti a munkáját hatékonyabban elérni, és így tovább.

> [!Note]
> A Jupyter-ről a [jupyter.org](https://jupyter.org/) és a [Jupyter dokumentációjában](https://jupyter-notebook.readthedocs.io/en/latest/)találhat további információt.

## <a name="pricing-and-quotas"></a>Díjszabás és kvóták

Azure Notebooks egy ingyenes szolgáltatás, de az egyes projektek 4 GB memóriával és 1 GB-nyi adattal rendelkeznek a visszaélések elkerülése érdekében. A fenti korlátokat meghaladó, megbízható felhasználók a jegyzetfüzetek futtatásának folytatásához egy CAPTCHA-kihívást látnak.

Az összes korlát felszabadításához jelentkezzen be Azure Notebooks egy olyan fiókkal, amely Azure Active Directory (például egy vállalati fiók) használatával. Ha ez a fiók Azure-előfizetéshez van társítva, az adott előfizetéshez tartozó Azure Data Science Virtual Machine-példányokhoz is csatlakozhat. További információ: [projektek kezelése és konfigurálása – számítási réteg](configure-manage-azure-notebooks-projects.md#compute-tier).

A notebook-kiszolgálók legfeljebb 8 órán keresztül vannak garantálva. A legtöbb esetben a tároló nem vonatkozik erre a korlátozásra, és továbbra is az idő függvényében fut, de a hosszú élettartamú munkamenetek időnként leállíthatók a rendszer stabilitására.

## <a name="available-kernels-and-environments"></a>Elérhető kernelek és környezetek

Minden egyes jegyzetfüzet esetében ki kell választania a kód celláinak futtatásához használt kernelt (azaz a futásidejű környezetet). A Azure Notebooks a következő kerneleket támogatja:

- Python 2,7 + Anaconda2 – 5.3.0
- Python 3,6 + Anaconda3 – 5.3.0
- Python 3,5 + Anaconda3 – 4.2.0 (elavulttá válik)
- R 3.4.1 + Microsoft R Open 3.4.1
- F # 4.1.9

A Azure Notebooks az alapszintű disztribúción túli további csomagokat is tartalmaz. A Python-kernelek közé tartoznak például a NumPy, a pandák, a scikit-Learn, a matplotlib és a bokeh-függvénytárak.

Emellett testre is szabhatja a projektet, hogy a projektben lévő összes jegyzetfüzethez hozzon létre környezetet. További információ [: gyors útmutató: projekt létrehozása egyéni környezettel](quickstart-create-jupyter-notebook-project-environment.md).

Az alapszintű disztribúciók mellett Azure Notebooks előre telepítve van számos további csomaggal, amelyek hasznosak az adatszakértők számára. Saját csomagokat is telepíthet az egyes nyelvekhez tartozó szokásos folyamat használatával.

## <a name="pre-configured-jupyter-extensions"></a>Előre konfigurált Jupyter-bővítmények

Azure Notebooks előre konfigurálva van a következő Jupyter-bővítményekkel:

- [Rise](https://github.com/damianavila/RISE): A Jupyter Slideshow bővítmény (más néven live_reveal). További információ: jegyzetfüzet- [Diavetítés futtatása](present-jupyter-notebooks-slideshow.md).
- [JupyterLab](https://github.com/jupyterlab/jupyterlab): teljes számítási környezet a Jupyter-jegyzetfüzetek használatához.
- [Altair](https://github.com/ellisonbg/altair): a Python deklaratív statisztikai vizualizációs könyvtára.
- [BQPlot](https://github.com/bloomberg/bqplot): interaktív ábrázolási keretrendszer a Jupyter-jegyzetfüzetekhez.
- [IpyWidgets](https://github.com/jupyter-widgets/ipywidgets): interaktív HTML-widgetek Jupyter-jegyzetfüzetekhez.

## <a name="issues-and-getting-help"></a>Problémák és Segítség kérése

Mivel Azure Notebooks még mindig előzetes verzióban érhető el, a szolgáltatás olyan ideiglenes leállást tapasztalhat, amely gyakoribb vagy tartósabb lehet, mint a többi Azure-szolgáltatás. Előfordulhat, hogy egyes funkciók hiányosak vagy hibákat tartalmaznak.

Javasoljuk, hogy a Azure Notebooks előzetes verzió használata az üzleti szempontból kritikus fontosságú alkalmazásokhoz, illetve a bizalmas jegyzetfüzetekhez és adatokhoz.

A Azure Notebooksával kapcsolatos kérdéseiről a [GitHub-adattáron](https://github.com/Microsoft/AzureNotebooks/issues)teheti fel a problémát.

## <a name="next-steps"></a>További lépések  

- [Minta-jegyzetfüzetek megismerése](azure-notebooks-samples.md)

- Gyorsútmutatók:

  - [Jegyzetfüzet létrehozása és megosztása](quickstart-create-share-jupyter-notebook.md)
  - [Jegyzetfüzet klónozása](quickstart-clone-jupyter-notebook.md)
  - [Helyi Jupyter-jegyzetfüzet migrálása](quickstart-migrate-local-jupyter-notebook.md)
  - [Egyéni környezet használata](quickstart-create-jupyter-notebook-project-environment.md)
  - [Bejelentkezés és felhasználói azonosító beállítása](quickstart-sign-in-azure-notebooks.md)

- Oktatóanyagok:

  - [Notebook létrehozása és futtatása](tutorial-create-run-jupyter-notebook.md  )

- Útmutató a cikkekhez:
  
  - [Projektek létrehozása és klónozása](create-clone-jupyter-notebooks.md)
  - [Projektek konfigurálása és kezelése](configure-manage-azure-notebooks-projects.md)
  - [Csomagok telepítése jegyzetfüzetből](install-packages-jupyter-notebook.md)
  - [Diavetítés bemutatása](present-jupyter-notebooks-slideshow.md)
  - [Adatfájlok használata](work-with-project-data-files.md)
  - [Adatforrások elérése](access-data-resources-jupyter-notebooks.md)
  - [Az Azure Machine Learning használata](use-machine-learning-services-jupyter-notebooks.md)
