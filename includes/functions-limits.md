---
author: ggailey777
ms.service: cost-management-billing
ms.topic: include
ms.date: 05/09/2019
ms.author: glenga
ms.openlocfilehash: 4643bb7f95e4fd1249d3ab6699c1f835c77f18fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77198343"
---
| Erőforrás | [Felhasználási terv](../articles/azure-functions/functions-scale.md#consumption-plan) | [Prémium szintű csomag](../articles/azure-functions/functions-scale.md#premium-plan) | [App Szolgáltatási csomag](../articles/azure-functions/functions-scale.md#app-service-plan)<sup>1</sup> |
| --- | --- | --- | --- |
| Horizontális felskálázás | Eseményvezérelt | Eseményvezérelt | [Manuális/automatikus skálázás](../articles/app-service/manage-scale-up.md) | 
| Példányok maximális | 200 | 100 | 10-20 |
|Alapértelmezett [idő-túlóra időtartama](../articles/azure-functions/functions-scale.md#timeout) (min) |5 | 30 |30<sup>2</sup> |
|Maximális [időout-időtartam](../articles/azure-functions/functions-scale.md#timeout) (perc) |10 | határtalan<sup>8</sup> | határtalan<sup>3</sup> |
| Kimenő kapcsolatok maximális száma (példányonként) | 600 aktív (összesen 1200) | Korlátos | Korlátos |
| A kérelem maximális mérete (MB)<sup>4</sup> | 100 | 100 | 100 |
| Lekérdezési karakterlánc maximális hossza<sup>4</sup> | 4096 | 4096 | 4096 |
| A kérelem maximális URL-címének hossza<sup>4</sup> | 8192 | 8192 | 8192 |
| [ACU](../articles/virtual-machines/windows/acu.md) példányonként | 100 | 210-840 | 100-840 |
| Maximális memória (GB példányonként) | 1.5 | 3.5-14 | 1.75-14 |
| Funkcióalkalmazások csomagonként |100 |100 |határtalan<sup>5</sup> |
| [App Service-csomagok](../articles/app-service/overview-hosting-plans.md) | 100 [régiónként](https://azure.microsoft.com/global-infrastructure/regions/) |100 erőforráscsoportonként |100 erőforráscsoportonként |
| <sup>6.</sup> tároló |1 GB |250 GB |50-1000 GB |
| Egyéni tartományok alkalmazásonként</a> |500<sup>7</sup> |500 |500 |
| Egyéni tartomány [SSL-támogatása](../articles/app-service/configure-ssl-bindings.md) |kötött SNI SSL-kapcsolat | határtalan SNI SSL és 1 IP SSL kapcsolat |határtalan SNI SSL és 1 IP SSL kapcsolat | 

<sup>1</sup> A különböző App Service-csomag-beállítások ra vonatkozó konkrét korlátozásokat az App Service-csomag korlátai című témakörben [téssz.](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)  
<sup>2</sup> Alapértelmezés szerint a Functions 1.x futásidejű időbeli meghosszabbítása egy App Service-csomagban nincs korlátozás alatt.  
<sup>3</sup> Az App Service-csomag beállítása [Mindig be van kapcsolva](../articles/azure-functions/functions-scale.md#always-on). Fizessen standard [áron](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4</sup> Ezek a korlátok [vannak beállítva a fogadó](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config).  
<sup>5</sup> A ténylegesen üzemeltethető függvényalkalmazások száma az alkalmazások tevékenységétektől, a géppéldányok méretétől és a megfelelő erőforrás-kihasználtságtól függ.  
<sup>6</sup> A tárolási korlát az ideiglenes tárolás teljes tartalommérete az azonos App Service-csomagösszes alkalmazásában. A felhasználási csomag az Azure Files-t használja az ideiglenes tároláshoz.  
<sup>7</sup> Ha a függvényalkalmazás [egy felhasználási csomagban](../articles/azure-functions/functions-scale.md#consumption-plan)van tárolva, csak a CNAME beállítás támogatott. [Prémium csomagban](../articles/azure-functions/functions-scale.md#premium-plan) vagy App [Service-csomagban](../articles/azure-functions/functions-scale.md#app-service-plan)lévő függvényalkalmazások esetén egyéni tartományt cname vagy A rekord használatával is leképezhet.  
<sup>8</sup> Garantált akár 60 percig.