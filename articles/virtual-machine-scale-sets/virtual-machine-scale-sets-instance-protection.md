---
title: Az Azure virtuálisgép-méretezési csoport példány védelmének beállítása példányok |} A Microsoft Docs
description: Ismerje meg, hogyan védheti meg és méretezési csoport horizontális leskálázási műveletek az Azure virtuális gép méretezési csoport példányaihoz.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2019
ms.author: manayar
ms.openlocfilehash: 61430f5a43a04fa0e5b2f0c79ff03419c73aaf28
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66416548"
---
# <a name="instance-protection-for-azure-virtual-machine-scale-set-instances-preview"></a>Az Azure virtuálisgép-méretezési csoport példány védelmének beállítása instances (előzetes verzió)
Az Azure virtuális gép méretezési csoportok lehetővé teszik a keresztül a számítási feladatok esetében jobb rugalmasság [automatikus skálázási](virtual-machine-scale-sets-autoscale-overview.md), így megadhatja, ha az infrastruktúra horizontális felskálázást, és mikor skálázását követve rugalmasan méretezhető –. A méretezési csoportok lehetővé teszi, hogy központilag kezelése, konfigurálása és frissíteni a virtuális gépek nagy számú különböző keresztül [frissítési szabályzatának](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) beállításait. Frissítés a méretezési csoport modelljéből lehet konfigurálni, és az új konfigurációt a program automatikusan alkalmazza minden egyes scale set-példány, ha beállította a frissítési szabályzat automatikus vagy működés közbeni.

Mivel az alkalmazás feldolgozza a forgalmat, előfordulhat olyan helyzetben, amikor a méretezési csoport többi eltérően kell kezelni olyan specifikus példányai állítson be egy példányt. Például a méretezési csoportban lévő egyes példányok a hosszú ideig futó műveletek végrehajtása sikerült, és nem szeretné, hogy ezek a példányok lehet horizontálisan az addig, amíg a művelet befejezéséhez. Előfordulhat, hogy is speciális rendelkezik, a méretezési csoportban, mint a méretezési készlet más tagjai további vagy a különböző feladatok végrehajtásához néhány példányok. Ezek a "speciális" virtuális gépek, nem lehet módosítani a méretezési csoportban a többi példány van szüksége. Példányok védelmének engedélyezése ezeket és más forgatókönyvek esetében az alkalmazás további vezérlők biztosít.

Ez a cikk bemutatja, hogyan lehet alkalmazni, és a másik példány védelmi képességek használata a méretezési csoport példányaihoz.

> [!NOTE]
>Példányok védelmének jelenleg nyilvános előzetes verzióban érhető el. Nem vehetnek részt eljárás az alább ismertetett előzetes funkciók használatához szükséges. Példány protection előzetes verziója csak a 2019-03-01-es verziójú API-val és a felügyelt lemezek használata a méretezési csoportok esetén támogatott.

## <a name="types-of-instance-protection"></a>Példányok védelmének típusai
Méretezési csoportokhoz kétféle típusú példány védelmi funkciókat biztosítják:

-   **A horizontális leskálázási védelme**
    - Által engedélyezett **protectFromScaleIn** tulajdonságot a méretezési csoport példány beállítása
    - Által kezdeményezett automatikus leskálázási ellen védi a példány
    - (Beleértve a példány törlése) példány felhasználó által kezdeményezett műveletek **nincs letiltva**
    - A méretezési műveletek (frissítése, rendszerkép alaphelyzetbe állítása, szabadítsa fel, stb.) vannak **nincs letiltva**

-   **A méretezési csoport műveletek védelme**
    - Által engedélyezett **protectFromScaleSetActions** tulajdonságot a méretezési csoport példány beállítása
    - Által kezdeményezett automatikus leskálázási ellen védi a példány
    - A méretezési műveletek ellen védi a példány (például a frissítés, rendszerkép alaphelyzetbe állítása, szabadítsa fel, stb.)
    - (Beleértve a példány törlése) példány felhasználó által kezdeményezett műveletek **nincs letiltva**
    - A teljes méretezési törlése **nincs letiltva**

