---
title: Mi az Azure Data Science Virtual Machine
titleSuffix: Azure Data Science Virtual Machine
description: Az Azure Data Science Virtual Machine áttekintése – egyszerűen létrehozhat és használhat virtuális gépeket az Azure Cloud platformon előre telepített és konfigurált eszközökkel és könyvtárakkal az adatelemzéshez és az intelligens alkalmazások fejlesztéséhez.
keywords: adatelemzési eszközök, adatelemző virtuális gép, eszközök adatelemzéshez, linux adatelemzés
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 04/02/2020
ms.openlocfilehash: 03bfee258fe96d90c32b6a305b99856a11d9a087
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80754986"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>Mi a Linux és a Windows rendszerhez készült Azure Data Science Virtual Machine?

A Data Science Virtual Machine (DSVM) egy testreszabott virtuálisgép-rendszerkép az Azure Cloud platformon, amely kifejezetten az adatelemzéshez készült. Számos népszerű adatelemzési eszköz előre telepítve van és előre konfigurálva van az intelligens alkalmazások speciális elemzésekhez való kialakításához. 

A DSVM a következő címen érhető el:

+ **Windows Server 2019**
+ **Ubuntu 18,04 LTS**
+ Windows Server 2016
+ Ubuntu 16.04 LTS

> [!NOTE]
> A Deep learninghez készült összes virtuálisgép-eszköz be lett hajtva a Data Science Virtual Machineba. 

## <a name="why-choose-the-dsvm"></a>Miért érdemes kiválasztani a DSVM?

A Data Science Virtual Machine célja, hogy minden képzettségi szinten és az iparágon keresztül biztosítson szakembereket egy súrlódás nélküli, előre konfigurált adatelemzési környezettel. Ahelyett, hogy saját maga is kiépít egy hasonló munkaterületet, létrehozhat egy DSVM. Ez a választás a telepítés, a konfiguráció és a csomag felügyeleti folyamatainál akár napokat, akár _heteket_ is megtakaríthat. A DSVM üzembe helyezése után azonnal munkához láthat adatelemzési projektjén.

## <a name="sample-use-cases"></a>Példa használati esetekre

Alább bemutatjuk néhány gyakori használati esetet a DSVM-ügyfelek számára.

### <a name="moving-data-science-workloads-to-the-cloud"></a>Adatelemzési számítási feladatok áthelyezése a felhőbe

A DSVM olyan adatelemző csapatok számára biztosít alapkonfigurációt, amelyek a helyi asztalait egy felügyelt Felhőbeli asztallal szeretnék helyettesíteni, így biztosítva, hogy egy adott csapat összes adatszakértője konzisztens módon legyen beállítva a kísérletek ellenőrzéséhez és az együttműködés előmozdításához. A rendszergazdai terhek csökkentésével csökkenti a költségeket is. Ez a terhek csökkentése a speciális analitikai szoftvercsomagok kiértékeléséhez, telepítéséhez és karbantartásához szükséges időt takarít meg.

### <a name="data-science-training-and-education"></a>Adatelemzési képzés és oktatás

Az adatelemzési osztályokat tanító vállalati oktatók és oktatók általában virtuálisgép-rendszerképet biztosítanak. A rendszerkép biztosítja, hogy a tanulók konzisztens beállításokkal rendelkezzenek, és hogy a minták kiszámíthatóan működjenek. 

A DSVM egy igény szerinti környezetet hoz létre egy konzisztens beállítással, amely megkönnyíti a támogatási és kompatibilitási kihívásokat. Olyan esetekben, amikor gyakran kell környezetet kiépíteni, különösen a rövidebb kurzusokhoz, ez jelentős előnnyel jár.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Igény szerinti rugalmas kapacitás nagyszabású projektekhez

