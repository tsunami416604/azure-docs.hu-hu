---
author: ggailey777
ms.service: billing
ms.topic: include
ms.date: 05/09/2019
ms.author: glenga
ms.openlocfilehash: d00af77a2d502cb534a4ab4a0db9231e5e8748b7
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69636452"
---
| Resource | [Felhasználási terv](../articles/azure-functions/functions-scale.md#consumption-plan) | [Prémium csomag](../articles/azure-functions/functions-scale.md#premium-plan) | [App Service terv](../articles/azure-functions/functions-scale.md#app-service-plan) <sup>1</sup> |
| --- | --- | --- | --- |
| Horizontális felskálázás | Esemény vezérelt | Esemény vezérelt | [Manuális/automatikus méretezés](../articles/app-service/manage-scale-up.md) | 
| Példányok maximális száma | 200 | 20 | 10-20 |
|Alapértelmezett [időtúllépési időtartam](../articles/azure-functions/functions-scale.md#timeout) (perc) |5 | 30 |30<sup>2</sup> |
|Maximális [időtúllépési időtartam](../articles/azure-functions/functions-scale.md#timeout) (perc) |10 | unbounded | nem kötött<sup>3</sup> |
| Kimenő kapcsolatok maximális száma (/példány) | 600 aktív (1200 összesen) | unbounded | unbounded |
| Kérelem maximális mérete (MB)<sup>4</sup> | 100 | 100 | 100 |
| Lekérdezési karakterlánc maximális hossza<sup>4</sup> | 4096 | 4096 | 4096 |
| Kérelem URL-címének maximális hossza<sup>4</sup> | 8192 | 8192 | 8192 |
| [ACU](../articles/virtual-machines/windows/acu.md) -példányok száma | 100 | 210-840 | 100-840 |
| Maximális memória (GB/példány) | 1.5 | 3,5 – 14 | 1,75 – 14 |
| Function Apps/csomag |100 |100 |nem kötött<sup>5</sup> |
| [App Service-csomagok](../articles/app-service/overview-hosting-plans.md) | 100 [régiónként](https://azure.microsoft.com/global-infrastructure/regions/) |100/erőforráscsoport |100/erőforráscsoport |
| <sup>6</sup> . tárterület |1 GB |250 GB |50-1000 GB |
| Egyéni tartományok/alkalmazás</a> |500<sup>7</sup> |500 |500 |
| Egyéni tartomány [SSL-támogatása](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |nem kötött SNI SSL-kapcsolatok belefoglalva | nem kötött SNI SSL és 1 IP SSL kapcsolat is |nem kötött SNI SSL és 1 IP SSL kapcsolat is | 

<sup>1</sup> a különböző app Service-csomagokra vonatkozó beállításokra vonatkozó meghatározott korlátokat a [app Service csomag korlátai](../articles/azure-subscription-service-limits.md#app-service-limits)című részben tekintheti meg.  
<sup>2</sup> alapértelmezés szerint a app Service csomag 1. x futtatókörnyezetének időtúllépése nem kötött.  
<sup>3</sup> a app Service tervnek [mindig](../articles/azure-functions/functions-scale.md#always-on)be kell állítani. A standard [díjszabás](https://azure.microsoft.com/pricing/details/app-service/)szerint kell fizetni.  
<sup>4</sup> ezek a korlátok [a gazdagépen vannak beállítva](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config).  
<sup>5</sup> a gazdagépek tényleges száma az alkalmazások tevékenységéből, a gépi példányok méretétől és a megfelelő erőforrás-felhasználástól függ.  
<sup>6</sup> a tárolási korlát a teljes tartalom mérete az ideiglenes tárolóban, az összes alkalmazás között, ugyanabban a app Service csomagban. A felhasználási terv az ideiglenes tároláshoz Azure Files használ.  
<sup>7</sup> ha a Function alkalmazás egy [felhasználási](../articles/azure-functions/functions-scale.md#consumption-plan)csomagban található, csak a CNAME beállítás támogatott. A [prémium](../articles/azure-functions/functions-scale.md#premium-plan) szintű csomagban vagy [app Service](../articles/azure-functions/functions-scale.md#app-service-plan)csomagban található Function apps esetében egy egyéni tartományt egy CNAME vagy egy rekord használatával képezhető le.
