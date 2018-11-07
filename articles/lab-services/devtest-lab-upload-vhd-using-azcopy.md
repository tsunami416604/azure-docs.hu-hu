---
title: VHD-fájl feltöltése AzCopy használatával az Azure DevTest Labs |} A Microsoft Docs
description: VHD-fájl feltöltése AzCopy használatával tesztkörnyezet tárfiókba
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
ms.openlocfilehash: 482bfe41ebfec7353466f7e243e9e767caee7828
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51228283"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-azcopy"></a>VHD-fájl feltöltése AzCopy használatával tesztkörnyezet tárfiókba

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

Az Azure DevTest Labs szolgáltatásban a VHD-fájlok létrehozása az egyéni lemezképek, amelyek segítségével a virtuális gépek üzembe helyezése használható. A következő lépések végigvezetik az AzCopy parancssori segédprogram használatával töltse fel a VHD-fájl egy tesztkörnyezet egy tárfiókba. Miután feltöltötte a VHD-fájl a [további lépések szakaszt](#next-steps) felsorolja az egyes cikkeket, amelyek bemutatják, hogyan hozhat létre egy egyéni rendszerképet a feltöltött VHD-fájlból. Lemezek és virtuális merevlemezek, az Azure-ban kapcsolatos további információkért lásd: [lemezek és virtuális gépek virtuális merevlemezeinek ismertetése](../virtual-machines/linux/about-disks-and-vhds.md)

> [!NOTE] 
>  
> Az AzCopy csak Windows parancssori segédprogram.

## <a name="step-by-step-instructions"></a>Részletes útmutató

A következő lépések végigvezetik egy VHD-fájl feltöltése az Azure DevTest Labs használatával [AzCopy](https://aka.ms/downloadazcopy). 

1. Kérje le a labor létrehozása az Azure portal használatával storage-fiók neve:

1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.

1. Tesztkörnyezetek listájában jelölje ki a kívánt tesztkörnyezetben.  

1. A labor paneljén válassza ki a **konfigurációs**. 

1. A laborgyakorlat **konfigurációs** panelen válassza ki **egyéni lemezképeket (VHD)**.

1. Az a **egyéni lemezképek** panelen válasszon ki **+ Hozzáadás**. 

1. Az a **egyéni lemezkép** panelen válassza ki **VHD**.

1. Az a **VHD** panelen válassza ki **Nahrát VHD pomocí PowerShell**.

    ![Töltse fel a VHD pomocí Powershellu](./media/devtest-lab-upload-vhd-using-azcopy/upload-image-using-psh.png)

1. A **Nahrát image pomocí PowerShell** panel megjeleníti a hívást a **Add-AzureVhd** parancsmagot. Az első paraméterrel (*cél*) tartalmaz a blob-tároló URI azonosítója (*feltölti*) a következő formátumban:

    ```
    https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...
    ``` 

1. Jegyezze fel a teljes URI-t, a későbbi lépésekben szolgál.

1. A virtuális merevlemez fájl feltöltése AzCopy használatával:
 
1. [Töltse le és telepítse az AzCopy legújabb verzióját](https://aka.ms/downloadazcopy).

1. Nyisson meg egy parancsablakot, és keresse meg az AzCopy telepítési könyvtárára. Igény szerint is hozzáadhat az AzCopy telepítési hely a rendszerbeli elérési úton. Alapértelmezés szerint az AzCopy a következő könyvtárba van telepítve:

    ```command-line
    %ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy
    ```

1. A tárfiók kulcsát, és a blob tárolójának URI-t használ, futtassa a következő parancsot a parancssorba. A *vhdFileName* értéknek kell lennie az idézőjelek között. A VHD-fájlt, és az átviteli sebesség méretétől függően a folyamat egy VHD-fájl feltöltése megnőhet.   

    ```command-line
    AzCopy /Source:<sourceDirectory> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Pattern:"<vhdFileName>" /BlobType:page
    ```

## <a name="next-steps"></a>További lépések

- [Egy egyéni lemezkép készítése VHD-fájlból az Azure portal segítségével Azure DevTest Labs szolgáltatásban](devtest-lab-create-template.md)
- [Egyéni rendszerkép létrehozása a PowerShell-lel VHD-fájlból az Azure DevTest Labs szolgáltatásban](devtest-lab-create-custom-image-from-vhd-using-powershell.md)