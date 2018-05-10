---
title: Az első virtuális gép létrehozása egy tesztkörnyezetben, a Azure DevTest Labs szolgáltatásban |} Microsoft Docs
description: Útmutató az első virtuális gép létrehozása a Azure DevTest Labs szolgáltatásban egy tesztkörnyezetben
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: fbc5a438-6e02-4952-b654-b8fa7322ae5f
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 0240bbdf2055ac44f8836adfaad3bf6c44893b77
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="create-your-first-vm-in-a-lab-in-azure-devtest-labs"></a>Az első virtuális gép létrehozása az Azure DevTest Labs szolgáltatásban egy tesztkörnyezetben

Kezdetben DevTest Labs eléréséhez és az első virtuális gép létrehozásához, akkor lesz valószínűleg megteheti segítségével előre betöltött [alap Piactéri lemezképhez](devtest-lab-configure-marketplace-images.md). Később a is képes lesz választhat egy [egyéni lemezképet, és egy képletet](devtest-lab-add-vm.md) további virtuális gépek létrehozásakor. 

Ez az oktatóanyag végigvezeti az első virtuális gép hozzáadása egy laborhoz a DevTest Labs szolgáltatásban az Azure portál használatával.

## <a name="steps-to-add-your-first-vm-to-a-lab-in-azure-devtest-labs"></a>Lépések végrehajtásával adja hozzá az első virtuális gép egy tesztkörnyezetet a Azure DevTest Labs szolgáltatásban
1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.
1. Labs listában jelölje ki a labor kívánja a virtuális gép létrehozása.  
1. A tesztlabor a **áttekintése** panelen válassza **+ Hozzáadás**.  

    ![Adja hozzá a virtuális gép gomb](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. Az a **base válasszon** panelen válassza a Piactéri lemezképet a virtuális gép számára.
1. Az a **virtuális gép** panelen adjon meg egy nevet az új virtuális gép a **virtuálisgép-nevet** szövegmezőben.

    ![Labor VM panel](./media/devtest-lab-add-vm/devtestlab-lab-add-first-vm.png)

1. Adjon meg egy **felhasználónév** kap, amely rendszergazdai jogosultságokkal a virtuális gépen.  
1. Adjon meg egy jelszót a szövegmező feliratú **írjon be egy értéket**.
1. A **virtuális gép lemeztípus** határozza meg, hogy milyen típusú a virtuális gépek a tesztkörnyezetben.
1. Válassza ki **virtuálisgép-méret** , és válassza ki a Processzormagok RAM memória méretét és a merevlemez mérete a virtuális gép létrehozásához adjon meg előre meghatározott elemek.
1. Válassza ki **összetevők** és - műtermék - listából válassza ki és konfigurálja az alapjául szolgáló lemezképhez hozzáadni kívánt összetevők.
    **Megjegyzés:** Ha ismerkedik a DevTest Labs szolgáltatásban, vagy tekintse meg az összetevők, konfigurálása a [meglévő összetevő felvétele a virtuális gépek](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) szakaszt, és térjen vissza ide befejezésekor.
1. Válassza ki **létrehozása** a megadott virtuális gép hozzáadása a labor.

   A tesztkörnyezet panel állapotát jeleníti meg a virtuális gép létrehozás - először mint **létrehozása**, majd, mint a **futtató** a virtuális gép elindítása után.

## <a name="next-steps"></a>További lépések
* A virtuális gép létrehozása után keresztül csatlakozhat a virtuális gép kiválasztásával **Connect** a virtuális gép paneljén.
* Tekintse meg [virtuális gép hozzáadása egy laborhoz](devtest-lab-add-vm.md) bővebb információt a további virtuális gépek hozzáadása a tesztkörnyezetben.
* Megismerkedhet a [Office DevTest Labs Azure Resource Manager gyorsindítási sablonok](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates).
