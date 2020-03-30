---
title: Karbantartási konfiguráció áthelyezése másik Azure-régióba
description: Megtudhatja, hogyan helyezhet át virtuális gép karbantartási konfigurációját egy másik Azure-régióba
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: fe03bead238d3fb7bda3ee685bd5587c3e0dbc58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304458"
---
# <a name="move-a-maintenance-control-configuration-to-another-region"></a>Karbantartás-ellenőrzési konfiguráció áthelyezése másik régióba

Kövesse ezt a cikket a Karbantartási vezérlés konfigurációjának áthelyezése egy másik Azure-régióba. Előfordulhat, hogy több okból is át szeretne helyezni egy konfigurációt. Például egy új régió előnyeinek kihasználásához, egy adott régióban elérhető funkciók vagy szolgáltatások üzembe helyezéséhez, a belső szabályzati és irányítási követelmények teljesítéséhez, vagy a kapacitástervezésre adott válaszként.

A karbantartási vezérlés testre szabott karbantartási konfigurációkkal lehetővé teszi a platformfrissítések [Windows-](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) és [Linux-virtuális](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=%2Fazure%2Fvirtual-machines%2Flinux%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Flinux%2Fbreadcrumb%2Ftoc.json&view=azure-java-stable) gépekre, valamint az Azure dedikált gazdagépekre való alkalmazásának szabályozását. A karbantartás-ellenőrzés régiók közötti áthelyezésére több forgatókönyv is létezik:

- A karbantartási vezérlés konfigurációjának áthelyezéséhez, de a konfigurációhoz társított erőforrásokhoz azonban kövesse a cikkben található utasításokat.
- A karbantartási konfigurációhoz társított erőforrások áthelyezéséhez, de nem magához a konfigurációhoz, kövesse [az alábbi utasításokat.](move-region-maintenance-configuration-resources.md)
- A karbantartási konfiguráció és a hozzá társított erőforrások áthelyezéséhez először kövesse a cikkutasításait. Ezután kövesse [az alábbi utasításokat](move-region-maintenance-configuration-resources.md).

## <a name="prerequisites"></a>Előfeltételek

A karbantartási vezérlő konfigurációjának áthelyezése előtt:

- A karbantartási konfigurációk azure-beli virtuális gépekhez vagy dedikált gazdagépekhez vannak társítva. Győződjön meg arról, hogy virtuális gép/gazda erőforrások léteznek az új régióban, mielőtt elkezdené.
- Azonosítani: 
    - Meglévő karbantartási vezérlési konfigurációk.
    - Azok az erőforráscsoportok, amelyekben a meglévő konfigurációk jelenleg tartózkodnak. 
    - Azok az erőforráscsoportok, amelyekhez a konfigurációk az új régióba való áthelyezés után kerülnek hozzáadásra. 
    - Az áthelyezni kívánt karbantartási konfigurációhoz társított erőforrások.
    - Ellenőrizze, hogy az új régió ban lévő erőforrások megegyeznek-e az aktuális karbantartási konfigurációkhoz társított erőforrásokkal. A konfigurációk lehetnek azonos neveket az új régióban, mint a régi, de ez nem szükséges.

## <a name="prepare-and-move"></a>Felkészülés és mozgás 

1. Az egyes előfizetések összes karbantartási konfigurációjának lekérése. Ehhez futtassa a CLI [az maintenance configuration list](https://docs.microsoft.com/cli/azure/ext/maintenance/maintenance/configuration?view=azure-cli-latest#ext-maintenance-az-maintenance-configuration-list) parancsot, és cserélje le $subId az előfizetés-azonosítójára.

    ```
    az maintenance configuration list --subscription $subId --query "[*].{Name:name, Location:location, ResGroup:resourceGroup}" --output table
    ```
2. Tekintse át az előfizetésen belüli konfigurációs rekordok visszaadott táblalistáját. Az alábbiakban erre láthat példát. A lista az adott környezethez tartozó értékeket fog tartalmazni.

    **Név** | **Helyen** | **Erőforráscsoport**
    --- | --- | ---
    Karbantartás kihagyása | eastus2 | konfiguráció-erőforrás-csoport
    IgniteDemoConfig | eastus2 | konfiguráció-erőforrás-csoport
    defaultMaintenanceConfiguration-eastus | eastus | tesztkonfiguráció
    

3. Mentse a listát referenciaként. A konfigurációk áthelyezésekénél segít ellenőrizni, hogy minden el lett-e helyezve.
4. Referenciaként rendelje hozzá az egyes konfigurációkat/erőforráscsoportokat az új régió új erőforráscsoportjához.
5. Hozzon létre új karbantartási konfigurációkat az új régióban a [PowerShell](../virtual-machines/maintenance-control-powershell.md#create-a-maintenance-configuration)vagy a [CLI](../virtual-machines/maintenance-control-cli.md#create-a-maintenance-configuration)használatával.
6. Társítsa a konfigurációkat az új régió erőforrásaihoz a [PowerShell](../virtual-machines/maintenance-control-powershell.md#assign-the-configuration)vagy a [CLI](../virtual-machines/maintenance-control-cli.md#assign-the-configuration)használatával.


## <a name="verify-the-move"></a>Az áthelyezés ellenőrzése

A konfigurációk áthelyezése után hasonlítsa össze az új régió konfigurációit és erőforrásait az elkészített táblalistával.


## <a name="clean-up-source-resources"></a>Forrásforrások karbantartása

Az áthelyezés után fontolja meg az áthelyezett karbantartási konfigurációk törlését a forrásrégióban, a [PowerShellben](../virtual-machines/maintenance-control-powershell.md#remove-a-maintenance-configuration)vagy a [CLI-ben.](../virtual-machines/maintenance-control-cli.md#delete-a-maintenance-configuration)


## <a name="next-steps"></a>További lépések

Kövesse [ezeket az utasításokat,](move-region-maintenance-configuration-resources.md) ha a karbantartási konfigurációkhoz társított erőforrásokat kell áthelyeznie. 
