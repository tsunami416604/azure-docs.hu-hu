---
title: Szolgáltatásvégpont-szabályzatok létrehozása és társítása – Azure Portal
titlesuffix: Azure Virtual Network
description: Ebben a cikkben megtudhatja, hogyan állíthatja be és társítva a szolgáltatásvégpont-szabályzatok az Azure Portal használatával.
services: virtual-network
documentationcenter: virtual-network
author: RDhillon
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 02/21/2020
ms.author: rdhillon
ms.openlocfilehash: d26fd2fec5f9d5ab8e9d82ff2c6bd83b11c72e99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651189"
---
# <a name="create-change-or-delete-service-endpoint-policy-using-the-azure-portal"></a>Szolgáltatásvégpont-szabályzat létrehozása, módosítása vagy törlése az Azure Portalhasználatával

A szolgáltatásvégpont-szabályzatok lehetővé teszik a virtuális hálózati forgalom szűrését adott Azure-erőforrásokra a szolgáltatásvégpontokon keresztül. Ha nem ismeri a szolgáltatásvégpont-szabályzatok, tekintse meg [a szolgáltatás végpontszabályzatok áttekintése](virtual-network-service-endpoint-policies-overview.md) további információkért tekintse meg.

 Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Szolgáltatásvégpont-házirend létrehozása
> * Szolgáltatásvégpont-házirend-definíció létrehozása
> * Virtuális hálózat létrehozása alhálózattal
> * Szolgáltatásvégpont-házirend társítása alhálózathoz

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba 

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-service-endpoint-policy"></a>Szolgáltatásvégpont-házirend létrehozása

1. Válassza az Azure Portal bal felső sarkában található **+ Erőforrás létrehozása** lehetőséget.
2. A keresési ablaktáblán írja be a "szolgáltatásvégpont-házirend" kifejezést, és válassza a **Szolgáltatásvégpont-házirend lehetőséget,** majd válassza **a Létrehozás lehetőséget.**

![Szolgáltatásvégpont-házirend létrehozása](./media/virtual-network-service-endpoint-policies-portal/create-sep-resource.png)

3. Írja be vagy jelölje ki a következő információkat az **Alapok** 

   - Előfizetés : Válassza ki az előfizetést a szabályzathoz
   - Erőforráscsoport : Válassza **az Új létrehozása lehetőséget,** és írja be a *myResourceGroup értéket*
   - Név : myEndpointPolicy
   - Helyszín : Usa középső régiója
 
   ![A szolgáltatásvégpont-házirend alapjainak létrehozása](./media/virtual-network-service-endpoint-policies-portal/create-sep-basics.png)

4. Válassza a **+ Hozzáadás** lehetőséget az Erőforrások csoportban, és írja be vagy jelölje ki a következő adatokat az **Erőforrás hozzáadása** ablaktáblában **Resources**

   - Szolgáltatás : Csak a **Microsoft.Storage** érhető el a Szolgáltatásvégpont-házirendekkel
   - Hatókör : Válasszon ki egyet az **egyfiókos,** **az előfizetésben lévő összes fiókból** és **az erőforráscsoport minden fiókjából**
   - Előfizetés : Válassza ki a tárfiókra vonatkozó előfizetését. A szabályzat- és tárfiókok különböző előfizetésekben is lehetnek.
   - Erőforráscsoport : Válassza ki az erőforráscsoportot. Kötelező, ha a hatókör értéke" "Minden fiók az erőforráscsoportban" vagy "Egyfiók".  
   - Erőforrás : Válassza ki az Azure Storage-erőforrást a kiválasztott Előfizetés vagy Erőforráscsoport alatt
   - Kattintson a **Hozzáadás** gombra az alján az erőforrás hozzáadásának befejezéséhez

   ![Szolgáltatásvégpont-házirend-definíciója - erőforrás](./media/virtual-network-service-endpoint-policies-portal/create-sep-add-resource.png)

   - További erőforrások hozzáadása a fenti lépések szükség szerint történő megismétlésével

5. Nem kötelező: Adja meg vagy válassza ki a **címkékben**a következő információkat:
   
   - Kulcs : Válassza ki a kulcsot a házirendhez. Pl.: Osztály     
   - Érték : Adja meg a kulcs értékpárjait. Pl.: Pénzügy

