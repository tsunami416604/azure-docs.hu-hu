---
title: Az Azure Machine Learning 2017 előzetes verzió – gyakori kérdések |} A Microsoft Docs
description: Ez a cikk gyakori kérdéseket tartalmaz, valamint választ ad az Azure Machine Learning előzetes verziójú funkciók
services: machine-learning
author: serinakaye
ms.author: serinak
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 08/30/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 45cf987d9af7b7dd0e8f05056b49ba56835603e7
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52313941"
---
# <a name="azure-machine-learning-frequently-asked-questions"></a>Az Azure Machine Learning – gyakori kérdések

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 

Egy teljes körűen felügyelt Azure-szolgáltatás, amely lehetővé teszi, hogy hozzon létre, tesztelheti, kezelése és gépi tanulási és AI-modellek üzembe helyezése az Azure Machine Learning. A szolgáltatások és a letölthető alkalmazás kínálnak a kód megközelítés, amely a felhőben, a helyszíni és a peremhálózati adja meg a szerelvény, helyezhet üzembe, kezelhet, és figyelheti a modelleket, a sebesség és a rugalmasságot. Azt is megteheti az Azure Machine Learning Studio kínál a böngésző alapú, vizuális fogd és vidd szerzői környezet, amelyben nincs kódolásra szükséges. 

## <a name="general-product-questions"></a>Általános kérdések

**Milyen más Azure-szolgáltatások szükségesek?**

