---
title: Alkalmazások életciklus-felügyelete
titleSuffix: ML Studio (classic) - Azure
description: Alkalmazáséletciklus-kezelés alkalmazása – gyakorlati tanácsok az Azure Machine Learning Studióban (klasszikus)
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 10/27/2016
ms.openlocfilehash: 3f22ce3b1fb750e33e35d35ee1fe5ad1893abcfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204136"
---
# <a name="application-lifecycle-management-in-azure-machine-learning-studio-classic"></a>Alkalmazáséletciklus-kezelés az Azure Machine Learning Studióban (klasszikus)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Az Azure Machine Learning Studio (klasszikus) az Azure felhőplatformon működő gépi tanulási kísérletek fejlesztésére szolgáló eszköz. Olyan, mintha a Visual Studio IDE és a skálázható felhőszolgáltatás egyetlen platformba olvadna. A standard alkalmazáséletciklus-kezelési (ALM) eljárásokat a különböző eszközök és az automatikus végrehajtás és üzembe helyezés verziószámozásától az Azure Machine Learning Studio -ig (klasszikus) építheti be. Ez a cikk néhány lehetőséget és megközelítést tartalmaz.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="versioning-experiment"></a>Verziószámozási kísérlet
A kísérletek et két ajánlott módon lehet verzióval verziózni. Támaszkodhat a beépített futtatási előzményekre, vagy exportálhatja a kísérletet JSON formátumban, hogy külsőleg kezelje azt. Minden megközelítés jön-val az előnye és hátránya.

### <a name="experiment-snapshots-using-run-history"></a>Kísérletpillanatképek futtatási előzmények használatával
Az Azure Machine Learning Studio (klasszikus) tanulási kísérlet végrehajtási modelljében a kísérlet egy nem módosítható pillanatképe kerül elküldésre a feladatütemezőnek, amikor a **Futtatás** a kísérletszerkesztőben parancsra kattint. A pillanatképek listájának megtekintéséhez kattintson az **Előzmények futtatása** elemre a parancssávon a kísérletszerkesztő nézetben.

![Az Előzmények futtatása gomb](./media/version-control/runhistory.png)

Ezután megnyithatja a pillanatképet Zárolt módban, ha a kísérlet nevére kattint a kísérlet futtatásakor és a pillanatkép készítéskor. Figyelje meg, hogy csak a lista első eleme, amely az aktuális kísérletet jelöli, szerkeszthető állapotban van. Azt is vegye figyelembe, hogy minden pillanatkép lehet a különböző állapotállapotok is, beleértve a befejezett (részleges futtatás), sikertelen, sikertelen (részleges futtatás) vagy vázlat.

![Előzmények futtatása lista](./media/version-control/runhistorylist.png)

Megnyitása után mentheti a pillanatkép-kísérletet új kísérletként, majd módosíthatja azt. Ha a kísérlet pillanatképe olyan eszközöket tartalmaz, például betanított modellek, átalakítások vagy adatkészletek, amelyek frissített verziók, a pillanatkép megtartja az eredeti verzióra mutató hivatkozásokat, amikor a pillanatkép készült. Ha a zárolt pillanatképet új kísérletként menti, az Azure Machine Learning Studio (klasszikus) észleli ezen eszközök újabb verziójának létezését, és automatikusan frissíti azokat az új kísérletben.

Ha törli a kísérletet, a kísérlet összes pillanatképe törlődik.

