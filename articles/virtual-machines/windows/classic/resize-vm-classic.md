---
title: Egy Windows virtuális Gépet a klasszikus üzembe helyezési modellel - Azure átméretezése |} Microsoft Docs
description: A klasszikus üzembe helyezési modellel, az Azure Powershell létrehozott Windows virtuális gépek méretét.
services: virtual-machines-windows
documentationcenter: ''
author: Drewm3
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: e3038215-001c-406e-904d-e0f4e326a4c7
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/19/2016
ms.author: drewm
ms.openlocfilehash: 6afc8125462a659d0892e7dbb53f1f71789815cc
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="resize-a-windows-vm-created-in-the-classic-deployment-model"></a>A Windows virtuális gép létrehozása a klasszikus üzembe helyezési modellel átméretezése
Ez a cikk bemutatja, hogyan méretezze át egy Windows virtuális Gépet, a klasszikus üzembe helyezési modellel, az Azure Powershell használatával létrehozni.

Annak eldöntéséhez, hogy átméretezni a virtuális gép lehetőséget, nincsenek két fogalom, amely a tartomány a méretezze át a virtuális gép számára elérhető méretek szabályozza. Az első fogalma a régió, ahol a virtuális Gépre van telepítve. A Virtuálisgép-méretek elérhető régióban listáját az Azure-régiókat weblap szolgáltatások lapon alatt áll. A második fogalma a fizikai hardver a virtuális Gépet tartalmazó. A fizikai kiszolgálók, virtuális gépeket üzemeltet a fürtök közös fizikai hardver együtt vannak csoportosítva. A módszer egy virtuális gép méretének módosítása eltér attól függően, hogy a kívánt új Virtuálisgép-méretet támogatja a hardveres fürt a virtuális Gépet tartalmazó.

> [!IMPORTANT] 
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Emellett [méretezze át a virtuális gép létrehozása a Resource Manager üzembe helyezési modellel](../resize-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="add-your-account"></a>A fiók hozzáadása
Konfigurálnia kell az Azure PowerShell, a klasszikus Azure-erőforrásokat. Konfigurálhatja az Azure PowerShell hagyományos erőforrások kezeléséhez az alábbi lépésekkel.

1. A PowerShell-parancssorba írja be a `Add-AzureAccount` kattintson **Enter**. 
2. Írja be az Azure-előfizetéshez társított e-mail címét, és kattintson a **Folytatás**. 
3. Adja meg a fiókhoz tartozó jelszót. 
4. Kattintson a **bejelentkezés**. 

## <a name="resize-in-the-same-hardware-cluster"></a>Hardver ugyanazon fürt átméretezése
A virtuális gép elérhető, a hardver-fürt a virtuális Gépet üzemeltető méretre átméretezéséhez hajtsa végre az alábbi lépéseket.

1. Futtassa a következő PowerShell-parancsot a hardver-fürt a felhőalapú szolgáltatás, amely tartalmazza a virtuális Gépet üzemeltető érhető el a Virtuálisgép-méretek listázása.
   
    ```powershell
    Get-AzureService | where {$_.ServiceName -eq "<cloudServiceName>"}
    ```
2. A következő parancsokat a virtuális gép átméretezésével.
   
    ```powershell
    Get-AzureVM -ServiceName <cloudServiceName> -Name <vmName> | Set-AzureVMSize -InstanceSize <newVMSize> | Update-AzureVM
    ```

## <a name="resize-on-a-new-hardware-cluster"></a>Új hardver fürt átméretezése
A virtuális gép nem érhető el, a hardver-fürt a virtuális Gépet, a felhőalapú üzemeltetési méretre átméretezése szolgáltatás és a felhőalapú szolgáltatás virtuális gépeinek kell újra létrehoznia. Ezért a felhőalapú szolgáltatás virtuális gépeinek meg kell adni a hardver-fürtökön támogatott méretének minden felhőalapú szolgáltatás egyetlen hardver fürtön helyezkedik el. A következő lépések azt ismerteti, hogyan méretezze át a virtuális gépek törlésével és majd újbóli létrehozása a felhőalapú szolgáltatás.

1. Futtassa a következő PowerShell-parancsot a Virtuálisgép-méretek elérhető régióban listázásához. 
   
    ```powershell
    Get-AzureLocation | where {$_.Name -eq "<locationName>"}
    ```
2. Jegyezze fel az összes konfigurációs beállítások az egyes virtuális gépek a a felhőalapú szolgáltatás, amely tartalmazza a virtuális gépek a méretezhető. 
3. Törölje a felhőalapú szolgáltatás, a lemezek megőrizni az egyes virtuális gépek beállítás választása esetén minden virtuális gépe.
4. Hozza létre újra a virtuális Gépet a kívánt Virtuálisgép-méretet használó méretezhető át.
5. Hozza létre újra a többi virtuális gépen, amely a felhőalapú szolgáltatás, a hardver fürt ilyenkor a felhőalapú szolgáltatáshoz elérhető Virtuálisgép-méretet használó voltak.

Egy minta parancsfájlt törlése és újbóli létrehozása egy felhőszolgáltatás, új Virtuálisgép-méretet használó található [Itt](https://github.com/Azure/azure-vm-scripts). 

## <a name="next-steps"></a>További lépések
* [A virtuális gép létrehozása a Resource Manager üzembe helyezési modellel átméretezése](../resize-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

