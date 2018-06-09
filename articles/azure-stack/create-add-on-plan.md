---
title: Ebből a cikkből megismerheti, hogyan frissítheti a Azure verem ajánlatok és tervek |} Microsoft Docs
description: A cikk ismerteti a meglévő Azure verem ajánlatok és tervek megtekintése és módosítása.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.custom: mvc
ms.date: 06/07/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: a84148a3ac31d51ff30cebffab00e5fec8fdaa87
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35238416"
---
# <a name="azure-stack-add-on-plans"></a>Az Azure verem bővítmény tervek
Azure verem kezelőként létrehozhat, amelyek tartalmazzák a kívánt szolgáltatások és a felhasználók számára előfizetés vonatkozó kvótákat tervek. Ezek [ *tervek kiinduló* ](azure-stack-create-plan.md) a felhasználók számára az alapvető szolgáltatásokat tartalmazzák, és csak akkor ajánlat / egy referenciaterv. Ha módosítania kell az ajánlatot, akkor használhatja *bővítmény tervek* , amely lehetővé teszi a számítógép, tárolási, kiterjeszteni terv módosítása, vagy hálózati kvóták kezdetben a referenciaterv az ajánlott. 

Bár a egyesítésével mindent a Összevonhat egyazon csomagba néhány esetben optimális, érdemes lehet tervezze meg, és a további szolgáltatások bővítmény tervek segítségével base rendelkezik. Például sikerült mellett dönt IaaS szolgáltatást kínál a referenciaterv részeként az összes PaaS services bővítmény tervek számít. Terveket is használható az Azure-verem környezetében erőforrások fogyasztásának szabályozására. Például ha azt szeretné, hogy a felhasználók az erőforrás-használat szem előtt tartva, lehet viszonylag kicsi referenciaterv (attól függően, hogy a szükséges szolgáltatások), és felhasználói kapacitás elérni, mint azok volna riasztást kap, hogy által már igénybe vett erőforrások lefoglalása a hozzárendelt terv alapján. Ott a felhasználók további erőforrások elérhető bővítmény tervének előfordulhat, hogy ki. 

> [!NOTE]
> Ha egy felhasználó egy bővítmény terv hozzáadja a meglévő ajánlat előfizetése, a további erőforrások sikerült eltarthat egy órát jelennek meg. 

## <a name="create-an-add-on-plan"></a>Egy bővítmény terv létrehozása
Bővítmény tervek meglévő ajánlatot módosításával jönnek létre:

1. Jelentkezzen be a verem Azure felügyeleti portálján a felhőalapú rendszergazdaként.
2. Ugyanezen lépések segítségével [hozzon létre egy új referenciaterv](azure-stack-create-plan.md) korábban nem kínált szolgáltatásokat kínáló új terv létrehozásához. Ebben a példában a Key Vault (Microsoft.KeyVault) szolgáltatások szerepelni fog az új tervet.
3. A felügyeleti portálon kattintson **kínál** , és válassza a lefokozásra szolgáló bővítmény tervének frissíteni kell.

   ![](media/create-add-on-plan/1.PNG)

4.  Az ajánlat tulajdonságok alján görgessen és válassza ki **bővítmény tervek**. Kattintson a **Hozzáadás** parancsra.
   
    ![](media/create-add-on-plan/2.PNG)

5. Jelölje ki a hozzáadni. Az ebben a példában a terv neve **Key vault terv**, és kattintson a **válasszon** a terv hozzáadása az ajánlat. A terv hozzáadásának a ajánlatra sikeresen értesítést kell kapnia.
   
    ![](media/create-add-on-plan/3.PNG)

6. Tekintse át a bővítmény győződjön meg arról, hogy az új bővítmény megtervezése ajánlatot mellékelt tervek listáját.
   
    ![](media/create-add-on-plan/4.PNG)

## <a name="next-steps"></a>További lépések
[Ajánlat létrehozása](azure-stack-create-offer.md)