6. Válassza **a Véleményezés + Létrehozás lehetőséget.** Ellenőrizze az adatokat, és kattintson a **Létrehozás gombra.** További szerkesztéshez kattintson az **Előző**gombra. 

   ![Szolgáltatásvégpont-házirend végső érvényesítésének létrehozása](./media/virtual-network-service-endpoint-policies-portal/create-sep-review-create.png)
  
## <a name="view-endpoint-policies"></a>Végpont-házirendek megtekintése 

1. A portál *Minden szolgáltatás* mezőjében kezdje el beírni a *szolgáltatásvégpont-házirendeket.* Válassza a **Szolgáltatásvégpont-házirendek**lehetőséget.
2. Az **Előfizetések**csoportban válassza ki az előfizetést és az erőforráscsoportot, ahogy az az alábbi képen látható

   ![Házirend megjelenítése](./media/virtual-network-service-endpoint-policies-portal/sep-view.png)
       
3. Válassza ki a házirendet, és kattintson a **Házirend-definíciók** elemre további házirend-definíciók megtekintéséhez vagy hozzáadásához.

   ![Házirend-definíciók megjelenítése](./media/virtual-network-service-endpoint-policies-portal/sep-policy-definition.png)

4. Válassza a **Társított alhálózatok lehetőséget** a házirend társítására használt alhálózatok megtekintéséhez. Ha még nincs alhálózat társítva, kövesse a következő lépésutasításait.

   ![Társított alhálózatok](./media/virtual-network-service-endpoint-policies-portal/sep-associated-subnets.png)
 
5. Házirend társítása alhálózathoz

>[!WARNING] 
> Győződjön meg arról, hogy az alhálózatról elért összes erőforrás hozzá van adva a házirend-definícióhoz, mielőtt a házirendet az adott alhálózathoz társítané. A házirend társítása után csak a felsorolt erőforrások *engedélyezése* engedélyezett lesz a szolgáltatás végpontok felett. 
>
> Győződjön meg arról is, hogy nem léteznek felügyelt Azure-szolgáltatások a szolgáltatásvégpont-szabályzathoz társított alhálózatban

- Mielőtt egy házirendet egy alhálózathoz társíthatna, létre kell hoznia egy virtuális hálózatot és alhálózatot. Kérjük, olvassa el a [virtuális hálózat létrehozása](./quick-create-portal.md) cikket ebben a segítséget.

- Miután beállította a virtuális hálózatot és az alhálózatot, konfigurálnia kell a virtuális hálózati szolgáltatás végpontjait az Azure Storage számára. A Virtuális hálózat panelen válassza a **Szolgáltatás végpontjai**elemet , majd a következő ablaktáblán válassza a **Microsoft.Storage** elemet, az **Alhálózatok** csoportban pedig a kívánt virtuális hálózatot vagy alhálózatot.

- Most kiválaszthatja a Szolgáltatásvégpont-szabályzatot a fenti ablaktábla legördülő legördülő helyéről, ha már létrehozott service endpoint-házirendeket, mielőtt konfigurálta volna a Szolgáltatásvégpontot az alhálózathoz az alábbi módon.

    ![Alhálózat társítása szolgáltatásvégpont létrehozása közben](./media/virtual-network-service-endpoint-policies-portal/vnet-config-service-endpoint-add-sep.png)

- VAGY ha a szolgáltatásvégpontok már konfigurálása után társítszolgáltatási végpontszabályzatokat, az alhálózatot a Szolgáltatásvégpont-házirend panelen keresztül társítja, ha a **társított alhálózatok** ablaktáblára navigál az alábbi módon.

    ![Alhálózat társítása sep-en keresztül](./media/virtual-network-service-endpoint-policies-portal/sep-edit-subnet-association.png)

>[!WARNING] 
>Az Azure Storage-erőforrásokhoz való hozzáférés minden régióban korlátozott lesz, mint egy szolgáltatás végpont szabályzat a b. alhálózat.

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban létrehozott egy szolgáltatásvégpont-szabályzatot, és társította azt egy alhálózathoz. Ha többet szeretne megtudni a szolgáltatás végpontszabályzatairól, olvassa el a [szolgáltatásvégpont-szabályzatok áttekintése című témakört.](virtual-network-service-endpoint-policies-overview.md)
