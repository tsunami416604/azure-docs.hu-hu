---
title: VMware virtuális gépek áttelepítése az Azure-ba kiszolgálóoldali titkosítással (SSE) és ügyfél által felügyelt kulcsokkal (CMK) az Azure Áttelepítési Kiszolgáló áttelepítése használatával
description: Ismerje meg, hogyan telepítheti át a VMware virtuális gépeket az Azure-ba kiszolgálóoldali titkosítással (SSE) és ügyféláltal felügyelt kulcsokkal (CMK) az Azure Áttelepítési Kiszolgáló áttelepítésével
author: bsiva
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 03/12/2020
ms.author: raynew
ms.openlocfilehash: c6b791fda43a018a26204b2b43dc1e581ff3a945
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269483"
---
# <a name="migrate-vmware-vms-to-azure-vms-enabled-with-server-side-encryption-and-customer-managed-keys"></a>Virtuális gépvirtuális gépek áttelepítése az Azure-beli virtuális gépekre kiszolgálóoldali titkosítással és ügyféláltal kezelt kulcsokkal engedélyezve

Ez a cikk ismerteti, hogyan telepítheti át a VMware virtuális gépeket az Azure virtuális gépekre a kiszolgálóoldali titkosítással (SSE) ügyfél által felügyelt kulcsokkal (CMK) titkosított lemezekkel, az Azure Áttelepítési kiszolgáló áttelepítése (ügynök nélküli replikáció) használatával.

Az Azure Migrate Server Migration portal felület lehetővé teszi [a VMware virtuális gépek áttelepítését az Azure-ba ügynök nélküli replikációval.](tutorial-migrate-vmware.md) A portálélmény jelenleg nem teszi lehetővé az SSE bekapcsolását cmk-rel az Azure-ban replikált lemezekhez. Az SSE cmk-rel történő bekapcsolásának lehetősége a replikált lemezekhez jelenleg csak a REST API-n keresztül érhető el. Ebben a cikkben megtudhatja, hogyan hozhat létre és helyezhet üzembe egy [Azure Resource Manager-sablont](../azure-resource-manager/templates/overview.md) egy VMware virtuális gép replikálásához, és konfigurálhatja a replikált lemezeket az Azure-ban az SSE cmk használatával.

Ebben a cikkben szereplő példák az [Azure PowerShell](/powershell/azure/new-azureps-module-az) használatával hajtsák végre az Erőforrás-kezelő sablon létrehozásához és üzembe helyezéséhez szükséges feladatokat.

[További információ](../virtual-machines/windows/disk-encryption.md) a felügyelt lemezek ügyfél által felügyelt kulcsaival (CMK) rendelkező kiszolgálóoldali titkosításról (SSE).

## <a name="prerequisites"></a>Előfeltételek

- Tekintse át a VMware virtuális gépek Azure-ba való migrálásáról szóló [oktatóanyagot](tutorial-migrate-vmware.md) ügynök nélküli replikációval az eszközkövetelmények megértéséhez.
- [Kövesse az alábbi utasításokat](how-to-add-tool-first-time.md) egy Azure Migrate projekt létrehozásához, és adja hozzá az **Azure Migrate: Server Migration** eszközt a projekthez.
- [Kövesse ezeket](how-to-set-up-appliance-vmware.md) az utasításokat az Azure Migrate-készülék beállítása vmware a helyszíni környezetben, és a teljes felderítés.

## <a name="prepare-for-replication"></a>Felkészülés a replikációra

A virtuális gép felderítésének befejezése után a kiszolgálóáttelepítési csempe felderített kiszolgálói sora a készülék által felderített vmware virtuális gépek számát jeleníti meg.

A virtuális gépek replikálásának megkezdése előtt elő kell készíteni a replikációs infrastruktúrát.

