---
title: RDP-tulajdonságok testreszabása a PowerShell használatával – Azure
description: A Windows rendszerű virtuális asztal RDP-tulajdonságainak testreszabása PowerShell-parancsmagokkal.
author: Heidilohr
ms.topic: how-to
ms.date: 09/04/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7c4bda1ecf28e964db6ba672157790114affe650
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89462224"
---
# <a name="customize-remote-desktop-protocol-rdp-properties-for-a-host-pool"></a>A gazdagépek RDP protokoll (RDP) tulajdonságainak testreszabása

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali Azure Resource Manager Windows virtuális asztali objektumokkal vonatkozik. Ha a Windowsos virtuális asztalt (klasszikus) Azure Resource Manager objektumok nélkül használja, tekintse meg [ezt a cikket](./virtual-desktop-fall-2019/customize-rdp-properties-2019.md).

A gazdagépek RDP protokoll (RDP) tulajdonságainak (például a többmonitoros élmény és a hangátirányítás) testreszabása lehetővé teszi, hogy a felhasználók igényei alapján optimális élményt nyújtson a felhasználóknak. Az RDP-tulajdonságokat a Windows rendszerű virtuális asztali számítógépeken a Azure Portal vagy az **Update-AzWvdHostPool** parancsmag *-CustomRdpProperty* paraméter használatával szabhatja testre.

A támogatott tulajdonságok és az alapértelmezett értékek teljes listájáért tekintse meg a [támogatott RDP-fájlok beállításait](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context) .

## <a name="default-rdp-file-properties"></a>Az RDP-fájl alapértelmezett tulajdonságai

Az RDP-fájlok alapértelmezés szerint a következő tulajdonságokkal rendelkeznek:

|RDP-tulajdonság|Asztali számítógépen|RemoteAppként|
|---|---|---|
|Többszörös figyelési mód|Engedélyezve|N.A.|
|Meghajtó-átirányítások engedélyezve|Meghajtók, vágólap, nyomtatók, COM-portok, USB-eszközök és intelligens kártyák|Meghajtók, vágólap és nyomtatók|
|Távoli hang mód|Helyi lejátszás|Helyi lejátszás|

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, kövesse a [Windows rendszerű virtuális asztali PowerShell-modul beállítása](powershell-module.md) című témakör útmutatását a PowerShell-modul beállításához és az Azure-ba való bejelentkezéshez.

## <a name="configure-rdp-properties-in-the-azure-portal"></a>RDP-tulajdonságok konfigurálása a Azure Portalban

Az RDP-tulajdonságok konfigurálása a Azure Portalban:

1. Jelentkezzen be az Azure-ba <https://portal.azure.com> .
2. Adja meg a **Windows rendszerű virtuális asztalt** a keresősávban.
3. A szolgáltatások területen válassza a **Windows virtuális asztal**elemet.
4. A Windows rendszerű virtuális asztal lapon a képernyő bal oldalán található menüben válassza a **gazdagép-készletek** elemet.
5. Válassza ki a frissíteni kívánt **gazdagép-készlet nevét** .
6. A képernyő bal oldalán található menüben válassza a **Tulajdonságok** lehetőséget.
7. Az RDP-tulajdonságok szerkesztésének megkezdéséhez a **Tulajdonságok** lapon válassza az **RDP-beállítások lehetőséget** . A tulajdonságokat pontosvesszővel tagolt formátumban kell megadni, például a PowerShell-példákat.
8. Ha elkészült, kattintson a **Mentés** gombra a módosítások mentéséhez.

A következő szakaszban megtudhatja, hogyan szerkesztheti manuálisan az egyéni RDP-tulajdonságokat a PowerShellben.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Egyetlen egyéni RDP-tulajdonság hozzáadása vagy szerkesztése

Egyetlen egyéni RDP-tulajdonság hozzáadásához vagy szerkesztéséhez futtassa a következő PowerShell-parancsmagot:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty <property>
```

A következő parancsmag futtatásával ellenőrizheti, hogy az imént futtatott parancsmagot frissítette-e:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <customRDPpropertystring>
```

Ha például a "audiocapturemode" tulajdonságot egy 0301HP nevű gazdagépen keresi meg, akkor a következő parancsmagot kell megadnia:

```powershell
Get-AzWvdHostPool -ResourceGroupName 0301rg -Name 0301hp | format-list Name, CustomRdpProperty

Name              : 0301HP
CustomRdpProperty : audiocapturemode:i:1;
```

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Több egyéni RDP-tulajdonság hozzáadása vagy szerkesztése

Több egyéni RDP-tulajdonság hozzáadásához vagy szerkesztéséhez futtassa az alábbi PowerShell-parancsmagokat úgy, hogy az egyéni RDP-tulajdonságokat pontosvesszővel tagolt karakterláncként adja meg:

```powershell
$properties="<property1>;<property2>;<property3>"
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty $properties
```

A következő parancsmag futtatásával ellenőrizze, hogy az RDP tulajdonság hozzá lett-e adva:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <customRDPpropertystring>
```

A korábbi parancsmagok alapján például ha több RDP-tulajdonságot állít be a 0301HP-gazdagépen, a parancsmag a következőhöz hasonlóan fog kinézni:

```powershell
Get-AzWvdHostPool -ResourceGroupName 0301rg -Name 0301hp | format-list Name, CustomRdpProperty

Name              : 0301HP
CustomRdpProperty : audiocapturemode:i:1;audiomode:i:0;
```

## <a name="reset-all-custom-rdp-properties"></a>Az összes egyéni RDP-tulajdonság alaphelyzetbe állítása

Az egyéni RDP-tulajdonságokat alapértékre állíthatja úgy, hogy az [Egyéni RDP-tulajdonság hozzáadása vagy szerkesztése](#add-or-edit-a-single-custom-rdp-property)lehetőségre kattint, vagy a következő PowerShell-parancsmag futtatásával alaphelyzetbe állíthatja a gazdagépek összes egyéni RDP-tulajdonságát:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty ""
```

Annak ellenőrzéséhez, hogy sikeresen eltávolította-e a beállítást, adja meg a következő parancsmagot:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <CustomRDPpropertystring>
```

## <a name="next-steps"></a>Következő lépések

Most, hogy testreszabta az adott címkészlet RDP-tulajdonságait, bejelentkezhet egy Windows rendszerű virtuális asztali ügyfélbe, hogy egy felhasználói munkamenet részeként tesztelje őket. A következő útmutatókból megtudhatja, hogyan csatlakozhat egy munkamenethez az Ön által választott ügyfél használatával:

- [Kapcsolódás a Windows asztali ügyféllel](connect-windows-7-10.md)
- [Kapcsolódás a webügyféllel](connect-web.md)
- [Kapcsolódás az Android-ügyféllel](connect-android.md)
- [Kapcsolódás a macOS-ügyfélhez](connect-macos.md)
- [Kapcsolódás az iOS-ügyfélhez](connect-ios.md)
