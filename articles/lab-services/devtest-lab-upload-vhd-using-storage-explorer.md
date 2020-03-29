---
title: VHD-fájl feltöltése az Azure DevTest Labs alkalmazásba a Storage Explorer használatával
description: VHD-fájl feltöltése a labor tárfiókjába a Microsoft Azure Storage Explorer használatával
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
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: defafdd5809b7e537b3b9abb78f8cb63d0033c16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170368"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-microsoft-azure-storage-explorer"></a>VHD-fájl feltöltése a labor tárfiókjába a Microsoft Azure Storage Explorer használatával

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

Az Azure DevTest Labs, VHD-fájlok at lehet használni, hogy egyéni lemezképek, amelyek a virtuális gépek kiépítése. Ez a cikk bemutatja, hogyan tölthet fel egy vHD-fájlt egy tesztkörnyezet tárfiókjába a [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) használatával. Miután feltöltötte a vhd fájlt, a [Következő lépések szakasz](#next-steps) ban néhány cikket, amelyek bemutatják, hogyan lehet létrehozni egy egyéni képet a feltöltött VHD-fájlból. Az Azure-beli lemezekről és virtuális merevlemezekről a [Felügyelt lemezek – Bevezetés című témakörben](../virtual-machines/linux/managed-disks-overview.md) talál további információt.

## <a name="step-by-step-instructions"></a>Lépésenkénti utasítások

A következő lépések segítségével egy Virtuális merevlemez-fájlt tölthet fel a DevTest Labs programba a [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md)használatával.

1. [Töltse le és telepítse a Microsoft Azure Storage Explorer legújabb verzióját.](https://www.storageexplorer.com)

1. A labor tárfiókjának nevének beszereznie az Azure Portalhasználatával:

    1. Jelentkezzen be az [Azure Portalra.](https://go.microsoft.com/fwlink/p/?LinkID=525040)
    
    1. Válassza a **Minden szolgáltatás**lehetőséget, majd a listából válassza a **DevTest Labs** elemet.
    
    1. A laborok listájából válassza ki a kívánt labort.  
    
    1. A labor paneljén válassza a **Konfiguráció**lehetőséget. 
    
    1. A labor **konfigurációpanelen** válassza **az Egyéni lemezképek (VD-k)** lehetőséget.
    
    1. Az **Egyéni képek** panelen válassza a **+Hozzáadás**lehetőséget. 
    
    1. Az **Egyéni kép** panelen válassza a **VHD**lehetőséget.
    
    1. A **VHD** panelen válassza **a VHD feltöltése a PowerShell használatával lehetőséget.**
    
        ![Virtuális merevlemez feltöltése a PowerShell használatával][0]
    
    1. A **PowerShell-panel használatával egy kép feltöltése** megjeleníti az **Add-AzureVhd** parancsmag hívását. Az első paraméter (*Cél*) a tesztkörnyezet tárfiókának nevét tartalmazza a következő formátumban:
    
        `https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...`

    1. Jegyezze fel a tárfiók nevét, ahogy azt a későbbi lépésekben használják.
    
1. Csatlakozzon egy Azure-előfizetési fiókhoz a Storage Explorer használatával.

    > [!TIP] 
    > 
    > A Storage Explorer számos csatlakozási lehetőséget támogat. Ez a szakasz az Azure-előfizetéshez társított tárfiókhoz való csatlakozást mutatja be. A Storage Explorer által támogatott egyéb csatlakozási lehetőségeket az Első lépések a [Tárolókezelővel](../vs-azure-tools-storage-manage-with-storage-explorer.md)című cikkben olvashatja.
 
    1. Nyissa meg a Storage Explorert.
    
    1. A Storage Explorerben válassza az **Azure-fiók beállításai lehetőséget.** 
    
        ![Azure-fiók beállításai][1]
    
    1. A bal oldali ablaktáblán azok a Microsoft-fiókok jelennek meg, amelyekbe bejelentkezett. Egy másik fiókhoz csatlakozáshoz válassza a **Fiók hozzáadása** lehetőséget, és a párbeszédpanelek útmutatásait követve jelentkezzen be egy olyan Microsoft-fiókkal, amely legalább egy aktív Azure-előfizetéssel társítva van.
    
        ![Fiók hozzáadása][2]
    
    1. Amint sikeresen bejelentkezett egy Microsoft-fiókkal, a bal oldali ablaktáblán megjelenik a fiókhoz társított összes Azure-előfizetés. Válassza ki azt az Azure-előfizetést amellyel dolgozni szeretne, majd válassza az **Alkalmaz** lehetőséget. (Az **Összes előfizetés** kiválasztása koronként a felsorolt Azure-előfizetések közül az összes vagy egyik et sem választja ki.)
    
        ![Azure-előfizetések kiválasztása][3]
    
    1. A bal oldali ablaktábla megjeleníti a kiválasztott Azure-előfizetésekhez társított összes tárfiókot.
    
        ![Kiválasztott Azure-előfizetések][4]

1. Keresse meg a labor tárfiókját:

    1. A Storage Explorer bal oldali ablaktáblájában keresse meg és bontsa ki a labort birtokló Azure-előfizetés csomópontját.
    
    1. Az előfizetés csomópontja alatt bontsa ki **a Storage-fiókok csomópontot.**

    1. Bontsa ki a tesztkörnyezet tárfiók-csomópontját a Blob Containers, **a Fájlmegosztások,** a **Várólisták**és a Táblák csomópontjainak **Blob Containers**felfedéséhez. **Tables**
    
    1. Bontsa ki a **Blob-tárolók** csomópontját.
    
    1. Válassza ki a feltöltési blob tároló megjelenítéséhez annak tartalmát a jobb oldali ablaktáblában.
        
        ![Könyvtár feltöltése][5]

1. Töltse fel a VHD-fájlt a Storage Explorer segítségével:

    1. A Storage Explorer jobb oldali ablaktáblában meg kell jelennie a blobok listáját a tesztkörnyezet tárfiókjának **feltöltési** blobtárolójában. A blobszerkesztő eszköztárán válassza a **Feltöltés lehetőséget.** 
        
        ![Feltöltés gomb][6]
    
    1. A **Feltöltés** legördülő menüben válassza **a Fájlok feltöltése...** lehetőséget.
    
    1. A **Fájlok feltöltése** párbeszédpanelen jelölje ki a három pontot.
        
        ![Fájl kijelölése][8]  

    1. A **Feltöltendő fájlok kiválasztása** párbeszédpanelen keresse meg a kívánt Virtuális merevlemez-fájlt, jelölje ki, és válassza a **Megnyitás**gombot.
    
    1. Amikor visszatér a **Fájlok feltöltése** párbeszédpanelre, módosítsa a **Blob típusát** **Lapblobra.**
    
    1. Válassza a **Feltöltés** lehetőséget.

        ![Fájl kijelölése][9]  
    
    1. A **Tárolókezelő tevékenységnapló ablaktábláján** megjelenik a letöltési állapot (a feltöltés megszakítására szolgáló hivatkozásokkal együtt). A VHD-fájl feltöltésének folyamata a VHD fájl méretétől és a kapcsolat sebességétől függően hosszadalmas lehet. 

        ![Feltöltési fájl állapota][10]  

## <a name="next-steps"></a>További lépések

- [Egyéni lemezkép létrehozása az Azure DevTest Labs ben egy virtuális merevlemez-fájlból az Azure Portalhasználatával](devtest-lab-create-template.md)
- [Egyéni lemezkép létrehozása az Azure DevTest Labs ben egy Virtuális merevlemez-fájlból a PowerShell használatával](devtest-lab-create-custom-image-from-vhd-using-powershell.md)

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