### <a name="exportimport-experiment-in-json-format"></a>Exportálási/importálási kísérlet JSON formátumban
A futtatási előzmények pillanatképei a kísérlet nem módosítható verzióját tartják meg az Azure Machine Learning Studio -ban (klasszikus) minden alkalommal, amikor futtatásra beküldik. Mentheti a kísérlet helyi példányát is, és beadhatja a kedvenc forrásvezérlő rendszerébe, például a Team Foundation Serverbe, majd később újra létrehozhat egy kísérletet abból a helyi fájlból. Ehhez használhatja az [Azure Machine Learning PowerShell](https://aka.ms/amlps) parancsokkal [*Az Export-AmlExperimentGraph*](https://github.com/hning86/azuremlps#export-amlexperimentgraph) és [*az Import-AmlExperimentGraph parancsokkal.*](https://github.com/hning86/azuremlps#import-amlexperimentgraph)

A JSON-fájl a kísérletgrafikon szöveges ábrázolása, amely tartalmazhat egy hivatkozást a munkaterület i. eszközökre, például egy adatkészletre vagy egy betanított modellre. Nem tartalmazza az eszköz szerializált verzióját. Ha megpróbálja importálni a JSON-dokumentumot a munkaterületre, a hivatkozott eszközöknek már létezniük kell a kísérletben hivatkozott eszközazonosítókkal. Ellenkező esetben nem férhet hozzá az importált kísérlethez.

## <a name="versioning-trained-model"></a>Verziószámozás betanított modell
Egy betanított modell az Azure Machine Learning Studio (klasszikus) szerializált`.iLearner`egy formátumban ismert iLearner fájl ( ), és a munkaterülethez társított Azure Blob tárfiók tárolja. Az egyik módja annak, hogy egy másolatot az iLearner fájl átképzési API-n keresztül. [Ez](/azure/machine-learning/studio/retrain-machine-learning-model) a cikk bemutatja, hogyan működik az átképzési API. A magas szintű lépések:

1. Állítsa be a képzési kísérletet.
2. Adjon hozzá egy webszolgáltatás kimeneti portját a Betanítási modell modulhoz, vagy a betanított modellt előállító modulhoz, például a Tune Model Hyperparameter vagy az R modell létrehozása.
3. Futtassa a betanítási kísérletet, majd telepítse modellként webes szolgáltatásként.
4. Hívja meg a betanítási webszolgáltatás BES-végpontját, és adja meg a kívánt iLearner-fájlnevet és a Blob-tárfiók helyét, ahol a rendszer tárolni fogja.
5. A BES-hívás befejezése után gyűjtse be az előállított iLearner fájlt.

Az iLearner fájl beolvasásának másik módja a [*Download-AmlExperimentNodeOutput*](https://github.com/hning86/azuremlps#download-amlexperimentnodeoutput)PowerShell parancsleten keresztül történik. Ez egyszerűbb lehet, ha csak azt, hogy egy példányát az iLearner fájlt anélkül, hogy újra betanítása a modell programozott módon.

Miután a betanított modellt tartalmazó iLearner-fájlt, alkalmazhatja a saját verziózási stratégiáját. A stratégia lehet olyan egyszerű, mint egy előde/postfix alkalmazása elnevezési konvencióként, és csak elhagyja az iLearner fájlt a Blob storage-ban, vagy másolja /importálja azt a verziókövető rendszerbe.

A mentett iLearner fájl ezután használható a telepített webszolgáltatásokon keresztültörténő pontozáshoz.

## <a name="versioning-web-service"></a>Verziószámozási webszolgáltatás
Kétféle webszolgáltatásokat telepíthet egy Azure Machine Learning Studio (klasszikus) kísérletből. A klasszikus webszolgáltatás szorosan kapcsolódik a kísérlethez és a munkaterülethez. Az új webszolgáltatás az Azure Resource Manager keretrendszert használja, és már nem kapcsolódik az eredeti kísérlethez vagy a munkaterülethez.

### <a name="classic-web-service"></a>Klasszikus webszolgáltatás
Klasszikus webszolgáltatás verziójú verzió, kihasználhatja a webszolgáltatás végpont-konstrukció. Itt van egy tipikus folyamat:

1. A prediktív kísérlet egy új klasszikus webszolgáltatás, amely tartalmazza az alapértelmezett végpontot.
2. Hozzon létre egy új végpont nevű ep2, amely elérhetővé teszi a kísérlet/betanított modell aktuális verzióját.
3. Menjen vissza, és frissítse a prediktív kísérletet és a betanított modellt.
4. Újratelepíti a prediktív kísérletet, amely ezután frissíti az alapértelmezett végpontot. De ez nem változtatja meg ep2.
5. Hozzon létre egy további végpont nevű ep3, amely elérhetővé teszi a kísérlet új verzióját, és a betanított modell.
6. Menjen vissza a 3.

Idővel előfordulhat, hogy több végpontok ugyanabban a webszolgáltatásban létrehozott. Minden végpont a kísérlet időponthoz kötött másolatát jelöli, amely a betanított modell időponthoz kötött verzióját tartalmazza. Ezután a külső logika segítségével határozza meg, hogy melyik végpontot kell hívni, ami gyakorlatilag azt jelenti, hogy kiválasztja a betanított modell egy verzióját a pontozási futtatáshoz.

Számos azonos webszolgáltatás-végpontot is létrehozhat, majd az iLearner fájl különböző verzióit a végponthoz javíthatja hasonló hatás elérése érdekében. [Ez](create-models-and-endpoints-with-powershell.md) a cikk részletesebben ismerteti, hogyan lehet ezt megvalósítani.

### <a name="new-web-service"></a>Új webes szolgáltatás
Ha új Azure Resource Manager-alapú webszolgáltatást hoz létre, a végpont-konstrukció már nem érhető el. Ehelyett létrehozhat webszolgáltatás-definíciós (WSD) fájlokat JSON formátumban, a prediktív kísérletből az [Export-AmlWebServiceDefinitionFromExperiment](https://github.com/hning86/azuremlps#export-amlwebservicedefinitionfromexperiment) PowerShell parancslap használatával, vagy az [*Export-AzMlWebservice*](https://docs.microsoft.com/powershell/module/az.machinelearning/export-azmlwebservice) PowerShell parancsleménnyel egy telepített Erőforrás-kezelő alapú webszolgáltatásból.

Miután az exportált WSD-fájlt és verziókövetést, telepítheti a WSD-t új webszolgáltatásként egy másik webszolgáltatási csomagban egy másik Azure-régióban. Csak győződjön meg róla, hogy a megfelelő tárfiók konfigurációját, valamint az új webszolgáltatás-csomag azonosítóját adja meg. A különböző iLearner-fájlok javításához módosíthatja a WSD-fájlt, frissítheti a betanított modell helyhivatkozását, és új webszolgáltatásként telepítheti azt.

## <a name="automate-experiment-execution-and-deployment"></a>A kísérlet végrehajtásának és telepítésének automatizálása
Az ALM fontos szempontja az alkalmazás végrehajtási és telepítési folyamatának automatizálása. Az Azure Machine Learning Studio (klasszikus) a [PowerShell-modul](https://aka.ms/amlps)használatával valósítható meg. Íme egy példa a végpontok között lépéseket, amelyek relevánsak a szabványos ALM automatikus végrehajtási/telepítési folyamat az [Azure Machine Learning Studio (klasszikus) PowerShell-modul](https://aka.ms/amlps)használatával. Minden lépés egy vagy több PowerShell-parancslaphoz van csatolva, amelyek segítségével végrehajthatja ezt a lépést.

1. [Adatkészlet feltöltése](https://github.com/hning86/azuremlps#upload-amldataset).
2. Átmásolhat egy betanítási kísérletet a munkaterületre egy [munkaterületről](https://github.com/hning86/azuremlps#copy-amlexperiment) vagy a [Katalógusból,](https://github.com/hning86/azuremlps#copy-amlexperimentfromgallery)vagy [importálhat](https://github.com/hning86/azuremlps#import-amlexperimentgraph) egy [exportált kísérletet](https://github.com/hning86/azuremlps#export-amlexperimentgraph) helyi lemezről.
3. [Frissítse az adatkészletet](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) a betanítási kísérletben.
4. [Futtassa a betanítási kísérletet.](https://github.com/hning86/azuremlps#start-amlexperiment)
5. [A betanított modell népszerűsítése](https://github.com/hning86/azuremlps#promote-amltrainedmodel).
6. [Prediktív kísérlet másolása](https://github.com/hning86/azuremlps#copy-amlexperiment) a munkaterületre.
7. [Frissítse a betanított modellt](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) a prediktív kísérletben.
8. [Futtassa a prediktív kísérletet.](https://github.com/hning86/azuremlps#start-amlexperiment)
9. [Webszolgáltatás üzembe helyezése](https://github.com/hning86/azuremlps#new-amlwebservice) a prediktív kísérletből.
10. Tesztelje a webszolgáltatás [RRS](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) vagy [BES-végpont.](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint)

## <a name="next-steps"></a>További lépések
* Töltse le az [Azure Machine Learning Studio (klasszikus) PowerShell-modult,](https://aka.ms/amlps) és kezdje el automatizálni az ALM-feladatokat.
* Ismerje meg, hogyan [hozhat létre és kezelhet nagyszámú ml-modellt egyetlen kísérlet használatával a](create-models-and-endpoints-with-powershell.md) PowerShellen és az átképzési API-n keresztül.
* További információ [az Azure Machine Learning webszolgáltatások üzembe helyezéséről.](deploy-a-machine-learning-web-service.md)
