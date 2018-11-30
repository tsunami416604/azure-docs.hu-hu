---
title: Hozza létre és társítsa a szolgáltatásvégpont-szabályzat – Azure portal |} A Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan állítható be, és a kapcsolódó szolgáltatásvégpont-szabályzat az Azure portal használatával.
services: virtual-network
documentationcenter: virtual-network
author: anithaa
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 09/18/2018
ms.author: anithaa
ms.openlocfilehash: 6bd318da5b3cd667d0a39b9c963e8d3ffd472f41
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52633010"
---
# <a name="create-change-or-delete-service-endpoint-policy-using-the-azure-portal"></a>Létrehozása, módosítása vagy törlése a szolgáltatásvégpont-szabályzat az Azure portal használatával

Szolgáltatásvégpont-szabályzat lehetővé teszi az adott Azure-erőforrások virtuális hálózati forgalom szűrése Szolgáltatásvégpontok keresztül. Ha még nem ismeri a szolgáltatásvégpont-szabályzatokra, [szolgáltatási végpont házirendek – áttekintés](virtual-network-service-endpoint-policies-overview.md) további.

 Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Egy szolgáltatásvégpont-szabályzat létrehozása
> * Szolgáltatási végpont szabályzatdefiníció létrehozása
> * Hozzon létre egy virtuális hálózatot egy alhálózattal
> * Egy alhálózathoz szolgáltatásvégpont-szabályzat társítása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba 

Jelentkezzen be az Azure Portalra a http://portal.azure.com webhelyen.

## <a name="create-a-service-endpoint-policy"></a>Egy szolgáltatásvégpont-szabályzat létrehozása

1. Válassza az Azure Portal bal felső sarkában található **+ Erőforrás létrehozása** lehetőséget.
2. A keresés panelen írja be a "szolgáltatásvégpont-szabályzat szolgáltatás", és válassza ki **szolgáltatásvégpont-szabályzat (előzetes verzió) szolgáltatás** , majd **létrehozás**.
3. Adja meg, vagy válassza ki a következő adatokat a **alapjai** 

   - Előfizetés: Válassza ki az előfizetést a házirend.    
   - Erőforráscsoport: válasszon **új létrehozása** , és adja meg *myResourceGroup*.     
   - Name: myEndpointPolicy
   - Hely: USA nyugati középső RÉGIÓJA     
 
   ![Hozzon létre szolgáltatásvégpont házirend alapjai](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-create-startpane.PNG)
   
4. Adja meg, vagy válassza ki a következő adatokat a **Szabályzatdefiníciók**

   - Kattintson a **+ adjon hozzá egy erőforrást**, adja meg, vagy válassza ki a következő adatokat, fogadja el a többi beállítás esetében az alapértelmezett és kattintson a **Hozzáadás**.  
   - Hatókör: Jelölje ki **egyetlen fiók** vagy **előfizetésben található összes fiók** vagy **erőforráscsoportban lévő összes fiók**.    
   - Előfizetés: Válassza ki az előfizetést a tárfiókhoz. A házirend- és storage-fiókok különböző előfizetésekhez is lehet.   
   - Erőforráscsoport: Jelölje ki az erőforráscsoportot. Szükséges, amennyiben a hatókör van beállítva, "Az összes fiókok erőforráscsoportban" vagy "Egyetlen fiók".  
   - Erőforrás: mystorageaccountportal    
   - Kattintson a **+ adjon hozzá egy erőforrást** folytatja a további erőforrások hozzáadásával.
   
   ![Szolgáltatásvégpont szabályzatdefiníciók létrehozása](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-create-policydefinitionspane.PNG)
   
5. Nem kötelező: Adjon meg vagy válassza ki a következő adatokat a **címkék**:
   
   - Kulcs: Válassza ki a kulcsot a szabályzathoz. Például: osztály     
   - Érték: Adja meg a kulcs érték pár. Például: Pénzügy

6. Válassza ki **felülvizsgálat + létrehozás**. Ellenőrizze az adatokat, és kattintson **létrehozás**. Ahhoz, hogy további módosításokat, kattintson a **előző**. 

   ![Hozzon létre szolgáltatásvégpont házirend végső ellenőrzés](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-create-finalcreatereview.PNG)
  
 
## <a name="view-endpoint-policies"></a>Végpont szabályzatok megtekintése 

