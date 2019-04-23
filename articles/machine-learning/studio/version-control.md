---
title: Alkalmazások életciklus-felügyelete
titleSuffix: Azure Machine Learning Studio
description: Application Lifecycle Management ajánlott eljárások az Azure Machine Learning Studio a alkalmazni
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.date: 10/27/2016
ms.openlocfilehash: 046afaa0e83fa572d6cd43a3717707892b25af69
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59793692"
---
# <a name="application-lifecycle-management-in-azure-machine-learning-studio"></a>Alkalmazáséletciklus-kezelés az Azure Machine Learning Studióban
Az Azure Machine Learning Studio egy olyan eszköz, amely a rendszer üzembe helyezte azt az Azure-felhőplatformon a machine learning-kísérletek fejlesztéséhez. Például a Visual Studio IDE, és a méretezhető felhőalapú szolgáltatás Egyesítés egyetlen platformban. Beépítheti a verziókezelés standard Application Lifecycle Management (ALM) eljárásait különböző eszközök automatikus végrehajtás és üzembe helyezés, Azure Machine Learning studióba. Ez a cikk ismerteti az egyes beállítások és módszerek.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="versioning-experiment"></a>Verziókezelés kísérlet
Kétféleképpen ajánlott verzió, a kísérletek. A beépített futtatási előzmények támaszkodnak, vagy exportálja a kísérletet, külsőleg kezelheti, hogy JSON formátumban. Mindegyik megközelítésnek és a hátrányai tartalmaz.

### <a name="experiment-snapshots-using-run-history"></a>Kísérlet a pillanatképek használata a futtatási előzmények
A végrehajtási modellben, az Azure Machine Learning Studio tanulási kísérletet a kísérlet egy nem módosítható pillanatkép elküldve a a Feladatütemező, amikor kattint **futtatása** a kísérlet szerkesztőben. A pillanatképek listáját megtekintéséhez kattintson **futtatási előzmények** a kísérlet szerkesztő nézetben a parancssávon.

![Futtatási előzmények gomb](./media/version-control/runhistory.png)

Lehetőség van, akkor nyissa meg a pillanatkép a kísérletet a kísérlet futtatása és a pillanatkép el lett küldve időben nevére kattintva zárolt módban kerül. Figyelje meg, hogy a listában, a jelenlegi kísérletet jelöl, amely csak az első elem szerkeszthető állapotban van. Is figyelje meg, hogy minden pillanatkép különböző állapota lehet, állapotok befejeződött (a részleges futtatása), sikertelen, beleértve a sikertelen (a részleges futtatása), vagy vázlatszintű.

![Futtatási előzmények lista](./media/version-control/runhistorylist.png)

Megnyitás után mentse a pillanatkép kísérlet egy új kísérlet, és módosíthatja azt. A kísérlet pillanatkép adategységeket, mint a betanított modellek, átalakítások vagy adatkészletek frissített verzióit tartalmazza, ha a pillanatkép megőrzi az eredeti verzió mutató hivatkozásokat, ha a pillanatkép. Új kísérlet menti a zárolt pillanatkép, ha az Azure Machine Learning Studio ezeknek az eszközöknek egy újabb verziója meglétét észleli, és automatikusan frissíti őket az új kísérlet.

Ha törli a kísérletet, a rendszer törli a kísérlet összes pillanatképet.

