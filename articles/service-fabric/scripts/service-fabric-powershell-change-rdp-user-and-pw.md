---
title: Az RDP-felhasználónév és -jelszó frissítése a PowerShellben
description: Azure PowerShell-példaszkript – Az RPD-felhasználónév és -jelszó frissítése az egy konkrét csomóponttípusba tartozó összes Service Fabric-fürtcsomópont esetében.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 03/19/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: b7f229162ed745408121d898f5af516d22a0c039
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984929"
---
# <a name="update-the-admin-username-and-password-of-the-vms-in-a-cluster"></a>Az egy fürtbe tartozó virtuális gépek rendszergazdai felhasználónevének és jelszavának frissítése

A Service Fabric-fürtök mindegyik [csomóponttípusa](../service-fabric-cluster-nodetypes.md) egy virtuálisgép-méretezési csoportot jelent. Ez a példaszkript frissíti az egy konkrét csomóponttípusba tartozó fürtözött virtuális gépek rendszergazdai felhasználónevét és jelszavát.  Adja hozzá a VMAccessAgent bővítményt a méretezési csoporthoz, mert a rendszergazdai jelszó nem módosítható méretezésicsoport-tulajdonság.  A felhasználónév és jelszó módosításai a méretezési csoport összes csomópontjára érvényesek. Szabja testre a paramétereket szükség szerint.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Szükség esetén telepítse az Azure PowerShellt az [Azure PowerShell útmutatójának](/powershell/azure/overview) utasításait követve. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-user-and-pw/change-rdp-user-and-pw.ps1 "Updates a RDP username and password for cluster nodes")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja: A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Get-AzVmss](/powershell/module/az.compute/get-azvmss) | Lekéri egy fürtcsomóponttípus (egy virtuálisgép-méretezési csoport) tulajdonságait.   |
| [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension)| Hozzáad egy bővítményt a virtuálisgép-méretezési csoporthoz.|
| [Update-AzVmss](/powershell/module/az.compute/update-azvmss)|Frissíti a virtuálisgép-méretezési csoport állapotát a helyi VMSS-objektum állapotával megegyezőre.|

## <a name="duration"></a>Időtartam

Egy öt csomópontos sal rendelkező egyetlen csomóponttípus például 45–60 perces időtartammal rendelkezik a felhasználónév vagy jelszó módosításához. 

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

További Azure Powershell-példákat az Azure Service Fabrichez az [Azure PowerShell-példák](../service-fabric-powershell-samples.md) között találhat.
