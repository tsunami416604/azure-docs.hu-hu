---
title: Egyéni metrikák az Azure-beli erőforráshoz küldeni az Azure Monitor metrika tároló egy REST API-val
description: Egyéni metrikák az Azure-beli erőforráshoz küldeni az Azure Monitor metrika tároló egy REST API-val
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: ed810726a0709c80034412eba437c05e76f65758
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54460379"
---
# <a name="send-custom-metrics-for-an-azure-resource-to-the-azure-monitor-metric-store-by-using-a-rest-api"></a>Egyéni metrikák az Azure-beli erőforráshoz küldeni az Azure Monitor metrika tároló egy REST API-val

Ez a cikk bemutatja, hogyan küldhet egyéni metrikákat az Azure-erőforrásokhoz a Azure Monitor-metrikák tároló egy REST API-n keresztül. Után a metrikák az Azure monitorban, egy, a standard metrikák érheti el az összes a műveleteket végezheti el. Példák vannak diagramkészítési, riasztás és az Útválasztás azokat más külső eszközök.  

>[!NOTE]  
>A REST API-t csak lehetővé teszi az Azure-erőforrások egyéni mérőszámok küldése. A különböző környezetek vagy a helyszíni erőforrások mérőszámait elküldéséhez használhatja [Application Insights](../../azure-monitor/app/api-custom-events-metrics.md).    


## <a name="create-and-authorize-a-service-principal-to-emit-metrics"></a>Hozzon létre, és engedélyezze a mérőszámok kibocsátható egy egyszerű szolgáltatást 

Egyszerű szolgáltatás létrehozása az Azure Active Directory-bérlőben található útmutatót [egyszerű szolgáltatás létrehozása](../../active-directory/develop/howto-create-service-principal-portal.md). 

Amíg ez a folyamat lépésein, vegye figyelembe a következőket: 

- Bármely URL-cím is megadhatja a bejelentkezési URL-címéhez.  
- Hozzon létre egy új ügyfél az alkalmazáshoz tartozó titkos kulcsot.  
- Mentse a kulcsot, és használja az ügyfél-Azonosítóját a későbbi lépésekben.  

Adja meg az 1, figyelési metrikákat közzétevő, az erőforrás kívánt gridre bocsáthatja ki az metrikákat az engedélyek részeként létrehozott alkalmazást. Ha azt tervezi, az alkalmazás használatát, gridre bocsáthatja ki az egyéni metrikákat az erőforrások száma, adja meg ezeket az engedélyeket az erőforrás-csoportba vagy előfizetésbe szinten. 

## <a name="get-an-authorization-token"></a>Egy engedélyezési jogkivonat beolvasása
Nyisson meg egy parancssort, és futtassa a következő parancsot:

```shell
curl -X POST https://login.microsoftonline.com/<yourtenantid>/oauth2/token -F "grant_type=client_credentials" -F "client_id=<insert clientId from earlier step> " -F "client_secret=<insert client secret from earlier step>" -F "resource=https://monitoring.azure.com/"
```
Mentse a hozzáférési jogkivonatot a válaszból.

![Hozzáférési jogkivonat](./media/metrics-store-custom-rest-api/accesstoken.png)

## <a name="emit-the-metric-via-the-rest-api"></a>A metrika a REST API-n keresztül küldik. 

1. Illessze be a következő JSON-fájlba, és mentse a fájt **custommetric.json** a helyi számítógépen. Frissítse a idő paramétert a JSON-fájlt: 
    
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

1. A parancssori ablakban a metrikaadatok közzététele: 
    - **azureRegion**. Meg kell egyeznie az erőforrás metrikáit, Ön kibocsátó a telepítési régió. 
    - **erőforrás-azonosító**.  Erőforrás-azonosító az Azure-erőforrás a mutatószám követi nyomon.  
    - **Hozzáférési tokent**. Illessze be a jogkivonatot, korábban beszerzett.

    ```Shell 
    curl -X POST curl -X POST https://<azureRegion>.monitoring.azure.com/<resourceId>/metrics -H "Content-Type: application/json" -H "Authorization: Bearer <AccessToken>" -d @custommetric.json 
    ```
1. Módosítsa a timestamp és a JSON-fájlban szereplő értékek. 
1. Ismételje meg az előző két lépést néhány alkalommal, így adatokat kell néhány percig.

## <a name="troubleshooting"></a>Hibaelhárítás 
Ha a folyamat néhány részét tartalmazó hibaüzenet kap, vegye figyelembe a következő hibaelhárítási információk:

1. Metrikákat az egy előfizetésen vagy erőforráscsoporton, az Azure-erőforrás nem adható ki. 
1. A tárolóba, amely több mint 20 perc régi nem helyezhető el egy metrikát. A metrika tároló riasztás és valós idejű diagramkészítési van optimalizálva. 
2. Hány dimenzió neve meg kell egyeznie az értékeket, és ez fordítva is igaz. Ellenőrizze az értékeket. 
2. Előfordulhat, hogy metrikákat az egy régióban, amely nem támogatja az egyéni metrikákat kibocsátó. Lásd: [támogatott régiók](../../azure-monitor/platform/metrics-custom-overview.md#supported-regions). 



## <a name="view-your-metrics"></a>A metrikák megtekintése 

1. Jelentkezzen be az Azure portálra. 

1. A bal oldali menüben válassza ki a **figyelő**. 

1. Az a **figyelő** lapon jelölje be **metrikák**. 

   ![Metrikák kiválasztása](./media/metrics-store-custom-rest-api/metrics.png) 

1. Az összesítési időszak módosításához **elmúlt 30 percben**.  

1. Az a **erőforrás** legördülő menüben válassza ki az erőforrást, a mérőszám alapján kibocsátott.  

1. Az a **névterek** legördülő menüjében válassza **QueueProcessing**. 

1. Az a **metrikák** legördülő menüjében válassza **QueueDepth**.  

 
## <a name="next-steps"></a>További lépések
- Tudjon meg többet [egyéni metrikákat](../../azure-monitor/platform/metrics-custom-overview.md).

