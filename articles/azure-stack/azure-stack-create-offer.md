---
title: Hozzon létre egy ajánlatot Azure verem |} Microsoft Docs
description: Felhő rendszergazdaként megtudhatja, hogyan hozzon létre egy ajánlatot a felhasználók Azure-készletben.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 96b080a4-a9a5-407c-ba54-111de2413d59
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/07/2018
ms.author: brenduns
ms.openlocfilehash: e5b96a9464bf4d0e3b69d2f635da32c6648ce793
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35247517"
---
# <a name="create-an-offer-in-azure-stack"></a>Ajánlat létrehozása az Azure Stackben

[Kínál](azure-stack-key-features.md) olyan beállításcsoportok, egy vagy több terveket tartalmazhatnak, amelyek a felhasználóknak, hogy vásárolni, illetve fizethetnek elő telepített szolgáltatók. Ez a dokumentum azt ismerteti, hogyan hozzon létre egy ajánlat, amely tartalmazza a [létrehozott tervet](azure-stack-create-plan.md). Ez az ajánlat segítségével előfizetők a virtuális gépek beállítása.

1. Jelentkezzen be a verem Azure felügyeleti portálra (https://adminportal.local.azurestack.external) válassza **új** > **bérlői kínál + tervek** > **kínálnak**.

   ![Ajánlat létrehozása](media/azure-stack-create-offer/image01.png)
  
2. A **új ajánlat**, adja meg egy **megjelenített név** és egy **erőforrásnév**, majd a **erőforráscsoport**, jelölje be **létrehozása új** vagy **meglévő**. A megjelenített nevének értéke az ajánlat rövid nevét. Ez a név szerepel, hogy a felhasználók látni, amikor előfizet egy ajánlatot a vonatkozó ajánlatot csak információ. Használjon egy egyszerűen elsajátítható nevet, amely segít megérteni, mi részeként elérhető ajánlatot. Kizárólag a rendszergazda láthatja az erőforrás nevét. Ezt a nevet használják a rendszergazdák az ajánlattal mint Azure Resource Manager-erőforrással végzett műveletekhez.

   ![Új ajánlat](media/azure-stack-create-offer/image01a.png)
  
3. Válassza ki **tervek kiinduló** megnyitásához a **megtervezése**. Válassza ki a kívánt ajánlatot szerepeljen, és válassza a tervek **válasszon**. Az ajánlat válassza létrehozásához **létrehozása**.

   ![Válassza ki a terv](media/azure-stack-create-offer/image02.png)
  
4. Miután létrehozta a kért ajánlat, állapotában módosítható. Ajánlatok kell tenni *nyilvános* a felhasználók számára, amikor előfizet teljes nézet lekéréséhez. Ajánlatok lehet:

   - **Nyilvános**: a felhasználók számára látható.
   - **Személyes**: csak a rendszergazdák felhőbe látható. Ez a beállítás akkor hasznos, a terv vagy ajánlat létrehozásakor elkészítése közben, vagy ha a felhő rendszergazdája szeretné [egyes felhasználók számára előfizetéssel, hozzon létre](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator).
   - **Leszerelve:** Új előfizetők már nem választhatják. A felhő rendszergazdájához használható leszerelt jövőbeli előfizetések megakadályozása, de hagyjon aktuális előfizetők nem érinti.

   > [!TIP]  
   > Az ajánlat módosításai nem közvetlenül a felhasználó számára látható. A módosítások megtekintéséhez jelentkezzen ki, és jelentkezzen be újra a felhasználói portálra, tekintse meg az új ajánlat felhasználóknak problémájuk lehet.

   Az ajánlat állapotának módosítása:

   - **1803 és újabb verzióit**:  
     Az ajánlat áttekintése, jelölje ki **kisegítő állapot**. Válassza ki a használni kívánt állapota (például *nyilvános*), és válassza **mentése**.
 
     ![Válassza ki a kisegítő állapota](media/azure-stack-create-offer/change-state.png)

     Másik megoldásként, ha hozzáfér egy ajánlatot lépjen **beállításokat kínálnak**. Válassza ki **kisegítő állapot** az állapot módosítására irányuló.

   - **1803-es vagy korábbi**:  
     Válassza ki **összes erőforrás**, keresse meg az új ajánlatot, és válassza ki az új ajánlat. Válassza ki **állapotváltoztatási**, majd válassza ki **nyilvános**.

   > [!NOTE]
   > Alapértelmezett ajánlatokat, a csomagok és a kvóták létrehozása a PowerShell is használható. További információkért lásd: [Azure verem szolgáltatás-rendszergazda információs](https://github.com/Azure/AzureStack-Tools/tree/master/ServiceAdmin).

## <a name="next-steps"></a>További lépések

- [Előfizetések létrehozása](azure-stack-subscribe-plan-provision-vm.md)
- [Virtuális gép kiépítése](azure-stack-provision-vm.md)
