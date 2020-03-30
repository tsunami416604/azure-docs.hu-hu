---
title: Karbantartási konfigurációhoz társított erőforrások áthelyezése másik régióba
description: Megtudhatja, hogyan helyezheti át a virtuális gép karbantartási konfigurációjához társított erőforrásokat egy másik Azure-régióba
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 3e271e2467b495e79a93ce5eab5edee36e65e619
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304445"
---
# <a name="move-resources-in-a-maintenance-control-configuration-to-another-region"></a>Erőforrások áthelyezése karbantartás-vezérlési konfigurációban egy másik régióba

Kövesse ezt a cikket a Maintenance Control konfigurációhoz társított erőforrások áthelyezése egy másik Azure-régióba. Előfordulhat, hogy több okból is át szeretne helyezni egy konfigurációt. Például egy új régió előnyeinek kihasználásához, egy adott régióban elérhető funkciók vagy szolgáltatások üzembe helyezéséhez, a belső szabályzati és irányítási követelmények teljesítéséhez, vagy a kapacitástervezésre adott válaszként.

A karbantartási vezérlés testre szabott karbantartási konfigurációkkal lehetővé teszi a platformfrissítések [Windows-](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) és [Linux-virtuális](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=%2Fazure%2Fvirtual-machines%2Flinux%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Flinux%2Fbreadcrumb%2Ftoc.json&view=azure-java-stable) gépekre, valamint az Azure dedikált gazdagépekre való alkalmazásának szabályozását. A karbantartás-ellenőrzés régiók közötti áthelyezésére több forgatókönyv is létezik:

- A karbantartási konfigurációhoz társított erőforrások áthelyezéséhez, de nem magához a konfigurációhoz, kövesse ezt a cikket.
- A karbantartás-ellenőrzési konfiguráció áthelyezéséhez, de a konfigurációhoz társított erőforrásokhoz azonban kövesse az [alábbi utasításokat.](move-region-maintenance-configuration.md)
- A karbantartási konfiguráció és a hozzá tartozó erőforrások áthelyezéséhez először kövesse [az alábbi utasításokat.](move-region-maintenance-configuration.md) Ezután kövesse az ebben a cikkben található utasításokat.

## <a name="prerequisites"></a>Előfeltételek

A Karbantartás-vezérlés konfigurációjához társított erőforrások áthelyezésének megkezdése előtt:

- Győződjön meg arról, hogy az áthelyező erőforrások léteznek az új régióban, mielőtt elkezdené.
- Ellenőrizze az áthelyezni kívánt Azure virtuális gépekhez és dedikált Azure-állomásokhoz társított Maintenance Control konfigurációkat. Ellenőrizze az egyes erőforrásokat külön-külön. Jelenleg nincs mód több erőforrás konfigurációinak beolvasására.
- Erőforrás konfigurációinak beolvasásakor:
    - Győződjön meg arról, hogy az előfizetési azonosítót használja a fiókhoz, nem pedig egy Azure dedikált gazdagép-azonosítót.
    - CLI: A --output tábla paraméter csak olvashatóságra szolgál, és törölhető vagy módosítható.
    - PowerShell: A Formátum-tábla név paraméter csak olvashatóság, és törölhető vagy módosítható.
    - Ha a PowerShell, hibaüzenetjelenik meg, ha megpróbálja felsorolni konfigurációk egy erőforrás, amely nem rendelkezik társított konfigurációk. A hiba a következőhöz fog hasonlítani: "A művelet nem sikerült állapottal: "Nem található". Részletek: 404 Ügyfél hiba: Nem található url".

    
## <a name="prepare-to-move"></a>Felkészülés az áthelyezésre

1. Mielőtt elkezdené, adja meg ezeket a változókat. Mindegyiknek példát mutattunk.

    **Változó** | **Részletek** | **Példa**
    --- | ---
    $subId | A karbantartási konfigurációkat tartalmazó előfizetés azonosítója | "a mi-előfizetés-ID"
    $rsrcGroupName | Erőforráscsoport neve (Azure VM) | "VMResourceGroup"
    $vmName | Virtuálisgép-erőforrás neve |  "myVM"
    $adhRsrcGroupName |  Erőforráscsoport (dedikált állomások) | "HostResourceGroup"
    $adh | Dedikált állomásnév | "myHost"
    $adhParentName | Szülő erőforrás neve | "HostGroup"
    
2. A karbantartási konfigurációk beolvasása a PowerShell [Get-AZConfigurationAssignment](https://docs.microsoft.com/powershell/module/az.maintenance/Get-AzConfigurationAssignment?view=azps-3.5.0) paranccsal:

    - Az Azure dedikált gazdagépek, fuss:
        ```
        Get-AzConfigurationAssignment -ResourceGroupName $adhRsrcGroupName -ResourceName $adh -ResourceType hosts -ProviderName Microsoft.Compute -ResourceParentName $adhParentName -ResourceParentType hostGroups | Format-Table Name
        ```

    - Az Azure virtuális gépek esetén futtassa:

        ```
        Get-AzConfigurationAssignment -ResourceGroupName $rgName -ResourceName $vmName -ProviderName Microsoft.Compute -ResourceType virtualMachines | Format-Table Name
        ```
3. A karbantartási konfigurációk beolvasása a CLI [az karbantartási hozzárendelés](https://docs.microsoft.com/cli/azure/ext/maintenance/maintenance/assignment?view=azure-cli-latest) imázsával:

    - Az Azure dedikált gazdagépek:

        ```
        az maintenance assignment list --subscription $subId --resource-group $adhRsrcGroupName --resource-name $adh --resource-type hosts --provider-name Microsoft.Compute --resource-parent-name $adhParentName --resource-parent-type hostGroups --query "[].{HostResourceGroup:resourceGroup,ConfigName:name}" --output table
        ```

    - Azure virtuális gépek esetén:

        ```
        az maintenance assignment list --subscription $subId --provider-name Microsoft.Compute --resource-group $rsrcGroupName --resource-name $vmName --resource-type virtualMachines --query "[].{HostResourceGroup:resourceGroup, ConfigName:name}" --output table
        ```


## <a name="move"></a>Áthelyezés 

1. [Kövesse ezeket](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) az utasításokat az Azure-beli virtuális gépek áthelyezése az új régióba.
2. Az erőforrások áthelyezése után szükség szerint alkalmazza újra a karbantartási konfigurációkat az új régió erőforrásaira, attól függően, hogy áthelyezte-e a karbantartási konfigurációkat. A [PowerShell](../virtual-machines/maintenance-control-powershell.md) vagy a CLI használatával karbantartási konfigurációt alkalmazhat egy [erőforrásra.](../virtual-machines/maintenance-control-cli.md)


## <a name="verify-the-move"></a>Az áthelyezés ellenőrzése

Ellenőrizze az új régió erőforrásait, és ellenőrizze az új régió ban lévő erőforrások társított konfigurációit. 

## <a name="clean-up-source-resources"></a>Forrásforrások karbantartása

Az áthelyezés után fontolja meg az áthelyezett erőforrások törlését a forrásrégióban.


## <a name="next-steps"></a>További lépések

A karbantartási konfigurációk áthelyezéséhez kövesse [az alábbi utasításokat.](move-region-maintenance-configuration.md) 
