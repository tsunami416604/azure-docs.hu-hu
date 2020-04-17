---
title: Rövid útmutató – Erőforrás-kezelő sablon Virtuálisgép-biztonsági másolat
description: Ismerje meg, hogyan lehet biztonsági másolatot adni a virtuális gépekről az Azure Resource Manager sablonnal
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/14/2019
ms.custom: mvc,subject-armqs
ms.openlocfilehash: d6fb73801f0f460daf2ed70f8dc88187e41ea887
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458845"
---
# <a name="back-up-a-virtual-machine-in-azure-with-resource-manager-template"></a>Virtuális gép biztonsági és biztonsági másolatot az Azure-ban az Erőforrás-kezelő sablonnal

[Az Azure Backup](backup-overview.md) biztonsági másolatot készít a helyszíni gépekről és alkalmazásokról, valamint az Azure virtuális gépekről. Ez a cikk bemutatja, hogyan biztonsági másolatot egy Azure-beli virtuális gép resource manager-sablonnal és az Azure PowerShell. Ez a rövid útmutató az Erőforrás-kezelő sablon üzembe helyezésének folyamatára összpontosít a Recover Services-tároló létrehozásához. Az Erőforrás-kezelő sablonjainak fejlesztéséről az [Erőforrás-kezelő dokumentációja](/azure/azure-resource-manager/) és a sablon hivatkozása című [témakörben olvashat bővebben.](/azure/templates/microsoft.recoveryservices/allversions)

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Másik lehetőségként biztonsági másolatot is tehet egy virtuális gépről az [Azure PowerShell,](./quick-backup-vm-powershell.md)az [Azure CLI](quick-backup-vm-cli.md)vagy az [Azure Portal használatával.](quick-backup-vm-portal.md)

## <a name="create-a-vm-and-recovery-services-vault"></a>Virtuálisgép- és helyreállítási szolgáltatások-tároló létrehozása

A [Recovery Services-tároló](backup-azure-recovery-services-vault-overview.md) egy logikai tároló, amely tárolja a biztonsági mentési adatokat a védett erőforrások, például az Azure virtuális gépek. Amikor egy biztonsági mentési feladat fut, létrehoz egy helyreállítási pontot a Recovery Services-tárolóban. Ezt követően ezen helyreállítási pontok egyikével állíthatja vissza az adatokat egy adott időpontra.

### <a name="review-the-template"></a>A sablon áttekintése

A rövid útmutatóban használt sablon az [Azure rövidútmutatós sablonjaiból származik.](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/) Ez a sablon lehetővé teszi egyszerű Windows virtuális gép és helyreállítási szolgáltatások trezor telepítését a DefaultPolicy for Protection konfigurálva.

:::code language="json" source="~/quickstart-templates/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json" range="1-247" highlight="221-245":::

A sablonban meghatározott erőforrások a következők:

- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Compute/virutalMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.RecoveryServices/vaults**](/azure/templates/microsoft.recoveryservices/2016-06-01/vaults)
- [**Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems**](/azure/templates/microsoft.recoveryservices/2016-06-01/vaults/backupfabrics/protectioncontainers/protecteditems)

### <a name="deploy-the-template"></a>A sablon üzembe helyezése

A sablon üzembe helyezéséhez válassza a **Próbálja ki** az Azure Cloud Shell megnyitásához, majd illessze be a következő PowerShell-parancsfájlt a rendszerhéj ablakába. A kód beillesztéséhez kattintson a jobb gombbal a rendszerhéj ablakára, majd válassza a **Beillesztés parancsot.**

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

Az Azure PowerShell az Erőforrás-kezelő sablon üzembe helyezéséhez szolgál ebben a rövid útmutatóban. Az [Azure Portal](../azure-resource-manager/templates/deploy-portal.md), az Azure [CLI](../azure-resource-manager/templates/deploy-cli.md)és [a Rest API](../azure-resource-manager/templates/deploy-rest.md) sablonok üzembe helyezéséhez is használható.

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

### <a name="start-a-backup-job"></a>Biztonsági mentési feladat indítása

A sablon létrehoz egy virtuális gép, és lehetővé teszi a virtuális gép. A sablon üzembe helyezése után el kell indítania egy biztonsági mentési feladatot. További információt a [Biztonsági másolat készítése című témakörben talál.](./quick-backup-vm-powershell.md#start-a-backup-job)

### <a name="monitor-the-backup-job"></a>A biztonsági mentési feladat monitorozása

A biztonsági mentési feladat figyelése a [Biztonsági másolat készítésfigyelése](./quick-backup-vm-powershell.md#monitor-the-backup-job)című témakörben látható.

## <a name="clean-up-the-deployment"></a>A telepítés karbantartása

Ha már nem kell biztonsági másolatot a virtuális gép, akkor tisztítsa meg.

- Ha szeretné kipróbálni a virtuális gép visszaállítását, hagyja ki a karbantartást.
- Ha egy meglévő virtuális gép, kihagyhatja a végső [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancsmag, hogy az erőforráscsoport és a virtuális gép a helyén marad.

Tiltsa le a védelmet, távolítsa el a visszaállítási pontokat és a tárolót. Ezután törölje az erőforráscsoportot és a kapcsolódó virtuálisgép-erőforrásokat az alábbiak szerint:

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>További lépések

A rövid útmutató során létrehozott egy Recovery Services-tárolót, engedélyezte a védelmet a virtuális gépen, valamint létrehozta a kezdeti helyreállítási pontot.

- [Ismerje meg, hogyan](tutorial-backup-vm-at-scale.md) biztonsági másolatot a virtuális gépek ről az Azure Portalon.
- [Ismerje meg, hogyan állíthatja](tutorial-restore-disk.md) vissza gyorsan a virtuális gépeket
- [További információ](../azure-resource-manager/templates/template-tutorial-create-first-template.md) az Erőforrás-kezelő sablonok létrehozásáról.
