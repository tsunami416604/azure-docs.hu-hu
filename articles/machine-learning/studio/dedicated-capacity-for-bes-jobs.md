---
title: Az Azure Batch szolgáltatás a Machine Learning Studio-feladatok |} A Microsoft Docs
description: Machine Learning-feladatok Azure Batch szolgáltatás áttekintése. Batch-készlet feldolgozási lehetővé teszi, hogy hozhat létre készleteket, amelyre elküldheti a batch-feladatok.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: seodec18, previous-title='Dedicated capacity for batch execution service jobs - Azure Machine Learning Studio | Microsoft Docs'
ms.author: amlstudiodocs
editor: cgronlun
ms.service: machine-learning
ms.component: studio
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.openlocfilehash: 44b63a51049e663e55cc59a623837cf65cab9ccc
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53275193"
---
# <a name="azure-batch-service-for-azure-machine-learning-studio-jobs"></a>Az Azure Batch szolgáltatás az Azure Machine Learning Studio-feladatok

Machine Learning Batch-készlet feldolgozási ügyfél által felügyelt méretezési biztosít az Azure Machine Learning kötegelt végrehajtási szolgáltatás. Klasszikus kötegelt feldolgozása, egy több-bérlős környezet, amely korlátozza az egyidejűleg futó feladatainak számát a sor kerül a machine learning küldhet, és a feladatok sorba állva várják első-az-érkezési idősorrendben történik. Ez a bizonytalanság azt jelenti, hogy Ön nem előre pontosan mikor fog futni a feladat.

Batch-készlet feldolgozási lehetővé teszi, hogy hozhat létre készleteket, amelyre elküldheti a batch-feladatok. Ön szabályozhatja, hogy a készlet méretét, és mely készlethez a feladat elküldésekor. A BES-feladat futtatása a saját feldolgozási területen biztosít, kiszámítható feldolgozási teljesítmény, és lehetővé teszi, amelyek megfelelnek a feldolgozási terhelés számához erőforráskészletek létrehozását.

## <a name="how-to-use-batch-pool-processing"></a>Feldolgozás Batch-készlet használata

A Batch-készlet feldolgozási konfigurációs jelenleg nem áll rendelkezésre az Azure Portalon keresztül. Batch-készlet feldolgozási használatához tegye a következőket:

-   Batch-készlet-fiók létrehozása és a egy készlet szolgáltatás URL-CÍMÉT és a egy engedélyezési kulcsot a CSS hívása
-   Hozzon létre egy új Resource Manager-alapú webszolgáltatás és a számlázási csomag

A fiók létrehozásához, hívja fel a Microsoft ügyfél- és támogatási (CSS), és adja meg az előfizetés-azonosító. CSS fog dolgozni, hogy az adott forgatókönyvnek megfelelő kapacitásának meghatározásához. CSS ezután konfigurálja a fiókot hozhat létre készleteket maximális számát és a virtuális gépek (VM), amely minden készlet elhelyezheti maximális számát. Ha a fiók már konfigurálva van, rendelkezésre a készlet szolgáltatás URL-CÍMÉT és a egy engedélyezési kulcsot.

Miután létrehozta a fiókot, a készlet szolgáltatás URL-CÍMÉT és a hitelesítési kulcs használatával készletet a Batch-készlet a műveletek végrehajtása.

![Batch-készlet szolgáltatás architektúrája.](./media/dedicated-capacity-for-bes-jobs/pool-architecture.png)

A készlet szolgáltatás URL-CÍMÉT, amely az Ön számára biztosított CSS-készlet létrehozása művelet meghívásával létrehozott készletek. Egy készlet létrehozásakor meg kell adnia, a virtuális gépek számát és az URL-címét a swagger.json egy új Resource Manager-alapú Machine Learning webszolgáltatás. A webszolgáltatás létrehozására, a számlázási társítás biztosítunk. A Batch-készlet szolgáltatás a megfelelő swagger.json rendelje hozzá a készlethez egy számlázási csomagot használja. Futtatható bármely BES webszolgáltatás, mindkét új Resource Manager-alapú és klasszikus, választja a készleten.

Olyan új Resource Manager-alapú webes szolgáltatást használ, de vegye figyelembe, hogy az a feladatok díjszabása a szolgáltatáshoz tartozó számlázási csomag vannak terhelve. Érdemes egy webszolgáltatás és az új számlázási csomag kifejezetten a futó feladatok Batch-készlet létrehozásához.

Webszolgáltatások létrehozásával kapcsolatos további információkért lásd: [egy Azure Machine Learning webszolgáltatás üzembe helyezése](publish-a-machine-learning-web-service.md).

Miután létrehozott egy készletet, a kötegelt kérelem URL-cím használatával a webszolgáltatás a BES-feladat elküldése. Kiválaszthatja a készlethez, vagy a klasszikus kötegelt feldolgozásra küldje. A Batch-készlet feldolgozási feladatok elküldéséhez ad hozzá a következő paramétert a feladat beküldése kérelem törzse:

"AzureBatchPoolId": "&lt;tárolókészlet azonosítója&gt;"

Ha nem adja hozzá a paramétert, a feladat fut, a klasszikus kötegelt folyamat környezetben. A készlet elegendő erőforrás áll rendelkezésre, ha a feladat azonnal el fog indulni. Ha a készlet nem rendelkezik az ingyenes forrásokat, a feladat várólistára van állítva, ameddig egy erőforrás nem érhető el.

Ha azt tapasztalja, hogy rendszeresen eléri a készletek kapacitását, és nagyobb kapacitásra van szüksége, CSS hívja, és növelheti a kvótákat képviselőjét dolgozhat.

Kérelem (példa):

https://ussouthcentral.services.azureml.net/subscriptions/80c77c7674ba4c8c82294c3b2957990c/services/9fe659022c9747e3b9b7b923c3830623/jobs?api-version=2.0

```json
{

    "Input":{
    
        "ConnectionString":"DefaultEndpointsProtocol=https;BlobEndpoint=https://sampleaccount.blob.core.windows.net/;TableEndpoint
        =https://sampleaccount.table.core.windows.net/;QueueEndpoint=https://sampleaccount.queue.core.windows.net/;FileEndpoint=https://zhguim
        l.file.core.windows.net/;AccountName=sampleaccount;AccountKey=&lt;Key&gt;;",
        
        "BaseLocation":null,
        
        "RelativeLocation":"testint/AdultCensusIncomeBinaryClassificationDataset.csv",
        
        "SasBlobToken":null
    
    },
    
    "GlobalParameters":{ },
    
    "Outputs":{
    
        "output1":{
        
            "ConnectionString":"DefaultEndpointsProtocol=https;BlobEndpoint=https://sampleaccount.blob.core.windows.net/;TableEndpo
            int=https://sampleaccount.table.core.windows.net/;QueueEndpoint=https://sampleaccount.queue.core.windows.net/;FileEndpoint=https://sampleaccount.file.core.windows.net/;AccountName=sampleaccount;AccountKey=&lt;Key&gt;",
            "BaseLocation":null,
            "RelativeLocation":"testintoutput/testint\_results.csv",
            
            "SasBlobToken":null
        
        }
    
    },
    
    "AzureBatchPoolId":"8dfc151b0d3e446497b845f3b29ef53b"

}
```

## <a name="considerations-when-using-batch-pool-processing"></a>Batch-készlet feldolgozási használatának szempontjai

Batch-készlet feldolgozása egy folyamatosan elérhető számlázható szolgáltatás létrejöttét és, társíthatja azt egy Resource Manager-alapú számlázási csomag szükséges. Csak lesznek számlázva a készlet fut; a számítási órák száma feladatok futtatása során a idő készlet függetlenül. Ha létrehoz egy készletet, számlázzuk ki a számítási óra a készletben lévő minden egyes virtuális gép mindaddig, amíg a készlet törlése, még akkor is, ha fut egy feladat sem a batch-készletben. A számlázás a virtuális gépek akkor kezdődik, amikor a kiépítés után, és leáll, amikor törölve lettek. Található a csomagok valamelyikét használhatja a [Machine Learning díjszabását ismertető weblapon](https://azure.microsoft.com/pricing/details/machine-learning/).

A számlázás. példa:

Ha a Batch-készlet létrehozása 2 virtuális gépekkel, és 24 óra múlva törli azt a számlázási csomag terhelt 48 számítási óra; függetlenül attól, hogy hány feladatok futtatná időszak alatt.

Ha a Batch-készlet létrehozása 4 virtuális gépekkel, és 12 óra múlva törli azt, a számlázási csomag egyben terhelést 48 számítási óra.

Azt javasoljuk, hogy a meghatározásához, ha a feladatok elvégzése a feladat állapotának lekérdezéséhez. Ha az összes feladat végzett futtatását, hívja meg a készlet átméretezése művelet beállítása a virtuális gépek számát a készletben, nulla. Ha az adatbáziskészlet erőforrásainak rövid, és szeretne létrehozni egy új készletet, például egy másik számlázási csomag elleni számlázási törölheti a készlet inkább ha összes feladat végzett futtatását.


| **Mikor feldolgozás Batch-készlet használata**    | **Klasszikus kötegelt feldolgozási, mikor használja**  |
|---|---|
|Futó feladatok nagy számú szüksége<br>Vagy<br/>Érdemes tudni, hogy a feladatok azonnal futtatja-e<br/>Vagy<br/>Garantált átviteli van szüksége. Például hogy kell feladatok számos futtatásához egy megadott időkereten belül, és szeretne horizontális felskálázása a számítási erőforrásokat az igényeknek.    | Néhány feladat futtatásakor<br/>És<br/> A feladatok azonnal nem szükséges |