1. Hozzon létre egy Service Bus-példányt a célrégióban. A Service Bus a helyszíni Azure Áttelepítési készülék a kiszolgálóáttelepítési szolgáltatással való kommunikációhoz a replikáció és az áttelepítés koordinálására.
2. Hozzon létre egy tárfiókot a replikációból származó műveleti naplók átviteléhez.
3. Hozzon létre egy tárfiókot, amelyaz Azure Migrate appliance replikációs adatokat tölt fel.
4. Hozzon létre egy Key Vault és konfigurálja a Key Vault kezelésére megosztott hozzáférés aláírás tokenek blob hozzáférés a storage-fiókok létrehozott 3 és 4 lépésben.
5. Hozzon létre egy megosztott hozzáférésű aláírás-jogkivonatot az 1.
6. Hozzon létre egy Key Vault hozzáférési szabályzatot, amely lejáratja a helyszíni Azure Migrate-készülék (a készülék AAD alkalmazás használatával) és a kiszolgálóáttelepítési szolgáltatás hozzáférést a Key Vault.
7. Hozzon létre egy replikációs házirendet, és konfigurálja a Kiszolgálóáttelepítési szolgáltatást az előző lépésben létrehozott replikációs infrastruktúra részleteivel.

A replikációs infrastruktúrát létre kell hozni a cél Azure-régióban az áttelepítéshez és a cél Azure-előfizetés, amely a virtuális gépek áttelepítése.

A kiszolgálóáttelepítési portál szolgáltatása leegyszerűsíti a replikációs infrastruktúra előkészítését azáltal, hogy automatikusan ezt teszi, amikor először replikál egy virtuális gép egy projektben. Ebben a cikkben azt feltételezzük, hogy már replikált egy vagy több virtuális gépet a portállal, és hogy a replikációs infrastruktúra már létre van hozva. Megvizsgáljuk, hogyan fedezheti fel a meglévő replikációs infrastruktúra részleteit, és hogyan használhatja ezeket az adatokat bemenetként az Erőforrás-kezelő sablonba, amely et a cmk replikáció beállításához használ.

### <a name="identifying-replication-infrastructure-components"></a>Replikációs infrastruktúra-összetevők azonosítása

1. Az Azure Portalon nyissa meg az erőforráscsoportok lapot, és válassza ki azt az erőforráscsoportot, amelyben az Azure Migrate projekt létrejött.
2. Válassza a Bal oldali menü **Központi telepítések parancsát,** és keressen egy központi telepítési nevet a *"Microsoft.MigrateV2.VMwareV2EnableMigrate"* karakterlánccal kezdődően. A portálfelület által létrehozott Erőforrás-kezelő-sablonok listája jelenik meg a projektben lévő virtuális gépek replikációjának beállításához. Letöltünk egy ilyen sablont, és ezt használjuk bázisként a sablon cmk-rel történő replikációra való előkészítéséhez.
3. A sablon letöltéséhez válassza ki az előző lépés karakterlánc-mintájának megfelelő központi telepítést, > válassza a **Sablon parancsot** a bal oldali menüből, > kattintson a letöltés **gombra** a felső menüből. Mentse a template.json fájlt helyileg. Ezt a sablonfájlt az utolsó lépésben fogja szerkeszteni.

## <a name="create-a-disk-encryption-set"></a>Lemeztitkosítási készlet létrehozása

A lemeztitkosítási készlet objektum a felügyelt lemezeket egy key vaulthoz rendeli hozzá, amely tartalmazza az SSE-hez használandó CMK-t. Virtuális gépek replikálása a CMK-vel, hozzon létre egy lemeztitkosítási készletet, és adja át azt a replikációs művelet bemeneteként.

