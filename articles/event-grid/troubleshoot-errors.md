---
title: Azure Event Grid – hibaelhárítási útmutató
description: Ez a cikk a hibakódok, a hibaüzenetek, a leírások és a javasolt műveletek listáját tartalmazza.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: 1ab9aeac0bde21e229fdb57b7ad02d5d48471551
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75645072"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>Azure Event Grid hibák elhárítása
Ez a hibaelhárítási útmutató a Azure Event Grid hibakódok, a hibaüzenetek, a leírások és az ajánlott műveletek listáját tartalmazza, amelyeket a hibák vételekor kell végrehajtania. 

## <a name="error-code-400"></a>Hibakód: 400
| Hibakód | Hibaüzenet | Leírás | Ajánlás |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode. BadRequest<br/>400 | A témakör nevének 3 és 50 karakter közöttinek kell lennie. | Az egyéni témakör nevének hossza csak 3 és 50 karakter közötti lehet. A témakör neve csak alfanumerikus betűket, számokat és a "-" karaktert tartalmazhat. A név nem kezdődhet a következő fenntartott szavakkal: <ul><li>Microsoft</li><li>EventGrid</li><li>Rendszer</li></ul> | Válasszon egy másik témakör nevét, amely megfelel a témakör neve követelményeinek. |
| HttpStatusCode. BadRequest<br/>400 | A tartománynév hossza csak 3 és 50 karakter közötti lehet. | A tartománynév hossza csak 3 és 50 karakter közötti hosszúságú lehet. A témakör neve csak alfanumerikus betűket, számokat és a "-" karaktert tartalmazhat. A név nem kezdődhet a következő fenntartott szavakkal:<ul><li>Microsoft</li><li>EventGrid</li><li>Rendszer</li> | Válasszon másik tartománynevet, amely megfelel a tartománynév követelményeinek. |
| HttpStatusCode. BadRequest<br/>400 | Érvénytelen lejárati idő. | Az esemény-előfizetés lejárati ideje határozza meg, hogy mikor kell kivonni az esemény-előfizetést. Az értéknek a jövőben érvényes DateTime értéknek kell lennie.| Győződjön meg arról, hogy az esemény-előfizetés lejárati ideje érvényes DateTime formátumban van megadva, és a jövőre van beállítva. |

## <a name="error-code-409"></a>Hibakód: 409
| Hibakód | Hibaüzenet | Leírás | Javasolt művelet |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode. Conflict <br/>409 | A megadott nevű témakör már létezik. Válasszon másik témakör-nevet.   | Az egyéni témakör nevének egyedinek kell lennie egyetlen Azure-régióban a megfelelő közzétételi művelet biztosítása érdekében. Ugyanezt a nevet különböző Azure-régiókban is használhatja. | Válasszon másik nevet a témakörnek. |
| HttpStatusCode. Conflict <br/> 409 | A megadott tartomány már létezik. Válasszon másik tartománynevet. | A tartománynévnek egyedinek kell lennie egyetlen Azure-régióban a megfelelő közzétételi művelet biztosítása érdekében. Ugyanezt a nevet különböző Azure-régiókban is használhatja. | Válasszon másik nevet a tartománynak. |
| HttpStatusCode. Conflict<br/>409 | Elérte a kvóta korlátját. További információ ezekről a korlátozásokról: [Azure Event Grid korlátok](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits).  | Az egyes Azure-előfizetések az általa használható Azure Event Grid erőforrások számát korlátozzák. A kvóta egy részét vagy egészét túllépte, és nem hozható létre több erőforrás. |    Győződjön meg az erőforrások aktuális használatáról, és törölje a szükséges adatokat. Ha továbbra is meg kell emelnie a kvótát, küldjön [aeg@microsoft.com](mailto:aeg@microsoft.com) e-mailt a szükséges erőforrások pontos számával. |


## <a name="next-steps"></a>További lépések
Ha további segítségre van szüksége, tegye fel a problémát a [stack overflow fórumba](https://stackoverflow.com/questions/tagged/azure-eventgrid) , vagy nyisson meg egy [támogatási jegyet](https://azure.microsoft.com/support/options/). 
