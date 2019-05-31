---
author: ggailey777
ms.service: billing
ms.topic: include
ms.date: 05/09/2019
ms.author: glenga
ms.openlocfilehash: 8f30d9fb2fcfe8f55af13d7726aa8458f8733b3f
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236016"
---
| Resource | [Használatalapú csomag](../articles/azure-functions/functions-scale.md#consumption-plan) | [Prémium szintű csomag](../articles/azure-functions/functions-scale.md#premium-plan-public-preview) | [App Service-csomag](../articles/azure-functions/functions-scale.md#app-service-plan)<sup>1</sup> |
| --- | --- | --- | --- |
| Horizontális felskálázás | Eseményvezérelt | Eseményvezérelt | [Manuális vagy automatikus skálázás](../articles/app-service/web-sites-scale.md) | 
|Alapértelmezett [időtúllépési időtartam](../articles/azure-functions/functions-scale.md#timeout) (min.) |5 | 30 |30<sup>2</sup> |
|Maximális [időtúllépési időtartam](../articles/azure-functions/functions-scale.md#timeout) (min.) |10 | korlátlan streameken működő | korlátlan streameken működő<sup>3</sup> |
| Kimenő kapcsolatok maximális száma (példányonként) | 600 aktív (összesen 1200-as) | korlátlan streameken működő | korlátlan streameken működő |
| Maximális kérés mérete (MB)<sup>4</sup> | 100 | 100 | 100 |
| Maximális lekérdezési karakterlánc hosszának<sup>4</sup> | 4096 | 4096 | 4096 |
| Kérelem URL-cím legfeljebb<sup>4</sup> | 8192 | 8192 | 8192 |
| [ACU](../articles/virtual-machines/windows/acu.md) példányonként | 100 | 210-840 | 100-840 |
| Maximális memória (GB / példány) | 1.5 | 3.5-14 | 1.75-14 |
| Függvényalkalmazások jén |100 |100 |korlátlan streameken működő<sup>5</sup> |
| [App Service-csomagok](../articles/app-service/overview-hosting-plans.md) | 100 / [régió](https://azure.microsoft.com/global-infrastructure/regions/) |az adott erőforráscsoport esetében 100 |az adott erőforráscsoport esetében 100 |
| Storage<sup>6</sup> |1 GB |250 GB |50-1000 GB |
| Egyéni tartományok alkalmazásonként</a> |500<sup>7</sup> |500 |500 |
| Egyéni tartomány [SSL-támogatás](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |Nem támogatott, a helyettesítő tanúsítványt *. azurewebsites.net alapértelmezés szerint elérhető| korlátlan streameken működő SNI SSL és 1 IP SSL-kapcsolatot tartalmaz |korlátlan streameken működő SNI SSL és 1 IP SSL-kapcsolatot tartalmaz | 

<sup>1</sup>adott korlátait a különféle App Service-csomagokkal, tekintse meg a [App Service-csomag korlátok](../articles/azure-subscription-service-limits.md#app-service-limits).  
<sup>2</sup>alapértelmezés szerint az a funkciók 1.x modul az App Service-csomag időtúllépés a korlátlan streameken működő.  
<sup>3</sup>használatához az App Service-csomag lehet [Always On](../articles/azure-functions/functions-scale.md#always-on). Standard megfizeti [díjszabás](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4</sup> Ez a korlát [állítsa be a gazdagép](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config).  
<sup>5</sup> függvény alkalmazásokat üzemeltethet a tényleges száma attól függ, a tevékenység az alkalmazások, a gép példány és a megfelelő erőforrás-használat méretét.   
<sup>6</sup>a megadott tárolási kapacitása szerepel a teljes tartalom mérete az ideiglenes tároló összes alkalmazása tekintetében ugyanazt az App Service-csomagban. Használatalapú csomag használja az Azure Files ideiglenes tárhely.  
<sup>7</sup>amikor a függvényalkalmazás szolgáltatásban üzemeltetett egy [Használatalapú csomag](../articles/azure-functions/functions-scale.md#consumption-plan), csak a CNAME beállítás támogatott. Az alkalmazások esetében egy [prémium szintű csomag](../articles/azure-functions/functions-scale.md#premium-plan-public-preview) vagy egy [App Service-csomag](../articles/azure-functions/functions-scale.md#app-service-plan), leképezhet egy egyéni tartományt egy olyan CNAME REKORDOT vagy egy A rekordot. 
