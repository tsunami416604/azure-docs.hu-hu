---
title: Az Azure notebookok áttekintése
description: Futtassa a Jupyter notebookok felhőalapú az ingyenes Azure-jegyzetfüzetek szolgáltatáshoz, ahol nincs telepítő vagy a konfiguráció nem szükséges.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 9cea5a8e-c52d-4bdc-9e4a-cecdc1ad02c1
ms.service: notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 2640316ce8915018df30fc94df0e1fbb207e894b
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53713607"
---
# <a name="overview-of-azure-notebooks"></a>Az Azure notebookok áttekintése

Az Azure Notebooks egy ingyenes üzemeltetett szolgáltatás a Jupyter notebookok felhőben történő, telepítés nélküli fejlesztéséhez és futtatásához. [Jupyter](https://jupyter.org/) (korábbi nevén IPython-) egy nyílt forráskódú projekt, amely lehetővé teszi egyszerű egyesítése Markdown szöveget, végrehajtható kódok, állandó adatait, képeket és a Vizualizációk egyetlen, a megosztható vászonra, akkor a *notebook* (képe ajánlatának jupyter.org):

[![](https://jupyter.org/assets/jupyterpreview.png "Jupyter-notebookok")](https://jupyter.org/assets/jupyterpreview.png#lightbox)

A kód, a grafikus és magyarázó szöveg hatékony kombinációja, miatt Jupyter számos célra, beleértve a data science utasítás, adatok tisztítása és átalakítása, numerikus szimuláció, statisztikai modellezési és fejlesztését a legnépszerűbb vált Machine learning-modellek.

## <a name="hassle-free-experience"></a>Zökkenőmentes felhasználói élményt

Az Azure notebookok első lépéseket gyorsan a prototípus-készítés, az adatelemzés, tanulmányi vagy tanulási program Python nyújt segítséget:

- Értenie az adatokhoz egy teljes Anaconda környezet nincs telepítés azonnali hozzáféréssel rendelkezik.
- A tanári zökkenőmentes Python-környezetet biztosíthat a tanulók számára.
- Előadó adhat egy hasonló előadás vagy a résztvevőket fordítania a szoftver telepítése 45 perc kérése nélkül webináriumra.
- A fejlesztők és a hobbi kategóriát, egy rövid kód firkatömb notebookok használhatja.

Notebookok válhatnak még hatékonyabb, ha a személyek is együtt dolgozik a böngésző által elérhető felhőszolgáltatást, mint például az Azure-Notebookjait (előzetes verzió) segítségével. A felhőben a felhasználók kell helyileg a Jupyter telepítése vagy nem érintik magukat a környezet fenntartása. A felhőben is is egyszerűvé teszi megosztás notebookok (és kapcsolódó adatait fájlok) az egyéb jogosult felhasználók elkerülése a külső eszközökkel, például a forrás-forráskódtárházakból notebookok megosztása. Az Azure-jegyzetfüzetek a felhasználók is jegyzetfüzetek másolása (vagy "klónozása") a saját fiókba, módosítása vagy kísérleti, amely különösen hasznos utasítás célokra.

Mivel az Azure-jegyzetfüzetek szerzői általános kódot, futtatása és megosztása a platform, használhatja a számos különböző forgatókönyveket:

- Ismerje meg, egy új programozási nyelvet – próbálja ki az egyik a [frontpage oktatóanyagok](https://notebooks.azure.com/Microsoft/projects/samples/html/Introduction%20to%20Python.ipynb)
- Ismerje meg, az adatelemzés – próbálja meg [Jake VanderPlas könyv](https://notebooks.azure.com/jakevdp/projects/PythonDataScienceHandbook)
- [A tanfolyam tanít](https://notebooks.azure.com/garth-wells/projects/CUED-IA-Computing-Michaelmas) diákoknak szóló több száz
- Adjon egy webináriumra, online vagy egy konferenciáról töltött idő nélküli telepítése 
- GitHub-felhasználók közvetlenül betölteni és futtathat jegyzetfüzeteket által [egy GitHub-launch jelvény létrehozása](https://notebooks.azure.com/help/projects/sharing/create-a-github-badge)
- Adjon [diavetítések például PowerPoint](https://notebooks.azure.com/help/jupyter-notebooks/slides) ahol diák lévő kódot az végrehajtható!

Röviden az Azure-jegyzetfüzetek segítségével munkája hatékonyabbá és így még többet.

> [!Note]
> További információ a Jupyter magát találhatók [jupyter.org](https://jupyter.org/) és a a [Jupyter-dokumentáció](https://jupyter-notebook.readthedocs.io/en/latest/).

## <a name="pricing-and-quotas"></a>Díjszabás és kvóták

Azure notebookok szolgáltatás ingyenes, de a 4 GB memória- és 1 GB-os adatok visszaélések megelőzése érdekében minden projekt. Ezeket a korlátokat keretet túllépő legitim felhasználók továbbra is fut a notebookok a Captcha kihívást lásd:.

A kiadás minden korlátokat, társítsa a fiókját az Azure-előfizetéssel, amely lehetővé teszi, hogy válassza ki a prémium szintű számítási szintek.

## <a name="available-kernels-and-environments"></a>Notebookokhoz elérhető kernelek és környezetek

Minden egyes notebook válassza ki a kernel (azaz a futtatókörnyezet) bármilyen kód cellák futtatására szolgál. Az Azure-jegyzetfüzeteket a következő kernelekkel támogatja:

- Python 2.7 + Anaconda2-5.3.0
- A Python 3.6-os + Anaconda3-5.3.0
- Python 3.5-ös + Anaconda3 4.2.0 (elavulttá válik)
- R 3.4.1 + Microsoft R 3.4.1 megnyitása
- F#4.1.9

Az Azure notebookok további csomagokat az alap disztribúciók túl is tartalmaz. A Python kernelekkel, például a numpy, a pandas, a scikit-ismerje meg, matplotlib és bokeh kódtárakat.

A környezet összes jegyzetfüzet létrehozása a projektben egy projektet is testreszabhatja. További információkért lásd: [a rövid útmutató: Hozzon létre egy projektet egy egyéni környezettel rendelkező](quickstart-create-jupyter-notebook-project-environment.md).

Az alap disztribúciók mellett Azure notebookok további csomagok száma, amelyek hasznosak a adatszakértők számára előre telepítve áll rendelkezésre. A saját csomagokat a tipikus folyamat segítségével az egyes nyelvekhez is telepítheti.

## <a name="pre-configured-jupyter-extensions"></a>Előre konfigurált Jupyter-bővítmények

Az Azure notebookok előre konfigurálva, a következő Jupyter kiterjesztésű:

- [EZÉRT](https://github.com/damianavila/RISE): A Jupyter diavetítés bővítmény (más néven live_reveal). További információkért lásd: [futtatása egy jegyzetfüzetet Diavetítés](present-jupyter-notebooks-slideshow.md).
- [JupyterLab](https://github.com/jupyterlab/jupyterlab): A Jupyter notebookok használata teljes számítási környezetet.
- [Altair](https://github.com/ellisonbg/altair): Deklaratív statisztikai Vizualizáció kódtára a Pythonhoz.
- [BQPlot](https://github.com/bloomberg/bqplot): Egy interaktív ábrázolási keretrendszer Jupyter notebookokhoz.
- [IpyWidgets](https://github.com/jupyter-widgets/ipywidgets): Interaktív HTML widgetek Jupyter notebookokhoz.

## <a name="issues-and-getting-help"></a>Problémák és segítség kérése

Mivel az Azure-jegyzetfüzetek továbbra is előzetes verzióban érhető el, a szolgáltatás tapasztalhat, amelyek gyakoribb vagy más Azure-szolgáltatásokkal, mint már tartós átmeneti kimaradásainak. Egyes funkciók, hiányos vagy hibákat tartalmaz.

Jelenleg javasoljuk, hogy üzleti szempontból kritikus fontosságú alkalmazások, vagy a bizalmas notebookok és az adatok Azure-on Előzetesben notebookok használatával szemben.

Vitatni a kérdéseket Azure jegyzetfüzetekkel kapcsolatos, jelentse be a problémát a [GitHub-adattár](https://github.com/Microsoft/AzureNotebooks/issues).

## <a name="next-steps"></a>További lépések  

- [Ismerkedés a mintafüzetek](azure-notebooks-samples.md)

- Gyorsútmutatók:

  - [Hozzon létre és osszon meg a notebook](quickstart-create-share-jupyter-notebook.md)
  - [A notebook klónozása](quickstart-clone-jupyter-notebook.md)
  - [Helyi Jupyter notebook áttelepítése](quickstart-migrate-local-jupyter-notebook.md)
  - [Egy egyéni környezet használata](quickstart-create-jupyter-notebook-project-environment.md)
  - [Jelentkezzen be, és állítsa be a felhasználói azonosító](quickstart-sign-in-azure-notebooks.md)

- Oktatóanyagok:

  - [Hozzon létre és -jegyzetfüzet futtatása](tutorial-create-run-jupyter-notebook.md  )

- Útmutatók:
  
  - [Hozzon létre, és klónozza a projektek](create-clone-jupyter-notebooks.md)
  - [Konfigurálhatja és kezelheti a projektek](configure-manage-azure-notebooks-projects.md)
  - [Egy jegyzetfüzetet a csomagok telepítése](install-packages-jupyter-notebook.md)
  - [Diavetítés bemutatásához](present-jupyter-notebooks-slideshow.md)
  - [Adatfájlok használata](work-with-project-data-files.md)
  - [Adatok erőforrások eléréséhez](access-data-resources-jupyter-notebooks.md)
  - [Az Azure Machine Learning-szolgáltatások használata](use-machine-learning-services-jupyter-notebooks.md)
