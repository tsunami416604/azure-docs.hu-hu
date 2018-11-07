---
title: Az Azure Machine Learning fejlesztési környezetek |} A Microsoft Docs
description: Az Azure Machine Learning szolgáltatással használható fejlesztési környezetek áttekintése. Python 3 a fejlesztési környezet egyetlen követelménye, de még a Conda-környezetek használatát javasoljuk. A fejlesztői eszközök javasoljuk, a Jupyter Notebooks, Azure notebookok és kódja/IDE-szerkesztők.
services: machine-learning
author: rastala
ms.author: roastala
manager: cgronlun
ms.service: machine-learning
ms.component: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 9/24/2018
ms.openlocfilehash: 4d25e147044053aa76afe2da482b71c24efc2325
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51242874"
---
# <a name="development-environment-for-azure-machine-learning"></a>Az Azure Machine Learning a fejlesztési környezet 

További információ a fejlesztési környezetekben használható az Azure Machine Learning szolgáltatással. 

Az Azure Machine Learning szolgáltatás platform-agnosztikus, és nincs szükség egy adott fejlesztési környezetet. Szükséges __Python 3__, és azt javasoljuk, __Conda__ elkülönített környezetek létrehozása. __Ha már rendelkezik egy fejlesztési környezetben, amely megfelel ezeknek a követelményeknek__, kihagyhatja ezt a dokumentumot, és nyissa meg a [a fejlesztési környezet konfigurálása](how-to-configure-environment.md) dokumentumot.

Ez a dokumentum többi részén mutatja be a fejlesztési környezetet, javasoljuk, hogy:

* __Jupyter notebook__
* __Az Azure notebookok__
* __Integrált fejlesztési környezetekkel (IDE) és a kód szerkesztők__
* __Adatelemző virtuális gép__

Ezekben a környezetekben összehasonlítása az nehéz, mivel a jegyzetfüzetek és a ide-ket is kiterjeszthető. Például egyes ide-ket a Jupyter notebookok ügyfélként is használható. Általánosan fogalmazva __notebookok__ tervezett __interaktív kísérletezés__ és __Vizualizáció__. __Ide-k és kódszerkesztők__ biztosít eszközöket __kód minőségének javítására__ és __külső rendszerekkel való integrációt segítik__ például verziókezelést.

> [!TIP]
> Egy környezet használatával nem kizárhatja a másik használatával. Notebookok és ide-k csak eszközöket, és igény szerint lehet vegyes. Például, előfordulhat, hogy is kezdhetik a kísérletezgetést történő használatát, majd exportálja egy python-szkriptet a Szerkesztés és hibakeresés egy IDE-ben.
>
> Ezért az adatelemző virtuális gép Jupyter-notebookok és számos népszerű Python ide-ket is biztosít.

## <a name="jupyter-notebooks"></a>Jupyter-notebookok

A Jupyter Notebooks része a [Jupyter projekt](https://jupyter.org/). Ezek összpontosítanak egy interaktív kódolási biztosítása érdekében, ahol létrehozhatja az élő kód kombinálhatók a leíró szöveg és képek, dokumentumok. A Jupyter Notebooks kiszállítására különböző platformokon is telepítheti.

A saját Jupyter Notebook telepítés lehetővé teszi, hogy telepítése és konfigurálása a környezet igény szerint. Azonban Ön a rendszer karbantartásáért felelős.

## <a name="azure-notebooks"></a>Azure Notebooks

[Az Azure notebookok](https://notebooks.azure.com) (előzetes verzió) olyan notebookok környezet az Azure-felhőben. A Jupyter alapul, és előre betöltött környezetet biztosít a népszerű könyvtárak. Az Azure Machine Learning SDK már telepítve van az Azure-jegyzetfüzetekben, így kísérleteztek az szinte semmilyen beállítást.

Az Azure notebookok emellett leegyszerűsíti a notebookok megosztási folyamatának. Megosztva egy URL-címet a notebookok, győződjön meg arról, a szalagtár nyilvános, vagy megosztás a közösségi média, az Azure-jegyzetfüzetek felületén.

Azure notebookok hátránya, hogy nem rendelkezik teljes körű, a környezetet, és nem lehet szükséges, egyéni szoftverek telepítését. Egyben megosztott környezetben, így a notebookok futhat lassabb, mint egy dedikált Jupyter Notebook telepítésére.

## <a name="ides-and-code-editors"></a>Ide-k és kódszerkesztők

Nincsenek számos ide-k és kódszerkesztők, amely az Azure Machine Learning fog működni. Az egyetlen szoftver követelmény, az Azure Machine Learning SDK-t is telepíthető a használata a `pip` segédprogramot.

Javasoljuk, hogy [Visual Studio Code](https://code.visualstudio.com/), ahogy az eszközöket biztosít a Python nyelv és az Azure Machine Learning használatához. Érhető el Linux, macOS és Windows platformokról.

## <a name="data-science-virtual-machine"></a>Adatelemzési virtuális gép

Az adatelemzési virtuális gép (DSVM) az előző környezetek kombinációját. Az Azure platformon, amely rendelkezik a Jupyter Notebooks, Visual Studio Code és az Azure Machine Learning SDK előre telepített virtuális gép. Mivel a szükséges szoftver előre telepítve van a Virtuálisgép-lemezképben található, elkezdheti a kísérletezés az Azure Machine Learning gyorsan a virtuális gép létrehozása után.

A dsvm-hez van szüksége, mint például a Processzor, a grafikus Processzor és memória számítási erőforrások kiválasztását teszi lehetővé. Azt is előre telepítve van az egyéb szerkesztők, mint a PyCharm, valamint népszerű machine learning-szoftverek – például a TensorFlow, a Keras és a PyTorch. Ha a szükséges szoftver nincs telepítve, telepítheti saját magának.

Mi az előre telepített további információkért lásd: a [Mi az adatelemző virtuális gép](../data-science-virtual-machine/overview.md) dokumentumot.

## <a name="next-steps"></a>További lépések

Most, hogy a fejlesztési környezetek megismerkedett további [a fejlesztési környezet konfigurálása](how-to-configure-environment.md).

