---
title: Azure Batch szolgáltatási feladatok
titleSuffix: ML Studio (classic) Azure
description: Machine Learning Studio (klasszikus) feladatok Azure Batch szolgáltatásainak áttekintése. A Batch-készlet feldolgozása lehetővé teszi, hogy olyan készleteket hozzon létre, amelyeken kötegelt feladatokat küldhet.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18, previous-title='Dedicated capacity for batch execution service jobs - Azure Machine Learning Studio (classic) | Microsoft Docs'
ms.date: 04/19/2017
ms.openlocfilehash: a0e829f61117df25f8643301a0acdadeafd7c267
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684811"
---
# <a name="azure-batch-service-for-azure-machine-learning-studio-classic-jobs"></a>Azure Machine Learning Studio (klasszikus) feladatok Azure Batch szolgáltatása

Machine Learning batch-készlet feldolgozása az ügyfél által felügyelt méretezést biztosít a Azure Machine Learning batch-végrehajtási szolgáltatáshoz. A gépi tanuláshoz készült klasszikus kötegelt feldolgozás több-bérlős környezetben történik, amely korlátozza az egyidejű feladatok számát, és a feladatokat a rendszer első alkalommal kivezeti a várólistára. Ez a bizonytalanság azt jelenti, hogy a feladatok futtatásakor nem lehet pontosan előre jelezni.

A Batch-készlet feldolgozása lehetővé teszi, hogy olyan készleteket hozzon létre, amelyeken kötegelt feladatokat küldhet. Szabályozhatja a készlet méretét, valamint azt, hogy a rendszer melyik készletre küldi el a feladatot. A BES-feladatok a saját feldolgozási területén futnak, és kiszámítható feldolgozási teljesítményt biztosítanak, valamint lehetővé teszik a beküldött feldolgozási terhelésnek megfelelő erőforráskészlet létrehozását.

> [!NOTE]
> Készlet létrehozásához új Resource Manager-alapú Machine Learning webszolgáltatást kell létrehoznia. A létrehozást követően bármelyik BES webszolgáltatást futtathatja, a készleten pedig új Resource Manager-alapú és klasszikus is.

## <a name="how-to-use-batch-pool-processing"></a>A Batch-készlet feldolgozásának használata

A Batch-készlet feldolgozásának konfigurálása jelenleg nem érhető el a Azure Portalon keresztül. A Batch-készlet feldolgozásának használatához a következőket kell tennie:

-   CSS hívása batch-fiók létrehozásához és a készlet szolgáltatás URL-címének és engedélyezési kulcsának beszerzéséhez
-   Új Resource Manager-alapú webszolgáltatás és számlázási csomag létrehozása

A fiók létrehozásához hívja meg a Microsoft ügyfél-és támogatási szolgálatát (CSS), és adja meg az előfizetés-AZONOSÍTÓját. A CSS együttműködik Önnel, hogy meghatározza a forgatókönyvnek megfelelő kapacitást. A CSS ezután konfigurálja a fiókját a létrehozható készletek maximális száma és az egyes készletekben elhelyezhető virtuális gépek maximális száma (VM) használatával. A fiók konfigurálása után a készlet szolgáltatás URL-címét és egy engedélyezési kulcsot kell megadnia.

A fiók létrehozása után a készlet szolgáltatás URL-címét és az engedélyezési kulcsot használhatja a készlet-felügyeleti műveletek végrehajtásához a Batch-készleten.

![A Batch Pool szolgáltatás architektúrája.](./media/dedicated-capacity-for-bes-jobs/pool-architecture.png)

A készletek létrehozásához hívja meg a készlet létrehozása műveletet a készlet szolgáltatás URL-címére, amelyet a CSS biztosít Önnek. Készlet létrehozásakor megadhatja a virtuális gépek számát és az új Resource Manager-alapú Machine Learning webszolgáltatás hencegő. JSON fájljának URL-címét. Ez a webszolgáltatás a számlázási társítás létrehozásához van megadva. A Batch-készlet szolgáltatás a henceg. JSON használatával rendeli hozzá a készletet egy számlázási tervhez. Futtathat bármely BES-webszolgáltatást, amely a készleten új Resource Manager-alapú és klasszikus is lehet.

