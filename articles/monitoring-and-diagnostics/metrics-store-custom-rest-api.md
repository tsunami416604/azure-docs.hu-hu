---
title: Egyéni metrikák az Azure Monitor mérőszámhoz egy Azure-beli erőforráshoz tárolásához, egy REST API-val küldése
description: Egyéni metrikák az Azure Monitor mérőszámhoz egy Azure-beli erőforráshoz tárolásához, egy REST API-val küldése
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: c01440437eae3cb076627ab9f2221e33b833f472
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46977236"
---
# <a name="send-custom-metrics-for-an-azure-resource-to-the-azure-monitor-metric-store-using-a-rest-api"></a>Egyéni metrikák az Azure Monitor mérőszámhoz egy Azure-beli erőforráshoz tárolásához, egy REST API-val küldése

Ez a cikk bemutatja, hogyan küldhet egyéni metrikákat az Azure-erőforrásokhoz a Azure Monitor-metrikák tároló egy REST API-n keresztül.  Miután a metrikák az Azure monitorban, azokat a standard szintű mérőszámokat, például a diagramkészítési, riasztások, Útválasztás őket a más külső eszközök érheti el az összes a műveleteket végezheti el.  

>[!NOTE] 
>A REST API-t csak lehetővé teszi az Azure-erőforrások egyéni mérőszámok küldése. A különböző környezetek vagy a helyszíni erőforrások mérőszámait elküldéséhez használhatja [Application Insights](../application-insights/app-insights-api-custom-events-metrics.md).    


## <a name="create-and-authorize-a-service-principal-to-emit-metrics"></a>Hozzon létre, és engedélyezze a mérőszámok kibocsátható egy egyszerű szolgáltatást 

Egy egyszerű szolgáltatásnév létrehozása az Azure Active Directory-bérlőben címen található utasításokat követve [egyszerű szolgáltatás létrehozása](../azure-resource-manager/resource-group-create-service-principal-portal.md). 

Megjegyzés: a folyamat során a következő: 

- A bejelentkezési URL-címéhez helyezheti bármely URL-címben.  
- Új titkos ügyfélkulcsot az alkalmazás létrehozása  
- Mentse a kulcsot, és használja az ügyfél-Azonosítóját a későbbi lépésekben.  

Adjon meg az alkalmazáshoz, lépés részeként létrehozta a kívánt gridre bocsáthatja ki az metrikákat az erőforrás 1 "Figyelési metrikákat közzétevő" engedéllyel. Ha azt tervezi, az alkalmazás használatát, gridre bocsáthatja ki az egyéni metrikákat az erőforrások száma, adja meg ezeket az engedélyeket az erőforrás-csoportba vagy előfizetésbe szinten. 

## <a name="get-an-authorization-token"></a>Egy engedélyezési jogkivonat beolvasása
Nyisson meg egy parancssort, és futtassa a következő parancsot
```shell
curl -X POST https://login.microsoftonline.com/<yourtenantid>/oauth2/token -F "grant_type=client_credentials" -F "client_id=<insert clientId from earlier step> " -F "client_secret=<insert client secret from earlier step>" -F "resource=https://monitoring.azure.com/"
```
Mentse a hozzáférési jogkivonatot a válaszból

![Hozzáférési jogkivonat](./media/metrics-store-custom-rest-api/accesstoken.png)

## <a name="emit-the-metric-via-the-rest-api"></a>A metrika a REST API-n keresztül küldik. 

1. Illessze be a következő JSON-fájlba, és mentse a helyi számítógépen custommetric.json. Frissítse a idő paramétert a JSON-fájlt. 
    
    ```json
    { 
        "time": "2018-09-13T16:34:20”, 
        "data": { 
            "baseData": { 
                "metric": "QueueDepth", 
                "namespace": "QueueProcessing", 
                "dimNames": [ 
                  "QueueName", 
                  "MessageType" 
                ], 
                "series": [ 
                  { 
                    "dimValues": [ 
                      "ImagesToProcess", 
                      "JPEG" 
                    ], 
                    "min": 3, 
                    "max": 20, 
                    "sum": 28, 
                    "count": 3 
                  } 
                ] 
            } 
        } 
    } 
    ``` 

1. A parancssori ablakban a metrikaadatok közzététele 
    - Az Azure-régióban – meg kell egyeznie az erőforrás metrikáit, vannak kibocsátó a telepítési régió. 
    - Erőforrás-azonosító – erőforrás-azonosító az Azure-erőforrás a mutatószám nyomon követ.  
    - Hozzáférési tokent – illessze be a korábban megszerzett jogkivonattal

    ```Shell 
    curl -X POST curl -X POST https://<azureRegion>.monitoring.azure.com/<resourceId> /metrics -H "Content-Type: application/json" -H "Authorization: Bearer <AccessToken>" -d @custommetric.json 
    ```
1. Módosítsa a timestamp és a JSON-fájlban szereplő értékek. 
1. Ismételje meg az előző két lépést néhány alkalommal adatokat, így több percig.

## <a name="troubleshooting"></a>Hibaelhárítás 
Ha hiba lép fel a folyamat néhány részét kapja, figyelembe véve a következő

1. Metrikákat az egy előfizetésen vagy erőforráscsoporton, az Azure-erőforrás nem adható ki. 
1. A tárolóba, amely több mint 20 perc régi nem helyezhető el egy metrikát. A metrika tároló riasztás és valós idejű diagramkészítési van optimalizálva. 
2. Hány dimenzió neve meg kell egyeznie az értékeket, és fordítva. Ellenőrizze az értékeket. 
2. Előfordulhat, hogy a régióban, amely nem támogatja az egyéni metrikákat metrikákat kibocsátó. Lásd: [támogatott régiók egyéni metrika (előzetes verzió)](metrics-custom-overview.md#supported-regions) 



## <a name="view-your-metrics"></a>A metrikák megtekintése 

1. Bejelentkezés az Azure Portalra 

1. A bal oldali menüben kattintson a **figyelője** 

1. A figyelő lapon kattintson a **metrikák**. 

   ![Hozzáférési jogkivonat](./media/metrics-store-custom-rest-api/metrics.png) 

1. Az összesítési időszak módosításához **elmúlt 30 percben**.  

1. Az a *erőforrás* legördülő menüben válassza ki az erőforrást, a mérőszám alapján kibocsátott.  

1. Az a *névterek* legördülő menüben válassza **QueueProcessing** 

1. Az a *metrikák* válassza ki a legördülő lista **QueueDepth**.  

 
## <a name="next-steps"></a>További lépések
- Tudjon meg többet [egyéni metrikákat](metrics-custom-overview.md).