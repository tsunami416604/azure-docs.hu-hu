---
title: "Azure Machine Learning 2017 előzetes verzió – gyakori kérdések |} Microsoft Docs"
description: "Ez a cikk gyakran feltett kérdéseket tartalmaz, és megválaszolja az Azure Machine Learning előzetes verziójú funkciók"
services: machine-learning
author: serinakaye
ms.author: serinak
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 08/30/2017
ms.openlocfilehash: 33583fb3d9bca94592c05c4c39c802e673a2ea8a
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2017
---
# <a name="azure-machine-learning-frequently-asked-questions"></a>Gyakori kérdések az Azure Machine Learning

## <a name="general-product-questions"></a>Általános kérdések

**Mi az Azure Machine Learning?**

Az Azure Machine Learning egy teljes körűen felügyelt Azure-szolgáltatás lehetővé teszi, hogy hozzon létre, tesztelheti, kezelése és központi telepítése a gépi tanulási és AI modellek. Az új szolgáltatásokat és letölthető alkalmazás egy kódot-első megközelítés, amely kihasználja a felhő, a helyszíni és az Edge böngésző számára adja meg a vonat, telepíthet, kezelheti az ajánlatot, és figyelje a modellek tápellátáshoz, a sebesség és a rugalmasságot. Azt is megteheti az Azure Machine Learning Studio kínál egy webböngésző-alapú, visual fogd és vidd fejlesztői környezet nem kódolás esetén szükséges. 

**Hogyan kezdjem el az előzetes?**

A kezdéshez legkönnyebben a kísérleti szolgáltatás az Azure-előfizetés. A portálról is szabad munkaterület alkalmazás letöltése vagy egy modell felügyeleti fiók. Emellett letöltheti a dokumentációs oldalát útmutatókat, videók, bemutató kódot és több. 

**Nincs Azure-előfizetéssel. Lehet próbálni a új szolgáltatások?**

A Microsoft Azure portfóliót részeként az új szolgáltatások az Azure-előfizetés szükséges. Emellett erőforrásokat, például az erőforráscsoportok, virtuális gépek stb létrehozásához megfelelő engedélyekkel kell rendelkeznie. 

**Ahol régiók a következők a új szolgáltatások érhetők el?**

