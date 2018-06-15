---
title: VHD-fájl feltöltése az Azure DevTest Labs segítségével a Microsoft Azure Tártallózó |} Microsoft Docs
description: Használatával a Microsoft Azure Tártallózó tesztlabor a tárfiók VHD-fájl feltöltése
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
ms.openlocfilehash: cafb3ec2da335165a3b66c18d83930008c6d72e5
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788045"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-microsoft-azure-storage-explorer"></a>Használatával a Microsoft Azure Tártallózó tesztlabor a tárfiók VHD-fájl feltöltése

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

Azure DevTest Labs szolgáltatásban, a VHD-fájlok segítségével hozzon létre egyéni képek, amelyek segítségével virtuális gépeket. Ez a cikk bemutatja, hogyan használandó [Microsoft Azure Tártallózó](../vs-azure-tools-storage-manage-with-storage-explorer.md) egy VHD-fájlt feltölteni a tesztkörnyezet tárfiókja. Miután a VHD-fájl feltöltött a [további lépések szakaszt](#next-steps) felsorolja az egyes cikkeket, amelyek bemutatják, hogyan lehet a feltöltött VHD-fájl létrehozása egyéni lemezkép. További információ a lemezek és a VHD-ken az Azure-ban: [lemezek és a virtuális merevlemezek a virtuális gépek](../virtual-machines/linux/about-disks-and-vhds.md)

## <a name="step-by-step-instructions"></a>Részletes útmutató

A következő lépések végigvezetik a VHD-fájl feltöltése a DevTest Labs segítségével [Microsoft Azure Tártallózó](../vs-azure-tools-storage-manage-with-storage-explorer.md).

1. [Töltse le és telepítse a legújabb verzióját a Microsoft Azure Tártallózó](http://www.storageexplorer.com).

1. Töltse le az Azure portál használatával a tesztkörnyezet tárfiókja nevét:

    1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=525040).
    
    1. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.
    
    1. Válassza ki a kívánt labor labs listájának megtekintéséhez.  
    
    1. A labor paneljén válassza **konfigurációs**. 
    
    1. A tesztlabor a **konfigurációs** panelen válassza **egyéni lemezképeket (VHD)**.
    
    1. Az a **egyéni lemezképek** panelen válasszon ki **+ Hozzáadás**. 
    
    1. Az a **egyéni lemezkép** panelen válassza **VHD**.
    
    1. Az a **VHD** panelen válassza **a PowerShell használatával virtuális merevlemez feltöltéséhez**.
    
        ![Töltse fel a virtuális merevlemez a PowerShell használatával][0]
    
    1. A **PowerShell-lel lemezkép feltöltése a** csempe megjeleníti hívása a **Add-AzureVhd** parancsmag. Az első paraméter (*cél*) tartalmazza a tárfiók nevét a labor a következő formátumban:
    
        https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/... 

    1. Jegyezze meg a tárfiók nevét, a későbbi lépésekben használatban van.
    
1. Csatlakozás Azure-előfizetés fiókhoz Tártallózó használatával.

    > [!TIP] 
    > 
    > A Tártallózó kapcsolati beállításokat támogatja. Ez a szakasz mutatja be, az Azure-előfizetéshez társított storage-fiókhoz való csatlakozást. A Tártallózó által támogatott más kapcsolati beállítások megtekintéséhez tekintse meg a cikk [Ismerkedés a Tártallózó](../vs-azure-tools-storage-manage-with-storage-explorer.md).
 
    1. Nyissa meg a Storage Explorert.
    
    1. A Tártallózó, válassza ki a **Azure Fiókbeállítások**. 
    
        ![Azure-fiók beállításai][1]
    
    1. A bal oldali ablaktáblán, a Microsoft-fiókkal bejelentkezett meg. Egy másik fiókhoz csatlakozáshoz válassza a **Fiók hozzáadása** lehetőséget, és a párbeszédpanelek útmutatásait követve jelentkezzen be egy olyan Microsoft-fiókkal, amely legalább egy aktív Azure-előfizetéssel társítva van.
    
        ![Fiók hozzáadása][2]
    
    1. Amint sikeresen bejelentkezett egy Microsoft-fiókkal, a bal oldali ablaktáblán megjelenik a fiókhoz társított összes Azure-előfizetés. Válassza ki azt az Azure-előfizetést amellyel dolgozni szeretne, majd válassza az **Alkalmaz** lehetőséget. (Kiválasztásával **előfizetéseket** ki-és a kiválasztott összes vagy egyik sem a felsorolt Azure-előfizetést.)
    
        ![Azure-előfizetések kiválasztása][3]
    
    1. A bal oldali ablaktábla megjeleníti a kiválasztott Azure-előfizetésekhez társított összes tárfiókot.
    
        ![Kiválasztott Azure-előfizetések][4]

1. Keresse meg a tesztkörnyezet tárfiókja:

    1. A Tártallózó bal oldali ablaktáblán keresse meg, és bontsa ki a csomópontot az Azure-előfizetés, amely a tesztkörnyezet tulajdonosa.
    
    1. Az előfizetési csomópontot, bontsa ki a **Tárfiókok**.

    1. Bontsa ki a labor tárolási fiók csomópontot, hogy láthatóvá váljon a csomópontok **Blobtárolók**, **fájlmegosztások**, **várólisták**, és **táblák**.
    
    1. Bontsa ki a **Blobtárolók** csomópont.
    
    1. A tartalom megjelenítéséhez a jobb oldali ablaktáblában a feltöltések blob-tároló választása.
        
        ![Directory feltöltése][5]

1. Töltse fel a VHD-fájlt a Tártallózó:

    1. A Tártallózó jobb oldali ablaktáblában kell megjelennie a blobok a lista tartalmazza a **feltölt** blob tároló a tesztkörnyezet tárfiókja. Válassza a blob-szerkesztő eszköztár **feltöltése** 
        
        ![Feltöltés gomb][6]
    
    1. Az a **feltöltése** legördülő menüben válassza **fájlok feltöltése...** .
    
    1. Az a **fájlok feltöltése** párbeszédpanelen válassza ki a három pont.
        
        ![Fájl kiválasztása][8]  

    1. A a **válassza ki a feltöltendő fájlt** párbeszédpanelen keresse meg a kívánt VHD-fájlt, jelölje ki, majd válassza ki **nyitott**.
    
    1. Amikor visszatér a **fájlok feltöltése** párbeszédpanelen módosítsa **Blob-típusú** való **oldalakra vonatkozó Blob**.
    
    1. Válassza a **Feltöltés** lehetőséget.

        ![Fájl kiválasztása][9]  
    
    1. A Tártallózó **tevékenységnapló** ablaktábla megjeleníti azokat a letöltés állapota (együtt megszakítani a feltöltést mutató). A folyamat egy VHD-fájl feltöltése megnőhet méretét a VHD-fájlt és a kapcsolat sebességétől függően. 

        ![Fájlfeltöltés állapot][10]  

## <a name="next-steps"></a>További lépések

- [Létrehozhat egyéni rendszerképeket a VHD-fájl az Azure portál használata az Azure DevTest Labs szolgáltatásban](devtest-lab-create-template.md)
- [Egyéni lemezkép létrehozása a PowerShell használatával VHD-fájl az Azure DevTest Labs szolgáltatásban](devtest-lab-create-custom-image-from-vhd-using-powershell.md)

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