Az adatelemzési ötletbörzékhez/versenyeken, illetve a nagyméretű adatmodellezésen és-kutatáson túl nagy kapacitást kell kibővíteni, jellemzően rövid időtartamra. A DSVM segítségével gyorsan, igény szerint replikálhatja az adatelemzési környezetet, a nagy teljesítményű számítástechnikai erőforrások által futtatható kísérleteket lehetővé tevő kibővített kiszolgálókon.

### <a name="custom-compute-power-for-azure-notebooks"></a>Azure Notebooks egyéni számítási teljesítmény

[Azure Notebooks](../../notebooks/azure-notebooks-overview.md) egy ingyenes üzemeltetett szolgáltatás, amely a felhőben Jupyter-jegyzetfüzeteket fejleszt, futtat és oszt meg a telepítés nélkül. Az ingyenes szolgáltatási csomag legfeljebb 4 GB memóriával és 1 GB adattal rendelkezik. 

Az összes korlát felszabadításához csatolhat jegyzetfüzet-projektet egy DSVM vagy bármely más, a Jupyter-kiszolgálón futó virtuális géphez. Ha Azure Active Directory (például vállalati) fiókkal jelentkezik be a Azure Notebooksba, a jegyzetfüzetek automatikusan megjeleníti a fiókhoz társított előfizetések Dsvm. A rendelkezésre álló számítási teljesítmény kibontásához [DSVM csatolhat Azure Notebookshoz](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier) .

### <a name="short-term-experimentation-and-evaluation"></a>Rövidtávú kísérletezés és kiértékelés

A DSVM segítségével kiértékelheti vagy megismerheti az új adatelemzési [eszközöket](./tools-included.md), különösen a közzétett [minták és](./dsvm-samples-and-walkthroughs.md)útmutatók egyes részein.


### <a name="deep-learning-with-gpus"></a>Mély tanulás a GPU-k révén

A DSVM a képzési modellek mély tanulási algoritmusokat használhatnak a grafikus feldolgozási egységeken (GPU-k) alapuló hardveren. Az Azure platform virtuálisgép-méretezési képességeinek kihasználásával a DSVM a Felhőbeli GPU-alapú hardverek igény szerinti használatát teszi lehetővé. Ha nagyméretű modelleket szeretne betanítani, váltson GPU-alapú virtuális gépre, vagy ha nagy sebességű számításokra van szüksége, miközben az operációs rendszer lemezét is megtartja. Az N sorozatú GPU-t használó virtuális gépek SKU-DSVM is kiválaszthatja. Vegye figyelembe, hogy az ingyenes Azure-fiókok nem támogatják a GPU-t használó virtuális gépekhez tartozó SKU-ket.

A DSVM Windows-kiadásai előre telepítve vannak a GPU-illesztőprogramok, keretrendszerek és a Deep learning keretrendszerek GPU-verzióival. A Linux-kiadásban a GPU-k mélyreható megismerése engedélyezve van az Ubuntu-Dsvm. 

A DSVM Ubuntu-vagy Windows-kiadásait egy olyan Azure-beli virtuális gépre is üzembe helyezheti, amely nem a GPU-k alapján van. Ebben az esetben az összes mély tanulási keretrendszer vissza fog térni a CPU-módra.

[További információ az elérhető Deep learning-és AI-keretrendszerekről](dsvm-tools-deep-learning-frameworks.md).

<a name="included"></a>
## <a name="whats-included-on-the-dsvm"></a>Mit tartalmaz a DSVM?

Tekintse meg a Windows és a Linux DSVM található eszközök teljes listáját [itt](tools-included.md).

## <a name="next-steps"></a>További lépések

További információ ezekről a cikkekről:

+ Windows:
  + [Windowsos DSVM beállítása](provision-vm.md)
  + [Tíz dolog, amit elvégezhet a Windows DSVM](vm-do-ten-things.md)

+ Linux:
  + [Linux DSVM (Ubuntu) beállítása](dsvm-ubuntu-intro.md)
  + [Adatelemzés Linux-DSVM](linux-dsvm-walkthrough.md)