Támogatott Azure-régiók kísérletezhet, és a modell a kezelési szolgáltatás az Azure Machine Learning keresse meg a [Azure termékek régiónként](https://azure.microsoft.com/regions/services/) lap.

További régiókban, azt a termék fejlesztésére lesz hozzáadva.  A telepítendő régiók sorrendjének segíthet a [Azure Machine Learning visszajelzés](https://feedback.azure.com/forums/257792-machine-learning) hely. 

**Milyen más Azure-szolgáltatások szükségesek?**

Az Azure Machine Learning összes képességeket teljesen tapasztal, Azure Machine Learnign Visual Studio VSTS fiókot, Azure Blob Storage, Azure tároló beállításjegyzék, adatok tudományos VM vagy a HDInsight számítási és az Azure Tárolószolgáltatás fogja használni.

**Hogyan kapcsolódnak Azure Machine Learning Microsoft Machine Learning szolgáltatásokat az SQL Server 2017?**   

Az SQL Server 2017 Machine Learning szolgáltatások egy bővíthető, méretezhető platform gépi tanulási feladatok integrálása adatbázis munkafolyamatok. A forgatókönyvekben, ahol szükséges, ha az adatmozgatás a költséges vagy tarthatatlan például egy helyszíni megoldás tökéletes legyen. Ezzel szemben felhőalapú vagy hibrid munkaterhelések egy nagyszerű beválik, ha az új Azure-szolgáltatásokhoz. 

**Támogatják a Python és a R? Mi a helyzet más programozási nyelvek, például C++**

Sajnos jelenleg támogatott Python csak. Jelenleg az R-integráció dolgozik, és várt számához hamarosan elérhetővé. 

**Hogyan Azure Machine Learning kapcsolódik a Microsoft Machine Learning a Spark?**

A: MMLSpark biztosít a részletes tanulási, és a hatékonyságot, kiemeléssel Apache Spark adatok tudományos eszközök megkönnyítik a kísérleti és -az-a-legkorszerűbb algoritmusokat. MMLSpark kínál a Microsoft kognitív eszközkészlet és OpenCV Spark gépi tanulási folyamatok integrálását. A képnek és szövegnek adatok hatékony, magas szinten méretezhető prediktív és elemzési modellek hozhat létre. MMLSpark egy nyílt forráskódú licenc érhető el, és AML munkaterület fogyasztható modellek és algoritmusok halmazát tartalmazza. MMLSpark további információkért látogasson el a termék dokumentációját. 

**Az új eszközök és szolgáltatások által támogatott Spark mely verzióival? Felső rész**

Munkaterület jelenleg tartalmazza, és támogatja a MMLSpark verzió 0,8 értéket, amely összeegyeztethető Apache Spark 2.1. Akkor is szeretné MMLSpark 0,8 GPU-kompatibilis Docker képe Linux virtuális gépeken.

## <a name="experimentation-service"></a>Kísérletezhet szolgáltatás

**Mi az az Azure Machine Learning kísérletezhet szolgáltatás?**

A kísérleti szolgáltatás nem felügyelt Azure szolgáltatás, amely a következő szintre a machine learning kísérletezhet. Kísérletek építhetők helyileg vagy a felhőben. Egy asztalon prototípus gyorsan majd méretezni virtuális gépek vagy a Spark-fürtök. A legújabb GPU technology Azure virtuális gépek lehetővé teszik a részletes tanulási gyors és hatékony végezhetnek. Azt is szerepel a Git szoros integrációja, könnyen csatlakoztatható a nyomkövetési kód, beállítást és együttműködés meglévő munkafolyamatok. 

**Hogyan tudom megterheljük a kísérleti szolgáltatás?**

Az első két felhasználók az Azure Machine Learning kísérletezhet szolgáltatással kapcsolatos szabadon. További felhasználók $50/hónap Public Preview arányát kell fizetnie. További információ az árak és számlázás látogasson el az árazás oldalra.

**I megterheljük futtattam hány kísérletek alapján?**

Nem, a kísérletezhet szolgáltatás lehetővé teszi, hogy annyi kísérletek kell és díjak csak a felhasználók száma alapján. A kísérletezéshez felhasznált számítási erőforrásokért külön díjat számolunk fel. Javasoljuk, hogy hajtsa végre több kísérletek, így megtalálja a legjobban illeszkedő a modell a megoldás.   

**Milyen számítási és tárolási erőforrásokat meghatározott típusú használható?**

A kísérleti szolgáltatás hajthat végre a kísérletek (közvetlen vagy Docker-alapú), a helyi gép [Azure virtuális gépek](https://azure.microsoft.com/services/virtual-machines/), és [HDInsight](https://azure.microsoft.com/services/hdinsight/). Is hozzáfér a szolgáltatás egy [Azure Storage](https://azure.microsoft.com/services/storage/) tárolása a végrehajtás kimenetének fiókot, és kihasználhatják a [Visual Studio Team Service](https://azure.microsoft.com/services/visual-studio-team-services/) a verzió-vezérléshez és a Git tárolási fiók. Vegye figyelembe, hogy számlázott egymástól függetlenül a bármely felhasznált számítási és tárolási erőforrásokat, az egyes árképzési alapján.  


## <a name="model-management"></a>Modellkezelés

**Mi az Azure Machine Learning modell kezelése?**

Azure Machine Learning modell felügyeleti egy felügyelt Azure szolgáltatás, amely lehetővé teszi az adatok kutatók és fejlesztői-ops csapat üzembe prediktív modelleket megbízhatóan történő széles körének a környezetben. Git tárházak találhatók, és a Docker tárolók nyomon és ismételhetőség biztosítanak. Modellek megbízhatóan a felhőben, a helyszíni vagy a peremhálózati is telepíthető. Amennyiben az éles, kezelheti a modell teljesítmény, majd proaktív újratanítása ha csökken a teljesítmény. Telepítheti a helyi gépen modellek [Azure virtuális gépek](https://azure.microsoft.com/services/virtual-machines/), a Spark [HDInsight](https://azure.microsoft.com/services/hdinsight/) vagy Kubernetes vezénylését [Azure Tárolószolgáltatás](https://azure.microsoft.com/services/container-service/) fürtök.  

**Mi az a "model"?**

Egy modell egy kísérleti futnak, amelyek kimenete modell Management lett előléptetve. Egy modellt a biztosító fiókkal regisztrált alapján a terv, beleértve a frissített átképezési vagy a verziójával iterációs modellek számít.

**Mi az, hogy a "felügyelt modell?"**

A modell a betanítási folyamat kimenete, ami a gépi tanulási algoritmus betanítási adatokon való alkalmazását jelenti. Felügyeleti modell lehetővé teszi modellek webszolgáltatásként telepítése, a modellek különböző verzióinak kezelése és a Teljesítményfigyelő és metrikákat. Az Azure Machine Learning modell felügyeleti fiók "Kezelt" modellek vannak regisztrálva. Vizsgáljunk meg egy olyan példát, amelyben értékesítés-előrejelzést szeretne elvégezni. A kísérleti fázisban sok modellek különböző adatkészletek vagy algoritmusok készítése. Négy különböző pontosság modellek hozta létre, de csak a modell regisztrálása a legmagasabb pontossága választani. A modell, amely regisztrálva van-e az első felügyelt modellje válik.
 
**Mi az a "telepítés?"**

Felügyeleti modell lehetővé teszi modellek telepítését az Azure-ban csomagolt web service tárolóként. Ezek a webszolgáltatások REST API-k használatával is elindítható. Minden webszolgáltatáshoz egy központi telepítésnél számít, és aktív üzemelő példányok száma a terv felé bájtjai számítanak. Az értékesítési előrejelzés példában a legjobb teljesítményt a modell központi telepítésekor használ, a terv értéke eggyel növekszik, egy központi telepítésnél. Ha újratanítása és központi telepítése egy másik verziója, akkor két üzemelő példány. Ha azt állapítja meg, amely az újabb modell célszerűbb, és törölje az eredeti, a központi telepítési száma eggyel csökken.  

**A központi telepítések adott számítási erőforrásokat érhetők el?** 

Modell kezelése futtathatja a központi telepítések, Docker-tároló regisztrálva [Azure Tárolószolgáltatás](https://azure.microsoft.com/services/container-service/), mint a [Azure virtuális gépek](https://azure.microsoft.com/services/virtual-machines/), vagy a helyi gépen. További telepítési célok hamarosan lesz hozzáadva. Vegye figyelembe, hogy számlázott egymástól függetlenül bármilyen felhasznált számítási erőforrások, az egyes árképzési alapján.     

**Az Azure Machine Learning modell Management használatával telepíti a kísérleti szolgáltatás eltérő eszközök használatával készített modellek?**

A legjobb élményt kap a kísérleti szolgáltatással létrehozott modelleket központi telepítésekor. Telepíthet azonban más keretrendszerek és eszközök használatával készített modellek. Támogatjuk például scikit MMLSpark, TensorFlow, Microsoft kognitív eszközkészlet modellek számos – ismerje meg, Keras stb. 

**Használhatja a saját Azure-erőforrások?**

Igen, a kísérleti szolgáltatás és a modell kezelése több Azure adattárolókhoz együtt használhatók, a számítási számítási feladatok és az egyéb szolgáltatásokat. Tekintse meg a műszaki dokumentációk további tájékoztatást talál a szükséges Azure-szolgáltatásokhoz.

**Akkor támogatja a helyszíni és felhőszolgáltatásban telepítési forgatókönyvek?**

Igen. Azt támogatja a helyszíni és felhőalapú környezetek keresztül Docker-tároló. Helyi végrehajtási célok a következők: egycsomópontos Docker telepítések esetén [Microsoft SQL Server ML szolgáltatásokkal](https://docs.microsoft.com/sql/advanced-analytics/r/r-services), Hadoop vagy Spark. Felhő is támogatja központi telepítések keresztül Docker, többek között: fürtözött központi telepítések Azure Tárolószolgáltatás és Kubernetes, HDInsight vagy Spark-fürtök keresztül. Peremhálózati forgatókönyvek Docker-tárolók és Azure IOT peremhálózati keresztül támogatottak. 

**Futtathatja a Docker-lemezkép létrejött az Azure Machine Learning parancssori felület használatával egy másik gazdagépen?**

Igen. Használhatja a lemezképet a docker gazdagépen webszolgáltatásként, amíg a gazdagép rendelkezik-e a docker lemezkép tárolásához elegendő számítási erőforrásokat.

**Támogatják a telepített modellek átképezési?**

Igen, ugyanannak a modellnek több verziót is telepíthet. Modell kezelése szolgáltatásfrissítések támogatása az összes frissített modellek és a képeket.

## <a name="workbench"></a>Workbench

**Mi az az Azure Machine Learning-munkaterület?**

Az Azure Machine Learning-munkaterület egy kiegészítő alkalmazás szakmai adatszakértőkön készült. Érhető el a Windows és Mac, a Machine Learning munkaterület áttekintése, és biztosít a felügyeleti vezérlő a machine learning megoldások. A Machine Learning-munkaterület élvonalbeli AI keretrendszerek hozzáférés Microsoft-és a nyílt forráskódú közösségi tartalmaz. Azt a legnépszerűbb adatok tudományos eszközök gazdag, beleértve a TensorFlow, Microsoft kognitív eszközkészlet, Spark ML, scikit szerepel – ismerje meg, és több. Integráció a népszerű adattudomány IDEs például Jupyter notebookok, PyCharm és a Visual Studio Code is engedélyezése befejeződött. A Machine Learning-munkaterület képes beépített előkészítése gyorsan mintát, ismerje meg, és készítse elő az adatokat, hogy a strukturált vagy strukturálatlan. Az új adatok előkészítő eszköze, [PROSE](https://microsoft.github.io/prose/), a Microsoft Research legmodernebb technológiára épül.  

**Munkaterület van egy IDE?**

Nem. A Machine Learning-munkaterület úgy tervezték, mint például a Jupyter notebookok, Visual Studio Code és PyCharm népszerű IDEs való egy kiegészítő, de nincs teljesen működőképes IDE. A Machine Learning-munkaterület kínál néhány egyszerű szöveg szerkesztési képességeket, de a hibakeresést, intellisense és egyéb általánosan használt IDE képességek nem támogatottak. Azt javasoljuk, hogy használ a kedvenc IDE kód fejlesztési és hibakeresést. 

**Az Azure Machine Learning-munkaterület használatával díjat van?**

Nem. Az Azure Machine Learning-munkaterület egy ingyenes alkalmazást. amely korlátlan számú gépre letölthető, és korlátlan számú felhasználó használhatja. Az Azure Machine Learning Workbench csak kísérletezési fiókkal használható. .  

**Támogatott parancssori képességeket?**

A: az Azure Machine Learning Igen, a teljes CLI-felületet biztosít. A Machine Learning CLI az Azure Machine Learning-munkaterület alapértelmezés szerint telepítve van. A rendszer a Adattudomány Linux virtuális gépet az Azure részeként is biztosít, és a lesz integrálni a [Azure parancssori felület](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest)


**Használhatok Jupyter notebookok munkaterület?**

Igen! Munkaterület, az ügyfél üzemeltetési alkalmazásként munkaterület a Jupyter notebookok futtatható, ugyanúgy történik, mint a böngésző ügyfélként. 

**Mely Jupyter Notebook kernelek támogatottak?**

A: munkaterület mellékelt Jupyter a jelenlegi verziója elindítja egy Python 3 kernel, és egy kiegészítő rendszermag a aml_config mappában lévő minden egyes "runconfig" fájlhoz. Támogatott konfigurációk a következők:
- Helyi Python
- A helyi vagy távoli Docker Python

## <a name="data-formats-and-capabilities"></a>Adatok formátumokat és képességek

**Mely fájlformátumok adatfeldolgozást munkaterület jelenleg támogatottak?**

Az adatok előkészítése eszközök munkaterület jelenleg a következő formátumok adatfeldolgozást támogatja: 
- Tagolt fájlok például a fürt megosztott kötetei szolgáltatás, TSV, stb.  
- A rögzített szélességű fájlok
- Egyszerű szöveges fájlokról
- Excel (.xls/xlsx)
- JSON-fájlok
- Parquet fájlok 
- (Parancsfájlok), ha a megoldás igényel adatfeldolgozást további forrásból származó fájlok egyéni Python kód segítségével... 

**Milyen adatok tárolási helye jelenleg támogatott?**

A nyilvános előzetes verziójához a munkaterület az adatfeldolgozást támogatja: 
- Helyi merevlemez-meghajtóról vagy a csatlakoztatott hálózati tárolási helye
- Az Azure BLOB vagy Azure Storage (az Azure-előfizetés szükséges)
- Azure SQL Server-kiszolgáló
- Microsoft SQL Server


**Milyen típusú adatok wrangling, előkészítési és átalakítások állnak rendelkezésre?**

A nyilvános előzetes verziójához a munkaterület "Származnia oszlop szerint példa", "vegyes oszlop által például" "Szöveget Fürtszolgáltatás", "Hiányzó értékek kezelni" és sok más támogatja.  Munkaterület az adattípus konvertálása adatösszesítés (DARABSZÁM, KÖZÉPÉRTÉK, VARIANCIA, stb.) és összetett adatokat illesztések is támogatja. A támogatott képességek teljes listáját keresse fel a termék dokumentációját. 

**Vannak-e bármilyen adatok mérete korlátozások Azure Machine Learning-munkaterület, kísérletezhet vagy modell kezelése?**

A: az új szolgáltatások nem ugyanazok az adatok korlátozások. Vannak azonban a környezet, amelyben hajtja végre az adatok előkészítése, a modell betanítási, a kísérletezés vagy a központi telepítés által bevezetett korlátozások. Például egy helyi környezet képzési céloz meg, ha korlátozott szabad hely a merevlemezen. Azt is megteheti Ha a HDInsight céloz meg, bármely társított mérete korlátozza vagy kiszámítására korlátozások. 

## <a name="algorithms-and-libraries"></a>Algoritmusok és tárak

**Milyen algoritmusok Azure Machine Learning-munkaterület támogatottak?**

A kép termékeink és szolgáltatásaink közé tartozik a legjobb a nyílt forráskódú Közösség. Algoritmusok és könyvtárak TensorFlow, scikit beleértve számos támogatjuk-Apache Spark, és a Microsoft kognitív eszközkészlet megismeréséhez. Az Azure Machine Learning-munkaterület csomagokat is a [Microsoft revoscalepy](https://docs.microsoft.com/sql/advanced-analytics/python/what-is-revoscalepy) csomag.

**Azure Machine Learning hogyan kapcsolódik a Microsoft kognitív eszközkészlet?**

A [Microsoft kognitív eszközkészlet](https://www.microsoft.com/cognitive-toolkit/) az új eszközök és szolgáltatások által támogatott sok keretrendszerek egyike. A kognitív eszközkészlet egy egységes mély tanulási eszközkészlet, amely lehetővé teszi használnak, és népszerű gépi tanulási adatcsatorna-továbbító Neurális hálózatokat, Convolutional hálók, beleértve a modellek egyesítése Sequence-Sequence és ismétlődő hálózatok. A Microsoft kognitív eszközkészlet további tudnivalókért keresse fel a [termékdokumentációjának](https://docs.microsoft.com/cognitive-toolkit/). 

## <a name="pricing-and-billing"></a>Árak és számlázás

**Vannak, díjszabási Azure Machine Learning előzetes?** 

V: az Azure Machine Learning-munkaterület alkalmazást az Azure-előfizetők ingyenes. A kísérleti szolgáltatás és a modell kezelése kínál az ingyenes rétegek fizetett szinteken, nyilvános előzetes kedvezményes áron avialable mellett.

**I megterheljük futtattam hány kísérletek alapján?**

Nem. A kísérletezés szolgáltatásban korlátlan számú kísérlet elvégezhető, a fizetendő díjat pedig kizárólag a felhasználók száma határozza meg. A kísérletezéshez felhasznált számítási erőforrásokért külön díjat számolunk fel.  Javasoljuk, hogy a megoldás a legjobb modell elérésének több kísérletek végrehajtása. 

**I kiszabott alapján hány alkalommal a webszolgáltatások lesz neve?**

Nem. Webszolgáltatások hívhatók szükséges, a számlázási modell kezelése hatással nélkül gyakorisággal. Az üzemelő példányok méretezését teljes egészében Ön határozhatja meg az alkalmazások szükségleteihez igazodva.

**Hogyan méretezheti I vásárolt, az Azure Machine Learning modell kezelése az egységek száma?**

Módosíthatja a egységek száma felfelé vagy lefelé, az Azure-portálon vagy a parancssori felület használatával. 

**Mi lesz a számlázási megjelenését?**

Váltók naponta előállítása. A számlázás tekintetében a nap kezdete az UTC szerinti éjfél. A számlázás havi rendszerességgel történik. Külön díjak sem merülnek fel a bármely Azure-szolgáltatások, az Azure Machine Learning együtt használni. Díjak lehetnek, azonban nem csak: 
- Számítási díjakat
- HDInsight
- Azure Container Service
- Azure Container Registry 
- Azure Blob Storage
- Application Insights
- Azure Key Vault
- Visual Studio Team Services
- Az Azure Event Hubs
- Az Azure Stream Analytics a a további részleteket, vagy egy minta számlázási megtekintéséhez látogasson el az árképzést ismertető oldalra. 

## <a name="support-and-training"></a>Támogatás és betanítás

**Hol kaphatok képzést az Azure Machine Learning használatáról?**

Az [Azure Machine Learning dokumentációs központjában](./overview-what-is-azure-ml.md) oktatóvideókat és útmutatókat talál az Azure Machine Learning használatáról. Ezek a lépésenkénti útmutatók a szolgáltatások esetében, és az adatok tudományos életciklusát ismertetik. A Machine Learning központját folyamatosan új anyagokkal bővítjük. Kérelmezheti, a felhasználó-visszajelzési fórumon további tanulási anyagok esetében.

**Hol kaphatok támogatást az Azure Machine Learninghez?**

Ha technikai támogatást nyissa meg Azure támogatásához, és válassza ki a Machine Learning. Az Azure Machine Learning is rendelkezik egy aktív közösségi [StackOverflow](https://stackoverflow.com/questions/tagged/azure-machine-learning) "Azure-gépi tanulásra", a csapat által figyelt címkézését.  Javaslatok és funkciókra vonatkozó kérések küldheti el a [Azure Machine Learning visszajelzés](https://feedback.azure.com/forums/257792-machine-learning) hely. 
