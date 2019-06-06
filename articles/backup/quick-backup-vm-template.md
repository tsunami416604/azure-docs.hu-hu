---
title: Azure rövid útmutató – Resource Manager-sablonnal rendelkező virtuális gép biztonsági mentése
description: Ismerje meg, hogyan készíthet biztonsági mentést virtuális gépeiről az Azure Resource Manager-sablon
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/14/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: b144d7509562b8ca0bca6299caee4a7ce292f4a6
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481368"
---
# <a name="back-up-a-virtual-machine-in-azure-with-resource-manager-template"></a>Resource Manager-sablonnal az Azure-beli virtuális gép biztonsági mentése

[Az Azure Backup](backup-overview.md) a helyszíni gépek és az alkalmazások és az Azure virtuális gépek biztonsági mentését. Ez a cikk bemutatja, hogyan készíthet biztonsági másolatot a Resource Manager-sablon Azure virtuális Gépekhez és az Azure PowerShell. Ez a rövid útmutató helyreállítása Services-tároló létrehozása a Resource Manager-sablonnal üzembe helyezését tárgyalja. A Resource Manager-sablonok fejlesztéséhez további információkért lásd: [Resource Manager dokumentációja](/azure/azure-resource-manager/) és a [sablonreferenciája](/azure/templates/microsoft.recoveryservices/allversions).

Másik megoldásként készíthet biztonsági mentést egy virtuális gépet [Azure PowerShell-lel](./quick-backup-vm-powershell.md), a [Azure CLI-vel](quick-backup-vm-cli.md), vagy a a [az Azure portal](quick-backup-vm-portal.md).

## <a name="create-a-vm-and-recovery-services-vault"></a>Hozzon létre egy virtuális Gépet, és a Recovery Services-tároló

A [Recovery Services-tároló](backup-azure-recovery-services-vault-overview.md) olyan logikai tároló, amely tárolja a védett erőforrások, például az Azure virtuális gépek biztonsági másolati adatait. Amikor egy biztonsági mentési feladat fut, a Recovery Services-tároló belül egy helyreállítási pontot hoz létre. Ezt követően ezen helyreállítási pontok egyikével állíthatja vissza az adatokat egy adott időpontra.

A rendszer a rövid útmutatóban használt sablon [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/). Ez a sablon lehetővé teszi egyszerű Windows virtuális gép és a DefaultPolicy védelemre konfigurált a Recovery Services-tároló üzembe helyezése.

A sablon üzembe helyezéséhez válassza **kipróbálás** nyissa meg az Azure Cloud shellt, és illessze be a következő PowerShell-parancsfájlt a shell ablakába. Illessze be a kódot, kattintson a jobb gombbal a rendszerhéj ablakát, és jelölje ki **illessze be**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name (limited to eight characters) that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username for the virtual machine"
$adminPassword = Read-Host -Prompt "Enter the administrator password for the virtual machine" -AsSecureString
$dnsPrefix = Read-Host -Prompt "Enter the unique DNS Name for the Public IP used to access the virtual machine"

$resourceGroupName = "${projectName}rg"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -adminUsername $adminUsername -adminPassword $adminPassword -dnsLabelPrefix $dnsPrefix
```

Ebben a rövid útmutatóban a Resource Manager-sablon üzembe helyezése az Azure PowerShell segítségével. A [az Azure portal](../azure-resource-manager/resource-group-template-deploy-portal.md), [Azure CLI-vel](../azure-resource-manager/resource-group-template-deploy-cli.md), és [Rest API-val](../azure-resource-manager/resource-group-template-deploy-rest.md) üzembe helyezheti a sablonokat is használható.

## <a name="start-a-backup-job"></a>Biztonsági mentési feladat indítása

A sablon létrehoz egy virtuális Gépet, és lehetővé teszi, hogy vissza a virtuális gépen. A sablon üzembe helyezéséhez, miután kell a biztonsági mentési feladat elindításához. További információkért lásd: [indítsa el a biztonsági mentési feladat](./quick-backup-vm-powershell.md#start-a-backup-job).

## <a name="monitor-the-backup-job"></a>A biztonsági mentési feladat monitorozása

A biztonsági mentési feladat monitorozása: [a biztonsági mentési feladat monitorozása](./quick-backup-vm-powershell.md#monitor-the-backup-job).

## <a name="clean-up-the-deployment"></a>Az üzemelő példány eltávolítása

Ha már nincs szüksége a biztonsági másolatot készíteni a virtuális Gépet, akkor távolíthatja el.

- Ha azt szeretné, és próbálja ki a virtuális gép visszaállítása, hagyja ki a tiszta fel.
- Ha egy meglévő virtuális Gépet használt, eltekinthet a végső [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) megtarthatja az erőforráscsoportot és a virtuális gép helyen.

Tiltsa le a védelmet, távolítsa el a helyreállítási pontokat és a tárolót. Törölje az erőforráscsoportot és a társított virtuális gép erőforrásait, a következő:

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>További lépések

A rövid útmutató során létrehozott egy Recovery Services-tárolót, engedélyezte a védelmet a virtuális gépen, valamint létrehozta a kezdeti helyreállítási pontot.

- [Ismerje meg, hogyan](tutorial-backup-vm-at-scale.md) a virtuális gépek biztonsági mentése az Azure Portalon.
- [Ismerje meg, hogyan](tutorial-restore-disk.md) gyorsan a virtuális gép visszaállítása
