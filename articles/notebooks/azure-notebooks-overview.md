---
title: Az Azure notebookok áttekintése
description: Futtassa a Jupyter notebookok felhőalapú az ingyenes Azure-jegyzetfüzetek szolgáltatáshoz, ahol nincs telepítő vagy a konfiguráció nem szükséges.
ms.topic: overview
ms.date: 04/05/2019
ms.openlocfilehash: b3dfa6cec962809fad1a03f5100c55315ff6a9ad
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277681"
---
# <a name="overview-of-azure-notebooks"></a>Az Azure notebookok áttekintése

Az Azure Notebooks egy ingyenes üzemeltetett szolgáltatás a Jupyter notebookok felhőben történő, telepítés nélküli fejlesztéséhez és futtatásához. A [Jupyter](https://jupyter.org/) (korábbi nevén IPython) egy nyílt forráskódú projekt, amely lehetővé teszi a Markdown-szöveg, a végrehajtható kód, az állandó adatok, a grafikák és a vizualizációk egyszerű egyesítését egyetlen, megosztható vászonra, a *notebookra* (a jupyter.org rendszerkép jóvoltából):

[Példák a Jupyter notebookokra ![](https://jupyter.org/assets/jupyterpreview.png)](https://jupyter.org/assets/jupyterpreview.png#lightbox)

A kód, a grafikus és magyarázó szöveg hatékony kombinációja, miatt Jupyter számos célra, beleértve a data science utasítás, adatok tisztítása és átalakítása, numerikus szimuláció, statisztikai modellezési és fejlesztését a legnépszerűbb vált Machine learning-modellek.

## <a name="hassle-free-experience"></a>Zökkenőmentes felhasználói élményt

Az Azure notebookok első lépéseket gyorsan a prototípus-készítés, az adatelemzés, tanulmányi vagy tanulási program Python nyújt segítséget:

- Értenie az adatokhoz egy teljes Anaconda környezet nincs telepítés azonnali hozzáféréssel rendelkezik.
- A tanári zökkenőmentes Python-környezetet biztosíthat a tanulók számára.
- Előadó adhat egy hasonló előadás vagy a résztvevőket fordítania a szoftver telepítése 45 perc kérése nélkül webináriumra.
- A fejlesztők és a hobbi kategóriát, egy rövid kód firkatömb notebookok használhatja.

Notebookok válhatnak még hatékonyabb, ha a személyek is együtt dolgozik a böngésző által elérhető felhőszolgáltatást, mint például az Azure-Notebookjait (előzetes verzió) segítségével. A felhőben a felhasználók kell helyileg a Jupyter telepítése vagy nem érintik magukat a környezet fenntartása. A felhőben is is egyszerűvé teszi megosztás notebookok (és kapcsolódó adatait fájlok) az egyéb jogosult felhasználók elkerülése a külső eszközökkel, például a forrás-forráskódtárházakból notebookok megosztása. Az Azure-jegyzetfüzetek a felhasználók is jegyzetfüzetek másolása (vagy "klónozása") a saját fiókba, módosítása vagy kísérleti, amely különösen hasznos utasítás célokra.

Mivel az Azure-jegyzetfüzetek szerzői általános kódot, futtatása és megosztása a platform, használhatja a számos különböző forgatókönyveket:

- Ismerkedjen meg az új programozási nyelvvel – próbálja ki a [FrontPage oktatóanyagok](https://notebooks.azure.com/Microsoft/projects/samples/html/Introduction%20to%20Python.ipynb) egyikét
- Adatelemzés – a [Jake VanderPlas](https://notebooks.azure.com/jakevdp/projects/PythonDataScienceHandbook) kipróbálása
- Több száz diák [képzésének tanítása](https://notebooks.azure.com/garth-wells/projects/CUED-IA-Computing-Michaelmas)
- Adjon egy webináriumra, online vagy egy konferenciáról töltött idő nélküli telepítése 
- A GitHub-felhasználók közvetlen betöltését és futtatását teszi lehetővé a [GitHub indítási jelvényének létrehozásával](https://notebooks.azure.com/help/projects/sharing/create-a-github-badge)
- A [PowerPointhoz hasonló diavetítéseket](https://notebooks.azure.com/help/jupyter-notebooks/slides) adhat meg, ahol a diák kódja végrehajtható!

Röviden az Azure-jegyzetfüzetek segítségével munkája hatékonyabbá és így még többet.

> [!Note]
> A Jupyter-ről a [jupyter.org](https://jupyter.org/) és a [Jupyter dokumentációjában](https://jupyter-notebook.readthedocs.io/en/latest/)találhat további információt.

## <a name="pricing-and-quotas"></a>Díjszabás és kvóták

Azure Notebooks egy ingyenes szolgáltatás, de az egyes projektek 4 GB memóriával és 1 GB-nyi adattal rendelkeznek a visszaélések elkerülése érdekében. Ezeket a korlátokat keretet túllépő legitim felhasználók továbbra is fut a notebookok a Captcha kihívást lásd:.

Az összes korlát felszabadításához jelentkezzen be Azure Notebooks egy olyan fiókkal, amely Azure Active Directory (például egy vállalati fiók) használatával. Ha ez a fiók Azure-előfizetéshez van társítva, az adott előfizetéshez tartozó Azure Data Science Virtual Machine-példányokhoz is csatlakozhat. További információ: [projektek kezelése és konfigurálása – számítási réteg](configure-manage-azure-notebooks-projects.md#compute-tier).

A notebook-kiszolgálók legfeljebb 8 órán keresztül vannak garantálva. A legtöbb esetben a tároló nem vonatkozik erre a korlátozásra, és továbbra is az idő függvényében fut, de a hosszú élettartamú munkamenetek időnként leállíthatók a rendszer stabilitására.

## <a name="available-kernels-and-environments"></a>Notebookokhoz elérhető kernelek és környezetek

Minden egyes notebook válassza ki a kernel (azaz a futtatókörnyezet) bármilyen kód cellák futtatására szolgál. Az Azure-jegyzetfüzeteket a következő kernelekkel támogatja:

- Python 2.7 + Anaconda2-5.3.0
- A Python 3.6-os + Anaconda3-5.3.0
- Python 3.5-ös + Anaconda3 4.2.0 (elavulttá válik)
- R 3.4.1 + Microsoft R Open 3.4.1
- F#4.1.9

Az Azure notebookok további csomagokat az alap disztribúciók túl is tartalmaz. A Python kernelekkel, például a numpy, a pandas, a scikit-ismerje meg, matplotlib és bokeh kódtárakat.

A környezet összes jegyzetfüzet létrehozása a projektben egy projektet is testreszabhatja. További információ [: gyors útmutató: projekt létrehozása egyéni környezettel](quickstart-create-jupyter-notebook-project-environment.md).

Az alap disztribúciók mellett Azure notebookok további csomagok száma, amelyek hasznosak a adatszakértők számára előre telepítve áll rendelkezésre. A saját csomagokat a tipikus folyamat segítségével az egyes nyelvekhez is telepítheti.

## <a name="pre-configured-jupyter-extensions"></a>Előre konfigurált Jupyter-bővítmények

Az Azure notebookok előre konfigurálva, a következő Jupyter kiterjesztésű:

- [Rise](https://github.com/damianavila/RISE): A Jupyter Slideshow bővítmény (más néven live_reveal). További információ: jegyzetfüzet- [Diavetítés futtatása](present-jupyter-notebooks-slideshow.md).
- [JupyterLab](https://github.com/jupyterlab/jupyterlab): teljes számítási környezet a Jupyter-jegyzetfüzetek használatához.
- [Altair](https://github.com/ellisonbg/altair): a Python deklaratív statisztikai vizualizációs könyvtára.
- [BQPlot](https://github.com/bloomberg/bqplot): interaktív ábrázolási keretrendszer a Jupyter-jegyzetfüzetekhez.
- [IpyWidgets](https://github.com/jupyter-widgets/ipywidgets): interaktív HTML-widgetek Jupyter-jegyzetfüzetekhez.

## <a name="issues-and-getting-help"></a>Problémák és segítség kérése

Mivel az Azure-jegyzetfüzetek továbbra is előzetes verzióban érhető el, a szolgáltatás tapasztalhat, amelyek gyakoribb vagy más Azure-szolgáltatásokkal, mint már tartós átmeneti kimaradásainak. Egyes funkciók, hiányos vagy hibákat tartalmaz.

Jelenleg javasoljuk, hogy üzleti szempontból kritikus fontosságú alkalmazások, vagy a bizalmas notebookok és az adatok Azure-on Előzetesben notebookok használatával szemben.

A Azure Notebooksával kapcsolatos kérdéseiről a [GitHub-adattáron](https://github.com/Microsoft/AzureNotebooks/issues)teheti fel a problémát.

## <a name="next-steps"></a>Következő lépések  

- [Minta-jegyzetfüzetek megismerése](azure-notebooks-samples.md)

- Gyorsútmutatók:

  - [Jegyzetfüzet létrehozása és megosztása](quickstart-create-share-jupyter-notebook.md)
  - [Jegyzetfüzet klónozása](quickstart-clone-jupyter-notebook.md)
  - [Helyi Jupyter-jegyzetfüzet migrálása](quickstart-migrate-local-jupyter-notebook.md)
  - [Egyéni környezet használata](quickstart-create-jupyter-notebook-project-environment.md)
  - [Jelentkezzen be, és állítson be egy felhasználói azonosítót](quickstart-sign-in-azure-notebooks.md)

- Oktatóanyagok:

  - [Jegyzetfüzet létrehozása és futtatása](tutorial-create-run-jupyter-notebook.md  )

- Útmutatók:
  
  - [Projektek létrehozása és klónozása](create-clone-jupyter-notebooks.md)
  - [Projektek konfigurálása és kezelése](configure-manage-azure-notebooks-projects.md)
  - [Csomagok telepítése jegyzetfüzetből](install-packages-jupyter-notebook.md)
  - [Bemutató megjelenítése](present-jupyter-notebooks-slideshow.md)
  - [Adatfájlok használata](work-with-project-data-files.md)
  - [Hozzáférés az adaterőforrásokhoz](access-data-resources-jupyter-notebooks.md)
  - [Azure Machine Learning használata](use-machine-learning-services-jupyter-notebooks.md)
