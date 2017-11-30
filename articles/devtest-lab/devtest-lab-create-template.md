---
title: "A VHD-fájl Azure DevTest Labs egyéni lemezkép létrehozása |} Microsoft Docs"
description: "Útmutató egyéni lemezkép létrehozása a Azure DevTest Labs szolgáltatásban a VHD-fájl az Azure portál használatával"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: b795bc61-7c28-40e6-82fc-96d629ee0568
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: tarcher
ms.openlocfilehash: a421327ab8794315005327833b873dc5ebd57e9e
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2017
---
# <a name="create-a-custom-image-from-a-vhd-file"></a>Létrehozhat egyéni rendszerképeket a VHD-fájl

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

## <a name="step-by-step-instructions"></a>Részletes útmutató

A következő lépések végigvezetik a VHD-fájl az Azure portál használatával egyéni lemezkép létrehozása:

1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Válassza a **További szolgáltatások**, majd a **DevTest Labs** elemet a listából.

1. Válassza ki a kívánt labor labs listájának megtekintéséhez.  

1. A labor paneljén válassza **konfigurációs**. 

1. A tesztlabor a **konfigurációs** panelen válassza **egyéni lemezképeket (VHD)**.

1. Az a **egyéni lemezképek** panelen válassza **+ Hozzáadás**.

    ![Egyéni lemezkép hozzáadása](./media/devtest-lab-create-template/add-custom-image.png)

1. Adja meg az egyéni lemezkép nevét. Ez a név alap képek listájának a virtuális gép létrehozásakor.

1. Adja meg az egyéni lemezkép leírását. A leírást alap képek listája jelenik meg a virtuális gépek létrehozásakor.

1. Válassza ki **VHD**.

1. Az a **VHD** panelen válassza ki a kívánt VHD-fájlt.

1. Válassza ki **OK** bezárásához a **VHD** panelen.

1. Válassza ki **operációs rendszer konfigurációja**.

1. Az a **operációs rendszer konfigurációja** lapra, válassza ki vagy **Windows** vagy **Linux**.

1. Ha **Windows** van jelölve, adja meg a jelölőnégyzet keresztül e *Sysprep* már futtatták azon a gépen. 

1. Válassza ki **OK** bezárásához a **operációs rendszer konfigurációja** panelen.

1. Válassza ki **OK** egyéni lemezkép létrehozásához.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Kapcsolódó blogbejegyzések

- [Egyéni lemezképek vagy képletek?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Az Azure DevTest Labs között egyéni lemezképek másolása](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

##<a name="next-steps"></a>Következő lépések

- [A virtuális gépek hozzáadása a tesztkörnyezet](./devtest-lab-add-vm.md)
