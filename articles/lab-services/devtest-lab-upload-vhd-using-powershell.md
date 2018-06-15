---
title: Töltse fel a VHD-fájlt a PowerShell használatával Azure DevTest Labs |} Microsoft Docs
description: Töltse fel a VHD-fájlt a tesztlabor a tárfiók PowerShell használatával
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: ea2687c61239e893f46dfa12c5b822a51823e1f3
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788731"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-powershell"></a>Töltse fel a VHD-fájlt a tesztlabor a tárfiók PowerShell használatával

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

Azure DevTest Labs szolgáltatásban, a VHD-fájlok segítségével hozzon létre egyéni képek, amelyek segítségével virtuális gépeket. A következő lépések végigvezetik a tesztkörnyezet tárfiókja egy VHD-fájl feltöltése a PowerShell használatával. Miután a VHD-fájl feltöltött a [további lépések szakaszt](#next-steps) felsorolja az egyes cikkeket, amelyek bemutatják, hogyan lehet a feltöltött VHD-fájl létrehozása egyéni lemezkép. További információ a lemezek és a VHD-ken az Azure-ban: [lemezek és a virtuális merevlemezek a virtuális gépek](../virtual-machines/linux/about-disks-and-vhds.md)

## <a name="step-by-step-instructions"></a>Részletes útmutató

A következő lépések végigvezetik a VHD-fájl feltöltése a PowerShell használatával Azure DevTest Labs. 

1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.

1. Válassza ki a kívánt labor labs listájának megtekintéséhez.  

1. A labor paneljén válassza **konfigurációs**. 

1. A tesztlabor a **konfigurációs** panelen válassza **egyéni lemezképeket (VHD)**.

1. Az a **egyéni lemezképek** panelen válasszon ki **+ Hozzáadás**. 

1. Az a **egyéni lemezkép** panelen válassza **VHD**.

1. Az a **VHD** panelen válassza **a PowerShell használatával virtuális merevlemez feltöltéséhez**.

    ![Töltse fel a virtuális merevlemez a PowerShell használatával](./media/devtest-lab-upload-vhd-using-powershell/upload-image-using-psh.png)

1. Az a **PowerShell-lel lemezkép feltöltése a** panelen másolja a létrehozott PowerShell-parancsfájl egy szövegszerkesztőben.

1. Módosítsa a **LocalFilePath** paramétere a **Add-AzureVhd** parancsmag a feltölteni kívánt VHD-fájl helyére mutasson.

1. Egy PowerShell-parancssorba, futtassa a **Add-AzureVhd** parancsmag (a módosított rendelkező **LocalFilePath** paraméter).

> [!WARNING] 
> 
> A folyamat egy VHD-fájl feltöltése megnőhet méretét a VHD-fájlt és a kapcsolat sebességétől függően.

## <a name="next-steps"></a>További lépések

- [Létrehozhat egyéni rendszerképeket a VHD-fájl az Azure portál használata az Azure DevTest Labs szolgáltatásban](devtest-lab-create-template.md)
- [Egyéni lemezkép létrehozása a PowerShell használatával VHD-fájl az Azure DevTest Labs szolgáltatásban](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
