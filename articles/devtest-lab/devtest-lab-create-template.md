---
title: "A VHD-fájl Azure DevTest Labs egyéni lemezkép létrehozása |} Microsoft Docs"
description: "Útmutató egyéni lemezkép létrehozása a Azure DevTest Labs szolgáltatásban a VHD-fájl az Azure portál használatával"
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: b795bc61-7c28-40e6-82fc-96d629ee0568
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: v-craic
ms.openlocfilehash: d20e92d16309f998b4979549997874a80a3ea2dd
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/20/2018
---
# <a name="create-a-custom-image-from-a-vhd-file"></a>Létrehozhat egyéni rendszerképeket a VHD-fájl

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

## <a name="step-by-step-instructions"></a>Részletes útmutató

A következő lépések végigvezetik a VHD-fájl az Azure portál használatával egyéni lemezkép létrehozása:

1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.

1. Válassza ki a kívánt labor labs listájának megtekintéséhez.  

1. A labor főpanelen válassza **konfigurációs és házirendek**. 

1. Az a **konfigurációs és házirendek** ablaktáblán válassza előbb **egyéni lemezképek**.

1. Az a **egyéni lemezképek** ablaktáblán válassza előbb **+ Hozzáadás**.

    ![Egyéni lemezkép hozzáadása](./media/devtest-lab-create-template/add-custom-image.png)

1. Adja meg az egyéni lemezkép nevét. Ez a név alap képek listájának a virtuális gép létrehozásakor.

1. Adja meg az egyéni lemezkép leírását. A leírást alap képek listája jelenik meg a virtuális gépek létrehozásakor.

1. A **operációsrendszer-típus**, válassza **Windows** vagy **Linux**.

    - Ha **Windows**, adja meg a jelölőnégyzet keresztül e *sysprep* már futtatták azon a gépen. 
    - Ha **Linux**, adja meg a jelölőnégyzet keresztül e *deprovision* már futtatták azon a gépen. 

1. Válassza ki a **VHD** a legördülő menüből. Ez az új egyéni lemezkép létrehozásához használandó virtuális merevlemez. Ha szükséges, válassza a **a PowerShell használatával virtuális merevlemez feltöltéséhez**.

1. Ha egyéni lemezkép létrehozásához használt kép nem egy licencelt (a Microsoft által kiadott) a csomag neve, a terv ajánlat és a terv publisher is beírhatja.

   - **Terv neve:** a Piactéri lemezkép (SKU) nevét adja meg az egyéni lemezkép létrehozása 
   - **Tervezze meg az ajánlat:** adja meg a termék (ajánlat) a Piactéri lemezkép, amelyből a egyéni lemezkép létrehozása 
   - **Tervezze meg a kiadó:** a gyártót a Piactéri lemezkép, amelyből a egyéni lemezkép létrehozása

   > [!NOTE]
   > Ha van-e a lemezkép segítségével létrehozhat egyéni rendszerképeket **nem** licencelt lemezképet, majd ezeket a mezőket üres, és amennyiben úgy dönt, tölti. Ha a kép **van** licencelt lemezképet, majd a mezők automatikus terv adatokkal feltöltve. Ha megpróbálja módosítani őket ebben az esetben, egy figyelmeztető üzenet jelenik meg.
   >
   >

1. Válassza ki **OK** egyéni lemezkép létrehozásához.

Néhány perc elteltével az egyéni rendszerképet jön létre, és a tesztkörnyezet tárfiókja belül tárolja. Hozzon létre egy új virtuális Gépet kíván lab-felhasználó, amikor a kép érhető el alap képek listájában.

![Kép: egyéni elérhető alap képek listája](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Kapcsolódó blogbejegyzések

- [Egyéni lemezképek vagy képletek?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Az Azure DevTest Labs között egyéni lemezképek másolása](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>További lépések

- [A virtuális gépek hozzáadása a tesztkörnyezet](./devtest-lab-add-vm.md)
