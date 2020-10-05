---
title: Mi az Azure Data Science Virtual Machine
titleSuffix: Azure Data Science Virtual Machine
description: Az Azure Data Science Virtual Machine áttekintése – egy könnyen használható virtuális gép az Azure Cloud platformon előre telepített és konfigurált eszközökkel és könyvtárakkal az adatelemzéshez.
keywords: adatelemzési eszközök, adatelemző virtuális gép, eszközök adatelemzéshez, linux adatelemzés
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 04/02/2020
ms.openlocfilehash: 2bfcdfcb01e7908c199054e793d82cdfa1b726c7
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "88816337"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>Mi a Linux és a Windows rendszerhez készült Azure Data Science Virtual Machine?

A Data Science Virtual Machine (DSVM) egy testreszabott virtuálisgép-rendszerkép az Azure Cloud platformon, amely kifejezetten az adatelemzéshez készült. Számos népszerű adatelemzési eszköz előre telepítve van, és előre konfigurálva van az intelligens alkalmazások speciális analitikai felépítése érdekében.

A DSVM a következő címen érhető el:

+ Windows Server 2019
+ Ubuntu 18,04 LTS

## <a name="comparison-with-azure-machine-learning"></a>Összehasonlítás a Azure Machine Learning

A DSVM egy testreszabott virtuálisgép-rendszerkép az adatelemzéshez, de a [Azure Machine learning](https://docs.microsoft.com/azure/machine-learning/overview-what-is-azure-ml) (AzureML) egy végpontok közötti platform, amely a következőket foglalja magában:

+ Teljes körűen felügyelt számítás
  + Compute Instances (Számítási példányok)
  + Elosztott ML-feladatok számítási fürtök
  + Fürtök következtetése valós idejű pontozáshoz
+ Adattárolók (például blob, ADLS Gen2, SQL DB)
+ Kísérlet követése
+ Modellkezelés
+ Notebooks
+ Környezetek (Conda és R-függőségek kezelése)
+ Címkézés
+ Folyamatok (a végpontok közötti adatelemzési munkafolyamatok automatizálása)

### <a name="comparison-with-azureml-compute-instances"></a>Összehasonlítás AzureML számítási példányokkal

[Azure Machine learning számítási példányok](https://docs.microsoft.com/azure/machine-learning/concept-compute-instance) teljes mértékben konfigurált és __felügyelt__ virtuálisgép-rendszerképek, míg a DSVM egy nem __felügyelt__ virtuális gép.

A két termék-ajánlat közötti fő különbségek a következők:


|Szolgáltatás |Adattudomány<br>VM |AzureML<br>Számítási példány  | 
|---------|---------|---------|
| Teljes körűen felügyelt | Nem        | Igen        |
|Nyelvi támogatás     |  Python, R, Julia, SQL, C#,<br> Java, Node.js, F #       | Python és R        |
|Operációs rendszer     | Ubuntu<br>Windows         |    Ubuntu     |
|Előre konfigurált GPU-beállítás     |  Igen       |    Igen     |
|Vertikális Felskálázási beállítás | Igen | Igen |
|SSH-hozzáférés    | Igen        |    Igen     |
|RDP-hozzáférés    | Igen        |     Nem    |
|Beépített<br>Üzemeltetett jegyzetfüzetek     |   Nem<br>(további konfiguráció szükséges)      |      Igen   |
|Beépített egyszeri bejelentkezés     | Nem <br>(további konfiguráció szükséges)         |    Igen     |
|Beépített együttműködés     | Nem         | Igen        |
|Előre telepített eszközök     |  Jupyter (labor), RStudio-kiszolgáló, VSCode,<br> Visual Studio, Notebookshoz, Juno,<br>Power BI Desktop, SSMS, <br>Microsoft Office 365, Apache Drill       |     Jupyter (labor)<br> RStudio Server   |

## <a name="sample-use-cases"></a>Használati példák

Alább bemutatjuk néhány gyakori használati esetet az ügyfelek DSVM.

### <a name="short-term-experimentation-and-evaluation"></a>Rövidtávú kísérletezés és kiértékelés

A DSVM segítségével kiértékelheti vagy megismerheti az új adatelemzési [eszközöket](./tools-included.md), különösen a közzétett [minták és](./dsvm-samples-and-walkthroughs.md)útmutatók egyes részein.

### <a name="deep-learning-with-gpus"></a>Mély tanulás grafikus processzorokkal

A DSVM a képzési modellek mély tanulási algoritmusokat használhatnak a grafikus feldolgozási egységeken (GPU-k) alapuló hardveren. Az Azure platform virtuálisgép-méretezési képességeinek kihasználásával a DSVM a Felhőbeli GPU-alapú hardverek igény szerinti használatát teszi lehetővé. Nagy modellek betanításakor, vagy ha nagy sebességű számításokra van szükség, az operációsrendszer-lemez megtartásával válthat GPU-alapú virtuális gépre. Az N sorozatú GPU-k által engedélyezett virtuálisgép-SKU-k bármelyikét kiválaszthatja a DSVM. Megjegyzés: a GPU-t használó virtuális gépek nem támogatottak az ingyenes Azure-fiókokban.

A DSVM Windows-kiadásai előre telepítve vannak a GPU-illesztőprogramok, keretrendszerek és a Deep learning-keretrendszerek GPU-verzióival. A Linux-kiadásokban a Deep learning on GPU-k engedélyezve vannak az Ubuntu-Dsvm. 

A DSVM Ubuntu-vagy Windows-kiadásait egy olyan Azure-beli virtuális gépre is üzembe helyezheti, amely nem a GPU-k alapján van. Ebben az esetben az összes mély tanulási keretrendszer vissza fog térni a CPU-módra.

[További információ az elérhető Deep learning-és AI-keretrendszerekről](dsvm-tools-deep-learning-frameworks.md).

### <a name="data-science-training-and-education"></a>Adatelemzési képzés és oktatás

A nagyvállalatoknál adatelemzési képzéseket tartó oktatók általában biztosítanak egy virtuálisgép-lemezképet. A rendszerkép biztosítja, hogy a tanulók konzisztens beállításokkal rendelkezzenek, és hogy a minták kiszámíthatóan működjenek.

A DSVM egy igény szerinti környezetet hoz létre egy konzisztens beállítással, amely megkönnyíti a támogatási és kompatibilitási kihívásokat. Olyan esetekben, amikor gyakran kell környezetet kiépíteni, különösen a rövidebb kurzusokhoz, ez jelentős előnnyel jár.


## <a name="whats-included-on-the-dsvm"></a>Mit tartalmaz a DSVM?

A Windows-és Linux-Dsvm található eszközök teljes listáját [itt](tools-included.md)találja.

## <a name="next-steps"></a>További lépések

További információ ezekről a cikkekről:

+ Windows:
  + [Windowsos DSVM beállítása](provision-vm.md)
  + [Adatelemzés Windows DSVM](vm-do-ten-things.md)

+ Linux:
  + [Linux DSVM (Ubuntu) beállítása](dsvm-ubuntu-intro.md)
  + [Adatelemzés Linux-DSVM](linux-dsvm-walkthrough.md)
