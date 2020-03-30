---
title: Azure Event Grid – Hibaelhárítási útmutató
description: Ez a cikk a hibakódok, a hibaüzenetek, a leírások és az ajánlott műveletek listáját tartalmazza.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: 1ab9aeac0bde21e229fdb57b7ad02d5d48471551
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645072"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>Az Azure Event Grid hibáinak elhárítása
Ez a hibaelhárítási útmutató az Azure Event Grid hibakódjainak listáját, hibaüzeneteket, azok leírását és ajánlott műveleteket tartalmaz, amelyeket a hibák belépésekén el kell tennie. 

## <a name="error-code-400"></a>Hibakód: 400
| Hibakód | Hibaüzenet | Leírás | Ajánlás |
| ---------- | ------------- | ----------- | -------------- | 
| httpstatuscode.BadRequest<br/>400 | A témakör nevének 3 és 50 karakter közötti hosszúságúnak kell lennie. | Az egyéni témakörnév hosszának 3 és 50 karakter közötti hosszúságúnak kell lennie. A témakör nevében csak alfanumerikus betűk, számjegyek és a '-' karakter szerepelhet. A név nem kezdődjön a következő fenntartott szavakkal: <ul><li>Microsoft</li><li>EventGrid</li><li>Rendszer</li></ul> | Válasszon egy másik témakörnevet, amely megfelel a témakörnév követelményeinek. |
| httpstatuscode.BadRequest<br/>400 | A tartománynév hosszának 3 és 50 karakter között kell lennie. | A tartománynév hosszának 3 és 50 karakter közötti hosszúságúnak kell lennie. A témakör nevében csak alfanumerikus betűk, számjegyek és a '-' karakter szerepelhet. A név nem kezdődjön a következő fenntartott szavakkal:<ul><li>Microsoft</li><li>EventGrid</li><li>Rendszer</li> | Válasszon egy másik tartománynevet, amely megfelel a tartománynév követelményeinek. |
| httpstatuscode.BadRequest<br/>400 | Érvénytelen lejárati idő. | Az esemény-előfizetés lejárati ideje határozza meg, hogy az esemény-előfizetés mikor lesz kivonva. Ennek az értéknek a jövőben érvényes DateTime értéknek kell lennie.| Győződjön meg arról, hogy az Esemény-előfizetés lejárati ideje érvényes DateTime formátumban van beállítva, és a jövőben lesz beállítva. |

## <a name="error-code-409"></a>Hibakód: 409
| Hibakód | Hibaüzenet | Leírás | Javasolt művelet |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode.Ütközés <br/>409 | A megadott nevű témakör már létezik. Válasszon másik témakörnevet.   | Az egyéni témakör nevének egyedinek kell lennie egyetlen Azure-régióban a megfelelő közzétételi művelet biztosítása érdekében. Ugyanazt a nevet lehet használni a különböző Azure-régiókban. | Válasszon másik nevet a témakörhöz. |
| HttpStatusCode.Ütközés <br/> 409 | A megadott tartomány már létezik. Válasszon másik tartománynevet. | A tartománynévnek egyedinek kell lennie egyetlen Azure-régióban a megfelelő közzétételi művelet biztosítása érdekében. Ugyanazt a nevet lehet használni a különböző Azure-régiókban. | Válasszon másik nevet a tartományhoz. |
| HttpStatusCode.Ütközés<br/>409 | Kvótakorlát elérve. Ezekről a korlátokról az [Azure Event Grid-korlátok című témakörben](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits)talál további információt.  | Minden Azure-előfizetés rendelkezik egy korlátot az Azure Event Grid-erőforrások, amelyek et használhat. A kvóta egy részét vagy egészét túllépték, és nem lehetett több erőforrást létrehozni. |    Ellenőrizze az aktuális erőforrás-használatot, és törölje azokat, amelyekre nincs szükség. Ha továbbra is növelnie kell a kvótát, küldjön egy e-mailt a szükséges erőforrások pontos [aeg@microsoft.com](mailto:aeg@microsoft.com) számával. |


## <a name="next-steps"></a>További lépések
Ha további segítségre van szüksége, tegye fel a problémát a [Stack Overflow fórumon,](https://stackoverflow.com/questions/tagged/azure-eventgrid) vagy nyisson meg egy [támogatási jegyet.](https://azure.microsoft.com/support/options/) 
