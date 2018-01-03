---
title: "Hálózati biztonsági csoport folyamata naplók Azure hálózati figyelőt kezelése |} Microsoft Docs"
description: "Ez a lap ismerteti, hogyan Azure hálózati figyelőt hálózati biztonsági csoport folyamata kezelése"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 01606cbf-d70b-40ad-bc1d-f03bb642e0af
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 633543aba99f5c09b14a9e4b11adf59ca04d0fe5
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="manage-network-security-group-flow-logs-in-the-azure-portal"></a>Hálózati biztonsági csoport folyamata naplók az Azure portálon kezelése

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [CLI 1.0](network-watcher-nsg-flow-logging-cli-nodejs.md)
> - [CLI 2.0](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

Hálózati biztonsági csoport folyamata feldolgozásra egyik funkciója, amely lehetővé teszi, hogy tekintse meg a hálózati biztonsági csoporton keresztül bemenő és kimenő IP-forgalom hálózati figyelőt. A folyamat naplók JSON formátumban vannak megírva, és a fontos információkat, beleértve: 

- Kimenő és bejövő forgalom szabály alapon.
- A hálózati adapterre alkalmazza a folyamatot.
- a folyamat (forrás vagy a cél IP, forrás vagy a cél port, protocol) 5 rekordos információt.
- Információ arról, hogy forgalom engedélyezett vagy megtagadott.

## <a name="before-you-begin"></a>Előkészületek

Ez a forgatókönyv azt feltételezi, hogy már követte lépéseit [hozzon létre egy hálózati figyelőt példányt](network-watcher-create.md). A forgatókönyv emellett feltételezi, hogy egy rendelkezik egy erőforráscsoportot, egy érvényes virtuális géppel.

## <a name="register-insights-provider"></a>Elemzések szolgáltató regisztrálása

A folyamat sikeres, működéséhez naplózás a **Microsoft.Insights** szolgáltató regisztrálva kell lennie. Regisztrálja a szolgáltatót, hogy a következő lépéseket: 

1. Ugrás a **előfizetések**, majd válassza ki az előfizetést, amelyekhez folyamata naplók engedélyezése. 
2. Az a **előfizetés** panelen válassza **erőforrás-szolgáltató**. 
3. Nézze meg a szolgáltatók listáját, és ellenőrizze, hogy a **microsoft.insights** szolgáltató regisztrálva van. Ha nem, akkor válasszon **regisztrálása**.

![Nézet szolgáltatók][providers]

## <a name="enable-flow-logs"></a>Attribútumfolyam naplók engedélyezése

Ezeket a lépéseket tenni a folyamatot, amely a hálózati biztonsági csoport folyamata naplók engedélyezése.

### <a name="step-1"></a>1. lépés

Nyissa meg a hálózati figyelőt példányhoz, és válassza ki **NSG Flow naplózza**.

![Attribútumfolyam naplók – áttekintés][1]

### <a name="step-2"></a>2. lépés

Válassza ki a hálózati biztonsági csoportot a listából.

![Attribútumfolyam naplók – áttekintés][2]

### <a name="step-3"></a>3. lépés 

A a **adatfolyam-naplói beállításainak** panelen állítsa a állapotát **a**, és majd a tárfiók konfigurálása.  Amikor elkészült, válassza ki a **OK**. Válassza ki **mentése**.

![Attribútumfolyam naplók – áttekintés][3]

## <a name="download-flow-logs"></a>Attribútumfolyam naplók letöltése

Attribútumfolyam naplók a storage-fiók mentése. Töltse le a folyamat naplók megtekintheti őket.

### <a name="step-1"></a>1. lépés

Folyamat naplók letöltéséhez kiválasztása **folyamata naplók letölthető konfigurált tárfiókok**. Ebben a lépésben megnyitná a tárolási fiók nézetet adja meg a mely naplók letöltéséhez.

![Folyamatnaplók beállításai][4]

### <a name="step-2"></a>2. lépés

Nyissa meg a megfelelő tárolási fiók. Válassza ki **tárolók** > **insights-napló-networksecuritygroupflowevent**.

![Folyamatnaplók beállításai][5]

### <a name="step-3"></a>3. lépés

Nyissa meg a folyamat napló helye, válassza ki, majd válassza ki **letöltése**.

![Folyamatnaplók beállításai][6]

A napló szerkezete kapcsolatos információkért látogasson el a [hálózati biztonsági csoport folyamata napló áttekintése](network-watcher-nsg-flow-logging-overview.md).

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan [jelenítheti meg az NSG folyamata naplók a powerbi-jal](network-watcher-visualize-nsg-flow-logs-power-bi.md).

<!-- Image references -->
[1]: ./media/network-watcher-nsg-flow-logging-portal/figure1.png
[2]: ./media/network-watcher-nsg-flow-logging-portal/figure2.png
[3]: ./media/network-watcher-nsg-flow-logging-portal/figure3.png
[4]: ./media/network-watcher-nsg-flow-logging-portal/figure4.png
[5]: ./media/network-watcher-nsg-flow-logging-portal/figure5.png
[6]: ./media/network-watcher-nsg-flow-logging-portal/figure6.png
[providers]: ./media/network-watcher-nsg-flow-logging-portal/providers.png
