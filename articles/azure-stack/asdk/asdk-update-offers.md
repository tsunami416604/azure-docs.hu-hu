---
title: "Ebben az oktatóanyagban elsajátíthatja, hogyan frissítheti a Azure verem ajánlatok és tervek |} Microsoft Docs"
description: "A cikk ismerteti a meglévő Azure verem ajánlatok és tervek megtekintése és módosítása."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 3b5d2ab5e924f578f5838d11b0d2058aacf67dec
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-update-offers-and-plans"></a>Oktatóanyag: ajánlatok és csomagok frissítése
Azure verem kezelőként létrehozhat, amelyek tartalmazzák a kívánt szolgáltatások és a felhasználók számára előfizetés vonatkozó kvótákat tervek. Ezek *tervek kiinduló* a felhasználók számára az alapvető szolgáltatásokat tartalmazzák, és csak akkor ajánlat / egy referenciaterv. Ha módosítania kell az ajánlatot, akkor használhatja *bővítmény tervek* , amely lehetővé teszi a számítógép, tárolási, kiterjeszteni terv módosítása, vagy hálózati kvóták kezdetben a referenciaterv az ajánlott. 

Bár a egyesítésével mindent a Összevonhat egyazon csomagba néhány esetben optimális, érdemes lehet tervezze meg, és a további szolgáltatások bővítmény tervek segítségével base rendelkezik. Például sikerült mellett dönt IaaS szolgáltatást kínál a referenciaterv részeként az összes PaaS services bővítmény tervek számít. Terveket is a korlátozott ASDK környezetben erőforrások fogyasztásának vezérlésére használható. Például ha azt szeretné, hogy a felhasználók az erőforrás-használat szem előtt tartva, lehet viszonylag kicsi referenciaterv (attól függően, hogy a szükséges szolgáltatások), és felhasználói kapacitás elérni, mint azok volna riasztást kap, hogy által már igénybe vett erőforrások lefoglalása a hozzárendelt terv alapján. Ott a felhasználók további erőforrások elérhető bővítmény tervének előfordulhat, hogy ki. 

> [!NOTE]
> Ha egy felhasználó egy bővítmény terv hozzáadja a meglévő ajánlat előfizetése, a további erőforrások sikerült eltarthat egy órát jelennek meg. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Egy bővítmény terv létrehozása 
> * A bővítmény csomag előfizetés

## <a name="create-an-add-on-plan"></a>Egy bővítmény terv létrehozása
**Bővítmény tervek** meglévő ajánlatot módosításával jönnek létre.

1. Jelentkezzen be a [verem Azure portal](https://adminportal.local.azurestack.external) felhő rendszergazdaként.
2. A korábban használt lépésekkel [hozzon létre egy referenciaterv](asdk-offer-services.md) korábban nem kínált szolgáltatásokat kínáló új terv létrehozásához. Ebben a példában a Key Vault (Microsoft.KeyVault) szolgáltatások szerepelni fog a tervet.
3. A felügyeleti portálon kattintson **kínál** , és válassza a lefokozásra szolgáló bővítmény tervének frissíteni kell.

   ![](media/asdk-update-offers/1.PNG)

4.  Az ajánlat tulajdonságok alján görgessen és válassza ki **bővítmény tervek**. Kattintson a **Hozzáadás** parancsra.
   
    ![](media/asdk-update-offers/2.PNG)

5. Jelölje ki a hozzáadni. Az ebben a példában a terv neve **Key vault terv**, és kattintson a **válasszon** a terv hozzáadása az ajánlat. A terv hozzáadásának a ajánlatra sikeresen értesítést kell kapnia.
   
    ![](media/asdk-update-offers/3.PNG)

6. Tekintse át a bővítmény győződjön meg arról, hogy az új bővítmény megtervezése ajánlatot mellékelt tervek listáját.
   
    ![](media/asdk-update-offers/4.PNG)

## <a name="subscribe-to-the-add-on-plan"></a>A bővítmény csomag előfizetés
Kell egy meglévő Azure verem előfizetéshez egy bővítmény tervvel kiterjeszteni a Azure verem felhasználói portálra történő bejelentkezéshez.

Használja ezeket a lépéseket az Azure-verem üzemeltető amilyenben további erőforrásokat derítse fel, és adja hozzá egy bővítmény terv korábban előfizetett ajánlatot.

1. Jelentkezzen be a [felhasználói portál](https://portal.local.azurestack.external).
2. Az előfizetés kiterjesztése bővítmény tervvel megkereséséhez kattintson **további szolgáltatások**, kattintson a **előfizetések**, majd jelölje ki az előfizetését.
   
    ![](media/asdk-update-offers/5.PNG)

3.  Az előfizetés áttekintésben kattintson **Hozzáadás terv**.
   
    ![](media/asdk-update-offers/6.PNG)

4. A Hozzáadás terv panelen válassza ki a bővítmény terv hozzáadásához az előfizetéshez. Ebben a példában **Key vault terv** van kiválasztva. Ezután a rendszer elküld egy értesítés, hogy a bővítmény terv nem sikeres szerezte be, valamint, hogy szeretné-e a portál hozzáférjen a frissített előfizetés frissítése.
   
    ![](media/asdk-update-offers/7.PNG)

5. Végül tekintse át a bővítmény tervezi ahhoz az előfizetéshez tartozik társított győződjön meg arról, hogy a bővítmény terv sikeresen hozzáadva.
   
    ![](media/asdk-update-offers/8.PNG)


## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Egy bővítmény terv létrehozása 
> * A bővítmény csomag előfizetés

> [!div class="nextstepaction"]
> [A virtuális gép létrehozása sablonból](asdk-create-vm-template.md)