Kövesse az [alábbi](../virtual-machines/windows/disk-encryption.md#powershell) példát egy lemeztitkosítási készlet létrehozásához az Azure PowerShell használatával. Győződjön meg arról, hogy a lemeztitkosítási készlet jön létre a cél-előfizetés, amely a virtuális gépek áttelepítése, és a cél Azure-régióban az áttelepítés.

```azurepowershell
$Location = "southcentralus"                           #Target Azure region for migration 
$TargetResourceGroupName = "ContosoMigrationTarget"
$KeyVaultName = "ContosoCMKKV"
$KeyName = "ContosoCMKKey"
$KeyDestination = "Software"
$DiskEncryptionSetName = "ContosoCMKDES"

$KeyVault = New-AzKeyVault -Name $KeyVaultName -ResourceGroupName $TargetResourceGroupName -Location $Location -EnableSoftDelete -EnablePurgeProtection

$Key = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KeyName -Destination $KeyDestination

$desConfig = New-AzDiskEncryptionSetConfig -Location $Location -SourceVaultId $KeyVault.ResourceId -KeyUrl $Key.Key.Kid -IdentityType SystemAssigned

$des = New-AzDiskEncryptionSet -Name $DiskEncryptionSetName -ResourceGroupName $TargetResourceGroupName -InputObject $desConfig

Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get

New-AzRoleAssignment -ResourceName $KeyVaultName -ResourceGroupName $TargetResourceGroupName -ResourceType "Microsoft.KeyVault/vaults" -ObjectId $des.Identity.PrincipalId -RoleDefinitionName "Reader"
```

## <a name="get-details-of-the-vmware-vm-to-migrate"></a>Az áttelepítendő VMware vm részletei

Ebben a lépésben az Azure PowerShell használatával szerezheti be az áttelepítendő virtuális gép részleteit. Ezeket az adatokat fogja használni a replikációhoz használt Erőforrás-kezelő sablon létrehozásához. Pontosabban, a két tulajdonság a következő:

- A gép erőforrás-azonosítója a felderített virtuális gépek.
- A virtuális gép lemezeinek listája és lemezazonosítói.

```azurepowershell

$ProjectResourceGroup = "ContosoVMwareCMK"   #Resource group that the Azure Migrate Project is created in
$ProjectName = "ContosoVMwareCMK"            #Name of the Azure Migrate Project

$solution = Get-AzResource -ResourceGroupName $ProjectResourceGroup -ResourceType Microsoft.Migrate/MigrateProjects/solutions -ExpandProperties -ResourceName $ProjectName | where Name -eq "Servers-Discovery-ServerDis
covery"

# Displays one entry for each appliance in the project mapping the appliance to the VMware sites discovered through the appliance.
$solution.Properties.details.extendedDetails.applianceNameToSiteIdMapV2 | ConvertFrom-Json | select ApplianceName, SiteId
```
```Output
ApplianceName  SiteId
-------------  ------
VMwareApplianc /subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite
```

Másolja a SiteId-karakterlánc értékét, amely megfelel az Azure Migrate berendezésnek, amelyen keresztül a virtuális gép felderített. A fenti példában a SiteId a *"/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite"*

```azurepowershell

#Replace value with SiteId from the previous step
$SiteId = "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite"
$SiteName = Get-AzResource -ResourceId $SiteId -ExpandProperties | Select-Object -ExpandProperty Name
$DiscoveredMachines = Get-AzResource -ResourceGroupName $ProjectResourceGroup -ResourceType Microsoft.OffAzure/VMwareSites/machines  -ExpandProperties -ResourceName $SiteName

#Get machine details
PS /home/bharathram> $MachineName = "FPL-W19-09"     #Replace string with VMware VM name of the machine to migrate
PS /home/bharathram> $machine = $Discoveredmachines | where {$_.Properties.displayName -eq $MachineName}
PS /home/bharathram> $machine.count   #Validate that only 1 VM was found matching this name.
```

Másolja az áttelepítendő számítógép ResourceId-, név- és lemezazonosító-értékeit.
```Output
PS > $machine.Name
10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210
PS > $machine.ResourceId
/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210

PS > $machine.Properties.disks | select uuid, label, name, maxSizeInBytes

uuid                                 label       name    maxSizeInBytes
----                                 -----       ----    --------------
6000C291-5106-2aac-7a74-4f33c3ddb78c Hard disk 1 scsi0:0    42949672960
6000C293-39a1-bd70-7b24-735f0eeb79c4 Hard disk 2 scsi0:1    53687091200
6000C29e-cbee-4d79-39c7-d00dd0208aa9 Hard disk 3 scsi0:2    53687091200

```

## <a name="create-resource-manager-template-for-replication"></a>Erőforrás-kezelő sablon létrehozása replikációhoz

- Nyissa meg az Erőforrás-kezelő sablonfájlját, amelyet az Ön által választott szerkesztő ben letöltött **A replikációs infrastruktúra összetevőinek azonosítása** lépésben töltött le.
- Az összes erőforrás-definíció eltávolítása a sablonból, kivéve a *"Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems" típusú erőforrásokat.*
- Ha a fenti típusú erőforrás-definíciók száma több, egy kivételével távolítsa el az összeset. Távolítsa el a **dependsOn** tulajdonságdefiníciókat az erőforrás-definícióból.
- A lépés végén rendelkeznie kell egy fájllal, amely az alábbi példához hasonlóan néz ki, és ugyanazokkal a tulajdonságokkal rendelkezik.

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems",
            "apiVersion": "2018-01-10",
            "name": "ContosoMigration7371rsvault/VMware104e4replicationfabric/VMware104e4replicationcontainer/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_500937f3-805e-9414-11b1-f22923456e08",
            "properties": {
                "policyId": "/Subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.RecoveryServices/vaults/ContosoMigration7371rsvault/replicationPolicies/migrateVMware104e4sitepolicy",
                "providerSpecificDetails": {
                    "instanceType": "VMwareCbt",
                    "vmwareMachineId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_500937f3-805e-9414-11b1-f22923456e08",
                    "targetResourceGroupId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/PayrollRG",
                    "targetNetworkId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/PayrollRG/providers/Microsoft.Network/virtualNetworks/PayrollNW",
                    "targetSubnetName": "PayrollSubnet",
                    "licenseType": "NoLicenseType",
                    "disksToInclude": [
                        {
                            "diskId": "6000C295-dafe-a0eb-906e-d47cb5b05a1d",
                            "isOSDisk": "true",
                            "logStorageAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.Storage/storageAccounts/migratelsa1432469187",
                            "logStorageAccountSasSecretName": "migratelsa1432469187-cacheSas",
                            "diskType": "Standard_LRS"
                        }
                    ],
                    "dataMoverRunAsAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "snapshotRunAsAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "targetBootDiagnosticsStorageAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.Storage/storageAccounts/migratelsa1432469187",
                    "targetVmName": "PayrollWeb04"
                }
            }
        }
    ]
}
```

- A **névtulajdonság** szerkesztése az erőforrás-definícióban. Cserélje le a névtulajdonság utolsó "/" karakterét követő karakterláncot *$machine értékre. Név*( az előző lépésből).
- Módosítsa a **properties.providerSpecificDetails.vmwareMachineId** tulajdonság értékét *$machine értékkel. ResourceId*( az előző lépésből).
- Állítsa be a **targetResourceGroupId**, **targetNetworkId**, **targetSubnetName** értékeket a célerőforrás-csoport azonosítójára, a célvirtuális hálózati erőforrás azonosítójára és a cél alhálózat nevére.
- Állítsa be a **licenseType** értékét "WindowsServer" értékre az Azure Hybrid Benefit alkalmazásához ehhez a virtuális géphez. Ha ez a virtuális gép nem jogosult az Azure Hybrid Benefit használatára, állítsa a **licenseType** értékét NoLicenseType értékre.
- Módosítsa a **targetVmName** tulajdonság értékét az áttelepített virtuális gép kívánt Azure virtuálisgép-nevére.
- Tetszés szerint adjon hozzá egy **targetVmSize** nevű tulajdonságot a **targetVmName** tulajdonság alatt. Állítsa be a **targetVmSize** tulajdonság értékét a kívánt Azure virtuálisgép-méretre az áttelepített virtuális géphez.
- A **disksToInclude** tulajdonság a replikációhoz szükséges lemezbemenetek listája, amelynek minden olyan listaelem, amely egy helyszíni lemezt jelöl. Hozzon létre annyi listaelemet, mint a helyszíni virtuális gép lemezeinek száma. Cserélje le a listaelemben lévő **diskId** tulajdonságot az előző lépésben azonosított lemezek uuidazonosítójára. Állítsa az **isOSDisk** értéket "true" értékre a virtuális gép operációs rendszerlemezéhez, és "hamis" értéket az összes többi lemezhez. Hagyja változatlanul a **logStorageAccountId** és a **logStorageAccountSasSecretName** tulajdonságokat. Állítsa be a **diskType** értéket az Azure Managed Disk típusra (*Standard_LRS, Premium_LRS, StandardSSD_LRS)* a lemezhez való használatra. A CMK-vel titkosítani kívánt lemezekhez adjon hozzá egy **diskEncryptionSetId nevű tulajdonságot,** és állítsa be az értéket a létrehozott lemeztitkosítási készlet( $des erőforrásazonosítójára.** Id**) a *Lemeztitkosítási készlet létrehozása* lépésben
- Mentse a szerkesztett sablonfájlt. A fenti példában a szerkesztett sablonfájl a következőképpen néz ki:

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems",
            "apiVersion": "2018-01-10",
            "name": "ContosoVMwareCMK00ddrsvault/VMwareApplianca8bareplicationfabric/VMwareApplianca8bareplicationcontainer/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210",
            "properties": {
                "policyId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.RecoveryServices/vaults/ContosoVMwareCMK00ddrsvault/replicationPolicies/migrateVMwareApplianca8basitepolicy",
                "providerSpecificDetails": {
                    "instanceType": "VMwareCbt",
                    "vmwareMachineId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210",
                    "targetResourceGroupId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoMigrationTarget",
                    "targetNetworkId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/cmkRTest/providers/Microsoft.Network/virtualNetworks/cmkvm1_vnet",
                    "targetSubnetName": "cmkvm1_subnet",
                    "licenseType": "NoLicenseType",
                    "disksToInclude": [
                        {
                            "diskId": "6000C291-5106-2aac-7a74-4f33c3ddb78c",
                            "isOSDisk": "true",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        },
                        {
                            "diskId": "6000C293-39a1-bd70-7b24-735f0eeb79c4",
                            "isOSDisk": "false",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        },
                        {
                            "diskId": "6000C29e-cbee-4d79-39c7-d00dd0208aa9",
                            "isOSDisk": "false",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        }
                    ],
                    "dataMoverRunAsAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "snapshotRunAsAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "targetBootDiagnosticsStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                    "performAutoResync": "true",
                    "targetVmName": "FPL-W19-09"
                }
            }
        }
    ]
}
```

## <a name="set-up-replication"></a>Replikáció beállítása

Most már telepítheti a szerkesztett Erőforrás-kezelő sablont a projekt erőforráscsoporthoz a virtuális gép replikációjának beállításához. Megtudhatja, hogyan [helyezhet üzembe erőforrásokat az Azure Resource Manager-sablonokkal és az Azure PowerShelllel](../azure-resource-manager/templates/deploy-powershell.md)

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName $ProjectResourceGroup -TemplateFile "C:\Users\Administrator\Downloads\template.json"
```

```Output
DeploymentName          : template
ResourceGroupName       : ContosoVMwareCMK
ProvisioningState       : Succeeded
Timestamp               : 3/11/2020 8:52:00 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
Outputs                 :
DeploymentDebugLogLevel :

```

## <a name="next-steps"></a>További lépések

[Figyelje](tutorial-migrate-vmware.md#track-and-monitor) a replikáció állapotát a portálon keresztül, és hajtsa végre a tesztáttelepítéseket és az áttelepítéseket.
