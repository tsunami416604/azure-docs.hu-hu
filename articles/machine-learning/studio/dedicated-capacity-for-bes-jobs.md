---
title: "A Machine Learning kötegelt végrehajtási szolgáltatás feladatok kapacitás dedikált |} Microsoft Docs"
description: "A gépi tanulási feladatok Azure Batch-szolgáltatások áttekintése."
services: machine-learning
documentationcenter: 
author: garyericson
manager: raymondl
editor: 
ms.service: machine-learning
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: raymondl
ms.openlocfilehash: 4a4c5e6bf44fb4774d9ba501479383d6c7d3b128
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="azure-batch-service-for-machine-learning-jobs"></a>A gépi tanulási feladatok az Azure Batch szolgáltatás

Gépi tanulás Batch-készlet feldolgozása ügyfél által felügyelt méretezési biztosít az Azure Machine Learning kötegelt végrehajtási szolgáltatás. Klasszikus kötegelt feldolgozása gépi tanulás kerül sor, több-bérlős környezetben, amely korlátozza az egyidejűleg futó feladatainak számát akkor is elküldhetik, és a feladatok várólistára első-first out alapon. Ez a bizonytalanság azt jelenti, hogy Ön nem pontosan előre jelezni mikor fog futni a feladat.

Készlet kötegfeldolgozási hozhat létre készleteket, amelyre elküldheti a kötegelt feladatok. Szabályozhatja a készlet méretét, és mely készletbe a feladat elküldése megtörtént. A BES feladat fut, a saját feldolgozási terület előre jelezhető feldolgozás és képes létrehozni a feldolgozási terhelés elküldött megfelelő erőforráskészletek biztosítása.

## <a name="how-to-use-batch-pool-processing"></a>Batch-készlet feldolgozási használata

A Batch-készlet feldolgozási konfigurációs már nem érhető el az Azure portálon keresztül. Batch-készlet feldolgozási használatához az alábbiak szükségesek:

-   Készlet Batch-fiók létrehozása, és szerezze be a készlet szolgáltatás URL-címe és engedélykulcs CSS hívása
-   Hozzon létre egy új erőforrás-kezelő alapú webszolgáltatás és a számlázási csomag

A fiók létrehozásához, hívja a Microsoft ügyfélszolgálata és a támogatási szolgálathoz (CSS), és adja meg az előfizetés-azonosító. CSS Önnel együttműködve helyzetnek megfelelő kapacitásának meghatározásához. CSS ezután konfigurálja a fiókot hozhat létre készleteket maximális számát és a virtuális gépek (VM) elhelyezheti mindegyik készlet maximális számát. A fiók van konfigurálva, ha rendelkezésre állnak a készlet szolgáltatás URL-címe és engedélykulcs.

A fiók létrehozását követően a készlet szolgáltatás URL-címe és a hitelesítési kulcs használatával készlet felügyeleti műveleteket a Batch-készlet.

![Kötegelt készlet szolgáltatás architektúrája.](./media/dedicated-capacity-for-bes-jobs/pool-architecture.png)

A készlet szolgáltatás URL-címe, amely az Ön számára biztosított CSS-készlet létrehozása művelet hívása készletek létrehozásához. Készletet hoz létre, amikor adja meg, hány virtuális gépek és a swagger.json egy új Resource Manager URL-Machine Learning webszolgáltatáshoz. Ennek a webszolgáltatásnak a számlázási társítás létrehozásához valósul meg. A Batch-készlet szolgáltatás a swagger.json használ a tárolókészlet társítsa egy számlázási csomagot. Futtathatja a BES webszolgáltatás, mindkét alapú új erőforrás-kezelő és a klasszikus, a készlet.

Bármely új Resource Manager-alapú webszolgáltatás, de vegye figyelembe, hogy a feladatokhoz tartozó számlázási terhére-e a szolgáltatáshoz tartozó számlázási csomagot. Érdemes lehet egy webszolgáltatás és az új számlázási csomag kifejezetten a futó feladatok Batch-készlet létrehozásához.

Webszolgáltatások létrehozásával kapcsolatos további információkért lásd: [központi telepítése az Azure Machine Learning webszolgáltatás](publish-a-machine-learning-web-service.md).

Miután létrehozta a készletben, a a kötegelt kérelem URL-cím segítségével a webszolgáltatás BES feladat elküldése. Dönthet úgy, hogy küldje el a készlethez, vagy a klasszikus kötegfeldolgozási. A Batch-készlet feldolgozása a feladat elküldése, a következő paraméter a feladat elküldése kérelemtörzset hozzáadhat:

"AzureBatchPoolId":"&lt;pool ID&gt;"

Ha nem adja hozzá a paramétert, a feladat a klasszikus kötegelt folyamat-környezetében futtatható. A készlet elegendő erőforrás áll rendelkezésre, ha a a feladat futtatása azonnal elindul. A készlet nem rendelkezik szabad erőforrást, ha a feladat várólistára van állítva, amíg egy erőforrás áll rendelkezésre.

Ha talál meg, hogy rendszeresen eléri a készletek kapacitását, és nagyobb kapacitást van szüksége, CSS hívja, és növelje a kvóták képviselője dolgozni.

Példa egy kérelem:

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

Batch-készlet feldolgozásakor – a számlázható szolgáltatás pedig, amelyhez Ön társítandó Resource Manageren alapul számlázási csomagot. A készlet fut; számítási órák számát csak számlázása függetlenül a feladatok futnak a készlethez tartozó idő során. Készletet hoz létre, ha kell fizetni az egyes virtuális gépek a készlet számítási órában a készlet nem törlik, még akkor is, ha nincsenek kötegelt feladatok futnak a készletben. A virtuális gépek számlázási akkor kezdődik, amikor a kiépítés után, és leáll, amikor törölve lettek. Használhatja a sémák megtalálható-e a [Machine Learning díjszabás lap](https://azure.microsoft.com/pricing/details/machine-learning/).

Számlázási. példa:

Ha a Batch-készlet létrehozása 2 virtuális gépekkel és 24 óra múlva törli azt a számlázási csomag terhelt számítási 48 óra; függetlenül attól, hogy hány feladat futtatná adott időszakban.

Ha a Batch-készlet létrehozása 4 virtuális gépekkel és 12 óra elteltével törölje, a számlázási csomag is számítási terhelést 48 óra.

Azt javasoljuk, hogy a feladatok teljes meghatározásához a feladatállapot lekérdezésére. Ha a feladatok futása befejeződött, hívja meg a készlet átméretezése műveletet a virtuális gépek számának beállítása a tárolókészlet nulla. Ha rövid készlet erőforrások és kell létrehoznia egy új készletet, például egy másik számlázási csomag elleni számlázási törölheti a készlet helyette ha összes feladat futása befejeződött.


| **Használja a kötegelt mikor feldolgozása**    | **Használja a klasszikus kötegfeldolgozási végezhető, ha**  |
|---|---|
|Nagyszámú feladatok futtatásához szükséges<br>Vagy<br/>A feladatok azonnal elindul tudnia kell<br/>Vagy<br/>Garantált átviteli van szüksége. Például szeretné egy adott időszakon feladatok száma futnak, és szeretné, hogy a számítási erőforrásokat, az igényeknek horizontális.    | Néhány feladatot futtat<br/>És<br/> Nem szükséges azonnal futtatni a feladatot |
