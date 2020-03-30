---
title: Példányvédelem az Azure virtuálisgép méretezési példányaihoz
description: Ismerje meg, hogyan védheti meg az Azure virtuálisgép-méretezési példányokat a méretezési és méretezési csoportműveletektől.
author: avirishuv
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: avverma
ms.openlocfilehash: 021faad28fb575c4ffeb4d895ad451d8cd82b1a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254117"
---
# <a name="instance-protection-for-azure-virtual-machine-scale-set-instances"></a>Példányvédelem az Azure virtuálisgép méretezési példányaihoz

Az Azure virtuálisgép-méretezési készletek lehetővé teszik a számítási feladatok jobb rugalmasságát [az automatikus skálázás](virtual-machine-scale-sets-autoscale-overview.md)révén, így beállíthatja, hogy az infrastruktúra mikor méretezhető ki, és mikor méretezhető be. A méretezési csoportok lehetővé teszik nagyszámú virtuális gép központi kezelését, konfigurálását és frissítését a különböző [frissítési](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) házirend-beállításokon keresztül. A méretezési csoport modelljének frissítését konfigurálhatja, és az új konfiguráció automatikusan minden méretezési csoport példányra vonatkozik, ha a frissítési házirendet automatikus vagy gördülő állapotra állította.

Az alkalmazás folyamatai során előfordulhatnak olyan helyzetek, amikor azt szeretné, hogy bizonyos példányok a méretezési csoport többi példányától eltérően kezeljék. Például a méretezési csoport bizonyos példányai hosszú ideig futó műveleteket végezhetnek, és nem szeretné, hogy ezek a példányok skálázhatók legyenek a műveletek befejezéséig. Előfordulhat, hogy a méretezési csoport néhány példányát is speciálissá tette, hogy a méretezési csoport többi tagjától eltérő feladatokat hajtson végre. Ezeket a "speciális" virtuális gépeket nem kell módosítani a méretezési csoport többi példányával. A példányvédelem további vezérlőket biztosít az alkalmazás hoz való ilyen és más forgatókönyvek engedélyezéséhez.

Ez a cikk bemutatja, hogyan alkalmazhatja és használhatja a különböző példányvédelmi képességek méretezési csoport példányokkal.

## <a name="types-of-instance-protection"></a>A példányvédelem típusai
A méretezési csoportok kétféle példányvédelmi képességet biztosítanak:

-   **Védelem a méretezéstől**
    - A scale set példány **protectFromScaleIn** tulajdonsággal engedélyezve
    - Védi a példányt az automatikus skálázás által kezdeményezett méretezési folyamattal szemben
    - A felhasználó által kezdeményezett példányműveletek (beleértve a példánytörlését is) **nincsenek letiltva**
    - A méretezési készleten kezdeményezett műveletek (frissítés, újrakép, felszabadítás stb.) **nincsenek blokkolva**

-   **Védelem a méretezési műveletekkel szemben**
    - A **scalesetactions** tulajdonsággal engedélyezve a méretezési csoport példányán
    - Védi a példányt az automatikus skálázás által kezdeményezett méretezési folyamattal szemben
    - Védi a példányt a méretezési csoporton kezdeményezett műveletektől (például frissítés, újraképzés, felszabadítás stb.)
    - A felhasználó által kezdeményezett példányműveletek (beleértve a példánytörlését is) **nincsenek letiltva**
    - A teljes méretezési csoport törlése **nincs letiltva**

## <a name="protect-from-scale-in"></a>Védelem a méretezéstől
A példányvédelem a példányok létrehozása után a méretezési csoport példányaira alkalmazható. A védelem csak a [példánymodellen](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) van alkalmazva és módosítva, a [méretezési csoport modelljén](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model)nem.

Az alábbi példákban részletezett méretezési csoportpéldányain többféleképpen is alkalmazhat horizontális felskálázási védelmet.

### <a name="azure-portal"></a>Azure portál

Az Azure Portalon keresztül a méretezési csoport egy példányát alkalmazhatja. Egyszerre csak egy példány módosítható. Ismételje meg a védeni kívánt példányok lépéseit.
 
1. Lépjen egy meglévő virtuálisgép-méretezési csoportra.
1. Válassza a **Példányok** lehetőséget a bal oldali menü **Beállítások területén.**
1. Jelölje ki a védeni kívánt példány nevét.
1. Válassza a **Védelmi házirend** lapot.
1. A **Védelmi házirend** panelen válassza a **Védelem a méretezéstől** beállítást.
1. Kattintson a **Mentés** gombra. 

### <a name="rest-api"></a>REST API

A következő példa a méretezési csoport egy példányára alkalmazza a horizontális felskálázási védelmet.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true
    }
  }        
}

