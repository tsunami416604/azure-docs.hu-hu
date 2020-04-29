---
title: Beépített alkalmazások közzététele a Windows rendszerű virtuális asztalon – Azure
description: Beépített alkalmazások közzététele a Windows Virtual Desktopban.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a697c9a62e52e82a550969e1852abd1489ed59b9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79127739"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop"></a>Beépített alkalmazások közzététele a Windows rendszerű virtuális asztalon

Ebből a cikkből megtudhatja, hogyan tehet közzé alkalmazásokat a Windows rendszerű virtuális asztali környezetben.

## <a name="publish-built-in-apps"></a>Beépített alkalmazások közzététele

Beépített alkalmazás közzététele:

1. Kapcsolódjon az egyik virtuális géphez a gazdagép-készletben.
2. A [cikkben](/powershell/module/appx/get-appxpackage?view=win10-ps/)szereplő utasítások alapján szerezze be a közzétenni kívánt alkalmazás **PackageFamilyName** .
3. Végül futtassa a következő parancsmagot az `<PackageFamilyName>` előző lépésben megtalált **PackageFamilyName** lecserélve:
   
   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:appsFolder\<PackageFamilyName>!App"
   ```

>[!NOTE]
> A Windows virtuális asztal csak a alkalmazással kezdődő telepítési hellyel rendelkező alkalmazások `C:\Program Files\Windows Apps`közzétételét támogatja.

## <a name="update-app-icons"></a>Alkalmazás ikonjainak frissítése

Miután közzétett egy alkalmazást, az alapértelmezés szerint a Windows-alkalmazás ikonja jelenik meg a normál ikon helyett. Ha módosítani szeretné az ikont a szokásos ikonjára, helyezze a kívánt ikont egy hálózati megosztásra. A támogatott képformátumok a következők: PNG, BMP, GIF, JPG, JPEG és ICO.

## <a name="publish-microsoft-edge"></a>Microsoft Edge közzététele

A Microsoft Edge közzétételéhez használt folyamat némileg eltér a közzétételi folyamattól más alkalmazások esetében. Ha közzé szeretné tenni a Microsoft Edge-t az alapértelmezett kezdőlapon, futtassa a következő parancsmagot:

```powershell
New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge" 
```

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan konfigurálhatja a hírcsatornákat úgy, hogy megszervezzék, hogyan jelenjenek meg az alkalmazások a [Windows rendszerű virtuális asztali felhasználók számára a hírcsatornák testreszabásakor](customize-feed-for-virtual-desktop-users.md).
- Ismerje meg a MSIX-alkalmazás csatolása funkciót a [MSIX-alkalmazás beállítása](app-attach.md)című témakörben.