### <a name="exportimport-experiment-in-json-format"></a>Exportálás/importálás kísérlet JSON formátumban
A futtatási előzményeket pillanatképeknek a kísérlet egy nem módosítható verzióját minden alkalommal, amikor azt elküldve a futtatása az Azure Machine Learning Studio ne. Is mentheti a kísérlet egy helyi példányát, és ellenőrizze a kedvenc forráskódú verziókezelő rendszer, például a Team Foundation Server, és később hozza létre újból a helyi fájl kísérlet. Használhatja a [Azure Machine Learning PowerShell](https://aka.ms/amlps) parancsmagok [ *Export-AmlExperimentGraph* ](https://github.com/hning86/azuremlps#export-amlexperimentgraph) és [  *Import-AmlExperimentGraph* ](https://github.com/hning86/azuremlps#import-amlexperimentgraph) hajthatja végre, hogy.

A JSON-fájlt a kísérlet diagram, többek között előfordulhat, hogy eszközökhöz való hivatkozást a munkaterületen, például egy adatkészletet és betanított modell képviselő szöveges alakot. A szerializált verzió az eszköz nem tartalmaz. Kísérel meg importálni a JSON-dokumentum vissza a munkaterületre, ha a hivatkozott eszközök már léteznie kell az adott objektum azonosítóját, amely a kísérletben hivatkozott. Ellenkező esetben nem férhet hozzá az importált kísérletet.

## <a name="versioning-trained-model"></a>Verziókezelés betanított modell
Az Azure Machine Learning Studióban betanított modell szerializált olyan formátumra, más néven iLearner-fájlt (`.iLearner`), és tárolja a munkaterülethez társított Azure Blob storage-fiók. Egyik módja a iLearner-fájlt egy példánya, a megőrzési API-n keresztül. [Ez a cikk](/azure/machine-learning/studio/retrain-machine-learning-model) ismerteti a megőrzési API működése. A magas szintű lépéseket:

1. Állítsa be a betanítási kísérlet.
2. Adja hozzá a web service kimeneti portját a tanítási modell modulhoz, vagy a modul, amely a betanított modell, például a modell Hiperparaméter finomhangolása vagy az R-modell létrehozása.
3. A betanítási kísérlet futtatásához és majd üzembe helyezheti webszolgáltatásként, amely modell betanítási.
4. A BES végpont a betanítási webes szolgáltatás hívása, és adja meg a kívánt iLearner fájl neve és a Blob-tárfiók helyszíne azt tárolásához.
5. Az előállított iLearner-fájlt gyűjtsön, a BES hívás befejeződése után.

Kérje le a iLearner-fájlt egy másik módja, a PowerShell parancsmagokon keresztül [ *letöltési-AmlExperimentNodeOutput*](https://github.com/hning86/azuremlps#download-amlexperimentnodeoutput). Ez megkönnyíti, ha csak át szeretné anélkül, hogy a modell programozott újratanítás kellene iLearner-fájlt egy példánya lehet.

Miután a betanított modellt tartalmazó iLearner-fájlt, majd a saját verziókezelési stratégiát alkalmazhat. A stratégia lehet egyszerűen alkalmazása előtti/utótag egy elnevezési konvenciója, és csak a Blob storage-ban iLearner-fájlt elhagyása, vagy másolja/való importálás a verziókezelő rendszer.

A mentett iLearner-fájlt majd keresztül üzembe helyezett webszolgáltatások pontozó használható.

## <a name="versioning-web-service"></a>Verziókezelés webszolgáltatás
Kétféle típusú webes szolgáltatás közül az Azure Machine Learning Studio kísérletezéshez telepítheti. A klasszikus webszolgáltatás szorosan összekapcsolt, a kísérletet, valamint a munkaterületen. Az új webszolgáltatás használja az Azure Resource Manager-keretrendszert, és azt már nem az eredeti kísérlet vagy a munkaterület együtt használja.

### <a name="classic-web-service"></a>Klasszikus webszolgáltatás
Klasszikus webszolgáltatások verzióra akkor kihasználhatja a web service végpont szerkezet. A következő jellemzően a következő folyamatot:

1. A prediktív kísérletből telepít egy új klasszikus webszolgáltatás, amely tartalmaz egy alapértelmezett végpont.
2. Ep2, amely a jelenlegi verziója a kísérlet/betanított modell nevű új végpont létrehozásához.
3. Lépjen vissza, és frissítse a prediktív kísérletet, és a betanított modell.
4. A prediktív kísérletet, amely frissíti az alapértelmezett végpont újbóli telepítése. De ez nem módosítja a ep2.
5. Létrehozhat egy további végpont nevű ep3, amely a kísérletet, és a betanított modell új verzióját.
6. Lépjen vissza, szükség esetén a 3. lépéssel.

Idővel lehetséges, hogy az azonos web service-ben létrehozott sok végpont. Minden végpont a kísérletet, amely tartalmazza a időponthoz verzióját a betanított modell egy időponthoz példányát jelöli. Ezután használhatja a külső logikai szeretne hívásokat indítani, melyik végponthoz meghatározásához vagyis hatékonyan kiválasztja a pontozási Futtatás a betanított modell egy verziója.

Sok azonos webszolgáltatás-végpontok is létrehozhat, és ezután javítani a iLearner-fájlt a végpontra hasonló hatás eléréséhez különböző verzióit. [Ez a cikk](create-models-and-endpoints-with-powershell.md) részletesebben ismerteti, hogyan hajthatja végre, hogy.

### <a name="new-web-service"></a>Új webszolgáltatás
Ha létrehoz egy új Azure Resource Manager-alapú webes szolgáltatás, a végpont szerkezet már nem érhető el. Ehelyett létrehozhat webes szolgáltatás definíciós (WSD) fájlok, JSON formátumban, az a prediktív kísérletből a [Export-AmlWebServiceDefinitionFromExperiment](https://github.com/hning86/azuremlps#export-amlwebservicedefinitionfromexperiment) PowerShell-parancsmag segítségével, vagy a [ *Export-AzMlWebservice* ](https://docs.microsoft.com/powershell/module/az.machinelearning/export-azmlwebservice) egy már üzembe helyezett Resource Manager-alapú webszolgáltatás a PowerShell-parancsmag segítségével.

Miután az exportált WSD-fájlt, és szabályozhatja azt a verziót, is telepítheti a WSD új webszolgáltatásként egy másik web service-csomag egy másik Azure-régióban. Csak ellenőrizze, hogy a megfelelő storage-fiók konfigurációját, valamint az új webes szolgáltatási csomag. Adjon meg Javítása a különböző iLearner-fájlt, módosítsa a WSD-fájlt, és a hely hivatkozás a betanított modell frissítése, és új webszolgáltatásként üzembe helyezni.

## <a name="automate-experiment-execution-and-deployment"></a>Kísérlet-végrehajtás és üzembe helyezésének automatizálása
Fontos szempont a ALM, hogy tudni végrehajtási és az alkalmazás központi telepítési folyamat automatizálása. Az Azure Machine Learning Studióban, akkor ez segítségével végezheti a [PowerShell-modul](https://aka.ms/amlps). Íme egy példa, amely a standard szintű alkalmazások teljes körű lépések automatikus végrehajtás és üzembe helyezési folyamat használatával a [Azure Machine Learning Studio PowerShell modul](https://aka.ms/amlps). Az egyes lépések, amelyek segítségével a lépés elvégzéséhez legalább egy PowerShell-parancsmagok van csatolva.

1. [Töltse fel az adatkészlet](https://github.com/hning86/azuremlps#upload-amldataset).
2. Betanítási kísérlet másolja be a munkaterületet egy [munkaterület](https://github.com/hning86/azuremlps#copy-amlexperiment) vagy [katalógus](https://github.com/hning86/azuremlps#copy-amlexperimentfromgallery), vagy [importálása](https://github.com/hning86/azuremlps#import-amlexperimentgraph) egy [exportált](https://github.com/hning86/azuremlps#export-amlexperimentgraph) helyi kísérlet a lemez.
3. [Az adatkészlet frissítéséhez](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) a betanítási kísérlet során.
4. [A tanítási kísérlet futtatása](https://github.com/hning86/azuremlps#start-amlexperiment).
5. [A betanított modell előléptetése](https://github.com/hning86/azuremlps#promote-amltrainedmodel).
6. [Másolja egy prediktív kísérletet](https://github.com/hning86/azuremlps#copy-amlexperiment) a munkaterületre.
7. [Frissítse a betanított modell](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) az a prediktív kísérletet.
8. [Futtassa a prediktív kísérletet](https://github.com/hning86/azuremlps#start-amlexperiment).
9. [Webszolgáltatás üzembe helyezése](https://github.com/hning86/azuremlps#new-amlwebservice) a prediktív kísérletből.
10. A webszolgáltatás teszteléséhez [RRS](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) vagy [BES](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint) végpont.

## <a name="next-steps"></a>További lépések
* Töltse le a [Azure Machine Learning Studio PowerShell](https://aka.ms/amlps) modul és a kezdési a ALM feladatok automatizálására.
* Ismerje meg, hogyan [létrehozása és kezelése a nagy számú gépi Tanulási modellek csak egyetlen kísérletben használatával](create-models-and-endpoints-with-powershell.md) PowerShell és az átképezési API segítségével.
* Tudjon meg többet [Azure Machine Learning-webszolgáltatások üzembe helyezéséhez](publish-a-machine-learning-web-service.md).
