---
title: Hozzon létre egy ajánlatot az Azure Stackben |} A Microsoft Docs
description: Felhő-rendszergazdák megtudhatja, hogyan hozhat létre egy ajánlatot, a felhasználók számára az Azure Stackben.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: efemmano
ms.openlocfilehash: 079f45e37bef24ac974a0e2df7b1e81f1002cac0
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54159077"
---
# <a name="create-an-offer-in-azure-stack"></a>Ajánlat létrehozása az Azure Stackben

[Kínál](azure-stack-key-features.md) csoportjai, egy vagy több olyan csomag, amelyet szolgáltatók felhasználóknak, amelyet a felhasználók is vásárolhat, illetve fizethetnek elő. Ez a cikk bemutatja, hogyan hozhat létre, amely tartalmazza az ajánlatot a [létrehozott csomagot](azure-stack-create-plan.md). Ez az ajánlat virtuális gépeken (VM) beállítása lehetővé teszi az előfizetők számára.

1. Jelentkezzen be a [Azure Stack rendszergazdai portál](https://adminportal.local.azurestack.external) válassza **+ erőforrás létrehozása**, majd **bérlői ajánlatok és csomagok**, majd **ajánlat**.

   ![Ajánlat létrehozása](media/azure-stack-create-offer/image01.png)
  
2. Alatt **új ajánlat**, adjon meg egy **megjelenített neve** és a egy **erőforrásnév**, majd a **erőforráscsoport**, jelölje be **létrehozása új** vagy **meglévő**. A megjelenítendő név az ajánlat rövid nevét. Erre a rövid névre, csak az ajánlat, amely a felhasználó számára látható előfizetés egy ajánlatra vonatkozó információk. Használható, amely segít a felhasználóknak megérteni, hogy mit tartalmaz az ajánlat az intuitív név. Csak a rendszergazda láthatja az erőforrás neve. Ezt a nevet használják a rendszergazdák az ajánlattal mint Azure Resource Manager-erőforrással végzett műveletekhez.

   ![Új ajánlat](media/azure-stack-create-offer/image01a.png)
  
3. Válassza ki **Alapcsomagok** megnyitásához a **megtervezése**. Válassza ki a szeretne foglalni az ajánlatba, és válassza a csomagokat **kiválasztása**. Válassza a ajánlat létrehozása **létrehozás**.

   ![Adatforgalmi díjcsomag kiválasztása](media/azure-stack-create-offer/image02.png)
  
4. Miután létrehozta az ajánlatot, módosíthatja a állapotában. Ajánlatok kell tenni **nyilvános** a felhasználók számára a teljes képet kaphat, amikor előfizetnek. Ajánlatok a következők lehetnek:

   - **Nyilvános**: Látható a felhasználók számára.
   - **Privát**: Csak a rendszergazdák elhelyezhetik látható. Ez a beállítás akkor hasznos, a csomag vagy ajánlat tervezése közben, vagy ha a felhő rendszergazdája szeretné [hozzon létre minden egyes felhasználók számára előfizetést](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator).
   - **Szerelni**: Lezárva az új előfizetők számára. A felhő rendszergazdájához, leszerelheti ajánlatok megelőzni a jövőbeli előfizetések, de a jelenlegi előfizetők változatlanul hagyja.

   > [!TIP]  
   > Az ajánlat módosítások nem láthatók azonnal a felhasználó számára. A módosítások megtekintéséhez jelentkezzen ki, majd jelentkezzen be újra a felhasználói portálra, tekintse meg az új ajánlat felhasználók előfordulhat, hogy kell.

   Az ajánlat áttekintése képernyőn válassza ki a **kisegítő lehetőségek állapota**. Válassza ki a használni kívánt állapot (például **nyilvános**), majd válassza ki **mentése**.

     ![Válassza ki az állapot](media/azure-stack-create-offer/change-stage-1807.png)

     Alternatív megoldásként válassza **Állapotváltozáshoz** majd állapotba.

    ![Válassza ki a kisegítő lehetőségek állapota](media/azure-stack-create-offer/change-stage-select-1807.png)

   > [!NOTE]
   > PowerShell használatával alapértelmezett ajánlatok, tervek és kvóták létrehozása. További információkért lásd: [Azure Stack PowerShell-modul 1.4.0-s](/powershell/azure/azure-stack/overview?view=azurestackps-1.4.0).

## <a name="next-steps"></a>További lépések

- [Előfizetések létrehozása](azure-stack-subscribe-plan-provision-vm.md)
- [Virtuális gép kiépítése](azure-stack-provision-vm.md)
