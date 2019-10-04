---
title: Azure rövid útmutató – virtuális gép biztonsági mentése Resource Manager-sablonnal
description: Ismerje meg, hogyan készíthet biztonsági mentést a virtuális gépekről Azure Resource Manager sablonnal
author: dcurwin
manager: carmonm
ms.service: backup
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/14/2019
ms.author: dacurwin
ms.custom: mvc
ms.openlocfilehash: 7075e127192635c08aa2da2b4798dea6d6abb13b
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639417"
---
# <a name="back-up-a-virtual-machine-in-azure-with-resource-manager-template"></a>Virtuális gépek biztonsági mentése az Azure-ban Resource Manager-sablonnal

[Azure Backup](backup-overview.md) biztonsági mentést készít a helyszíni gépekről és alkalmazásokról, valamint az Azure-beli virtuális gépekről. Ebből a cikkből megtudhatja, hogyan készíthet biztonsági mentést egy Azure-beli virtuális gépről Resource Manager-sablonnal és Azure PowerShell. Ebben a rövid útmutatóban egy Resource Manager-sablon üzembe helyezésének folyamatára koncentrálunk a helyreállítási tár létrehozásához. A Resource Manager-sablonok fejlesztésével kapcsolatos további információkért tekintse meg a [Resource Manager dokumentációját](/azure/azure-resource-manager/) és a sablonra vonatkozó [referenciát](/azure/templates/microsoft.recoveryservices/allversions).

Azt is megteheti, hogy a virtuális gép biztonsági mentését [Azure PowerShell](./quick-backup-vm-powershell.md), az [Azure CLI](quick-backup-vm-cli.md)vagy a [Azure Portal](quick-backup-vm-portal.md)használatával végezheti el.

## <a name="create-a-vm-and-recovery-services-vault"></a>Virtuális gép és Recovery Services-tároló létrehozása

A [Recovery Services](backup-azure-recovery-services-vault-overview.md) -tár egy logikai tároló, amely a védett erőforrások, például az Azure-beli virtuális gépek biztonsági mentési szolgáltatásait tárolja. A biztonsági mentési feladatok futtatásakor egy helyreállítási pontot hoz létre a Recovery Services-tárolón belül. Ezt követően ezen helyreállítási pontok egyikével állíthatja vissza az adatokat egy adott időpontra.

Az ebben a rövid útmutatóban használt sablon az [Azure Gyorsindítás sablonjaiból](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/)származik. Ez a sablon lehetővé teszi, hogy egyszerű Windows-alapú virtuális gépet és Recovery Services-tárolót helyezzen üzembe, amely a DefaultPolicy-védelemmel van konfigurálva.

A sablon üzembe helyezéséhez válassza a **kipróbálás** lehetőséget az Azure Cloud Shell megnyitásához, majd illessze be a következő PowerShell-szkriptet a rendszerhéj ablakába. A kód beillesztéséhez kattintson a jobb gombbal a rendszerhéj-ablakra, majd válassza a **Beillesztés**lehetőséget.

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

A Azure PowerShell a Resource Manager-sablon üzembe helyezéséhez használható ebben a rövid útmutatóban. A [Azure Portal](../azure-resource-manager/resource-group-template-deploy-portal.md), az [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md)és a [REST API](../azure-resource-manager/resource-group-template-deploy-rest.md) is használható sablonok telepítéséhez.

## <a name="start-a-backup-job"></a>Biztonsági mentési feladat indítása

A sablon létrehoz egy virtuális gépet, és lehetővé teszi a virtuális gép visszaállítását. A sablon üzembe helyezése után el kell indítania egy biztonsági mentési feladatot. További információkért lásd: [biztonsági mentési feladatok elindítása](./quick-backup-vm-powershell.md#start-a-backup-job).

## <a name="monitor-the-backup-job"></a>A biztonsági mentési feladat monitorozása

A biztonsági mentési feladatok figyeléséhez tekintse meg [a biztonsági mentési feladatok figyelése](./quick-backup-vm-powershell.md#monitor-the-backup-job)című témakört.

## <a name="clean-up-the-deployment"></a>Az üzemelő példány tisztítása

Ha már nincs szüksége a virtuális gép biztonsági mentésére, törölheti azt.

- Ha ki szeretné próbálni a virtuális gép visszaállítását, ugorja át a tisztítást.
- Ha meglévő virtuális gépet használt, akkor kihagyhatja a végleges [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancsmagot, hogy az erőforráscsoport és a virtuális gép ne legyen a helyén.

Tiltsa le a védelmet, távolítsa el a visszaállítási pontokat és a tárolót. Ezután törölje az erőforráscsoportot és a kapcsolódó virtuálisgép-erőforrásokat az alábbiak szerint:

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>További lépések

A rövid útmutató során létrehozott egy Recovery Services-tárolót, engedélyezte a védelmet a virtuális gépen, valamint létrehozta a kezdeti helyreállítási pontot.

- Megtudhatja, [Hogyan](tutorial-backup-vm-at-scale.md) készíthet biztonsági mentést a virtuális gépekről a Azure Portalban.
- Megtudhatja, [Hogyan](tutorial-restore-disk.md) állíthatja be gyorsan a virtuális gépeket
