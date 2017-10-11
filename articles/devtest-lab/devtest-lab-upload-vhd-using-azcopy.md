---
title: "Töltse fel a VHD-fájlt a Azure DevTest Labs AzCopy használatával |} Microsoft Docs"
description: "AzCopy segítségével tesztlabor a tárfiók VHD-fájl feltöltése"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: tarcher
ms.openlocfilehash: a4f43354740d9f17570932b0b9c753f46d67dc33
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="upload-vhd-file-to-labs-storage-account-using-azcopy"></a>AzCopy segítségével tesztlabor a tárfiók VHD-fájl feltöltése

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

Azure DevTest Labs szolgáltatásban, a VHD-fájlok segítségével hozzon létre egyéni képek, amelyek segítségével virtuális gépeket. A következő lépések végigvezetik a VHD-fájl feltöltése a tesztkörnyezet tárfiókja az AzCopy parancssori segédprogram segítségével. Miután a VHD-fájl feltöltött a [további lépések szakaszt](#next-steps) felsorolja az egyes cikkeket, amelyek bemutatják, hogyan lehet a feltöltött VHD-fájl létrehozása egyéni lemezkép. További információ a lemezek és a VHD-ken az Azure-ban: [lemezek és a virtuális merevlemezek a virtuális gépek](../virtual-machines/linux/about-disks-and-vhds.md)

> [!NOTE] 
>  
> AzCopy csak Windows parancssori segédprogram.

## <a name="step-by-step-instructions"></a>Lépésenkénti utasítások

A következő lépések végigvezetik a VHD-fájl feltöltése a Azure DevTest Labs segítségével [AzCopy](http://aka.ms/downloadazcopy). 

1. Töltse le az Azure portál használatával a tesztkörnyezet tárfiókja nevét:

1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Válassza a **További szolgáltatások**, majd a **DevTest Labs** elemet a listából.

1. Válassza ki a kívánt labor labs listájának megtekintéséhez.  

1. A labor paneljén válassza **konfigurációs**. 

1. A tesztlabor a **konfigurációs** panelen válassza **egyéni lemezképeket (VHD)**.

1. Az a **egyéni lemezképek** panelen válasszon ki **+ Hozzáadás**. 

1. Az a **egyéni lemezkép** panelen válassza **VHD**.

1. Az a **VHD** panelen válassza **a PowerShell használatával virtuális merevlemez feltöltéséhez**.

    ![Töltse fel a virtuális merevlemez a PowerShell használatával](./media/devtest-lab-upload-vhd-using-azcopy/upload-image-using-psh.png)

1. A **PowerShell-lel lemezkép feltöltése a** csempe megjeleníti hívása a **Add-AzureVhd** parancsmag. Az első paraméter (*cél*) tartalmaz a blob-tároló URI-JÁNAK (*feltölt*) a következő formátumban:

    ```
    https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...
    ``` 

1. Jegyezze meg a teljes URI-címe, a későbbi lépésekben használatban van.

1. Töltse fel az AzCopy VHD-fájlt:
 
1. [Töltse le és telepítse a legújabb verziót az AzCopy](http://aka.ms/downloadazcopy).

1. Nyisson meg egy parancsablakot, és keresse meg az AzCopy telepítési könyvtárára. Másik lehetőségként az AzCopy telepítési helyet adhat hozzá a fájlrendszerbeli elérési. Alapértelmezés szerint a AzCopy telepítve van a következő könyvtárra:

    ```command-line
    %ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy
    ```

1. A fiók kulcs és a blob tároló URI használatával, a következő parancsot a parancssorba. A *vhdFileName* érték nem lehet idézőjelben. A folyamat egy VHD-fájl feltöltése megnőhet méretét a VHD-fájlt és a kapcsolat sebességétől függően.   

    ```command-line
    AzCopy /Source:<sourceDirectory> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Pattern:"<vhdFileName>" /BlobType:page
    ```

## <a name="next-steps"></a>Következő lépések

- [Létrehozhat egyéni rendszerképeket a VHD-fájl az Azure portál használata az Azure DevTest Labs szolgáltatásban](devtest-lab-create-template.md)
- [Egyéni lemezkép létrehozása a PowerShell használatával VHD-fájl az Azure DevTest Labs szolgáltatásban](devtest-lab-create-custom-image-from-vhd-using-powershell.md)