Használhat bármely új Resource Manager-alapú webszolgáltatást, de vegye figyelembe, hogy a feladatok számlázása az adott szolgáltatáshoz társított számlázási terv alapján történik. Létrehozhat egy webszolgáltatást és egy új számlázási tervet, amely kifejezetten a Batch Pool-feladatok futtatására szolgál.

A webszolgáltatások létrehozásával kapcsolatos további információkért lásd: [Azure Machine learning webszolgáltatás üzembe helyezése](deploy-a-machine-learning-web-service.md).

A készlet létrehozása után elküldheti a BES feladatot a webszolgáltatás batch-kérelmek URL-címével. Dönthet úgy is, hogy egy készletbe vagy egy klasszikus kötegelt feldolgozásba küldi. A Batch-készlet feldolgozására vonatkozó feladatok elküldéséhez adja hozzá a következő paramétert a beküldési kérelem törzséhez:

"AzureBatchPoolId": "&lt;készlet azonosítója&gt;"

Ha nem adja hozzá a paramétert, a rendszer futtatja a feladatot a klasszikus kötegelt feldolgozási környezetben. Ha a készlet rendelkezik rendelkezésre álló erőforrásokkal, a rendszer azonnal elindítja a feladatot. Ha a készlet nem rendelkezik szabad erőforrásokkal, a rendszer várólistára helyezi a feladatot, amíg az erőforrás elérhetővé nem válik.

Ha azt tapasztalja, hogy rendszeresen eléri a készletek kapacitását, és nagyobb kapacitásra van szüksége, meghívhatja a CSS-t, és a kvóták növeléséhez használhatja a képviselőt.

Példa kérésre:

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

## <a name="considerations-when-using-batch-pool-processing"></a>Szempontok a Batch-készlet feldolgozásakor

A Batch-készlet feldolgozása egy mindig a számlázható szolgáltatás, amely megköveteli, hogy egy Resource Manager-alapú számlázási csomaggal társítsa. Csak a készlet által futtatott számítási órák száma után kell fizetnie. az adott időkészletben futtatott feladatok számától függetlenül. Ha létrehoz egy készletet, a készletben lévő egyes virtuális gépek számítási óráját a készlet törlése után számítjuk fel, még akkor is, ha a készletben nem futnak batch-feladatok. A virtuális gépek számlázása akkor kezdődik el, amikor befejeződött az üzembe helyezés, és a törlésük után leáll. A [Machine learning díjszabási oldalán](https://azure.microsoft.com/pricing/details/machine-learning/)található csomagok bármelyikét használhatja.

Számlázási példa:

Ha 2 virtuális géppel rendelkező batch-készletet hoz létre, és 24 óra elteltével törli azt, akkor a számlázási terv 48 számítási órát számol fel; attól függetlenül, hogy hány feladatot futtattak az adott időszakban.

Ha 4 virtuális géppel rendelkező batch-készletet hoz létre, és 12 óra elteltével törli azt, akkor a számlázási terv 48 számítási órát is felszámol.

Javasoljuk, hogy a feladatok befejezésének megkezdéséhez kérdezze le a feladat állapotát. Ha az összes feladat futása befejeződött, hívja meg a készlet átméretezése műveletet a készletben lévő virtuális gépek számának nulla értékre állításához. Ha kevés a készlet erőforrásai, és létre kell hoznia egy új készletet, például egy másik számlázási csomag alapján, akkor törölheti a készletet, ha az összes feladat futása befejeződött.


| **Batch-készlet feldolgozásának használata, ha**    | **Klasszikus kötegelt feldolgozás használata, ha**  |
|---|---|
|Nagy mennyiségű feladatot kell futtatnia<br>Vagy<br/>Tudnia kell, hogy a feladatok azonnal le lesznek futtatva<br/>Vagy<br/>Garantált átviteli sebességre van szükség. Például egy adott időkereten belül több feladatot kell futtatnia, és a számítási erőforrásokat szeretné kibővíteni az igényeinek megfelelően.    | Csak néhány feladatot futtat<br/>And<br/> Nincs szükség a feladatok azonnali futtatására |
