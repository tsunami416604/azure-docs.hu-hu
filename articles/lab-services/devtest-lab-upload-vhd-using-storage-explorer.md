---
title: VHD-fájl feltöltése a Microsoft Azure Storage Explorer használatával Azure DevTest Labs |} A Microsoft Docs
description: A Microsoft Azure Storage Explorer használatával tesztkörnyezet tárfiók VHD-fájl feltöltése
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
ms.openlocfilehash: 55e8f536419f102f8f0fe69773ed486407c50cfb
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2019
ms.locfileid: "56328860"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-microsoft-azure-storage-explorer"></a>A Microsoft Azure Storage Explorer használatával tesztkörnyezet tárfiók VHD-fájl feltöltése

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

Az Azure DevTest Labs szolgáltatásban a VHD-fájlok létrehozása az egyéni lemezképek, amelyek segítségével a virtuális gépek üzembe helyezése használható. Ez a cikk bemutatja, hogyan használhatja [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) egy VHD-fájl feltöltése egy tesztkörnyezet egy tárfiókba. Miután feltöltötte a VHD-fájl a [további lépések szakaszt](#next-steps) felsorolja az egyes cikkeket, amelyek bemutatják, hogyan hozhat létre egy egyéni rendszerképet a feltöltött VHD-fájlból. Lemezek és virtuális merevlemezek, az Azure-ban kapcsolatos további információkért lásd: [Bevezetés a managed Disks szolgáltatásba](../virtual-machines/linux/managed-disks-overview.md)

## <a name="step-by-step-instructions"></a>Részletes útmutató

A következő lépések végigvezetik egy VHD-fájl feltöltése a DevTest Labs használatával [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).

1. [Töltse le és telepítse a Microsoft Azure Storage Explorer legújabb verziójának](http://www.storageexplorer.com).

1. Kérje le a labor létrehozása az Azure portal használatával storage-fiók neve:

    1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).
    
    1. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.
    
    1. Tesztkörnyezetek listájában jelölje ki a kívánt tesztkörnyezetben.  
    
    1. A labor paneljén válassza ki a **konfigurációs**. 
    
    1. A laborgyakorlat **konfigurációs** panelen válassza ki **egyéni lemezképeket (VHD)**.
    
    1. Az a **egyéni lemezképek** panelen válasszon ki **+ Hozzáadás**. 
    
    1. Az a **egyéni lemezkép** panelen válassza ki **VHD**.
    
    1. Az a **VHD** panelen válassza ki **Nahrát VHD pomocí PowerShell**.
    
        ![Töltse fel a VHD pomocí Powershellu][0]
    
    1. A **Nahrát image pomocí PowerShell** panel megjeleníti a hívást a **Add-AzureVhd** parancsmagot. Az első paraméterrel (*cél*) tartalmazza a tárfiók nevét a tesztkörnyezethez a következő formátumban:
    
        https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/... 

    1. Jegyezze fel a tárfiók nevét, a későbbi lépésekben szolgál.
    
1. Csatlakozhat egy Azure-előfizetése fiókját a Storage Explorer használatával.

    > [!TIP] 
    > 
    > Storage Explorer kapcsolati beállításokat támogatja. Ez a szakasz azt mutatja be, csatlakozhat egy Azure-előfizetéséhez társított storage-fiókot. A Storage Explorer által támogatott egyéb kapcsolati beállítások megjelenítéséhez, tekintse meg a cikk [Ismerkedés a Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).
 
    1. Nyissa meg a Storage Explorert.
    
    1. A Storage Explorerben válassza ki a **Azure-fiók beállításai**. 
    
        ![Azure-fiók beállításai][1]
    
    1. A bal oldali ablaktábla megjeleníti a Microsoft-fiókkal, amelybe bejelentkezett. Egy másik fiókhoz csatlakozáshoz válassza a **Fiók hozzáadása** lehetőséget, és a párbeszédpanelek útmutatásait követve jelentkezzen be egy olyan Microsoft-fiókkal, amely legalább egy aktív Azure-előfizetéssel társítva van.
    
        ![Fiók hozzáadása][2]
    
    1. Amint sikeresen bejelentkezett egy Microsoft-fiókkal, a bal oldali ablaktáblán megjelenik a fiókhoz társított összes Azure-előfizetés. Válassza ki azt az Azure-előfizetést amellyel dolgozni szeretne, majd válassza az **Alkalmaz** lehetőséget. (Kiválasztásával **minden előfizetés** be-vagy kikapcsolja a választott összes vagy a felsorolt Azure-előfizetést.)
    
        ![Azure-előfizetések kiválasztása][3]
    
    1. A bal oldali ablaktábla megjeleníti a kiválasztott Azure-előfizetésekhez társított összes tárfiókot.
    
        ![Kiválasztott Azure-előfizetések][4]

1. Keresse meg a storage-fiókot a labor létrehozása:

    1. A Storage Explorer bal oldali ablaktáblán keresse meg, és bontsa ki a csomópontot az Azure-előfizetés, amely a labor tulajdonosa.
    
    1. Az előfizetési csomópontot, bontsa ki a **Tárfiókok**.

    1. Bontsa ki a fiók tárolócsomópont a labor-csomópont felfedéséhez **Blobtárolók**, **fájlmegosztások**, **üzenetsorok**, és **táblák**.
    
    1. Bontsa ki a **Blobtárolók** csomópont.
    
    1. Válassza ki a feltöltések blobtárolót a tartalom megjelenítéséhez a jobb oldali ablaktáblán.
        
        ![Töltse fel a könyvtár][5]

1. A Storage Explorer használatával VHD-fájl feltöltése:

    1. A Storage Explorer jobb oldali ablaktáblában lévő blobok listája megjelenik a **feltölti** a labor storage-fiók blob-tárolóba. Válassza a blob szerkesztő eszköztár **feltöltése** 
        
        ![Feltöltés gomb][6]
    
    1. Az a **feltöltése** legördülő menüjében válassza **fájlok feltöltése...** .
    
    1. Az a **fájlok feltöltése** párbeszédpanelen kattintson a három pontra.
        
        ![Fájl kiválasztása][8]  

    1. Az a **feltölteni kívánt fájlok kiválasztása** párbeszédpanelen keresse meg a kívánt VHD-fájlt, jelölje ki azt, és válassza **nyílt**.
    
    1. Amikor visszatér a **fájlok feltöltése** párbeszédpanelen módosítsa **Blob típusát** való **Lapblob**.
    
    1. Válassza a **Feltöltés** lehetőséget.

        ![Fájl kiválasztása][9]  
    
    1. A Storage Explorer **tevékenységnapló** panelen látható a letöltés állapota (valamint hivatkozások a feltöltés megszakításához). A VHD-fájlt, és az átviteli sebesség méretétől függően a folyamat egy VHD-fájl feltöltése megnőhet. 

        ![Fájlfeltöltés állapot][10]  

## <a name="next-steps"></a>További lépések

- [Egy egyéni lemezkép készítése VHD-fájlból az Azure portal segítségével Azure DevTest Labs szolgáltatásban](devtest-lab-create-template.md)
- [Egyéni rendszerkép létrehozása a PowerShell-lel VHD-fájlból az Azure DevTest Labs szolgáltatásban](devtest-lab-create-custom-image-from-vhd-using-powershell.md)

[0]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-image-using-psh.png
[1]: ./media/devtest-lab-upload-vhd-using-storage-explorer/settings-icon.png
[2]: ./media/devtest-lab-upload-vhd-using-storage-explorer/add-account-link.png
[3]: ./media/devtest-lab-upload-vhd-using-storage-explorer/subscriptions-list.png
[4]: ./media/devtest-lab-upload-vhd-using-storage-explorer/storage-accounts-list.png
[5]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-dir.png
[6]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-button.png
[7]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-files.png
[8]: ./media/devtest-lab-upload-vhd-using-storage-explorer/select-file.png
[9]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-file.png
[10]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-status.png
