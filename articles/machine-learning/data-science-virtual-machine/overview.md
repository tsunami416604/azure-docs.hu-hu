---
title: Mi az Azure Data Science virtuális gép
titleSuffix: Azure Data Science Virtual Machine
description: Alapvető elemzési helyzetek és összetevők Windows és Linux rendszerű adatelemző virtuális gépekhez.
keywords: adatelemzési eszközök, adatelemző virtuális gép, eszközök adatelemzéshez, linux adatelemzés
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 12/31/2019
ms.openlocfilehash: a5fbcc1eef8717fdb1aa7f914c3e0ba6594fc27a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281799"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>Mi az Azure Data Science virtuális gép Linux és Windows?

Az adatelemzési virtuális gép (DSVM) egy testre szabott virtuális gép az Azure felhőplatformon kifejezetten adatelemzési célokra készült. Számos népszerű adatelemzési eszközzel rendelkezik, amelyek előre telepítve vannak, és előre konfigurálva vannak, hogy intelligens alkalmazásokat építsenek a fejlett elemzésekhez. 

A DSVM a következő oldalon érhető el:
+ **Windows Server 2019**
+ **Ubuntu 18.04 LTS**
+ Windows Server 2016
+ Ubuntu 16.04 LTS és CentOS 7.4


> [!NOTE]
> A deep learning minden virtuálisgép-eszköze be van hajtva az adatelemzési virtuális gépbe. 


## <a name="why-choose-the-dsvm"></a>Miért válassza a DSVM-et?
Az adatelemzési virtuális gép célja, hogy az adatszakemberek minden képzettségi szint és az iparágak között egy súrlódásmentes, előre konfigurált adatelemzési környezetben. Ahelyett, hogy egy hasonló munkaterületet a saját, kiépítheti a DSVM. Ezzel a választással napokat vagy akár _heteket_ takaríthat meg a telepítési, konfigurációs és csomagkezelési folyamatok során. A DSVM üzembe helyezése után azonnal munkához láthat adatelemzési projektjén.

## <a name="sample-use-cases"></a>Használati esetek mintája

Az alábbiakban bemutatjuk néhány gyakori használati esetek DSVM ügyfelek számára.

### <a name="moving-data-science-workloads-to-the-cloud"></a>Adatelemzési számítási feladatok áthelyezése a felhőbe

A DSVM alapkonfigurációt biztosít az adatelemzési csoportok számára, amelyek le szeretnék cserélni a helyi asztalukat egy felügyelt felhőalapú asztalra, biztosítva, hogy a csapat összes adattudósa egységes beállítással rendelkezzen, amellyel ellenőrizheti a kísérleteket és elő szeretné mozdítani az együttműködést. Csökkenti a költségeket is azáltal, hogy csökkenti a rendszergazdai terheket. Ez a tehercsökkentés időt takarít meg a speciális elemzésekhez szükséges szoftvercsomagok kiértékeléséhez, telepítéséhez és karbantartásához.

### <a name="data-science-training-and-education"></a>Adatelemzési képzés és oktatás
Vállalati oktatók és oktatók, akik tanítanak adatelemzési osztályok általában egy virtuális gép imázsát. A kép biztosítja, hogy a diákok egységes beállítással rendelkezzenek, és hogy a minták kiszámíthatóan működjenek. 

A DSVM igény szerinti környezetet hoz létre, amely egységes beállítással könnyíti meg a támogatási és inkompatibilitási kihívásokat. Olyan esetekben, amikor gyakran kell környezetet kiépíteni, különösen a rövidebb kurzusokhoz, ez jelentős előnnyel jár.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Igény szerinti rugalmas kapacitás nagyszabású projektekhez
Adattudomány hackathons/versenyek vagy nagyszabású adatmodellezés és feltárási igényel kibővített hardverkapacitás, általában rövid időtartamra. A DSVM segítségével replikálhatja az adatelemzési környezet gyorsan igény szerint, a kibővített kiszolgálók, amelyek lehetővé teszik a kísérleteket, hogy a nagy teljesítményű számítástechnikai erőforrások futtatható.

