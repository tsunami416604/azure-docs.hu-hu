---
title: Beépített alkalmazások közzététele a Windows Virtuális asztalon – Azure
description: Beépített alkalmazások közzététele a Windows Virtuális asztalon.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a697c9a62e52e82a550969e1852abd1489ed59b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127739"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop"></a>Beépített alkalmazások közzététele a Windows Virtuális asztalon

Ebből a cikkből megtudhatja, hogyan tehet közzé alkalmazásokat a Windows virtuális asztali környezetben.

## <a name="publish-built-in-apps"></a>Beépített alkalmazások közzététele

Beépített alkalmazás közzététele:

1. Csatlakozzon a gazdagépkészlet egyik virtuális gépéhez.
2. A közzétenni kívánt alkalmazás **PackageFamilyName-nevét** a [cikkben](/powershell/module/appx/get-appxpackage?view=win10-ps/)található utasításokat követve.
3. Végül futtassa a következő `<PackageFamilyName>` parancsmamot az előző lépésben található **PackageFamilyName** helyett:
   
   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:appsFolder\<PackageFamilyName>!App"
   ```

>[!NOTE]
> A Windows Virtual Desktop csak a kezdetű `C:\Program Files\Windows Apps`telepítési helyekkel rendelkező alkalmazások közzétételét támogatja.

## <a name="update-app-icons"></a>Alkalmazásikonok frissítése

Az alkalmazás közzététele után a szokásos ikonkép helyett az alapértelmezett Windows-alkalmazás ikon jelenik meg. Ha az ikont normál ikonra szeretné módosítani, helyezze a kívánt ikon képét a hálózati megosztásra. A támogatott képformátumok a következők: PNG, BMP, GIF, JPG, JPEG és ICO.

## <a name="publish-microsoft-edge"></a>Microsoft Edge közzététele

A Microsoft Edge közzétételéhez használt folyamat egy kicsit eltér a többi alkalmazás közzétételi folyamatától. Ha a Microsoft Edge-et az alapértelmezett kezdőlappal szeretné közzétenni, futtassa a következő parancsmalapot:

```powershell
New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge" 
```

## <a name="next-steps"></a>További lépések

- A fájlok konfigurálása az alkalmazások felhasználók számára való megjelenítésének rendszerezéséről a [Windows virtuális asztali felhasználók hírcsatornájának testreszabása](customize-feed-for-virtual-desktop-users.md)című menüben olvashat.
- Az MSIX alkalmazáscsatolási funkcióról az [MSIX alkalmazáscsatolás beállítása című](app-attach.md)helyen olvashat.