## <a name="protect-from-scale-in"></a>A horizontális leskálázási védelme
Példányok védelmének alkalmazhatók a méretezési csoport példányaihoz, miután a példányok készülnek. Védelmet alkalmazni, és csak a módosított a [példányú modellre](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) és nem a a [méretezési modell](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Többféle módon, ahogy az az alábbi példák a méretezési csoport példányaihoz horizontális leskálázási védelem alkalmazása.

### <a name="rest-api"></a>REST API

Az alábbi példa egy példányt a méretezési csoportban lévő horizontális leskálázási védelmi vonatkozik.

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
>Az API-verzió a 2019-03-01-es és újabb példány védelmét csak támogatott.

### <a name="azure-powershell"></a>Azure PowerShell

Használja a [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) parancsmag a méretezési csoport horizontális leskálázási védelmi alkalmazandó szabályzatkészlet példány.

Az alábbi példa egy példányt a méretezési Példányazonosító 0 kellene horizontális leskálázási védelmi vonatkozik.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Használat [az vmss update](/cli/azure/vmss#az-vmss-update) alkalmazzon horizontális leskálázási az a scale set-példány.

Az alábbi példa egy példányt a méretezési Példányazonosító 0 kellene horizontális leskálázási védelmi vonatkozik.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true
```

## <a name="protect-from-scale-set-actions"></a>A méretezési csoport műveletek védelme
Példányok védelmének alkalmazhatók a méretezési csoport példányaihoz, miután a példányok készülnek. Védelmet alkalmazni, és csak a módosított a [példányú modellre](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) és nem a a [méretezési modell](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Védelme egy példányt a méretezési csoport műveletek is védelmet nyújt a példány által kezdeményezett automatikus leskálázási a.

Nincsenek a több különböző módot is, hogy alkalmazása a méretezési csoport beállítani műveletek általi védelmet a méretezési csoport példányaihoz az alábbi példák leírt módon.

### <a name="rest-api"></a>REST API

Az alábbi példa egy példányt a méretezési csoportban, a méretezési csoport műveletek védelmet alkalmaz.

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
>Példányok védelmének csak az API-verzió a 2019-03-01-es és újabb esetén támogatott.</br>
Védelme egy példányt a méretezési csoport műveletek is védelmet nyújt a példány által kezdeményezett automatikus leskálázási a. Nem adható meg "protectFromScaleIn": hamis értéket, ha a "protectFromScaleSetActions" beállítása: igaz

### <a name="azure-powershell"></a>Azure PowerShell

Használja a [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) parancsmag a skálától-védelem beállítása a scale set-példány műveletek.

Az alábbi példa egy példánnyal rendelkező 0 azonosítója a méretezési scale set műveletekből védelmet alkalmaz.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true `
  -ProtectFromScaleSetAction $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Használat [az vmss update](/cli/azure/vmss#az-vmss-update) védelem a méretezési csoport műveletek a scale set-példányra.

Az alábbi példa egy példánnyal rendelkező 0 azonosítója a méretezési scale set műveletekből védelmet alkalmaz.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true \
  --protect-from-scale-set-actions true
```

## <a name="troubleshoot"></a>Hibaelhárítás
### <a name="no-protectionpolicy-on-scale-set-model"></a>A méretezési csoport modelljéből nincs protectionPolicy
Példányok védelmének csak akkor érvényes, a méretezési csoport példányaihoz nem pedig a méretezési csoport modelljéből.

### <a name="no-protectionpolicy-on-scale-set-instance-model"></a>A méretezési csoport modelljéből példány nincs protectionPolicy
Alapértelmezés szerint alkalmazásvédelmi szabályzat nem alkalmazható egy példány létrehozásakor.

Példányok védelmének méretezési csoport példányaihoz, a példányok létrehozása után is alkalmazható.

### <a name="not-able-to-apply-instance-protection"></a>Nem sikerült példány-védelem
Példányok védelmének csak az API-verzió a 2019-03-01-es és újabb esetén támogatott. Ellenőrizze az API-verziót használja, és szükség szerint módosítsa. Szükség lehet a PowerShell vagy parancssori felület frissítése a legújabb verzióra.

## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan [az alkalmazás üzembe helyezéséhez](virtual-machine-scale-sets-deploy-app.md) a virtuálisgép-méretezési csoportok.