### <a name="custom-compute-power-for-azure-notebooks"></a>Egyéni számítási teljesítmény az Azure-jegyzetfüzetekhez
[Az Azure Notebooks](../../notebooks/azure-notebooks-overview.md) egy ingyenes üzemeltetett szolgáltatás a Jupyter-jegyzetfüzetek fejlesztéséhez, futtatásához és megosztásához a felhőben telepítés nélkül. Az ingyenes szolgáltatási szint 4 GB memóriára és 1 GB adatra korlátozódik. 

Az összes korlát felszabadításához csatolhat egy jegyzetfüzet-projektet egy DSVM-hez vagy bármely más, Jupyter-kiszolgálón futó virtuális géphez. Ha az Azure Active Directory használatával (például vállalati fiókkal) fiókkal jelentkezik be az Azure Notebooks ba, a notebookok automatikusan megjelenítik a DSVM-eket az adott fiókhoz társított előfizetésekben. [DSVM-et csatlakoztathat az Azure Notebooks-hoz](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier) a rendelkezésre álló számítási teljesítmény bővítéséhez.

### <a name="short-term-experimentation-and-evaluation"></a>Rövidtávú kísérletezés és kiértékelés
A DSVM segítségével új adatelemzési [eszközöket](./tools-included.md)értékelhet vagy tanulhat, különösen a közzétett [minták és forgatókönyvek némelyikének átnézésével.](./dsvm-samples-and-walkthroughs.md)


### <a name="deep-learning-with-gpus"></a>Mély tanulás GPU-kkal
A DSVM-ben a betanítási modellek mélytanulási algoritmusokat használhatnak a grafikus feldolgozó egységeken (GPU-k) alapuló hardveren. Az Azure platform virtuális gépméretezési képességeinek kihasználásával a DSVM segít a GPU-alapú hardver ek használatával a felhőben az igényeinek megfelelően. A GPU-alapú virtuális gépre válthat nagy modellek betanításakor, vagy ha nagy sebességű számításokra van szüksége, miközben ugyanazt az operációsrendszert tartja. Bármelyik N sorozatú GPU-kompatibilis virtuálisgép-sus-t választhatd DSVM-mel. Kérjük, vegye figyelembe, hogy az ingyenes Azure-fiókok nem támogatják a GPU-kompatibilis virtuálisgép-skus-okat.

A DSVM Windows Server 2016 kiadása előre telepítve van a mélytanulási keretrendszerek GPU-illesztőprogramjaival, keretrendszerekkel és GPU-verzióival. A Linux kiadásban a GPU-k mély tanulása mind a CentOS, mind az Ubuntu DSVM-eken engedélyezve van. 

A DSVM Ubuntu, CentOS vagy Windows 2016 kiadását is telepítheti egy olyan Azure-beli virtuális gépre, amely nem GPU-kon alapul. Ebben az esetben az összes deep learning keretrendszerek esnek vissza a CPU módban.
 
[További információ az elérhető mélytanulásról és a a mi keretrendszerekről.](dsvm-tools-deep-learning-frameworks.md)

<a name="included"></a>

## <a name="whats-included-on-the-dsvm"></a>Mit tartalmaz a DSVM?

Lásd a teljes listát az eszközök mind a Windows és a Linux DSVM [itt](tools-included.md).

## <a name="next-steps"></a>További lépések

További információ az alábbi cikkekkel:

+ Windows:
  + [Windowsos DSVM beállítása](provision-vm.md)
  + [Tíz dolog, amit elérhet a Windows DSVM-en](vm-do-ten-things.md)

+ Linux:
  + [LinuxOs DSVM (Ubuntu) beállítása](dsvm-ubuntu-intro.md)
  + [Linuxos DSVM (CentOS) beállítása](linux-dsvm-intro.md)
  + [Adattudomány Linux DSVM-en](linux-dsvm-walkthrough.md)