```

> [!NOTE]
>A példányvédelem csak a 2019-03-01-es és újabb API-verzióval támogatott

### <a name="azure-powershell"></a>Azure PowerShell

Az [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) parancsmag használatával a méretezési csoport példánya méretezési szintű védelmet alkalmazhat.

A következő példa a 0-s példányazonosítóval rendelkező méretezési csoport egy példányára alkalmaz skálázási védelmet.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

A [virtuális gépek frissítése](/cli/azure/vmss#az-vmss-update) használatával a méretezési csoport példánya a méretezési csoport példánya.

A következő példa a 0-s példányazonosítóval rendelkező méretezési csoport egy példányára alkalmaz skálázási védelmet.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true
```

## <a name="protect-from-scale-set-actions"></a>Védelem a méretezési műveletekkel szemben
A példányvédelem a példányok létrehozása után a méretezési csoport példányaira alkalmazható. A védelem csak a [példánymodellen](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) van alkalmazva és módosítva, a [méretezési csoport modelljén](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model)nem.

Egy példány védelme a méretezési csoport műveletek is védi a példányt az automatikus skálázás által kezdeményezett méretezési kezdeményezés.

A méretezési csoport műveletek védelmének alkalmazásával többféleképpen is alkalmazható a méretezési csoport példányai, ahogy az az alábbi példákban részletezhető.

### <a name="azure-portal"></a>Azure portál

Az Azure Portalon keresztül a méretezési csoporton keresztül a méretezési csoport egy példányát alkalmazhatja a méretezési csoporton keresztül. Egyszerre csak egy példány módosítható. Ismételje meg a védeni kívánt példányok lépéseit.
 
1. Lépjen egy meglévő virtuálisgép-méretezési csoportra.
1. Válassza a **Példányok** lehetőséget a bal oldali menü **Beállítások területén.**
1. Jelölje ki a védeni kívánt példány nevét.
1. Válassza a **Védelmi házirend** lapot.
1. A **Védelmi házirend** panelen jelölje be a **Méretezéscsoport-műveletek védelme jelölőnégyzetet.**
1. Kattintson a **Mentés** gombra. 

### <a name="rest-api"></a>REST API

A következő példa a méretezési csoport műveletek elleni védelmet alkalmazza a méretezési csoport egy példányára.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vMScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true,
      "protectFromScaleSetActions": true
    }
  }        
}

```

> [!NOTE]
>A példányvédelem csak a 2019-03-01-es és újabb API-verzióval támogatott.</br>
Egy példány védelme a méretezési csoport műveletek is védi a példányt az automatikus skálázás által kezdeményezett méretezési kezdeményezés. Nem adhatja meg a "protectFromScaleIn" értéket: false a "protectFromScaleSetActions" beállításakor: true

### <a name="azure-powershell"></a>Azure PowerShell

Az [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) parancsmag használatával védelmet alkalmazhat a méretezési csoport műveletek a méretezési csoport példánya.

A következő példa a méretezési csoport műveletek elleni védelmet alkalmazza a 0 példányazonosítóval rendelkező méretezési csoport egy példányának egy példányát.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true `
  -ProtectFromScaleSetAction $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

A [virtuális gépek frissítése](/cli/azure/vmss#az-vmss-update) használatával védelmet alkalmazhat a méretezési csoport műveletek a méretezési csoport példányát.

A következő példa a méretezési csoport műveletek elleni védelmet alkalmazza a 0 példányazonosítóval rendelkező méretezési csoport egy példányának egy példányát.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true \
  --protect-from-scale-set-actions true
```

## <a name="troubleshoot"></a>Hibaelhárítás
### <a name="no-protectionpolicy-on-scale-set-model"></a>Nincs protectionPolicy a méretezési modellen
A példányvédelem csak a méretezési csoport példányaiesetén alkalmazható, a méretezési csoport modellén nem.

### <a name="no-protectionpolicy-on-scale-set-instance-model"></a>Nincs protectionPolicy a méretezési csoport példánymodelljén
Alapértelmezés szerint a védelmi házirend nem vonatkozik egy példányra, amikor létrejön.

A példányok létrehozása után példányvédelmet alkalmazhat a méretezési csoport példányaira.

### <a name="not-able-to-apply-instance-protection"></a>Nem lehet alkalmazni a példányvédelmet
A példányvédelem csak a 2019-03-01-es és újabb API-verzióval támogatott. Ellenőrizze a használt API-verziót, és szükség szerint frissítse. Előfordulhat, hogy a PowerShellvagy a CLI frissítése is a legújabb verzióra.

## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan [telepítheti az alkalmazást](virtual-machine-scale-sets-deploy-app.md) a virtuális gép méretezési csoportok.