Az Azure Blob Storage és az Azure Container Registry az Azure Machine Learning segítségével. Emellett szüksége lesz számítási erőforrások, például egy adatelemző virtuális gép vagy HDInsight-fürt üzembe helyezése. Számítást és üzemeltetési szolgáltatást is üzembe helyezéséhez szükségesek a webes szolgáltatások, például [az Azure Container Service](https://docs.microsoft.com/azure/aks).

**Hogyan kapcsolódnak az Azure Machine Learning Microsoft Machine Learning-szolgáltatások az SQL Server 2017-ben?**   

Machine Learning-szolgáltatások az SQL Server 2017-ben az adatbázis munkafolyamatok gépi tanulási feladatok integrálása egy bővíthető, méretezhető platform. Olyan forgatókönyvekben, ahol egy helyszíni megoldás szükséges, ahol az adatmozgás, költséges vagy tarthatatlan például tökéletes választás. Ezzel szemben a felhőalapú vagy hibrid számítási feladatok egy kiválóan alkalmas a az új Azure-szolgáltatásokat. 

**Az Azure Machine Learning milyen kapcsolatban áll a Microsoft a gépi tanulás a Spark?**

MMLSpark biztosít deep learning és a beépített adatelemzési eszközzel az Apache Spark, a hatékonyságot, kiemeléssel Kísérletezési és állapot-az-a legújabb algoritmusokat megkönnyítése érdekében. MMLSpark kínál a Microsoft Cognitive Toolkit és OpenCV Spark Machine Learning-folyamatok integrációja. A képi és szöveges adatok méretezhető prediktív és analitikai modelleket hozhat létre. MMLSpark egy nyílt forráskódú licenc érhető el és felhasználható modelleket és algoritmusokat készletként AML Workbench szerepel. MMLSpark további információért keresse fel a termék dokumentációját. 

**Spark mely verziói támogatottak az új eszközöket és szolgáltatásokat?**

Workbench jelenleg is tartalmaz, és támogatja az MMLSpark verzió 0,8 értéket, amely nagy kompatibilis Apache Spark 2.1-es. Akkor is az MMLSpark 0,8 GPU-kompatibilis Docker-rendszerkép használata Linux rendszerű virtuális gépek egy esetére.

## <a name="experimentation-service"></a>Kísérletezési szolgáltatás

**Mi az az Azure Machine Learning-kísérletezés szolgáltatás?**

A Kísérletezési szolgáltatással hoztam létre egy felügyelt Azure-szolgáltatás, amely a machine learning-kísérletezés a következő szintre. Kísérletek építhetők fel, helyileg vagy a felhőben. Prototípus az asztalra, majd méretezhetőségnek virtuális gépre vagy a Spark-fürtökön. Azure virtuális gépek a legújabb GPU-technológiával lehetővé teszik a gyors és hatékonyan a deep learninget végezhetnek. Szoros integráció Git bővítettük, így könnyedén csatlakoztatható kód nyomon követése, konfiguráláshoz és együttműködés a meglévő munkafolyamatok is. 

**Hogyan kell fizetnem az a Kísérletezési szolgáltatással hoztam létre?**

Az első két felhasználó társítva az Azure Machine Learning-kísérletezés szolgáltatás használata ingyenes. További felhasználók a nyilvános előzetes verzióban aránya 50 USD havi díjat számítunk fel. Díjszabással és a számlázással kapcsolatos további információkért keresse fel a díjszabási oldalát.

**Kell fizetnem kíséreltek hogyan száma alapján?**

Nem, a Kísérletezési szolgáltatás lehetővé teszi tetszőleges számú kísérlet elvégezhető van szüksége, és a díjat csak a felhasználók száma alapján. A kísérletezéshez felhasznált számítási erőforrásokért külön díjat számolunk fel. Azt javasoljuk, több kísérletet is elvégezni megtalálhatja a legjobban illeszkedő a modell a megoldáshoz.   

**Milyen számítási és tárolási erőforrások adott típusú használhatok?**

A Kísérletezési szolgáltatás a kísérleteket helyi gépen (közvetlen vagy Docker-alapú), [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), és [HDInsight](https://azure.microsoft.com/services/hdinsight/). A szolgáltatás is hozzáfér egy [Azure Storage](https://azure.microsoft.com/services/storage/) tárfiókot futtatási kimenetek tárolásához, és kihasználhatják a egy [Visual Studio Team Service](https://azure.microsoft.com/services/visual-studio-team-services/) verziókövetés és a Git-storage-fiók. Vegye figyelembe, hogy akkor kell fizetnie egymástól függetlenül felhasznált számítási és tárolási erőforrásokért azok saját díjszabása alapján.  


## <a name="model-management"></a>Modellkezelés

**Mi az Azure Machine Learning Modellkezelés?**

Az Azure Machine Learning Modellkezelés egy felügyelt Azure-szolgáltatás, amely lehetővé teszi az adatok adatszakértők és a fejlesztő-operátor csapatok számára a különböző környezetekben történő megbízhatóan helyezhet üzembe prediktív modelleket. Git-tárházak és a Docker-tárolókat biztosít a ismételhetőség követheti nyomon és igazolhatja. Modellek megbízhatóan a felhőben, helyszíni vagy peremhálózati is telepíthető. Egyszer éles környezetben is kezelheti a modell teljesítményét, majd proaktív módon újratanítása, ha csökken a teljesítmény. Modellek helyi gépen való üzembe helyezése [Azure virtuális gépek](https://azure.microsoft.com/services/virtual-machines/), a Spark futtatása [HDInsight](https://azure.microsoft.com/services/hdinsight/) vagy Kubernetes-vezényelt [az Azure Container Service](https://azure.microsoft.com/services/container-service/) fürtök.  

**Mi az "modell"?**

A modell le egy Kísérletezési futnak, amelyek kimenete a modellkezeléshez lett előléptetve. Egy olyan modell, a üzemeltetési fiókban regisztrált beleszámít a csomag, többek között a modellek átképezési vagy verzióját az iteráció frissíteni.

**Mit jelent a "kezelt modell?"**

A modell a betanítási folyamat kimenete, ami a gépi tanulási algoritmus betanítási adatokon való alkalmazását jelenti. Modellkezelési lehetővé teszi a modellek webszolgáltatásként üzembe helyezése, a modellek különféle verzióit kezelheti, és azok teljesítményére és metrikák figyelése. Modellek "Felügyelt" már regisztrálva vannak az Azure Machine Learning Modellkezelés-fiókot. Vizsgáljunk meg egy olyan példát, amelyben értékesítés-előrejelzést szeretne elvégezni. A Kísérletezési fázisban különféle adatkészletek és algoritmusok használatával létrehozza a számos modellt. Négy modell a különböző pontosságú hozta létre, de csak a modell a legnagyobb pontosságú regisztrálni. A modell, amely regisztrálva lesz az első kezelt modell.
 
**Mit jelent a "központi telepítés?"**

Modellkezelési modellek üzembe helyezése az Azure-beli tárolókba csomagolt webszolgáltatásként teszi lehetővé. Ezekhez a webszolgáltatásokhoz REST API-k használatával lehet meghívni. Minden webszolgáltatás egy egyetlen központi telepítési számít, és aktív üzemelő példányok száma vannak a szolgáltatáscsomag kvótáját használja. Az értékesítés-előrejelzési példát, ha telepít a legjobban teljesítő modellt használ, a terv értéke akkor növekszik, egy központi telepítési. Újratanítás és üzembe helyezése egy másik verziója, akkor már két üzemelő példánya. Ha azt állapítja meg, amely az újabb modell jobban, és törölje az eredeti, az üzemelő példányok száma eggyel csökken.  

**Az egyes számítási erőforrások üzemelő példányok érhetők el?** 

Modellkezelési futtathatja az üzemelő példányok Docker-tárolókat a regisztrált [az Azure Container Service](https://azure.microsoft.com/services/container-service/), mint [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), vagy a helyi gépen. További üzembe helyezési célok hamarosan bekerül. Vegye figyelembe, hogy meg kell fizetnie egymástól függetlenül minden felhasznált számítási erőforrásokat, azok saját díjszabása alapján.     

**Az Azure Machine Learning Modellkezelés használatával helyezhet üzembe modelleket modellkezeléssel nem a Kísérletezési szolgáltatással hoztam létre?**

A legoptimálisabb modellt a Kísérletezési szolgáltatással létrehozott központi telepítésekor kap. Telepíthet azonban modellek más keretrendszerek és eszközök használatával. Számos modellt, beleértve a MMLSpark, TensorFlow, a Microsoft Cognitive Toolkit, scikit támogatjuk – ismerje meg, Keras stb. 

**Használhatja a saját Azure-erőforrások?**

Igen, a Kísérletezési szolgáltatással hoztam létre, és a Modellkezelési működjenek együtt több Azure-beli adat-tároló, a compute számítási feladatok és más szolgáltatásokhoz. Tekintse meg a műszaki dokumentáció további tájékoztatást talál a szükséges Azure-szolgáltatásokat.

**Akkor támogatja mind a helyszíni és felhőalapú üzembe helyezési forgatókönyveket?**

Igen. Azt támogatja a helyszíni és felhőalapú üzembe helyezési forgatókönyveket Docker-tárolók használatával. Helyi végrehajtási célok a következők: egycsomópontos Docker központi telepítések, [Microsoft SQL Server ML szolgáltatásokkal](https://docs.microsoft.com/sql/advanced-analytics/r/r-services), vagy a Hadoop, Spark. Felhő is támogatja, a Docker üzembe helyezések többek között: fürtözött központi telepítések keresztül az Azure Container Service és a Kubernetes, a HDInsight vagy a Spark-fürtök. Docker-tárolók és az Azure IOT Edge segítségével peremhálózati forgatókönyvek támogatása. 

**Futtathatók Docker-rendszerkép használata az Azure Machine Learning parancssori egy másik gazdagépen létrehozott?**

Igen. Minden olyan docker-gazdagép webszolgáltatásként a rendszerképét is használhatják, amennyiben a gazdagép rendelkezik a docker-rendszerképet üzemeltető elegendő számítási erőforrásait.

**Támogatja az üzembe helyezett modellnél átképezési?**

Igen, ugyanannak a modellnek több verzióját is telepítheti. Modellkezelési szolgáltatásfrissítések támogatása az összes frissített modelleket és képek.

## <a name="workbench"></a>Workbench

**Mi az az Azure Machine Learning Workbench?**

Az Azure Machine Learning Workbench egy kiegészítő alkalmazást a hivatásos adatszakértők számára készült. Érhető el a Windows és Mac rendszerű, a Machine Learning Workbench biztosít áttekintése, kezelését és ellenőrzését gépi tanulási megoldások. A Machine Learning Workbench biztosít hozzáférést a Microsoft és a nyílt forráskódú fejlesztői Közösség is élvonalbeli mesterséges Intelligencia keretrendszerekre. A legnépszerűbb data science eszközkészletek, többek között a TensorFlow, a Microsoft Cognitive Toolkit, a Spark ML, a scikit bővítettük – ismerje meg, stb. Emellett tettük, például a Visual Studio Code-ot, a Jupyter notebookok és a PyCharm közkedvelt adatelemzési ide-integráció. A Machine Learning Workbench gyorsan minta megértésében, valamint az adatok előkészítéséhez, strukturált vagy strukturálatlan beépített előkészítési képességekkel rendelkezik. Az új adat-előkészítési eszközével, nevű [PROSE](https://microsoft.github.io/prose/), a Microsoft Research a legmodernebb technológiai épül.  

**A Workbench az integrált fejlesztői Környezetig?**

Nem. A Machine Learning Workbench úgy tervezték, mint például a Jupyter Notebooks, a Visual Studio Code és a PyCharm népszerű Ide-k kiegészítése, de nem egy teljes funkcionalitású integrált fejlesztői környezet. A Machine Learning Workbench kínál néhány alapszintű szöveges képességek szerkesztését, de a hibakeresés, az intellisense és más gyakran használt IDE-funkciók nem támogatottak. Azt javasoljuk, hogy Ön kedvenc Fejlesztőkörnyezetét használhatja kód fejlesztéséhez, Szerkesztés és hibakeresés. Próbálja meg is Kezdésként [Visual Studio Code Tools for AI](https://www.visualstudio.com/downloads/ai-tools-vscode).

**Van-e az Azure Machine Learning Workbench használatával díját?**

Nem. Az Azure Machine Learning Workbench ingyenes alkalmazás. amely korlátlan számú gépre letölthető, és korlátlan számú felhasználó használhatja. Az Azure Machine Learning Workbench csak kísérletezési fiókkal használható. .  

**Parancssori képességeket támogatja?**

Igen, az Azure Machine Learning egy teljes CLI felületet kínál. A Machine Learning parancssori felület az Azure Machine Learning Workbench alapértelmezés szerint telepítve van. A Linux Data Science virtuális gép az Azure-ban részeként is biztosított, és a integráljuk a [Azure CLI-vel](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)


**Használhatom-e a Jupyter Notebooks Workbench?**

Igen! Jupyter notebookok Workbench, a üzemeltetési ügyfélalkalmazás, a Workbench alkalmazásban is futtatható, ugyanúgy történik, mint a böngésző-ügyfélként. 

**Melyik Jupyter notebookkernelek támogatottak?**

Jupyter mellékelt a Workbench jelenlegi verziója elindítja a Python 3 kernel, és a egy további kernel a aml_config mappában található "runconfig" fájl esetében. Támogatott konfigurációk a következők:
- Helyi Python
- A helyi vagy távoli Docker Python

## <a name="data-formats-and-capabilities"></a>Adat-formátumok és -képességek

**Melyik a fájlformátumokat jelenleg támogatja a Workbench adatbetöltés?**

A Workbench adatelőkészítési eszközök jelenleg a következő formátumok Adatbetöltési támogatja: 
- Tagolt fájlok, például a CSV, TSV, stb.  
- Rögzített szélességű fájlok
- Egyszerű szöveges fájlok
- Excel (.xls/xlsx)
- JSON-fájlok
- Parquet-fájlokat 
- Egyéni fájlok (szkriptek), ha a megoldáshoz szükséges további források adatbetöltést, a Python-kód segítségével... 

**Adatok tárolási helyeinek jelenleg támogatottak?**

Nyilvános előzetes verzió a Workbench adatbetöltést támogatja: 
- Helyi merevlemez-meghajtóról vagy a csatlakoztatott hálózati tárolási helye
- Az Azure BLOB vagy az Azure Storage (Azure-előfizetés szükséges)
- Azure SQL Server-kiszolgáló
- Microsoft SQL Server


**Milyen típusú adatok konvertálása, előkészítési és átalakítások érhetők el?**

A nyilvános előzetes verzióként a Workbench "Származtatott oszlop szerint példaként", "felosztási oszlop szerint például" "Szöveg csoportosulása", "Hiányzó értékek kezelni" és sok más támogatja.  Workbench is támogatja az adatok típusának átalakítása adatösszesítés (száma, átlag, eltérés, stb.) és az összetett illesztések. Támogatott képességek teljes listáját keresse fel a termék dokumentációját. 

**Vannak-e bármilyen adatok méretkorlátozások kényszeríti az Azure Machine Learning Workbench, kísérletezés és Modellkezelés?**

Nem, az új szolgáltatások nem írnak adatokat korlátozások. Vannak azonban a környezet, amelyben hajtja végre az adat-előkészítés, a modell betanítása, a Kísérletezési vagy a központi telepítés által korlátozásokat. Például egy helyi környezetben, a betanításhoz céloz meg, ha korlátozott szabad hely a merevlemezen. Azt is megteheti HDInsight céloz meg, ha Ön bármilyen kapcsolódó mérete korlátozva van vagy számítási korlátozások. 

## <a name="algorithms-and-libraries"></a>Algoritmusok és könyvtárak

**Milyen algoritmusokat támogatja az Azure Machine Learning Workbenchben?**

Előzetes verzió termékei és szolgáltatásai közé tartozik a nyílt forráskódú fejlesztői Közösség előnyeit. Támogatjuk a számos különféle algoritmusok és a kódtárakat, beleértve a TensorFlow, a scikit-ismerje meg, az Apache Spark és a Microsoft Cognitive Toolkit. Az Azure Machine Learning Workbench is csomagok a [Microsoft revoscalepy](https://docs.microsoft.com/sql/advanced-analytics/python/what-is-revoscalepy) csomagot.

**Hogyan kapcsolódnak az Azure Machine Learning a Microsoft Cognitive Toolkit?**

A [Microsoft Cognitive Toolkit](https://docs.microsoft.com/cognitive-toolkit/) egyike a számos keretrendszer, az új eszközök és szolgáltatások által támogatott. A Cognitive Toolkit egy egységesített mélytanulási eszközkészlet, amely lehetővé teszi, hogy használják, és kombinálhatja közkedvelt machine learning-modellek, például Feed-Forward Neurális hálózatokat, Konvolúciós hálózatokat, a feladatütemezés-sorozat, és ismétlődő hálózatokat. A Microsoft Cognitive Toolkit további információért látogasson el a [termékdokumentáció](https://docs.microsoft.com/cognitive-toolkit/). 