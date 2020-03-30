---
title: Hozzon létre egy Azure DevTest Labs egyéni lemezképet egy virtuális merevlemez-fájlból | Microsoft dokumentumok
description: Ismerje meg, hogyan hozhat létre egyéni lemezképet az Azure DevTest Labs-ben egy virtuális merevlemez-fájlból az Azure Portalon keresztül
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61296055"
---
# <a name="create-a-custom-image-from-a-vhd-file"></a>Egyéni kép létrehozása VHD-fájlból

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

## <a name="step-by-step-instructions"></a>Lépésenkénti utasítások

A következő lépések végigvezetik egy egyéni lemezkép létrehozásán egy Virtuális merevlemez-fájlból az Azure Portalon keresztül:

1. Jelentkezzen be az [Azure Portalra.](https://go.microsoft.com/fwlink/p/?LinkID=525040)

1. Válassza a **Minden szolgáltatás**lehetőséget, majd a listából válassza a **DevTest Labs** elemet.

1. A laborok listájából válassza ki a kívánt labort.  

1. A tesztkörnyezet fő ablaktábláján válassza a **Konfiguráció és házirendek**lehetőséget. 

1. A **Konfiguráció és házirendek** ablaktáblán válassza az **Egyéni képek**lehetőséget.

1. Az **Egyéni képek** ablaktáblán válassza a **+Hozzáadás**lehetőséget.

    ![Egyéni kép hozzáadása](./media/devtest-lab-create-template/add-custom-image.png)

1. Adja meg az egyéni kép nevét. Ez a név jelenik meg az alaplemezképek listájában virtuális gép létrehozásakor.

1. Adja meg az egyéni kép leírását. Ez a leírás az alaplemezképek listájában jelenik meg virtuális gép létrehozásakor.

1. Az **operációs rendszer típusához**válassza a **Windows** vagy **a Linux**lehetőséget.

    - Ha a **Windows**lehetőséget választja, adja meg a jelölőnégyzetben, hogy a *sysprep* futtatva van-e a számítógépen. 
    - Ha **a Linux**lehetőséget választja, adja meg a jelölőnégyzetben, hogy a *megszüntetés* futtatása a számítógépen történt-e. 

1. Válasszon **egy virtuális merevlemezt** a legördülő menüből. Ez a virtuális merevlemez, amely az új egyéni lemezkép létrehozásához használható. Szükség esetén válassza **a Virtuális merevlemez feltöltése a PowerShell használatával**lehetőséget.

1. Megadhat tervnevet, csomagajánlatot és tervközzétevőt is, ha az egyéni lemezkép létrehozásához használt lemezkép nem licencelt lemezkép (amelyet a Microsoft tett közzé).

   - **Terv neve:** Adja meg annak a Piactér-lemezképnek (SKU) a nevét, amelyből ez az egyéni lemezkép létrejön. 
   - **Terv ajánlat:** Adja meg annak a Piactér-képnek a termékét (ajánlatát), amelyből ez az egyéni lemezkép készült. 
   - **A terv kiadója:** Adja meg annak a Piactér-lemezképnek a közzétevőit, amelyből ez az egyéni lemezkép készült.

   > [!NOTE]
   > Ha az egyéni lemezkép létrehozásához használt kép **nem** licencelt kép, akkor ezek a mezők üresek, és ha úgy dönt, kitölthetők. Ha a **lemezkép** licencelt lemezkép, akkor a mezők automatikusan fel lesznek töltve a terv adataival. Ha ebben az esetben megpróbálja módosítani őket, figyelmeztető üzenet jelenik meg.
   >
   >

1. Az egyéni lemezkép létrehozásához válassza az **OK gombot.**

Néhány perc múlva létrejön az egyéni lemezkép, és a labor tárfiókban tárolja. Ha egy tesztkörnyezet-felhasználó új virtuális gép létrehozásához szeretne létrehozni, a lemezkép elérhető az alaplemezképek listájában.

![Az alapképek listájában elérhető egyéni kép](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Kapcsolódó blogbejegyzések

- [Egyéni képek vagy képletek?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Egyéni lemezképek másolása az Azure DevTest Labs között](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>További lépések

- [Virtuális gép hozzáadása a laborhoz](./devtest-lab-add-vm.md)
