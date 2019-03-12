---
title: Az Azure DevTest Labs egyéni lemezkép készítése VHD-fájlból |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egyéni rendszerkép VHD-fájlból az Azure portal segítségével Azure DevTest Labs szolgáltatásban
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: b795bc61-7c28-40e6-82fc-96d629ee0568
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 853c138c8cf73b41b0cebb6c1d349865e18eab6a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57550105"
---
# <a name="create-a-custom-image-from-a-vhd-file"></a>Egy egyéni lemezkép készítése VHD-fájlból

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

## <a name="step-by-step-instructions"></a>Részletes útmutató

A következő lépések végigvezetik egy egyéni rendszerkép VHD-fájlból az Azure portal használatával:

1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.

1. Tesztkörnyezetek listájában jelölje ki a kívánt tesztkörnyezetben.  

1. Válassza ki a fő panelen a labor **Konfigurace a zásady**. 

1. Az a **Konfigurace a zásady** ablaktáblán válassza előbb **egyéni lemezképek**.

1. Az a **egyéni lemezképek** ablaktáblán válassza előbb **+ Hozzáadás**.

    ![Egyéni rendszerkép hozzáadása](./media/devtest-lab-create-template/add-custom-image.png)

1. Adja meg az egyéni rendszerkép nevét. Ez a név alaplemezképek listájában egy virtuális gép létrehozásakor.

1. Adja meg az egyéni rendszerkép leírása. A leírás alaplemezképek listája jelenik meg a virtuális gép létrehozásakor.

1. A **operációs rendszer típusa**, ezek közül bármelyikre **Windows** vagy **Linux**.

    - Ha **Windows**, adja meg a jelölőnégyzet keresztül e *sysprep* már futtatták azon a gépen. 
    - Ha **Linux**, adja meg a jelölőnégyzet keresztül e *megszüntetési* már futtatták azon a gépen. 

1. Válassza ki a **VHD** a legördülő menüből. Ez az a virtuális Merevlemezt, amely az új egyéni rendszerkép létrehozására használható. Ha szükséges, válassza ki a **Nahrát VHD pomocí PowerShell**.

1. Is megadhatja a csomag neve, csomag ajánlat és terv közzétevő Ha az az egyéni rendszerkép létrehozásához használt lemezkép nem licencelt kép (a Microsoft által közzétett).

   - **Csomag neve:** Adja meg a Piactéri rendszerkép (Termékváltozat) nevét, amelyre ez az egyéni rendszerkép létrehozása 
   - **Nabídka plánu:** Adja meg a termék (ajánlat), a Piactéri lemezkép, amelyből ez az egyéni rendszerkép létrehozása 
   - **Vydavatel plánu:** Adja meg a kiadó, a Piactéri lemezkép, amelyből ez az egyéni rendszerkép létrehozása

   > [!NOTE]
   > A kép, hozzon létre egy egyéni rendszerképet használ-e **nem** licencelt kép, akkor ezek a mezők üresek, és ha úgy dönt, kitöltött. Ha a kép **van** licencelt kép, majd a mezők a rendszer automatikusan kitölti a terv információit. Ha meg ebben az esetben módosítsa őket, egy figyelmeztető üzenet jelenik meg.
   >
   >

1. Válassza ki **OK** az egyéni rendszerkép létrehozására.

Néhány perc elteltével az egyéni rendszerkép jön létre, és a labor tárfiók belül tárolja. A lab-felhasználó használni szeretne egy új virtuális gép létrehozása, amikor a lemezkép alaplemezképek listája érhető el.

![Egyéni lemezkép érhető el az alap-rendszerképek listája](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Kapcsolódó blogbejegyzések

- [Egyéni rendszerképek vagy képleteket?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Egyéni rendszerképek az Azure DevTest Labs közötti másolása](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>További lépések

- [A labor virtuális gép hozzáadása](./devtest-lab-add-vm.md)