1. Az a *minden szolgáltatás* a portál mezőben kezdje el beírni *szolgáltatásvégpont-szabályzatra*. Válassza ki **szolgáltatási végpont Policies(Preview)**.
2. A **előfizetések**, válassza ki az előfizetést és erőforráscsoportot, a következő képen látható módon

   ![A házirend megjelenítése](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-viewpolicies.PNG)
       
3. Válassza ki a szabályzatot, majd kattintson a **Szabályzatdefiníciók** megtekintéséhez, vagy adjon hozzá további szabályzatdefiníciók.

   ![A szabályzatdefiníciók megjelenítése](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-viewpolicy-adddefinitions.PNG)

4. Válassza ki **tartozó alhálózatok** az alhálózatok megtekintése a házirend társítva. Hozzá kell rendelnie a szabályzatot egy alhálózathoz, kattintson a "Nyissa meg a virtuális hálózat ugyanabban a régióban".

   ![Társított alhálózatok megjelenítése](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-view-associatedsubnets.PNG)
 
## <a name="associate-a-policy-to-a-subnet"></a>Rendeljen hozzá egy szabályzatot egy alhálózathoz

>[!WARNING] 
> Győződjön meg arról, hogy a kiválasztott szolgáltatás az alhálózatról elért összes erőforrás hozzáadódik a szabályzat a házirendet hozzárendelné előtt. Után a házirend társítva, csak az a felsorolt forrásokat el a szabályzatot hozzá lehet adni, az endpoint régiókban a szolgáltatás. 

Mielőtt hozzárendelhetne egy szabályzatot, amely egy alhálózathoz, létre kell hoznia egy virtuális hálózatot és alhálózatot, majd társíthatja a szabályzat az alhálózathoz:

1. Válassza az Azure Portal bal felső sarkában található **+ Erőforrás létrehozása** lehetőséget.
2. Válassza a **Hálózatkezelés**, majd a **Virtuális hálózat** elemet.
3. A **Virtuális hálózat létrehozása** területen adja meg vagy válassza ki a következő adatokat, fogadja a többi beállításnál fogadja el az alapértelmezett értékeket, majd válassza a **Létrehozás** elemet:
   - Name: myVirtualNetwork      
   - Címtere: 10.0.0.0/16      
   - Előfizetés: Válassza ki az előfizetését. A házirend a virtuális hálózatnak ugyanabban az előfizetésben kell lennie.     
   - Erőforráscsoport: válasszon **meglévő** , majd *myResourceGroup*     
   - Hely: USA nyugati középső RÉGIÓJA     
   - Alhálózat neve: titkos     
   - Címtartomány: 10.0.0.0/24
     
4. A portál felső részén található **Erőforrások, szolgáltatások és dokumentumok keresése** mezőben kezdje el beírni a *myVirtualNetwork* nevet. Amikor a **myVirtualNetwork** megjelenik a keresési eredmények között, válassza ki.
5. Alatt **beállítások**, jelölje be **alhálózatok** majd **privát**.
6. Ahogy az alábbi képen is látható, válassza ki a **Szolgáltatásvégpontokat**, jelölje be **Microsoft.Storage**, jelölje be **szolgáltatásvégpont-szabályzatra**, jelölje be  **myEndpointPolicy**, majd válassza ki **mentése**:

   ![Szabályzat hozzárendelése](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-associatepolicies.PNG)

>[!WARNING] 
>Más régiókban a szolgáltatási erőforrások hozzáférésének az alhálózatról, a hálózati biztonsági csoportok (NSG-k) alapján engedélyezett lesz. A hozzáférés korlátozása csak a végpont-régiók, korlátozza az NSG-k az endpoint régióban kizárólag-szolgáltatások forgalmára. Az NSG-k létrehozása a szolgáltatáscímkék régiónként további információkért lásd: [NSG-t az Azure-szolgáltatáscímkék.](manage-network-security-group.md?toc=%2fcreate-a-security-rule%2f.json)

Az alábbi példában az NSG-t csak Azure Storage-erőforrások régiója és WestUS2, az alacsonyabb prioritású szabály "Megtagadás minden" szabállyal eléréséhez korlátozott.

![Minden NSG megtagadása](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-nsg-rules.PNG)


## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban létrehozott egy szolgáltatásvégpont-szabályzatra, és hozzárendelte egy alhálózathoz. Szolgáltatásvégpont-szabályzat kapcsolatos további információkért lásd: [szolgáltatás végpontját a szabályzatok áttekintése.](virtual-network-service-endpoint-policies-overview.md)

