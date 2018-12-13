---
title: Hasonlítsa össze a machine learning a Microsoft termék-beállítások
description: Összehasonlíthat a Microsoft kínálatában található számos különféle terméket, hogy megalkothassa, üzembe helyezhesse és kezelhesse a gépi tanuláson alapuló modelljeit. Eldöntheti, mely terméket válassza ki a saját megoldásához.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: overview
ms.reviewer: jmartens
author: garyericson
ms.author: garye
ms.date: 12/04/2018
ms.openlocfilehash: bae28fd24cfbbf9fe36a626af1f0ec9359c8cda0
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53274119"
---
# <a name="what-are-the-machine-learning-products-at-microsoft"></a>Mik azok a machine learning-termékek, a Microsoft?

A Microsoft számos különféle terméket kínál, amelyekkel megalkothatja, üzembe helyezheti és kezelheti gépi tanuláson alapuló modelljeit. A termékek összehasonlításával kiválaszthatja, melyikkel lesz a leghatékonyabb a gépi tanuláson alapuló megoldásának fejlesztése.

| Gépi tanuláson alapuló termék | Mi ez? | Mik a lehetőségei |
|-|-|-|
| ***A felhőben*** | | |
| [Azure Machine Learning szolgáltatás](#azure-machine-learning-services) | Felügyelt felhőszolgáltatás Machine Learninghez  | Modellek betanítása, üzembe helyezése és kezelése Python és CLI használatával |
| [Az Azure Machine Learning Studióban](#azure-machine-learning-studio) | A csomóponthúzási&ndash;és&ndash;dobja el a vizuális felhasználói felületet, a gépi tanulás | Modellek létrehozása és üzembe helyezése, illetve tanulási kísérletek futtatása előre konfigurált algoritmusok használatával |
| [Azure Databricks](#azure-databricks) | Spark-alapú elemzési platform | Modellek és adat-munkafolyamatok létrehozása és üzembe helyezése |
| [Azure Cognitive Services](#azure-cognitive-services) | Azure szolgáltatás előre összeállított, mesterséges intelligencián és gépi tanuláson alapuló modellekkel | Intelligens funkciók egyszerű hozzáadása alkalmazásokhoz |
| [Azure Data Science Virtual Machine](#azure-data-science-virtual-machine) | Virtuális gép előre telepített adatelemzési eszközökkel | Gépi tanuláson alapuló megoldások fejlesztése előre konfigurált környezetben |
| ***A helyszínen***<br>*(a helyszíni kiszolgálók is futtatható virtuális gépen a felhőben)* | | |
| [SQL Server Machine Learning-szolgáltatások](#sql-server-machine-learning-services) | SQL-be ágyazott elemzési motor | Modellek létrehozása és üzembe helyezése SQL Serveren belül |
| [Microsoft Machine Learning Server](#microsoft-machine-learning-server) | Különálló nagyvállalati kiszolgáló prediktív elemzésekhez | Modellek építése és üzembe helyezése R és Python használatával |
| ***Fejlesztői eszközök*** | | |
| [ML.NET](#mlnet) | Nyílt forráskódú, többplatformos Machine Learning SDK | Gépi tanuláson alapuló megoldások fejlesztése .NET-alkalmazásokhoz |
| [Windows ML](#windows-ml) | Windows 10 Machine Learning platform | Betanított modellek értékelése Windows 10-es eszközökön |

## <a name="azure-machine-learning-service"></a>Azure Machine Learning szolgáltatás

[Az Azure Machine Learning szolgáltatás](overview-what-is-azure-ml.md) egy teljes körűen felügyelt felhőszolgáltatás, használt betanításához, üzembe helyezése és kezelése a gépi Tanulási modelleket ipari méretekben. Teljes körűen támogatja a nyílt forráskódú technológiákat, így több tízezer nyílt forráskódú Python-csomaggal, többek között TensorFlow-val, PyTorch-csal és scikit-learnnel is használható. Gazdag eszközök is elérhetők, például [Azure notebookok](https://notebooks.azure.com/), [Jupyter notebookok](http://jupyter.org), vagy a [a Visual Studio Code az Azure Machine Learning](https://visualstudio.microsoft.com/downloads/ai-tools-vscode/) bővítmény megkönnyíti a felfedezése és alakíthat át adatokat, és ezután betanítása és modellek üzembe helyezése. Az Azure Machine Learning szolgáltatás funkcióival a modellek generálása és finomhangolása könnyedén, hatékonyan és pontosan automatizálható.

Az Azure Machine Learning szolgáltatással a Python és a CLI használatával taníthat be, helyezhet üzembe és kezelhet gépi tanuláson alapuló modelleket ipari méretekben.

Próbálja ki a [Azure Machine Learning szolgáltatás ingyenes vagy fizetős verzióját](http://aka.ms/AMLFree) még ma.

## <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio

Az [Azure Machine Learning Studio](../studio/what-is-ml-studio.md) interaktív, grafikus munkaterületet nyújt, amelyen az előre összeállított gépi tanuláson alapuló algoritmusok használatával könnyedén hozhat létre, tesztelhet és helyezhet üzembe modelleket. A Machine Learning Studio a modelleket webszolgáltatásként teszi közzé, amelyeket az egyéni alkalmazások vagy az Excel és más üzletiintelligencia-eszközök egyszerűen felhasználhatnak.
Nincs szükség programozásra – a gépi tanuláson alapuló modellek létrehozásához elegendő az adathalmazokat és az elemzési modulokat egy interaktív vásznon összekapcsolni, ezt követően pedig a modell már néhány kattintással üzembe is helyezhető.

Akkor érdemes a Machine Learning Studiót használnia, ha programkód írása nélkül szeretne modelleket fejleszteni és üzembe helyezni.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="azure-databricks"></a>Azure Databricks

Az [Azure Databricks](/azure/azure-databricks/what-is-azure-databricks) a Microsoft Azure Cloud Services platformra optimalizált Apache Spark-alapú elemzési platform. A Databricks integrálva van az Azure-ral, így egyetlen kattintással beállítható, zökkenőmentes munkafolyamatokat tesz lehetővé, valamint olyan interaktív munkaterületet biztosít, amellyel lehetővé válik az adatszakértők, az adatmérnökök és az üzleti elemzők közötti együttműködés.
Python-, R-, Scala- és SQL-kód használatával adatokat kérdezhet le, vizualizálhat és modellezhet webes jegyzetfüzetekben.

Akkor érdemes a Databrickset használnia, ha az Apache Sparkon másokkal együttműködve szeretne gépi tanuláson alapuló megoldásokat létrehozni.

## <a name="azure-cognitive-services"></a>Azure Cognitive Services

Az [Azure Cognitive Services](/azure/cognitive-services/welcome) egy olyan API-készlet, amely lehetővé teszi a természetes kommunikációs módszereket használó alkalmazások összeállítását. Az API-k csupán néhány sornyi kód alapján lehetővé teszik az alkalmazások számára, hogy lássanak, halljanak, beszéljenek, megértsék és értelmezzék a felhasználó szükségleteit. Egyszerűen hozzáadhat intelligens funkciókat az alkalmazáshoz, például a következőket: 

- Érzelemfelismerés
- Látvány- és beszédfelismerés
- Language understanding (LUIS)
- Ismeretek és keresés

A Cognitive Services különböző eszközökre és platformokra történő alkalmazásfejlesztésre használható. Az API-k egyre fejlettebbek lesznek, és könnyű őket beállítani.

## <a name="azure-data-science-virtual-machine"></a>Azure Data Science Virtual Machine

Az [Azure Data Science Virtual Machine](../data-science-virtual-machine/overview.md) egy személyre szabott virtuálisgép-környezet a Microsoft Azure-felhőben, amelyet kifejezetten adatelemzésre hoztak létre. Számos népszerű adatelemzési és egyéb eszköz található meg rajta előre telepítve és konfigurálva, amelyek jelentősen felgyorsítják az intelligens alkalmazások fejlett elemzésekhez történő összeállítását.

Azure Machine Learning szolgáltatás célként támogatja a Data Science Virtual Machine-t.
Windows és Linux Ubuntu (az Azure Machine Learning szolgáltatás nem támogatott Linux CentOS) verzió érhető el.
Egy adott verzió információiért és a benne található funkciók listájáért lásd a [Azure Data Science Virtual Machine (adatelemző virtuális gép) bemutatása](../data-science-virtual-machine/overview.md) című témakört.

Akkor érdemes az Adatelemzési virtuális gépet használnia, ha egyetlen csomóponton kell futtatnia a feladatait. Illetve ha távolról kell virtuálisan felskáláznia a feldolgozást egyetlen gépen.

## <a name="sql-server-machine-learning-services"></a>SQL Server Machine Learning-szolgáltatások

Az [SQL Server Microsoft Machine Learning-szolgáltatás](https://docs.microsoft.com/sql/advanced-analytics/r/r-services) az SQL Server-adatbázisok relációs adatait statisztikai elemzéssel, adatvizualizációval, illetve R- és Python-kódot használó prediktív elemzéssel egészíti ki. A Microsoft R- és Python-kódtárai között megtalálhatók olyan fejlett modellezési és gépi tanulási algoritmusok is, amelyek párhuzamosan és nagy számosságban is képesek futni az SQL Serveren.

Akkor érdemes az SQL Server Machine Learning-szolgáltatásokat használnia, ha egy előre létrehozott mesterséges intelligenciára és prediktív elemzésre van szüksége az SQL Server relációs adataihoz.

## <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning-kiszolgáló

A [Microsoft Machine Learning-kiszolgáló](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server) egy vállalati kiszolgáló, amely R- és Python-folyamatok párhuzamos és elosztott munkaterheléseinek futtatására és kezelésére szolgál. A Microsoft Machine Learning-kiszolgáló Linux és Windows rendszeren, illetve Hadoopon és Apache Sparkon fut, valamint elérhető a [HDInsight-on](https://azure.microsoft.com/services/hdinsight/r-server/) is. Végrehajtómotorokat biztosít a [RecoScaleR-](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), a [revoscalepy-](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package) és a [Microsoft Machine Learning-csomagokkal](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package) összeállított megoldásokhoz, és kiterjeszti a nyílt forráskódú R és Python támogatását a nagy teljesítményű elemzésre, a statisztikai elemzésre, a gépi tanulásra és a nagyméretű adatkészletekre. Ezt a funkciót a kiszolgálóval telepített jogvédett csomagok biztosítják. Fejlesztéshez olyan IDE-ket használhat, mint például az [R Tools for Visual Studio](https://www.visualstudio.com/vs/rtvs/) és a [Python Tools for Visual Studio](https://www.visualstudio.com/vs/python/).

Akkor érdemes a Microsoft Machine Learning-kiszolgálót használnia, ha egy kiszolgálón R vagy Python használatával szeretne modelleket létrehozni, vagy így készült modelleket szeretne üzembe helyezni, illetve ha R- vagy Python-betanítást szeretne nagy méretben kiosztani egy Hadoop- vagy Spark-fürtön

## <a name="mlnet"></a>ML.NET

Az [ML-NET](https://docs.microsoft.com/dotnet/machine-learning/) egy ingyenes, nyílt forráskódú, többplatformos gépi tanulási keretrendszer, amellyel gépi tanuláson alapuló egyéni megoldásokat hozhat létre, majd integrálhat .NET-alkalmazásokban.

Akkor érdemes az ML.NET-et használnia, ha gépi tanuláson alapuló megoldásokat szeretne integrálni a .NET-alkalmazásaiba.

## <a name="windows-ml"></a>Windows ML

A [Windows ML](https://docs.microsoft.com/windows/uwp/machine-learning/) segítségével gépi tanuláson alapuló betanított modelleket használhat az alkalmazásaiban, és kiértékelheti a betanított modelleket helyileg, Windows 10-eszközökön.

Akkor érdemes a Windows ML-t használni, ha gépi tanuláson alapuló, már betanított modelleket szeretne használni a Windows-alkalmazásaiban.

## <a name="next-steps"></a>További lépések

- A Microsoftnál elérhető összes mesterségesintelligencia-fejlesztési termékről a [Microsoft AI-platformon](https://www.microsoft.com/ai) olvashat.
- Az AI-megoldások fejlesztésével kapcsolatos képzésről a [Microsoft AI School](https://aischool.microsoft.com/learning-paths) oldalán tájékozódhat.
