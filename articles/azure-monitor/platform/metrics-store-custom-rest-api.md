---
title: Metrikák küldése az Azure Monitor metrikaadatbázisba rest API használatával
description: Egyéni metrikák küldése egy Azure-erőforráshoz az Azure Monitor metrikatárolóba rest API használatával
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 84709c022631543101889f784231158ebb96b6f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662264"
---
# <a name="send-custom-metrics-for-an-azure-resource-to-the-azure-monitor-metric-store-by-using-a-rest-api"></a>Egyéni metrikák küldése egy Azure-erőforráshoz az Azure Monitor metrikatárolóba rest API használatával

Ez a cikk bemutatja, hogyan küldhet egyéni metrikákat az Azure-erőforrások az Azure Monitor metrikák tárolására egy REST API-n keresztül. Miután a metrikák az Azure Monitorban vannak, mindent elérhet velük, amit a szabványos metrikákkal végez. Ilyenek például a diagramkészítés, a riasztás és az átirányítás más külső eszközökre.  

>[!NOTE]  
>A REST API csak az Azure-erőforrások egyéni metrikák küldését teszi lehetővé. Metrikák küldéséhez az erőforrások különböző környezetekben vagy a helyszínen, használhatja [az Application Insights.](../../azure-monitor/app/api-custom-events-metrics.md)    


## <a name="create-and-authorize-a-service-principal-to-emit-metrics"></a>Metrikák at bocsátmeg és engedélyezhet egy egyszerű szolgáltatást 

Hozzon létre egy egyszerű szolgáltatás az Azure Active Directory-bérlőben az egyszerű [szolgáltatás létrehozása](../../active-directory/develop/howto-create-service-principal-portal.md)című részben található utasítások használatával. 

A folyamat során vegye figyelembe a következőket: 

- A bejelentkezési URL-címhez bármilyen URL-címet megadhat.  
- Hozzon létre egy új ügyféltitkot ehhez az alkalmazáshoz.  
- Mentse a kulcsot és az ügyfélazonosítót későbbi lépésekben való használatra.  

Adja meg az 1. Ha azt tervezi, hogy az alkalmazás segítségével egyéni metrikákat bocsát ki számos erőforrással szemben, ezeket az engedélyeket az erőforráscsoport vagy az előfizetés szintjén adhatja meg. 

## <a name="get-an-authorization-token"></a>Engedélyezési jogkivonat beszereznie
Nyisson meg egy parancssort, és futtassa a következő parancsot:

```shell
curl -X POST https://login.microsoftonline.com/<yourtenantid>/oauth2/token -F "grant_type=client_credentials" -F "client_id=<insert clientId from earlier step>" -F "client_secret=<insert client secret from earlier step>" -F "resource=https://monitoring.azure.com/"
```
Mentse a hozzáférési jogkivonatot a válaszból.

![Hozzáférési jogkivonat](./media/metrics-store-custom-rest-api/accesstoken.png)

## <a name="emit-the-metric-via-the-rest-api"></a>A metrika kitöltése a REST API-n keresztül 

1. Illessze be a következő JSON-t egy fájlba, és mentse a helyi számítógépre **custommetric.json néven.** Frissítse az időparamétert a JSON-fájlban: 
    
    ```json
    { 
        "time": "2018-09-13T16:34:20", 
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

1. A parancssorablakban tegye közzé a metrikaadatokat: 
   - **azureRegion .** Meg kell egyeznie annak az erőforrásnak a telepítési régiójával, amelyhez metrikákat bocsát ki. 
   - **resourceID**azonosítót.  Az Azure-erőforrás erőforrás-azonosítója, amelyhez a metrikát nyomon követi.  
   - **AccessToken**. Illessze be a korábban beszerzett jogkivonatot.

     ```Shell 
     curl -X POST https://<azureRegion>.monitoring.azure.com/<resourceId>/metrics -H "Content-Type: application/json" -H "Authorization: Bearer <AccessToken>" -d @custommetric.json 
     ```
1. Módosítsa az időbélyeget és az értékeket a JSON-fájlban. 
1. Ismételje meg az előző két lépést néhányszor, hogy több percig is legyenek adatai.

## <a name="troubleshooting"></a>Hibaelhárítás 
Ha a folyamat egy részével kapcsolatban hibaüzenet jelenik meg, vegye figyelembe az alábbi hibaelhárítási információkat:

1. Azure-erőforrásként nem adhat ki metrikákat egy előfizetéssel vagy erőforráscsoporttal szemben. 
1. Nem helyezhet el több mint 20 perces mérőszámot a boltban. A metrikatároló riasztásra és valós idejű diagramkészítésre van optimalizálva. 
2. A dimenziónevek számának meg kell egyeznie az értékekkel, és fordítva. Ellenőrizze az értékeket. 
2. Előfordulhat, hogy olyan régióval szemben bocsát ki metrikákat, amelyek nem támogatják az egyéni mutatókat. Lásd: [Támogatott régiók](../../azure-monitor/platform/metrics-custom-overview.md#supported-regions). 



## <a name="view-your-metrics"></a>A mérőszámok megtekintése 

1. Jelentkezzen be az Azure portálra. 

1. A bal oldali menüben válassza a **Monitor**lehetőséget. 

1. A **Figyelő** lapon válassza a **Metrikák**lehetőséget. 

   ![Mérőszámok kiválasztása](./media/metrics-store-custom-rest-api/metrics.png) 

1. Módosítsa az összesítési időszakot **utolsó 30 percre.**  

1. Az **erőforrás** legördülő menüben válassza ki azt az erőforrást, amelynek a metrikát kibocsátotta.  

1. A **névterek** legördülő menüben válassza a **QueueProcessing (QueueProcessing) menüt.** 

1. A **metrikák** legördülő menüben válassza a **Várólistamélység**lehetőséget.  

 
## <a name="next-steps"></a>További lépések
- További információ az [egyéni mutatókról.](../../azure-monitor/platform/metrics-custom-overview.md)

