---
title: Azure PowerShell-példaszkript – Az RPD-felhasználónév és -jelszó frissítése | Microsoft Docs
description: Azure PowerShell-példaszkript – Az RPD-felhasználónév és -jelszó frissítése az egy konkrét csomóponttípusba tartozó összes Service Fabric-fürtcsomópont esetében.
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 03/19/2018
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: a5c1e7786f02b7ebc42f96e4ed12b2fb5729bb41
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58498009"
---
# <a name="update-the-admin-username-and-password-of-the-vms-in-a-cluster"></a>Az egy fürtbe tartozó virtuális gépek rendszergazdai felhasználónevének és jelszavának frissítése

A Service Fabric-fürtök mindegyik [csomóponttípusa](../service-fabric-cluster-nodetypes.md) egy virtuálisgép-méretezési csoportot jelent. Ez a példaszkript frissíti az egy konkrét csomóponttípusba tartozó fürtözött virtuális gépek rendszergazdai felhasználónevét és jelszavát.  Adja hozzá a VMAccessAgent bővítményt a méretezési csoporthoz, mert a rendszergazdai jelszó nem módosítható méretezésicsoport-tulajdonság.  A felhasználónév és jelszó módosításai a méretezési csoport összes csomópontjára érvényesek. Szabja testre a paramétereket szükség szerint.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Szükség esetén telepítse az Azure PowerShellt az [Azure PowerShell útmutatójának](/powershell/azure/overview) utasításait követve. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-user-and-pw/change-rdp-user-and-pw.ps1 "Updates a RDP username and password for cluster nodes")]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja: A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Get-AzVmss](/powershell/module/az.compute/get-azvmss) | Lekéri egy fürtcsomóponttípus (egy virtuálisgép-méretezési csoport) tulajdonságait.   |
| [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension)| Hozzáad egy bővítményt a virtuálisgép-méretezési csoporthoz.|
| [Update-AzVmss](/powershell/module/az.compute/update-azvmss)|Frissíti a virtuálisgép-méretezési csoport állapotát a helyi VMSS-objektum állapotával megegyezőre.|

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

További Azure Powershell-példákat az Azure Service Fabrichez az [Azure PowerShell-példák](../service-fabric-powershell-samples.md) között találhat.
