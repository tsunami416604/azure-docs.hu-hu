---
title: Beépített alkalmazások közzététele a Windows Virtual Desktopban (klasszikus) – Azure
description: Beépített alkalmazások közzététele a Windows Virtual Desktopban (klasszikus).
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 80cd1a4c92441fb17ce0a66814ff0a39a92fb287
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88005567"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop-classic"></a>Beépített alkalmazások közzététele a Windows Virtual Desktopban (klasszikus)

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali (klasszikus) verzióra vonatkozik, amely nem támogatja Azure Resource Manager Windows rendszerű virtuális asztali objektumokat. Ha Azure Resource Manager Windows rendszerű virtuális asztali objektumokat szeretne felügyelni, tekintse meg [ezt a cikket](../publish-apps.md).

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
> A Windows virtuális asztal csak a alkalmazással kezdődő telepítési hellyel rendelkező alkalmazások közzétételét támogatja `C:\Program Files\Windows Apps` .

## <a name="update-app-icons"></a>Alkalmazás ikonjainak frissítése

Miután közzétett egy alkalmazást, az alapértelmezés szerint a Windows-alkalmazás ikonja jelenik meg a normál ikon helyett. Ha módosítani szeretné az ikont a szokásos ikonjára, helyezze a kívánt ikont egy hálózati megosztásra. A támogatott képformátumok a következők: PNG, BMP, GIF, JPG, JPEG és ICO.

## <a name="publish-microsoft-edge"></a>Microsoft Edge közzététele

A Microsoft Edge közzétételéhez használt folyamat némileg eltér a közzétételi folyamattól más alkalmazások esetében. Ha közzé szeretné tenni a Microsoft Edge-t az alapértelmezett kezdőlapon, futtassa a következő parancsmagot:

```powershell
New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge"
```

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan konfigurálhatja a hírcsatornákat úgy, hogy megszervezzék, hogyan jelenjenek meg az alkalmazások a [Windows rendszerű virtuális asztali felhasználók számára a hírcsatornák testreszabásakor](customize-feed-virtual-desktop-users-2019.md).
- Ismerje meg a MSIX-alkalmazás csatolása funkciót a [MSIX-alkalmazás beállítása](../app-attach.md)című témakörben.

