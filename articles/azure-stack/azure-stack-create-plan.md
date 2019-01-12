---
title: Csomag létrehozása az Azure Stackben |} A Microsoft Docs
description: Felhő-rendszergazdaként hozzon létre egy csomagot, amely lehetővé teszi, hogy a virtuális gépek előfizetők kiépítése.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: unknown
ms.openlocfilehash: 7a3a87c70402374bea6357d1b89a19938ca08868
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2019
ms.locfileid: "54243525"
---
# <a name="create-a-plan-in-azure-stack"></a>Csomag létrehozása az Azure Stackben

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

A [Csomagok](azure-stack-key-features.md) egy vagy több szolgáltatás csoportjai. Szolgáltatóként a felhasználóknak kínált terveket is létrehozhat. Ezután a felhasználók előfizethetnek azokra a a csomagok és szolgáltatások tartoznak. Ez a példa bemutatja, hogyan hozzon létre egy csomagot, amely tartalmazza a számítási, hálózati és tárolási erőforrás-szolgáltatókat. Ez a csomag virtuális gépek lehetővé teszi az előfizetők számára.

1. Jelentkezzen be a [Azure Stack rendszergazdai portál](https://adminportal.local.azurestack.external).

2. Csomag és ajánlat, amely a felhasználók előfizethetnek létrehozásához válassza **+ erőforrás létrehozása**, majd **ajánlatok és csomagok**, majd **terv**.
  
   ![Csomag kiválasztása](media/azure-stack-create-plan/select-plan.png)

3. Alatt **új terv**, adjon meg egy **megjelenített név** és a egy **erőforrásnév**. A megjelenített név a csomag rövid neve, a felhasználók számára is. Csak a rendszergazda láthatja az erőforrás nevét, mely segítségével a rendszergazdák a csomaggal mint Azure Resource Manager-erőforrással rendelkező működéséhez.

   ![Adja meg a részleteket](media/azure-stack-create-plan/plan-name.png)

4. Hozzon létre egy új **erőforráscsoport**, vagy válasszon ki egy meglévőt, a csomag tárolójaként.

   ![Adja meg az erőforráscsoportot](media/azure-stack-create-plan/resource-group.png)

5. Válassza ki **szolgáltatások** , és ezután jelölje be a **Microsoft.Compute**, **Microsoft.Network**, és **Microsoft.Storage**. Következő lépésként válassza ki **kiválasztása** a konfiguráció mentéséhez. Jelölőnégyzetek jelennek meg, ha az egérrel rámutat az egyes lehetőségek.
  
   ![Szolgáltatások kiválasztása](media/azure-stack-create-plan/services.png)

6. Válassza ki **kvóták**, **Microsoft.Storage (helyi)**, majd válassza a alapértelmezett kvóta, vagy válasszon **új kvóta létrehozása** testre szabott kvóta.
  
   ![Kvóták](media/azure-stack-create-plan/quotas.png)

7. Ha egy új kvóta próbál létrehozni, adja meg egy **neve** kvóta > adja meg a kvóta értékeket > válassza **OK**. A **kvóta létrehozása** párbeszédpanelje bezárul.

   ![Új kvóta](media/azure-stack-create-plan/new-quota.png)

   Ezután válassza ki a létrehozott új kvóta. Válassza a kvóta rendel hozzá, és bezárja a kiválasztása párbeszédpanelen.
  
   ![A kvóta hozzárendelése](media/azure-stack-create-plan/assign-quota.png)

8. Ismételje meg a 6 és 7 létrehozása és hozzárendelése a kvóták **Microsoft.Network (helyi)** és **Microsoft.Compute (helyi)**. Ha mindhárom szolgáltatást a hozzárendelt kvóták, a következő példához hasonlóan fog megtekintése.

   ![Teljes kvóta hozzárendelések](media/azure-stack-create-plan/all-quotas-assigned.png)

9. Alatt **kvóták**, válassza a **OK**, majd a **új csomag**, válassza a **létrehozás** a terv létrehozásához.

    ![A terv létrehozása](media/azure-stack-create-plan/create.png)

10. Az új terv megtekintéséhez válasszon **összes erőforrás**, keressen rá a tervet, és válassza ki a nevét. Ha az erőforrások listája hosszú, akkor **keresési** keresse meg a csomag neve.

   ![Tekintse át a terv](media/azure-stack-create-plan/plan-overview.png)

## <a name="next-steps"></a>További lépések

* [Ajánlat létrehozása](azure-stack-create-offer.md)
