---
title: Hálózati biztonsági csoport folyamata naplók Azure hálózati figyelőt kezelése |} Microsoft Docs
description: Ez a lap ismerteti, hogyan Azure hálózati figyelőt hálózati biztonsági csoport folyamata kezelése
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 01606cbf-d70b-40ad-bc1d-f03bb642e0af
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: cb41781c5ac8fb759cecea01402c08dd716bf7d7
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
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

Ebben a cikkben szereplő lépések elvégzéséhez már rendelkeznie kell a következőket:

- Egy meglévő hálózati figyelőt. Hozzon létre egy hálózati figyelőt, lásd: [hozzon létre egy hálózati figyelőt példányt](network-watcher-create.md).
- Egy meglévő erőforráscsoportot egy érvényes virtuális géppel. Ha egy virtuális gép nem rendelkezik, tekintse meg a hozzon létre egy [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) vagy [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) virtuális gépet.

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

A a **adatfolyam-naplói beállításainak** panelen állítsa a állapotát **a**, és majd a tárfiók konfigurálása. Válassza ki a meglévő tárfiókot, amely rendelkezik **minden hálózat** (alapértelmezett) listában kijelölt **tűzfalak és a virtuális hálózatok**alatt a **beállítások** a tárfiók. Miután kijelölt egy tárfiókot, válassza ki a **OK**, majd válassza ki **mentése**.

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
